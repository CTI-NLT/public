<!-- 0 Patch Tuesday April 2022 -->

---
# Microsoft Patch Tuesday - Abril 2022 

<!-- https://echo.notable.app/1f2867487175d2d557fe96fab52506dc61e1bc276078304d64e02aa1a0393824 -->


### Microsoft ha publicado más de 100 correcciones de seguridad para software que resuelven problemas críticos, incluidos dos zero-days.
Solucionó problemas que incluyen numerosos errores de ejecución remota de código (RCE), problemas de elevación de privilegios (EoP), DOS, fugas de información y suplantación de identidad.

### Prioridad Crítica
- CVE-2022-26809 - Vulnerabilidad de ejecución remota de código en el protocolo Remote Procedure Call.
- CVE-2022-24521 - **[Zero-day]** Vulnerabilidad de elevación de privilegios en _Common Log File System Driver_ de Windows.
- CVE-2022-26904 - **[Zero-day]** Vulnerabilidad de elevación de privilegios del servicio de perfil de usuario de Windows.

### Prioridad Importante
- CVE-2022-23259 - Vulnerabilidad de ejecución remota de código en Microsoft Dynamics 365 (on premise)
- CVE-2022-22008 - Vulnerabilidad de ejecución remota de código en la herramienta Windows Hyper-V.
- CVE-2022-23257 - Vulnerabilidad de ejecución remota de código en la herramienta Windows Hyper-V.
- CVE-2022-24537 - Vulnerabilidad de ejecución remota de código en la herramienta Windows Hyper-V.
- CVE-2022-26919 - Vulnerabilidad de ejecución remota de código en Windows LDAP.
- CVE-2022-24491 - Vulnerabilidad de ejecución remota de código en Windows Network File System.
- CVE-2022-24497 - Vulnerabilidad de ejecución remota de código en Windows Network File System.
- CVE-2022-24541 - Vulnerabilidad de ejecución remota de código en Windows Server Service.
- CVE-2022-24500 - Vulnerabilidad de ejecución remota de código en el protocolo Windows SMB.

<!-- 1 26809 ->
<!-- https://echo.notable.app/1b215af21a70ed19b4ed0a2a340f6a140598bf8973b2dd370b684091da358e1f -->
<details closed>
<summary>Detalle CVE-2022-26809</summary>

## CVE-2022-26809 - CVSS 9.8 - RCE
### Vulnerabilidad de ejecución remota de código en el protocolo Remote Procedure Call.

### Impacto
Atacantes podrían explotar la vulnerabilidad de forma remota, manejando un nivel bajo de complejidad, sin autenticación/privilegio o acceso previo, ni tampoco nececidad de interacción con usuarios. Pudiendo resultar a la victima pérdida total de confidencialidad, integridad y/o disponibilidad.

### Explotación probable
El análisis de Microsoft ha demostrado que el código de explotación podría crearse de tal manera que un atacante pudiera aprovechar esta vulnerabilidad de manera constante. Además, Microsoft está al tanto de instancias pasadas de este tipo de vulnerabilidad explotada. Esto lo convertiría en un objetivo atractivo para los atacantes y, por lo tanto, sería más probable que se pudieran crear exploits. Como tal, los clientes que revisaron la actualización de seguridad y determinaron su aplicabilidad dentro de su entorno deben tratar esto con mayor prioridad.

### Descripción
Para aprovechar esta vulnerabilidad, un atacante necesitaría enviar una llamada RPC especialmente diseñada a un host RPC. Esto podría resultar en la ejecución remota de código en el lado del servidor con los mismos permisos que el servicio RPC.
Para los servicios de red más populares (SMTP, DNS, HTTP...), utilizamos puertos asignados específicamente y gestionados por IANA [iana]. Pero hay muchos servicios menos comunes específicos para ciertos sistemas operativos que no tienen puertos asignados por IANA. Para estos servicios, el mecanismo de "llamada a procedimiento remoto" (RPC) puede usarse para estandarizar la comunicación. Tanto Windows como Unix implementan su propia versión de esta idea básica. En Unix, normalmente se denomina "SUN RPC" (RFC 1057). En Windows, tenemos MSRPC.

MSRPC permite que los mensajes se transmitan de varias maneras diferentes:
* SMB (Puerto 445 TCP o puerto 139) es probablemente el mecanismo más común. Los comandos a través de SMB se envían como escrituras de canalización con nombre que luego se pasan al servicio respectivo.
* Vía TCP (Puerto 135 TCP y puerto alto): Este mecanismo es similar a SUN RPC. El cliente primero se conectará a un mapeador de puntos finales (Puerto 135 para MSRPC, Puerto 111 para SUN RPC). El asignador de puntos finales devolverá el número de puerto que utiliza el servicio. Verá una segunda conexión TCP al puerto alto que transmite el mensaje RPC.
* A través de HTTP (puerto predeterminado 593). Esto es particularmente útil si RPC está expuesto a través de Internet. TLS se puede utilizar para el cifrado y HTTP puede proporcionar opciones de autenticación adicionales. También se puede utilizar el puerto 80/443.

### Producto(s) Afectado(s)
_Microsoft Windows_

### Mitigación
1. Aplicación de parches.
   - [Parches relacionados](https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2022-26809) <br>
2. Bloquear puerto TCP 445 en firewalls perimetrales.
    - <small>El puerto TCP 445 se utiliza para iniciar una conexión con el componente afectado. El bloqueo de este puerto en el firewall del perímetro de la red ayudará a proteger los sistemas que están detrás de ese firewall de los intentos de aprovechar esta vulnerabilidad. Esto puede ayudar a proteger las redes de ataques que se originan fuera del perímetro de la empresa. El bloqueo de los puertos afectados en el perímetro de la empresa es la mejor defensa para ayudar a evitar ataques desde Internet. Sin embargo, los sistemas aún podrían ser vulnerables a los ataques dentro del perímetro de su empresa.</small>
    <br>
3. Siga las pautas de Microsoft para proteger el tráfico SMB
    - [Tráfico SMB seguro en Windows Server.](https://docs.microsoft.com/windows-server/storage/file-server/smb-secure-traffic) <br>

4. Con el siguiente comando de __Nmap__ se puede enumerar los servicios RPC, y ayudar a identificar los sistemas expuestos:
   - <kbd>nmap _*IP-targets*_ --script=msrpc-enum</kbd>

### Enlaces relacionados
- https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2022-26809
- https://isc.sans.edu/forums/diary/An+Update+on+CVE202226809+MSRPC+Vulnerabliity+PATCH+NOW/28550/
- https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-26809
<br><br>
</details>




## CVE-2022-24521 - CVSS 7.8 - EoP
> ### Impacto
> Atacantes podrían explotar la vulnerabilidad de forma local, por dos caminos: accediendo al sistema de destino fisicamente (teclado, consola) o de forma remota (SSH), o sea, necesita autenticación/privilegio previo;
o interactuando con usuarios y logrando que executen las acciones necesarias para la obtener nivel de privilegios necesarios (por ejemplo, engañar a un usuario legítimo para que abra un documento malicioso).
Después de robo de credenciales o infiltración, el atacante necesita manejar bajo nivel de complejidad para executar la explotación, pudiendo resultar a la victima pérdida total de confidencialidad, integridad y/o disponibilidad.

### Explotación detectada
> Microsoft tiene conocimiento de que se está explotando una instancia de esta vulnerabilidad. Como tal, los clientes que revisaron la actualización de seguridad y determinaron su aplicabilidad dentro de su entorno deben tratar esto con la máxima prioridad.

### Descripción
> La brecha reside en _Common Log File System (CLFS)__ de Windows, y no requiere ninguna interacción humana para ser explotado. A pesar de que fue calificada como “importante y no critica”, la vulnerabilidad sigue siendo peligrosa, ya que su complejidad de ataque ha sido calificada como "baja". 
Fallas de EoP como esta se aprovechan después de la autenticación, después de que un atacante haya accedido con éxito a un sistema vulnerable, para obtener permisos más altos. 

### Producto(s) Afectado(s)
> _Microsoft Windows_

### Mitigación
> 1. Aplicación de parches.
>    - [Parches relacionados](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2022-2452) <br>

### Enlaces relacionados
> - https://msrc.microsoft.com/update-guide/vulnerability/CVE-2022-24521
> - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-24521
<br><br>

---
<!-- 26904 -->
## CVE-2022-26904 - CVSS 7 - EoP
<!-- https://echo.notable.app/ccba180a05108a8036632beeb7f8db86566e100b744fabc4ec5d05be77db380f -->

### Vulnerabilidad de elevación de privilegios en _User Profile Service_ de Windows

### Impacto
Atacantes podrían explotar la vulnerabilidad de forma local, por dos caminos: accediendo al sistema de destino fisicamente (teclado, consola) o de forma remota (SSH), o sea, necesita autenticación/privilegio previo;
o interactuando con usuarios y logrando que executen las acciones necesarias para la obtener nivel de privilegios necesarios (por ejemplo, engañar a un usuario legítimo para que abra un documento malicioso).
Además, un ataque exitoso depende de condiciones fuera del control del atacante y requiere una cantidad considerable de esfuerzo en la preparación y/o ejecución contra el componente vulnerable.
Pudiendo resultar a la victima pérdida total de confidencialidad, integridad y/o disponibilidad si efectivamente explotada.

### Explotación probable
El análisis de Microsoft ha demostrado que el código de explotación podría crearse de tal manera que un atacante pudiera aprovechar esta vulnerabilidad de manera constante. Además, Microsoft está al tanto de instancias pasadas de este tipo de vulnerabilidad explotada. Esto lo convertiría en un objetivo atractivo para los atacantes y, por lo tanto, sería más probable que se pudieran crear exploits. Como tal, los clientes que revisaron la actualización de seguridad y determinaron su aplicabilidad dentro de su entorno deben tratar esto con mayor prioridad.

### Descripción
La vulnerabilidad permite que un usuario local aumente los privilegios en el sistema. La vulnerabilidad existe debido a un error de límite en _User Profile Service_ de Windows. Un usuario local puede ejecutar código arbitrario con privilegios elevados explorando un _race condition_ en el servicio.

Un ataque de condición de carrera ocurre cuando un sistema informático que está diseñado para manejar tareas en una secuencia específica se ve obligado a realizar dos o más operaciones simultáneamente. Esta técnica aprovecha un intervalo de tiempo entre el momento en que se inicia un servicio y el momento en que entra en vigor un control de seguridad.

### Producto(s) Afectado(s)
 _Microsoft Windows_

### Mitigación
1. Aplicación de parches.
    - [Lista de parches](https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2022-26904) <br>
2. Considerar siguientes IoCs
    - [5.39.30.0/24](https://vuldb.com/?ctiid.197118) <br>

### Enlaces relacionados
 - https://msrc.microsoft.com/update-guide/en-US/vulnerability/CVE-2022-26904
 - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-26904
 - https://www.cybersecurity-help.cz/vdb/SB2022041304
 - https://vuldb.com/?ctiid.197118
<!-- /26904 -->
---

## CVE-2022-23259 - CVSS 8.8
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26904 - CVSS 7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-22008 - CVSS 7.7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26904 - CVSS 7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-23257 - CVSS 8.6
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26904 - CVSS 7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-24537 - CVSS 7.7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26904 - CVSS 7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26919 - CVSS 8.1
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26904 - CVSS 7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-24491 - CVSS 9.8
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26904 - CVSS 7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-24497 - CVSS 9.8
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26904 - CVSS 7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-24541 - CVSS 8.8
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26904 - CVSS 7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-24500 - CVSS 8.8
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---

## CVE-2022-26904 - CVSS 7
> ### Impacto

### Descripción
> 

### Producto(s) Afectado(s)
>

### Mitigación
> 1. 

### Enlaces relacionados
> - 
---





