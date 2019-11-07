---
title: Výuka a nasazování modelů z rozhraní příkazového řádku
titleSuffix: Azure Machine Learning
description: Naučte se používat rozšíření Machine Learning pro Azure CLI ke školení, registraci a nasazení modelu z příkazového řádku.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 1854599956755716955a6e691c3266ac54ddafd9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581555"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Kurz: výuka a nasazení modelu z rozhraní příkazového řádku
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto kurzu použijete rozšíření Machine Learning pro Azure CLI ke školení, registraci a nasazení modelu.

Školicí skripty Pythonu v tomto kurzu používají [scikit – Naučte](https://scikit-learn.org/) se naučit základní model. Fokus tohoto kurzu není ve skriptech nebo modelu, ale proces použití rozhraní příkazového řádku pro práci s Azure Machine Learning.

Přečtěte si, jak provést následující akce:

> [!div class="checklist"]
> * Instalace rozšíření Machine Learning
> * Vytvoření pracovního prostoru Azure Machine Learningu
> * Vytvoření výpočetního prostředku, který se používá ke výukě modelu
> * Spustit školicí běh
> * Registrace a stažení modelu
> * Nasazení modelu jako webové služby
> * Data skóre pomocí webové služby

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pokud chcete v tomto dokumentu použít příkazy rozhraní příkazového řádku z vašeho **místního prostředí**, potřebujete [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Použijete-li [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), k rozhraní příkazového řádku se dostanete v prohlížeči a v cloudu.

## <a name="download-the-example-project"></a>Stažení ukázkového projektu

Pro tento kurz Stáhněte projekt [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) . Soubory v adresářích `model-training` a `model-deployment` se používají v krocích v tomto kurzu.

Pokud chcete získat místní kopii souborů, buď [stáhněte archiv zip](https://github.com/microsoft/MLOps/archive/master.zip), nebo pomocí následujícího příkazu Gitu naklonujte úložiště:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Školicí soubory

`model-training` adresář obsahuje následující soubory, které se používají při výuce modelu:

* `.azureml\sklearn.runconfig`: __konfigurační soubor spuštění__ . Tento soubor definuje běhové prostředí potřebné ke školení modelu.
* `train-sklearn.py`: školicí skript. Tento soubor navlakuje model.
* `mylib.py`: pomocný modul, který používá `train-sklearn.py`.
* `training-env.yml`: definuje závislosti softwaru potřebné ke spuštění školicího skriptu.

Školicí skript používá datovou sadu diabetes, která je poskytována s scikit-učí pro výuku modelu.

### <a name="deployment-files"></a>Soubory nasazení

`model-deployment` adresář obsahuje následující soubory, které se používají k nasazení výukového modelu jako webové služby:

* `aciDeploymentConfig.yml`: __konfigurační soubor nasazení__ . Tento soubor definuje hostitelské prostředí potřebné pro model.
* `inferenceConfig.yml`: odvození souboru configuration__. Tento soubor definuje softwarové prostředí, které služba používá k určení skóre dat modelu.
* `score.py`: skript Pythonu, který přijímá příchozí data, vyhodnotí ho pomocí modelu a pak vrátí odpověď.
* `scoring-env.yml`: závislosti conda potřebné ke spuštění modelu a skriptu `score.py`.

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

Existuje několik způsobů, jak můžete z CLI ověřit předplatné Azure. Nejzákladnější je interaktivní ověřování pomocí prohlížeče. Chcete-li provést interaktivní ověřování, otevřete příkazový řádek nebo terminál a použijte následující příkaz:

```azurecli-interactive
az login
```

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je nutné otevřít prohlížeč a postupovat podle pokynů v příkazovém řádku. Pokyny zahrnují procházení [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadání autorizačního kódu.

## <a name="install-the-machine-learning-extension"></a>Instalace rozšíření Machine Learning

Pokud chcete nainstalovat rozšíření Machine Learning, použijte následující příkaz:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Pokud se zobrazí zpráva, že rozšíření je již nainstalováno, použijte následující příkaz, který aktualizuje na nejnovější verzi:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je základní kontejner prostředků na platformě Azure. Při práci s Azure Machine Learning bude skupina prostředků obsahovat váš pracovní prostor Azure Machine Learning. Bude také obsahovat další služby Azure, které pracovní prostor používá. Pokud například provedete svůj model pomocí cloudového výpočetního prostředku, vytvoří se tento prostředek ve skupině prostředků.

Pokud chcete __vytvořit novou skupinu prostředků__, použijte následující příkaz. Nahraďte `<resource-group-name>` názvem, který se má použít pro tuto skupinu prostředků. Nahraďte `<location>` oblastí Azure, kterou chcete použít pro tuto skupinu prostředků:

> [!TIP]
> Vyberte oblast, ve které je Azure Machine Learning k dispozici. Informace najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpověď z tohoto příkazu je podobná následujícímu kódu JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Další informace o práci se skupinami prostředků najdete v tématu [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Chcete-li vytvořit nový pracovní prostor, použijte následující příkaz. Nahraďte `<workspace-name>` názvem, který chcete použít pro tento pracovní prostor. Nahraďte `<resource-group-name>` názvem skupiny prostředků:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Připojit místní projekt k pracovnímu prostoru

V terminálu nebo příkazovém řádku použijte následující příkazy pro změnu adresářů do adresáře `mlops` a pak se připojte k pracovnímu prostoru:

```azurecli-interactive
cd ~/mlops
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/Code/MLOps/model-training",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Tento příkaz vytvoří soubor `.azureml/config.json`, který obsahuje informace potřebné pro připojení k vašemu pracovnímu prostoru. Zbývající příkazy `az ml` použité v tomto kurzu budou používat tento soubor, takže nemusíte přidávat pracovní prostor a skupinu prostředků do všech příkazů.

## <a name="create-the-compute-target-for-training"></a>Vytvoření cíle výpočetní služby pro školení

V tomto příkladu se pro výuku modelu používá virtuální počítač s poznámkovým blokem Azure Machine Learning. Pokud chcete vytvořit nový virtuální počítač s poznámkovým blokem, použijte následující příkaz:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu --max-nodes 4 --vm-size Standard_D2_V2
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "location": "<location>",
  "name": "cpu",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Tento příkaz vytvoří nový cíl výpočtů s názvem `cpu`s maximálně čtyřmi uzly. Vybraná velikost virtuálního počítače poskytuje virtuální počítač s prostředkem GPU. Informace o velikosti virtuálního počítače najdete v tématu [typy a velikosti virtuálních počítačů].

> [!IMPORTANT]
> Název cílového výpočetního prostředí (v tomto případě`cpu`) je důležitý. na něj odkazuje soubor `.azureml/sklearn.runconfig`, který se používá v další části.

## <a name="submit-the-training-run"></a>Odeslat školicí běh

Pokud chcete spustit školicí běh na `cpu` cílovém cíli, změňte adresáře na adresář `model-training` a pak použijte následující příkaz:

```azurecli-interactive
cd ~/mlops/model-training
az ml run submit-script -e myexperiment -c sklearn -d training-env.yml -t runoutput.json train-sklearn.py
```

Tento příkaz určuje název experimentu (`myexperiment`). Experiment ukládá informace o tomto běhu v pracovním prostoru.

Parametr `-c sklearn` určuje `.azureml/sklearn.runconfig` soubor. Jak už bylo zmíněno dříve, tento soubor obsahuje informace, které slouží ke konfiguraci prostředí používaného v rámci školicích běhů. Pokud tento soubor zkontrolujete, uvidíte, že odkazuje na `cpu` výpočetní cíl, který jste vytvořili dříve. Zobrazuje také počet uzlů, které se mají použít při výuce (`"nodeCount": "4"`), a obsahuje `"condaDependenciees"` oddíl se seznamem balíčků Pythonu potřebných ke spuštění školicího skriptu.

Další informace o spuštění konfiguračních souborů najdete v tématech [nastavení a použití výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)nebo na tento [soubor JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) , abyste viděli úplné schéma pro RunConfig.

Parametr `-t` ukládá odkaz na tento běh v souboru JSON a použije se v dalších krocích k registraci a stažení modelu.

V rámci školicích procesů IT streamuje informace z cvičení cvičení na vzdáleném výpočetním prostředku. Část informací se podobá následujícímu textu:

```text
alpha is 0.80, and mse is 3340.02
alpha is 0.85, and mse is 3349.36
alpha is 0.90, and mse is 3359.09
alpha is 0.95, and mse is 3369.13


The experiment completed successfully. Finalizing run...
Cleaning up all outstanding Run operations, waiting 300.0 seconds
```

Tento text se zaznamená do protokolu ze školicího skriptu (`train-sklearn.py`) a zobrazí dvě metriky výkonu pro tento model. V tomto případě chceme model s nejvyšší hodnotou alfa. Metriky výkonu jsou specifické pro model, který budete školením. Ostatní modely budou mít různé metriky výkonu.

Pokud provedete `train-sklearn.py`, všimnete si, že při ukládání vycvičených modelů do souboru používá také hodnotu alfa. V tomto případě si vlaky nahlásí několik modelů. Ten, který má nejvyšší hodnotu alfa, by měl být ten nejlepší. Jak vidíte výstup výše, a kód, model s Alpha 0,95 byl uložen jako `./outputs/ridge_0.95.pkl`

Model byl uložen do adresáře `./outputs` v cíli výpočetní služby, kde byl vyškolený. V tomto případě se jedná o virtuální počítač s Azure Machine Learning notebookem v cloudu Azure. Proces školení automaticky nahraje obsah adresáře `./outputs` z cíle výpočetního prostředí, kde k vašemu pracovnímu prostoru Azure Machine Learning přichází školení. Je uložen jako součást experimentu (`myexperiment` v tomto příkladu).

## <a name="register-the-model"></a>Registrace modelu

K registraci modelu přímo z uložené verze v experimentu použijte následující příkaz:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/ridge_0.95.pkl" -t registeredmodel.json
```

Tento příkaz zaregistruje soubor `outputs/ridge_0.95.pkl` vytvořený školením spustit jako novou registrací modelu s názvem `mymodel`. `--assets-path` odkazuje na cestu v experimentu. V tomto případě jsou informace o experimentech a spuštění načteny ze souboru `runoutput.json` vytvořeného pomocí školicího příkazu. `-t registeredmodel.json` vytvoří soubor JSON, který odkazuje na nový registrovaný model vytvořený tímto příkazem a je používán jinými příkazy rozhraní příkazového řádku, které pracují s registrovanými modely.

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Správa verzí modelů

Poznamenejte si číslo verze vrácené pro model. Verze se zvýší pokaždé, když zaregistrujete nový model s tímto názvem. Můžete například stáhnout model a zaregistrovat ho z místního souboru pomocí následujících příkazů:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "ridge_0.95.pkl"
```

První příkaz stáhne registrovaný model do aktuálního adresáře. Název souboru je `ridge_0.95.pkl`, což je soubor, na který se odkazuje při registraci modelu. Druhý příkaz registruje místní model (`-p "ridge_0.95.pkl"`) se stejným názvem jako předchozí registrace (`mymodel`). Tentokrát data JSON vrátí seznam verze jako 2.

## <a name="deploy-the-model"></a>Nasazení modelu

Model nasadíte tak, že změníte adresáře do adresáře `model-deployment` a pak použijete následující příkaz:

```azurecli-interactive
cd ~/mlops/model-deployment
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

Může se zobrazit zpráva "Nepodařilo se vytvořit klienta Docker". Tuto zprávu můžete ignorovat. Rozhraní příkazového řádku může nasadit webovou službu do místního kontejneru Docker a zkontroluje Docker. V tomto případě nepoužíváme místní nasazení.

Tento příkaz nasadí novou službu s názvem `myservice`s použitím verze 1 modelu, který jste předtím zaregistrovali.

Soubor `inferenceConfig.yml` poskytuje informace o tom, jak provést odvození, jako je například vstupní skript (`score.py`) a závislosti softwaru. Další informace o struktuře tohoto souboru naleznete v tématu [schéma konfigurace odvození](reference-azure-machine-learning-cli.md#inference-configuration-schema). Další informace o vstupních skriptech najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

`aciDeploymentConfig.yml` popisuje prostředí nasazení používané pro hostování služby. Konfigurace nasazení je specifická pro výpočetní typ, který používáte pro nasazení. V tomto případě se používá instance kontejneru Azure. Další informace najdete v tématu [schéma konfigurace nasazení](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Dokončení procesu nasazení bude trvat několik minut.

> [!TIP]
> V tomto příkladu se používá Azure Container Instances. Nasazení, která ACI automaticky vytvoří potřebný prostředek ACI. Pokud jste se místo toho chtěli nasadit do služby Azure Kubernetes, musíte cluster AKS vytvořit předem a zadat ho jako součást příkazu `az ml model deploy`. Příklad nasazení na AKS najdete v tématu [nasazení modelu do clusteru služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

Po několika minutách se vrátí informace podobné následujícímu formátu JSON:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>Identifikátor URI pro vyhodnocování

`scoringUri` vrácená z nasazení je koncový bod REST pro model nasazený jako webovou službu. Tento identifikátor URI můžete získat také pomocí následujícího příkazu:

```azurecli-interactive
az ml service show -n myservice
```

Tento příkaz vrátí stejný dokument JSON, včetně `scoringUri`.

Koncový bod REST lze použít k odeslání dat službě. Informace o vytváření klientských aplikací, které odesílají data do služby, najdete v tématu věnovaném [využívání modelu Azure Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md) .

### <a name="send-data-to-the-service"></a>Odeslat data do služby

I když můžete vytvořit klientskou aplikaci pro volání koncového bodu, služba Machine Learning CLI poskytuje nástroj, který může fungovat jako testovací klient. K odeslání testovacích dat do služby použijte následující příkaz:

```azurecli-interactive
az ml service run -n myservice -d '{"data":[[1,2,3,4,5,6,7,8,9,10]]}'
```

Odpověď z příkazu je podobná `[4684.920839774082]`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

> [!IMPORTANT]
> Prostředky, které jste vytvořili, je možné použít i ke splnění požadavků v dalších kurzech a článcích s návody pro službu Azure Machine Learning.

### <a name="delete-deployed-service"></a>Odstranit nasazenou službu

Pokud plánujete pokračovat v používání pracovního prostoru Azure Machine Learning, ale chcete se zbavit nasazené služby za účelem snížení nákladů, použijte následující příkaz:

```azurecli-interactive
az ml service delete -n myservice
```

Tento příkaz vrátí dokument JSON, který obsahuje název odstraněné služby. Odstranění služby může trvat několik minut.

### <a name="delete-the-training-compute"></a>Odstranění školicích výpočtů

Pokud plánujete, že budete nadále používat Azure Machine Learning pracovní prostor, ale chcete se zbavit `cpu`ho cíle výpočtů vytvořeného pro školení, použijte následující příkaz:

```azurecli-interactive
az ml computetarget delete -n cpu
```

Tento příkaz vrátí dokument JSON, který obsahuje ID odstraněného cíle výpočtů. Může to trvat několik minut, než se cíl výpočetního prostředí odstranil.

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít prostředky, které jste vytvořili, odstraňte je, abyste se neúčtují další poplatky.

Pokud chcete odstranit skupinu prostředků a všechny prostředky Azure vytvořené v tomto dokumentu, použijte následující příkaz. Nahraďte `<resource-group-name>` názvem skupiny prostředků, kterou jste vytvořili dříve:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Další kroky

V tomto Azure Machine Learning kurzu jste pro tyto úlohy použili rozhraní příkazového učení pro Machine Learning:

> [!div class="checklist"]
> * Instalace rozšíření Machine Learning
> * Vytvoření pracovního prostoru Azure Machine Learningu
> * Vytvoření výpočetního prostředku, který se používá ke výukě modelu
> * Spustit školicí běh
> * Registrace a stažení modelu
> * Nasazení modelu jako webové služby
> * Data skóre pomocí webové služby

Další informace o použití rozhraní příkazového řádku najdete v tématu [použití rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
