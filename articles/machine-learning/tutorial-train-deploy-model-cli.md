---
title: Trénování a nasazování modelů z cli
titleSuffix: Azure Machine Learning
description: Zjistěte, jak použít rozšíření strojového učení pro Azure CLI k trénování, registraci a nasazení modelu z příkazového řádku.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1cafc311c842cd5bc17fefe34eacbdfc99b7147a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617737"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Kurz: Trénování a nasazování modelu z cli
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto kurzu použijete rozšíření strojového učení pro Azure CLI trénovat, registrovat a nasazovat model.

Školicí skripty Pythonu v tomto kurzu používají [scikit-learn](https://scikit-learn.org/) k trénování základního modelu. Zaměření tohoto kurzu není na skripty nebo model, ale proces použití příkazového příkazového příkazu pro práci s Azure Machine Learning.

Přečtěte si, jak provést následující akce:

> [!div class="checklist"]
> * Instalace rozšíření strojového učení
> * Vytvoření pracovního prostoru Azure Machine Learning
> * Vytvoření výpočetního prostředku použitého k trénování modelu
> * Definování a registrace datové sady použité k trénování modelu
> * Zahájení tréninkového běhu
> * Registrace a stažení modelu
> * Nasazení modelu jako webové služby
> * Skóre dat pomocí webové služby

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* Chcete-li použít příkazy příkazu příkazu příkazu příkazového příkazu v tomto dokumentu z **místního prostředí**, potřebujete [příkazové příkazové příkazy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Pokud používáte [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), cli je přístupná prostřednictvím prohlížeče a žije v cloudu.

## <a name="download-the-example-project"></a>Stažení ukázkového projektu

Pro účely tohoto [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) kurzu stáhněte projekt. Soubory v `examples/cli-train-deploy` adresáři jsou používány kroky v tomto kurzu.

Chcete-li získat místní kopii souborů, [stáhněte si archiv ZIP](https://github.com/microsoft/MLOps/archive/master.zip)nebo použijte následující příkaz Git ke klonování úložiště:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Školicí soubory

Adresář `examples/cli-train-deploy` z projektu obsahuje následující soubory, které se používají při trénování modelu:

* `.azureml\mnist.runconfig`: Spustit __konfigurační__ soubor. Tento soubor definuje runtime prostředí potřebné k trénování modelu. V tomto příkladu také připojí data použitá k trénování modelu do trénovacího prostředí.
* `scripts\train.py`: Tréninkový skript. Tento soubor trénuje model.
* `scripts\utils.py`: Pomocný soubor používaný školicím skriptem.
* `.azureml\conda_dependencies.yml`: Definuje softwarové závislosti potřebné ke spuštění školicího skriptu.
* `dataset.json`: Definice datové sady. Slouží k registraci datové sady MNIST v pracovním prostoru Azure Machine Learning.

### <a name="deployment-files"></a>Soubory nasazení

Úložiště obsahuje následující soubory, které se používají k nasazení trénovaného modelu jako webové služby:

* `aciDeploymentConfig.yml`: __Konfigurační__ soubor nasazení. Tento soubor definuje hostitelské prostředí potřebné pro model.
* `inferenceConfig.json`: __Odvozený konfigurační__ soubor. Tento soubor definuje softwarové prostředí používané službou ke skóre dat s modelem.
* `score.py`: Skript pythonu, který přijímá příchozí data, skóruje pomocí modelu a pak vrátí odpověď.
* `scoring-env.yml`: Conda závislosti potřebné ke spuštění `score.py` modelu a skriptu.
* `testdata.json`: Datový soubor, který lze použít k testování nasazené webové služby.

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

Existuje několik způsobů, jak můžete ověřit na vaše předplatné Azure z cli. Nejzákladnější je interaktivní ověření pomocí prohlížeče. Chcete-li se interaktivně ověřit, otevřete příkazový řádek nebo terminál a použijte následující příkaz:

```azurecli-interactive
az login
```

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je třeba otevřít prohlížeč a postupovat podle pokynů na příkazovém řádku. Pokyny zahrnují procházení [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadání autorizačního kódu.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>Instalace rozšíření strojového učení

Chcete-li nainstalovat rozšíření strojového učení, použijte následující příkaz:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Pokud se zobrazí zpráva, že rozšíření je již nainstalováno, aktualizujte na nejnovější verzi pomocí následujícího příkazu:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je základní kontejner prostředků na platformě Azure. Při práci s Azure Machine Learning bude skupina prostředků obsahovat váš pracovní prostor Azure Machine Learning. Bude také obsahovat další služby Azure používané v pracovním prostoru. Například pokud trénujete model pomocí cloudového výpočetního prostředku, tento prostředek se vytvoří ve skupině prostředků.

Chcete-li __vytvořit novou skupinu prostředků__, použijte následující příkaz. Nahraďte `<resource-group-name>` název, který má být pro tuto skupinu prostředků používán. Nahraďte `<location>` oblastí Azure, která se použije pro tuto skupinu prostředků:

> [!TIP]
> Měli byste vybrat oblast, kde azure machine learning je k dispozici. Další informace naleznete v [tématu Produkty dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpověď z tohoto příkazu je podobná následující JSON:

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

Další informace o práci se skupinami zdrojů naleznete [v tématu az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Chcete-li vytvořit nový pracovní prostor, použijte následující příkaz. Nahraďte `<workspace-name>` název, který chcete použít pro tento pracovní prostor. Nahraďte `<resource-group-name>` se názvem skupiny prostředků:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu jsonu:

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

## <a name="connect-local-project-to-workspace"></a>Připojení místního projektu k pracovnímu prostoru

Z terminálu nebo příkazového řádku použijte následující příkazy, změňte adresáře do `cli-train-deploy` adresáře a připojte se k pracovnímu prostoru:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu jsonu:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Tento příkaz `.azureml/config.json` vytvoří soubor, který obsahuje informace potřebné pro připojení k pracovnímu prostoru. Ostatní `az ml` příkazy použité v tomto kurzu budou používat tento soubor, takže není třeba přidávat pracovní prostor a skupinu prostředků do všech příkazů.

## <a name="create-the-compute-target-for-training"></a>Vytvoření výpočetního cíle pro školení

Tento příklad používá cluster Azure Machine Learning Compute k trénování modelu. Chcete-li vytvořit nový výpočetní cluster, použijte následující příkaz:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

Výstup tohoto příkazu je podobný následujícímu jsonu:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Tento příkaz vytvoří nový `cpu-cluster`výpočetní cíl s názvem , s maximálně čtyřmi uzly. Vybraná velikost virtuálního počítače poskytuje virtuální ho virtuálního počítače s prostředkem GPU. Informace o velikosti virtuálního počítače najdete v tématu [typy a velikosti virtuálních počítačů].

> [!IMPORTANT]
> Název výpočetního cíle`cpu-cluster` ( v tomto případě) je důležitý; odkazuje na `.azureml/mnist.runconfig` něj soubor použitý v další části.

## <a name="define-the-dataset"></a>Definování datové sady

Chcete-li trénovat model, můžete poskytnout trénovací data pomocí datové sady. Chcete-li vytvořit datovou sadu z vázacích, musíte zadat definiční soubor datové sady. Soubor `dataset.json` uvedený v repo vytvoří novou datovou sadu pomocí dat MNIST. Vytvoří se datová `mnist-dataset`sada s názvem .

Chcete-li datovou `dataset.json` sadu zaregistrovat pomocí souboru, použijte následující příkaz:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

Výstup tohoto příkazu je podobný následujícímu jsonu:

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
> Zkopírujte hodnotu `id` položky, jak se používá v další části.

Chcete-li zobrazit komplexnější šablonu pro datovou sadu, použijte následující příkaz:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Odkaz na soubor dat

Chcete-li datovou sadu zpřístupnit v trénovacím prostředí, musíte na ni odkazovat ze souboru runconfig. Soubor `.azureml/mnist.runconfig` obsahuje následující položky YAML:

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

Změňte hodnotu `id` položky tak, aby odpovídala hodnotě vrácené při registraci datové sady. Tato hodnota se používá k načtení dat do cíle výpočetní ho během trénování.

Výsledkem tohoto yaml během tréninku jsou následující akce:

* Připojí datovou sadu (na základě ID datové sady) v trénovacím prostředí a `mnist` uloží cestu k přípojné sadě v proměnné prostředí.
* Předá umístění dat (přípojný bod) uvnitř trénovacího prostředí skriptu pomocí argumentu. `--data-folder`

Soubor runconfig také obsahuje informace používané ke konfiguraci prostředí používaného trénovacím spuštěním. Pokud tento soubor zkontrolujete, uvidíte, `cpu-compute` že odkazuje na výpočetní cíl, který jste vytvořili dříve. Také uvádí počet uzlů, které se`"nodeCount": "4"`mají použít `"condaDependencies"` při trénování ( ) a obsahuje oddíl, který obsahuje balíčky Pythonu potřebné ke spuštění školicího skriptu.

> [!TIP]
> Zatímco je možné ručně vytvořit soubor runconfig, soubor v tomto `generate-runconfig.py` příkladu byl vytvořen pomocí souboru obsaženého v úložišti. Tento soubor získá odkaz na registrovanou datovou sadu, vytvoří spustit konfigurační programově a pak ji zachová do souboru.

Další informace o spuštění konfiguračních souborů najdete [v tématu Nastavení a použití výpočetních cílů pro trénování modelu](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli). Úplný odkaz JSON naleznete v [souboru runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

## <a name="submit-the-training-run"></a>Odeslat tréninkový běh

Chcete-li spustit trénovací běh na `cpu-cluster` výpočetní cíl, použijte následující příkaz:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Tento příkaz určuje název experimentu`myexperiment`( ). Experiment ukládá informace o tomto spuštění v pracovním prostoru.

Parametr `-c mnist` určuje `.azureml/mnist.runconfig` soubor.

Parametr `-t` ukládá odkaz na toto spuštění v souboru JSON a bude použit v dalších krocích k registraci a stažení modelu.

Jako procesy spuštění školení, streamuje informace z relace školení na vzdálené výpočetní prostředek. Část informací je podobná následujícímu textu:

```output
Predict the test set
Accuracy is 0.9185
```

Tento text je zaznamenán z trénovacího skriptu a zobrazuje přesnost modelu. Ostatní modely budou mít různé metriky výkonu.

Pokud zkontrolujete trénovací skript, zjistíte, že také používá hodnotu alfa, když ukládá trénovaný model do `outputs/sklearn_mnist_model.pkl`.

Model byl uložen `./outputs` do adresáře na výpočetní cíl, kde byl trénovaný. V tomto případě instance Azure Machine Learning Compute v cloudu Azure. Proces školení automaticky nahraje obsah `./outputs` adresáře z výpočetního cíle, kde dojde k trénování do pracovního prostoru Azure Machine Learning. Je uložen jako součást experimentu`myexperiment` (v tomto příkladu).

## <a name="register-the-model"></a>Registrace modelu

Chcete-li model zaregistrovat přímo z uložené verze experimentu, použijte následující příkaz:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Tento příkaz zaregistruje `outputs/sklearn_mnist_model.pkl` soubor vytvořený trénovacím `mymodel`spuštěním jako registraci nového modelu s názvem . Odkazuje `--assets-path` na cestu v experimentu. V tomto případě jsou informace o experimentu a spuštění načteny ze souboru `runoutput.json` vytvořeného trénovacím příkazem. Vytvoří `-t registeredmodel.json` soubor JSON, který odkazuje na nový registrovaný model vytvořený tímto příkazem a používá se jinými příkazy příkazového příkazu, které pracují s registrovanými modely.

Výstup tohoto příkazu je podobný následujícímu jsonu:

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

### <a name="model-versioning"></a>Správa verzí modelu

Všimněte si čísla verze vrácené pro model. Verze se zintáží při každé registraci nového modelu s tímto názvem. Model můžete například stáhnout a zaregistrovat z místního souboru pomocí následujících příkazů:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

První příkaz stáhne registrovaný model do aktuálního adresáře. Název souboru `sklearn_mnist_model.pkl`je , což je soubor odkazovaný při registraci modelu. Druhý příkaz zaregistruje místní`-p "sklearn_mnist_model.pkl"`model ( ) se stejným`mymodel`názvem jako předchozí registrace ( ). Tentokrát vrácená data JSON uvádí verzi jako 2.

## <a name="deploy-the-model"></a>Nasazení modelu

Chcete-li nasadit model, použijte následující příkaz:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Může se zobrazit upozornění o "Nepodařilo se zkontrolovat existenci localwebservice" nebo "Nepodařilo se vytvořit klienta Dockeru". Můžete to bez obav ignorovat, protože nenasazujete místní webovou službu.

Tento příkaz nasadí `myservice`novou službu s názvem pomocí verze 1 modelu, který jste dříve zaregistrovali.

Soubor `inferenceConfig.yml` obsahuje informace o tom, jak použít model pro odvození. Odkazuje například na vstupní skript`score.py`( ) a softwarové závislosti.

Další informace o struktuře tohoto souboru naleznete [v tématu Inference schéma konfigurace](reference-azure-machine-learning-cli.md#inference-configuration-schema). Další informace o vstupních skriptech najdete v [tématu Nasazení modelů pomocí Azure Machine Learning](how-to-deploy-and-where.md#prepare-to-deploy).

Popisuje `aciDeploymentConfig.yml` prostředí nasazení používané k hostování služby. Konfigurace nasazení je specifická pro typ výpočetních prostředků, který používáte pro nasazení. V takovém případě se používá instance kontejneru Azure. Další informace naleznete ve [schématu konfigurace nasazení](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Bude trvat několik minut, než se proces nasazení dokončí.

> [!TIP]
> V tomto příkladu se používá Azure Container Instances. Nasazení do ACI automaticky vytvoří potřebný prostředek ACI. Pokud byste místo toho nasadit do služby Azure Kubernetes, musíte vytvořit cluster AKS předem a zadat jako součást příkazu. `az ml model deploy` Příklad nasazení do AKS najdete v [tématu Nasazení modelu do clusteru služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

Po několika minutách jsou vráceny informace podobné následujícímu JSON:

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

### <a name="the-scoring-uri"></a>Bodovací identifikátor URI

Vrácené `scoringUri` z nasazení je koncový bod REST pro model nasazený jako webová služba. Tento identifikátor URI můžete získat také pomocí následujícího příkazu:

```azurecli-interactive
az ml service show -n myservice
```

Tento příkaz vrátí stejný dokument JSON, včetně `scoringUri`.

Koncový bod REST lze použít k odeslání dat do služby. Informace o vytvoření klientské aplikace, která odesílá data do služby, najdete v [tématu využití modelu Azure Machine Learning nasazeného jako webová služba.](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Odeslání dat službě

Zatímco můžete vytvořit klientskou aplikaci pro volání koncového bodu, zařazování strojového učení CLI poskytuje nástroj, který může fungovat jako testovací klient. K odeslání dat v souboru `testdata.json` do služby použijte následující příkaz:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Pokud používáte PowerShell, použijte místo toho následující příkaz:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Odpověď z příkazu je `[ 3 ]`podobná .

## <a name="clean-up-resources"></a>Vyčištění prostředků

> [!IMPORTANT]
> Prostředky, které jste vytvořili, je možné použít i ke splnění požadavků v dalších kurzech a článcích s návody pro službu Azure Machine Learning.

### <a name="delete-deployed-service"></a>Odstranit nasazenou službu

Pokud plánujete pokračovat v používání pracovního prostoru Azure Machine Learning, ale chcete se zbavit nasazené služby ke snížení nákladů, použijte následující příkaz:

```azurecli-interactive
az ml service delete -n myservice
```

Tento příkaz vrátí dokument JSON, který obsahuje název odstraněné služby. Může trvat několik minut, než je služba odstraněna.

### <a name="delete-the-training-compute"></a>Odstranění trénovacího výpočetního výpočtu

Pokud plánujete pokračovat v používání pracovního prostoru Azure Machine Learning, `cpu-cluster` ale chcete se zbavit výpočetního cíle vytvořeného pro školení, použijte následující příkaz:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Tento příkaz vrátí dokument JSON, který obsahuje ID odstraněného výpočetního cíle. Může trvat několik minut, než byl cíl výpočetního prostředí odstraněn.

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít prostředky, které jste vytvořili, odstraňte je, abyste vám nevznikly další poplatky.

Chcete-li odstranit skupinu prostředků a všechny prostředky Azure vytvořené v tomto dokumentu, použijte následující příkaz. Nahraďte `<resource-group-name>` název skupiny prostředků, kterou jste vytvořili dříve:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu Azure Machine Learning jste použili cli strojového učení pro následující úkoly:

> [!div class="checklist"]
> * Instalace rozšíření strojového učení
> * Vytvoření pracovního prostoru Azure Machine Learning
> * Vytvoření výpočetního prostředku použitého k trénování modelu
> * Definování a registrace datové sady použité k trénování modelu
> * Zahájení tréninkového běhu
> * Registrace a stažení modelu
> * Nasazení modelu jako webové služby
> * Skóre dat pomocí webové služby

Další informace o použití příkazového příkazového příkazu [najdete v tématu Použití rozšíření příkazového příkazového příkazu pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
