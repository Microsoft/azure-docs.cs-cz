---
title: Povolení protokolu SSL ve službě Azure Container Instances
description: Vytvořit koncový bod SSL nebo TLS pro skupinu kontejnerů spuštěná ve službě Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 12de4ef31084d8ac8586c79ffe3d0a8e891727bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411389"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Povolení koncového bodu protokolu SSL ve skupině kontejnerů

Tento článek popisuje, jak vytvořit [skupinu kontejnerů](container-instances-container-groups.md) s kontejnerem aplikace a kontejner sajdkára s poskytovatelem služby SSL. Zařiďte si skupinu kontejnerů s samostatný koncový bod SSL povolíte připojení SSL pro vaši aplikaci bez změny kódu aplikace.

Můžete nastavit skupinu kontejnerů, který se skládá z dvou kontejnerů:
* Kontejner aplikace, která spouští jednoduchou webovou aplikaci pomocí veřejného Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) bitové kopie. 
* Kontejner sajdkára spuštěnou veřejnosti [Nginx](https://hub.docker.com/_/nginx) image nakonfigurovaná tak, aby používat protokol SSL. 

V tomto příkladu skupina kontejnerů zpřístupňuje pouze port 443 pro server Nginx s jeho veřejné IP adresy. Server Nginx směruje požadavky HTTPS na doprovodnou webovou aplikací, které interně naslouchá na portu 80. Příklad pro kontejnerové aplikace, které naslouchat na jiné porty můžete přizpůsobit.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení tohoto článku můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud chcete použít ho místně, verze 2.0.55 nebo později se doporučuje. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Pokud chcete nastavit server Nginx jako zprostředkovatele SSL, budete potřebovat certifikát SSL. Tento článek ukazuje, jak vytvořit a nastavit certifikát SSL podepsaný svým držitelem. Pro produkční scénáře by měl získat certifikát od certifikační autority.

Chcete-li vytvořit certifikát SSL podepsaný svým držitelem, použijte [OpenSSL](https://www.openssl.org/) nástroje dostupné v Azure Cloud Shell a řadu distribucí systému Linux, nebo použijte nástroj srovnatelné klienta v operačním systému.

Nejprve vytvořte žádost o certifikát (soubor .csr) v místní pracovní adresář:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Postupujte podle pokynů pro přidání identifikační informace. Běžný název zadejte název hostitele spojenou s certifikátem. Po zobrazení výzvy k zadání hesla, aniž by museli zadávat, chcete-li přeskočit přidání hesla stiskněte klávesu Enter.

Spusťte následující příkaz k vytvoření certifikátu podepsaného svým držitelem (soubor s příponou CRT) z žádosti o certifikát. Příklad:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Teď byste měli vidět tři soubory v adresáři: žádost o certifikát (`ssl.csr`), privátní klíč (`ssl.key`) a certifikát podepsaný svým držitelem (`ssl.crt`). Použijete `ssl.key` a `ssl.crt` v dalších krocích.

## <a name="configure-nginx-to-use-ssl"></a>Nakonfigurujte server Nginx na používání protokolu SSL

### <a name="create-nginx-configuration-file"></a>Vytvořte konfigurační soubor Nginx

V této části vytvoříte konfigurační soubor Nginx tak, aby používat protokol SSL. Začněte tak, že zkopírujete následující text do nového souboru s názvem`nginx.conf`. Ve službě Azure Cloud Shell můžete použít Visual Studio Code k vytvoření souboru ve svém pracovním adresáři:

```console
code nginx.conf
```

V `location`, nezapomeňte nastavit `proxy_pass` s správný port pro aplikaci. V tomto příkladu jsme nastavili pro port 80 `aci-helloworld` kontejneru.

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
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
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

### <a name="base64-encode-secrets-and-configuration-file"></a>Kódování Base64 tajné kódy a konfigurační soubor

Zakódovat do formátu Base64 konfigurační soubor Nginx, certifikát SSL a klíče SSL. V další části zadejte obsah kódovaný soubor YAML, použít k nasazení skupiny kontejnerů.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Nasazení skupiny kontejnerů

Zadat konfigurace kontejneru v teď nasadit skupinu kontejnerů [soubor YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Vytvořit soubor YAML

Zkopírujte následující kód YAML do nového souboru s názvem `deploy-aci.yaml`. Ve službě Azure Cloud Shell můžete použít Visual Studio Code k vytvoření souboru ve svém pracovním adresáři:

```console
code deploy-aci.yaml
```

Zadejte obsah kódovaný ve formátu base64 soubory označené v rámci `secret`. Například `cat` všech souborů s kódováním base64 zobrazíte jeho obsah. Během nasazování, tyto soubory jsou přidány do [tajný svazek](container-instances-volume-secret.md) ve skupině kontejnerů. V tomto příkladu je tajný svazek připojený ke kontejneru Nginx.

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

Vytvořte skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group#az-group-create) příkaz:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nasadit skupinu kontejnerů s [az container vytvořit](/cli/azure/container#az-container-create) příkazu, předejte soubor YAML jako argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Zobrazení stavu nasazení

Chcete-li zobrazit stav nasazení, použijte následující [az container show](/cli/azure/container#az-container-show) příkaz:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Pro úspěšné nasazení výstup je podobný následujícímu:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Ověřte připojení protokolem SSL

Pokud chcete zobrazit spuštěnou aplikaci, přejděte na jeho IP adresu v prohlížeči. Například IP adresou uvedenou v tomto příkladu je `52.157.22.76`. Je nutné použít `https://<IP-ADDRESS>` zobrazíte běžící aplikaci z důvodu konfigurace serveru Nginx. Pokusí se připojit s `http://<IP-ADDRESS>` nezdaří.

![Snímek obrazovky prohlížeče ukazující aplikaci spuštěnou v instanci kontejneru Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Vzhledem k tomu tento příklad používá certifikát podepsaný svým držitelem a není jednou z certifikační autority, prohlížeč zobrazí upozornění zabezpečení při připojování k webu přes protokol HTTPS. Toto chování je očekávané.
>

## <a name="next-steps"></a>Další postup

Tento článek vám ukázali, jak nastavit službu kontejneru Nginx povolit připojení SSL k webové aplikaci spuštěnou ve skupině kontejnerů. Můžete upravit tento příklad pro aplikace, které naslouchat na jiné porty než port 80. Můžete také aktualizovat konfigurační soubor Nginx automaticky přesměrovat připojení serverů na portu 80 (HTTP) pro použití protokolu HTTPS.

Tento článek používá Nginx v sajdkára, ale můžete použít jiného zprostředkovatele SSL, jako [Caddy](https://caddyserver.com/).

Dalším přístupem k povolení protokolu SSL ve skupině kontejnerů se skupiny v nasazení [virtuální síť Azure](container-instances-vnet.md) s [Azure application gateway](../application-gateway/overview.md). Bránu můžete nastavit jako koncový bod SSL. Viz ukázka [šablonu nasazení](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) můžete přizpůsobit pro povolení ukončení protokolu SSL na bráně.
