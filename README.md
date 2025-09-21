<div align="center">
<img src="https://digitallfran.co/logo.svg" alt="Logo DigitAllFran" width="200"/>
<h1 align="center">QoS Avanzado para ISP: Emulando CAKE DiffServ8 con Mangle y Queue Tree</h1>
<h3>Priorizacion de 8 Niveles de Trafico para Planes de Clientes en RouterOS v7</h3>
<p>
<img src="https://img.shields.io/badge/RouterOS-v7.x-0099D5?style=for-the-badge&logo=mikik&logoColor=white" alt="RouterOS v7+"/>
<img src="https://img.shields.io/badge/QoS-Avanzado-9b59b6?style=for-the-badge" alt="QoS Avanzado"/>
<img src="https://img.shields.io/badge/Mangle-Queue_Tree-e67e22?style=for-the-badge" alt="Mangle y Queue Tree"/>
<img src="https://img.shields.io/badge/Escenario-ISP-34495e?style=for-the-badge" alt="Escenario ISP"/>
</p>
</div>
<hr>

> [!NOTE]<div style="border-left: 5px solid #007bff; padding: 10px 15px; background-color: #f8f9fa; margin-top: 20px; margin-bottom: 20px;">
> <p><strong>Objetivo de este Script: Educacion y Demostracion</strong><br>
> Este script es un ejercicio tecnico disenado para replicar manualmente la logica de priorizacion de 8 niveles de trafico del algoritmo <strong>CAKE (diffserv8)</strong>. Su proposito es demostrar el nivel de detalle y la complejidad necesarios para construir un sistema de Calidad de Servicio (QoS) granular y robusto en MikroTik utilizando Mangle y Queue Tree.</p>
> </div>

> [!WARNING]
> <div style="border-left: 5px solid #ffc107; padding: 10px 15px; background-color: #fff3cd; margin-bottom: 20px;">
> <p><strong>RECOMENDACION IMPORTANTE: Utiliza CAKE Nativamente</strong><br>
> Aunque este script es funcional, es una <strong>simulacion</strong>. Si tu hardware lo soporta, la forma mas optima, eficiente y recomendada de gestionar la calidad de servicio es utilizando el algoritmo <strong>CAKE</strong> de forma nativa. CAKE no solo prioriza el trafico, sino que tambien gestiona activamente la latencia (Bufferbloat) y la justicia de flujos, tareas que este script no realiza con la misma eficacia.</p>
> <p><strong>Este script es una herramienta para entender lo que CAKE hace por ti con un solo clic.</strong></p>
> </div>
El Concepto: Un Sistema de QoS en 4 Pasos
Para construir nuestro sistema, seguiremos una logica de 4 pasos fundamentales:
Preparacion del Firewall: Desactivar fasttrack para que el router pueda "ver" e inspeccionar cada paquete.
Creacion de Planes (Queue Types): Definir los limites de velocidad y la justicia entre clientes de un mismo plan usando PCQ.
El Cerebro (Mangle): Clasificar y etiquetar cada paquete segun su nivel de prioridad.
El Organizador (Queue Tree): Usar las etiquetas del Mangle para colocar cada paquete en la cola de prioridad correcta.
Paso 1: Preparacion del Firewall
Antes de empezar, debemos asegurarnos de que el firewall no este usando fasttrack para las conexiones que queremos gestionar. El fasttrack acelera el trafico, pero lo hace "invisible" para las reglas de Mangle.
Nota: El script de configuracion inicial ya incluye la modificacion necesaria en la regla de fasttrack. Si usaste ese script, este paso ya esta cubierto.
Paso 2: Creacion de los "Moldes" de Velocidad (Queue Types)
Aqui definimos los planes de nuestros clientes usando PCQ (Per Connection Queue). PCQ tiene dos funciones vitales:
Limitar la velocidad: Asegura que cada cliente individual no supere el ancho de banda contratado (5M, 8M o 15M).
Repartir de forma justa: Si varios clientes de un mismo plan usan la red al mismo tiempo, PCQ reparte el ancho de banda disponible de forma equitativa entre ellos.
<pre><code>/queue type
add kind=pcq name=PCQ_Basico_Bajada pcq-classifier=dst-address pcq-rate=5M
add kind=pcq name=PCQ_Basico_Subida pcq-classifier=src-address pcq-rate=5M
add kind=pcq name=PCQ_Familiar_Bajada pcq-classifier=dst-address pcq-rate=8M
add kind=pcq name=PCQ_Familiar_Subida pcq-classifier=src-address pcq-rate=8M
add kind=pcq name=PCQ_Gamer_Bajada pcq-classifier=dst-address pcq-rate=15M
add kind=pcq name=PCQ_Gamer_Subida pcq-classifier=src-address pcq-rate=15M
</code></pre>
Paso 3: El Cerebro del QoS (Firewall Mangle)
Esta es la parte mas compleja. El Mangle actua como un inspector de trafico que pone una etiqueta (marca) a cada paquete. Lo hacemos en tres fases.
Fase 1: Marcar la Conexion por Tipo de Servicio
Primero, identificamos el tipo de trafico y le ponemos una etiqueta generica a toda la conexion. Usamos las 8 clases de DiffServ8 como guia.
<pre><code># [QoS] P1: Control de Red (DSCP 48-63 y ICMP)
:for i from=48 to=63 do={
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P1: Control de Red (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P1_NetworkControl passthrough=yes
}
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P1: Control de Red (ICMP)" connection-mark=no-mark protocol=icmp new-connection-mark=MC_P1_NetworkControl passthrough=yes

# [QoS] P2: Telefonia (DSCP 46 y Puertos VoIP)
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P2: Telefonia (DSCP 46)" connection-mark=no-mark dscp=46 new-connection-mark=MC_P2_Telephony passthrough=yes
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P2: Telefonia (Puertos VoIP)" connection-mark=no-mark port=5060,5061,10000-20000 protocol=udp new-connection-mark=MC_P2_Telephony passthrough=yes

# [QoS] P3: Senalizacion (DSCP 32-47)
:for i from=32 to=47 do={
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P3: Senalizacion (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P3_Signaling passthrough=yes
}

# [QoS] P4: Conferencia Multimedia (DSCP 24-31)
:for i from=24 to=31 do={
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P4: Conferencia Multimedia (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P4_MultimediaConf passthrough=yes
}

# [QoS] P5: Interactivo Tiempo Real (DSCP 40 y Puertos de Juegos)
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P5: Interactivo Tiempo Real (DSCP 40)" connection-mark=no-mark dscp=40 new-connection-mark=MC_P5_RealTime passthrough=yes
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P5: Interactivo Tiempo Real (Puertos de Juegos)" connection-mark=no-mark port=7000-9000,27000-27050,30000-30050 protocol=udp new-connection-mark=MC_P5_RealTime passthrough=yes

# [QoS] P6: Streaming Multimedia (DSCP 16-23 y Puertos Web/Video)
:for i from=16 to=23 do={
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P6: Streaming Multimedia (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P6_Streaming passthrough=yes
}
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P6: Streaming Multimedia (Puertos Web/Video)" connection-mark=no-mark port=80,443 protocol=tcp new-connection-mark=MC_P6_Streaming passthrough=yes

# [QoS] P7: Datos de Baja Latencia (DSCP 8-15 y DNS)
:for i from=8 to=15 do={
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P7: Datos de Baja Latencia (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P7_LowLatencyData passthrough=yes
}
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P7: Datos de Baja Latencia (DNS)" connection-mark=no-mark port=53 protocol=udp new-connection-mark=MC_P7_LowLatencyData passthrough=yes

# [QoS] P8: Mejor Esfuerzo (Todo lo demas)
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P8: Mejor Esfuerzo (Todo lo demas)" connection-mark=no-mark new-connection-mark=MC_P8_BestEffort passthrough=yes
</code></pre>
Fases 2 y 3: Marcar los Paquetes de Subida y Bajada
Ahora, leemos la etiqueta generica de la conexion y, dependiendo de que cliente (Plan Basico, Familiar o Gamer) este generando el trafico, le ponemos una etiqueta final y unica al paquete.
<pre><code># === FASE 2: Marcar Paquetes de Subida (en PREROUTING) ===
# --- Plan BASICO ---
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P1 Subida" connection-mark=MC_P1_NetworkControl in-interface-list=LANs new-packet-mark=MP_Basico_P1_Subida passthrough=no src-address-list=Plan_Basico
... (y asi para las 8 prioridades y los 3 planes) ...

# === FASE 3: Marcar Paquetes de Bajada (en FORWARD) ===
# --- Plan BASICO ---
add action=mark-packet chain=forward comment="[QoS] MP: Basico P1 Bajada" connection-mark=MC_P1_NetworkControl in-interface-list=WANs new-packet-mark=MP_Basico_P1_Bajada passthrough=no dst-address-list=Plan_Basico
... (y asi para las 8 prioridades y los 3 planes) ...
</code></pre>
Nota: El codigo completo se encuentra en la seccion final.
Paso 4: El Organizador (Queue Tree)
Finalmente, el Queue Tree usa las etiquetas de paquete que creamos en Mangle para dirigir cada paquete a su cola correcta. Las colas tienen una priority del 1 (la mas alta) al 8 (la mas baja), asegurando que el trafico critico siempre tenga preferencia.
<pre><code>/queue tree
# Colas Globales
add name=SUBIDA_TOTAL parent=global max-limit=500M
add name=BAJADA_TOTAL parent=global max-limit=500M

# -- Colas para Plan BASICO --
add name=PlanBasico_P1_Subida packet-mark=MP_Basico_P1_Subida parent=SUBIDA_TOTAL priority=1 queue=PCQ_Basico_Subida
add name=PlanBasico_P2_Subida packet-mark=MP_Basico_P2_Subida parent=SUBIDA_TOTAL priority=2 queue=PCQ_Basico_Subida
... (y asi para las 8 prioridades de subida y bajada) ...

# -- Colas para Plan FAMILIAR --
... (8 prioridades de subida y bajada) ...

# -- Colas para Plan GAMER --
... (8 prioridades de subida y bajada) ...
</code></pre>
> [!NOTE]<h2>Script Limpio de QoS (para Copiar y Pegar)</h2>
> <p>Esta es la version final del script de QoS con todos los comandos en secuencia, lista para ser ejecutada directamente en la terminal de tu router.</p>

<pre><code>
# Creacion de planes
/queue type
add kind=pcq name=PCQ_Basico_Bajada pcq-classifier=dst-address pcq-rate=5M
add kind=pcq name=PCQ_Basico_Subida pcq-classifier=src-address pcq-rate=5M
add kind=pcq name=PCQ_Familiar_Bajada pcq-classifier=dst-address pcq-rate=8M
add kind=pcq name=PCQ_Familiar_Subida pcq-classifier=src-address pcq-rate=8M
add kind=pcq name=PCQ_Gamer_Bajada pcq-classifier=dst-address pcq-rate=15M
add kind=pcq name=PCQ_Gamer_Subida pcq-classifier=src-address pcq-rate=15M

# 2.4 - Construir el Arbol de Colas (Version 8 Prioridades - Emulando DiffServ8)
/queue tree
add name=SUBIDA_TOTAL parent=global max-limit=500M
add name=BAJADA_TOTAL parent=global max-limit=500M
add name=PlanBasico_P1_Subida packet-mark=MP_Basico_P1_Subida parent=SUBIDA_TOTAL priority=1 queue=PCQ_Basico_Subida comment="Prioridad 1 para Plan Basico"
add name=PlanBasico_P2_Subida packet-mark=MP_Basico_P2_Subida parent=SUBIDA_TOTAL priority=2 queue=PCQ_Basico_Subida comment="Prioridad 2 para Plan Basico"
add name=PlanBasico_P3_Subida packet-mark=MP_Basico_P3_Subida parent=SUBIDA_TOTAL priority=3 queue=PCQ_Basico_Subida comment="Prioridad 3 para Plan Basico"
add name=PlanBasico_P4_Subida packet-mark=MP_Basico_P4_Subida parent=SUBIDA_TOTAL priority=4 queue=PCQ_Basico_Subida comment="Prioridad 4 para Plan Basico"
add name=PlanBasico_P5_Subida packet-mark=MP_Basico_P5_Subida parent=SUBIDA_TOTAL priority=5 queue=PCQ_Basico_Subida comment="Prioridad 5 para Plan Basico"
add name=PlanBasico_P6_Subida packet-mark=MP_Basico_P6_Subida parent=SUBIDA_TOTAL priority=6 queue=PCQ_Basico_Subida comment="Prioridad 6 para Plan Basico"
add name=PlanBasico_P7_Subida packet-mark=MP_Basico_P7_Subida parent=SUBIDA_TOTAL priority=7 queue=PCQ_Basico_Subida comment="Prioridad 7 para Plan Basico"
add name=PlanBasico_P8_Subida packet-mark=MP_Basico_P8_Subida parent=SUBIDA_TOTAL priority=8 queue=PCQ_Basico_Subida comment="Prioridad 8 para Plan Basico"
add name=PlanBasico_P1_Bajada packet-mark=MP_Basico_P1_Bajada parent=BAJADA_TOTAL priority=1 queue=PCQ_Basico_Bajada comment="Prioridad 1 para Plan Basico"
add name=PlanBasico_P2_Bajada packet-mark=MP_Basico_P2_Bajada parent=BAJADA_TOTAL priority=2 queue=PCQ_Basico_Bajada comment="Prioridad 2 para Plan Basico"
add name=PlanBasico_P3_Bajada packet-mark=MP_Basico_P3_Bajada parent=BAJADA_TOTAL priority=3 queue=PCQ_Basico_Bajada comment="Prioridad 3 para Plan Basico"
add name=PlanBasico_P4_Bajada packet-mark=MP_Basico_P4_Bajada parent=BAJADA_TOTAL priority=4 queue=PCQ_Basico_Bajada comment="Prioridad 4 para Plan Basico"
add name=PlanBasico_P5_Bajada packet-mark=MP_Basico_P5_Bajada parent=BAJADA_TOTAL priority=5 queue=PCQ_Basico_Bajada comment="Prioridad 5 para Plan Basico"
add name=PlanBasico_P6_Bajada packet-mark=MP_Basico_P6_Bajada parent=BAJADA_TOTAL priority=6 queue=PCQ_Basico_Bajada comment="Prioridad 6 para Plan Basico"
add name=PlanBasico_P7_Bajada packet-mark=MP_Basico_P7_Bajada parent=BAJADA_TOTAL priority=7 queue=PCQ_Basico_Bajada comment="Prioridad 7 para Plan Basico"
add name=PlanBasico_P8_Bajada packet-mark=MP_Basico_P8_Bajada parent=BAJADA_TOTAL priority=8 queue=PCQ_Basico_Bajada comment="Prioridad 8 para Plan Basico"
add name=PlanFamiliar_P1_Subida packet-mark=MP_Familiar_P1_Subida parent=SUBIDA_TOTAL priority=1 queue=PCQ_Familiar_Subida comment="Prioridad 1 para Plan Familiar"
add name=PlanFamiliar_P2_Subida packet-mark=MP_Familiar_P2_Subida parent=SUBIDA_TOTAL priority=2 queue=PCQ_Familiar_Subida comment="Prioridad 2 para Plan Familiar"
add name=PlanFamiliar_P3_Subida packet-mark=MP_Familiar_P3_Subida parent=SUBIDA_TOTAL priority=3 queue=PCQ_Familiar_Subida comment="Prioridad 3 para Plan Familiar"
add name=PlanFamiliar_P4_Subida packet-mark=MP_Familiar_P4_Subida parent=SUBIDA_TOTAL priority=4 queue=PCQ_Familiar_Subida comment="Prioridad 4 para Plan Familiar"
add name=PlanFamiliar_P5_Subida packet-mark=MP_Familiar_P5_Subida parent=SUBIDA_TOTAL priority=5 queue=PCQ_Familiar_Subida comment="Prioridad 5 para Plan Familiar"
add name=PlanFamiliar_P6_Subida packet-mark=MP_Familiar_P6_Subida parent=SUBIDA_TOTAL priority=6 queue=PCQ_Familiar_Subida comment="Prioridad 6 para Plan Familiar"
add name=PlanFamiliar_P7_Subida packet-mark=MP_Familiar_P7_Subida parent=SUBIDA_TOTAL priority=7 queue=PCQ_Familiar_Subida comment="Prioridad 7 para Plan Familiar"
add name=PlanFamiliar_P8_Subida packet-mark=MP_Familiar_P8_Subida parent=SUBIDA_TOTAL priority=8 queue=PCQ_Familiar_Subida comment="Prioridad 8 para Plan Familiar"
add name=PlanFamiliar_P1_Bajada packet-mark=MP_Familiar_P1_Bajada parent=BAJADA_TOTAL priority=1 queue=PCQ_Familiar_Bajada comment="Prioridad 1 para Plan Familiar"
add name=PlanFamiliar_P2_Bajada packet-mark=MP_Familiar_P2_Bajada parent=BAJADA_TOTAL priority=2 queue=PCQ_Familiar_Bajada comment="Prioridad 2 para Plan Familiar"
add name=PlanFamiliar_P3_Bajada packet-mark=MP_Familiar_P3_Bajada parent=BAJADA_TOTAL priority=3 queue=PCQ_Familiar_Bajada comment="Prioridad 3 para Plan Familiar"
add name=PlanFamiliar_P4_Bajada packet-mark=MP_Familiar_P4_Bajada parent=BAJADA_TOTAL priority=4 queue=PCQ_Familiar_Bajada comment="Prioridad 4 para Plan Familiar"
add name=PlanFamiliar_P5_Bajada packet-mark=MP_Familiar_P5_Bajada parent=BAJADA_TOTAL priority=5 queue=PCQ_Familiar_Bajada comment="Prioridad 5 para Plan Familiar"
add name=PlanFamiliar_P6_Bajada packet-mark=MP_Familiar_P6_Bajada parent=BAJADA_TOTAL priority=6 queue=PCQ_Familiar_Bajada comment="Prioridad 6 para Plan Familiar"
add name=PlanFamiliar_P7_Bajada packet-mark=MP_Familiar_P7_Bajada parent=BAJADA_TOTAL priority=7 queue=PCQ_Familiar_Bajada comment="Prioridad 7 para Plan Familiar"
add name=PlanFamiliar_P8_Bajada packet-mark=MP_Familiar_P8_Bajada parent=BAJADA_TOTAL priority=8 queue=PCQ_Familiar_Bajada comment="Prioridad 8 para Plan Familiar"
add name=PlanGamer_P1_Subida packet-mark=MP_Gamer_P1_Subida parent=SUBIDA_TOTAL priority=1 queue=PCQ_Gamer_Subida comment="Prioridad 1 para Plan Gamer"
add name=PlanGamer_P2_Subida packet-mark=MP_Gamer_P2_Subida parent=SUBIDA_TOTAL priority=2 queue=PCQ_Gamer_Subida comment="Prioridad 2 para Plan Gamer"
add name=PlanGamer_P3_Subida packet-mark=MP_Gamer_P3_Subida parent=SUBIDA_TOTAL priority=3 queue=PCQ_Gamer_Subida comment="Prioridad 3 para Plan Gamer"
add name=PlanGamer_P4_Subida packet-mark=MP_Gamer_P4_Subida parent=SUBIDA_TOTAL priority=4 queue=PCQ_Gamer_Subida comment="Prioridad 4 para Plan Gamer"
add name=PlanGamer_P5_Subida packet-mark=MP_Gamer_P5_Subida parent=SUBIDA_TOTAL priority=5 queue=PCQ_Gamer_Subida comment="Prioridad 5 para Plan Gamer"
add name=PlanGamer_P6_Subida packet-mark=MP_Gamer_P6_Subida parent=SUBIDA_TOTAL priority=6 queue=PCQ_Gamer_Subida comment="Prioridad 6 para Plan Gamer"
add name=PlanGamer_P7_Subida packet-mark=MP_Gamer_P7_Subida parent=SUBIDA_TOTAL priority=7 queue=PCQ_Gamer_Subida comment="Prioridad 7 para Plan Gamer"
add name=PlanGamer_P8_Subida packet-mark=MP_Gamer_P8_Subida parent=SUBIDA_TOTAL priority=8 queue=PCQ_Gamer_Subida comment="Prioridad 8 para Plan Gamer"
add name=PlanGamer_P1_Bajada packet-mark=MP_Gamer_P1_Bajada parent=BAJADA_TOTAL priority=1 queue=PCQ_Gamer_Bajada comment="Prioridad 1 para Plan Gamer"
add name=PlanGamer_P2_Bajada packet-mark=MP_Gamer_P2_Bajada parent=BAJADA_TOTAL priority=2 queue=PCQ_Gamer_Bajada comment="Prioridad 2 para Plan Gamer"
add name=PlanGamer_P3_Bajada packet-mark=MP_Gamer_P3_Bajada parent=BAJADA_TOTAL priority=3 queue=PCQ_Gamer_Bajada comment="Prioridad 3 para Plan Gamer"
add name=PlanGamer_P4_Bajada packet-mark=MP_Gamer_P4_Bajada parent=BAJADA_TOTAL priority=4 queue=PCQ_Gamer_Bajada comment="Prioridad 4 para Plan Gamer"
add name=PlanGamer_P5_Bajada packet-mark=MP_Gamer_P5_Bajada parent=BAJADA_TOTAL priority=5 queue=PCQ_Gamer_Bajada comment="Prioridad 5 para Plan Gamer"
add name=PlanGamer_P6_Bajada packet-mark=MP_Gamer_P6_Bajada parent=BAJADA_TOTAL priority=6 queue=PCQ_Gamer_Bajada comment="Prioridad 6 para Plan Gamer"
add name=PlanGamer_P7_Bajada packet-mark=MP_Gamer_P7_Bajada parent=BAJADA_TOTAL priority=7 queue=PCQ_Gamer_Bajada comment="Prioridad 7 para Plan Gamer"
add name=PlanGamer_P8_Bajada packet-mark=MP_Gamer_P8_Bajada parent=BAJADA_TOTAL priority=8 queue=PCQ_Gamer_Bajada comment="Prioridad 8 para Plan Gamer"

/ip firewall mangle
:for i from=48 to=63 do={ /ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P1: Control de Red (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P1_NetworkControl passthrough=yes }
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P1: Control de Red (ICMP)" connection-mark=no-mark protocol=icmp new-connection-mark=MC_P1_NetworkControl passthrough=yes
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P2: Telefonia (DSCP 46)" connection-mark=no-mark dscp=46 new-connection-mark=MC_P2_Telephony passthrough=yes
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P2: Telefonia (Puertos VoIP)" connection-mark=no-mark port=5060,5061,10000-20000 protocol=udp new-connection-mark=MC_P2_Telephony passthrough=yes
:for i from=32 to=47 do={ /ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P3: Senalizacion (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P3_Signaling passthrough=yes }
:for i from=24 to=31 do={ /ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P4: Conferencia Multimedia (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P4_MultimediaConf passthrough=yes }
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P5: Interactivo Tiempo Real (DSCP 40)" connection-mark=no-mark dscp=40 new-connection-mark=MC_P5_RealTime passthrough=yes
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P5: Interactivo Tiempo Real (Puertos de Juegos)" connection-mark=no-mark port=7000-9000,27000-27050,30000-30050 protocol=udp new-connection-mark=MC_P5_RealTime passthrough=yes
:for i from=16 to=23 do={ /ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P6: Streaming Multimedia (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P6_Streaming passthrough=yes }
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P6: Streaming Multimedia (Puertos Web/Video)" connection-mark=no-mark port=80,443 protocol=tcp new-connection-mark=MC_P6_Streaming passthrough=yes
:for i from=8 to=15 do={ /ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P7: Datos de Baja Latencia (DSCP $i)" connection-mark=no-mark dscp=$i new-connection-mark=MC_P7_LowLatencyData passthrough=yes }
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P7: Datos de Baja Latencia (DNS)" connection-mark=no-mark port=53 protocol=udp new-connection-mark=MC_P7_LowLatencyData passthrough=yes
/ip firewall mangle add action=mark-connection chain=prerouting comment="[QoS] P8: Mejor Esfuerzo (Todo lo demas)" connection-mark=no-mark new-connection-mark=MC_P8_BestEffort passthrough=yes
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P1 Subida" connection-mark=MC_P1_NetworkControl in-interface-list=LANs new-packet-mark=MP_Basico_P1_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P2 Subida" connection-mark=MC_P2_Telephony in-interface-list=LANs new-packet-mark=MP_Basico_P2_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P3 Subida" connection-mark=MC_P3_Signaling in-interface-list=LANs new-packet-mark=MP_Basico_P3_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P4 Subida" connection-mark=MC_P4_MultimediaConf in-interface-list=LANs new-packet-mark=MP_Basico_P4_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P5 Subida" connection-mark=MC_P5_RealTime in-interface-list=LANs new-packet-mark=MP_Basico_P5_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P6 Subida" connection-mark=MC_P6_Streaming in-interface-list=LANs new-packet-mark=MP_Basico_P6_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P7 Subida" connection-mark=MC_P7_LowLatencyData in-interface-list=LANs new-packet-mark=MP_Basico_P7_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P8 Subida" connection-mark=MC_P8_BestEffort in-interface-list=LANs new-packet-mark=MP_Basico_P8_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P1 Subida" connection-mark=MC_P1_NetworkControl in-interface-list=LANs new-packet-mark=MP_Familiar_P1_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P2 Subida" connection-mark=MC_P2_Telephony in-interface-list=LANs new-packet-mark=MP_Familiar_P2_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P3 Subida" connection-mark=MC_P3_Signaling in-interface-list=LANs new-packet-mark=MP_Familiar_P3_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P4 Subida" connection-mark=MC_P4_MultimediaConf in-interface-list=LANs new-packet-mark=MP_Familiar_P4_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P5 Subida" connection-mark=MC_P5_RealTime in-interface-list=LANs new-packet-mark=MP_Familiar_P5_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P6 Subida" connection-mark=MC_P6_Streaming in-interface-list=LANs new-packet-mark=MP_Familiar_P6_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P7 Subida" connection-mark=MC_P7_LowLatencyData in-interface-list=LANs new-packet-mark=MP_Familiar_P7_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P8 Subida" connection-mark=MC_P8_BestEffort in-interface-list=LANs new-packet-mark=MP_Familiar_P8_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P1 Subida" connection-mark=MC_P1_NetworkControl in-interface-list=LANs new-packet-mark=MP_Gamer_P1_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P2 Subida" connection-mark=MC_P2_Telephony in-interface-list=LANs new-packet-mark=MP_Gamer_P2_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P3 Subida" connection-mark=MC_P3_Signaling in-interface-list=LANs new-packet-mark=MP_Gamer_P3_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P4 Subida" connection-mark=MC_P4_MultimediaConf in-interface-list=LANs new-packet-mark=MP_Gamer_P4_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P5 Subida" connection-mark=MC_P5_RealTime in-interface-list=LANs new-packet-mark=MP_Gamer_P5_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P6 Subida" connection-mark=MC_P6_Streaming in-interface-list=LANs new-packet-mark=MP_Gamer_P6_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P7 Subida" connection-mark=MC_P7_LowLatencyData in-interface-list=LANs new-packet-mark=MP_Gamer_P7_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P8 Subida" connection-mark=MC_P8_BestEffort in-interface-list=LANs new-packet-mark=MP_Gamer_P8_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Basico P1 Bajada" connection-mark=MC_P1_NetworkControl in-interface-list=WANs new-packet-mark=MP_Basico_P1_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P2 Bajada" connection-mark=MC_P2_Telephony in-interface-list=WANs new-packet-mark=MP_Basico_P2_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P3 Bajada" connection-mark=MC_P3_Signaling in-interface-list=WANs new-packet-mark=MP_Basico_P3_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P4 Bajada" connection-mark=MC_P4_MultimediaConf in-interface-list=WANs new-packet-mark=MP_Basico_P4_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P5 Bajada" connection-mark=MC_P5_RealTime in-interface-list=WANs new-packet-mark=MP_Basico_P5_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P6 Bajada" connection-mark=MC_P6_Streaming in-interface-list=WANs new-packet-mark=MP_Basico_P6_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P7 Bajada" connection-mark=MC_P7_LowLatencyData in-interface-list=WANs new-packet-mark=MP_Basico_P7_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P8 Bajada" connection-mark=MC_P8_BestEffort in-interface-list=WANs new-packet-mark=MP_Basico_P8_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P1 Bajada" connection-mark=MC_P1_NetworkControl in-interface-list=WANs new-packet-mark=MP_Familiar_P1_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P2 Bajada" connection-mark=MC_P2_Telephony in-interface-list=WANs new-packet-mark=MP_Familiar_P2_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P3 Bajada" connection-mark=MC_P3_Signaling in-interface-list=WANs new-packet-mark=MP_Familiar_P3_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P4 Bajada" connection-mark=MC_P4_MultimediaConf in-interface-list=WANs new-packet-mark=MP_Familiar_P4_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P5 Bajada" connection-mark=MC_P5_RealTime in-interface-list=WANs new-packet-mark=MP_Familiar_P5_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P6 Bajada" connection-mark=MC_P6_Streaming in-interface-list=WANs new-packet-mark=MP_Familiar_P6_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P7 Bajada" connection-mark=MC_P7_LowLatencyData in-interface-list=WANs new-packet-mark=MP_Familiar_P7_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P8 Bajada" connection-mark=MC_P8_BestEffort in-interface-list=WANs new-packet-mark=MP_Familiar_P8_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P1 Bajada" connection-mark=MC_P1_NetworkControl in-interface-list=WANs new-packet-mark=MP_Gamer_P1_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P2 Bajada" connection-mark=MC_P2_Telephony in-interface-list=WANs new-packet-mark=MP_Gamer_P2_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P3 Bajada" connection-mark=MC_P3_Signaling in-interface-list=WANs new-packet-mark=MP_Gamer_P3_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P4 Bajada" connection-mark=MC_P4_MultimediaConf in-interface-list=WANs new-packet-mark=MP_Gamer_P4_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P5 Bajada" connection-mark=MC_P5_RealTime in-interface-list=WANs new-packet-mark=MP_Gamer_P5_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P6 Bajada" connection-mark=MC_P6_Streaming in-interface-list=WANs new-packet-mark=MP_Gamer_P6_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P7 Bajada" connection-mark=MC_P7_LowLatencyData in-interface-list=WANs new-packet-mark=MP_Gamer_P7_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P8 Bajada" connection-mark=MC_P8_BestEffort in-interface-list=WANs new-packet-mark=MP_Gamer_P8_Bajada passthrough=no dst-address-list=Plan_Gamer

:log info "Script de QoS Avanzado Aplicado Correctamente."
</code></pre>
<hr>
<div align="center" style="margin-top: 40px;">
<img src="https://digitallfran.co/logo.svg" alt="Logo DigitAllFran" width="100"/>
<p><strong>¿Necesitas soporte, consultoria o quieres llevar tu comunicacion al siguiente nivel?</strong></p>
<a href="https://digitallfran.co" target="_blank" rel="noopener noreferrer">¡Visita digitallfran.co y agenda tu consulta!</a>
</div>
