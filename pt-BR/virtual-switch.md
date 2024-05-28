---
order: 70
icon: server
---
# Switch Virtual

### Configurando um switch virtual (VSWITCH) com failover

Neste exercício, iremos configurar o Switch Virtual para usar dois controladores e dois conjuntos separados de dispositivos OSA. Se o controlador primário falhar, o controlador de backup assumirá. Da mesma forma, se o OSA primário falhar, o tráfego será alternado para usar os dispositivos OSA de backup.

Os passos de alto nível no processo estão nas seções a seguir:


- Defina um VSWITCH & Desligue e re-IPL z/VM

Defina o Switch Virtual no arquivo SYSTEM CONFIG para garantir que o Switch Virtual seja definido no IPL. Use a declaração MODIFY VSWITCH para autorizar um usuário z/VM a se conectar ao Switch Virtual.

- Faça login como MAINT. 
- Exiba uma lista de dispositivos OSA disponíveis. Você deve
ver os dispositivos 803-805 e 806-808 livres.

```
===> q osa free
```

Agora você pode executar o script GETCF0 para obter acesso de escrita ao disco parm primário.


```
===> getcf0
```

- Edite o arquivo SYSTEM CONFIG.


```
===> x system config x 
```

- Adicione as seguintes declarações ao final do arquivo para:

(Altere o X em macprefix para o número do seu aluno)

```
/* define vswitch named VSW1 and set MAC address prefix to 02-00-0X */
DEFINE VSWITCH VSW1 RDEV 803 806 
VMLAN MACPREFIX 0200XX
```

- Salve as alterações e saia do arquivo.

```
===> file
```

- Verifique a sintaxe usando o comando CPSYNTAX.

```
===> access 193 f
===> cpsyntax system config 
```

- Execute o script para liberar o acesso de escrita ao disco parm primário.

```
===> freecf0 
```

### Teste a configuração do VSWITCH

Reinicie o sistema z/VM e verifique as alterações que você fez na
seção de rede.


- Desligue e faça o re-ipl do seu sistema z/VM.

```
===> shutdown reipl 
```

- Faça login como MAINT.

- Exiba o VSWITCH:


```
===> q vswitch 
```

O VSWITCH VSW1 está definido?

- Para ver o status dos controladores do VSWITCH, emita o comando:


```
===> q controller
```

Qual controlador é o seu primário?


Se algum dos comandos acima indicou que você tem problemas na sua configuração,
revise seu trabalho e tente novamente. Você testará a capacidade de failover do
VSWITCH em um exercício posterior.

Este é o nosso ambiente atual após as alterações:


![Environment with TCPIP and
VSWITCH](/imgs/workshop-vswitch.png){#fig:workshopvswitch}
