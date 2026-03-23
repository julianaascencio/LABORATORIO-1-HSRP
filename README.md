# LABORATORIO-1-HSRP
## Integrantes
Lesly Juliana Ascencio Peréz

## Objetivos
En esta actividad Packet Tracer, aprenderá a configurar Hot Standby Router Protocol (HSRP) para proporcionar dispositivos de puerta de enlace predeterminados redundantes a hosts en LAN. Después de configurar HSRP, probará la configuración para comprobar que los hosts pueden utilizar la puerta de enlace predeterminada redundante si el dispositivo de puerta de enlace principal no está disponible.

· Configurar un router activo HSRP (HSRP Active).
· Configurar un router en espera HSRP (HSRP Standby).
·Verificar la operación HSRP.

## Tabla de configuraciones
<img width="339" height="227" alt="image" src="https://github.com/user-attachments/assets/a21f7d9b-4a4a-4a7b-908e-412428cea56a" />

## Antecedentes/Escenario
STP proporciona redundancia sin bucles entre switches dentro de una LAN. Sin embargo, no proporciona puertas de enlace predeterminadas redundantes , para dispositivos de usuario final dentro de la red, si falla un router de puerta de enlace. Los protocolos de redundancia de primer salto (FHRP), proporcionan puertas de enlace predeterminadas redundantes para dispositivos finales sin necesidad de configuración adicional del usuario final. Al usar FHRP, dos o más routers pueden compartir la misma dirección IP virtual y dirección MAC y pueden actuar como un solo router virtual. Los hosts de la red se configuran con una dirección IP compartida como puerta de enlace predeterminada. En esta actividad Packet Tracer, configurará el Protocolo HSRP de Cisco, el cual es un FHRP.

Configurará HSRP en los routers R1 y R3, que sirven como puertas de enlace predeterminadas para los hosts en LAN 1 y LAN 2. Al configurar HSRP, creará una puerta de enlace virtual que utilice la misma dirección de puerta de enlace predeterminada para los hosts de ambas LAN. Si un router de puerta de enlace deja de estar disponible, el segundo router se hará cargo con la misma dirección de puerta de enlace predeterminada que utilizó el primer router. Dado que los hosts de las LAN están configurados con la dirección IP de la puerta de enlace virtual como puerta de enlace predeterminada, los hosts recuperarán la conectividad a las redes remotas después de que HSRP active el router restante.

## Configuración
### Configuracion de interfaces en R1 y R2

1. En R1
   
 ```bash
 enable
configure terminal

interface g0/1
ip address 192.168.1.1 255.255.255.0
no shutdown
```
2. En R3
 ```bash
 enable
configure terminal

interface g0/0
ip address 192.168.1.3 255.255.255.0
no shutdown
```
### Configuración de HSRP

1. En R1 (ACTIVO)
   
 ```bash
interface g0/1
standby 1 ip 192.168.1.254
standby 1 priority 110
standby 1 preempt
```

Explicación:

- standby 1 ip: IP virtual
- priority 110: Mayor prioridad → será el activo
- preempt: Permite que recupere el rol si vuelve

2. En R3 (STANDBY)
   
 ```bash
interface g0/0
standby 1 ip 192.168.1.254
standby 1 priority 100
standby 1 preempt
```

3. Configuración PCs

Configurar la Ip virtual como gateway, no las IP físicas en PC-A y PC-B
```bash
Gateway: 192.168.1.254
```
<img width="771" height="299" alt="image" src="https://github.com/user-attachments/assets/c80ccafb-cd77-4e2e-bd75-6f82fd6ac440" />

4. Verficiar HSRP en R1 y R3
```bash
show standby
```
<img width="324" height="125" alt="image" src="https://github.com/user-attachments/assets/24ee945e-2732-4441-8492-66edd110640d" />

<img width="316" height="130" alt="image" src="https://github.com/user-attachments/assets/c504ceaa-37de-4844-9994-a6b4a8292845" />

## Pruebas de funcionalidad
1. Ping desde PC-A a PC-B

<img width="621" height="258" alt="image" src="https://github.com/user-attachments/assets/d06dc3b3-f233-41e8-8105-835efa39dc85" />

2. Ping desde PC-A a iNTERNET

<img width="581" height="257" alt="image" src="https://github.com/user-attachments/assets/2d7f5ded-57f0-43fd-900d-fd7094327a23" />


# Parte 1: Verificar la conectividad
## Paso 1: Rastrear la ruta de acceso al servidor Web desde el PC-A
con el siguiente comando podemos observar los saltos (hops) tal y como se mostrara en la imagen a continuación

```bash
tracert 209.265.200.226
```
<img width="610" height="196" alt="image" src="https://github.com/user-attachments/assets/9e952524-b29a-4908-8680-f3672c6d014a" />

Ruta desde PC-A:
R1 → R2 → I-Net → Web Server

## Paso 2: Rastrear la ruta de acceso al servidor Web desde el PC-B
Tal y como en el paso anterior, relaizamos las mismas acciones pero en el PC-B

```bash
tracert 209.165.200.226
```

<img width="627" height="192" alt="image" src="https://github.com/user-attachments/assets/037dad1a-138e-4524-a672-44c5d3d5fa3b" />

ya tenemos configurado HSRP en R1 y R3, 

LA PRIORIDAD DE R3 ES DEL 100 POR DEFECTO

Configuracion en R1
<img width="307" height="131" alt="image" src="https://github.com/user-attachments/assets/e6ef3015-c69b-4fbd-a7b8-f392ba473def" />

Configuracion en R3

<img width="303" height="128" alt="image" src="https://github.com/user-attachments/assets/d446418c-ffb1-4a5e-b8a3-300e1ffaf76b" />

## Respuestas:

### Router activo:
- R1

### MAC virtual:
- 0000.0C9F.F001

## Parte 3: Probar HSRP
volvemos a ejecutar el comando tracert para ver la ruta desde el PC-B
<img width="597" height="189" alt="image" src="https://github.com/user-attachments/assets/2a4876a7-ca05-4cb3-8ad0-222b4b1cd612" />

Ahora rompemos el enlace entre R1 y S1 y volvemos a ejecutar el comando tracert desde el PC-B para ver si cambia la ruta y ahora usa la IP virtual (HSRP)

<img width="630" height="196" alt="image" src="https://github.com/user-attachments/assets/6cbd9068-2853-4c86-bd2a-462406874e2a" />

## Respuesta:
- Cambia de R1 a R3 automáticamente

Ahora restauramos el enlace R1 y vemos que nuevamente toma la ruta principal 
<img width="313" height="98" alt="image" src="https://github.com/user-attachments/assets/2569367e-2e2f-4b7f-9215-52d841c47484" />

## Respuestas:

 Ruta usada:
- Vuelve a R1 → R2 → Internet

 Sin preempt:
- No volvería a R1
- Se quedaría en R3
  
# Conclusión del laboratorio
En este laboratorio se logró comprender y aplicar el funcionamiento del protocolo HSRP (Hot Standby Router Protocol), el cual permite garantizar alta disponibilidad en la red mediante la implementación de una puerta de enlace virtual compartida entre varios routers. A través de las pruebas realizadas, se evidenció que antes de configurar HSRP, cada red dependía de un único router como gateway, lo que generaba un punto único de falla.

Posteriormente, al configurar HSRP entre los routers R1 y R3, se estableció una IP virtual (192.168.1.254) que fue utilizada por los dispositivos finales como puerta de enlace predeterminada. Se definió a R1 como router activo mediante una mayor prioridad, mientras que R3 quedó en estado de espera (standby). Esto permitió observar que el tráfico de la red fluía normalmente a través del router activo.

Durante las pruebas de falla, al desconectar el router activo, se comprobó que el router en espera asumió automáticamente el rol de gateway sin interrumpir significativamente la conectividad, evidenciando así la capacidad de recuperación de la red. Asimismo, al restablecer el router principal, se verificó el funcionamiento del comando preempt, el cual permitió que R1 retomara su rol como router activo.

En conclusión, HSRP es una herramienta fundamental para mejorar la resiliencia y disponibilidad de las redes, ya que minimiza el impacto de fallas en los dispositivos de capa 3, asegurando la continuidad del servicio y una mejor experiencia para los usuarios finales.
