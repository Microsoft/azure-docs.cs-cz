---
title: 'Rychlé zprovoznění: Použití sady Python SDK k vytvoření pracovního prostoru služby strojového učení – Azure Machine Learning'
description: Začněte se službou Azure Machine Learning.  Nainstalujte sadu Python SDK a použijte ji k vytvoření pracovního prostoru. Tento pracovní prostor je základním blokem v cloudu pro experimentování, školení a nasazení modelů strojového učení pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: ee24c1797d0f52d2529ed583a0cfe90cc9e27035
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067752"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Rychlé zprovoznění: Použití Pythonu při začátcích se službou Azure Machine Learning

V tomto rychlém startu použijete sadu SDK služby Azure Machine Learning pro Python, abyste vytvořili a potom používali [pracovní prostor](concept-azure-machine-learning-architecture.md) služby Machine Learning. Tento pracovní prostor je základním blokem pro experimentování, školení a nasazení modelů strojového učení v cloudu pomocí služby Azure Machine Learning.

V tomto kurzu budete instalovat sadu SDK pro Python a:
* vytvoříte pracovní prostor ve vašem předplatném Azure,
* vytvoříte konfigurační soubor pro tento pracovní prostor pro pozdější použití v jiných poznámkových blocích a skriptech,
* napíšete kód, který zaznamenává hodnoty v pracovním prostoru,
* zobrazíte zaprotokolované hodnoty z vašeho pracovního prostoru.

Pracovní prostor a jeho konfigurační soubor, které v tomto rychlém startu vytvoříte, můžete použít i ke splnění požadavků v dalších kurzech a článcích s návody pro službu Azure Machine Learning. Stejně jako u jiných služeb Azure má i služba Azure Machine Learning svá omezení a kvóty. [Další informace o kvótách a postupu, jak požádat o zvýšení.](how-to-manage-quotas.md)

Abychom vám usnadnili práci, jsou do pracovního prostoru automaticky přidané tyto prostředky Azure, pokud jsou regionálně dostupné: [registr kontejneru](https://azure.microsoft.com/services/container-registry/), [úložiště](https://azure.microsoft.com/services/storage/), [Application Insights](https://azure.microsoft.com/services/application-insights/) a [trezor klíčů](https://azure.microsoft.com/services/key-vault/).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


##  <a name="install-the-sdk"></a>Instalace sady SDK

**Tuto část přeskočte, pokud používáte** počítač DSVM (Data Science Virtual Machine) vytvořený po 27. září 2018, protože tyto počítače DSVM se dodávají s předinstalovanou sadou Python SDK.

Před instalací sady SDK doporučujeme nejdříve vytvořit izolované prostředí Pythonu. I když tento rychlý start používá aplikaci [Miniconda](https://conda.io/docs/user-guide/install/index.html), můžete také použít plnou nainstalovanou aplikaci [Anaconda](https://www.anaconda.com/) nebo [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalace aplikace Miniconda


[Stáhněte](https://conda.io/miniconda.html) a nainstalujte aplikaci Miniconda. Zvolte verzi Pythonu 3.7 nebo novější. Nevybírejte verzi Pythonu 2.x.

### <a name="create-an-isolated-python-environment"></a>Vytvoření izolovaného prostředí Pythonu 

Spusťte okno příkazového řádku a pomocí Pythonu 3.6 vytvořte nové prostředí Conda s názvem `myenv`.

```sh
conda create -n myenv -y Python=3.6
```

Aktivujte prostředí.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Instalace sady SDK

Nainstalujte sadu SDK do aktivovaných prostředí Conda. Tento kód nainstaluje základní součásti sady SDK Azure Machine Learning spolu se serverem Jupyter Notebook v prostředí Conda `myenv`.  Instalace zabere **přibližně čtyři minuty**.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Zadáním tohoto příkazu spusťte Jupyter Notebook.
```sh
jupyter notebook
```

V okně prohlížeče vytvořte pomocí výchozího jádra `Python 3` nový poznámkový blok. 

Zadáním následujícího kódu Pythonu do buňky v poznámkovém bloku a jeho spuštěním zobrazte verzi sady SDK.

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

Spuštění předcházejícího kódu může aktivovat nové okno prohlížeče, ve kterém se můžete přihlásit ke svému účtu Azure. Jakmile se přihlásíte, ověřovací token bude uložen do místní mezipaměti.

Pokud chcete zobrazit podrobnosti o pracovním prostoru včetně přidruženého úložiště, registru kontejneru a trezoru klíče, zadejte následující:

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Zapsání konfiguračního souboru

Uložte podrobnosti pracovního prostoru do konfiguračního souboru v aktuálním adresáři. Tento soubor se nazývá ‚aml_config\config.json‘.  

Tento konfigurační soubor pracovního prostoru usnadňuje načtení toho samého pracovního prostoru později s jinými poznámkovými bloky a skripty ve stejném adresáři nebo podadresáři. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

Volání rozhraní API `write_config()` vytvoří konfigurační soubor v aktuálním adresáři. Soubor `config.json` obsahuje následující:

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

# create a new experiemnt
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

![zaprotokolované hodnoty na portálu](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků 
>[!IMPORTANT]
>Prostředky, které jste vytvořili, je možné použít i ke splnění požadavků v dalších kurzech a článcích s návody pro službu Azure Machine Learning.

Pokud zde vytvořené prostředky nebudete používat, odstraňte vytvořené prostředky pro tento rychlý start, aby se vám za ně neúčtovaly poplatky.

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Další kroky

Teď máte vytvořené prostředky potřebné k tomu, abyste mohli začít experimentovat a nasazovat modely. Také jste spustili kód v poznámkovém bloku a prozkoumali historii spuštění z tohoto kódu ve vašem pracovním prostoru v cloudu.

Budete potřebovat několik dalších balíčků ve vašem prostředí, které použijete s kurzy pro Azure Machine Learning:

1. Zavřete poznámkový blok v prohlížeči.
1. V okně příkazového řádku použijte `Ctrl` + `C` a zastavte server poznámkového bloku.
1. Nainstalujte další balíčky.

    ```sh
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

Po instalaci těchto balíčků postupujte podle kurzů, aby se naučili vytvořit a nasadit model.  

> [!div class="nextstepaction"]
> [Kurz: Školení modelu klasifikace obrázků](tutorial-train-models-with-aml.md)

Můžete také prozkoumat [pokročilejší příklady na Githubu](https://aka.ms/aml-notebooks).
