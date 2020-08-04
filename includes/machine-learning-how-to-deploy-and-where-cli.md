---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542765"
---
## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).
- Model. Pokud nemáte školený model, můžete použít soubory modelů a závislostí, které jsou k dispozici v [tomto kurzu](https://aka.ms/azml-deploy-cloud).
- [Rozšíření rozhraní příkazového řádku Azure (CLI) pro službu Machine Learning](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>Připojení k pracovnímu prostoru

Podle pokynů v dokumentaci k Azure CLI [nastavte kontext předplatného](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Pak postupujte takto:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

k zobrazení pracovních prostorů, ke kterým máte přístup.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrace modelu

Registrovaný model je logický kontejner pro jeden nebo více souborů, které tvoří model. Například pokud máte model uložený ve více souborech, můžete je zaregistrovat jako jeden model v pracovním prostoru. Po registraci souborů si pak můžete stáhnout nebo nasadit registrovaný model a získat všechny soubory, které jste zaregistrovali.

> [!TIP]
> Při registraci modelu zadáte cestu buď umístění v cloudu (z školicího běhu), nebo místního adresáře. Tato cesta je určena pouze k vyhledání souborů pro nahrání v rámci procesu registrace. Nemusí odpovídat cestě použité ve skriptu pro zadávání. Další informace najdete v tématu [vyhledání souborů modelu ve vstupním skriptu](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Modely strojového učení jsou zaregistrované ve vašem pracovním prostoru Azure Machine Learning. Model může pocházet z Azure Machine Learning nebo z někde jinde. Při registraci modelu můžete volitelně zadat metadata o modelu. `tags` `properties` Slovníky a, které použijete pro registraci modelu, lze použít k filtrování modelů.

Následující příklady ukazují, jak registrovat model.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrace modelu z školicího běhu Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

`--asset-path`Parametr odkazuje na cloudové umístění modelu. V tomto příkladu je použita cesta k jednomu souboru. Pokud chcete do registrace modelu zahrnout více souborů, nastavte `--asset-path` na cestu ke složce, která obsahuje soubory.

### <a name="register-a-model-from-a-local-file"></a>Registrace modelu z místního souboru

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Pokud chcete do registrace modelu zahrnout více souborů, nastavte `-p` na cestu ke složce, která obsahuje soubory.

Další informace o nástroji `az ml model register` najdete v [referenční dokumentaci](/cli/azure/ext/azure-cli-ml/ml/model).

## <a name="define-an-entry-script"></a>Definování skriptu pro zadávání

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Definovat odvozenou konfiguraci

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

Následující příkaz ukazuje, jak nasadit model pomocí rozhraní příkazového řádku:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

V tomto příkladu konfigurace určuje následující nastavení:

* Model vyžaduje Python.
* [Skript vstupu](#define-an-entry-script), který se používá ke zpracování webových požadavků odeslaných do nasazené služby
* Soubor Conda, který popisuje balíčky Pythonu potřebné pro odvození

Informace o použití vlastní image Docker s odvozenou konfigurací najdete v tématu [nasazení modelu pomocí vlastní image Docker](../articles/machine-learning/how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Zvolit cíl výpočetní technologie

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definování konfigurace nasazení

Možnosti, které jsou k dispozici pro konfiguraci nasazení, se liší v závislosti na zvoleném cílovém výpočetním prostředí.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

Další informace najdete v dokumentaci [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

## <a name="deploy-your-model"></a>Nasazení modelu

Teď jste připraveni nasadit svůj model.

### <a name="using-a-registered-model"></a>Použití registrovaného modelu

Pokud jste model zaregistrovali v pracovním prostoru Azure Machine Learning, nahraďte "MyModel: 1" názvem vašeho modelu a číslem verze.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Použití místního modelu

Pokud byste nechtěli zaregistrovat model, můžete předat parametr "sourceDirectory" v inferenceconfig.jsna, abyste určili místní adresář, ze kterého bude váš model sloužit.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```
## <a name="delete-resources"></a>Odstranění prostředků

Pokud chcete odstranit nasazenou webovou službu, použijte `az ml service <name of webservice>` .

Pokud chcete z pracovního prostoru odstranit registrovaný model, použijte`az ml model delete <model id>`

Přečtěte si další informace o [odstranění webové](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) služby a [odstranění modelu](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete) .