---
title: Použití rest ke správě prostředků ML
titleSuffix: Azure Machine Learning
description: Použití rest API k vytvoření, spuštění a odstranění prostředků Azure ML
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580624"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Vytvoření, spuštění a odstranění prostředků Azure ML pomocí rest

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Existuje několik způsobů, jak spravovat prostředky Azure ML. Můžete použít [portál](https://portal.azure.com/), [rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)nebo [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Nebo můžete zvolit rozhraní REST API. Rozhraní REST API používá http slovesa standardním způsobem k vytváření, načítání, aktualizaci a odstraňování prostředků. Rozhraní REST API pracuje s libovolným jazykem nebo nástrojem, který může provádět požadavky HTTP. Rest je přímočará struktura často dělá to dobrá volba ve skriptovacích prostředích a pro automatizaci MLOps. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Načtení autorizačního tokenu
> * Vytvoření správně formátovaného požadavku REST pomocí ověřování instančního objektu
> * Použití požadavků GET k načtení informací o hierarchických prostředcích Azure ML
> * Použití požadavků PUT a POST k vytvoření a úpravě prostředků
> * Čištění prostředků pomocí požadavků DELETE 
> * Použití autorizace založené na klíči ke skóre nasazených modelů

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure,** pro které máte práva správce. Pokud takové předplatné nemáte, vyzkoušejte [bezplatné nebo placené osobní předplatné.](https://aka.ms/AMLFree)
- Pracovní [prostor Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Požadavky REST pro správu používají ověřování instančního objektu. Postupujte podle kroků v [části Nastavení ověřování pro prostředky a pracovní postupy Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) a vytvořte instanční objekt ve vašem pracovním prostoru.
- **Nástroj curl.** Curl **curl** program je k dispozici v [podsystému Windows pro Linux](https://aka.ms/wslinstall/) nebo jakékoliv unixové distribuce. V prostředí PowerShell je **curl** alias pro `curl -d "key=val" -X POST uri` `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` **Invoke-WebRequest** a stane se . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Načtení ověřovacího tokenu instančního objektu

Požadavky REST pro správu jsou ověřeny pomocí implicitního toku OAuth2. Tento tok ověřování používá token poskytovaný instancí služby vašeho předplatného. Chcete-li načíst tento token, budete potřebovat:

- ID vašeho klienta (identifikace organizace, do které vaše předplatné patří)
- ID vašeho klienta (které bude přidruženo k vytvořenému tokenu)
- Váš klient skýtavá tajemství (které byste měli chránit)

Tyto hodnoty byste měli mít z odpovědi na vytvoření instančního objektu, jak je popsáno v [části Nastavení ověřování pro prostředky a pracovní postupy Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication). Pokud používáte předplatné společnosti, pravděpodobně nemáte oprávnění k vytvoření instančního objektu. V takovém případě byste měli použít [bezplatné nebo placené osobní předplatné](https://aka.ms/AMLFree).

Chcete-li načíst token:

1. Otevřete okno terminálu.
1. Zadejte na příkazovém řádku následující kód.
1. Nahraďte vlastní `{your-tenant-id}` `{your-client-id}`hodnoty `{your-client-secret}`pro , a . V tomto článku řetězce obklopené složených zvlněnými závorkami jsou proměnné, které budete muset nahradit vlastními příslušnými hodnotami.
1. Spuštěním příkazu

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Odpověď by měla poskytnout přístupový token dobré po dobu jedné hodiny:

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

Poznamenejte si token, protože jej použijete k ověření všech následných požadavků na správu. U děláte to tak, že ve všech požadavcích nastavíte hlavičku Autorizace:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Všimněte si, že hodnota začíná řetězcem "Nosič " včetně jedné mezery před přidáním tokenu.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Získání seznamu skupin prostředků přidružených k vašemu předplatnému

Chcete-li načíst seznam skupin prostředků přidružených k vašemu předplatnému, spusťte:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

V rámci Azure se publikuje mnoho rest api. Každý poskytovatel služeb aktualizuje své rozhraní API na vlastní kadenci, ale činí tak bez porušení stávajících programů. Poskytovatel služeb používá `api-version` argument k zajištění kompatibility. Argument `api-version` se liší od služby ke službě. Pro službu Machine Learning, například aktuální `2019-11-01`verze rozhraní API je . Pro účty úložiště je `2019-06-01`to . Pro trezory klíčů je `2019-09-01`to . Všechna volání REST `api-version` by měla nastavit argument na očekávanou hodnotu. Můžete se spolehnout na syntaxi a sémantiku zadané verze i v případě, že rozhraní API se nadále vyvíjí. Pokud odešlete požadavek zprostředkovateli bez argumentu, `api-version` odpověď bude obsahovat seznam podporovaných hodnot čitelný pro člověka. 

Výše uvedené volání bude mít za následek zhutněnou odpověď JSON formuláře: 

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


## <a name="drill-down-into-workspaces-and-their-resources"></a>Přechod k podrobnostem pracovních prostorů a jejich zdrojů

Chcete-li načíst sadu pracovních prostorů ve skupině prostředků, `{your-subscription-id}`spusťte `{your-access-token}`následující, napovědující a: `{your-resource-group}` 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Opět obdržíte seznam JSON, tentokrát obsahující seznam, z nichž každá položka podrobně popisuje pracovní prostor:

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

Chcete-li pracovat s prostředky v rámci pracovního prostoru, přepnete z obecného **serveru management.azure.com** na server rozhraní REST API specifický pro umístění pracovního prostoru. Všimněte si `discoveryUrl` hodnoty klíče ve výše uvedené odpovědi JSON. Pokud se vám tato adresa URL zobrazí, obdržíte odpověď jako:

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

Hodnota `api` odpovědi je adresa URL serveru, který budete používat pro další požadavky. Chcete-li například uvést experimenty, odešlete následující příkaz. Nahraďte `regional-api-server` hodnotou `api` odpovědi `centralus.api.azureml.ms`(například). Rovněž `your-subscription-id` `your-resource-group`nahraďte , , `your-workspace-name`, a `your-access-token` jako obvykle:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Podobně chcete-li načíst registrované modely ve vašem pracovním prostoru, odešlete:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Všimněte si, že seznam `history/v1.0` experimentů cesta začíná while do `modelmanagement/v1.0`seznamu modelů, cesta začíná . ROZHRANÍ REST API je rozděleno do několika operačních skupin, z nichž každá má odlišnou cestu. Referenční dokumenty rozhraní API na níže uvedených odkazech uvádějí operace, parametry a kódy odpovědí pro různé operace.

|Oblast|Cesta|Odkaz|
|-|-|-|
|Artefakty|artefakt/v2.0/|[Reference k rozhraní REST API](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Úložiště dat|úložiště dat/v1.0/|[Reference k rozhraní REST API](https://docs.microsoft.com/rest/api/azureml/datastores)|
|Ladění hyperparametrů|hyperdrive/v1.0/|[Reference k rozhraní REST API](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modely|řízení modelu/v1.0/|[Reference k rozhraní REST API](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Historie spuštění|provedení/v1.0/ a historie/v1.0/|[Reference k rozhraní REST API](https://docs.microsoft.com/rest/api/azureml/runs)|

Rozhraní REST API můžete prozkoumat pomocí obecného vzoru:

|Komponenta URL|Příklad|
|-|-|
| https://| |
| regional-api-server/ | centralus.api.azureml.ms/ |
| provozní cesta/ | historie/v1.0/ |
| předplatné/{vaše předplatné-id}/ | předplatné/abcde123-abab-abab-1234-0123456789/ |
| resourceGroups/{your-resource-group}/ | resourceGroups/MyResourceGroup/ |
| poskytovatelé/poskytovatel provozu/ | poskytovatelů/Microsoft.MachineLearningServices/ |
| cesta zprostředkovatele-prostředku/ | pracovní prostory/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| provozní koncový bod/ | artefakty/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Vytváření a úpravy prostředků pomocí požadavků PUT a POST

Kromě načítání prostředků pomocí příkazu GET podporuje rozhraní REST API vytváření všech prostředků potřebných k trénování, nasazování a monitorování řešení ML. 

Trénování a spouštění modelů ML vyžaduje výpočetní prostředky. Výpočetní prostředky pracovního prostoru můžete vypsat pomocí: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Chcete-li vytvořit nebo přepsat pojmenovaný výpočetní prostředek, použijete požadavek PUT. `your-subscription-id`Kromě nyní známých substitucí , `your-resource-group`, `your-workspace-name`a `your-access-token`, `your-compute-name`náhražky a hodnot pro `location`, `vmSize` `vmPriority`, `scaleSettings`, `adminUserName`, a `adminUserPassword`. Jak je uvedeno v odkazu na [Machine Learning Compute – vytvoření nebo aktualizace sdk reference](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), následující příkaz vytvoří vyhrazené, jednouzelový Standard_D1 (základní výpočetní prostředek procesoru), který se zmenšit po 30 minutách:

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
> V terminálech systému Windows může být při odesílání dat JSON možná muset uniknout symbolům dvojité nabídky. To znamená, že `"location"` `\"location\"`text, například stane . 

Úspěšný požadavek obdrží `201 Created` odpověď, ale všimněte si, že tato odpověď jednoduše znamená, že proces zřizování byl zahájen. Budete muset dotazování (nebo pomocí portálu) potvrdit jeho úspěšné dokončení.

### <a name="create-an-experimental-run"></a>Vytvoření experimentálního běhu

Chcete-li spustit spuštění v rámci experimentu, potřebujete složku zip obsahující trénovací skript a související soubory a soubor JSON s pusťte definici. Složka zip musí mít soubor položky Pythonu v kořenovém adresáři. Jako příklad můžete zazipovat triviální program Pythonu, například následující, do složky nazvané **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Uložte tento další úryvek jako **definition.json**. Potvrďte, že hodnota "Skript" odpovídá názvu souboru Pythonu, který jste právě zazipovali. Potvrďte, že hodnota "Cíl" odpovídá názvu dostupného výpočetního prostředku. 

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

Po těchto souborů na `multipart/form-data` server pomocí obsahu:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Úspěšný požadavek POST `200 OK` vygeneruje stav s tělem odpovědi obsahujícím identifikátor vytvořeného spuštění:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Spustit můžete sledovat pomocí vzoru REST-ful, který by měl být nyní známý:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Odstranění prostředků, které již nepotřebujete

Některé, ale ne všechny prostředky podporují příkaz DELETE. Zkontrolujte [odkaz na rozhraní API](https://docs.microsoft.com/rest/api/azureml/) před potvrzením rozhraní REST API pro odstranění případů použití. Chcete-li odstranit model, například můžete použít:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Použití REST k sekretáži nasazeného modelu

I když je možné nasadit model tak, aby se ověřoval pomocí instančního objektu, většina klientských nasazení používá ověřování na základě klíče. Příslušný klíč najdete na stránce nasazení na kartě **Koncové body** aplikace Studio. Stejné umístění zobrazí identifikátor URI skóre koncového bodu. Vstupy modelu musí být modelovány jako pole JSON s názvem `data`:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Vytvoření pracovního prostoru pomocí funkce REST 

Každý pracovní prostor Azure ML má závislost na čtyřech dalších prostředcích Azure: registru kontejnerů s povolenou správou, trezorklíčů, prostředek Application Insights a účet úložiště. Pracovní prostor nelze vytvořit, dokud tyto prostředky neexistují. Podrobnosti o vytvoření každého takového prostředku naleznete v odkazu rozhraní REST API.

Chcete-li vytvořit pracovní prostor, zadejte `management.azure.com`volání podobné následujícímu písmenu . Zatímco toto volání vyžaduje, abyste nastavili velký počet proměnných, je strukturálně identické s jinými voláními, o kterých tento článek diskutoval. 

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

Měli byste `202 Accepted` obdržet odpověď a ve vrácených `Location` záhlavích identifikátor URI. Tento identifikátor URI můžete získat pro informace o nasazení, včetně užitečných informací o ladění, pokud došlo k potížím s jedním z vašich závislých prostředků (například pokud jste zapomněli povolit přístup správce v registru kontejneru). 

## <a name="troubleshooting"></a>Řešení potíží

### <a name="resource-provider-errors"></a>Chyby zprostředkovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Přesunutí pracovního prostoru

> [!WARNING]
> Přesunutí pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutí vlastního předplatného do nového tenanta není podporováno. To může způsobit chyby.

### <a name="deleting-the-azure-container-registry"></a>Odstranění registru kontejnerů Azure

Pracovní prostor Azure Machine Learning používá azure kontejnerregistru (ACR) pro některé operace. Automaticky vytvoří instanci ACR, když ji poprvé potřebuje.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Další kroky

- Prozkoumejte úplný [odkaz na rozhraní API AzureML REST](https://docs.microsoft.com/rest/api/azureml/).
- Naučte se používat Studio & Designer [předpovědět ceny automobilů s návrhářem (náhled)](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Prozkoumejte [Azure Machine Learning pomocí poznámkových bloků Jupyter](https://docs.microsoft.com/azure//machine-learning/samples-notebooks).
