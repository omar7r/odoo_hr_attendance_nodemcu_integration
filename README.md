# Integración entre el módulo hr_attendance de Odoo 8.0 y un NodeMCU con un lector RFID RC522

## nodemcu_rfid_wifimanager2: Proyecto Arduino para NodeMCU
- Dispone de un portal de configuración sobre una WIFI local donde se pone el host, usuario, contraseña, base de datos y puerto de Odoo y además se configura la wifi de acceso a internet.
- La wifi local se mostrará la primera vez y cada vez que se le de al botón de RESET.
- La wifi local de llama "AutoConnectAP" y tiene como contraseña "12345678"
- Si se conecta desde un móvil (la web es responsive), de forma automática se accederá al portal de configuración sino se accedería en http//192.168.4.1
- Si ya le configuramos una wifi se conectará de forma automática a esta al encenderse y en breve comenzaría a leer tarjetas RFID.
- Se debe de crear un cuenta en CloudMQTT y configurarla en el código, esta cuenta debe de tener dos tópicos: retorno y acesso asociado a un usuario el cual hay que configurar en el código junto a su contraseña.

- Al pasar una tarjeta RFID crea un mensaje MQTT con el identificador de la tarjeta
- Se queda a la espera de respuesta y si es válida ilumina un led verde y lanza un sonido y si no es correcta, enciende una led roja.

- EL botón RESET borra los credenciales del wifi para poder volver a conectarse a la wifi local.
- Los datos de configuración se guardan en el propio NodeMCU en un fichero con formato JSON.


## RFID_mqtt_client.py
- Script de Python que comprueba continuamnete si hay mensajes nuevos en CloudMQTT y en el caso de tenerlos se conecta a Odoo para registrar el acceso.
- Hace uso de un módulo que permite asociar RFIDs a empleados. El módulo se llama hr_attendance_rfid y se puede encontrar en la carpeta project_addons de https://github.com/Comunitea/cmnt_odoo
- Este módulo añade también un modelo de Log del que hace uso este script.
- Si encuentra la tarjeta RFID asociada a algún empleado, hace los correspondiente, marcar entrada o marcar salida y deja registro de la fecha y hora en la que se hizo.
- En el caso de no encontrar la tarjeta añade un registro al log informando del id de la tarjeta y de que no fue encontrado, esto nos puede servir para asociar por primera vez los ids a los empleados.
