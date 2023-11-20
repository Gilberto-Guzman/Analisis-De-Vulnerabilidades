# Configuración Inicial del Servidor
    
    sudo apt update
    sudo apt upgrade
    sudo ufw app list
    sudo ufw allow OpenSSH
    sudo ufw enable

    sudo apt install apache2
    sudo ufw app list
    sudo ufw allow in "Apache Full"
    sudo ufw status
    sudo apt install php-mbstring php-xml php-bcmath php-mysql php-intl php-curl unzip git

    sudo apt install mysql-server
    mysql -u root -p
    CREATE DATABASE trabajosunach_db CHARACTER SET utf8 COLLATE utf8_spanish_ci;
    SHOW DATABASES;
    ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'Eq_VdeRwx79e46i';
    FLUSH PRIVILEGES;
    EXIT;

    sudo apt install php libapache2-mod-php
    php -v

    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('sha384', 'composer-setup.php') === 'e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64d92e6563bbde02') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"
    mv composer.phar /usr/bin/composer
    composer

    cd /var/www/
    git clone https://github.com/Gilberto-Guzman/trabajosunach
    rm -rf html
    mv trabajosunach/ html
    cd html
    composer install
    cp .env.example .env
    php artisan key:generate
    sudo nano .env

    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=trabajosunach
    DB_USERNAME=root
    DB_PASSWORD=Eq_VdeRwx79e46i


    --- ??? ---
    MAIL_DRIVER=smtp
    MAIL_HOST=smtp.gmail.com
    MAIL_PORT=587
    MAIL_USERNAME=ejemplo@gmail.com
    MAIL_PASSWORD="ejemplo"
    MAIL_ENCRYPTION=tls
    MAIL_FROM_ADDRESS="ejemplo@gmail.com"
    MAIL_FROM_NAME="${APP_NAME}"
    MAIL_ENCRYPTION=ssl

    php artisan migrate

    sudo nano /etc/apache2/sites-available/000-default.conf

    <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/public
        <Directory />
            Options FollowSymLinks
            AllowOverride None
        </Directory>
        <Directory /var/www/html>
            AllowOverride All
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>

    sudo systemctl reload apache2

    sudo a2enmod rewrite
    systemctl restart apache2
    chown -R www-data:www-data /var/www/html
    sudo chown -R $USER:www-data storage
    sudo chown -R $USER:www-data bootstrap/cache
    chmod -R 775 storage
    chmod -R 775 bootstrap/cache

    php artisan storage:link
    php artisan optimize:clear
    sudo systemctl reload apache2
    php artisan route:cache
    php artisan view:cache
    php artisan db:seed
    
    sudo apt install npm
    npm install
    npm install vite
    npm audit fix --force
    npm run build

# Parches de Seguridad

    sudo apt install libapache2-mod-security2
    sudo a2enmod security2
    sudo nano /etc/modsecurity/modsecurity.conf  

    # -- Rule engine initialization ----------------------------------------------

    # Enable ModSecurity, attaching it to every transaction. Use detection
    # only to start with, because that minimises the chances of post-installation
    # disruption.
    #
    SecRuleEngine On


    # -- Request body handling ---------------------------------------------------

    # Allow ModSecurity to access request bodies. If you don't, ModSecurity
    # won't be able to see any POST parameters, which opens a large security
    # hole for attackers to exploit.
    #
    SecRequestBodyAccess On


    # Enable XML request body parser.
    # Initiate XML Processor in case of xml content-type
    #
    SecRule REQUEST_HEADERS:Content-Type "^(?:application(?:/soap\+|/)|text/)xml" \
        "id:'200000',phase:1,t:none,t:lowercase,pass,nolog,ctl:requestBodyProcessor=XML"

    # Enable JSON request body parser.
    # Initiate JSON Processor in case of JSON content-type; change accordingly
    # if your application does not use 'application/json'
    #
    SecRule REQUEST_HEADERS:Content-Type "^application/json" \
        "id:'200001',phase:1,t:none,t:lowercase,pass,nolog,ctl:requestBodyProcessor=JSON"

    # Sample rule to enable JSON request body parser for more subtypes.
    # Uncomment or adapt this rule if you want to engage the JSON
    # Processor for "+json" subtypes
    #
    #SecRule REQUEST_HEADERS:Content-Type "^application/[a-z0-9.-]+[+]json" \
    #     "id:'200006',phase:1,t:none,t:lowercase,pass,nolog,ctl:requestBodyProcessor=JSON"

    # Maximum request body size we will accept for buffering. If you support
    # file uploads then the value given on the first line has to be as large
    # as the largest file you are willing to accept. The second value refers
    # to the size of data, with files excluded. You want to keep that value as
    # low as practical.
    #
    SecRequestBodyLimit 13107200
    SecRequestBodyNoFilesLimit 131072

    # Store up to 128 KB of request body data in memory. When the multipart
    # parser reaches this limit, it will start using your hard disk for
    # storage. That is slow, but unavoidable.
    #
    SecRequestBodyInMemoryLimit 131072

    # What do do if the request body size is above our configured limit.
    # Keep in mind that this setting will automatically be set to ProcessPartial
    # when SecRuleEngine is set to DetectionOnly mode in order to minimize
    # disruptions when initially deploying ModSecurity.
    #
    SecRequestBodyLimitAction Reject

    # Maximum parsing depth allowed for JSON objects. You want to keep this
    # value as low as practical.
    #
    SecRequestBodyJsonDepthLimit 512

    # Verify that we've correctly processed the request body.
    # As a rule of thumb, when failing to process a request body
    # you should reject the request (when deployed in blocking mode)
    # or log a high-severity alert (when deployed in detection-only mode).
    #
    SecRule REQBODY_ERROR "!@eq 0" \
    "id:'200002', phase:2,t:none,log,deny,status:400,msg:'Failed to parse request body.',logdata:'%{reqbody_error_msg}',severity:2"

    # By default be strict with what we accept in the multipart/form-data
    # request body. If the rule below proves to be too strict for your
    # environment consider changing it to detection-only. You are encouraged
    # _not_ to remove it altogether.
    #
    SecRule MULTIPART_STRICT_ERROR "!@eq 0" \
    "id:'200003',phase:2,t:none,log,deny,status:400, \
    msg:'Multipart request body failed strict validation: \
    PE %{REQBODY_PROCESSOR_ERROR}, \
    BQ %{MULTIPART_BOUNDARY_QUOTED}, \
    BW %{MULTIPART_BOUNDARY_WHITESPACE}, \
    DB %{MULTIPART_DATA_BEFORE}, \
    DA %{MULTIPART_DATA_AFTER}, \
    HF %{MULTIPART_HEADER_FOLDING}, \
    LF %{MULTIPART_LF_LINE}, \
    SM %{MULTIPART_MISSING_SEMICOLON}, \
    IQ %{MULTIPART_INVALID_QUOTING}, \
    IP %{MULTIPART_INVALID_PART}, \
    IH %{MULTIPART_INVALID_HEADER_FOLDING}, \
    FL %{MULTIPART_FILE_LIMIT_EXCEEDED}'"

    # Did we see anything that might be a boundary?
    #
    SecRule MULTIPART_UNMATCHED_BOUNDARY "!@eq 0" \
    "id:'200004',phase:2,t:none,log,deny,msg:'Multipart parser detected a possible unmatched boundary.'"

    # PCRE Tuning
    # We want to avoid a potential RegEx DoS condition
    #
    SecPcreMatchLimit 100000
    SecPcreMatchLimitRecursion 100000

    # Some internal errors will set flags in TX and we will need to look for these.
    # All of these are prefixed with "MSC_".  The following flags currently exist:
    #
    # MSC_PCRE_LIMITS_EXCEEDED: PCRE match limits were exceeded.
    #
    SecRule TX:/^MSC_/ "!@streq 0" \
            "id:'200005',phase:2,t:none,deny,msg:'ModSecurity internal error flagged: %{MATCHED_VAR_NAME}'"


    # -- Response body handling --------------------------------------------------

    # Allow ModSecurity to access response bodies. 
    # You should have this directive enabled in order to identify errors
    # and data leakage issues.
    # 
    # Do keep in mind that enabling this directive does increases both
    # memory consumption and response latency.
    #
    SecResponseBodyAccess On

    # Which response MIME types do you want to inspect? You should adjust the
    # configuration below to catch documents but avoid static files
    # (e.g., images and archives).
    #
    SecResponseBodyMimeType text/plain text/html text/xml

    # Buffer response bodies of up to 512 KB in length.
    SecResponseBodyLimit 524288

    # What happens when we encounter a response body larger than the configured
    # limit? By default, we process what we have and let the rest through.
    # That's somewhat less secure, but does not break any legitimate pages.
    #
    SecResponseBodyLimitAction ProcessPartial


    # -- Filesystem configuration ------------------------------------------------

    # The location where ModSecurity stores temporary files (for example, when
    # it needs to handle a file upload that is larger than the configured limit).
    # 
    # This default setting is chosen due to all systems have /tmp available however, 
    # this is less than ideal. It is recommended that you specify a location that's private.
    #
    SecTmpDir /tmp/

    # The location where ModSecurity will keep its persistent data.  This default setting 
    # is chosen due to all systems have /tmp available however, it
    # too should be updated to a place that other users can't access.
    #
    SecDataDir /tmp/


    # -- File uploads handling configuration -------------------------------------

    # The location where ModSecurity stores intercepted uploaded files. This
    # location must be private to ModSecurity. You don't want other users on
    # the server to access the files, do you?
    #
    #SecUploadDir /opt/modsecurity/var/upload/

    # By default, only keep the files that were determined to be unusual
    # in some way (by an external inspection script). For this to work you
    # will also need at least one file inspection rule.
    #
    #SecUploadKeepFiles RelevantOnly

    # Uploaded files are by default created with permissions that do not allow
    # any other user to access them. You may need to relax that if you want to
    # interface ModSecurity to an external program (e.g., an anti-virus).
    #
    #SecUploadFileMode 0600


    # -- Debug log configuration -------------------------------------------------

    # The default debug log configuration is to duplicate the error, warning
    # and notice messages from the error log.
    #
    #SecDebugLog /opt/modsecurity/var/log/debug.log
    #SecDebugLogLevel 3


    # -- Audit log configuration -------------------------------------------------

    # Log the transactions that are marked by a rule, as well as those that
    # trigger a server error (determined by a 5xx or 4xx, excluding 404,  
    # level response status codes).
    #
    SecAuditEngine RelevantOnly
    SecAuditLogRelevantStatus "^(?:5|4(?!04))"

    # Log everything we know about a transaction.
    SecAuditLogParts ABCEFHJKZ

    # Use a single file for logging. This is much easier to look at, but
    # assumes that you will use the audit log only ocassionally.
    #
    SecAuditLogType Serial
    SecAuditLog /var/log/apache2/modsec_audit.log

    # Specify the path for concurrent audit logging.
    #SecAuditLogStorageDir /opt/modsecurity/var/audit/


    # -- Miscellaneous -----------------------------------------------------------

    # Use the most commonly used application/x-www-form-urlencoded parameter
    # separator. There's probably only one application somewhere that uses
    # something else so don't expect to change this value.
    #
    SecArgumentSeparator &

    # Settle on version 0 (zero) cookies, as that is what most applications
    # use. Using an incorrect cookie version may open your installation to
    # evasion attacks (against the rules that examine named cookies).
    #
    SecCookieFormat 0

    # Specify your Unicode Code Point.
    # This mapping is used by the t:urlDecodeUni transformation function
    # to properly map encoded data to your language. Properly setting
    # these directives helps to reduce false positives and negatives.
    #
    SecUnicodeMapFile unicode.mapping 20127

    # Improve the quality of ModSecurity by sharing information about your
    # current ModSecurity version and dependencies versions.
    # The following information will be shared: ModSecurity version,
    # Web Server version, APR version, PCRE version, Lua version, Libxml2
    # version, Anonymous unique id for host.
    # NB: As of April 2022, there is no longer any advantage to turning this
    # setting On, as there is no active receiver for the information.
    SecStatusEngine Off

    cd /etc/modsecurity
    sudo git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git
    sudo mv owasp-modsecurity-crs/crs-setup.conf.example owasp-modsecurity-crs/crs-setup.conf

    sudo apt-get install libapache2-mod-evasive
    sudo a2enmod evasive
    sudo nano /etc/apache2/mods-enabled/evasive.conf
    systemctl restart apache2

    <IfModule mod_evasive20.c>
        DOSHashTableSize    3097
        DOSPageCount        20
        DOSSiteCount        50
        DOSPageInterval     1
        DOSSiteInterval     5
        DOSBlockingPeriod   3600

        DOSEmailNotify      you@yourdomain.com
        DOSSystemCommand    "su - someuser -c '/sbin/... %s ...'"
        DOSLogDir           "/var/log/mod_evasive"
    </IfModule>

    iptables -A INPUT -p tcp --syn -m connlimit --connlimit-above 10 -j REJECT --reject-with tcp-reset
    iptables -A INPUT -p tcp --syn -m limit --limit 1/s --limit-burst 3 -j RETURN
    iptables -A INPUT -p tcp --syn -j DROP
    iptables -A INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 20 -j REJECT

    sudo apt-get -y install libapache2-mod-qos
    sudo nano /etc/apache2/mods-available/qos.conf

    <IfModule mod_qos.c>
        # handle connections from up to 100000 different IPs
        QS_ClientEntries 100000
        # allow only 50 connections per IP
        QS_SrvMaxConnPerIP 50
        # limit the maximum number of active TCP connections to 256
        MaxClients 256
        # disables keep-alive when 180 (70%) TCP connections are occupied
        QS_SrvMaxConnClose 180
        # minimum request/response speed 
        # (deny clients that keep connections open without requesting anything)
        QS_SrvMinDataRate 150 1200
    </IfModule>

    sudo apt install wafw00f
    wafw00f trabajosunach.tech
    wafw00f 67.205.166.36

    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
    sudo certbot --apache

    sudo apt install python3-pip
    sudo apt install python3-requests
    cd /home/

    sudo nano programa1.py

    import re
    from collections import Counter
    import socket
    import platform
    import getpass
    import requests  # Puede que necesites instalar la librería 'requests'

    def extract_public_ips(log_file_path):
        ip_regex = re.compile(r'\d+\.\d+\.\d+\.\d+')

        with open(log_file_path, 'r') as log_file:
            ip_addresses = [re.search(ip_regex, line).group() for line in log_file if 'GET' in line]

        return ip_addresses

    def resolve_hostnames(ip_addresses):
        hostnames = {}
        for ip in ip_addresses:
            try:
                hostname = socket.gethostbyaddr(ip)[0]
                hostnames[ip] = hostname
            except socket.herror:
                # No se pudo resolver el nombre para esta IP
                hostnames[ip] = "Unknown"

        return hostnames

    def get_os_information():
        os_info = platform.system() + " " + platform.version()
        return os_info

    def get_local_ip():
        local_ip = socket.gethostbyname(socket.gethostname())
        return local_ip

    def get_username():
        username = getpass.getuser()
        return username

    def get_location(ip):
        try:
            response = requests.get(f"http://ipinfo.io/{ip}/json")
            data = response.json()
            location = data.get('city', 'Unknown') + ', ' + data.get('region', 'Unknown') + ', ' + data.get('country', 'Unknown')
            return location
        except requests.RequestException as e:
            print(f"Error obteniendo la ubicación para la IP {ip}: {e}")
            return "Unknown"

    def count_ips(ip_addresses):
        ip_counter = Counter(ip_addresses)
        return ip_counter

    if __name__ == "__main__":
        apache_log_file = '/var/log/apache2/access.log'

        public_ips = extract_public_ips(apache_log_file)
        hostnames = resolve_hostnames(public_ips)
        ip_counter = count_ips(public_ips)
        os_info = get_os_information()
        local_ip = get_local_ip()
        username = get_username()

        print("Información del Sistema Operativo:", os_info)
        print("Dirección IP Local:", local_ip)
        print("Nombre de Usuario:", username)
        print("\nDirecciones IP públicas conectadas al sitio web Apache 2:")
        for ip, count in ip_counter.items():
            hostname = hostnames[ip]
            location = get_location(ip)
            print(f"{ip} ({hostname}): {count} conexiones, Ubicación: {location}")

    python3 programa2.py

    import requests

    def get_ip_info(ip):
        url = f"http://ipinfo.io/{ip}/json"
        response = requests.get(url)
        
        if response.status_code == 200:
            ip_data = response.json()
            print("IP Address:", ip_data.get('ip'))
            print("Hostname:", ip_data.get('hostname'))
            print("City:", ip_data.get('city'))
            print("Region:", ip_data.get('region'))
            print("Country:", ip_data.get('country'))
            print("Location:", ip_data.get('loc'))
            print("Organization:", ip_data.get('org'))
        else:
            print("Unable to fetch IP information.")

    if __name__ == "__main__":
        user_ip = input("Enter the public IP address to check: ")
        get_ip_info(user_ip)

    
    python3 programa1.py
    python3 programa2.py
