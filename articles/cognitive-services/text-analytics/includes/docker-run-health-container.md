---
title: Příklad spuštění kontejneru příkazu Docker run
titleSuffix: Azure Cognitive Services
description: Příkaz Spustit jako Docker pro kontejner stavu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: 3a89ddbc17eab683c658430a64447328ee024a3c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089697"
---
## <a name="install-the-container"></a>Instalace kontejneru

Existuje několik způsobů, jak můžete nainstalovat a spustit Analýza textu pro kontejner stavů. 

- Použijte [Azure Portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) k vytvoření prostředku analýza textu a k získání kontejneru použijte Docker.
- K automatizaci nasazení prostředků a konfigurace kontejnerů použijte následující skripty PowerShellu a rozhraní příkazového řádku Azure CLI.

### <a name="run-the-container-locally"></a>Místní spuštění kontejneru

Pokud chcete po stažení Image kontejneru spustit kontejner ve vlastním prostředí, Najděte jeho ID Image:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Spusťte následující `docker run` příkaz. Nahraďte níže uvedené zástupné symboly vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
|-------------|-------|---|
| **{API_KEY}** | Klíč pro prostředek Analýza textu. Můžete ji najít na stránce **klíč a koncový bod** prostředku na Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Koncový bod pro přístup k rozhraní API pro analýzu textu. Můžete ji najít na stránce **klíč a koncový bod** prostředku na Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | ID image pro váš kontejner. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Vstupní adresář pro kontejner. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Tento příkaz:

- Předpokládá, že vstupní adresář v hostitelském počítači existuje.
- Spustí Analýza textu pro kontejner stavů z image kontejneru.
- Přiděluje 6 PROCESORových jader a 12 gigabajtů (GB) paměti.
- Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
- Přijímá podmínky licenční smlouvy s koncovým uživatelem (EULA) a zodpovědného AI (RAI).
- Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

### <a name="demo-ui-to-visualize-output"></a>Ukázkové uživatelské rozhraní pro vizualizaci výstupu

> [!NOTE]
> Tato ukázka je k dispozici pouze u Analýza textu pro kontejner Health.

Kontejner poskytuje rozhraní API prediktivního koncového bodu pro dotazy založené na REST.  K dispozici jsme také nástroj pro vizualizaci v kontejneru, který je přístupný připojením `/demo` ke koncovému bodu kontejneru. Například:

```
http://<serverURL>:5000/demo
```

Použijte níže uvedený příklad žádosti o vytvoření dotazu do kontejneru, který jste nasadili, a nahraďte `serverURL` proměnnou odpovídající hodnotou.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1-preview.4/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Instalace kontejneru pomocí Azure Web App for Containers

Azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) je prostředek Azure vyhrazený pro spouštění kontejnerů v cloudu. Přináší předem připravené funkce, jako je automatické škálování, podpora kontejnerů Docker a dokování Docker, podpora protokolu HTTPS a mnohem víc.

> [!NOTE]
> Pomocí Azure Web App se automaticky získá doména ve formě `<appservice_name>.azurewebsites.net`

Spuštěním tohoto skriptu PowerShellu pomocí Azure CLI vytvoříte Web App for Containers pomocí svého předplatného a image kontejneru přes HTTPS. Před odesláním první žádosti počkejte, než se skript dokončí (přibližně 25-30 minut).

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME 
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Instalace kontejneru pomocí Azure Container instance

K jednoduššímu nasazení můžete použít také službu Azure Container instance (ACI). ACI je prostředek, který umožňuje spouštět kontejnery Docker na vyžádání ve spravovaném prostředí Azure bez serveru. 

Postup nasazení prostředku ACI pomocí Azure Portal najdete v tématu [použití Azure Container Instances](../../containers/azure-container-instance-recipe.md) . Pomocí rozhraní příkazového řádku Azure můžete také použít skript prostředí PowerShell, který vytvoří ACI na vašem předplatném pomocí Image kontejneru.  Před odesláním první žádosti počkejte, než se skript dokončí (přibližně 25-30 minut).  V důsledku omezení maximálního počtu procesorů na prostředek ACI tuto možnost nevybírejte, pokud očekáváte, že budete odesílat více než 5 velkých dokumentů (přibližně 5000 znaků) na jednu žádost.
Informace o dostupnosti najdete v článku věnovaném [místní podpoře ACI](../../../container-instances/container-instances-region-availability.md) . 

> [!NOTE] 
> Azure Container Instances Nezahrnovat podporu protokolu HTTPS pro předdefinované domény. Pokud potřebujete protokol HTTPS, budete ho muset ručně nakonfigurovat, včetně vytvoření certifikátu a registrace domény. Pokyny k tomu, jak to udělat s NGINX, najdete níže.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Zabezpečené připojení ACI

Ve výchozím nastavení není k dispozici žádné zabezpečení při použití ACI s rozhraním API kontejnerů. Důvodem je to, že kontejnery se obvykle spouštějí jako součást pod, která je chráněná před síťovým mostem zvenčí. Můžete ale upravit kontejner s frontou komponentou, která zachovává koncový bod kontejneru jako soukromý. Následující příklady používají [Nginx](https://www.nginx.com) jako příchozí bránu k podpoře protokolu HTTPS/SSL a ověřování pomocí klientského certifikátu.

> [!NOTE]
> NGINX je Open-Source Server HTTP a server proxy s vysokým výkonem. Kontejner NGINX se dá použít k ukončení připojení TLS pro jeden kontejner. K dispozici jsou také složitější řešení pro ukončení protokolu TLS založená na NGINX.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Nastavení NGINX jako příchozí brány

NGINX používá [konfigurační soubory](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) pro povolení funkcí za běhu. Chcete-li povolit ukončení protokolu TLS pro jinou službu, je nutné zadat certifikát SSL pro ukončení připojení TLS a  `proxy_pass` zadat adresu služby. Ukázka je uvedena níže.


> [!NOTE]
> `ssl_certificate` očekává cestu, která se má zadat v místním systému souborů kontejneru NGINX. Adresa zadaná pro `proxy_pass` musí být dostupná v rámci sítě kontejneru Nginx.

Kontejner NGINX načte všechny soubory v `_.conf_` , které jsou připojeny `/etc/nginx/conf.d/` do cesty konfigurace protokolu HTTP.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Ukázkový soubor pro sestavení Docker

Níže uvedený příklad ukazuje, jak lze vytvořit soubor [Docker](https://docs.docker.com/compose/reference/overview) pro nasazení NGINX a analýza textu pro kontejnery Health:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Chcete-li zahájit tento soubor Docker, spusťte následující příkaz z konzoly na kořenové úrovni souboru:

```bash
docker-compose up
```

Další informace najdete v dokumentaci k NGINX o [ukončení Nginx SSL](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).
