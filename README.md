# AWK
Comando de AWK
### Proyecto: Análisis y Generación de Informes de Acceso a un Servidor Web

#### Objetivo:
Desarrollar un script en bash que analice los registros de acceso
(logs) de un servidor web, genere informes y realice acciones según
ciertos criterios utilizando AWK y otros comandos de Linux.

#### Funcionalidades del Script:

1. **Análisis de Logs:**
   - Procesar los registros de acceso (logs) del servidor web para
extraer información relevante como direcciones IP, rutas de acceso,
códigos de respuesta, etc.

2. **Generación de Informes:**
   - Generar informes sobre el tráfico del servidor web, incluyendo
estadísticas como las IPs más activas, las rutas más visitadas, etc.

3. **Monitoreo y Acciones Automatizadas:**
   - Monitorear en tiempo real el acceso al servidor web utilizando `htop`.
   - Automatizar acciones como bloquear direcciones IP que realicen
accesos maliciosos utilizando `iptables`.

4. **Usaremos este archivo para verificar logs
https://raw.githubusercontent.com/linuxacademy/content-elastic-log-samples/master/access.log
