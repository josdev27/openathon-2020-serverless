# Laboratorio 0
Aunque existe la posibilidad de usar una cuenta de aws de formación para realizar los distintos laboratorios, vamos a informar cómo es posible crear una cuenta en aws y realizar los distintos pasos para tener una cuenta propia y configurada para realizar las prácticas y así poder continuar una vez haya finalizado el Openathon.

## ¿Cómo se crea una cuenta en AWS?

Aunque en la [web de ayuda amazon](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/) podemos encontrar información sobre los pasos a seguir para crear una cuenta, vamos a dar detalle sobre los mismos e intentar ampliar esa información.

Para crear una cuenta, será necesario abrir la página principal de [amazon webservices](https://aws.amazon.com/). En ella se hará clic sobre el enlace 
Create an AWS account.
<p align="center">
    <img src="resources/crear_cuenta_aws_1.PNG">
</p>
Será necesario informar los siguientes valores de la nueva cuenta:
<p align="center">
    <img src="resources/crear_cuenta_aws_2.PNG">
</p>
En la siguiente ventana se completarán los datos de contacto.
<p align="center">
    <img src="resources/crear_cuenta_aws_3.PNG">
</p>
Lo siguiente será introducir los valores de pago. Hay que tener en cuenta que Amazon puede realizar un cobro de 1$ para verificar la tarjeta.
<p align="center">
    <img src="resources/crear_cuenta_aws_4.PNG">
</p>
A continuación, es necesario validar el número de teléfono. Se podrá hacer mediante llamada telefónica o sms. Este paso nos permite verificar la identidad.

Finalmente seleccionaremos un plan, el plan básico.
<p align="center">
    <img src="resources/crear_cuenta_aws_6.PNG">
</p>

## Creación usuario de desarrollo.
Lo primero que hay que hacer es logarnos con nuestra nueva cuenta de AWS.
<p align="center">
    <img src="resources/crear_usuario_aws_7.PNG">
</p>
A continuación, haremos clic en el menú services y teclearemos IAM, seleccionando el servicio Identity and Access Management (IAM).
<p align="center">
    <img src="resources/crear_usuario_aws_8.PNG">
</p>
Sobre el menú de la izquierda, haremos clic sobre Users. No debemos tener ningún usuario creado todavía, por lo que vamos a proceder a crear uno, para ello se hace clic sobre Add User.
En la siguiente ventana informamos el nombre del usuario, marcamos las checks de Programmatic access y AWS Management Console access. También marcamos la opción de establecer nuestra password. Una vez se haya completado hacemos clic sobre Next:Permissions.
<p align="center">
    <img src="resources/crear_usuario_aws_9.PNG">
</p>

Lo siguiente será asignar a este nuevo usuario a un grupo. Como no existe ningún grupo creado previamente clicamos sobre Create Group.
Como nombre de grupo introducimos developers. En las policies marcamos AdministratorAccess.
<p align="center">
    <img src="resources/crear_usuario_aws_10.PNG">
</p>
Con ello ya podemos hacer clic sobre Create group.
<p align="center">
    <img src="resources/crear_usuario_aws_11.PNG">
</p>
Podemos hacer clic sobre Next:Tags, donde podrán asignársele etiquetas.
<p align="center">
    <img src="resources/crear_usuario_aws_12.PNG">
</p>
Y Next:Review donde se mostrará un resumen de nuestro nuevo usuario:
<p align="center">
    <img src="resources/crear_usuario_aws_13.PNG">
</p>

Finalmente, el usuario se ha creado. Si queréis hacer uso del plugin de eclipse puede ser buena idea hacer clic sobre download.csv para configurar el usuario en eclipse.
<p align="center">
    <img src="resources/crear_usuario_aws_14.PNG">
</p>

Para poder logarnos ahora con nuestro nuevo usuario, podemos ir al usuario, en la pestaña Security Credentials tenemos una url con el acceso.
<p align="center">
    <img src="resources/crear_usuario_aws_15.PNG">
</p>
Al introducir esa url en el navegador, directamente tendremos nuestro id y username. Faltará introducir la password que seteamos al crear el usuario.
<p align="center">
    <img src="resources/crear_usuario_aws_16.PNG">
</p>
## Control de Gastos
Todos los servicios de los que vamos a hacer uso en esta Openathon están cubiertos en su totalidad por el free tier con unos límites de recursos gratuitos muy por encima de los esperados de un ejercicio como este.

Limites del Free Tier para los servicios usados:
    •	Api Gateway:  1 Million api calls per month (only first 12 months on free tier)
    •	Lambda: 1 Million free requests per month (up to 3.2 million  seconds of compute time per month)  (no time limit on free tier)
    •	DynamoDB: 25 Gb storage and 25 WCU and 25 RCU Capacity units. (no time limit on free tier)
    •	S3: 5 Gb storage, 20.000 get Requests 2.000 put requests (only first 12 months on free tier)

Para conocer el gasto que tenemos pendiente en nuestra cuenta y permitir recibir alertas sobre los mismos. Para ello nos logaremos como root en AWS y en el menú services escribiremos Billing.
<p align="center">
    <img src="resources/gastos_usuario_aws_17.PNG">
</p>
Localizaremos las secciones “Spend summary” y "Month-to-Date" y validaremos que son cero.

Adicionalmente, en el Menú Billing>Bills podemos ver un resumen de los servicios usados y su coste.
<p align="center">
    <img src="resources/gastos_usuario_aws_18.PNG">
</p>

Si tuviesemos algún coste, podemos localizarlo viendo el menú Cost Managment>Cost Explorer.
En él podremos determinar que servicio está activo y generando costes y proceder como correponda(si no es necesario es posible eliminar).
## Alarma de Gastos
Adicionalmente, y para una mayor tranquilidad Amazon posibilita la generación de alarmas de gastos. En este [enlace](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html) podemos encontrar detalle de como proceder.
Básicamente es necesario: 
•	Habilitar billing alerts.
•	Crear billing alarm. 

