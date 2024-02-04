---
order: 60
icon: ":desktop_computer:"
---
# Criando uma Máquina Virtual

O diretório de usuário z/VM especifica a configuração e as características operacionais das máquinas virtuais. Um diretório de usuário z/VM existe em duas formas: uma forma de origem que consiste em um ou mais arquivos CMS, e uma forma de objeto, criada a partir da origem, em um disco formatado CP. Cada máquina virtual tem uma entrada de diretório. Aqui está um exemplo de entrada de diretório:

![Exemplo de Diretório de Usuário](/imgs/userdir.png)

O exemplo na figura acima é um simples convidado Linux. Este capítulo irá guiá-lo para construir um convidado mais complexo usando outras declarações de diretório como PROFILE e DIRMAINT como ferramenta para gerenciar essas entradas.

Uma entrada começa com uma das seguintes declarações: GLOBALDEFS (entrada de definições globais), PROFILE (entrada de perfil), USER (entrada de usuário), IDENTITY (entrada de identidade), ou SUBCONFIG (entrada de subconfiguração). Para nosso workshop, as entradas mais importantes são **PROFILE** e **USER**, de qualquer forma, as outras entradas também são explicadas.

## Definição de DIRETÓRIO

A Definição de DIRETÓRIO consiste em uma ou mais declarações de controle de DIRETÓRIO que definem a saída dos diretórios de objeto. A declaração de DIRETÓRIO define para o CP o dispositivo no qual você alocou espaço para o diretório. (Estes dispositivos também devem ser um volume CP_owned). O diretório de usuário em si é gerenciado pelo DIRMAINT.

A seguinte definição de DIRETÓRIO simplesmente diz que o volume DASD M01RES tem espaço de diretório alocado nele para o diretório de objeto. É um tipo de dispositivo 3390, e 123 é o número do dispositivo virtual que contém o diretório.


```
DIRECTORY 123 3390 M01RES
```

Você pode consultar informações das definições de DIRETÓRIO executando:

```
DIRM DIRECTORY ?
```

## Definição GLOBAL

A seção de Definição Global começa com a declaração de controle de diretório GLOBALDEFS. Ela também inclui declarações de controle de diretório que definem configurações globais a serem usadas em todas as definições de usuário. Esta é uma declaração opcional. Se especificada, deve preceder diretamente todas as definições PROFILE e USER. Só pode haver uma declaração de controle de diretório GLOBALDEFS especificada. O diretório de usuário que acompanha o sistema instalado contém os seguintes Globaldefs:

```
00026 *                                                              
00027 GLOBALDEFS                                                     
00028       POSIXGROUP system 0                                      
00029       POSIXGROUP staff  1                                      
00030       POSIXGROUP bin    2                                      
00031       POSIXGROUP sys    3                                      
00032       POSIXGROUP adm    4                                      
00033       POSIXGROUP mail   6                                      
00034       POSIXGROUP security 7                                    
00035       POSIXGROUP nobody 4294967294                             
00036 *                                                              
00037 ***************************************************************
```

## Definições de PROFILE

Cada definição de PROFILE começa com uma declaração de controle de diretório PROFILE e consolida outras declarações de controle de diretório que são comumente usadas em vários usuários. Quando PROFILE é especificado, ele deve seguir a última declaração de DIRETÓRIO, a seção de definição global (se houver) e preceder as declarações de USER.

Este é um exemplo de perfil:


```
PROFILE IBMDFLT           
  SPOOL 000C 2540 READER *
  SPOOL 000D 2540 PUNCH A 
  SPOOL 000E 1403 A       
  CONSOLE 009 3215 T      
 LINK MAINT 0190 0190 RR  
 LINK MAINT 019D 019D RR  
 LINK MAINT 019E 019E RR  
 LINK MAINT 0402 0402 RR  
 LINK MAINT 0401 0401 RR   
```

## Definições de USER

Definições de usuário (definições de máquina virtual) começam com uma declaração de controle de diretório USER e definem uma máquina virtual individual.

A declaração USER inicia a entrada de diretório de cada máquina virtual. Ela também define um ID de usuário e senha, um logon e tamanho máximo de armazenamento virtual, e privilégios de comando.

Aqui está um exemplo de definições de usuário, neste caso o usuário PMAINT:


```
USER PMAINT   WD5JU8QP  128M 1000M G
 AUTOLOG AUTOLOG1 OP1 MAINT         
 ACCOUNT 1 SYSPROG                  
 MACH ESA                           
 POSIXINFO UID 0 GNAME system       
 OPTION MAINTCCW LNKS LNKE LNKNOPAS 
 IPL 190                            
 NAMESAVE GCS                       
 NAMESAVE VTAM                      
 CONSOLE 009 3215 T                 
 LINK MAINT 190 190 RR              
 LINK MAINT 193 193 RR              
 LINK MAINT 19E 19E RR              
 LINK MAINT 19D 19D RR              
 LINK MAINT 401 401 RR              
 LINK MAINT 402 402 RR              
 SPOOL 00C 2540 READER *            
 SPOOL 00D 2540 PUNCH  A            
 SPOOL 00E 1403 A               
```

A declaração de controle de diretório acima define uma máquina virtual chamada PMAINT, cuja senha é 'WD5JU8QP'. Quando PMAINT faz login, são alocados 128 Megabytes de armazenamento virtual. É permitido um máximo de 1000 Megabytes de armazenamento virtual, e ela é da classe G, ou usuário geral.

A tabela a seguir contém uma descrição de diferentes classes de comando:


| Classe | Usuário e Função                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A      | Operador do Sistema: O usuário da classe A controla o sistema z/VM. O operador do sistema é responsável pela disponibilidade do sistema z/VM e seus recursos. Além disso, o operador do sistema controla a contabilidade do sistema, mensagens de transmissão, opções de desempenho de máquina virtual e outras opções que afetam o desempenho geral do z/VM. Nota: O usuário da classe A que é automaticamente logado durante a inicialização do CP é designado como o operador principal do sistema. |
| B      | Operador de Recursos do Sistema: O usuário da classe B controla todos os recursos reais do sistema z/VM, exceto aqueles controlados pelo operador do sistema e pelo operador de spooling.                                                                                                                                                                                                                                                                                                                                          |
| C      | Programador do Sistema: O usuário da classe C atualiza ou muda parâmetros de todo o sistema do z/VM.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| D      | Operador de Spooling: O usuário da classe D controla arquivos de spool e os equipamentos reais de leitor, impressora e perfurador do sistema alocados para uso de spooling.                                                                                                                                                                                                                                                                                                                                                        |
| E      | Analista de Sistema: O usuário da classe E examina e salva dados de operação do sistema em áreas de armazenamento z/VM especificadas.                                                                                                                                                                                                                                                                                                                                                                                               |
| F      | Representante de Serviço: O usuário da classe F obtém e examina em detalhes dados sobre dispositivos de entrada e saída conectados ao sistema z/VM. Esta classe de privilégio é reservada apenas para uso da IBM.                                                                                                                                                                                                                                                                                                               |
| G      | Usuário Geral: O usuário da classe G controla funções associadas a uma máquina virtual particular.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Qualquer | Comandos pertencentes à classe "Qualquer" estão disponíveis para qualquer usuário, independentemente da classe de privilégio do usuário. Esses comandos são principalmente aqueles usados para ganhar acesso ou renunciar ao acesso do sistema z/VM.                                                                                                                                                                                                                                                                                |
| H      | Reservado para uso da IBM.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| I - Z - 6 | Essas classes são reservadas para redefinição por cada instalação para seu próprio uso (usando declarações ou comandos MODIFY).

## Criando um usuário CMS simples

Esta seção fornecerá instruções para criar uma VM CMS. No primeiro passo, nem mesmo teremos discos nesta VM e aprenderemos como adicionar minidisks e redimensioná-los. Esta máquina será usada para hospedar alguns arquivos que serão usados na próxima seção.

### Como criar uma nova VM

Nosso objetivo é criar uma nova VM chamada *VMTOOLS*. Existe um tipo especial de arquivo chamado *DIRECT*. Vamos criar um novo arquivo chamado VMTOOLS DIRECT que hospedará as declarações de diretório necessárias para executar nossa VM CMS.


- Logado como MAINT

- Crie um arquivo chamado VMTOOLS DIRECT

```
X VMTOOLS DIRECT A
```

- Insira o seguinte conteudo:

```js #
USER VMTOOLS WORK2016 64M 64M G
IPL CMS
SPOOL 000C 2540 READER *
SPOOL 000D 2540 PUNCH A
SPOOL 000E 1403 A
CONSOLE 009 3215 T
LINK MAINT 0190 0190 RR
LINK MAINT 019D 019D RR
LINK MAINT 019E 019E RR
```

A linha 1 define o nome, senha, quantidade de memória e classe de privilégio da nossa nova VM.

As linhas 2, 7, 8, 9 estão relacionadas ao CMS. Estamos vinculando discos que são necessários para o IPL do CMS em nossa VM.

As linhas 3, 4, 5, 6 estão relacionadas a alguns dispositivos padrão, como READER, PUNCH, PRINTER e CONSOLE.

- Salve o arquivo e saia

```
====> FILE
```

- Para adicionar nossa nova VM ao diretório, vamos usar o comando DIRM ADD:


```
DIRM ADD VMTOOLS
```


- Se você obteve RC = 0, estamos bem, caso contrário, há um erro de digitação no seu arquivo DIRECT VMTOOLS, corrija o erro e tente novamente.

### Como adicionar um minidisco

- Tente fazer login na sua nova VM e você verá uma mensagem:


```
z/VM Version 6 Release 3.0, Service Level 1501 (64-bit), 
built on IBM Virtualization Technology
There is no logmsg data
FILES: 0003 RDR,   NO PRT,   NO PUN
LOGON AT 13:30:14 EDT SATURDAY 07/16/16
z/VM V6.3.0    2015-06-18 16:09
 
DMSACP113S A(191) not attached or invalid device address
Ready; T=0.03/0.03 13:30:16
```

- Ops! Não temos um "Disco-A" para nossa VM... precisamos criá-lo.

- Deslogue de VMTOOLS

- Faça login em MAINT

- Execute:

```
DIRM FOR VMTOOLS AMD
```

- Será exibido um PAINEL que precisa ser preenchido como este (As senhas estão em branco, mas você pode definir como: READ WRITE MULTI):

![DASD AMD](/imgs/dasdamd.png)

- Estamos criando um novo minidisco 191, do nosso GRUPO LINUX, com 5 cilindros. O rótulo é VMT191.

- O resultado esperado é:

![Resultado DASD AMD](/imgs/dasdamdresult.png)

- Se você quiser verificar quão diferente está o diretório da sua VMTOOLS execute:


```
DIRM FOR VMTOOLS REView
```

- RECEIVE \<n\> (REP

Onde \<n\> é o id do leitor que aparecerá no início da resposta.response.

![Reader ID](/imgs/receivefile.png)

- Você pode abrir usando XEDIT e verá a nova declaração MDISK no final

```
X VMTOOLS DIRECT
```

```
MDISK 0191 3390 0001 5 LNX301 MR XXXXXXXX XXXXXXXX XXXXXXXX
```


### Como mudar o tamanho do minidisco


- Execute:

```
DIRM FOR VMTOOLS CMD
```

- DIRM FOR VMTOOLS CMD abrirá um painel

- Vamos aumentar o tamanho do minidisco 191 de 5 cil para 10 cil

- Siga o painel:

![DASD CMD](/imgs/dasdcmd.png)

- Pressione F5 para enviar.


### Exercício

Adicione um novo minidisco:

- Address: 192
-  Device type: 3390 
-   On AUTOG: 
    -     Size: 300
    -     Grpname: LINUX 
    -     Link Mode: MR 
    -     Label: LNX192 
    -     Passwords: READ WRITE MULTI


### Transferir arquivos via FTP para VMTOOLS 192

Durante a aula, o instrutor ensinará como transferir arquivos via FTP para nosso novo minidisco 192. Esses arquivos são essenciais para a instalação do Linux. Estes passos não estão documentados aqui porque dependem do seu ambiente.

Estas instruções estão presentes no conjunto de slides.

## Criando nosso Perfil Linux

Nesta seção, será criado um perfil para ser incluído pelas máquinas virtuais Linux.

- Faça login como MAINT. 
- Crie o arquivo fonte do diretório do usuário.


```
===> x LINDFLT DIRECT
```


- Em XEDIT digite "INPUT" para habilitar o modo de entrada


```
===> INPUT
```

- Cole o seguinte conteúdo

```
PROFILE LINDFLT
COMMAND CP SET PF12 RETRIEVE
COMMAND CP SET PF11 RETRIEVE FORWARD
COMMAND SET VSWITCH VSW1 GRANT &USERID
COMMAND DEFINE NIC 600 TYPE QDIO
COMMAND COUPLE 600 TO SYSTEM VSW1
IPL CMS                                                                   
MACH ESA 2                                                                
CPU 00 BASE
OPTION APPLMON
SPOOL 000C 2540 READER *         
SPOOL 000D 2540 PUNCH A     
SPOOL 000E 1403 A              
CONSOLE 009 3215 T       
LINK MAINT 0190 0190 RR    
LINK MAINT 019D 019D RR      
LINK MAINT 019E 019E RR   
LINK VMTOOLS 192 191 RR
LINK TCPMAINT 592 592 RR 
```

- Pressione ENTER duas vezes para sair do modo de entrada.

!!! Explanation

```js
PROFILE LINDFLT - Define the profile named LINDFLT 
COMMAND CP SET
PF12 RETRIEVE - Enable F12 to retrieve commands 
COMMAND CP SET PF11
RETRIEVE FORWARD - Enable F11 to 
COMMAND SET VSWITCH VSW1 GRANT &USERID - Grant access to VSW1 
COMMAND DEFINE NIC 600 TYPE QDIO - Define a network card on vaddr 600 
COMMAND COUPLE 600 TO SYSTEM VSW1 - Couple the NIC to VSW1 
IPL CMS - When the user ID is logged on, IPL CMS 
MACH ESA 2 - The machine architecture is ESA with a maximum of 4 CPUs. 
OPTION APPLMON - To enable Linux guest for data gathering 
CPU 00 BASE - CPU 00 is the base and the only CPU set for now 
SPOOL 000C 2540 READER \* - Definition of a virtual reader 
SPOOL 000D 2540 PUNCH A - Definition of a virtual punch 
SPOOL 000E 1403 A - Definition of a virtual printer 
CONSOLE 009 3215 T - Definition of a console 
LINK MAINT 0190 0190 RR - Link to MAINT's 190 minidisk with read access 
LINK MAINT 019D 019D RR - Link to MAINT's 19D minidisk with read access
LINK MAINT 019E 019E RR - Link to MAINT's 19E minidisk with read access 
LINK VMTOOLS 192 191 RR - Link to VMTOOLS 192 minidisk as 191 with read access 
LINK TCPMAINT 592 592 RR - Link to TCPMAINT's 592 minidisk with read access. This is where the ftp utility is located.
```

!!!


- Salve e saia do XEDIT usando o comando "file".

Para adicionar nosso novo perfil ao diretório z/VM, use DIRM ADD:


```
DIRM ADD LINDFLT
```

```
dirm add lindflt
PUN FILE 0021 SENT TO   DIRMAINT RDR AS  0051 RECS 0020 CPY  001 0 NOHOLD NOKEEP
DVHXMT1191I Your ADD request has been sent for processing to DIRMAINT at
DVHXMT1191I ZVMWSXX.
Ready; T=0.01/0.01 14:37:31
 DVHREQ2288I Your ADD request for LINDFLT at * has been accepted.
 DVHBIU3450I The source for directory entry LINDFLT has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHBIU3428I Changes made to directory entry LINDFLT have been placed
 DVHBIU3428I online.
 DVHBIU3450I The source for directory entry LINDFLT has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHBIU3428I Changes made to directory entry LINDFLT have been placed
 DVHBIU3428I online.
 DVHBIU3450I The source for directory entry LINDFLT has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHBIU3428I Changes made to directory entry LINDFLT have been placed
 DVHBIU3428I online.
 DVHBIU3450I The source for directory entry LINDFLT has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHBIU3428I Changes made to directory entry LINDFLT have been placed
 DVHBIU3428I online.
 DVHBIU3450I The source for directory entry LINDFLT has been updated.
 DVHBIU3424I The next ONLINE will take place immediately.
 DVHBIU3428I Changes made to directory entry LINDFLT have been placed
 DVHBIU3428I online.
 DVHREQ2289I Your ADD request for LINDFLT at * has completed; with RC =
 DVHREQ2289I 0.
 

                                                            RUNNING   ZVMWSXX 
```


## Criar a máquina virtual LINUX1

Para criar uma imagem Linux, usaremos um DASD 3390-09 (10016 cil) como base. O DIRMAINT possui um recurso chamado PROTODIRs que podemos usar para criar um modelo de diretório para nosso Linux.

- Primeiro, pegue uma cópia do PROTODIR LINUX


```
===> DIRM SEND LINUX PROTODIR
```


- **Receba** um arquivo chamado LINUX PROTODIR e edite-o


```
X LINUX PROTODIR
```


- No XEDIT digite "INPUT" para habilitar o modo de entrada


```
===> INPUT
```


- Em XEDIT digite "INPUT" para habilitar o modo de entrada


```
USER LINUX NOLOG 1G 1G G
INCLUDE LINDFLT       
MDISK 0100 3390 AUTOG 1600 LINUX MR READ WRITE MULTIPLE
MDISK 0101 3390 AUTOG 800 LINUX MR READ WRITE MULTIPLE
MDISK 0102 3390 AUTOG 6016 LINUX MR READ WRITE MULTIPLE
MDISK 0103 3390 AUTOG 800 LINUX MR READ WRITE MULTIPLE
MDISK 0104 3390 AUTOG 800 LINUX MR READ WRITE MULTIPLE
```

- Salve e feche o arquivo

- Aplique as mudanças do LINUX PROTODIR usando o seguinte comando


```
===> DIRM FILE LINUX PROTODIR
```

```
dirm file linux protodir
PUN FILE 0320 SENT TO   DIRMAINT RDR AS  0424 RECS 0013 CPY  001 0 NOHOLD NOKEEP
DVHXMT1191I Your FILE request has been sent for processing to DIRMAINT
DVHXMT1191I at SSI1.
Ready; T=0.12/0.14 13:34:17
 DVHREQ2288I Your FILE request for MAINT at * has been accepted.
 DVHRCV3821I File LINUX PROTODIR E2 has been received; RC = 0.
 DVHREQ2289I Your FILE request for MAINT at * has completed; with RC = 0.
```

- Crie a definição da máquina virtual para LINUX1.


```
===> dirm add linux1 like linux pw zcloud
```


- Se você receber RC = 0, você deve ser capaz de consultar sua nova VM:


```
q linux1
HCPCQU045E LINUX1 not logged on
Ready(00045); T=0.01/0.01 13:36:33
```

- Se você receber a resposta "Invalid option - LINUX1", algo deu errado no passo acima

```
q linux1
HCPCQV003E Invalid option - LINUX1
Ready(00003); T=0.01/0.01 13:38:31
```

### Exercício

- Crie a VM LINUX2. Esta VM será usada no próximo capítulo



