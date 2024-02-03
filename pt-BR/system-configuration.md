---
order: 85
icon: ":gear:"
---
# System Configuration {#ch:systemconfig}

Como programador de sistema, você deve se familiarizar com o arquivo SYSTEM CONFIG.

O arquivo SYSTEM CONFIG contém as definições primárias do sistema usadas quando o CP é inicializado (IPLed). Todas as informações necessárias para configurar o CP estaticamente vêm deste arquivo. Em um cluster SSI, todos os membros usam o mesmo arquivo SYSTEM CONFIG; no entanto, você pode especificar que determinadas declarações de configuração se apliquem apenas a membros específicos, qualificando as declarações com um identificador de sistema. Este tópico tem exemplos desse método de especificação.

O arquivo SYSTEM CONFIG reside em um minidisco especial formatado em CMS (CF0) pertencente ao ID de usuário PMAINT. Minidiscos que contêm esses objetos são chamados de discos de parâmetro porque, quando alocados, esses discos recebem um tipo de categoria de registro especial chamado "PARM". Pode haver mais de um disco de parâmetro alocado em um sistema z/VM para backup e recuperação.

Informações relacionadas: "Using Configuration Files" in z/VM: CP Planning and Administration, SC24-6178-09.

## Exercícios

### Discos de parâmetro do sistema

O arquivo SYSTEM CONFIG está localizado em um disco de parâmetro do sistema. Antes de começar a atualizar o arquivo SYSTEM CONFIG, você deve acessar o disco de parâmetro (Neste caso, o CF0 do usuário PMAINT).

Os minidiscos CF1 e CF3 do usuário MAINT são tradicionalmente usados como discos de parâmetro do sistema. Use o comando QUERY CPDISK para verificar quais discos de parâmetro do sistema estão em uso no seu sistema:

- Faça login como MAINT e digite o seguinte comando e pressione Enter


```
===> q cpdisk
```

```
q userid                                                             
MAINT    AT ZVMWSXX                                                  
q cpdisk                                                             
Label  Userid   Vdev Mode Stat Vol-ID Rdev Type   StartLoc     EndLoc
MNTCF1 MAINT    0CF1  A   R/O  AB1RES 6205 CKD          39        158
MNTCF3 MAINT    0CF3  C   R/O  AB1RES 6205 CKD         160        279
```

### Passos para acessar o disco de parâmetro e criar um backup para o arquivo SYSTEM CONFIG

Realize os seguintes passos para acessar o disco de parâmetro CF0:


- Faça login como MAINT e acesse o disco de parâmetro (CF0 do usuário PMAINT). Digite esses comandos e pressione Enter após cada comando:

```
===> link pmaint cf0 cf0 mr

===> access cf0 x
```

```
q userid                                                                        
MAINT    AT ZVMWSXX                                                             
Ready; T=0.01/0.01 08:30:09                                                     

link pmaint cf0 cf0 mr                                                          
Ready; T=0.01/0.01 08:30:23                                                     

access cf0 x                                                                    
Ready; T=0.01/0.01 08:30:37                                                     

q disk                                                                          
LABEL  VDEV M  STAT   CYL TYPE BLKSZ   FILES  BLKS USED-(%) BLKS LEFT  BLK TOTAL
MNT191 191  A   R/W   175 3390 4096        4         11-01      31489      31500
MNT5E5 5E5  B   R/O    18 3390 4096      134       1537-47       1703       3240
MNT2CC 2CC  C   R/O    10 3390 4096        3        104-06       1696       1800
MNT51D 51D  D   R/O    26 3390 4096      293       1796-38       2884       4680
PMT551 551  E   R/O    40 3390 4096        8        119-02       7081       7200
MNT190 190  S   R/O   207 3390 4096      696      18085-49      19175      37260
MNT19E 19E  Y/S R/O   500 3390 4096     1123      30403-34      59597      90000
(*|\textcolor{red}{PMTCF0 CF0}|*)  X   R/W   120 3390 4096        2         15-01      21585      21600
Ready; T=0.01/0.01 08:30:39                                                     
                                                                                
                                                                                
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX   
```

- Como prática recomendada, faça um backup do arquivo SYSTEM CONFIG:

```
===> copy system config x system configbk x 
```

```
copy system config x system configbk x  
Ready; T=0.01/0.01 08:32:53             

listfile * * x                          
LOGO     CONFIG   X1                    
SYSTEM   CONFIG   X1                    
SYSTEM   CONFIGBK X1                    
Ready; T=0.01/0.01 08:32:59      
```
:::

Você sabe que terminou quando tiver acesso ao disco CF0 e tiver criado um backup do arquivo SYSTEM CONFIG no disco de parâmetro.

### Editar o arquivo SYSTEM CONFIG

Após vincular em "Multiple Write Mode", você pode editar o arquivo
SYSTEM CONFIG com o editor XEDIT.

Antes de começar a atualizar o arquivo SYSTEM CONFIG, vamos criar um pequeno
REXX que automatizará o acesso ao disco SYSTEM CONFIG. A partir do usuário MAINT,
use o XEDIT para criar um novo arquivo chamado GETCF0 EXEC A:

```
===> xedit getcf0 exec a
```

Adicione o seguinte conteúdo:

```
/* REXX will link PMAINT CF0 */

'LINK PMAINT CF0 CF0 MR'
'ACCESS CF0 X'
```

Execute um script "GETCF0" que acessará o disco de parâmetro como o disco "X".

### Etapas para atualizar a lista de volumes de propriedade do CP (CP-owned)

A lista de volumes CP-owned é o local onde você especifica os rótulos dos volumes de paginação, spooling e disco temporário que o CP deve anexar automaticamente ao sistema durante o IPL. Esses volumes contêm os dados do sistema para o seu sistema z/VM. Todos os outros volumes no sistema são considerados volumes de usuário.

**Antes de começar:** Você precisa formatar e alocar seus volumes de paginação, spooling, disco temporário ou volumes permanentes. Você precisa ter acesso ao disco de parâmetro.

Realize os seguintes passos para atualizar a lista de volumes de propriedade do CP:

- Faça login como MAINT
- Edite o arquivo SYSTEM CONFIG. Digite um dos dois comandos a seguir para obter acesso e editar o arquivo, e pressione a tecla Enter:

```
===> xedit system config x
```

Ou

```
===> filel * * x (go to the CMD column of the file, type x and press enter)
```

Você verá um arquivo como o seguinte:

```
SYSTEM   CONFIG   X1  F 80  Trunc=80 Size=376 Line=0 Col=1 Alt=0               
                                                                                
00000 * * * Top of File * * *                                                   
00001 /**********************************************************************/  
00002 /*             SYSTEM CONFIG FILE                                     */  
00003 /**********************************************************************/  
00004 /*                                                                    */  
00005 /*  Refer to CP Planning and Administration for SYSTEM CONFIG rules   */  
00006 /*                                                                    */  
00007 /*  Warning - Always run CPSYNTAX after updating the SYSTEM CONFIG    */  
00008 /*                                                                    */  
00009 /**********************************************************************/  
00010                                                                           
00011                                                                           
00012 /**********************************************************************/  
00013 /*                   System_Identifier Information                    */  
00014 /**********************************************************************/  
00015                                                                           
00016   /* System_Identifier LPAR ZVMWSXX  ZVMWSXX */                           
00017   /* System_Identifier LPAR @@LU-2 @@MEMSLOT2 */
00018   /* System_Identifier LPAR @@LU-3 @@MEMSLOT3 */                          
00019   /* System_Identifier LPAR @@LU-4 @@MEMSLOT4 */                          
00020                                                                           
00021       System_Identifier * * ZVMWSXX                                       
00022                                                                           
00023                                                                           
00024 /**********************************************************************/  
00025 /*         SSI Statement required for VMSSI feature                   */  
00026 /**********************************************************************/  
00027                                                                           
00028   SSI CLUSTER1  PDR_VOLUME VMCOM1,                                        
00029         SLOT 1 ZVMWSXX                                                    
00030 /*      SLOT 2 @@MEMSLOT2,  */                                            
00031 /*      SLOT 3 @@MEMSLOT3,  */                                            
00032 /*      SLOT 4 @@MEMSLOT4   */                                            
00033                                                                           
00034                                                                           
00035 /**********************************************************************/  
00036 /*                Checkpoint and Warmstart Information                */  
00037 /**********************************************************************/  
00038                                                                           
00039   ZVMWSXX:     System_Residence,                                          
====>                                                                           
```

- Localize a string "VOLUMES DE DESPEJO E SPOOL". Na linha de comando do XEDIT,
digite o seguinte comando e pressione Enter:

```
===> /dump/ & /spool/ & /volume/
```

- Adicione volumes de Spool adicionais nos slots em **ordem crescente** (slots
11, 12 e assim por diante).

Duplicate the following line:

::: bclogo
Duplicating a line on XEDIT To duplicate a line in XEDIT place a double
quote (\") in the line number and press enter, example:

00080
/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/

00081

[\"]{style="color: red"}0082 CP_Owned Slot 10 M01S01

00083

00084
/\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/
:::

```
00082     CP_Owned   Slot  10  M01S01
```

Adicione o volume de Spool previamente formatado (M01S02) **abaixo da linha M01S01**.

Agora você deve ver algo como o seguinte:

```
00072 /*                                           DUMP & SPOOL VOLUMES     */
00073 /* Dump and spool volumes begin with slot 10 and are assigned in      */
00074 /* ascending order, without regard to the system that owns them.      */
00075 /* In an SSI cluster all member systems MUST have access to ALL spool */
00076 /* volumes and they MUST be assigned the same slot number on each     */
00077 /* system. When moving to an SSI cluster volume ownership, which      */
00078 /* determines which member owns and can allocate the spool space,     */
00079 /* will be established by CPFMTXA.                                    */
00080 /**********************************************************************/
00081
00082     CP_Owned   Slot  10  M01S01
00083     CP_Owned   (*|\textcolor{red}{Slot  11}|*)  (*|\textcolor{red}{M01S02}|*)
```

- Para adicionar o volume PAGE, localize a string "VOLUMES DE PÁGINA E TDISK" (Dica: você pode encontrar esta seção logo abaixo da seção de SPOOL). Na linha de comando do XEDIT, digite o seguinte comando e pressione Enter:

```
===> /page/ & /tdisk/ & /volume/
```

- Adicione volumes de Página adicionais nos slots em **ordem decrescente** (slots 254, 253 e assim por diante).

Duplicar a linha a seguir:


```
00095     CP_Owned   Slot 255  M01P01
```

Adicione o volume de Página previamente formatado (M01P02) **acima da linha M01P01**.

Agora você deve ver algo como o seguinte:

```
SYSTEM   CONFIG   Z1  F 80  Trunc=80 Size=280 Line=85 Col=1 Alt=2              
                                                                                
00085 /*                                           PAGE & TDISK VOLUMES     */  
00086 /* In a SSI cluster Paging and T-disk volumes are not shared.         */  
00087 /* To avoid interference with spool volumes and to automatically      */  
00088 /* have all unused slots defined as "Reserved", begin with slot 255   */  
00089 /* and assign them in descending order.  It is NOT required that each */  
00090 /* system use the same slot numbers for paging and T-disk volumes.    */  
00091 /* When moving to an SSI cluster volume ownership will be established */  
00092 /* by CPFMTXA OWNER.                                                  */  
00093 /**********************************************************************/  
00094                                                                           
00095     CP_Owned   Slot 254  M01P02                                      
00096     CP_Owned   Slot 255  M01P01                                           
00097                                                                           
00098 /**********************************************************************/  
00099 /*                          User_Volume_List                          */  
00100 /**********************************************************************/  
00101 /* These volumes contain the minidisks for your guests, as well as    */  
00102 /* the product disks for z/VM. Volumes that are not intended to hold  */  
00103 /* "local" minidisks, i.e., minidisks that would be unique to a       */  
00104 /* single system, should be kept on separate volumes.                 */  
00105                                                                           
00106 /**********************************************************************/  
00107 /* Shared User Volumes                                                */  
00108 /**********************************************************************/  
00109                User_Volume_List  VMCOM2 630RL1 630RL2                     
00110                                                                           
00111 /**********************************************************************/  
00112 /* User volumes for local minidisks                                   */  
00113 /**********************************************************************/  
====>                         
```

- Salve o arquivo. Na linha de comando do XEDIT, digite este comando e pressione a tecla Enter:

```
===> save
```
:::

### Passos para atualizar a lista de volumes do usuário

Assim como existe uma lista de volumes DASD que o CP deve anexar automaticamente ao sistema durante o IPL para acesso às áreas do sistema CP, existe uma lista de volumes DASD que o CP deve anexar automaticamente ao sistema para definições de minidiscos do usuário.

Como todos os minidiscos são gerenciados pelo CP, todos os volumes que abrigam minidiscos devem ser anexados ao sistema z/VM. O CP deve controlar os volumes para poder reorientar programas de canal iniciados por um sistema operacional convidado. O convidado percebe seus discos como começando no cilindro 0, mas a verdadeira localização do minidisco do convidado começa em um deslocamento do cilindro real 0.

A menos que você qualifique as declarações de volume do usuário com um nome de sistema, os volumes do usuário são anexados a todos os membros do cluster SSI.

Se nenhum volume do usuário estiver anexado ao sistema no momento do IPL, os dispositivos reais que abrigam os minidiscos precisam ser anexados manualmente. Caso contrário, as máquinas virtuais não terão discos. Para evitar a anexação manual, você pode instruir o CP a procurar rótulos de volume DASD e anexar esses dispositivos durante o IPL.

A declaração USER_VOLUME_LIST direciona o CP a anexar volumes DASD do usuário específicos durante o carregamento (IPL) do z/VM. A declaração USER_VOLUME_INCLUDE permite que você crie um identificador de volume geral e inclua todos os volumes que correspondem ao identificador geral.

Exemplo: Se todos os seus volumes do usuário Linux têm um identificador de volume que começa com LNX, você pode adicionar esta declaração:

```
02109          User_Volume_Include LNX*
```

Se um volume é normalmente anexado ao sistema usando uma declaração USER_VOLUME_INCLUDE, o CP não notifica o operador se o volume não estiver montado. Se um volume do usuário for necessário para a operação normal do sistema, especifique-o com uma declaração USER_VOLUME_LIST para que o operador seja notificado durante a inicialização do sistema se o volume não estiver montado.

**Antes de começar:** Você precisa formatar e alocar os volumes do usuário necessários. Você precisa ter acesso ao disco de parâmetros.

- Se ainda não estiver fazendo isso, você deve editar o arquivo de SYSTEM CONFIG. Digite este comando e pressione a tecla Enter:

```
===> xedit system config x
```

Siga estes passos para atualizar a lista de volumes do usuário:

- Encontre a string "User_Volume_List" na seção intitulada "User volumes for local minidisks.". Na linha de comando do XEDIT, digite este comando e
pressione a tecla Enter:

```
===> /user_volume_list
```

- Para volumes de usuário compartilhados, atualize as declarações User_Volume_List na seção "Shared User Volumes".

- Inclua os volumes LNX301, LNX302 e LNX303 nesta lista (usaremos o caractere curinga LNX\*)

- Na área de prefixo para a última declaração USER_VOLUME_LIST, digite "i2" e pressione a tecla Enter. Por exemplo:


```
00099 /*                          User_Volume_List                          */
00100 /**********************************************************************/
00101 /* These volumes contain the minidisks for your guests, as well as    */
00102 /* the product disks for z/VM. Volumes that are not intended to hold  */
00103 /* "local" minidisks, i.e., minidisks that would be unique to a       */
00104 /* single system, should be kept on separate volumes.                 */
00105                                                                         
00106 /**********************************************************************/
00107 /* Shared User Volumes                                                */
00108 /**********************************************************************/
(*|\textcolor{red}{i2}|*)109                User_Volume_List  VMCOM2 630RL1 630RL2                   
00110                                                                         
00111 /**********************************************************************/
00112 /* User volumes for local minidisks                                   */
00113 /**********************************************************************/
```

- Adicione uma declaração User_Volume_Include. Se você seguiu as instruções do último capítulo, provavelmente possui volumes chamados LNX301, LNX302 e LNX303, então faz sentido usar o caractere curinga LNX\* para nossos volumes.
Exemplo:

```
00099 /*                          User_Volume_List                          */
00100 /**********************************************************************/
00101 /* These volumes contain the minidisks for your guests, as well as    */
00102 /* the product disks for z/VM. Volumes that are not intended to hold  */
00103 /* "local" minidisks, i.e., minidisks that would be unique to a       */
00104 /* single system, should be kept on separate volumes.                 */
00105                                                                         
00106 /**********************************************************************/
00107 /* Shared User Volumes                                                */
00108 /**********************************************************************/
00109                User_Volume_List  VMCOM2 630RL1 630RL2                   
00110                (*|\textcolor{red}{User\_Volume\_Include  LNX*}|*)                                                                        
00111                                                                       
00112 /**********************************************************************/
00113 /* User volumes for local minidisks                                   */
00114 /**********************************************************************/
```

- Ao terminar as declarações User_Volume_List, pressione a tecla Enter.
- Salve o arquivo. Na linha de comando do XEDIT, digite este comando e pressione a tecla Enter:

```
===> save
```

Pergunta: Existe outra maneira de adicionar esses discos LNX\* à lista de volumes do usuário?


### Passos para atualizar a declaração de FEATURES

A declaração FEATURES em SYSTEM CONFIG permite que você modifique atributos
associados ao sistema em execução no momento do IPL. Neste procedimento, você
irá modificar alguns dos recursos.


- O recurso **Auto_Warm_IPL** faz com que o CP ignore a solicitação de opções de inicialização, desde que o desligamento anterior do sistema tenha sido bem-sucedido. O recurso permite uma inicialização totalmente automatizada do z/VM.

- O recurso **Clear_TDisk** faz com que o CP apague completamente os discos temporários (ou seja, sobrescreva todo o disco temporário com zeros) após esses discos serem desanexados. O recurso impede que outro usuário que defina um disco temporário com o mesmo tamanho acesse dados gravados pelo usuário anterior.

- O recurso **Retrieve** define o número padrão e máximo de buffers de recuperação permitidos por usuário em seu sistema. Os buffers de recuperação criam um histórico de comandos, dos quais os usuários podem recuperar comandos previamente emitidos. A recuperação de comando geralmente é atribuída a uma tecla de função do programa, como PF12 (F12). A atribuição é feita por meio do comando CP SET, SET PF12 RETRIEVE. Ao pressionar PF12, um comando é recuperado e escrito de volta na área de comando na tela do terminal. Provavelmente, você não precisa alterar essas configurações.

- O recurso **Passwords_on_Cmds** permite que os usuários usem senhas ao usar os comandos CP AUTOLOG, LINK ou LOGON.

- O recurso **Disconnect_timeout** controla se e quando uma máquina virtual é desconectada após ser forçada a desconectar. Você desativará esse recurso, para que qualquer máquina virtual que tenha sido forçada a desconectar não seja desconectada.

- Os recursos **ShutdownTime** e **Signal ShutdownTime** permitem que uma máquina virtual se registre no CP para receber um sinal de desligamento quando o z/VM está sendo desligado. O CP aguarda para desligar-se até que o intervalo de tempo (em segundos) seja excedido ou todas as máquinas virtuais habilitadas para o sinal de desligamento tenham relatado um desligamento bem-sucedido. Algumas distribuições Linux suportam essa função, o que permite que o Linux seja desligado corretamente antes do desligamento do z/VM.

**Observação:** A menos que você qualifique os recursos com um nome de sistema, esses recursos serão os mesmos para todos os membros em seu cluster SSI.


Procedimento: Encontre a linha que contém o texto "Features Statement", é uma seção. Na linha de comando do XEDIT, digite este comando e pressione a tecla Enter:

```
===> /Features Statement
```

Você verá algo como isto:

```
00146 /*                         Features Statement                         */
00147 /**********************************************************************/
00148
00149  Features ,
00150    Disable ,                       /* Disable the following features */
00151      Set_Privclass ,               /* Disallow SET PRIVCLASS command */
00152      Auto_Warm_IPL ,               /* Prompt at IPL always           */
00153      Clear_TDisk   ,               /* Don't clear TDisks at IPL time */
00154    Retrieve ,                      /* Retrieve options               */
00155      Default  20 ,                 /* Default.... default is 20      */
00156      Maximum  255 ,                /* Maximum.... default is 255     */
00157    MaxUsers noLimit ,              /* No limit on number of users    */
00158    Passwords_on_Cmds ,             /* What commands allow passwords? */
00159      Autolog  yes ,                /* ... AUTOLOG does               */
00160      Link     yes ,                /* ... LINK does                  */
00161      Logon    yes ,                /* ... and LOGON does, too        */
00162    Vdisk Userlim 144000 blocks     /* Maximum vdisk allowed per user */
```

e vamos modificar para algo como isto:

```
00148 /*                         Features Statement                         */
00149 /**********************************************************************/
00150
00151  Features ,
00152    Enable,                         /* Enable the following features  */
00153      Set_Privclass ,               /* Allow SET PRIVCLASS command    */
00154      Auto_Warm_IPL ,               /* Do not prompt at IPL           */
00155      Clear_TDisk   ,               /* Clear TDisks at IPL time       */
00156    Retrieve ,                      /* Retrieve options               */
00157      Default  99 ,                 /* Default.... default is 20      */
00158      Maximum  255 ,                /* Maximum.... default is 255     */
00159    MaxUsers noLimit ,              /* No limit on number of users    */
00160    Passwords_on_Cmds ,             /* What commands allow passwords? */
00161      Autolog  yes ,                /* ... AUTOLOG does               */
00162      Link     yes ,                /* ... LINK does                  */
00163      Logon    yes ,                /* ... and LOGON does, too        */
00164    Vdisk ,                         /* Maximum vdisk allowed per user */
00165      Syslim infinite ,
00166      Userlim infinite ,
00167    Disconnect_timeout off
00168    Set ,
00169      ShutdownTime 30 ,
00170      Signal ShutdownTime 500
00171

```

Na próxima seção, configuraremos os dispositivos para iniciar online.

### Configurar o acesso de controle aos dispositivos durante a inicialização

Às vezes, seu sistema z/VM pode ter acesso a dispositivos que você não
deseja que sejam iniciados online durante o IPL. Por exemplo, os dispositivos podem
duplicar rótulos de dispositivos usados pelo seu sistema de produção ou podem estar
sendo usados por outros LPARs ou sistemas. Você pode especificar intervalos de dispositivos
que o z/VM não deve iniciar online durante o IPL.

Encontre a seção intitulada "Status of Devices". Na linha de comando do XEDIT,
digite este comando e pressione a tecla Enter:

```
===> /status of devices
```

Na linha com a palavra "Devices", digite i na área de prefixo e pressione a tecla Enter:

```
SYSTEM   CONFIG   Z1  F 80  Trunc=80 Size=286 Line=190 Col=1 Alt=2           
                                                                              
00190 /*                        Status of Devices                           */
00191 /**********************************************************************/
00192                                                                         
(*| \textcolor{red}{i} |*)0193  Devices ,                                                              
00194    Online_at_IPL   0000-FFFF,                                           
00195    Sensed          0000-FFFF                                            
00196                                                                         
00197 /**********************************************************************/
00198 /*                     Console Definitions                            */
00199 /**********************************************************************/
00200      
```

Digite a seguinte linha e pressione a tecla Enter:

```
===> Offline_at_IPL 0000-FFFF,
```

Digite um intervalo de dispositivos no comando ONLINE_AT_IPL com um intervalo de dispositivos específico para o seu sistema.

```
===> Online_at_IPL 0009,   /* Console device */
```

Adicione uma nova declaração ONLINE_AT_IPL para cada intervalo de dispositivos. Use o comando de prefixo "i" para adicionar linhas.

```
===> Online_at_IPL 0200-0700,  /* PERM,PAGE,SPOOL */
===> Online_at_IPL 0800-0808,  /* OSAs */
```

O resultado:

```
SYSTEM   CONFIG   Z1  F 80  Trunc=80 Size=289 Line=187 Col=1 Alt=0           
                                                                              
00187 /**********************************************************************/
00188 /*                        Status of Devices                           */
00189 /**********************************************************************/
00190                                                                         
00191  Devices ,                                                              
00192    Offline_at_IPL   0000-FFFF,                                          
00193    Online_at_IPL    0009,       /* Console */                
00196    Online_at_IPL    0200-0700,  /* PERM,PAGE,SPOOL */
00198    Online_at_IPL    0800-0808,  /* OSAs */
00200    Sensed           0000-FFFF                                           
00201                                                                                            
```

### Definir endereços para consoles

Durante o primeiro IPL do seu sistema z/VM, você precisou especificar um parâmetro de carga para poder se comunicar com o Stand-Alone Program Loader (SAPL). O motivo é que o novo sistema z/VM não sabia quais endereços de dispositivo usar para exibir mensagens e prompts. O sistema de instalação inclui endereços de dispositivo padrão para uso como console do operador do sistema e console de mensagens de emergência, mas esses endereços raramente correspondem à configuração de hardware de produção. Portanto, para não precisar usar o SAPL toda vez que você IPL o z/VM, você precisa fornecer o endereço do seu console IPL e do seu console de mensagens de emergência na declaração Operator_Consoles. Durante o IPL, o CP tenta cada dispositivo na declaração Operator_Consoles (da esquerda para a direita) até encontrar um dispositivo ativo. Se nenhum dispositivo da lista estiver ativo, o CP carrega um estado de espera desabilitado e termina. O console de mensagens de emergência é usado como um console adicional durante falhas. Defina o console de emergência com a declaração Emergency_Message_Console.

Encontre a seção intitulada "Console Definitions".

```
===> /console 
```

Defina **0009** como o primeiro dispositivo nas declarações **Operator_Consoles** e **Emergency_Message_Consoles**. Isso dirá ao z/VM para abrir a comunicação com o operador usando o dispositivo **0009**, que é o endereço do console virtual. Isso eliminará a necessidade de passar parâmetros de carga no próximo boot.

```
00200 /**********************************************************************/
00201 /*                     Console Definitions                            */
00202 /**********************************************************************/
00203                                                                         
00204  Operator_Consoles     (*|\textcolor{red}{0009}|*) 0020 0021 0022 0023 0E20 0E21 1020 ,        
00205                        System_3270 System_Console                       
00206  Emergency_Message_Consoles   (*|\textcolor{red}{0009}|*) 0020 0021 0022 0023 0E20 0E21 1020 , 
00207                               System_Console                            
00208                                                                         
```

Salve todas as alterações que você fez até agora e saia do arquivo de SYSTEM CONFIG.

```
===> file
```

### Etapas para verificar a sintaxe do arquivo de CONFIGURAÇÃO DO SISTEMA

Como o arquivo de CONFIGURAÇÃO DO SISTEMA contém dados muito importantes, é necessário ter muito cuidado para garantir que seu conteúdo esteja correto. O sistema pode não iniciar corretamente se esse arquivo contiver erros. Corrigir os erros pode ser complicado. O z/VM fornece uma ferramenta, CPSYNTAX, no minidisco 193 para verificar a sintaxe do arquivo de CONFIGURAÇÃO DO SISTEMA.

**Procedimento**


- Faça login como MAINT & Para acessar o comando CPSYNTAX, acesse o
disco 193 como F. Na linha de comando, digite o seguinte comando e
pressione a tecla Enter:

```
===> access 193 f
```

- Execute o comando CPSYNTAX. Na linha de comando, digite este comando e
pressione a tecla Enter:

```
===> cpsyntax system config x
```

- Verifique se o código de retorno é zero. Se não for zero,
modifique o arquivo de SYSTEM CONFIG e execute novamente a utilidade CPSYNTAX. Exemplo de um código de retorno diferente de zero:

```
acc 193 f                                                                       
Ready; T=0.01/0.01 17:14:17                                                     

listfile cpsyntax * *                                                           
CPSYNTAX EXEC     F2                                                            
Ready; T=0.01/0.01 17:14:28                                                     

cpsyntax system config x                                                        
HCPSYK6700E FILE SYSTEM CONFIG X, RECORD 164:                                   
HCPSYK6701E INVALID SYSTEM CONFIGURATION FILE STATEMENT - DISCONNECT_TIMEOUT    
                                                                                
Ready(00008); T=0.37/0.38 17:14:46                                              
                                                                                
                                                                                
                                                                                
                                                            RUNNING   ZVMWSXX  
```

- Vamos criar um EXEC chamado FREECF0 no seu "disco A" de MAINT, ele irá liberar o disco cf0 para nós em futuras atualizações:

```
===> x freecf0 exec
```

```
00000 * * * Top of File * * *
00001 /* REXX */             
00002 'RELEASE X'            
00003 'CP DETACH CF0'        
00004 * * * End of File * * *
```

- Execute o script "FREECF0" para liberar e desanexar o disco de parâmetro CF0.

```
===> freecf0
```
:::

### Reinicie o z/VM e verifique as alterações

Neste exercício, você aprenderá a usar alguns novos comandos CP/CMS para verificar as alterações que você fez no sistema.


- Desligue e reinicie o seu z/VM

```
===> shutdown reipl
```

Quando o sistema é desligado e reiniciado, você verá uma série de mensagens de IPL. O z/VM restaura o sistema para o mesmo estado em que estava antes do desligamento (por exemplo, com o OPERATOR desconectado).

Pressione a tecla Pause se o status do console for "MORE\..."

Para obter um logotipo do z/VM, pressione a tecla Enter.

Faça login como MAINT (senha WD5JU8QP)

Pressione a tecla Enter para ir do estado "VM READ" para o estado "RUNNING".

- Verifique o espaço de paginação e spooling

Use o comando QUERY ALLOC para exibir o número de cilindros ou páginas alocados, em uso e disponíveis para os volumes DASD conectados ao sistema.

Exiba as informações de alocação para o espaço de paginação.

```
===> query alloc page
```

A saída deve mostrar que o sistema agora possui dois volumes de paginação.

```
q alloc page                                                
                EXTENT     EXTENT  TOTAL  PAGES   HIGH    % 
VOLID  RDEV      START        END  PAGES IN USE   PAGE USED 
------ ---- ---------- ---------- ------ ------ ------ ---- 
M01P02 0300          0       2500 450180     40     51   1% 
M01P01 0206          1       3337 600660      0      0   0% 
                                  ------ ------        ---- 
SUMMARY                            1026K     24          1% 
USABLE                             1026K     24          1% 
Ready; T=0.01/0.01 11:14:41                                                              
```

Exiba as informações de alocação para o espaço de spooling.

```
===> query alloc spool
```

Se você adicionou outro disco para spool, a saída deve mostrar que o sistema agora possui dois volumes de spool.

```
query alloc spool                                          
                EXTENT     EXTENT  TOTAL  PAGES   HIGH    %
VOLID  RDEV      START        END  PAGES IN USE   PAGE USED
------ ---- ---------- ---------- ------ ------ ------ ----
M01S01 0205          1       3337 600660   9020  39600   1%
M01P02 0304          1       3337 600660      0      0   0%
                                  ------ ------        ----
SUMMARY                            1026K   9020          1%
USABLE                             1026K   9020          1%
Ready; T=0.01/0.01 11:15:20   
```

- Verifique o identificador do sistema. Use o comando IDENTIFY para exibir
informações sobre o seu ID de usuário e nó.

```
===> id
```

```
identify                                                           
MAINT    AT ZVMWSXX  VIA *        09/13/13 11:18:27 EDT      FRIDAY
Ready; T=0.01/0.01 11:18:27  
```

Seu identificador de sistema deve ser ZVMWSxx. O identificador de sistema também é exibido no canto inferior direito do console.

- Verifique a lista de volumes de usuário

Use o comando QUERY DASD para exibir uma lista dos DASDs que estão
anexados ao sistema.

```
q dasd                         
DASD 0200 CP OWNED  M01RES   89
DASD 0201 CP OWNED  VMCOM1   13
DASD 0202 CP SYSTEM VMCOM2   2 
DASD 0203 CP SYSTEM 630RL1   12
DASD 0204 CP SYSTEM 630RL2   6 
DASD 0205 CP OWNED  M01S01   1 
DASD 0206 CP OWNED  M01P01   0 
DASD 0207 CP SYSTEM M01W01   10
DASD 0208 CP SYSTEM M01W02   1 
DASD 0209 CP SYSTEM M01W03   1 
DASD (*|\textcolor{red}{0300}|*) CP OWNED  (*|\textcolor{red}{M01P02}|*)   0 
DASD (*|\textcolor{red}{0301}|*) CP SYSTEM (*|\textcolor{red}{LNX301}|*)   0
DASD (*|\textcolor{red}{0302}|*) CP SYSTEM (*|\textcolor{red}{LNX302}|*)   0
DASD (*|\textcolor{red}{0303}|*) CP SYSTEM (*|\textcolor{red}{LNX303}|*)   0
DASD (*|\textcolor{red}{0304}|*) CP OWNED  (*|\textcolor{red}{M01S02}|*)   0 
Ready; T=0.01/0.01 11:19:21    
```

- Verifique recursos Use o comando QUERY RETRIEVE para exibir a configuração
dos limites do buffer de chave de recuperação.

```
===> q ret
```

```
q ret                                                     
99 buffers available.  Maximum of 255 buffers may be selected.
Ready; T=0.01/0.01 12:27:53      
```

Agora é possível armazenar e recuperar um máximo de 99 comandos no buffer de chave de recuperação. As teclas de recuperação (PF11 e PF12) são definidas no arquivo PROFILE EXEC.

- Verificar dispositivos offline

Mais uma vez, use o comando QUERY DASD para exibir quaisquer dispositivos offline.

```
===> query dasd offline
```

```
query dasd offline             
An offline DASD was not found. 
Ready; T=0.01/0.01 12:29:29
```
:::

Este é o nosso ambiente atual após as alterações:

![Environment with extra PAGE and
SPOOL](/imgs/workshop-pagespool.png){#fig:workshoppagespool}
