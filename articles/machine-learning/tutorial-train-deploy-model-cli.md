---
title: Výuka a nasazování modelů z rozhraní příkazového řádku
titleSuffix: Azure Machine Learning
description: Naučte se používat rozšíření Machine Learning pro Azure CLI ke školení, registraci a nasazení modelu z příkazového řádku.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 119f1eaa838b404a3dbdc22f692f559816810dde
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578684"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Kurz: výuka a nasazení modelu z rozhraní příkazového řádku


V tomto kurzu použijete rozšíření Machine Learning pro Azure CLI ke školení, registraci a nasazení modelu.

Školicí skripty Pythonu v tomto kurzu používají [scikit – Naučte](https://scikit-learn.org/) se naučit jednoduchý model. Fokus tohoto kurzu není ve skriptech nebo modelu, ale proces použití rozhraní příkazového řádku pro práci s Azure Machine Learning.

Přečtěte si, jak provést následující akce:

> [!div class="checklist"]
> * Instalace rozšíření Machine Learning
> * Vytvoření pracovního prostoru Azure Machine Learning
> * Vytvoření výpočetního prostředku, který se používá ke výukě modelu
> * Definovat a zaregistrovat datovou sadu, která se používá ke výukě modelu
> * Spustit školicí běh
> * Registrace a stažení modelu
> * Nasazení modelu jako webové služby
> * Data skóre pomocí webové služby

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pokud chcete v tomto dokumentu použít příkazy rozhraní příkazového řádku z vašeho **místního prostředí** , potřebujete [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

    Použijete-li [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), k rozhraní příkazového řádku se dostanete v prohlížeči a v cloudu.

## <a name="download-the-example-project"></a>Stažení ukázkového projektu

Pro tento kurz stáhněte [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) projekt. Soubory v `examples/cli-train-deploy` adresáři jsou používány kroky v tomto kurzu.

Pokud chcete získat místní kopii souborů, buď [stáhněte archiv zip](https://github.com/microsoft/MLOps/archive/master.zip), nebo pomocí následujícího příkazu Gitu naklonujte úložiště:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Školicí soubory

`examples/cli-train-deploy`Adresář z projektu obsahuje následující soubory, které se používají při výuce modelu:

* `.azureml\mnist.runconfig`: __Konfigurační soubor spuštění__ . Tento soubor definuje běhové prostředí potřebné ke školení modelu. V tomto příkladu také připojí data používaná ke výuce modelu do školicího prostředí.
* `scripts\train.py`: Školicí skript. Tento soubor navlakuje model.
* `scripts\utils.py`: Pomocný soubor používaný skriptem pro školení.
* `.azureml\conda_dependencies.yml`: Definuje závislosti softwaru potřebné ke spuštění školicího skriptu.
* `dataset.json`: Definice datové sady. Slouží k registraci datové sady MNIST ručně zapsaných v pracovním prostoru Azure Machine Learning.

### <a name="deployment-files"></a>Soubory nasazení

Úložiště obsahuje následující soubory, které se používají k nasazení výukového modelu jako webové služby:

* `aciDeploymentConfig.yml`: __Konfigurační soubor nasazení__ . Tento soubor definuje hostitelské prostředí potřebné pro model.
* `inferenceConfig.json`: __Odvození konfiguračního__ souboru. Tento soubor definuje softwarové prostředí, které služba používá k určení skóre dat modelu.
* `score.py`: Skript v jazyce Python, který přijímá příchozí data, vyhodnotí ho pomocí modelu a pak vrátí odpověď.
* `scoring-env.yml`: Závislosti conda potřebné ke spuštění modelu a `score.py` skriptu.
* `testdata.json`: Datový soubor, který lze použít k otestování nasazené webové služby.

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

Existuje několik způsobů, jak můžete z CLI ověřit předplatné Azure. Nejjednodušším je interaktivní ověřování pomocí prohlížeče. Chcete-li provést interaktivní ověřování, otevřete příkazový řádek nebo terminál a použijte následující příkaz:

```azurecli-interactive
az login
```

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je nutné otevřít prohlížeč a postupovat podle pokynů v příkazovém řádku. Pokyny zahrnují procházení [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadávání autorizačního kódu.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

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

Skupina prostředků je kontejner prostředků na platformě Azure. Při práci s Azure Machine Learning bude skupina prostředků obsahovat váš pracovní prostor Azure Machine Learning. Bude také obsahovat další služby Azure, které pracovní prostor používá. Pokud například provedete svůj model pomocí cloudového výpočetního prostředku, vytvoří se tento prostředek ve skupině prostředků.

Pokud chcete __vytvořit novou skupinu prostředků__ , použijte následující příkaz. Nahraďte `<resource-group-name>` názvem, který se má použít pro tuto skupinu prostředků. Nahraďte `<location>` oblastí Azure, kterou chcete použít pro tuto skupinu prostředků:

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

Další informace o práci se skupinami prostředků najdete v tématu [AZ Group](/cli/azure/group?preserve-view=true&view=azure-cli-latest).

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

V terminálu nebo příkazovém řádku použijte následující příkazy pro změnu adresáře do `cli-train-deploy` adresáře a pak se připojte k pracovnímu prostoru:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Tento příkaz vytvoří `.azureml/config.json` soubor, který obsahuje informace potřebné pro připojení k vašemu pracovnímu prostoru. Zbývající `az ml` příkazy použité v tomto kurzu budou používat tento soubor, takže nemusíte přidávat pracovní prostor a skupinu prostředků do všech příkazů.

## <a name="create-the-compute-target-for-training"></a>Vytvoření cíle výpočetní služby pro školení

V tomto příkladu se pro výuku modelu používá výpočetní cluster Azure Machine Learning. Chcete-li vytvořit nový výpočetní cluster, použijte následující příkaz:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Tento příkaz vytvoří nový cíl výpočtů `cpu-cluster` s názvem s maximálně čtyřmi uzly. Vybraná velikost virtuálního počítače poskytuje virtuální počítač s prostředkem GPU. Informace o velikosti virtuálního počítače najdete v tématu [typy a velikosti virtuálních počítačů].

> [!IMPORTANT]
> Název cílového výpočetního `cpu-cluster` prostředí (v tomto případě) je důležitý. na něj odkazuje `.azureml/mnist.runconfig` soubor použitý v následující části.

## <a name="define-the-dataset"></a>Definovat datovou sadu

Pokud chcete vytvořit výuku modelu, můžete zadat školicí data pomocí datové sady. Chcete-li vytvořit datovou sadu z rozhraní příkazového řádku, je nutné zadat soubor definice datové sady. `dataset.json`Soubor uvedený v úložišti vytvoří novou datovou sadu pomocí dat mnist ručně zapsaných. Datová sada, kterou vytváří, má název `mnist-dataset` .

Chcete-li datovou sadu zaregistrovat pomocí `dataset.json` souboru, použijte následující příkaz:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Zkopírujte hodnotu `id` položky, jak je používáno v další části.

Chcete-li zobrazit komplexnější šablonu pro datovou sadu, použijte následující příkaz:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Odkaz na datovou sadu

Aby byla datová sada dostupná ve školicím prostředí, musíte na ni odkazovat ze souboru RunConfig. `.azureml/mnist.runconfig`Soubor obsahuje následující položky YAML:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Změňte hodnotu `id` položky tak, aby odpovídala hodnotě vrácené při registraci datové sady. Tato hodnota se používá k načtení dat do výpočetního cíle během školení.

Tato YAML má za následek následující akce během školení:

* Připojí datovou sadu (na základě ID datové sady) ve školicím prostředí a uloží cestu k přípojnému bodu v `mnist` proměnné prostředí.
* Předá do skriptu pomocí argumentu umístění dat (přípojný bod) uvnitř školicího prostředí `--data-folder` .

Soubor RunConfig obsahuje také informace, které slouží ke konfiguraci prostředí používaného v rámci školicího běhu. Pokud tento soubor zkontrolujete, uvidíte, že odkazuje na `cpu-compute` výpočetní cíl, který jste vytvořili dříve. Zobrazuje také počet uzlů, které se mají použít při výuce ( `"nodeCount": "4"` ), a obsahuje `"condaDependencies"` část, která obsahuje seznam balíčků Python potřebných ke spuštění školicího skriptu.

> [!TIP]
> I když je možné soubor RunConfig vytvořit ručně, byl v tomto příkladu vytvořen pomocí souboru, který je `generate-runconfig.py` součástí úložiště. Tento soubor získá odkaz na registrovanou datovou sadu, vytvoří programově config a pak ho přetrvá do souboru.

Další informace o spuštění konfiguračních souborů najdete v tématu [použití výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md#whats-a-run-configuration). Úplný odkaz na JSON najdete v [runconfigschema.jsna](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Odeslat školicí běh

Pokud chcete spustit školicí běh na `cpu-cluster` cílovém výpočetním cíli, použijte následující příkaz:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Tento příkaz určuje název experimentu ( `myexperiment` ). Experiment ukládá informace o tomto běhu v pracovním prostoru.

`-c mnist`Parametr určuje `.azureml/mnist.runconfig` soubor.

`-t`Parametr ukládá odkaz na tento běh v souboru JSON a bude použit v dalších krocích k registraci a stažení modelu.

V rámci školicích procesů IT streamuje informace z cvičení cvičení na vzdáleném výpočetním prostředku. Část informací se podobá následujícímu textu:

```output
Predict the test set
Accuracy is 0.9185
```

Tento text se zaznamená do protokolu ze školicího skriptu a zobrazí přesnost modelu. Ostatní modely budou mít různé metriky výkonu.

Pokud provedete kontrolu školicího skriptu, všimnete si, že při ukládání školicího modelu do nástroje používá také hodnotu alfa `outputs/sklearn_mnist_model.pkl` .

Model byl uložen do `./outputs` adresáře ve výpočetním cíli, kde byl vyškolený. V tomto případě Azure Machine Learning výpočetní instance v cloudu Azure. Proces školení automaticky nahraje obsah `./outputs` adresáře z cílového výpočetního prostředí, kde k vašemu pracovnímu prostoru Azure Machine Learning dojde k výuce. Je uložen jako součást experimentu ( `myexperiment` v tomto příkladu).

## <a name="register-the-model"></a>Registrace modelu

K registraci modelu přímo z uložené verze v experimentu použijte následující příkaz:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Tento příkaz zaregistruje `outputs/sklearn_mnist_model.pkl` soubor vytvořený pomocí školicího běhu jako novou registraci modelu s názvem `mymodel` . `--assets-path`Odkazuje na cestu v experimentu. V tomto případě jsou informace o experimentech a spuštění načteny ze `runoutput.json` souboru vytvořeného pomocí příkazu pro školení. `-t registeredmodel.json`Vytvoří soubor JSON, který odkazuje na nový registrovaný model vytvořený tímto příkazem a je používán jinými příkazy rozhraní příkazového řádku, které pracují s registrovanými modely.

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
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

První příkaz stáhne registrovaný model do aktuálního adresáře. Název souboru je `sklearn_mnist_model.pkl` , což je soubor, na který se odkazuje při registraci modelu. Druhý příkaz registruje místní model ( `-p "sklearn_mnist_model.pkl"` ) se stejným názvem jako předchozí registrace ( `mymodel` ). Tentokrát data JSON vrátí seznam verze jako 2.

## <a name="deploy-the-model"></a>Nasazení modelu

Chcete-li nasadit model, použijte následující příkaz:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Může se zobrazit upozornění týkající se selhání při kontrole existence LocalWebservice nebo vytvoření klienta Docker. Tuto možnost můžete bez obav ignorovat, protože neprovádíte nasazení místní webové služby.

Tento příkaz nasadí novou službu s názvem s `myservice` použitím verze 1 modelu, který jste předtím zaregistrovali.

`inferenceConfig.yml`Soubor poskytuje informace o tom, jak použít model pro odvození. Například odkazuje na vstupní skript ( `score.py` ) a závislosti softwaru.

Další informace o struktuře tohoto souboru naleznete v tématu [schéma konfigurace odvození](reference-azure-machine-learning-cli.md#inference-configuration-schema). Další informace o vstupních skriptech najdete v tématu [nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md).

`aciDeploymentConfig.yml`Popisuje prostředí nasazení používané pro hostování služby. Konfigurace nasazení je specifická pro výpočetní typ, který používáte pro nasazení. V tomto případě se používá instance kontejneru Azure. Další informace najdete v tématu [schéma konfigurace nasazení](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Dokončení procesu nasazení bude trvat několik minut.

> [!TIP]
> V tomto příkladu se používá Azure Container Instances. Nasazení, která ACI automaticky vytvoří potřebný prostředek ACI. Pokud jste se místo toho chtěli nasadit do služby Azure Kubernetes, musíte cluster AKS vytvořit předem a zadat ho jako součást `az ml model deploy` příkazu. Příklad nasazení na AKS najdete v tématu [nasazení modelu do clusteru služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

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

`scoringUri`Vráceným nasazením je koncový bod REST pro model nasazený jako webovou službu. Tento identifikátor URI můžete získat také pomocí následujícího příkazu:

```azurecli-interactive
az ml service show -n myservice
```

Tento příkaz vrátí stejný dokument JSON, včetně `scoringUri` .

Koncový bod REST lze použít k odeslání dat službě. Informace o vytváření klientských aplikací, které odesílají data do služby, najdete v tématu věnovaném [využívání modelu Azure Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md) .

### <a name="send-data-to-the-service"></a>Odeslat data do služby

I když můžete vytvořit klientskou aplikaci pro volání koncového bodu, služba Machine Learning CLI poskytuje nástroj, který může fungovat jako testovací klient. K odeslání dat v `testdata.json` souboru do služby použijte následující příkaz:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Pokud používáte PowerShell, použijte místo toho následující příkaz:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Odpověď z příkazu je podobná `[ 3 ]` .

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

Pokud plánujete, že budete nadále používat Azure Machine Learning pracovní prostor, ale chcete se zbavit `cpu-cluster` výpočetního cíle vytvořeného pro školení, použijte následující příkaz:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
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
> * Vytvoření pracovního prostoru Azure Machine Learning
> * Vytvoření výpočetního prostředku, který se používá ke výukě modelu
> * Definovat a zaregistrovat datovou sadu, která se používá ke výukě modelu
> * Spustit školicí běh
> * Registrace a stažení modelu
> * Nasazení modelu jako webové služby
> * Data skóre pomocí webové služby

Další informace o použití rozhraní příkazového řádku najdete v tématu [použití rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).