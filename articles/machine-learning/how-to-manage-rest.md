---
title: Použití REST ke správě prostředků ML
titleSuffix: Azure Machine Learning
description: Jak používat rozhraní REST API k vytváření, spouštění a odstraňování prostředků Azure Machine Learning, jako je například pracovní prostor nebo registrování modelů.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 09a0580adbe6d51e4de811a57ee17203d65a2435
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316897"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Vytváření, spouštění a odstraňování prostředků Azure ML pomocí REST



K dispozici je několik způsobů, jak spravovat prostředky Azure ML. Můžete použít [portál](https://portal.azure.com/), [rozhraní příkazového řádku](/cli/azure/?preserve-view=true&view=azure-cli-latest)nebo [sadu Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py). Případně můžete zvolit REST API. REST API používá operace HTTP standardním způsobem k vytváření, načítání, aktualizaci a odstraňování prostředků. REST API funguje s jakýmkoli jazykem nebo nástrojem, který může provádět požadavky HTTP. Jednoduchá struktura je často vhodná pro vytváření skriptovacích prostředí a pro automatizaci MLOps. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Načtení autorizačního tokenu
> * Vytvoření správně formátované žádosti REST pomocí ověřování instančního objektu
> * Použití GET požadavků pro načtení informací o hierarchických prostředcích Azure ML
> * Použití požadavků PUT a POST k vytváření a úpravám prostředků
> * Použití požadavků DELETE k vyčištění prostředků 
> * Použití autorizace na základě klíčů k určení skóre nasazených modelů

## <a name="prerequisites"></a>Předpoklady

- **Předplatné Azure** , pro které máte práva správce. Pokud nemáte takové předplatné, vyzkoušejte [bezplatné nebo placené osobní předplatné](https://aka.ms/AMLFree) .
- [Pracovní prostor Azure Machine Learning](./how-to-manage-workspace.md)
- Žádosti REST pro správu používají ověřování instančního objektu. Postupujte podle kroků v části [nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy](./how-to-setup-authentication.md#service-principal-authentication) pro vytvoření instančního objektu v pracovním prostoru.
- Nástroj pro **otáčení** . **Oblý** program je k dispozici v [subsystému Windows pro Linux](/windows/wsl/install-win10) nebo distribuci systému UNIX. V PowerShellu je **kudrlinkou** alias pro **volání metody Invoke-WebRequest** a `curl -d "key=val" -X POST uri` bude se jednat o `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Načtení ověřovacího tokenu instančního objektu

Žádosti REST pro správu se ověřují pomocí implicitního toku OAuth2. Tento tok ověřování používá token poskytnutý instančním objektem vašeho předplatného. K načtení tohoto tokenu budete potřebovat:

- Vaše ID tenanta (Identifikace organizace, které patří do vašeho předplatného)
- ID klienta (které bude přidruženo k vytvořenému tokenu)
- Váš tajný klíč klienta (který byste měli chránit)

Tyto hodnoty by měly být z odpovědi na Vytvoření instančního objektu. Získání těchto hodnot je popsáno v tématu [nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy](./how-to-setup-authentication.md#service-principal-authentication). Pokud používáte předplatné vaší společnosti, možná nemáte oprávnění k vytvoření instančního objektu. V takovém případě byste měli použít buď [bezplatné, nebo placené osobní předplatné](https://aka.ms/AMLFree).

Načtení tokenu:

1. Otevřete okno terminálu.
1. Do příkazového řádku zadejte následující kód.
1. Nahraďte vlastní hodnoty pro `{your-tenant-id}` , a `{your-client-id}` `{your-client-secret}` . V celém tomto článku jsou řetězce obklopené složenými závorkami proměnné, které budete muset nahradit vlastními odpovídajícími hodnotami.
1. Spuštěním příkazu

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Odpověď by měla poskytnout token pro přístup, který je vhodný pro jednu hodinu:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Poznamenejte si token, protože ho budete používat k ověřování všech dalších požadavků na správu. Provedete to tak, že nastavíte hlavičku autorizace ve všech požadavcích:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Všimněte si, že hodnota začíná řetězcem "Bearer", včetně jednoho prostoru před přidáním tokenu.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Získat seznam skupin prostředků přidružených k vašemu předplatnému

Pokud chcete načíst seznam skupin prostředků přidružených k vašemu předplatnému, spusťte:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

V rámci Azure se publikuje mnoho rozhraní REST API. Každý poskytovatel služeb aktualizuje své rozhraní API na svém vlastním tempo, ale to bez přerušení stávajících programů. Poskytovatel služby používá `api-version` k zajištění kompatibility argument. `api-version`Argument se liší od služby po službu. Pro službu Machine Learning je například aktuální verze rozhraní API `2019-11-01` . V případě účtů úložiště je to `2019-06-01` . Pro trezory klíčů je to `2019-09-01` . Všechna volání REST by měla nastavit `api-version` argument na očekávanou hodnotu. Můžete spoléhat na syntaxi a sémantiku zadané verze, i když rozhraní API stále vyvíjí. Pokud odešlete požadavek poskytovateli bez `api-version` argumentu, odpověď bude obsahovat seznam podporovaných hodnot čitelných uživatelem. 

Výše uvedené volání bude mít za následek zkomprimovanou odpověď ve formátu JSON: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Přechod k podrobnostem v pracovních prostorech a jejich prostředcích

Pokud chcete načíst sadu pracovních prostorů ve skupině prostředků, spusťte následující příkaz, který nahradí `{your-subscription-id}` , `{your-resource-group}` a `{your-access-token}` : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Znovu obdržíte seznam JSON, tentokrát obsahující seznam, každou položku, které podrobně popisuje pracovní prostor:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Pokud chcete pracovat s prostředky v pracovním prostoru, přepínejte z obecného serveru **Management.Azure.com** na server REST API specifický pro umístění pracovního prostoru. Všimněte si hodnoty `discoveryUrl` klíče v předchozí odpovědi JSON. Pokud obdržíte tuto adresu URL, obdržíte nějakou odpověď, například:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Hodnota `api` odpovědi je adresa URL serveru, který budete používat pro další požadavky. Chcete-li zobrazit seznam experimentů, například odeslat následující příkaz. Nahraďte `regional-api-server` hodnotou `api` odpovědi (např `centralus.api.azureml.ms` .). Také nahraďte `your-subscription-id` ,, `your-resource-group` `your-workspace-name` a `your-access-token` jako obvykle:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Podobně pro načtení registrovaných modelů v pracovním prostoru odešlete:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Všimněte si, že pokud chcete zobrazit seznam experimentů, na které začíná cesta `history/v1.0` a kdy se mají vypisovat modely, začíná cesta `modelmanagement/v1.0` . REST API je rozdělen do několika operačních skupin, z nichž každá má odlišnou cestu. 

|Oblast|Cesta|
|-|-|
|Artifacts|/rest/api/azureml|
|Úložiště dat|/azure/machine-learning/how-to-access-data|
|Ladění hyperparametrů|Hyperdrive/v 1.0/|
|Modely|modelmanagement/v 1.0/|
|Historie spuštění|provádění/v 1.0/a historie/v 1.0/|

REST API můžete prozkoumat pomocí obecného vzoru:

|Komponenta adresy URL|Příklad|
|-|-|
| https://| |
| oblastní rozhraní API – Server/ | centralus.api.azureml.ms/ |
| operace – cesta/ | Historie/v 1.0/ |
| předplatné/{ID vašeho předplatného}/ | Subscriptions/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{vaše-Resource-Group}/ | resourceGroups/MyResourceGroup/ |
| poskytovatelé/operace – poskytovatel/ | Zprostředkovatelé/Microsoft. MachineLearningServices/ |
| poskytovatel – prostředek-cesta/ | pracovní prostory/MLWorkspace/MyWorkspace/FirstExperiment/spustí/1/ |
| operace – koncový bod/ | artefakty/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Vytváření a úpravy prostředků pomocí požadavků PUT a POST

Kromě načtení prostředků pomocí příkazu GET REST API podporuje vytváření všech prostředků potřebných ke školení, nasazení a monitorování řešení ML. 

Školicí a běžící modely ML vyžadují výpočetní prostředky. Můžete vypsat výpočetní prostředky pracovního prostoru pomocí: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Pokud chcete vytvořit nebo přepsat pojmenovaný výpočetní prostředek, použijte požadavek PUT. V následující části se kromě nově známých nahrazení,,, `your-subscription-id` `your-resource-group` `your-workspace-name` a `your-access-token` , náhradou `your-compute-name` a hodnot pro `location` , `vmSize` ,, `vmPriority` `scaleSettings` , `adminUserName` a `adminUserPassword` . Jak je uvedeno v odkazu na [odkaz na výpočetní prostředky služby Machine Learning – vytvořit nebo aktualizovat sadu SDK](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), následující příkaz vytvoří vyhrazený Standard_D1 s jedním uzlem (základní výpočetní prostředek procesoru), který se bude škálovat po 30 minutách:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> V terminálech Windows může být nutné při posílání dat JSON řídicí znaky vymezit. To znamená, že se jedná o text, jako je například `"location"` `\"location\"` . 

Úspěšná žádost obdrží `201 Created` odpověď, ale Všimněte si, že tato odpověď jednoduše znamená, že proces zřizování začal. Budete se muset dotazovat (nebo použít portál) k potvrzení úspěšného dokončení.

### <a name="create-an-experimental-run"></a>Vytvoření experimentálního spuštění

K zahájení běhu v experimentu potřebujete složku zip, která obsahuje školicí skript a související soubory, a soubor JSON s definicí spuštění. Složka zip musí mít v kořenovém adresáři vstupní soubor Pythonu. Například ZIP program triviálního Pythonu, jako je například následující, do složky s názvem **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Tento další fragment kódu uložte jako **definition.jsna**. Potvrďte, že hodnota "skript" odpovídá názvu souboru Pythonu, který jste právě nastavili. Potvrďte, že hodnota Target odpovídá názvu dostupného výpočetního prostředku. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Publikovat tyto soubory na serveru pomocí `multipart/form-data` obsahu:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Úspěšná žádost POST vygeneruje `200 OK` stav s textem odpovědi obsahujícím identifikátor vytvořeného běhu:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Běh můžete monitorovat pomocí vzoru REST-Vytvářejte, který by teď měl být známý:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Odstranit prostředky, které už nepotřebujete

Některé, ale ne všechny, prostředky podporují příkaz DELETE. Než se pustíte do REST API pro případy použití odstranění, ověřte [Reference k rozhraní API](/rest/api/azureml/) . Chcete-li odstranit model, například můžete použít:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Použití REST k určení skóre nasazeného modelu

I když je možné model nasadit, aby se ověřil s instančním objektem, většina nasazení klientů používá ověřování založené na klíčích. Příslušný klíč najdete na stránce pro nasazení na kartě **koncové body** v aplikaci Studio. Ve stejném umístění se zobrazí identifikátor URI bodování vašeho koncového bodu. Vstupy vašeho modelu musí být modelovány jako pole JSON s názvem `data` :

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Vytvoření pracovního prostoru pomocí REST 

Každý pracovní prostor Azure ML má závislost na čtyřech dalších prostředcích Azure: registr kontejnerů s povoleným správou, Trezor klíčů, Application Insights prostředek a účet úložiště. Pracovní prostor nemůžete vytvořit, dokud tyto prostředky neexistují. Podrobnosti o vytvoření každého takového prostředku najdete v referenčních informacích k REST API.

Chcete-li vytvořit pracovní prostor, vložte volání podobné následujícímu `management.azure.com` . I když toto volání vyžaduje, abyste nastavili velký počet proměnných, je strukturálně stejné jako jiné volání popsané v tomto článku. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Měli byste obdržet `202 Accepted` odpověď a v vrácených hlavičkách `Location` identifikátor URI. Tento identifikátor URI můžete získat pro informace o nasazení, včetně užitečných ladicích informací, pokud dojde k potížím s některým závislými prostředky (například pokud jste zapomněli povolit přístup správce k vašemu registru kontejneru). 

## <a name="troubleshooting"></a>Řešení potíží

### <a name="resource-provider-errors"></a>Chyby poskytovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Přesun pracovního prostoru

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.

### <a name="deleting-the-azure-container-registry"></a>Odstranění Azure Container Registry

Azure Machine Learning pracovní prostor používá pro některé operace Azure Container Registry (ACR). Automaticky vytvoří instanci ACR, když ji poprvé potřebuje.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Další kroky

- Projděte si [odkaz na úplný REST API AzureML](/rest/api/azureml/).
- Naučte se, jak pomocí návrháře [předpovídat cenu automobilu s návrhářem](./tutorial-designer-automobile-price-train-score.md).
- Prozkoumejte [Azure Machine Learning s poznámkovým blokem Jupyter](..//machine-learning/samples-notebooks.md).