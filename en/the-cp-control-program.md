---
order: 95
icon: ":keyboard:"
---

# Control Program (CP)

## Introdução

z/VM possui dois componentes principais:

-   O Programa de Controle (CP) controla a Máquina Real.

-   O Sistema de Monitor Conversacional (CMS) controla as Máquinas Virtuais.

O CP é às vezes chamado de hipervisor. Ele é a camada entre o
hardware e as máquinas virtuais. Cada máquina virtual parece ter seu
próprio CPU, armazenamento (memória) e dispositivos. Na realidade, esses itens podem ser:

-   Reais. Por exemplo, você pode dedicar uma interface de rede real a uma
    máquina virtual para seu uso exclusivo.

-   Compartilhados. Por exemplo, o CPU é compartilhado por meio de divisão de tempo e o
    armazenamento real é compartilhado como armazenamento virtual.

-   Simulados. Por exemplo, um switch virtual é um switch de rede LAN simulado.
    CP mapeia transparentemente dispositivos e recursos virtuais para seus equivalentes reais.

-   Emulados. Por exemplo, discos SCSI emulados FBA. As declarações EDEVICE
    nos permitem definir dispositivos emulados que representam dispositivos reais para CP. Isso significa dispositivos SCSI.

Os tópicos a seguir explicam como o CP gerencia os recursos do computador para
máquinas virtuais.


## Central processing units (CPUs)

TODO: Review

- Um servidor IBM z13 pode ter até 141 processadores.
- Um servidor IBM zEnterprise EC12 pode ter até 101 processadores.
- z13 pode ter até 80 LPARs, um aumento dos 65 em zEC12.
- Uma Partição Lógica agora pode ter até 141 processadores definidos no z13 (101 para zEC12).
- Uma LPAR z/VM pode ter até 64 processadores lógicos se o SMT estiver desativado ou 32 processadores lógicos definidos se o SMT estiver ativado.
- Processadores virtuais (ou CPUs virtuais) em uma única máquina virtual (arquitetada): 64
  - No entanto, NVirtual > NLogical geralmente não é prático.
- Se capaz de rodar no modo multiprocessador, seu sistema operacional de máquina virtual despacha trabalho em suas CPUs virtuais como se estivesse rodando em hardware real. O CP cuida do despacho de trabalho em suas CPUs virtuais para CPUs reais.
- **Diretriz:** Nunca dê a uma máquina virtual mais CPUs virtuais do que existem CPUs reais.


Geralmente, as máquinas virtuais compartilham todos os CPUs, mas um CPU real pode ser dedicado a uma máquina virtual, o que significa que o CPU é reservado para uso exclusivo dessa máquina virtual. Isso obviamente tem um impacto no desempenho das outras máquinas virtuais no sistema.

## Storage

O termo _Storage_ no mainframe é análogo à memória em um computador pessoal. Os comandos do CP se referem à memória como _storage_, portanto, não confunda o termo "_storage_" com armazenamento em disco ou fita.

Cada máquina virtual tem seu próprio _storage_ virtual. O CP gerencia a residência das páginas da máquina virtual no _storage_ real por meio da paginação. Páginas que não foram referenciadas podem ser movidas do _storage_ real para um dispositivo de paginação. Quando uma máquina virtual requer uma página que não está mais no _storage_ real, ocorre uma falta de página e o CP traz a página faltante de volta para o _storage_ real.

O CP possui facilidades que permitem que partes do _storage_ real sejam compartilhadas por muitas máquinas virtuais. Tais porções são chamadas de segmentos compartilhados. Esse compartilhamento economiza no _storage_ real e requer menos paginação, melhorando assim o desempenho. Por exemplo, o núcleo CMS é compartilhado no _storage_ real por todas as máquinas virtuais que carregaram o CMS pelo nome; ou seja, toda máquina virtual CMS mapeia um segmento de _storage_ virtual de 1 MB para o mesmo 1 MB de _storage_ real.


## DASD and minidisks

DASD, o termo de mainframe para drives de disco, significa "direct access storage device" e é análogo a um disco rígido em um computador pessoal. Um único DASD real é chamado de volume ou volume real. Cada volume tem uma etiqueta ou número de série de volume (volser) que identifica o volume para o z/VM.

É importante entender a maneira como o z/VM compartilha DASD. O CP pode logicamente particionar volumes DASD reais em minidiscos, que é análogo a dividir um disco rígido de computador pessoal em várias partições. Um minidisco tem sua própria etiqueta, que é distinta da etiqueta DASD real. Cada máquina virtual pode ter um ou mais minidiscos e esses minidiscos estão sob controle do sistema operacional da maquina virtual.


## Minidisks Temporários

Você pode criar um minidisco temporário a partir de um pool especial de discos reais. O disco dura enquanto a máquina virtual estiver conectada. Ao desconectar, o minidisco temporário é excluído e o espaço retorna para o pool de disco temporário disponível.

## Discos virtuais em storage

Discos virtuais em armazenamento são semelhantes a minidiscos temporários, exceto que os discos são mapeados para o _storage_ em vez de cilindros de discos reais. O uso de discos virtuais em _storage_ evita a necessidade de I/O de disco. O CP gerencia as páginas do disco virtual como parte de sua gestão de memória real.


## Virtual readers, punches, e printers

Estes dispositivos não estão associados a dispositivos reais, mas são implementados através do sistema de arquivos de spool.

Nos primeiros dias da computação, a entrada no computador vinha de cartões perfurados carregados em um leitor de cartões (_reader_). Você usava uma perfuradora (_keypunch_) para registrar seu programa em cartões perfurados, depois carregava os cartões em um leitor de cartões (_reader_), que interpretava seus cartões e carregava seu programa no computador. A saída do programa era escrita em uma impressora (_printer_). O z/VM preserva esse pedaço da história da computação através de dispositivos virtuais de leitura, perfuração e impressão, também chamados de dispositivos de registro unitário. Os dispositivos de registro unitário oferecem uma maneira prática de enviar arquivos de um dispositivo virtual para outro, para outras máquinas virtuais ou para dispositivos reais (como impressoras reais). Por exemplo, você pode pensar em um arquivo sendo enviado de uma máquina virtual para outra como o equivalente virtual de pegar uma pilha de cartões de um computador e carregá-la no leitor de cartões de outro computador.

Por trás da manipulação desses arquivos, está um sistema de arquivos CP chamado sistema de arquivos de spool. O CP gerencia arquivos de spool em um ou mais volumes DASD que atuam como áreas de armazenamento temporário. Um arquivo de spool é uma coleção de dados juntamente com instruções de controle de dispositivo para processamento em um dispositivo de registro unitário. O spooling é o processamento de arquivos criados por ou destinados a leitores virtuais, perfurações e impressoras. Através de comandos CP e CMS, você pode enviar arquivos de spool de um dispositivo virtual para outro, de sua máquina virtual para outra e para dispositivos reais.

Por convenção, cada máquina virtual tem um leitor virtual (_reader_) no número de dispositivo virtual **00C**, uma perfuradora virtual (_punch_) no número de dispositivo virtual **00D** e uma impressora virtual (_printer_) no número de dispositivo virtual **00E**. Seu leitor virtual é como a caixa de entrada de um sistema de e-mail, exceto que mais do que apenas e-mails podem ser colocados lá. Através de sua perfuradora virtual, você pode colocar uma cópia de um sistema operacional inteiro no spool do sistema, e então usar o comando CP IPL para carregar e executar esse sistema operacional em sua máquina virtual.

## O virtual machine console

O console da máquina virtual ou console virtual é a interface principal
da máquina virtual. Quando você se conecta a uma máquina virtual a partir de um
terminal local ou uma estação de trabalho remota, o console virtual é
associado à sessão do terminal. A partir do console, você pode inserir comandos CP,
como carregar (IPL) um sistema operacional. O console virtual
é o dispositivo que um sistema operacional vê como seu sistema ou console
de hardware.

Enquanto você trabalha em seu console, o canto inferior direito da tela
exibe vários avisos de status. Os avisos informam o que está acontecendo
no sistema no momento atual. Se você esquecer o que esses avisos
significam, você pode retornar a esta seção para referência.

| **Status do Console** | **Descrição** |
|-----------------------|---------------|
| **CP READ**           | Este aviso significa que o Programa de Controle (CP) está esperando você inserir um comando. |
| **VM READ**           | Este aviso significa que um sistema operacional de máquina virtual, como o CMS, está esperando você inserir um comando. |
| **RUNNING**           | Este aviso significa que a máquina virtual está processando algo. No caso do CMS, significa que o CMS está pronto para que você insira um comando. |
| **MORE\...**          | Este aviso indica que há mais informações do que podem caber na tela atual. Após uma pausa (que depende das configurações do terminal da sua máquina virtual), a próxima tela de informações é exibida. Para ver a próxima tela imediatamente, pressione a tecla Limpar (Clear). Para manter essa informação na tela, pressione a tecla Enter, o que muda **MORE\...** para **HOLDING**. |
| **HOLDING**           | Este aviso significa que o sistema está esperando que você limpe a tela antes de mostrar mais informações. O aviso aparece quando a tela exibe **MORE\...** e você pressiona a tecla Enter. O aviso também pode aparecer quando outro usuário lhe envia uma mensagem. Para cancelar a retenção, pressione a tecla _Clear_. |
| **NOT ACCEPTED**      | Este aviso significa que o sistema está processando algo e está muito ocupado para aceitar outro comando. Espere alguns segundos e insira seu comando novamente. |


## O Diretório do Usuário

O diretório do usuário z/VM (ou registro do usuário) descreve a configuração
e as características operacionais de cada máquina virtual que pode ser
criada pelo CP. Um diretório do usuário z/VM existe em duas formas: uma forma de origem
que consiste em um ou mais arquivos CMS, e uma forma de objeto, compilada
a partir da origem, em um disco formatado pelo CP.

Cada máquina virtual tem uma entrada no diretório.


![User Directory example](/imgs/userdir.png)

## Exercícios

### Logon na MAINT

Faça login na máquina virtual MAINT. A senha padrão mudou
no **z/VM 6.3**. Em nosso ambiente, a senha padrão é
**WD5JU8QP** (ou o nome da máquina em alguns casos). Sempre verifique
o Guia de Instalação que acompanha seu pedido no IBM Shopz.

Alternativamente, limpe a tela de login e, no modo CP, digite **===\> logon maint WD5JU8QP**

```
USERID ===> maint 
PASSWORD ===> WD5JU8QP (complex password - installation default) 
```


**Pressione a tecla ENTER uma vez para mostrar algumas mensagens do PROFILE EXEC para este userid.**

**Pressione a tecla ENTER novamente para continuar e alterar o status de "VM READ" para o status "RUNNING".**

O "PROFILE EXEC" será executado para configurar seu ambiente. Agora você está no CMS. É semelhante ao TSO no z/OS ou a um shell no Linux. Você aprenderá mais sobre o CMS na próxima seção. Agora, vamos fazer alguns comandos CP para se familiarizar com o sistema.


```
LOGON MAINT                                                                     
z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),                        
built on IBM Virtualization Technology                                          
There is no logmsg data                                                         
FILES:   NO RDR,   NO PRT,   NO PUN                                             
LOGON AT 22:42:30 EDT SATURDAY 08/31/13                                         
z/VM V6.3.0    2013-08-31 21:23                                                 
                                                                                
DMSACP723I B (5E5) R/O                                                          
DMSACP723I D (51D) R/O                                                          
DMSACP723I E (551) R/O                                                          
                                                                                
******************************************************************              
                                                                                
THE MAINT630 USER ID **MUST** BE USED INSTEAD OF MAINT                          
WHEN INSTALLING SERVICE.                                                        
                                                                                
******************************************************************              
                                                                                
PRESS ENTER TO CONTINUE                                                         
                                                                                
Ready; T=0.01/0.01 22:43:44                                                     
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

## Comandos CP

Esta seção descreverá alguns comandos CP.

### HELP

Você usa os comandos CP para se comunicar com o programa de controle. Os comandos CP
controlam os dispositivos conectados a uma máquina virtual e suas
características.

1\. Para ver uma lista dos comandos CP:

```
===> HELP
```

**Posicione o cursor em "CP"**

**Pressione a tecla ENTER**

**O que você vê aqui é um subconjunto dos comandos CP disponíveis no z/VM.**

2\. Para exibir o texto de ajuda para o comando DISCONNECT:

**Pressione F8, se necessário, para avançar a tela.**

**Posicione o cursor em "DISConn"**

**Pressione a tecla ENTER**

A parte inferior da tela lista as teclas PF para opções adicionais que
estão disponíveis para o comando selecionado.


```
CP MENU                  Menu Help Information                   line 10 of 34 
                                                                                
 *CPQUERY  :DYNIO    CPRELease DUPlex    LINK      REFRESH   STore              
 *CPSET    :HELP     CPTRAP    ECho      LOADBUF   REPeat    SYNCmdrs           
 *CPUTIL   #CP       CPTYPE    ENable    LOADVFCB  REQuest   SYStem             
 *DEFINE   ACNT      CPU       EXTernal  LOCate    RESET     TAg                
 *DELETE   ACTivate  CPVLoad   FLASHBND  LOCATEVM  RESTART   TERMinal           
 *DETACH   ASSOCiate CPXLoad   FLASHCopy LOCK      RETAIN    TRace              
 *DISABLE  ASTERISK  CPXUnload FLASHEST  LOGoff    REWind    TRANsfer           
 *DISPLAY  AT        DEActive  FLASHRES  Logon     SAVESEG   TRSAVE             
 *DUMP     ATTach    DEDicate  FLASHTGT  Message   SAVESYS   TRSOurce           
 *ENABLE   ATTN      DEFine    FLASHWIT  MESSAGEA  SCREen    UNCOUPLE           
 *INDICATE AUTOLOg   DEFSEG    Flush     MESSAGEU  SEND      UNDEDicate         
 *LOCATE   BAckspace DEFSYS    FOR       MODify    SET       UNDIAL             
 *MODIFY   Begin     DELete    FORCE     MONitor   SHUTDOWN  UNLOCK             
 *MONITOR  CHange    DESTAGE   FORWard   MSGNOH    SIGnal    UTILITY            
 *PURGE    Close     DETach    FRee      NOTReady  SILENTly  VARY               
 *RDEVICE  COMMands  Dial      GIVE      ORDer     SLeep     VDelete            
 *SPXTAPE  COMMIT    DISAble   GIVRDEV   PER       SMsg      VInput             
 *STORE    CONCOPY   DISASSOCi GIVVDEV   POUNDCP   SNAPDUMP  VMDump             
 *TERMINAL COUPLE    DISCARD   HALT      PURge     SPAce     VMDUMPTL           
 *TRACE    CP        (*| \textcolor{red}{DISConn} |*)  HOld      Query     SPool     VMRELOcate         
 *TRSOURCE CPACcess  DISPlay   HYPerswap READY     SPXtape   Warning            
 *VMDUMPTL CPCAche   DRain     INDicate  RECordin  STArt     XAUTOLOg           
 *XLINK    CPHX      DUmp      Ipl       REDEFine  STOP      XLINK              
 :DUMPS    CPLISTfile                                                           
* * * End of File * * *                                                         
 PF1= Help     2= Top      3= Quit     4= Return     5= Clocate   6= ?          
 PF7= Backward 8= Forward  9= PFkeys  10=           11=          12= Cursor     
                                                                                
====>                                                                           
                                                            Macro-read 2 Files  
```

```
CP DISCONN                All Help Information                   line 1 of 140 
(c) Copyright IBM Corporation 1990, 2013                                        
                                                                                
 DISCONNECT                                                                     
                                                                                
 >>--DISConnect--.------.---------------------------------------------------->< 
                 '-HOld-'                                                       
                                                                                
 Authorization                                                                  
                                                                                
 Privilege Class: Any                                                           
                                                                                
 Purpose                                                                        
                                                                                
 Use DISCONNECT to disconnect your display from the host system without         
 stopping operations in your virtual machine.                                   
                                                                                
 Operands                                                                       
                                                                                
 HOld                                                                           
     causes the non-SNA TTY display terminal telecommunication connection to    
     remain in effect after your session is disconnected. If you specify the    
     HOLD option from a logical device, you disconnect from the logical device  
     without losing the connection between the logical device and the system.   
                                                                                
 Usage Notes                                                                    
                                                                                
 PF1=          2= Top      3= Quit     4= Return     5= Clocate   6= ?          
 PF7= Backward 8= Forward  9= PFkeys  10=           11=          12= Cursor     
                                                                                
====>                                                                           
                                                            Macro-read 3 Files  
```

3\. Quando terminar de navegar, saia da utilidade de ajuda.

**Pressione a tecla F3 (Sair) repetidamente até retornar ao estado "RUNNING"**

Você pode ter que fazer isso repetidamente, dependendo de quão aprofundado você foi na função AJUDA.

4\. Outra maneira de ir diretamente para o menu de comandos CP é com o seguinte comando de ajuda:


```
===> HELP CP MENU
```

5\. Saia do menu de ajuda e retorne ao prompt "READY":

**Pressione a tecla F3 repetidamente até retornar ao estado "RUNNING"**

### QUERY

Vamos aprender um pouco sobre o ambiente da sua máquina virtual com o
comando QUERY.

1\. Para exibir uma lista de comandos que sua máquina virtual (MAINT, neste
caso) está autorizada a emitir:

```
===> query commands
```

```
Ready; T=0.01/0.01 12:06:06                                                     
query  commands                                                                      
ACNT        ACTIVATE    ADJUNCT     ADSTOP      ASSOCIATE   AT                  
ATTACH      ATTN        AUTOLOG     BACKSPACE   BEGIN       CHANGE              
CLOSE       COMMANDS    COMMIT      CONCOPY     COUPLE      CPACCESS            
CPCACHE     CPHX        CPLISTFILE  CPRELEASE   CPFORMAT    CPTRAP              
CPTYPE      CPU         CPVLOAD     CPXLOAD     CPXUNLOAD   DEACTIVE            
DEACTIVATE  DEDICATE    DEFINE      DEFSEG      DEFSYS      DELETE              
DESTAGE     DETACH      DIAL        DISABLE     DISASSOCIATEDISCARD             
DISCONNECT  DISPLAY     DRAIN       DUMP        DUPLEX      ECHO                
ENABLE      EXTERNAL    FLASHCOPY   FLUSH       FOR         FORCE               
FORWARD     FREE        GIVE        HALT        HOLD        HYPERSWAP           
INDICATE    IPL         LINK        LOADBUF     LOADVFCB    LOCATE              
LOCATEVM    LOCK        LOGON       LOGOFF      MESSAGE     MODIFY              
MONITOR     MSGNOH      NOTREADY    ORDER       PURGE       QUERY               
READY       RECORDING   REDEFINE    REFRESH     REPEAT      REQUEST             
RESET       RESTART     RETAIN      REWIND      SAVESEG     SAVESYS             
SCREEN      SEND        SET         SHUTDOWN    SIGNAL      SILENTLY            
SLEEP       SMSG        SNAPDUMP    SPACE       SPOOL       SPXTAPE             
START       STOP        STORE       SYNCMDRS    SYSTEM      TAG                 
                                                                                
                                                            MORE...   ZVMWSXX   
```

```
TERMINAL    TRACE       TRANSFER    TRSAVE      TRSOURCE    UNCOUPLE            
UNDEDICATE  UNDIAL      UNLOCK      VARY        VDELETE     VINPUT              
VMDUMP      VMRELOCATE  WARNING     XAUTOLOG    XLINK       XSPOOL              
DIAG00      DIAG04      DIAG08      DIAG0C      DIAG10      DIAG14              
DIAG18      DIAG20      DIAG24      DIAG28      DIAG34      DIAG3C              
DIAG40      DIAG44      DIAG48      DIAG4C      DIAG54      DIAG58              
DIAG5C      DIAG60      DIAG64      DIAG68      DIAG70      DIAG74              
DIAG7C      DIAG84      DIAG88      DIAG8C      DIAG90      DIAG94              
DIAG98      DIAG9C      DIAGA0      DIAGA4      DIAGA8      DIAGB0              
DIAGB4      DIAGB8      DIAGBC      DIAGC8      DIAGCC      DIAGD0              
DIAGD4      DIAGD8      DIAGDC      DIAGE0      DIAGE4      DIAGEC              
DIAGF0      DIAGF8      DIAG204     DIAG210     DIAG214     DIAG218             
DIAG220     DIAG224     DIAG238     DIAG23C     DIAG240     DIAG244             
DIAG248     DIAG250     DIAG254     DIAG258     DIAG25C     DIAG260             
DIAG264     DIAG268     DIAG26C     DIAG270     DIAG274     DIAG278             
DIAG27C     DIAG280     DIAG288     DIAG290     DIAG29C     DIAG2A0             
DIAG2A4     DIAG2A8     DIAG2AC     DIAG2C0     DIAG2C4     DIAG2CC             
DIAG2E0     DIAG2FC     DIAG308                                                 
Ready; T=0.01/0.01 12:06:09                                                     
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

Esta lista é longa porque a máquina virtual MAINT possui o mais alto
nível de classe de privilégio (A, B, C, D, E, F, G), o que a permite emitir
todos os comandos. Normalmente, uma máquina virtual geral deve ter apenas
classe geral (G) de privilégio, que só permite que ela emita comandos para
controlar seus próprios recursos.

2\. Exiba uma lista de todos os processadores reais e indique a maneira como
cada processador está sendo utilizado.

```
===> query processors
```

A saída indica que seu z/VM possui ______________ processadores reais. O endereço do processador 00 é o ______________ processador e o endereço do processador 01 é o ______________ .


```
query processors                     
PROCESSOR 00 MASTER CP     
PROCESSOR 01 ALTERNATE CP  
PROCESSOR 02 ALTERNATE CP  
PROCESSOR 03 ALTERNATE CP  
PROCESSOR 04 ALTERNATE CP  
Ready; T=0.01/0.01 00:50:16
```

3\. Exiba o tamanho do storage real.

```
===> query storage
```

A saída indica que seu sistema z/VM tem ______________ de storage real.

```
QUERY STORAGE
STORAGE = 2G CONFIGURED = 2G INC = 1M STANDBY = 0  RESERVED = 0
Ready; T=0.01/0.01 00:50:34                                    
```

4\. Exiba o tamanho do storage acessível à sua máquina virtual (MAINT)

```
===> query virtual storage 
```

A máquina virtual MAINT tem acesso a ______________ MB de storage virtual.


```
Q V STOR                    
STORAGE = 256M              
Ready; T=0.01/0.01 00:50:49 
QUERY VIRTUAL STORAGE       
STORAGE = 256M              
Ready; T=0.01/0.01 00:50:56
```

5\. Exiba o status dos dispositivos de armazenamento direto real (DASDs).

```
===> q dasd all
```

Uma lista de dispositivos conhecidos por esta máquina virtual é exibida. Verifique a tabela abaixo para descrições de cada dispositivo e para que são usados.

| Device IDs                                    | Description                                                                                                                                                                                                                                                              |
|------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 0200                                           | Volume de inicialização ou "res" pack, às vezes chamado de SYSRES, ou volume de residência do sistema. O volume que você acabou de "IPL" (IPL 0200 CLEAR).                                                                                                                |
| 0201, 0205, 0206                               | Outros volumes de propriedade de CP. Por enquanto, considere volumes de propriedade de CP como volumes de sistema, contendo coisas como espaço de página (swap para aqueles que preferem a terminologia distribuída), espaço de spool, espaço temporário de arquivo, etc... |
| 2222, 24CC, 2CF0                               | Discos CMS usados para suportar seu ID de equipe no primeiro nível z/VM (ZVMDEMO).                                                                                                                                                                                      |
| 0300, 0304                                     | DASD para uso z/VM.                                                                                                                                                                                                                                                     |
| 0301, 0302, 0303                               | Usado para construir máquinas virtuais Linux.                                                                                                                                                                                                                           |
| 0191                                           | DASD compartilhado com alguns arquivos importantes usados durante este bootcamp.                                                                                                                                                                                        |
| 0190, 0191, 019D, 019E, 0400, 0401 e 0402      | Discos adicionais de MAINT 1º nível.                                                                                                                                                                                                                                    |


```
Q DASD ALL                                                                   
DASD 0200 CP OWNED  M01RES   21                                              
DASD 0201 CP OWNED  VMCOM1   2                                               
DASD 0202 CP SYSTEM VMCOM2   1                                               
DASD 0203 CP SYSTEM 630RL1   11                                              
DASD 0204 CP SYSTEM 630RL2   2                                               
DASD 0205 CP OWNED  M01S01   1                                               
DASD 0206 CP OWNED  M01P01   0                                               
DASD 0207 CP SYSTEM M01W01   1                                               
DASD 0208 CP SYSTEM M01W02   1                                               
DASD 0209 CP SYSTEM M01W03   1                                               
DASD 0190 MNT190  , DASD 0191 MNT191  , DASD 019D MNT19D  , DASD 019E MNT19E 
DASD 0300 M01S01  , DASD 0301 M02S01  , DASD 0401 MNT401  , DASD 0402 MNT402 
DASD 04CC MNT4CC  , DASD 2222 MNT4CC  , DASD 24CC MNT4CC  , DASD 2CF0 PMTCF0 
An offline DASD was not found.                                               
Ready; T=0.01/0.01 00:51:10                                                                                                                                  
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

6\. Exibir todos os dispositivos Open System Adapter (OSA) disponíveis.

```
===> q osa free
```

O output retorna todos os dispositivos OSA livres. Um dispositivo OSA livre é aquele que
não está atualmente em uso por um usuário ou pelo sistema.


```
q osa free                                                                
OSA  0800 FREE    , OSA  0801 FREE    , OSA  0802 FREE    , OSA  0803 FREE    
OSA  0804 FREE    , OSA  0805 FREE    , OSA  0806 FREE    , OSA  0807 FREE    
OSA  0808 FREE    , OSA  0809 FREE    , OSA  080A FREE    , OSA  080B FREE                                        
Ready; T=0.01/0.01 23:03:30                                                                                                                               
                                                                                
                                                            RUNNING   ZVMWSXX
```

7\. Exiba o número de cilindros ou páginas que estão alocados, em uso,
e disponíveis para volumes DASD anexados ao sistema que são propriedade de
'CP' (Control Program). Estes são tipicamente DASDs relacionados ao sistema.


```
===> q alloc
```

```
Q ALLOC                                                                         
DASD 0200 M01RES 3390 CKD-ECKD (UNITS IN CYLINDERS)                             
     TDISK TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     PAGE  TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     SPOOL TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     DRCT  TOTAL=00000000020 INUSE=00000000001 AVAIL=00000000019, ACTIVE        
DASD 0201 VMCOM1 3390 CKD-ECKD (UNITS IN CYLINDERS)                             
     TDISK TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     PAGE  TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     SPOOL TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     DRCT  TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
DASD 0205 M01S01 3390 CKD-ECKD (UNITS IN CYLINDERS)                             
     TDISK TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     PAGE  TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     SPOOL TOTAL=00000003337 INUSE=00000000162 AVAIL=00000003175                
     DRCT  TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
DASD 0206 M01P01 3390 CKD-ECKD (UNITS IN CYLINDERS)                             
     TDISK TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     PAGE  TOTAL=00000003337 INUSE=00000000001 AVAIL=00000003336                
     SPOOL TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
     DRCT  TOTAL=00000000000 INUSE=00000000000 AVAIL=00000000000                
IPL NUCLEUS ACTIVE ON VOLUME M01RES                                             
Ready; T=0.01/0.01 00:55:04                                                     
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX                                                                           
```

Em um exercício posterior, você aprenderá como formatar um volume DASD.
Basicamente, um volume deve ser alocado e utilizado das seguintes maneiras:


| **Tipo**  | **Descrição** |
|----------|---------------|
| **TDISK** | Tdisk é uma abreviação de disco temporário. O z/VM oferece a capacidade de definir discos temporários de trabalho ou utilitários. |
| **PAGE**  | O espaço de paginação no mainframe é semelhante ao swapping no ambiente distribuído. Windows tem arquivos de swap. Linux tem dispositivos de swap ou arquivos de swap. z/VM tem espaço de paginação. |
| **SPOOL** | O espaço de spool é onde arquivos de impressão, leitura e perfuração são armazenados. |
| **PERM**  | Espaço permanente do usuário. |


### VARY

Com um mainframe, você tem a capacidade de ligar dispositivos e torná-los disponíveis para uso, ou você pode desligá-los e removê-los do pool de recursos disponíveis. Isso é chamado de variar dispositivos online e variar dispositivos off-line.

Em um exercício anterior, você usou o comando 'Q DASD ALL' para exibir o status dos DASDs reais. Como os dispositivos 2222, 24CC e 2CF0 não são necessários para sua máquina virtual de segundo nível, vamos variá-los para offline.


```
===> vary offline 2222 24CC 2CF0
```

Exiba o status desses dispositivos para verificar se eles estão offline.

```
===> q 2222 24CC 2CF0
===> vary online 2222 24CC 2CF0
```

### ATTACH

Em um exercício anterior, você usou o comando 'Q OSA FREE' para exibir todos os endereços disponíveis do Open Systems Adapter. Você pode 'anexar' um dispositivo livre a uma máquina virtual e disponibilizá-lo para uso. Uma forma de anexar um dispositivo real é usando o comando 'ATTACH'.

Você usará os dispositivos OSA 800-802 neste workshop mais tarde para fornecer rede para o convidado Linux que você irá construir. Vamos anexar esses dispositivos à máquina virtual MAINT agora.

```
===> attach 800-802 maint 
```

Exiba o status desses dispositivos:

```
===> query osa free
===> q 800-802 
```

Observe que eles não estão mais livres (Q OSA FREE)! Eles aparecem como anexados ao MAINT. O comando para retornar esses dispositivos ao pool de recursos livres para que outras máquinas virtuais os utilizem é 'DETACH'.


### INDICATE

O CP também possui comandos para ajudar você a monitorar o que está acontecendo com o seu sistema; IND ou INDICATE, é um desses comandos. Vamos experimentar alguns:

```
===> ind load 
```

```
ind load                                                                        
AVGPROC-000% 05                                                                 
MDC READS-000000/SEC WRITES-000000/SEC HIT RATIO-000%                           
PAGING-0/SEC                                                                    
Q0-00000(00000)                           DORMANT-00001                         
Q1-00000(00000)           E1-00000(00000)                                       
Q2-00000(00000) EXPAN-001 E2-00000(00000)                                       
Q3-00000(00000) EXPAN-001 E3-00000(00000)                                       
                                                                                
PROC 0000-000% CP   HZ     PROC 0001-000% CP   HZ                               
PROC 0002-000% CP   HZ     PROC 0003-000% CP   HZ                               
PROC 0004-000% CP   HZ                                                          
                                                                                
LIMITED-00000                                                                   
Ready; T=0.01/0.01 23:06:38                                                     
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX  
```

Este simples comando informa muitas coisas. Você pode perceber de imediato que o seu sistema está "dormindo"!

- A utilização média é 000 
- Sem atividades de I/O 
- Sem paginação (lembre-se de que no z/VM 6.3 a IBM mudou o algoritmo de paginação; talvez às vezes você veja alguma atividade de paginação em um sistema não estressado)
- Etc.

Outro comando útil é o 'IND USER'. Você pode usar este comando para descobrir como uma máquina virtual específica está se comportando. Se os campos de contagem de I/O estiverem progredindo, você sabe que a máquina está trabalhando em algo. Esperançosamente, é algo produtivo. Você pode querer tentar alguns comandos IND USER quando o seu convidado Linux estiver rodando mais tarde. (Veja a saída abaixo)


```
===> ind user maint
===> ind user operator
===> and so on... (QUERY NAMES - show the users on z/VM)
```

Tente outrass variações...

```
===> ind paging
===> ind i/o
```

```
ind user                                                       
USERID=MAINT    MACH=ESA STOR=256M VIRT=V XSTORE=NONE          
IPLSYS=DEV 0190 DEVNUM=00036                                   
PAGES: RES=1622  WS=1601  LOCKEDREAL=0  RESVD=0                
       INSTAN=1622                                             
NPREF=0  PREF=0  READS=1  WRITES=13                            
CPU 00: CTIME=00:38 VTIME=000:00 TTIME=000:00 IO=000603        
        RDR=000000 PRT=000432 PCH=000000 TYPE=CP   CPUAFFIN=ON 
Ready; T=0.01/0.01 12:21:33                                    
```

```
ind paging                   
No users in page wait        
Ready; T=0.01/0.01 13:46:33  
```

```
ind i/o                     
No users in I/O wait        
Ready; T=0.01/0.01 13:47:04
```

### CPFMTXA

Quando você adiciona um novo disco a um PC, você pode formatá-lo, particioná-lo e construir sistemas de arquivos. Quando você adiciona um disco a um sistema z/VM, deve formatá-lo e informar ao z/VM como gostaria que o disco fosse usado, por exemplo: Page, Spool, Perm, etc.

CPFMTXA é uma utilidade para formatar e alocar página, spool, disco temporário e espaço de diretório em volumes DASD. Esses volumes especiais estão listados na lista 'CP_OWNED_VOLUMES' do arquivo 'SYSTEM CONFIG'. O z/VM usa volumes nesta lista sempre que precisa alocar espaço de página, spool, temporário e diretório. Além disso, o CPFMTXA pode ser usado para formatar, rotular e alocar volumes DASD para espaço de minidisco de usuário. Esse tipo de alocação é chamado de espaço PERM.

Agora, vamos praticar o uso da utilidade CPFMTXA.

#### Formatando um dispositivo para PAGE

1\. Primeiro, exiba uma lista de DASDs disponíveis:

```
===> q dasd free 
```

```
q dasd free                                                                 
DASD 0190 MNT190  , DASD 0191 DSD191  , DASD 0192 CMS191  , DASD 019D MNT19D
DASD 019E MNT19E  , DASD 0300 M01P02  , DASD 0301 M01P01  , DASD 0302 M01W01
DASD 0303 M01W02  , DASD 0401 MNT401  , DASD 0402 MNT402  , DASD 04CC MNT4CC
DASD 2222 MNT4CC  , DASD 24CC MNT4CC  , DASD 2CF0 FREE
Ready; T=0.01/0.01 15:49:09                                                 
```

O comando QUERY deve informar que o(s) disco(s) está(ão) livre(s). Anexe o volume ao MAINT. Exemplo: Se o volume estiver no endereço real 300, digite este comando e pressione a tecla Enter:

```
===> attach 300 *
```

```
attach 300 *                                 
DASD 0300 ATTACHED TO MAINT 0300 WITH DEVCTL 
Ready; T=0.01/0.01 15:48:00                  
```

O asterisco significa "anexar o dispositivo a mim", o ID do usuário que está emitindo o comando de anexar.


```
===> q 300
```

O dispositivo 300 deve aparecer como anexado ao MAINT.

```
q 300                                         
DASD 0300 ATTACHED TO MAINT    0300 R/W ZV63PG
Ready; T=0.01/0.01 15:49:45                   
```

Para obter acesso à utilidade CPFMTXA, verifique se você tem acesso ao minidisco 551. Na linha de comando, digite o seguinte comando e pressione a tecla Enter:


```
===> q accessed 
```

Ou

```
===> q disk
```

```
q accessed                                 
Mode  Stat     Files  Vdev  Label/Directory
A      R/W         5  191   MNT191         
B      R/O       134  5E5   MNT5E5         
C      R/W         3  2CC   MNT2CC         
D      R/O       293  51D   MNT51D         
E      R/O         8  551   PMT551         
S      R/O       696  190   MNT190         
Y/S    R/O      1123  19E   MNT19E         
Z/Z    R/O       404  19D   MNT19D         
Ready; T=0.01/0.01 15:51:28                
```

Neste exemplo, temos o minidisco 551 acessado com a letra E. Se você não tiver este minidisco acessado, digite o seguinte comando e pressione enter:


```
===> access 551 e 
```

```
acc 551 e                                  
DMSACP723I E (551) R/O                     
Ready; T=0.01/0.01 15:54:33                
q accessed                                 
Mode  Stat     Files  Vdev  Label/Directory
A      R/W         5  191   MNT191         
B      R/O       134  5E5   MNT5E5         
C      R/W         3  2CC   MNT2CC         
D      R/O       293  51D   MNT51D         
E      R/O         8  551   PMT551         
S      R/O       696  190   MNT190         
Y/S    R/O      1123  19E   MNT19E         
Z/Z    R/O       404  19D   MNT19D         
Ready; T=0.01/0.01 15:54:40                
```

Formate o dispositivo 0300 com o utilitário CPFMTXA. Digite:


```
===> cpfmtxa 
```

O utilitário CPFMTXA perguntará se você deseja formatar, alocar ou etiquetar. CPFMTXA é uma utilidade multifuncional. Neste caso, você deseja começar formatando. Digite:

```
===> format
```

CPFMTXA pergunta em seguida qual dispositivo você gostaria de formatar. Você vai formatar o dispositivo 0300, o dispositivo que você acabou de anexar ao MAINT. Digite:


```
===> 0300
```

CPFMTXA pergunta em seguida qual intervalo de cilindros você gostaria de formatar. Esta é uma ótima funcionalidade. Às vezes você não quer formatar todo o volume, apenas intervalos de cilindros selecionados em um volume. Neste caso, você deseja formatar todo o volume, então digite:


```
===> 0 end
```

Em seguida, é solicitado um novo rótulo de volume (M01P02 significa volume de paginação). Digite:

```
===> M01P02
```

Somos avisados de que o CPFMTXA está prestes a apagar todos os dados no volume; tem certeza de que deseja continuar?

```
CPFMTXA:                                                     
FORMAT WILL ERASE CYLINDERS 000000000-000003337 ON DISK 0300 
DO YOU WANT TO CONTINUE? (YES | NO)                          
```

Digite:

```
===> yes
```

O CPFMTXA agora invoca o ICKDSF para começar a formatar o volume. Este seria um bom momento para pegar uma xícara de café! Quando a formatação estiver concluída, o CPFMTXA apresenta o seguinte:

```
cpfmtxa                                                                         
ENTER FORMAT, ALLOCATE, LABEL, OWNER OR QUIT:                                   
format                                                                          
ENTER THE VDEV TO BE PROCESSED OR QUIT:                                         
0300                                                                            
ENTER THE CYLINDER RANGE TO BE FORMATTED ON DISK 0300 OR QUIT:                  
0 end                                                                           
ENTER THE VOLUME LABEL FOR DISK 0300:                                           
M01P02                                                                          
CPFMTXA:                                                                        
FORMAT WILL ERASE CYLINDERS 000000000-000003337 ON DISK 0300                    
DO YOU WANT TO CONTINUE? (YES | NO)                                             
yes                                                                             
HCPCCF6209I INVOKING ICKDSF.                                                    
ICK030E DEFINE INPUT  DEVICE: FN FT FM, "CONSOLE", OR "READER"                  
CONSOLE                                                                         
ICK031E DEFINE OUTPUT DEVICE: FN FT FM, "CONSOLE", OR "PRINTER"                 
CONSOLE                                                                         
ICKDSF - CMS/XA/ESA DEVICE SUPPORT FACILITIES 17.0                TIME: 02:34:33
        08/25/13     PAGE   1                                                   
                                                                                
ENTER INPUT COMMAND:                                                            
  CPVOL FMT MODE(ESA) UNIT(0300) VOLID(M01P02) NOVFY NFILL -                    
ENTER INPUT COMMAND:                                                            
 RANGE(0,3337)                                                                  
ICK00700I DEVICE INFORMATION FOR 0300 IS CURRENTLY AS FOLLOWS:                  
          PHYSICAL DEVICE = 3390                                                
          STORAGE CONTROLLER = 3990                                             
          STORAGE CONTROL DESCRIPTOR = E9                                       
          DEVICE DESCRIPTOR = 0C                                                
          ADDITIONAL DEVICE INFORMATION = 4A001F3C                              
          TRKS/CYL = 15, # PRIMARY CYLS = 3338                                  
ICK04000I DEVICE IS IN SIMPLEX STATE                                            
ICK00703I DEVICE IS OPERATED AS A MINIDISK                                      
ICK00091I 0300 NED=002107.900.IBM.82.0000000V9341                               
ICK091I   0300 NED=002107.900.IBM.82.0000000V9341                               
ICK03020I CPVOL WILL PROCESS 0300 FOR VM/ESA MODE                               
ICK03090I VOLUME SERIAL = M01P02                                                
ICK03022I FORMATTING THE DEVICE WITHOUT FILLER RECORDS                          
ICK03011I CYLINDER RANGE TO BE FORMATTED IS 0 - 3337                            
ICK003D REPLY U TO ALTER VOLUME 0300 CONTENTS, ELSE T                           
                                                                                
                                                            MORE...   ZVMWSXX
```

```
UICK03000I CPVOL REPORT FOR 0300 FOLLOWS:                                        
                                                                                
          FORMATTING OF CYLINDER 0 STARTED AT: 02:56:23                         
          FORMATTING OF CYLINDER 100 ENDED AT: 02:56:23                         
          FORMATTING OF CYLINDER 200 ENDED AT: 02:56:24                         
          FORMATTING OF CYLINDER 300 ENDED AT: 02:56:25                         
          FORMATTING OF CYLINDER 400 ENDED AT: 02:56:26                         
          FORMATTING OF CYLINDER 500 ENDED AT: 02:56:37                         
          FORMATTING OF CYLINDER 600 ENDED AT: 02:56:38                         
          FORMATTING OF CYLINDER 700 ENDED AT: 02:56:39                         
          FORMATTING OF CYLINDER 800 ENDED AT: 02:56:39                         
          FORMATTING OF CYLINDER 900 ENDED AT: 02:56:40                         
          FORMATTING OF CYLINDER 1000 ENDED AT: 02:56:41                        
          FORMATTING OF CYLINDER 1100 ENDED AT: 02:56:42                        
[...]                 
          FORMATTING OF CYLINDER 2800 ENDED AT: 02:56:54                     
FORMATTING OF CYLINDER 2900 ENDED AT: 02:56:55                     
FORMATTING OF CYLINDER 3000 ENDED AT: 02:56:56                     
FORMATTING OF CYLINDER 3100 ENDED AT: 02:56:56                     
FORMATTING OF CYLINDER 3200 ENDED AT: 02:56:57                     
FORMATTING OF CYLINDER 3300 ENDED AT: 02:56:58                     
FORMATTING OF CYLINDER 3337 ENDED AT: 02:56:58                     
                                                                   
VOLUME SERIAL NUMBER IS NOW = M01P02                               

          CYLINDER ALLOCATION CURRENTLY IS AS FOLLOWS:
          TYPE     START      END        TOTAL
          ----     -----      ---        -----
          PERM     0          3337       3338
 
ICK00001I FUNCTION COMPLETED, HIGHEST CONDITION CODE WAS 0
          14:28:59    04/28/16
 
ENTER INPUT COMMAND:
 END
 
ICK00002I ICKDSF PROCESSING COMPLETE. MAXIMUM CONDITION CODE WAS 0
ENTER ALLOCATION DATA
TYPE CYLINDERS
.................                                                                   
                                                                   
                                                  VM READ   ZVMWSXX 
```

O CPFMTXA está reportando o mapa de alocação atual do novo volume **M01P02**. Como você pode notar, ele está esperando uma entrada sua (instrução VM READ na parte inferior). Em resposta ao prompt "ENTER ALLOCATION DATA", digite os seguintes comandos:


```
===> PAGE 0 END <Enter>
===> END <Enter>
```

Note que, por padrão, o CPFMTXA aloca todos os cilindros como espaço PERM. O espaço PERM é onde os minidisks da máquina virtual são alocados, mas nós alteramos esse comportamento em resposta ao "ENTER ALLOCATION DATA".


```js #6
ICK03000I CPVOL REPORT FOR 0300 FOLLOWS:
 
          CYLINDER ALLOCATION CURRENTLY IS AS FOLLOWS:
          TYPE     START      END        TOTAL
          ----     -----      ---        -----
          PAGE     0          3337       3338
 
ICK00001I FUNCTION COMPLETED, HIGHEST CONDITION CODE WAS 0
          14:33:26    04/28/16
 
ENTER INPUT COMMAND:
 END
 
ICK00002I ICKDSF PROCESSING COMPLETE. MAXIMUM CONDITION CODE WAS 0
Ready; T=0.06/0.14 14:33:26                                                  
                                                                                
                                                                                
                                                                                
 
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

Para PAGE, SPOOL ou TDISK, precisamos definir um proprietário para o volume, especificando ssiname.sysname.

!!!
Regras para propriedade de volumes: Para volumes de usuário que são **compartilhados** (volumes PERM) em todo o cluster SSI, especifique NOSYS para o sysname (OWNER ssiname.NOSYS). Para volumes de usuário que **não são compartilhados** (volumes PAGE, SPOOL, TDISK) em todo o cluster SSI, especifique o sysname (OWNER ssiname.sysname).
!!!

Defina a propriedade para o volume:


```
===> cpfmtxa 0300 label owner ssiname.sysname
```

Para o nosso exemplo, digite o seguinte e pressione enter:

```
===> cpfmtxa 0300 M01P02 owner nossi.ZVMWSXX
```

Agora você terminou com o dispositivo 300. Desanexe-o do MAINT. Digite:


```
===> detach 300
```

```
detach 300                  
DASD 0300 DETACHED          
Ready; T=0.01/0.01 16:42:59
```

#### Exercício {#sec:formatdisks}

Utilizando o que você aprendeu no laboratório anterior:

- Formate o dispositivo **304** como SPOOL. Por favor, note que para CPFMTXA o nome do tipo é SPOL (apenas 1 "O"), não SPOOL. Nomeie-o como **M01S02**. Não esqueça de configurar a propriedade.
- Formate os dispositivos 301, 302 e 303, rotule-os como, por exemplo, LNX301, LNX302 e LNX303 e aloque o dispositivo inteiro com espaço PERM.


!!!
Hints Attach the device to MAINT

Format it

Detach the device
!!!

Na próxima seção, você aprenderá como adicionar isso ao sistema e tornar a alteração permanente.

### Adicionando PAGE ao SYSTEM

Antes de começar você precisa estar logado como **MAINT**.

Verifique os volumes de paginação no seu Sistema z/VM. Digite o seguinte comando e pressione a tecla Enter:

```
===> query alloc page
```

```
query alloc page                                            
                EXTENT     EXTENT  TOTAL  PAGES   HIGH    % 
VOLID  RDEV      START        END  PAGES IN USE   PAGE USED 
------ ---- ---------- ---------- ------ ------ ------ ---- 
M01P01 0206          1       3337 600660     25     34   1% 
                                  ------ ------        ---- 
SUMMARY                           600660     25          1% 
USABLE                            600660     25          1% 
Ready; T=0.01/0.01 15:59:14                                 
```

Defina a propriedade para o volume (se você ainda não o fez). Digite os seguintes comandos e pressione enter:

```
===> attach 0300 *
===> cpfmtxa 0300 m01p02 owner nossi.zvmwsxx
===> detach 300 *
```

```
att 300 *                                                                       
DASD 0300 ATTACHED TO MAINT 0300 WITH DEVCTL                                    
Ready; T=0.01/0.01 17:13:58                                                     

cpfmtxa 0300 m01p02 owner nossi.zvmwsxx                                         
HCPCCF6209I INVOKING ICKDSF.                                                    
ICK030E DEFINE INPUT  DEVICE: FN FT FM, "CONSOLE", OR "READER"                  
CONSOLE                                                                         
ICK031E DEFINE OUTPUT DEVICE: FN FT FM, "CONSOLE", OR "PRINTER"                 
CONSOLE                                                                         
ICKDSF - CMS/XA/ESA DEVICE SUPPORT FACILITIES 17.0                TIME: 17:14:14
        09/05/13     PAGE   1                                                   
                                                                                
ENTER INPUT COMMAND:                                                            
 CPVOL OWNER UNIT(0300) VFY(M01P02) NOSSI SYSNAME(ZVMWSxx)                      
ICK00700I DEVICE INFORMATION FOR 0300 IS CURRENTLY AS FOLLOWS:                  
          PHYSICAL DEVICE = 3390                                                
          STORAGE CONTROLLER = 3990                                             
          STORAGE CONTROL DESCRIPTOR = E9                                       
          DEVICE DESCRIPTOR = 0C                                                
          ADDITIONAL DEVICE INFORMATION = 4A001F3C                              
          TRKS/CYL = 15, # PRIMARY CYLS = 3338                                  
ICK04000I DEVICE IS IN SIMPLEX STATE                                            
ICK00703I DEVICE IS OPERATED AS A MINIDISK                                      
ICK00091I 0300 NED=002107.900.IBM.82.0000000V9341                               
ICK091I   0300 NED=002107.900.IBM.82.0000000V9341                               
ICK03020I CPVOL WILL PROCESS 0300 FOR VM/ESA MODE                               
ICK03090I VOLUME SERIAL = M01P02                                                
ICK03092I VOLUME VM SSI OWNER = (NO OWNER ASSIGNED)                             
ICK03093I VOLUME SYSTEM OWNER = ZVMWSXX                                         
ICK003D REPLY U TO ALTER VOLUME 0300 CONTENTS, ELSE T                           
                                                                                
                                                            MORE...   ZVMWSXX   
```

```
U                                                                               
ICK03000I CPVOL REPORT FOR 0300 FOLLOWS:                                        
          VOLUME VM SSI OWNER = (NO OWNER ASSIGNED)                             
          VOLUME SYSTEM OWNER = ZVMWSXX                                         
                                                                                
ICK00001I FUNCTION COMPLETED, HIGHEST CONDITION CODE WAS 0                      
          17:14:14    09/05/13                                                  
                                                                                
ENTER INPUT COMMAND:                                                            
 END                                                                            
                                                                                
ICK00002I ICKDSF PROCESSING COMPLETE. MAXIMUM CONDITION CODE WAS 0              
Ready; T=0.01/0.01 17:15:16                                                     
detach 300 *                
DASD 0300 DETACHED BY MAINT 
Ready; T=0.01/0.01 17:16:28                                                                                 
                                                                                
 
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX 
```

Digite o seguinte comando para obter o próximo número de slot disponível. Pressione Pausar/Interromper para limpar a tela até ver o slot 255 (o slot de página padrão na instalação). Em nosso sistema, você verá o slot 254 disponível.

```
===> query cpowned
```

```
 239  ------  ----  -----  Reserved                -------- --------            
 240  ------  ----  -----  Reserved                -------- --------            
 241  ------  ----  -----  Reserved                -------- --------            
 242  ------  ----  -----  Reserved                -------- --------            
 243  ------  ----  -----  Reserved                -------- --------            
 244  ------  ----  -----  Reserved                -------- --------            
 245  ------  ----  -----  Reserved                -------- --------            
 246  ------  ----  -----  Reserved                -------- --------            
 247  ------  ----  -----  Reserved                -------- --------            
 248  ------  ----  -----  Reserved                -------- --------            
 249  ------  ----  -----  Reserved                -------- --------            
 250  ------  ----  -----  Reserved                -------- --------            
 251  ------  ----  -----  Reserved                -------- --------            
 252  ------  ----  -----  Reserved                -------- --------            
 253  ------  ----  -----  Reserved                -------- --------            
 254  ------  ----  -----  Reserved                -------- --------            
 255  M01P01  0206  Own    Online and attached     -------- ZVMWSXX             
Ready; T=0.01/0.01 17:20:18                                                     
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

Digite o seguinte comando para definir dinamicamente o novo volume de página no slot 254.

```
===> define cpowned slot 254 m01p02
```

```
define cpowned slot 254 m01p02
Ready; T=0.01/0.01 17:25:28 
```

Digite o seguinte comando e pressione enter. Agora você anexou um novo volume de página ao seu sistema z/VM.

```
===> attach 300 system m01p02
```

```
attach 0300 system m01p02          
DASD 0300 ATTACHED TO SYSTEM M01P02
Ready; T=0.01/0.01 17:26:36    
```

Digite os seguintes comandos para garantir que o novo volume de página está funcionando em seu sistema z/VM. O novo volume de página deve agora aparecer.

```
===> query alloc page
```

```
q alloc page                                                
                EXTENT     EXTENT  TOTAL  PAGES   HIGH    % 
VOLID  RDEV      START        END  PAGES IN USE   PAGE USED 
------ ---- ---------- ---------- ------ ------ ------ ---- 
M01P02 0300          0       2500 450180      0      0   0% 
M01P01 0206          1       3337 600660     25     34   1% 
                                  ------ ------        ---- 
SUMMARY                            1026K     25          1% 
USABLE                             1026K     25          1% 
Ready; T=0.01/0.01 17:29:42                                 
                                                            
```

```
===> query cpowned
```

```
239  ------  ----  -----  Reserved                -------- --------            
 240  ------  ----  -----  Reserved                -------- --------            
 241  ------  ----  -----  Reserved                -------- --------            
 242  ------  ----  -----  Reserved                -------- --------            
 243  ------  ----  -----  Reserved                -------- --------            
 244  ------  ----  -----  Reserved                -------- --------            
 245  ------  ----  -----  Reserved                -------- --------            
 246  ------  ----  -----  Reserved                -------- --------            
 247  ------  ----  -----  Reserved                -------- --------            
 248  ------  ----  -----  Reserved                -------- --------            
 249  ------  ----  -----  Reserved                -------- --------            
 250  ------  ----  -----  Reserved                -------- --------            
 251  ------  ----  -----  Reserved                -------- --------            
 252  ------  ----  -----  Reserved                -------- --------            
 253  ------  ----  -----  Reserved                -------- --------            
 254  M01P02  0300  Own    Online and attached     -------- ZVMWSXX             
 255  M01P01  0206  Own    Online and attached     -------- ZVMWSXX             
Ready; T=0.01/0.01 17:30:14                                                     
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

Vamos aprender como adicionar esse volume de página ao arquivo SYSTEM CONFIG na Unidade, ou teremos resultados desagradáveis no próximo IPL do z/VM. (Capítulo [4](#ch:systemconfig) - explicaremos melhor o SYSTEM CONFIG).

