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
# Colas Globales (500M Simetricos)
add name=SUBIDA_TOTAL_500M parent=global max-limit=500M
add name=BAJADA_TOTAL_500M parent=global max-limit=500M

# -- Colas para Plan BASICO --
add name=PlanBasico_P1_Subida packet-mark=MP_Basico_P1_Subida parent=SUBIDA_TOTAL_500M priority=1 queue=PCQ_Basico_Subida comment="Prioridad 1 para Plan Basico"
add name=PlanBasico_P2_Subida packet-mark=MP_Basico_P2_Subida parent=SUBIDA_TOTAL_500M priority=2 queue=PCQ_Basico_Subida comment="Prioridad 2 para Plan Basico"
add name=PlanBasico_P3_Subida packet-mark=MP_Basico_P3_Subida parent=SUBIDA_TOTAL_500M priority=3 queue=PCQ_Basico_Subida comment="Prioridad 3 para Plan Basico"
add name=PlanBasico_P4_Subida packet-mark=MP_Basico_P4_Subida parent=SUBIDA_TOTAL_500M priority=4 queue=PCQ_Basico_Subida comment="Prioridad 4 para Plan Basico"
add name=PlanBasico_P5_Subida packet-mark=MP_Basico_P5_Subida parent=SUBIDA_TOTAL_500M priority=5 queue=PCQ_Basico_Subida comment="Prioridad 5 para Plan Basico"
add name=PlanBasico_P6_Subida packet-mark=MP_Basico_P6_Subida parent=SUBIDA_TOTAL_500M priority=6 queue=PCQ_Basico_Subida comment="Prioridad 6 para Plan Basico"
add name=PlanBasico_P7_Subida packet-mark=MP_Basico_P7_Subida parent=SUBIDA_TOTAL_500M priority=7 queue=PCQ_Basico_Subida comment="Prioridad 7 para Plan Basico"
add name=PlanBasico_P8_Subida packet-mark=MP_Basico_P8_Subida parent=SUBIDA_TOTAL_500M priority=8 queue=PCQ_Basico_Subida comment="Prioridad 8 para Plan Basico"
add name=PlanBasico_P1_Bajada packet-mark=MP_Basico_P1_Bajada parent=BAJADA_TOTAL_500M priority=1 queue=PCQ_Basico_Bajada comment="Prioridad 1 para Plan Basico"
add name=PlanBasico_P2_Bajada packet-mark=MP_Basico_P2_Bajada parent=BAJADA_TOTAL_500M priority=2 queue=PCQ_Basico_Bajada comment="Prioridad 2 para Plan Basico"
add name=PlanBasico_P3_Bajada packet-mark=MP_Basico_P3_Bajada parent=BAJADA_TOTAL_500M priority=3 queue=PCQ_Basico_Bajada comment="Prioridad 3 para Plan Basico"
add name=PlanBasico_P4_Bajada packet-mark=MP_Basico_P4_Bajada parent=BAJADA_TOTAL_500M priority=4 queue=PCQ_Basico_Bajada comment="Prioridad 4 para Plan Basico"
add name=PlanBasico_P5_Bajada packet-mark=MP_Basico_P5_Bajada parent=BAJADA_TOTAL_500M priority=5 queue=PCQ_Basico_Bajada comment="Prioridad 5 para Plan Basico"
add name=PlanBasico_P6_Bajada packet-mark=MP_Basico_P6_Bajada parent=BAJADA_TOTAL_500M priority=6 queue=PCQ_Basico_Bajada comment="Prioridad 6 para Plan Basico"
add name=PlanBasico_P7_Bajada packet-mark=MP_Basico_P7_Bajada parent=BAJADA_TOTAL_500M priority=7 queue=PCQ_Basico_Bajada comment="Prioridad 7 para Plan Basico"
add name=PlanBasico_P8_Bajada packet-mark=MP_Basico_P8_Bajada parent=BAJADA_TOTAL_500M priority=8 queue=PCQ_Basico_Bajada comment="Prioridad 8 para Plan Basico"

# -- Colas para Plan FAMILIAR --
add name=PlanFamiliar_P1_Subida packet-mark=MP_Familiar_P1_Subida parent=SUBIDA_TOTAL_500M priority=1 queue=PCQ_Familiar_Subida comment="Prioridad 1 para Plan Familiar"
add name=PlanFamiliar_P2_Subida packet-mark=MP_Familiar_P2_Subida parent=SUBIDA_TOTAL_500M priority=2 queue=PCQ_Familiar_Subida comment="Prioridad 2 para Plan Familiar"
add name=PlanFamiliar_P3_Subida packet-mark=MP_Familiar_P3_Subida parent=SUBIDA_TOTAL_500M priority=3 queue=PCQ_Familiar_Subida comment="Prioridad 3 para Plan Familiar"
add name=PlanFamiliar_P4_Subida packet-mark=MP_Familiar_P4_Subida parent=SUBIDA_TOTAL_500M priority=4 queue=PCQ_Familiar_Subida comment="Prioridad 4 para Plan Familiar"
add name=PlanFamiliar_P5_Subida packet-mark=MP_Familiar_P5_Subida parent=SUBIDA_TOTAL_500M priority=5 queue=PCQ_Familiar_Subida comment="Prioridad 5 para Plan Familiar"
add name=PlanFamiliar_P6_Subida packet-mark=MP_Familiar_P6_Subida parent=SUBIDA_TOTAL_500M priority=6 queue=PCQ_Familiar_Subida comment="Prioridad 6 para Plan Familiar"
add name=PlanFamiliar_P7_Subida packet-mark=MP_Familiar_P7_Subida parent=SUBIDA_TOTAL_500M priority=7 queue=PCQ_Familiar_Subida comment="Prioridad 7 para Plan Familiar"
add name=PlanFamiliar_P8_Subida packet-mark=MP_Familiar_P8_Subida parent=SUBIDA_TOTAL_500M priority=8 queue=PCQ_Familiar_Subida comment="Prioridad 8 para Plan Familiar"
add name=PlanFamiliar_P1_Bajada packet-mark=MP_Familiar_P1_Bajada parent=BAJADA_TOTAL_500M priority=1 queue=PCQ_Familiar_Bajada comment="Prioridad 1 para Plan Familiar"
add name=PlanFamiliar_P2_Bajada packet-mark=MP_Familiar_P2_Bajada parent=BAJADA_TOTAL_500M priority=2 queue=PCQ_Familiar_Bajada comment="Prioridad 2 para Plan Familiar"
add name=PlanFamiliar_P3_Bajada packet-mark=MP_Familiar_P3_Bajada parent=BAJADA_TOTAL_500M priority=3 queue=PCQ_Familiar_Bajada comment="Prioridad 3 para Plan Familiar"
add name=PlanFamiliar_P4_Bajada packet-mark=MP_Familiar_P4_Bajada parent=BAJADA_TOTAL_500M priority=4 queue=PCQ_Familiar_Bajada comment="Prioridad 4 para Plan Familiar"
add name=PlanFamiliar_P5_Bajada packet-mark=MP_Familiar_P5_Bajada parent=BAJADA_TOTAL_500M priority=5 queue=PCQ_Familiar_Bajada comment="Prioridad 5 para Plan Familiar"
add name=PlanFamiliar_P6_Bajada packet-mark=MP_Familiar_P6_Bajada parent=BAJADA_TOTAL_500M priority=6 queue=PCQ_Familiar_Bajada comment="Prioridad 6 para Plan Familiar"
add name=PlanFamiliar_P7_Bajada packet-mark=MP_Familiar_P7_Bajada parent=BAJADA_TOTAL_500M priority=7 queue=PCQ_Familiar_Bajada comment="Prioridad 7 para Plan Familiar"
add name=PlanFamiliar_P8_Bajada packet-mark=MP_Familiar_P8_Bajada parent=BAJADA_TOTAL_500M priority=8 queue=PCQ_Familiar_Bajada comment="Prioridad 8 para Plan Familiar"

# -- Colas para Plan GAMER --
add name=PlanGamer_P1_Subida packet-mark=MP_Gamer_P1_Subida parent=SUBIDA_TOTAL_500M priority=1 queue=PCQ_Gamer_Subida comment="Prioridad 1 para Plan Gamer"
add name=PlanGamer_P2_Subida packet-mark=MP_Gamer_P2_Subida parent=SUBIDA_TOTAL_500M priority=2 queue=PCQ_Gamer_Subida comment="Prioridad 2 para Plan Gamer"
add name=PlanGamer_P3_Subida packet-mark=MP_Gamer_P3_Subida parent=SUBIDA_TOTAL_500M priority=3 queue=PCQ_Gamer_Subida comment="Prioridad 3 para Plan Gamer"
add name=PlanGamer_P4_Subida packet-mark=MP_Gamer_P4_Subida parent=SUBIDA_TOTAL_500M priority=4 queue=PCQ_Gamer_Subida comment="Prioridad 4 para Plan Gamer"
add name=PlanGamer_P5_Subida packet-mark=MP_Gamer_P5_Subida parent=SUBIDA_TOTAL_500M priority=5 queue=PCQ_Gamer_Subida comment="Prioridad 5 para Plan Gamer"
add name=PlanGamer_P6_Subida packet-mark=MP_Gamer_P6_Subida parent=SUBIDA_TOTAL_500M priority=6 queue=PCQ_Gamer_Subida comment="Prioridad 6 para Plan Gamer"
add name=PlanGamer_P7_Subida packet-mark=MP_Gamer_P7_Subida parent=SUBIDA_TOTAL_500M priority=7 queue=PCQ_Gamer_Subida comment="Prioridad 7 para Plan Gamer"
add name=PlanGamer_P8_Subida packet-mark=MP_Gamer_P8_Subida parent=SUBIDA_TOTAL_500M priority=8 queue=PCQ_Gamer_Subida comment="Prioridad 8 para Plan Gamer"
add name=PlanGamer_P1_Bajada packet-mark=MP_Gamer_P1_Bajada parent=BAJADA_TOTAL_500M priority=1 queue=PCQ_Gamer_Bajada comment="Prioridad 1 para Plan Gamer"
add name=PlanGamer_P2_Bajada packet-mark=MP_Gamer_P2_Bajada parent=BAJADA_TOTAL_500M priority=2 queue=PCQ_Gamer_Bajada comment="Prioridad 2 para Plan Gamer"
add name=PlanGamer_P3_Bajada packet-mark=MP_Gamer_P3_Bajada parent=BAJADA_TOTAL_500M priority=3 queue=PCQ_Gamer_Bajada comment="Prioridad 3 para Plan Gamer"
add name=PlanGamer_P4_Bajada packet-mark=MP_Gamer_P4_Bajada parent=BAJADA_TOTAL_500M priority=4 queue=PCQ_Gamer_Bajada comment="Prioridad 4 para Plan Gamer"
add name=PlanGamer_P5_Bajada packet-mark=MP_Gamer_P5_Bajada parent=BAJADA_TOTAL_500M priority=5 queue=PCQ_Gamer_Bajada comment="Prioridad 5 para Plan Gamer"
add name=PlanGamer_P6_Bajada packet-mark=MP_Gamer_P6_Bajada parent=BAJADA_TOTAL_500M priority=6 queue=PCQ_Gamer_Bajada comment="Prioridad 6 para Plan Gamer"
add name=PlanGamer_P7_Bajada packet-mark=MP_Gamer_P7_Bajada parent=BAJADA_TOTAL_500M priority=7 queue=PCQ_Gamer_Bajada comment="Prioridad 7 para Plan Gamer"
add name=PlanGamer_P8_Bajada packet-mark=MP_Gamer_P8_Bajada parent=BAJADA_TOTAL_500M priority=8 queue=PCQ_Gamer_Bajada comment="Prioridad 8 para Plan Gamer"


# 2.5 - Implementar las Reglas de Mangle (Version 8 Prioridades - Emulando DiffServ8)
/ip firewall mangle
# -- FASE 1: Marcar Conexion por Servicio (Generico basado en 8 clases) --
# Primero se marca la conexion completa para clasificar el tipo de trafico.
# Las reglas se leen de arriba hacia abajo. La primera que coincide, marca y continua (passthrough=yes).
add action=mark-connection chain=prerouting comment="[QoS] P1: Control de Red (DSCP 48-63)" connection-mark=no-mark dscp=48-63 new-connection-mark=MC_P1_NetworkControl passthrough=yes
add action=mark-connection chain=prerouting comment="[QoS] P1: Control de Red (ICMP)" connection-mark=no-mark new-connection-mark=MC_P1_NetworkControl passthrough=yes protocol=icmp
add action=mark-connection chain=prerouting comment="[QoS] P2: Telefonia (DSCP 46)" connection-mark=no-mark dscp=46 new-connection-mark=MC_P2_Telephony passthrough=yes
add action=mark-connection chain=prerouting comment="[QoS] P2: Telefonia (Puertos VoIP)" connection-mark=no-mark new-connection-mark=MC_P2_Telephony passthrough=yes port=5060,5061,10000-20000 protocol=udp
add action=mark-connection chain=prerouting comment="[QoS] P3: Senalizacion (DSCP 32-47)" connection-mark=no-mark dscp=32-47 new-connection-mark=MC_P3_Signaling passthrough=yes
add action=mark-connection chain=prerouting comment="[QoS] P4: Conferencia Multimedia (DSCP 24-31)" connection-mark=no-mark dscp=24-31 new-connection-mark=MC_P4_MultimediaConf passthrough=yes
add action=mark-connection chain=prerouting comment="[QoS] P5: Interactivo Tiempo Real (DSCP 40)" connection-mark=no-mark dscp=40 new-connection-mark=MC_P5_RealTime passthrough=yes
add action=mark-connection chain=prerouting comment="[QoS] P5: Interactivo Tiempo Real (Puertos de Juegos)" connection-mark=no-mark new-connection-mark=MC_P5_RealTime passthrough=yes port=7000-9000,27000-27050,30000-30050 protocol=udp
add action=mark-connection chain=prerouting comment="[QoS] P6: Streaming Multimedia (DSCP 16-23)" connection-mark=no-mark dscp=16-23 new-connection-mark=MC_P6_Streaming passthrough=yes
add action=mark-connection chain=prerouting comment="[QoS] P6: Streaming Multimedia (Puertos Web/Video)" connection-mark=no-mark new-connection-mark=MC_P6_Streaming passthrough=yes port=80,443 protocol=tcp
add action=mark-connection chain=prerouting comment="[QoS] P7: Datos de Baja Latencia (DSCP 8-15)" connection-mark=no-mark dscp=8-15 new-connection-mark=MC_P7_LowLatencyData passthrough=yes
add action=mark-connection chain=prerouting comment="[QoS] P7: Datos de Baja Latencia (DNS)" connection-mark=no-mark new-connection-mark=MC_P7_LowLatencyData passthrough=yes port=53 protocol=udp
add action=mark-connection chain=prerouting comment="[QoS] P8: Mejor Esfuerzo (Todo lo demas)" connection-mark=no-mark new-connection-mark=MC_P8_BestEffort passthrough=yes

# === FASE 2: Marcar Paquetes de Subida (en PREROUTING) ===
# Leemos la marca de conexion (MC_P_...) que pusimos en Fase 1.
# Segun la IP de origen del cliente, le ponemos la marca final al paquete (MP_...).
# Usamos passthrough=no porque una vez que un paquete es marcado, no necesitamos que siga buscando coincidencias.
# --- Plan BASICO ---
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P1 Subida" connection-mark=MC_P1_NetworkControl in-interface-list=LANs new-packet-mark=MP_Basico_P1_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P2 Subida" connection-mark=MC_P2_Telephony in-interface-list=LANs new-packet-mark=MP_Basico_P2_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P3 Subida" connection-mark=MC_P3_Signaling in-interface-list=LANs new-packet-mark=MP_Basico_P3_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P4 Subida" connection-mark=MC_P4_MultimediaConf in-interface-list=LANs new-packet-mark=MP_Basico_P4_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P5 Subida" connection-mark=MC_P5_RealTime in-interface-list=LANs new-packet-mark=MP_Basico_P5_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P6 Subida" connection-mark=MC_P6_Streaming in-interface-list=LANs new-packet-mark=MP_Basico_P6_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P7 Subida" connection-mark=MC_P7_LowLatencyData in-interface-list=LANs new-packet-mark=MP_Basico_P7_Subida passthrough=no src-address-list=Plan_Basico
add action=mark-packet chain=prerouting comment="[QoS] MP: Basico P8 Subida" connection-mark=MC_P8_BestEffort in-interface-list=LANs new-packet-mark=MP_Basico_P8_Subida passthrough=no src-address-list=Plan_Basico
# --- Plan FAMILIAR ---
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P1 Subida" connection-mark=MC_P1_NetworkControl in-interface-list=LANs new-packet-mark=MP_Familiar_P1_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P2 Subida" connection-mark=MC_P2_Telephony in-interface-list=LANs new-packet-mark=MP_Familiar_P2_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P3 Subida" connection-mark=MC_P3_Signaling in-interface-list=LANs new-packet-mark=MP_Familiar_P3_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P4 Subida" connection-mark=MC_P4_MultimediaConf in-interface-list=LANs new-packet-mark=MP_Familiar_P4_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P5 Subida" connection-mark=MC_P5_RealTime in-interface-list=LANs new-packet-mark=MP_Familiar_P5_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P6 Subida" connection-mark=MC_P6_Streaming in-interface-list=LANs new-packet-mark=MP_Familiar_P6_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P7 Subida" connection-mark=MC_P7_LowLatencyData in-interface-list=LANs new-packet-mark=MP_Familiar_P7_Subida passthrough=no src-address-list=Plan_Familiar
add action=mark-packet chain=prerouting comment="[QoS] MP: Familiar P8 Subida" connection-mark=MC_P8_BestEffort in-interface-list=LANs new-packet-mark=MP_Familiar_P8_Subida passthrough=no src-address-list=Plan_Familiar
# --- Plan GAMER ---
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P1 Subida" connection-mark=MC_P1_NetworkControl in-interface-list=LANs new-packet-mark=MP_Gamer_P1_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P2 Subida" connection-mark=MC_P2_Telephony in-interface-list=LANs new-packet-mark=MP_Gamer_P2_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P3 Subida" connection-mark=MC_P3_Signaling in-interface-list=LANs new-packet-mark=MP_Gamer_P3_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P4 Subida" connection-mark=MC_P4_MultimediaConf in-interface-list=LANs new-packet-mark=MP_Gamer_P4_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P5 Subida" connection-mark=MC_P5_RealTime in-interface-list=LANs new-packet-mark=MP_Gamer_P5_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P6 Subida" connection-mark=MC_P6_Streaming in-interface-list=LANs new-packet-mark=MP_Gamer_P6_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P7 Subida" connection-mark=MC_P7_LowLatencyData in-interface-list=LANs new-packet-mark=MP_Gamer_P7_Subida passthrough=no src-address-list=Plan_Gamer
add action=mark-packet chain=prerouting comment="[QoS] MP: Gamer P8 Subida" connection-mark=MC_P8_BestEffort in-interface-list=LANs new-packet-mark=MP_Gamer_P8_Subida passthrough=no src-address-list=Plan_Gamer

# === FASE 3: Marcar Paquetes de Bajada (en FORWARD) ===
# El proceso es identico al de subida, pero para el trafico que entra por la WAN y va hacia un cliente.
# --- Plan BASICO ---
add action=mark-packet chain=forward comment="[QoS] MP: Basico P1 Bajada" connection-mark=MC_P1_NetworkControl in-interface-list=WANs new-packet-mark=MP_Basico_P1_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P2 Bajada" connection-mark=MC_P2_Telephony in-interface-list=WANs new-packet-mark=MP_Basico_P2_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P3 Bajada" connection-mark=MC_P3_Signaling in-interface-list=WANs new-packet-mark=MP_Basico_P3_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P4 Bajada" connection-mark=MC_P4_MultimediaConf in-interface-list=WANs new-packet-mark=MP_Basico_P4_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P5 Bajada" connection-mark=MC_P5_RealTime in-interface-list=WANs new-packet-mark=MP_Basico_P5_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P6 Bajada" connection-mark=MC_P6_Streaming in-interface-list=WANs new-packet-mark=MP_Basico_P6_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P7 Bajada" connection-mark=MC_P7_LowLatencyData in-interface-list=WANs new-packet-mark=MP_Basico_P7_Bajada passthrough=no dst-address-list=Plan_Basico
add action=mark-packet chain=forward comment="[QoS] MP: Basico P8 Bajada" connection-mark=MC_P8_BestEffort in-interface-list=WANs new-packet-mark=MP_Basico_P8_Bajada passthrough=no dst-address-list=Plan_Basico
# --- Plan FAMILIAR ---
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P1 Bajada" connection-mark=MC_P1_NetworkControl in-interface-list=WANs new-packet-mark=MP_Familiar_P1_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P2 Bajada" connection-mark=MC_P2_Telephony in-interface-list=WANs new-packet-mark=MP_Familiar_P2_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P3 Bajada" connection-mark=MC_P3_Signaling in-interface-list=WANs new-packet-mark=MP_Familiar_P3_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P4 Bajada" connection-mark=MC_P4_MultimediaConf in-interface-list=WANs new-packet-mark=MP_Familiar_P4_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P5 Bajada" connection-mark=MC_P5_RealTime in-interface-list=WANs new-packet-mark=MP_Familiar_P5_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P6 Bajada" connection-mark=MC_P6_Streaming in-interface-list=WANs new-packet-mark=MP_Familiar_P6_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P7 Bajada" connection-mark=MC_P7_LowLatencyData in-interface-list=WANs new-packet-mark=MP_Familiar_P7_Bajada passthrough=no dst-address-list=Plan_Familiar
add action=mark-packet chain=forward comment="[QoS] MP: Familiar P8 Bajada" connection-mark=MC_P8_BestEffort in-interface-list=WANs new-packet-mark=MP_Familiar_P8_Bajada passthrough=no dst-address-list=Plan_Familiar
# --- Plan GAMER ---
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P1 Bajada" connection-mark=MC_P1_NetworkControl in-interface-list=WANs new-packet-mark=MP_Gamer_P1_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P2 Bajada" connection-mark=MC_P2_Telephony in-interface-list=WANs new-packet-mark=MP_Gamer_P2_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P3 Bajada" connection-mark=MC_P3_Signaling in-interface-list=WANs new-packet-mark=MP_Gamer_P3_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P4 Bajada" connection-mark=MC_P4_MultimediaConf in-interface-list=WANs new-packet-mark=MP_Gamer_P4_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P5 Bajada" connection-mark=MC_P5_RealTime in-interface-list=WANs new-packet-mark=MP_Gamer_P5_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P6 Bajada" connection-mark=MC_P6_Streaming in-interface-list=WANs new-packet-mark=MP_Gamer_P6_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P7 Bajada" connection-mark=MC_P7_LowLatencyData in-interface-list=WANs new-packet-mark=MP_Gamer_P7_Bajada passthrough=no dst-address-list=Plan_Gamer
add action=mark-packet chain=forward comment="[QoS] MP: Gamer P8 Bajada" connection-mark=MC_P8_BestEffort in-interface-list=WANs new-packet-mark=MP_Gamer_P8_Bajada passthrough=no dst-address-list=Plan_Gamer


