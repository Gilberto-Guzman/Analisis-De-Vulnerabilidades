**1. Introducción a la recolección de información:**
La fase de recolección de información es crucial en el proceso de pruebas de seguridad. Implica recopilar datos sobre la infraestructura, tecnologías utilizadas, servicios expuestos y posibles puntos débiles en un sistema o aplicación.

**2. Identificación de la infraestructura:**
Este paso se enfoca en descubrir los activos de una organización, como servidores, dominios, direcciones IP, subdominios, etc. Esto ayuda a tener un panorama completo de la superficie de ataque.

**3. Fingerprint de aplicaciones y frameworks:**
El fingerprinting se refiere a la identificación de las tecnologías utilizadas en una aplicación o sitio web, como el tipo de servidor web, lenguaje de programación, framework y versiones. Esto es útil para encontrar posibles vulnerabilidades específicas.

**4. Fingerprint de aplicaciones a medida:**
Incluso las aplicaciones personalizadas pueden ser identificadas a través de señales como encabezados HTTP, patrones de URLs y comportamientos distintivos.

**5. Enumeración de recursos:**
En esta etapa, se identifican y enumeran los recursos disponibles en una aplicación o sitio web, como páginas, directorios, archivos y servicios, lo que puede ayudar a identificar posibles puntos de entrada para ataques.

**6. Exposición de información por malas configuraciones:**
Las malas configuraciones en servidores, bases de datos o aplicaciones pueden exponer información sensible, como contraseñas o datos internos, a posibles atacantes.

**7. Google Hacking:**
Google Hacking es el uso de operadores de búsqueda avanzada en el motor de búsqueda Google para encontrar información sensible o vulnerabilidades en sitios web y aplicaciones.

**8. Shodan HQ:**
Shodan es un motor de búsqueda que permite encontrar dispositivos conectados a Internet, como servidores, cámaras y otros dispositivos IoT. Puede usarse para buscar sistemas con configuraciones inseguras.

**9. Conceptos del protocolo HTTP y HTTPS:**
HTTP (Hypertext Transfer Protocol) es el protocolo utilizado para la transferencia de datos en la web. HTTPS es una versión segura de HTTP que utiliza cifrado SSL/TLS para proteger la comunicación.

**10. Codificación (Charset, HTML encoding, URL encoding):**
La codificación adecuada de datos es crucial para evitar vulnerabilidades, como la inyección de código malicioso. HTML encoding y URL encoding son métodos para convertir caracteres especiales en representaciones seguras.

**11. Same Origin Policy:**
Esta política de seguridad en los navegadores web restringe la interacción entre diferentes orígenes (dominios) para prevenir ataques de Cross-Site Scripting (XSS) y otros.

**12. Cookies y Sesiones:**
Las cookies son pequeños archivos almacenados en el navegador para mantener información entre peticiones. Las sesiones son mecanismos para mantener el estado del usuario en una aplicación.

**13. Webapp proxies:**
Las herramientas de proxy permiten observar y manipular las comunicaciones entre el navegador y el servidor, lo que es útil para analizar y entender las interacciones.

**14. Conceptos básicos del XSS (Cross Site Scripting):**
XSS es una vulnerabilidad que permite a un atacante inyectar código malicioso en una aplicación web, que luego es ejecutado en el navegador de otros usuarios.

**15. Anatomía de una explotación vía Cross Site Scripting:**
Un ataque XSS típico implica la inyección de código en una aplicación web, que luego es ejecutado en el navegador de la víctima.

**16. Los 3 tipos de XSS:**

- Reflejado (Reflected): El payload malicioso se refleja inmediatamente en la respuesta del servidor.
- Almacenado (Stored): El payload se almacena en el servidor y se muestra a múltiples usuarios.
- DOM-based: El payload afecta al DOM (modelo de objetos del documento) en el lado del cliente.

**17. Cómo encontrar/identificar la vulnerabilidad XSS:**
Los testers de seguridad pueden utilizar diversas técnicas, como la inyección de scripts de prueba, para identificar posibles puntos de entrada XSS.

**18. Explotación vía XSS:**
Un atacante puede robar cookies de sesión, redirigir a usuarios a sitios maliciosos o realizar acciones en nombre del usuario explotando una vulnerabilidad XSS.

**19. Cómo mitigar el riesgo de XSS:**
La mitigación del riesgo de XSS implica validar y escapar adecuadamente los datos de entrada, implementar políticas de seguridad de contenido y utilizar encabezados HTTP adecuados.

**20. Introducción a las inyecciones SQL:**
Las inyecciones SQL son vulnerabilidades que ocurren cuando los datos ingresados por el usuario se insertan directamente en consultas SQL sin ser validados correctamente.

**21. Cómo encontrar este tipo de vulnerabilidades:**
Pruebas exhaustivas de entrada pueden revelar puntos débiles en una aplicación donde los datos no están siendo correctamente validados antes de ser utilizados en consultas SQL.

**22. In-Band SQL Injections:**
En estos ataques, los resultados de la inyección se obtienen directamente a través del mismo canal de comunicación que se utilizó para el ataque.

**23. Error Based SQL Injections:**
Los errores generados por la base de datos en respuesta a consultas maliciosas son explotados para obtener información sobre la estructura de la base de datos.

**24. Blind SQL Injections:**
En este caso, el atacante no recibe directamente los resultados de la inyección, pero puede inferir información a través de preguntas binarias (verdadero/falso) al sistema.

**25. Introducción a SQL map:**
SQLMap es una herramienta automatizada para explotar y detectar vulnerabilidades de inyección SQL en aplicaciones web.

**26. Estrategias de mitigación:**
La mitigación de inyecciones SQL implica el uso de consultas parametrizadas o el uso de ORM (mapeo objeto-relacional), que evitan la inyección al separar los datos de las consultas.

**27. Explotación avanzada a través de SQL Injections:**
Un atacante puede usar inyecciones SQL para obtener acceso no autorizado, robar información confidencial o incluso manipular la base de datos.

**28. Ataques de sesión:**
Estos ataques buscan explotar debilidades en la gestión de sesiones, permitiendo a un atacante asumir la identidad de otro usuario.

**29. CSRF (Cross-Site Request Forgery):**
CSRF es un ataque en el que un atacante hace que un usuario realice acciones no deseadas en una aplicación sin su conocimiento o consentimiento.

**30. Ataques a través de archivos y recursos:**
Los atacantes pueden aprovechar archivos y recursos maliciosos o inseguros para comprometer la seguridad de una aplicación, como los ataques de inclusión de archivos.
