---
title: Povolit TLS s kontejnerem webpostranního vozíku
description: Vytvoření koncového bodu SSL nebo TLS pro skupinu kontejnerů běžící v Azure Container Instances spuštěním Nginx v kontejneru webvozíku
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 6587a84e7cbe655c509f74e9e39e93010e7058be
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558075"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Povolení koncového bodu TLS v kontejneru webvozík

Tento článek ukazuje, jak vytvořit [skupinu kontejnerů](container-instances-container-groups.md) pomocí kontejneru aplikace a kontejneru postranního vozíku s poskytovatelem TLS/SSL. Nastavením skupiny kontejnerů pomocí samostatného koncového bodu TLS povolíte připojení TLS pro vaši aplikaci beze změny kódu aplikace.

Nastavili jste ukázkovou skupinu kontejnerů skládající se ze dvou kontejnerů:
* Kontejner aplikace, který spouští jednoduchou webovou aplikaci s použitím veřejné image Microsoft [ACI-Hello](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) . 
* Kontejner postranového vozíku, na kterém běží veřejná image [Nginx](https://hub.docker.com/_/nginx) , nakonfigurovaná na používání protokolu TLS. 

V tomto příkladu skupina kontejnerů zveřejňuje port 443 pro Nginx s jeho veřejnou IP adresou. Nginx směruje požadavky HTTPS do doprovodné webové aplikace, která naslouchá interně na portu 80. Můžete upravit příklad pro kontejnerové aplikace, které naslouchají na jiných portech. 

Další přístupy k povolení TLS ve skupině kontejnerů najdete v části [Další kroky](#next-steps) .

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.55 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)

Pokud chcete nastavit Nginx jako poskytovatele TLS, budete potřebovat certifikát TLS/SSL. V tomto článku se dozvíte, jak vytvořit a nastavit certifikát TLS/SSL podepsaný svým držitelem. V produkčních scénářích byste měli získat certifikát od certifikační autority.

Chcete-li vytvořit certifikát TLS/SSL podepsaný svým držitelem, použijte nástroj [OpenSSL](https://www.openssl.org/) dostupný v Azure Cloud Shell a mnoha distribucích systému Linux nebo použijte srovnatelný Nástroj klienta v operačním systému.

Nejdřív vytvořte žádost o certifikát (soubor. CSR) v místním pracovním adresáři:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Podle pokynů přidejte identifikační informace. Do pole běžný název zadejte název hostitele přidružený k certifikátu. Po zobrazení výzvy k zadání hesla stiskněte klávesu ENTER bez psaní, abyste mohli přeskočit Přidání hesla.

Spuštěním následujícího příkazu vytvořte certifikát podepsaný svým držitelem (soubor. CRT) z žádosti o certifikát. Příklad:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Nyní byste měli vidět tři soubory v adresáři: žádost o certifikát ( `ssl.csr` ), privátní klíč ( `ssl.key` ) a certifikát podepsaný svým držitelem ( `ssl.crt` ). Použijete `ssl.key` a `ssl.crt` v pozdějších krocích.

## <a name="configure-nginx-to-use-tls"></a>Konfigurace nginx pro použití TLS

### <a name="create-nginx-configuration-file"></a>Vytvořit konfigurační soubor Nginx

V této části vytvoříte konfigurační soubor pro Nginx, který bude používat protokol TLS. Začněte zkopírováním následujícího textu do nového souboru s názvem `nginx.conf` . V Azure Cloud Shell můžete pomocí Visual Studio Code vytvořit soubor v pracovním adresáři:

```console
code nginx.conf
```

V `location` nástroji nezapomeňte nastavit `proxy_pass` správný port pro vaši aplikaci. V tomto příkladu nastavíme pro kontejner port 80 `aci-helloworld` .

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Tajné kódy a konfigurační soubory pro kódování Base64

Base64 – zakóduje konfigurační soubor Nginx, certifikát TLS/SSL a klíč TLS. V další části zadáte kódovaný obsah do souboru YAML, který se používá k nasazení skupiny kontejnerů.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Nasadit skupinu kontejnerů

Nyní nasaďte skupinu kontejnerů zadáním konfigurace kontejneru v [souboru YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Vytvořit soubor YAML

Zkopírujte následující YAML do nového souboru s názvem `deploy-aci.yaml` . V Azure Cloud Shell můžete pomocí Visual Studio Code vytvořit soubor v pracovním adresáři:

```console
code deploy-aci.yaml
```

Zadejte obsah souborů s kódováním base64, kde jsou uvedeny v části `secret` . Například `cat` každý ze souborů s kódováním base64 zobrazí jeho obsah. Během nasazování se tyto soubory přidají do [tajného svazku](container-instances-volume-secret.md) ve skupině kontejnerů. V tomto příkladu je tajný svazek připojený ke kontejneru Nginx.

```YAML
api-version: 2019-12-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Nasazení skupiny kontejnerů

Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](/cli/azure/group#az-group-create) :

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Nasaďte skupinu kontejnerů pomocí příkazu [AZ Container Create](/cli/azure/container#az-container-create) a předáním souboru YAML jako argumentu.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Zobrazit stav nasazení

Chcete-li zobrazit stav nasazení, použijte následující příkaz [AZ Container show](/cli/azure/container#az-container-show) :

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

V případě úspěšného nasazení je výstup podobný následujícímu:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>Ověřit připojení TLS

Pomocí prohlížeče přejděte na veřejnou IP adresu skupiny kontejnerů. IP adresa zobrazená v tomto příkladu je `52.157.22.76` , takže adresa URL je **https://52.157.22.76** . K zobrazení spuštěné aplikace z důvodu konfigurace serveru Nginx je nutné použít protokol HTTPS. Pokus o připojení přes protokol HTTP selže.

![Snímek obrazovky prohlížeče ukazující aplikaci spuštěnou v instanci kontejneru Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Vzhledem k tomu, že v tomto příkladu se používá certifikát podepsaný svým držitelem a ne certifikát od certifikační autority, zobrazuje prohlížeč při připojování k webu přes protokol HTTPS upozornění zabezpečení. Může být nutné přijmout upozornění nebo upravit nastavení prohlížeče nebo certifikátu, aby bylo možné pokračovat na stránku. Jde o očekávané chování.

>

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak nastavit kontejner Nginx pro povolení připojení TLS k webové aplikaci běžící ve skupině kontejnerů. Tento příklad můžete upravit pro aplikace, které naslouchají na jiných portech než port 80. Konfigurační soubor Nginx můžete také aktualizovat tak, aby automaticky přesměroval připojení serveru na portu 80 (HTTP) na používání protokolu HTTPS.

I když tento článek používá Nginx v postranním vozíku, můžete použít jiného poskytovatele TLS, jako je [Caddy](https://caddyserver.com/).

Pokud nasadíte skupinu kontejnerů ve [službě Azure Virtual Network](container-instances-vnet.md), můžete zvážit další možnosti, jak povolit koncový bod TLS pro instanci kontejneru back-endu, včetně:

* [Proxy služby Azure Functions](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) – Podívejte se na vzorovou [šablonu nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet).
