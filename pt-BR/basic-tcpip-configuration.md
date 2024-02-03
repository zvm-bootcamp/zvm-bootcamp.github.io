---
order: 75
icon: ":link:"
---
# Configuração Básica de TCP/IP

## Máquinas de Serviço TCPIP

Esta seção descreve as máquinas virtuais que são necessárias para fornecer serviços básicos e opcionais de TCP/IP. As máquinas virtuais listadas aqui compõem um conjunto de máquinas virtuais TCP/IP "padrão" que são definidas como parte do sistema z/VM quando ele é instalado.

Embora várias máquinas virtuais TCP/IP tenham requisitos específicos de definição, todos os servidores TCP/IP devem manter links para os seguintes minidisks, para permitir a operação correta:


| Minidisk      | Descrição                         |
| ------------- | --------------------------------- |
| TCPMAINT 592  | Disco de código do cliente        |
| TCPMAINT 591  | Disco de código do servidor       |
| TCPMAINT 198  | Arquivo de configuração ou disco de personalização |


## Máquinas Virtuais Necessárias

As seguintes máquinas virtuais são necessárias para fornecer serviços básicos de TCP/IP:


| Máquina    | Função                                                                                      |
|------------|-----------------------------------------------------------------------------------------------|
| 6VMTCP30   | Mantém o sistema TCP/IP. Os recursos de instalação e serviço são de propriedade deste ID de usuário.   |
| TCPIP      | Fornece serviços de comunicação TCP/IP. O servidor Telnet é implementado como um "cliente interno" dentro da máquina virtual TCPIP. |
| TCPMAINT   | Possui recursos de produção TCP/IP --- os discos 198, 591 e 592.                              |

## Máquinas Virtuais Opcionais

Existem muitas máquinas virtuais opcionais que você pode configurar para desempenhar funções de servidor TCP/IP. Alguns desses servidores incluem FTPSERVE, IMAP, PORTMAP, NAMESRV, SSLSERV, SMTP, etc.

## Arquivos de Configuração

Esta seção lista os vários arquivos de configuração TCP/IP que são necessários para fornecer serviços básicos TCP/IP para a maioria dos ambientes.

O primeiro arquivo, IBM DTCPARMS, contém definições de configuração do servidor. Os próximos três arquivos, PROFILE TCPIP, HOSTS LOCAL e ETC HOSTS, são arquivos de configuração para o servidor virtual TCPIP. Os próximos dois arquivos, TCPIP DATA e ETC SERVICES, precisam estar acessíveis a todos os servidores, aplicações e usuários TCP/IP; esses arquivos contêm informações que são (ou podem ser) referenciadas por todos os usuários. ETC GATEWAYS contém informações de roteamento para redes e hosts distantes.

### Os arquivos DTCPARMS

A configuração de cada servidor é controlada por um conjunto de arquivos com um tipo de arquivo de DTCPARMS. Estes arquivos podem conter dois tipos de informações:

- Nomes de classes de servidores que definem os protocolos de aplicação disponíveis para todas as máquinas virtuais servidoras. & IDs de usuários individuais do servidor e sua classe de servidor associada, bem como as características operacionais do servidor (segurança, dispositivos, parâmetros, etc.).

O programa de inicialização do servidor TCP/IP procura por definições de servidor de forma hierárquica. A tabela a seguir lista os arquivos DTCPARMS na ordem em que são pesquisados, juntamente com uma descrição de cada arquivo.

| Arquivo          | Propósito                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| userid DTCPARMS  | Pode ser usado para servidores que não requerem configuração pelo administrador TCP/IP, como um servidor de teste. Tal arquivo pode residir comumente em um disco ou diretório do servidor acessado em modo de arquivo A.                                                                                                                                                                                                                                                                      |
| nodeid DTCPARMS  | Útil para configurações com DASD compartilhado. O ID do nó usado é o ID do nó retornado pelo comando IDENTIFY do CMS. Este arquivo deve ser mantido no disco TCPMAINT 198.                                                                                                                                                                                                                                                                                                                      |
| SYSTEM DTCPARMS  | A maioria das configurações de servidor personalizadas deve ser mantida neste arquivo. Este arquivo deve ser mantido no disco TCPMAINT 198.                                                                                                                                                                                                                                                                                                                                                    |
| IBM DTCPARMS     | Classes de servidores fornecidas pela IBM, bem como as configurações padrão do servidor, são fornecidas por este arquivo. Este arquivo reside no disco TCPMAINT 591 e nunca deve ser modificado, pois pode ser substituído quando o serviço é aplicado, ou quando uma nova versão é instalada. Todas as modificações necessárias para sua instalação devem ser colocadas em SYSTEM DTCPARMS (ou, nodeID DTCPARMS, conforme necessário). |

### O Arquivo TCPIP DATA

O arquivo TCPIP DATA define parâmetros do sistema usados por aplicações cliente TCP/IP. É usado para especificar informações de configuração para sistemas host únicos ou múltiplos. Também permite que você especifique:

- Nome do host do host VM & ID do usuário da máquina virtual TCPIP & Origem do domínio do host & Rastreamento de saída & Especificações do servidor de nomes

Um arquivo de amostra TCP/IP DATA é enviado como TCPIP SDATA no disco TCPMAINT 592.

### Os Arquivos ETC HOSTS

Os arquivos host locais contêm informações necessárias para a resolução de nomes de host locais. Qualquer nome de domínio ou endereço IP especificado neste arquivo é acessível para uso em sua rede. Arquivos host locais são usados para criar a tabela do site, que permite a resolução de nomes e a resolução de nomes inversa sem usar um servidor de nomes de domínio.

O TCP/IP para z/VM oferece dois arquivos host locais para resolução de nomes de domínio e resolução de nomes inversa. O antigo arquivo HOSTS LOCAL (que suporta apenas IPv4) e o preferido arquivo ETC HOSTS (que suporta tanto IPv4 quanto IPv6).

O arquivo ETC HOSTS não requer processamento adicional para criar as tabelas do site usadas para resolução de nomes. As tabelas do site são criadas dinamicamente pelo resolvedor quando o arquivo ETC HOSTS é usado. O uso do arquivo HOSTS LOCAL requer que você execute o comando MAKESITE para criar as tabelas do site. Sempre que alterações são feitas no arquivo HOSTS LOCAL, você deve executar o comando MAKESITE para recriar as tabelas do site.

Um arquivo de amostra, ETCHOSTS SAMPLE, é fornecido com as fitas de distribuição TCP/IP no.

### O arquivo de PROFILE do servidor TCPIP

Quando a máquina virtual TCPIP é iniciada, os parâmetros de operação e configuração do TCP/IP são lidos de um arquivo de configuração inicial. O TCP/IP procura por um arquivo de configuração inicial na seguinte ordem e usa o primeiro arquivo presente nessa ordem:

- userid TCPIP, onde userid é o ID do usuário do servidor TCP/IP
- node_name TCPIP, onde node_name é o nome do nó do sistema retornado pelo comando IDENTIFY do CMS & PROFILE TCPIP

Este arquivo é usado para personalizar seu sistema, especificar a operação do sistema, parâmetros de Telnet e de rede. Se nenhum arquivo for encontrado, o TCP/IP usa valores padrão do servidor.

Um arquivo de configuração inicial de amostra é fornecido como PROFILE STCPIP no disco TCPMAINT 591.


## Exercícios

### IPWIZARD

You can initially configure TCP/IP via the IPWIZARD command which is
generally used just once. After IPWIZARD creates the initial
configuration files, they are typically maintained manually.


- Faça login como MAINT.
- O comando IPWIZARD está no disco MAINT 193.
Emita o comando ACCESS para que você possa acessar o IPWIZARD a partir desse minidisco.


```
==> acc 193 g
```

- Invoque o IPWIZARD.

```
==> ipwizard
```

- No painel '\*\*\* z/VM TCP/IP Configuration Wizard \*\*\*'. Preencha os seguintes dados:


```
    *** z/VM TCP/IP Configuration Wizard ***                 
                                                                             
The items that follow describe your z/VM host                                
                                                                             
User ID of VM TCP/IP Stack Virtual Machine:   TCPIP___                       
                                                                             
Host Name:     ZVMWSxx (your group name) ___________                                          
Domain Name:   mycompany.com_________                      
                                                                             
Gateway IP Address:  YOUR_GW_ADDR_______________________                 
                                                                             
DNS Addresses:                                                               
1) YOUR_DNS_______                                                           
2) _______________                                                           
3) _______________                          
       PF1 = HELP   PF3 = QUIT   PF8 = Continue   ENTER = Refresh    
```

- Continue com o próximo passo.

**Pressione F8**

- No painel '\*\*\* General Interface Configuration Panel \*\*\*'.
Preencha os seguintes dados:


```
                  *** General Interface Configuration Panel ***             
                                                                            
Interface Name:  ETH0____________     Device Number:  0800                  
                                                                            
IP Address:      (*|\textcolor{red}{YOUR\_IP\_ADDR}|*) (your team IP number)_                                            
Subnet Mask:     (*|\textcolor{red}{YOUR\_MASK}|*)__                                            
                                                                            
Path MTU Discovery (Optional):  _ Enabled      _ Disabled                   
                                                                            
Interface Type (Select one): (*|\textcolor{red}{Ask the layer to the instructor and put an 'X' there}|*)
                                                                            
   _    QDIO (layer 3)      _    QDIO (layer 2)      _    LCS               
   _    HiperSockets        _    CTC                                        
                                                                            
                                                                            
                                                                            
PF1 = HELP  PF3 = QUIT  PF7 = Backward  PF8 = Continue  ENTER = Refresh   
```

- Continue com o próximo passo.

**Pressione F8**

- No painel '\*\*\* QDIO Interface Configuration Panel \*\*\*'. Preencha os seguintes dados:
```
                   *** QDIO Interface Configuration Panel ***                
                                                                             
VLAN ID (optional):  ____                                                    
                                                                             
                                                                             
Router Type (Select one):                                                    
                                                                             
   _    Primary             _    Secondary           X    None               
                                                                             
                                                                             
Maximum Transmission Unit (MTU) size:  1500_                                 
                                                                             
                                                                             
Port Number (optional):  __                                                  
                                                                             
                                                                             
 PF1 = HELP  PF3 = QUIT  PF5 = Process  PF7 = Backward  ENTER = Refresh      
```

- Inicie a configuração da rede.

**Pressione a tecla PF5 para Processar**

- A pilha TCP/IP (TCPIP) deve ser reiniciada como parte deste procedimento.
Você gostaria de reiniciar o TCPIP e continuar?

**Digite '1' para Sim**

- A configuração do TCP/IP está completa quando você vê estas mensagens:


```
Successfully PINGed Interface (172.24.200.222)                    
Successfully PINGed Gateway (172.24.200.1)                        
Successfully PINGed DNS (172.24.200.241)                          
DTCIPW2519I Configuration complete; connectivity has been verified
DTCIPW2520I File PROFILE TCPIP created on TCPIP 198               
DTCIPW2520I File TCPIP DATA created on TCPIP 592                  
DTCIPW2520I File SYSTEM DTCPARMS created on TCPIP 198             
(*|\textcolor{red}{IUGIPW8392I IPWIZARD EXEC ENDED SUCCESSFULLY                      }|*)
DMSVML2061I TCPIP 592 released                                    
Ready; T=0.15/0.23 23:50:42      
```

- Neste ponto, seu sistema z/VM deve estar na rede. Vá para um prompt do DOS (ou sessão Linux) e tente fazer ping no seu sistema z/VM.


```
# ping <your ip>
```


### Visualizando arquivos de configuração TCP/IP

Vamos aprender o que o IPWIZARD fez por você.

- Faça login na máquina virtual TCPMAINT

**Desconecte-se de MAINT**

**Faça login em TCPMAINT**


```
LOGON TCPMAINT                                          
z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),
built on IBM Virtualization Technology                  
There is no logmsg data                                 
FILES: 0009 RDR,   NO PRT,   NO PUN                     
LOGON AT 10:19:32 EDT MONDAY 09/23/13                   
z/VM V6.3.0    2013-09-13 00:01                         
                                                        
Profile..: Spooling console to self (TCPMAINT)...       
Profile..: Setting PF Keys...                           
PF12 RETRIEVE BACKWARD                                  
PF24 RETRIEVE BACKWARD                                  
Profile..: Setting minidisk environment workspace...    
DMSACC724I 191 replaces A (191)                         
                                                        
Profile..: Setup complete                               
Ready; T=0.01/0.01 10:19:32        
```

- Liste os discos CMS que são acessados através do comando QUERY DISK. Note
que o disco TCPMAINT 198 é acessado como seu disco:


```
===> q disk
```

```
q disk                                                                          
LABEL  VDEV M  STAT   CYL TYPE BLKSZ   FILES  BLKS USED-(%) BLKS LEFT  BLK TOTAL
TCM191 191  A   R/W     7 3390 4096        2         11-01       1249       1260
TCM198 198  D   R/W     9 3390 4096        4         11-01       1609       1620
TCM591 591  E   R/W   122 3390 4096      166       7139-33      14821      21960
TCM592 592  F   R/W   140 3390 4096      858      10312-41      14888      25200
MNT190 190  S   R/O   207 3390 4096      696      18085-49      19175      37260
MNT19E 19E  Y/S R/O   500 3390 4096     1123      30403-34      59597      90000
MNT191 120  Z   R/O   175 3390 4096        7         24-01      31476      31500
Ready; T=0.01/0.01 10:24:34                                                     
```

- Este é um disco importante para arquivos de configuração TCP/IP. Liste todos os
arquivos neste disco. Qual é o comando?


===\>

```
TCPMAINT FILELIST A0  V 169  Trunc=169 Size=2 Line=1 Col=1 Alt=0               
Cmd   Filename Filetype Fm Format Lrecl    Records     Blocks   Date     Time   
      PROFILE  TCPIP    D1 V         73         55          1  9/19/14 14:39:11 
      SYSTEM   DTCPARMS D1 V         71          7          1  9/19/14 14:38:58 
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
    
                                                                            
                                                                                
1= Help       2= Refresh  3= Quit   4= Sort(type)  5= Sort(date)  6= Sort(size) 
7= Backward   8= Forward  9= FL /n 10=            11= XEDIT/LIST 12= Cursor     
                                                                                
====>                                                                           
                                                            X E D I T  1 File   
```


#### PROFILE TCPIP

- Olhe para o arquivo PROFILE TCPIP. Você pode digitar um **X**, que é um
sinônimo para **X**EDIT diretamente no comando FILELIST ao lado do arquivo que
você quer editar. & Procure pela string DEVICE. Você deve ver muitos dos
valores que digitou no IPWIZARD. A seguir, um exemplo de arquivo
para **ZVMWSXX**:


```
===> /device
```

```
PROFILE  TCPIP    D1  V 80  Trunc=80 Size=55 Line=37 Col=1 Alt=0               
                                                                                
00037 DEVICE DEV@0800  OSD 0800   NONROUTER                                     
00038 LINK ETH0 QDIOETHERNET DEV@0800   MTU 1800 IP                             
00039 ; (End DEVICE and LINK statements)                                        
00040 ; ----------------------------------------------------------------------  
00041 ; ----------------------------------------------------------------------  
00042 HOME                                                                      
00043 172.24.200.222 255.255.255.0 ETH0                                         
00044 ; (End HOME Address information)                                          
00045 ; ----------------------------------------------------------------------  
00046 GATEWAY                                                                   
00047 ; Network       Subnet          First           Link             MTU      
00048 ; Address       Mask            Hop             Name             Size     
00049 ; ------------- --------------- --------------- ---------------- -----    
00050 DEFAULTNET                      172.24.200.1    ETH0             1800     
00051 ; (End GATEWAY Static Routing information)                                
00052 ; ----------------------------------------------------------------------  
00053 START DEV@0800                                                            
00054 ; (End START statements)                                                  
00055 ; ----------------------------------------------------------------------  
00056 * * * End of File * * *                                                   
                                                                                
                                                                                
 
                                                                                
                                                                                
                                                                                
====>                                                                           
```

- Exit this file, enter:

```
 ===> qq
```


#### SYSTEM DTCPARMS

- Agora olhe para o arquivo SYSTEM DTCPARMS. & Você deve ver o seguinte:


```
SYSTEM   DTCPARMS D1  V 80  Trunc=80 Size=7 Line=0 Col=1 Alt=0                 
                                                                                
00000 * * * Top of File * * *                                                   
00001 .**********************************************************************   
00002 .* SYSTEM DTCPARMS created by DTCIPWIZ EXEC on 19 Sep 2013                
00003 .* Configuration program run by MAINT at 23:50:15                         
00004 .**********************************************************************   
00005 :nick.TCPIP    :type.server                                               
00006                :class.stack                                               
00007                :attach.0800-0802                                          
00008 * * * End of File * * *                                                   
                                                                                
                                                                                
 
                                                                                
====>                                                                           
```

Este arquivo é como os dispositivos OSA 800, 801 e 802 são anexados à
máquina de serviço TCPIP.

- Saia deste arquivo:

**Pressione a tecla F3 (ou digite qq)**

- Se você ainda está no modo FILELIST, saia e retorne ao modo RUNNING.

**Pressione a tecla F3**

### Renomeando o arquivo PROFILE TCPIP

Uma mudança que é recomendada é renomear seu arquivo de configuração principal, PROFILE TCPIP. É possível que a aplicação de serviço ao z/VM possa sobrescrever o arquivo PROFILE TCPIP.

- Use o comando RENAME para mudar o arquivo:


```
===> rename profile tcpip d zvmwsxx = d
```

- Agora você deve testar essa mudança. Você pode fazer isso forçando o ID de usuário TCPIP a sair do sistema (logoff) e, em seguida, fazendo login interativamente e observando-o voltar. Isso é análogo a um comando "service network restart" no Linux. Tenha cuidado ao fazer isso. Se você estiver usando a rede para acessar seu sistema, você perderá imediatamente a conexão.


```
===> force tcpip
```

- Agora faça login em TCPIP e inicie a pilha TCP/IP.

```
===> logoff TCPMAINT
===> logon TCPIP
```

Pressione **Enter** para executar o PROFILE EXEC

Pressione **Enter** novamente para iniciar a pilha TCP/IP. Observe que seu PROFILE renomeado é utilizado:


```
LOGON TCPIP                                                                     
z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),                        
built on IBM Virtualization Technology                                          
There is no logmsg data                                                         
FILES:   NO RDR, 0001 PRT,   NO PUN                                             
LOGON AT 12:25:00 EDT MONDAY 09/23/13                                           
z/VM V6.3.0    2013-09-13 00:01                                                 
                                                                                
DMSACP723I D (198) R/O                                                          
DMSACP723I E (591) R/O                                                          
DMSACP723I F (592) R/O                                                          
Ready; T=0.01/0.01 12:25:03                                                     
DTCRUN1022I Console log will be sent to default owner ID: TCPMAINT              
DTCRUN1046I Using 'server' definition (TCPIP) from file: SYSTEM DTCPARMS D1     
DTCRUN1046I Using 'class' definition (STACK) from file: IBM DTCPARMS E1         
DTCRUN1096I STORAGE = 128M                                                      
DTCRUN1038I Server is not configured to support secure connections              
DTCRUN1021R To cancel TCP/IP Stack startup, type any non-blank character and    
            press ENTER. To continue startup, just press ENTER.                 
                                                                                
DTCRUN1011I Server started at 12:25:07 on 23 Sep 2013 (Monday)                  
DTCRUN1011I Running server command: TCPIP                                       
DTCRUN1011I No parameters in use                                                
DTCRUN1048I SLVL service information for: TCPIP MODULE E2                       
DTCRUN1048I CMHOSTN  ZVM620    CMPRCOM  ZVM630    CMTCPPR  ZVM630               
DTCRUN1048I CMUNTOK  ZVM620    FPIPDOW  ZVM630    FPI6DOW  ZVM630               
DTCRUN1048I FPNOTIF  ZVM630    FPPARSE  ZVM630    FPQDIO   ZVM630               
DTCRUN1048I FPQUEUE  PK30609   FPROUND  ZVM620    FPSCHED  ZVM630               
DTCRUN1048I FPTCPDOW ZVM630    FPTCPREQ ZVM620    FPTCPUP  ZVM620               
DTCRUN1048I FPTIMER  ZVM620    FPTOCLAW PQ95126   FPTOCTC  PQ70168              
                                                                                
                                                            MORE...   ZVMWSXX   
```

```
DTCRUN1048I TCMIB    ZVM630    TCMON    ZVM630    TCMPRIO  ZVM620               
DTCRUN1048I TCNED    ZVM630    TCNOTIF  ZVM630    TCOFFLOP PQ21340              
DTCRUN1048I TCPARSE  ZVM630    TCPDOWN  ZVM620    TCPERUP  PQ49456              
DTCRUN1048I TCPEXITS ZVM620    TCPIP    ZVM620    TCPIPIN  ZVM630               
DTCRUN1048I TCPKT    ZVM620    TCPREQU  ZVM630    TCPRINT  ZVM630               
DTCRUN1048I TCPSSL   PM77039   TCPUP    ZVM630    TCQDIO   ZVM630               
DTCRUN1048I TCQUEUE  ZVM620    TCROUND  ZVM620    TCSOCKRE PM72267              
DTCRUN1048I TCSYSIO  PQ51738   TCTOATM  ZVM620    TCTOCLAW PQ95126              
DTCRUN1048I TCTOCTC  ZVM620    TCTOHPPI PQ51891   TCTOIUC  ZVM630               
DTCRUN1048I TCTOOSD  ZVM630    TCTOPC3  PQ66267   TCTREEP  ZVM620               
DTCRUN1048I TCUDPRE  ZVM620    TCUDPUP  ZVM630    TCUTIL   PM71046              
DTCRUN1048I TCVMSUB  ZVM620    TNLDSFP  ZVM630    TNSTMAS  ZVM630               
DTCRUN1048I TNTOCP   ZVM620    TNTOTCP  ZVM620    T6PREQU  ZVM630               
DTCRUN1048I T6PSSL   ZVM630    T6SOCKRE ZVM630                                  
DTCTCP001I z/VM TCP/IP Level 630                                                
                              ***** 09/23/13 *****                              
12:23:09 DTCIPI008I    Initializing... TCPIP MODULE E2 dated 05/22/13 at 14:18  
12:23:09 DTCIPI052I TCP/IP Module Load Address:  00C00000                       
12:23:09 DTCIPI009I Devices will use diagnose 98 real channel program support   
12:23:09 DTCIPI012I TCP/IP running under z/VM system                            
12:23:09 DTCIPI005I Trying to open TCPIP TCPIP *                                
12:23:09 DTCIPI005I Trying to open ZVMWSXX TCPIP *                              
12:23:09 DTCIPI006I Using profile file ZVMWSXX TCPIP D1 dated 09/19/13 at 23:50 
12:23:09 DTCFPM004I FPSM is available with 0 pages allocated                    
12:23:09 DTCFPM005I FPSM may allocate an additional 24570 pages out of 27301    
                              ***** 09/23/13 *****                              
12:23:09 DTCPRS354I AutoRestart is the default for device DEV@0800              
12:23:09 DTCOSD080I OSD initializing:                                           
12:23:09 DTCPRI385I  Device DEV@0800:                                           
12:23:09 DTCPRI386I     Type: OSD, Status: Not started                          
                                                                                
                                                            MORE...   ZVMWSXX   
```

```
10:32:22 DTCQDI001I QDIO device DEV@0800 device number 0800:                    
10:32:22 DTCQDI007I   Enabled for QDIO data transfers                           
10:32:22 DTCOSD238I ToOsd: IPv4 multicast support enabled for DEV@0800          
10:32:22 DTCOSD319I ProcessSetArpCache: Supported for device DEV@0800           
10:32:22 DTCOSD341I Obtained MAC address 02003000001E for device DEV@0800       
10:32:22 DTCOSD246I OSD device DEV@0800: Assigned IPv4 address 172.24.200.95    
10:32:22 DTCOSD234I ToOsd: TCPIP host is not set as a router for port UNASSIGNED
 for IPv4                                                                       
                                                   
                                                                                
                                                            RUNNING   ZVMWS01   
```

- Agora você deve DESLOGAR do TCPIP ou DESCONECTAR?

===\>

O primeiro encerrará a pilha enquanto o último permitirá que ela continue funcionando. Uma máquina de serviço VM é análoga a um daemon Linux. Use o comando #CP DISC. O #CP **executa no nível CP**.

### Verifique a configuração do TCPIP

Se tudo correu bem, seu z/VM agora deve estar na rede. Vamos tentar algumas coisas para verificar se a configuração está correta.

- Inicie uma sessão TN3270 e conecte-se diretamente ao seu sistema z/VM. Agora você tem a capacidade de fazer login em mais de uma máquina virtual por vez. & Faça login como TCPMAINT. & Use o comando NETSTAT para exibir informações sobre sua rede. Exiba as informações do dispositivo. Você deve ver informações sobre o dispositivo QDIO1.

```
===> netstat dev
```

Exiba as informações do gateway. Você deve ver informações sobre o gateway.

```
===> netstat gate
```

- Faça ping no gateway (primeiro salto).

```
===> ping GATEWAYIP
```

- Traçar rotas através de uma rede às vezes é necessário para ajudar a depurar
problemas de conectividade. Tente o comando TRACERTE e trace a rota para
outro IP:


```
===> tracerte ANOTHERIP
```

- Dê Log off da TCPMAINT.

```
===> logoff 
```


### Criar um arquivo ETC HOSTS

Os arquivos host locais contêm informações necessárias para a resolução de nomes de host locais. Qualquer nome de domínio ou endereço IP especificado neste arquivo é acessível para uso em sua rede. Arquivos host locais são usados para criar a tabela do site, que permite a resolução de nomes e a resolução inversa de nomes sem usar um servidor de nomes de domínio.

Um arquivo de amostra, ETCHOSTS SAMPLE, é fornecido com o sistema z/VM na entrega do disco TCPMAINT 592. Você pode usar este arquivo como um guia para criar um arquivo ETC HOSTS personalizado, que deve residir neste mesmo minidisco (TCPMAINT 592). Como cada site é único, as declarações dentro do arquivo ETC HOSTS devem ser personalizadas para sua instalação.

- Faça login como TCPMAINT.

- Identifique o file mode do disco 592


```
===> q disk
```

- Copie o arquivo de exemplo do disco 592 para ETC HOSTS

```
===> copy ETCHOSTS SAMPLE f ETC HOSTS =
```

- Edite o arquivo e adicione o IP de outro estudante


```
00046 # -----------------------------------------------------------------------
00047 # Local Host Loopback Address (IPv4 format)
00048 # -----------------------------------------------------------------------
00049
00050 127.0.0.1     localhost loopback
00051 (*|\textcolor{red}{172.24.200.222  zvmwsxx}|*)
00052
00053 # -----------------------------------------------------------------------
00054 # Special IPv6 Addresses
00055 # -----------------------------------------------------------------------
```

- Teste fazendo ping no z/VM usando seu nome:


```
===> ping zvmwsxxx
```

```
ping zvmwsxx
Ping Level 630: Pinging host ZVMWSXX (172.24.200.222).
                Enter #CP EXT to interrupt.
PING: Ping #1 response took 0.003 seconds. Successes so far 1.
Ready; T=0.08/0.17 19:41:26
```


### Configurar o PROFILE EXEC do AUTOLOG1

Quando o z/VM é inicializado, normalmente a máquina virtual AUTOLOG1 é logada
(a menos que o parâmetro NOAUTOLOG seja especificado na IPL). Seu PROFILE EXEC
é executado quando o CMS é inicializado. Usando este arquivo, execute as seguintes tarefas:


- Limite o cache do minidisk na memória principal e desative-o na memória expandida
com o comando SET MDC. & Inicie as máquinas virtuais que devem ser iniciadas usando o comando XAUTOLOG



- Faça login no AUTOLOG1.

- Antes de pressionar Enter no prompt VM READ, digite **acc (noprof** para
que o PROFILE EXEC não seja executado:


```
LOGON AUTOLOG1
z/VM Version 6 Release 3.0, Service Level 1201 (64-bit),
built on IBM Virtualization Technology
There is no logmsg data
FILES: NO RDR, NO PRT, NO PUN
LOGON AT 13:32:04 EDT TUESDAY 09/03/13
z/VM V6.3.0
2013-06-24 16:58
==> acc (noprof
```

- Faça uma cópia do PERFIL EXEC original

```
==> copy profile exec a = execorig =
```

Edite o PERFIL EXEC e adicione as seguintes três linhas abaixo do
comentário de processamento do cliente:


```
==> x profile exec
====> /customer
```

```
...
/* Customer processing can be added here
*/
/*********************************************************************/
"CP XAUTOLOG TCPIP"   /* Autolog TCPIP */
"CP SET MDC STOR 0M 256M" /* Limit minidisk cache in CSTOR */
...
====> file
```

- A VM TCPIP deve iniciar automaticamente durante a IPL.


Este é o nosso ambiente atual após as mudanças:


![Environment with TCPIP](/imgs/workshop-tcpip.png){#fig:workshoptcpip}
