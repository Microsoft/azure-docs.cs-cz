---
title: Povolení TLS s kontejnerem sajdkaři
description: Vytvoření koncového bodu SSL nebo TLS pro skupinu kontejnerů spuštěnou v instanci kontejneru Azure spuštěním Nginx v kontejneru postranního vaginu
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: b9ea9367219db694b89d6bf4a1e52efb373c71c4
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984602"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Povolení koncového bodu TLS v kontejneru postranního važeku

Tento článek ukazuje, jak vytvořit [skupinu kontejnerů](container-instances-container-groups.md) s kontejnerem aplikace a kontejneru sajdkáru se systémem zprostředkovatele TLS/SSL. Nastavením skupiny kontejnerů se samostatným koncovým bodem TLS povolíte připojení TLS pro vaši aplikaci beze změny kódu aplikace.

Nastavíte ukázkovou skupinu kontejnerů skládající se ze dvou kontejnerů:
* Kontejner aplikace, který spouští jednoduchou webovou aplikaci pomocí veřejné image [Microsoft aci-helloworld.](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 
* Kontejner sajdkársá kopie s veřejným [obrazem Nginx,](https://hub.docker.com/_/nginx) nakonfigurovaný pro použití TLS. 

V tomto příkladu skupina kontejnerů pouze zveřejňuje port 443 pro Nginx s jeho veřejnou IP adresu. Nginx směruje požadavky HTTPS do doprovodné webové aplikace, která interně naslouchá na portu 80. Můžete přizpůsobit příklad pro kontejnerové aplikace, které poslouchají na jiných portech. 

Další kroky k povolení protokolu TLS ve skupině kontejnerů naleznete v [dalších krocích.](#next-steps)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení tohoto článku můžete použít Azure Cloud Shell nebo místní instalaci vázací mise Azure. Pokud jej chcete používat místně, doporučujeme verzi 2.0.55 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Chcete-li nastavit Nginx jako poskytovatele TLS, potřebujete certifikát TLS/SSL. Tento článek ukazuje, jak vytvořit a nastavit certifikát TLS/SSL s vlastním podpisem. Pro produkční scénáře byste měli získat certifikát od certifikační autority.

Chcete-li vytvořit certifikát TLS/SSL podepsaný svým držitelem, použijte nástroj [OpenSSL](https://www.openssl.org/) dostupný v Azure Cloud Shellu a mnoha distribucích Linuxu nebo použijte srovnatelný klientský nástroj ve vašem operačním systému.

Nejprve vytvořte žádost o certifikát (soubor.csr) v místním pracovním adresáři:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Podle pokynů přidejte identifikační údaje. Do pole Běžný název zadejte název hostitele přidružený k certifikátu. Po zobrazení výzvy k zadání hesla stisknutím klávesy Enter bez psaní přidání hesla přeskočíte.

Spusťte následující příkaz a vytvořte z žádosti o certifikát certifikát certifikát podepsaný svým držitelem (soubor CRT). Příklad:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Nyní byste měli vidět tři soubory v`ssl.csr`adresáři: žádost`ssl.key`o certifikát ( ),`ssl.crt`soukromý klíč ( ) a certifikát podepsaný svým držitelem ( ). Můžete `ssl.key` použít `ssl.crt` a v pozdějších krocích.

## <a name="configure-nginx-to-use-tls"></a>Konfigurace nginxu pro použití TLS

### <a name="create-nginx-configuration-file"></a>Vytvoření konfiguračního souboru Nginx

V této části vytvoříte konfigurační soubor pro Nginx pro použití TLS. Začněte zkopírováním následujícího textu `nginx.conf`do nového souboru s názvem . V Prostředí Azure Cloud Shell můžete použít visual studio kód k vytvoření souboru ve vašem pracovním adresáři:

```console
code nginx.conf
```

V `location`aplikaci nastavíte `proxy_pass` správný port pro aplikaci. V tomto příkladu jsme nastavili `aci-helloworld` port 80 pro kontejner.

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

### <a name="base64-encode-secrets-and-configuration-file"></a>Základní kód tajných kódů a konfigurační soubor

Base64 kódovat konfigurační soubor Nginx, certifikát TLS/SSL a klíč TLS. V další části zadáte kódovaný obsah do souboru YAML, který se používá k nasazení skupiny kontejnerů.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Nasazení skupiny kontejnerů

Nyní nasaďte skupinu kontejnerů zadáním konfigurace kontejneru v [souboru YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Vytvořit soubor YAML

Zkopírujte následující yaml do `deploy-aci.yaml`nového souboru s názvem . V Prostředí Azure Cloud Shell můžete použít visual studio kód k vytvoření souboru ve vašem pracovním adresáři:

```console
code deploy-aci.yaml
```

Zadejte obsah souborů kódovaných base64, pokud `secret`je uvedeno v písmenu a). Například `cat` každý z base64 kódované soubory zobrazit jeho obsah. Během nasazení jsou tyto soubory přidány do [tajného svazku](container-instances-volume-secret.md) ve skupině kontejnerů. V tomto příkladu je tajný svazek připojen do kontejneru Nginx.

```YAML
api-version: 2018-10-01
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

Vytvořte skupinu prostředků pomocí příkazu [vytvořit skupinu az:](/cli/azure/group#az-group-create)

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Nasadit skupinu kontejnerů pomocí příkazu [az kontejner vytvořit,](/cli/azure/container#az-container-create) předávání souboru YAML jako argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Zobrazit stav nasazení

Chcete-li zobrazit stav nasazení, použijte následující příkaz [az container show:](/cli/azure/container#az-container-show)

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Pro úspěšné nasazení je výstup podobný následujícímu:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>Ověření připojení TLS

Pomocí prohlížeče přejděte na veřejnou IP adresu skupiny kontejnerů. Adresa IP zobrazená v `52.157.22.76`tomto příkladu **https://52.157.22.76**je , takže adresa URL je . Chcete-li zobrazit spuštěnou aplikaci, musíte použít protokol HTTPS z důvodu konfigurace serveru Nginx. Pokusy o připojení přes protokol HTTP se nezdaří.

![Snímek obrazovky prohlížeče ukazující aplikaci spuštěnou v instanci kontejneru Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Vzhledem k tomu, že tento příklad používá certifikát podepsaný svým držitelem a nikoli certifikát od certifikační autority, prohlížeč zobrazí upozornění zabezpečení při připojování k webu přes protokol HTTPS. Chcete-li přejít na stránku, může být nutné přijmout upozornění nebo upravit nastavení prohlížeče nebo certifikátu. Jde o očekávané chování.

>

## <a name="next-steps"></a>Další kroky

Tento článek vám ukázal, jak nastavit kontejner Nginx povolit připojení TLS k webové aplikaci spuštěné ve skupině kontejnerů. Tento příklad můžete přizpůsobit pro aplikace, které poslouchají na jiných portech než port 80. Můžete také aktualizovat konfigurační soubor Nginx tak, aby automaticky přesměroval připojení serveru na portu 80 (HTTP) na použití protokolu HTTPS.

Zatímco tento článek používá Nginx v sajdkáru, můžete použít jiného poskytovatele TLS, jako je [Caddy](https://caddyserver.com/).

Pokud nasadíte skupinu kontejnerů ve [virtuální síti Azure](container-instances-vnet.md), můžete zvážit další možnosti povolení koncového bodu TLS pro instanci back-endového kontejneru, včetně:

* [Servery Proxy pro funkce Azure](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) – viz ukázková [šablona nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet).
