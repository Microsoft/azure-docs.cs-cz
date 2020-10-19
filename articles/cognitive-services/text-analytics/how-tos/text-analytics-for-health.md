---
title: Jak používat Analýza textu pro stav
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak extrahovat a označovat lékařské informace z nestrukturovaného klinického textu pomocí Analýza textu pro stav.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: d9517eef8976e79db21fbe552861d0d59923e8ba
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173962"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Postupy: použití Analýza textu pro stav (Preview)

> [!NOTE]
> Analýza textu pro kontejner stavu se nedávno aktualizoval. Další informace o nedávných změnách najdete v tématu [co je nového](../whats-new.md) . Nezapomeňte si stáhnout nejnovější kontejner a použít uvedené aktualizace.

> [!IMPORTANT] 
> Analýza textu pro stav je funkce ve verzi Preview, která je k dispozici, a se všemi CHYBAmi. V takovém případě **by analýza textu pro stav (Preview) neměly být implementovány ani nasazeny v jakémkoli provozním provozu.** Analýza textu pro stav není zamýšlené ani nedostupné pro použití jako lékařské zařízení, klinická podpora, diagnostický nástroj nebo jiná technologie určená k použití v oblasti diagnostiky, udělení, zmírnění nebo prevence nákazy a jiných podmínek a společnost Microsoft neuděluje licenci ani právo k používání této možnosti pro tyto účely. Tato funkce není navržená ani implementovaná jako náhrada pro profesionální lékařské poradenství nebo zdravotní stanovisko, diagnostiku, zpracování nebo klinické rozsudky odborníka na zdravotní péči a neměla by se používat jako takový. Zákazník je výhradně zodpovědný za jakékoliv použití Analýza textu pro stav. Společnost Microsoft neopravňuje k tomu, aby Analýza textu pro zdraví nebo materiály poskytované v souvislosti s schopností byly dostatečné pro jakékoli lékařské účely nebo jinak splňovaly zdravotní a lékařské požadavky jakékoli osoby. 


Analýza textu pro stav je kontejnerová služba, která extrahuje a označí štítky relevantní lékařské informace z nestrukturovaných textů, jako jsou například poznámky lékaře, souhrny vypouštěných položek, klinické dokumenty a elektronické záznamy o stavu.  

## <a name="features"></a>Funkce

Analýza textu pro kontejner Health aktuálně provádí rozpoznávání pojmenovaných entit (NER), extrakci vztahů, negaci entit a propojení entit pro anglický text ve vlastním vývojovém prostředí, které splňuje vaše konkrétní požadavky na zabezpečení a řízení dat.

#### <a name="named-entity-recognition"></a>[Rozpoznávání pojmenovaných entit](#tab/ner)

Rozpoznávání pojmenovaných entit detekuje slova a fráze zmíněné v nestrukturovaném textu, které mohou být přidruženy k jednomu nebo více sémantickým typům, jako je například diagnostika, název léků, příznak/znaménko nebo stáří.

> [!div class="mx-imgBorder"]
> ![NER stavu](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Extrakce vztahu](#tab/relation-extraction)

Extrakce relace identifikuje smysluplná připojení mezi koncepty uvedenými v textu. Například relace "čas podmínky" je nalezena přidružením názvu podmínky k času. 

> [!div class="mx-imgBorder"]
> ![Stav znovu](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

Odkaz na entitu nejednoznačně liší jedinečné entity přidružením pojmenovaných entit uvedených v textu k konceptům, které se našly v předdefinované databázi konceptů. Například UMLS (Unified lékařské jazyky System).

> [!div class="mx-imgBorder"]
> ![Stav EL](../media/ta-for-health/health-entity-linking.png)

Analýza textu pro stav podporuje propojení se stavem a biozdravotnickými slovníky, které se nacházejí ve zdroji znalostí[Metathesaurus (Unified](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)lékařského jazyka).

#### <a name="negation-detection"></a>[Detekce negace](#tab/negation-detection) 

Význam lékařského obsahu je vysoce ovlivněn modifikátory, jako je například negace, což může mít kritické nevýznamy, pokud je chyba zjištěna chybně. Analýza textu pro stav podporuje detekci negace pro různé entity uvedené v textu. 

> [!div class="mx-imgBorder"]
> ![Zdravotní záporné](../media/ta-for-health/health-negation.png)

---

Úplný seznam podporovaných entit najdete v tématu [kategorie entit](../named-entity-types.md?tabs=health) vrácené funkcí analýza textu pro stav.

## <a name="supported-languages"></a>Podporované jazyky

Analýza textu pro stav podporuje pouze dokumenty v anglické jazykové verzi.

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Vyplňte a odešlete [formulář žádosti o Cognitive Services kontejnery](https://aka.ms/csgate) , který bude požadovat přístup ke kontejneru. V současné době se vám nebude účtovat Analýza textu pro využití stavu. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>Instalace kontejneru

Existuje několik způsobů, jak můžete nainstalovat a spustit kontejner. 

- Použijte [Azure Portal](text-analytics-how-to-install-containers.md?tabs=healthcare) k vytvoření prostředku analýza textu a k získání kontejneru použijte Docker.
- K automatizaci konfigurace kontejneru nasazení prostředků použijte následující skripty PowerShell a [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) .

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Instalace kontejneru pomocí Azure Web App for Containers

Azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) je prostředek Azure vyhrazený pro spouštění kontejnerů v cloudu. Přináší předem připravené funkce, jako je automatické škálování, podpora kontejnerů Docker a dokování Docker, podpora protokolu HTTPS a mnohem víc.

> [!NOTE]
> Pomocí Azure Web App se automaticky získá doména ve formě `<appservice_name>.azurewebsites.net`

Spuštěním tohoto skriptu PowerShellu pomocí Azure CLI vytvoříte Web App for Containers pomocí svého předplatného a image kontejneru přes HTTPS. Před odesláním první žádosti počkejte, než se skript dokončí (přibližně 25-30 minut).

```bash
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
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Instalace kontejneru pomocí Azure Container instance

K jednoduššímu nasazení můžete použít také službu Azure Container instance (ACI). ACI je prostředek, který umožňuje spouštět kontejnery Docker na vyžádání ve spravovaném prostředí Azure bez serveru. 

Postup nasazení prostředku ACI pomocí Azure Portal najdete v tématu [použití Azure Container Instances](text-analytics-how-to-use-container-instances.md) . Pomocí rozhraní příkazového řádku Azure můžete také použít skript prostředí PowerShell, který vytvoří ACI na vašem předplatném pomocí Image kontejneru.  Před odesláním první žádosti počkejte, než se skript dokončí (přibližně 25-30 minut).  V důsledku omezení maximálního počtu procesorů na prostředek ACI tuto možnost nevybírejte, pokud očekáváte, že budete odesílat více než 5 velkých dokumentů (přibližně 5000 znaků) na jednu žádost.
Informace o dostupnosti najdete v článku věnovaném [místní podpoře ACI](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability) . 

> [!NOTE] 
> Azure Container Instances Nezahrnovat podporu protokolu HTTPS pro předdefinované domény. Pokud potřebujete protokol HTTPS, budete ho muset ručně nakonfigurovat, včetně vytvoření certifikátu a registrace domény. Pokyny k tomu, jak to udělat s NGINX, najdete níže.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

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


## <a name="example-api-request"></a>Příklad požadavku rozhraní API
Kontejner poskytuje rozhraní API prediktivního koncového bodu pro dotazy založené na REST.  K dispozici jsme také nástroj pro vizualizaci v kontejneru, který je přístupný připojením **ukázky** ke koncovému bodu kontejneru, například:

```bash
http://<serverURL>:5000/demo
```

Použijte níže uvedený příklad žádosti o vytvoření dotazu do kontejneru, který jste nasadili, a nahraďte `serverURL` proměnnou odpovídající hodnotou.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Následující JSON je příklad souboru JSON připojeného k Analýza textu pro tělo žádosti rozhraní API pro stav:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>Tělo odpovědi rozhraní API

Následující JSON je příkladem Analýza textu pro tělo odpovědi rozhraní API pro stav:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>Výstup detekce negace

Při použití detekce negace může v některých případech jedna negace vybývat několika termíny najednou. Negace rozpoznané entity je reprezentovaná ve výstupu JSON hodnotou Boolean `isNegated` příznakem:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Výstup extrakce vztahu

Výstup extrakce vztahu obsahuje odkazy identifikátoru URI na *zdroj* vztahu a jeho *cíl*. Entity s rolí vztahu role `ENTITY` jsou přiřazeny k `target` poli. Entity s rolí vztahu role `ATTRIBUTE` jsou přiřazeny k `source` poli. Relace zkratek obsahují obousměrný `source` a `target` pole a `bidirectional` budou nastaveny na `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Kategorie pojmenovaných entit](../named-entity-types.md)
* [Co je nového](../whats-new.md)
