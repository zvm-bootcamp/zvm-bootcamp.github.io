---
order: 99
icon: ":rocket:"
---

# Ambiente do Workshop

## Hardware

A configuração de hardware depende de onde este workshop está sendo realizado. 
O instrutor abordará isso usando a apresentação.

## Sua Máquina Virtual

![z/VM de segundo nível no início do
workshop](/imgs/workshop1.png){#fig:zvm2nd}

A figura [1.1](#fig:zvm2nd){reference-type="ref"
reference="fig:zvm2nd"} representa os recursos do seu z/VM de 2º nível. 
No final deste workshop, esses recursos serão atribuídos compondo 
seu ambiente z/VM. **Alerta de Spoiler** na figura 
[1.2](#fig:zvm2ndcomplete){reference-type="ref"
reference="fig:zvm2ndcomplete"}.

![z/VM de segundo nível no final do
workshop](/imgs/workshop-linux.png){#fig:zvm2ndcomplete}

## Acesse seu z/VM

É hora de iniciar seu ambiente z/VM. Executaremos o IPL 
(Initial Program Load) do seu sistema. Em um ambiente de primeiro nível, 
isso é feito via HMC (Hardware Management Console), mas estamos em um 
ambiente restrito (2º nível) e nosso IPL é feito via o z/VM de primeiro nível.

Obtenha um número de equipe do instrutor:

        Equipe: _________________________________________________
        
        
        Endereço IP do z/VM: ____________________________________

Conecte-se ao sistema z/VM de primeiro nível, conhecido como ZVMDEMO.

Inicie o cliente TN3270:


![Clique em "Parâmetros de Link..."](/imgs/3270-1.png){#fig:32701}

![Preencha o IP primário e marque
"Auto-reconectar"](/imgs/3270-2.png){#fig:32702}

Depois de configurar o endereço IP, você deve ver a tela de login.


```
    z/VM ONLINE                                                                    
                                                                                
                                                                                
                                                                                
                                 / VV          VVV MM       MM                  
                                /  VV        VVV  MMM     MMM                   
                    ZZZZZZ     /   VV      VVV   MMMM   MMMM                    
                       ZZ     /    VV    VVV    MM MM MM MM                     
                      ZZ     /     VV  VVV     MM  MMM  MM                      
                     ZZ     /      VVVVV      MM   M   MM                       
                    ZZ     /       VVV       MM       MM                        
                   ZZZZZZ /        V        MM       MM                         
                                                                                
                     built on IBM Virtualization Technology                     
                                                                                
                                                                      
                                                                                
 Fill in your USERID and PASSWORD and press ENTER                               
 (Your password will not appear when you type it)                               
 USERID   ===>                                                                  
 PASSWORD ===>                                                                  
                                                                                
 COMMAND  ===>                                                                  
                                                            RUNNING   ZVMDEMO
```

Digite seu userid e a senha (consulte seu instrutor) e pressione
enter para fazer login no sistema (a senha será mostrada em branco).

```
USERID ===> zvmws<n> onde <n> é o número da equipe atribuído a você
PASSWORD ===> zcloud
```

```
z/VM ONLINE                                                                    
                                                                                
                                                                                
                                                                                
                                 / VV          VVV MM       MM                  
                                /  VV        VVV  MMM     MMM                   
                    ZZZZZZ     /   VV      VVV   MMMM   MMMM                    
                       ZZ     /    VV    VVV    MM MM MM MM                     
                      ZZ     /     VV  VVV     MM  MMM  MM                      
                     ZZ     /      VVVVV      MM   M   MM                       
                    ZZ     /       VVV       MM       MM                        
                   ZZZZZZ /        V        MM       MM                         
                                                                                
                     built on IBM Virtualization Technology                     
                                                                                
                                  LPAR  zVMTEC                                  
                                                                                
 Fill in your USERID and PASSWORD and press ENTER                               
 (Your password will not appear when you type it)                               
 USERID   ===>  zvmwsxx                                                         
 PASSWORD ===>                                                                  
                                                                                
 COMMAND  ===>                                                                  
                                                            RUNNING   ZVMDEMO
```

Se sua tela mostrar **"HOLDING"** no canto inferior direito, pressione a tecla **Pause**
para limpar a tela. Você deve ver em seguida a tela do Stand Alone Program
Loader.

```
STAND ALONE PROGRAM LOADER: z/VM VERSION 6 RELEASE 3.0                         
                                                                                
 DEVICE NUMBER:   0200       MINIDISK OFFSET:   39         EXTENT:  1           
                                                                                
 MODULE NAME:     CPLOAD     LOAD ORIGIN:       1000                            
                                                                                
 --------------------------------IPL PARAMETERS-------------------------------- 
fn=SYSTEM ft=CONFIG pdnum=1 pdvol=0201                                          
                                                                                
                                                                                
 -----------------------------------COMMENTS----------------------------------- 
                                                                                
                                                                                
                                                                                
                                                                                
 ------------------------------------------------------------------------------ 
                                                                                
                                                                                
                                                                                
 9= FILELIST  10= LOAD  11= TOGGLE EXTENT/OFFSET   
```

Você deve ver em seguida a tela do Stand Alone Program Loader.

**"Tab"** para avançar o cursor até a seção "IPL PARAMETERS". Digite
**"cons=0009"**.

Verifique o exemplo abaixo e **pressione a tecla F10** para carregar o sistema z/VM.


```
STAND ALONE PROGRAM LOADER: z/VM VERSION 6 RELEASE 3.0                         
                                                                                
 DEVICE NUMBER:   0200       MINIDISK OFFSET:   39         EXTENT:  1           
                                                                                
 MODULE NAME:     CPLOAD     LOAD ORIGIN:       1000                            
                                                                                
 --------------------------------IPL PARAMETERS-------------------------------- 
fn=SYSTEM ft=CONFIG pdnum=1 pdvol=0201 cons=0009                                
                                                                                
                                                                                
 -----------------------------------COMMENTS----------------------------------- 
                                                                                
                                                                                
                                                                                
                                                                                
 ------------------------------------------------------------------------------ 
                                                                                
                                                                                
                                                                                
 9= FILELIST  10= LOAD  11= TOGGLE EXTENT/OFFSET   
```

Insira o restante dos parâmetros IPL da seguinte forma.

!!!
**Pressione a tecla "Pause" sempre que o canto inferior direito do seu
console mostrar "MORE" ou "HOLDING".**
!!!

Quando solicitado a fornecer parâmetros de "start":

**Digite: "cold drain noautolog"**

```js !#25
22:28:58 z/VM  V6 R3.0  SERVICE LEVEL 1301 (64-BIT)                            
 22:28:59 SYSTEM NUCLEUS CREATED ON 2013-06-28 AT 14:58:28, LOADED FROM M01RES  
22:28:59                                                                        
22:28:59 ****************************************************************       
22:28:59 * LICENSED MATERIALS - PROPERTY OF IBM*                        *       
22:28:59 *                                                              *       
22:28:59 * 5741-A07 (C) COPYRIGHT IBM CORP. 1983, 2013. ALL RIGHTS      *       
22:28:59 * RESERVED. US GOVERNMENT USERS RESTRICTED RIGHTS - USE,       *       
22:28:59 * DUPLICATION OR DISCLOSURE RESTRICTED BY GSA ADP SCHEDULE     *       
22:28:59 * CONTRACT WITH IBM CORP.                                      *       
22:28:59 *                                                              *       
22:28:59 * * TRADEMARK OF INTERNATIONAL BUSINESS MACHINES.              *       
22:28:59 ****************************************************************       
22:28:59                                                                        
 22:28:59 HCPZCO6718I Using parm disk 1 on volume VMCOM1 (device 0201).         
 22:28:59 HCPZCO6718I Parm disk resides on cylinders 1 through 120.             
22:28:59 Start ((Warm|Force|COLD|CLEAN) (DRain) (DIsable)  (NODIRect)           
22:28:59       (NOAUTOlog)) or (SHUTDOWN)                                       
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
cold drain noautolog     
```

Quando solicitado a "Change TOD clock":

**Pressione enter para aceitar o padrão "no"**

Se necessário, pressione PAUSE BREAK (O status "MORE..." no canto inferior direito
do console).

Quando solicitado a continuar com "COLD start":

**Digite "go"**


```
22:33:40 HCPWRS2513I                                      _______              
 22:33:40 HCPWRS2513I Total files to be deleted                  1              
 22:33:42 HCPWRS2511A                                                           
 22:33:42 HCPWRS2511A Spool files will be deleted because of COLD start.        
 22:33:42 HCPWRS2511A No files have been deleted yet.                           
 22:33:42 HCPWRS2511A To continue COLD start and delete files, enter GO.        
 22:33:42 HCPWRS2511A To stop COLD start without deleting files, enter STOP.    
22:33:48 GO                                                                     
 22:33:49 HCPWRS2512I Spooling initialization is complete.                      
22:33:49 DASD 0205 dump unit CP IPL pages 19863                                 
22:33:49 HCPMLM3016I Management by the Unified Resource Manager is not available
 for this system.                                                               
22:33:49 HCPAAU2700I System gateway ZVMWSXX identified.                         
22:33:49 z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),               
22:33:49 built on IBM Virtualization Technology                                 
22:33:49 There is no logmsg data                                                
22:33:49 FILES:   NO RDR,   NO PRT,   NO PUN                                    
22:33:49 LOGON AT 22:33:49 EDT SATURDAY 08/31/13                                
22:33:49 GRAF  0009 LOGON  AS  OPERATOR USERS = 1                               
22:33:49 HCPIOP952I 2048M system storage                                        
22:33:49 FILES: 0000002 RDR, 0000001 PRT,      NO PUN                           
22:33:49 HCPCRC8082I Accounting records are accumulating for userid DISKACNT.   
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

Você inicializou com sucesso o z/VM quando receber as seguintes
mensagens:

```
22:33:40 HCPWRS2513I                                      _______              
 22:33:40 HCPWRS2513I Total files to be deleted                  1              
 22:33:42 HCPWRS2511A                                                           
 22:33:42 HCPWRS2511A Spool files will be deleted because of COLD start.        
 22:33:42 HCPWRS2511A No files have been deleted yet.                           
 22:33:42 HCPWRS2511A To continue COLD start and delete files, enter GO.        
 22:33:42 HCPWRS2511A To stop COLD start without deleting files, enter STOP.    
22:33:48 GO                                                                     
 22:33:49 HCPWRS2512I Spooling initialization is complete.                      
22:33:49 DASD 0205 dump unit CP IPL pages 19863                                 
22:33:49 HCPMLM3016I Management by the Unified Resource Manager is not available
 for this system.                                                               
22:33:49 HCPAAU2700I System gateway ZVMWSXX identified.                         
22:33:49 z/VM Version 6 Release 3.0, Service Level 1301 (64-bit),               
22:33:49 built on IBM Virtualization Technology                                 
22:33:49 There is no logmsg data                                                
22:33:49 FILES:   NO RDR,   NO PRT,   NO PUN                                    
22:33:49 LOGON AT 22:33:49 EDT SATURDAY 08/31/13                                
22:33:49 GRAF  0009 LOGON  AS  OPERATOR USERS = 1                               
22:33:49 HCPIOP952I 2048M system storage                                        
22:33:49 FILES: 0000002 RDR, 0000001 PRT,      NO PUN                           
22:33:49 HCPCRC8082I Accounting records are accumulating for userid DISKACNT.   
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

!!!
Pressione a tecla Pause para limpar a tela. Você deverá ver a
tela abaixo. Observe que ainda temos um status de More...
!!!


```
04: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
03: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
02: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
01: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
01: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
02: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
03: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
04: HCPGSP2627I The virtual machine is placed in CP mode due to a SIGP initial C
PU reset from CPU 00.                                                           
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            MORE...   ZVMDEMO  
```

!!!
Pressione a tecla Pause para limpar a tela novamente
!!!

Agora, digite o seguinte comando para descobrir qual usuário você é.


```
===> query userid
```


Digite o seguinte comando para descobrir como foi realizada a última IPL.


```
===> query cpload
```

```
12:44:25 Q USERID                                                               
12:44:25 OPERATOR AT ZVMWSXX                                                    
12:44:46                                                                        
12:44:46                                                                        
12:44:46                                                                        
12:44:49 Q CPLOAD                                                               
12:44:49 Module CPLOAD was loaded from minidisk on volume M01RES at cylinder 39.
12:44:49 Parm disk number 1 is on volume VMCOM1, cylinders 1 through 120.       
12:44:49 Last start was a system IPL.                                           
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

** TODO: Review **

Estas mensagens fornecem algumas informações básicas sobre o seu sistema:

1. O identificador do sistema é zVM. Ele também é exibido no canto inferior direito do console.

2. A versão do z/VM é 6 Release 3.0, nível de serviço (64 bits).

3. O usuário OPERATOR está atualmente conectado (Como? Comando \<QUERY USERID\>).

4. O sistema tem MB de armazenamento (memória) (ID da mensagem HCPIOP952I ou \<QUERY STORAGE\>).

Agora desconecte-se do 'OPERATOR'.


```
===> DISCONNECT
```

**Pressione a tecla Pause quando o status do console indicar "MORE\..."**

**Pressione a tecla Enter para continuar**

Você deve ver a janela de login para o seu sistema z/VM de segundo nível. Esta
é a única sessão disponível para os usuários se conectarem. Mais tarde, você ativará
o TCP/IP, que permitirá múltiplas conexões ao seu sistema.


```
z/VM ONLINE                                                                    
                                                                                
                                                                                
                                                                                
                                 / VV          VVV MM       MM                  
                                /  VV        VVV  MMM     MMM                   
                    ZZZZZZ     /   VV      VVV   MMMM   MMMM                    
                       ZZ     /    VV    VVV    MM MM MM MM                     
                      ZZ     /     VV  VVV     MM  MMM  MM                      
                     ZZ     /      VVVVV      MM   M   MM                       
                    ZZ     /       VVV       MM       MM                        
                   ZZZZZZ /        V        MM       MM                         
                                                                                
                     built on IBM Virtualization Technology                     
                                                                                
                                                                                
                                                                                
 Fill in your USERID and PASSWORD and press ENTER                               
 (Your password will not appear when you type it)                               
 USERID   ===>                                                                  
 PASSWORD ===>                                                                  
                                                                                
 COMMAND  ===>                                                                  
                                                            RUNNING   ZVMWSXX   
```

!!! warning
Attention It is important to ***disconnect***, **not** ***log off***
your system. Logging off from the system is equivalent to turning off
the computer, thus crashing your z/VM system.
!!!

