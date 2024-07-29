#!/bin/bash

# Función para analizar logs del servidor web
analizar_logs() {
    echo "Analizando logs del servidor web..."
    LOG_FILE="/tmp/vargas_adriana/access.log"

    # Extraer direcciones IP más activas
    echo "IPs más activas:"
    awk '{print $1}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5

    # Extraer rutas más visitadas
    echo "Rutas más visitadas:"
    awk '{print $7}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5
}

# Función para generar informes
generar_informes() {
    echo "Generando informes de acceso al servidor web..."

    LOG_FILE="/tmp/vargas_adriana/access.log"

    # Informe: Navegadores más utilizados
    echo "Navegadores más utilizados:"
    awk -F\" '{print $7}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5

    # Informe: Códigos de respuesta HTTP más frecuentes
    echo "Códigos de respuesta HTTP más frecuentes:"
    awk '{print $5}' $LOG_FILE | sort | uniq -c | sort -nr

    # Informe: Hits por día de la semana
    echo "Hits por día de la semana:"
    awk '{print $4}' $LOG_FILE | cut -d'[' -f2 | cut -d':' -f1 | sort | uniq -c | sort -nr

    # Informe: Hits por hora del día
    echo "Hits por hora del día:"
    awk '{print $4}' $LOG_FILE | cut -d':' -f2 | sort | uniq -c | sort -n

    # Informe: Tamaños de respuesta más comunes
    echo "Tamaños de respuesta más comunes:"
    awk '{print $10}' $LOG_FILE | sort -n | uniq -c | sort -nr | head -n 5

    # Informe: Clientes que generan más tráfico
    echo "Clientes que generan más tráfico:"
    awk '{print $1}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5

    # Informe: Métodos HTTP más utilizados
    echo "Métodos HTTP más utilizados:"
    awk '{print $6}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5

    # Informe: Códigos de estado de respuesta más frecuentes
    echo "Códigos de estado de respuesta más frecuentes:"
    awk '{print $9}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5

    # Informe: Tamaños de archivo más solicitados
    echo "Tamaños de archivo más solicitados:"
    awk '{print $10}' $LOG_FILE | sort -n | uniq -c | sort -nr | head -n 5

    # Informe: Referers más comunes
    echo "Referers más comunes:"
    awk -F\" '{print $4}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5

    # Informe: Códigos de respuesta de error más frecuentes
    echo "Códigos de respuesta de error más frecuentes:"
    awk '$9 >= 400 {print $9}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5

    # Informe: Hosts más visitados
    echo "Hosts más visitados:"
    awk '{print $8}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5

    # Informe: Protocolos utilizados
    echo "Protocolos utilizados:"
    awk '{print $12}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5

    # Informe: Códigos de respuesta redireccionados más frecuentes
    echo "Códigos de respuesta redireccionados más frecuentes:"
    awk '$9 >= 300 && $9 < 400 {print $9}' $LOG_FILE | sort | uniq -c | sort -nr | head -n 5
}

# Función para monitorear acceso en tiempo real
monitorear_acceso() {
    echo "Monitoreando acceso en tiempo real con htop..."
    if command -v htop >/dev/null 2>&1; then
        htop
    else
        echo "htop no está instalado. Usando top en su lugar."
        top
    fi
}

# Función para bloquear IPs maliciosas
bloquear_ips() {
    echo "Buscando IPs maliciosas y bloqueando con iptables..."

    # Ruta al archivo de log
    LOG_FILE="/tmp/vargas_adriana/access.log"

    # Encontrar IPs con más de 100 solicitudes en el archivo de log (ajustar según sea necesario)
    malicious_ips=$(awk '{print $1}' "$LOG_FILE" | sort | uniq -c | awk '$1 > 100 {print $2}')

    # Bloquear cada IP maliciosa con iptables y mostrar país
    for ip in $malicious_ips; do
        echo "Bloqueando IP: $ip"

        # Obtener información del país para la IP
        country=$(curl -s ipinfo.io/$ip/country)
        echo "IP $ip pertenece al país: $country"

        # Bloquear IP con iptables
        sudo iptables -A INPUT -s "$ip" -j DROP
    done
}

# Función principal del script
main() {
    clear
    echo "Bienvenido al sistema de análisis y generación de informes de acceso a un servidor web."
    echo "----------------------------------------------"

    analizar_logs
    generar_informes
    monitorear_acceso
    bloquear_ips

    echo "----------------------------------------------"
    echo "Fin del script."
}

# Llamar a la función principal
main
