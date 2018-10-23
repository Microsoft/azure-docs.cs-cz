---
title: 'Rychlé zprovoznění: Použití sady Python SDK k vytvoření pracovního prostoru služby strojového učení – Azure Machine Learning'
description: Začněte se službou Azure Machine Learning. Nainstalujte sadu Python SDK a použijte ji k vytvoření pracovního prostoru. Tento pracovní prostor je základním blokem v cloudu, který můžete využít k experimentování, trénování a nasazování modelů strojového učení pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: cc348ca50b942b6b8b1474ed4dac4067d107a4af
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377995"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Rychlé zprovoznění: Použití Pythonu při začátcích se službou Azure Machine Learning

V tomto rychlém startu použijete sadu SDK služby Azure Machine Learning pro Python, abyste vytvořili a potom používali [pracovní prostor](concept-azure-machine-learning-architecture.md) služby Machine Learning. Tento pracovní prostor je základním blokem v cloudu, který můžete využít k experimentování, trénování a nasazování modelů strojového učení pomocí služby Machine Learning.

V tomto kurzu nainstalujete sadu Python SDK a naučíte se následující:

* Vytvořit pracovní prostor ve vašem předplatném Azure.
* Vytvořit pro tento pracovní prostor konfigurační soubor pro pozdější použití v jiných poznámkových blocích a skriptech.
* Napsat kód, který protokoluje hodnoty v pracovním prostoru.
* Zobrazit zaprotokolované hodnoty z vašeho pracovního prostoru.

V tomto rychlém startu vytvoříte pracovní prostor a konfigurační soubor. Můžete je použít i ke splnění požadavků v dalších kurzech a článcích s návody pro službu Machine Learning. Stejně jako u jiných služeb Azure má i služba Machine Learning svá omezení a kvóty. [Další informace o kvótách a postupu, jak požádat o zvýšení.](how-to-manage-quotas.md)

Do vašeho pracovního prostoru se automaticky přidají následující prostředky Azure, pokud jsou regionálně dostupné:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="install-the-sdk"></a>Instalace sady SDK

*Pokud používáte virtuální počítač pro datové vědy vytvořený po 27. září 2018, tuto část přeskočte.* Na těchto virtuálních počítačích pro datové vědy je sada Python SDK již předinstalovaná.

Před instalací sady SDK doporučujeme vytvořit izolované prostředí Pythonu. I když tento rychlý start používá aplikaci [Miniconda](https://conda.io/docs/user-guide/install/index.html), můžete také použít plnou nainstalovanou aplikaci [Anaconda](https://www.anaconda.com/) nebo [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalace aplikace Miniconda


[Stáhněte](https://conda.io/miniconda.html) a nainstalujte aplikaci Miniconda. Vyberte Python verze 3.7 nebo novější. Nevybírejte Python verze 2.x.

### <a name="create-an-isolated-python-environment"></a>Vytvoření izolovaného prostředí Pythonu 

Otevřete okno příkazového řádku. Pak vytvořte nové prostředí Conda `myenv` s Pythonem 3.6.

```sh
conda create -n myenv -y Python=3.6
```

Aktivujte prostředí.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Instalace sady SDK

Nainstalujte sadu SDK do aktivovaných prostředí Conda. Tento kód nainstaluje základní komponenty sady SDK služby Machine Learning. Kromě toho v prostředí Conda `myenv` nainstaluje server Jupyter Notebook. Instalace zabere **přibližně čtyři minuty**.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud chcete spustit Jupyter Notebook, zadejte tento příkaz.
```sh
jupyter notebook
```

V okně prohlížeče vytvořte pomocí výchozího jádra `Python 3` nový poznámkový blok. 

Pokud chcete zobrazit verzi sady SDK, zadejte následující kód Pythonu do buňky v poznámkovém bloku a spusťte ho.

```python
import azureml.core
print(azureml.core.VERSION)
```

Vytvořte novou skupinu prostředků Azure.

V [seznamu předplatných na portálu Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) najděte hodnotu pro `<azure-subscription-id>`. Použijte libovolné předplatné, ve kterém máte roli vlastníka nebo přispěvatele.

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region
                     )
```

Spuštění předcházejícího kódu může aktivovat nové okno prohlížeče, ve kterém se můžete přihlásit ke svému účtu Azure. Jakmile se přihlásíte, ověřovací token se uloží do místní mezipaměti.

Pokud chcete zobrazit podrobnosti o pracovním prostoru, například přidružené úložiště, registr kontejneru a trezor klíčů, zadejte následující kód.

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Zapsání konfiguračního souboru

Uložte podrobnosti pracovního prostoru do konfiguračního souboru v aktuálním adresáři. Tento soubor se nazývá ‚aml_config\config.json‘.  

Tento konfigurační soubor pracovního prostoru usnadňuje načtení stejného pracovního prostoru. Můžete ho načíst s jinými poznámkovými bloky a skripty ve stejném adresáři nebo podadresáři. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

Volání rozhraní API `write_config()` vytvoří konfigurační soubor v aktuálním adresáři. Soubor `config.json` obsahuje následující skript.

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Použití pracovního prostoru

Napište kód, který používá základní rozhraní API sady SDK ke sledování experimentálních spuštění.

```python
from azureml.core import Experiment

# create a new experiment
exp = Experiment(workspace=ws, name='myexp')

# start a run
run = exp.start_logging()

# log a number
run.log('my magic number', 42)

# log a list (Fibonacci numbers)
run.log_list('my list', [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]) 

# finish the run
run.complete()
```

## <a name="view-logged-results"></a>Zobrazení zaprotokolovaných výsledků
Po dokončení spuštění můžete experimentální spuštění zobrazit na webu Azure Portal. Následující kód použijte k tisku adresy URL k výsledkům posledního spuštění.

```python
print(run.get_portal_url())
```

Pokud chcete zobrazit zaprotokolované hodnoty na webu Azure Portal v prohlížeči, použijte odkaz.

![Zaprotokolované hodnoty na portálu](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků 
>[!IMPORTANT]
>Prostředky, které jste vytvořili, je možné použít i ke splnění požadavků v dalších kurzech a článcích s návody pro službu Machine Learning.

Pokud už zde vytvořené prostředky nebudete chtít dál používat, odstraňte je, aby se vám za ně neúčtovaly poplatky.

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Další kroky

Vytvořili jste prostředky potřebné k experimentování s modely a jejich nasazení. Také jste spustili kód v poznámkovém bloku. Kromě toho jste prozkoumali historii spuštění z tohoto kódu ve vašem pracovním prostoru v cloudu.

Abyste své prostředí mohli používat v kurzech služby Machine Learning, je potřeba do něj přidat několik dalších balíčků.

1. Zavřete poznámkový blok v prohlížeči.
1. V okně příkazového řádku použijte `Ctrl` + `C` a zastavte server poznámkového bloku.
1. Nainstalujte další balíčky.

    ```sh
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

Po dokončení instalace těchto balíčků postupujte podle kurzů věnovaných trénování a nasazení modelu. 

> [!div class="nextstepaction"]
> [Kurz: Školení modelu klasifikace obrázků](tutorial-train-models-with-aml.md)

Můžete prozkoumat také [pokročilejší příklady na GitHubu](https://aka.ms/aml-notebooks).
