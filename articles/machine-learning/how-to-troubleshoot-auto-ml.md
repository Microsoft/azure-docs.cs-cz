---
title: Řešení automatických experimentů ML
titleSuffix: Azure Machine Learning
description: Naučte se řešit potíže s automatizovanými experimenty strojového učení a řešit problémy.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: 28aac830326d60161f54d7ad5fa03326c1d66462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563670"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Řešení automatizovaných experimentů ML v Pythonu

V této příručce se dozvíte, jak identifikovat a vyřešit známé problémy v automatizovaných experimentech strojového učení pomocí [sady Azure Machine Learning SDK](/python/api/overview/azure/ml/intro).

## <a name="version-dependencies"></a>Závislosti verzí

**`AutoML` závislosti na novější verzi balíčku při přerušení kompatibility**. Po 1.13.0 verze SDK nejsou modely načteny do starších sad SDK z důvodu nekompatibility mezi staršími verzemi připnutémi v předchozích `AutoML` balíčcích a novějšími verzemi, které dnes připnuté.

Očekávat chyby, jako například:

* V modulu nebyly nalezeny chyby, jako například,

  `No module named 'sklearn.decomposition._truncated_svd`

* Chyby importu, například,

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Chyby atributů, jako například,

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

Řešení závisí na `AutoML` verzi školení sady SDK:

* Pokud `AutoML` je vaše verze školení SDK větší než 1.13.0, budete potřebovat `pandas == 0.25.1` a `scikit-learn==0.22.1` .

    * Pokud dojde k neshodě verzí, upgradujte scikit-učí a/nebo PANDAS na správnou verzi s následujícím:

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Pokud `AutoML` je vaše verze školení SDK menší nebo rovna 1.12.0, budete potřebovat `pandas == 0.23.4` a `sckit-learn==0.20.3` .
  * Pokud dojde k neshodě verzí, downgrade scikit-učí a/nebo PANDAS na správnou verzi s následujícím:
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Nastavení

`AutoML` změny balíčků od verze 1.0.76 vyžadují odinstalaci předchozí verze před aktualizací na novou verzi.

* **`ImportError: cannot import name AutoMLConfig`**

    Pokud dojde k této chybě po upgradu z verze sady SDK před verzí v 1.0.76 na verzi v 1.0.76 nebo novější, vyřešte tuto chybu spuštěním: `pip uninstall azureml-train automl` a potom `pip install azureml-train-automl` . Skript automl_setup. cmd to provede automaticky.

* **automl_setup selžou**

  * Ve Windows spusťte automl_setup z příkazového řádku Anaconda. [Nainstalujte Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Ujistěte se, že je nainstalovaná verze conda 64 4.4.10 nebo novější. Pomocí příkazu můžete zjistit bit `conda info` . `platform`Měla by být `win-64` pro Windows nebo `osx-64` pro Mac. K ověření verze použijte příkaz `conda -V` . Pokud máte nainstalovanou předchozí verzi, můžete ji aktualizovat pomocí příkazu: `conda update conda` . Chcete-li zjistit 32-bit spuštěním 

  * Ujistěte se, že je nainstalovaná conda. 

  * Linux `gcc: error trying to exec 'cc1plus'`

    1. Pokud `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` dojde k chybě, nainstalujte nástroje pro sestavení RSZ pro distribuci systému Linux. Například v Ubuntu použijte příkaz `sudo apt-get install build-essential` .

    1. Pokud chcete automl_setup vytvořit nové prostředí Conda, předejte nový název jako první parametr. Zobrazit existující prostředí conda pomocí `conda env list` a odebrat je pomocí `conda env remove -n <environmentname>` .

* **automl_setup_linux. sh se nezdařila**: Pokud se v Ubuntu Linux automl_setup_linus. sh, došlo k chybě: `unable to execute 'gcc': No such file or directory`

  1. Ujistěte se, že jsou povolené Odchozí porty 53 a 80. Na virtuálním počítači Azure to můžete udělat z Azure Portal tím, že vyberete virtuální počítač a kliknete na **sítě**.
  1. Spusťte příkaz: `sudo apt-get update`
  1. Spusťte příkaz: `sudo apt-get install build-essential --fix-missing`
  1. Spustit `automl_setup_linux.sh` znovu

* **konfigurace. ipynb se nezdařila**:

  * Pro místní conda nejdříve zajistěte, aby `automl_setup` byl úspěšně spuštěn.
  * Ujistěte se, že je subscription_id správná. Subscription_id v Azure Portal Najděte tak, že vyberete všechny služby a potom předplatné. Znaky "<" a ">" by neměly být zahrnuty do hodnoty subscription_id. Například `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` má platný formát.
  * Zajistěte přístup k předplatnému pro přispěvatele nebo vlastníka.
  * Ověřte, zda je oblast jednou z podporovaných oblastí: `eastus2` , `eastus` , `westcentralus` , `southeastasia` , `westeurope` , `australiaeast` , `westus2` , `southcentralus` .
  * Ověřte přístup k oblasti pomocí Azure Portal.
  
* **Workspace.from_config se nezdařila**:

  V případě `ws = Workspace.from_config()` neúspěchu volání:

  1. Ujistěte se, že Poznámkový blok Configuration. ipynb byl úspěšně spuštěn.
  1. Pokud se Poznámkový blok spouští ze složky, která není ve složce, ve které `configuration.ipynb` bylo spuštěno, zkopírujte aml_config složky a config.jssouboru, který obsahuje do nové složky. Workspace.from_config přečte config.jspro složku poznámkového bloku nebo její nadřazenou složku.
  1. Pokud se používá nové předplatné, skupina prostředků, pracovní prostor nebo oblast, ujistěte se, že jste `configuration.ipynb` Poznámkový blok znovu spustili. Přímá změna config.jsna přímo bude fungovat jenom v případě, že pracovní prostor už existuje v zadané skupině prostředků v rámci zadaného předplatného.
  1. Pokud chcete změnit oblast, změňte pracovní prostor, skupinu prostředků nebo předplatné. `Workspace.create` pracovní prostor nebude vytvořen ani aktualizován, pokud již existuje, i když je zadaná oblast odlišná.

## <a name="tensorflow"></a>TensorFlow

Od verze 1.5.0 sady SDK služba automatizovaného strojového učení neinstaluje modely TensorFlow ve výchozím nastavení. Pokud chcete nainstalovat TensorFlow a používat ho s automatizovanými experimenty ML, nainstalujte `tensorflow==1.12.0` přes `CondaDependencies` .

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Numpy selhání

* **`import numpy` v systému Windows** došlo k chybě. v některých prostředích systému Windows se zobrazí chyba při načítání numpy s nejnovější verzí jazyka Python 3.6.8. Pokud se tento problém zobrazí, vyzkoušejte Python verze 3.6.7.

* nepovedlo **`import numpy` se: Podívejte** se na verzi TensorFlow v prostředí automatizovaného conda ml. Podporované verze jsou < 1,13. Pokud je verze >= 1,13, odinstalujte TensorFlow z prostředí.

Verzi TensorFlow a odinstalaci můžete ověřit následujícím způsobem:

  1. Spusťte příkazové prostředí, aktivujte prostředí Conda, ve kterém jsou nainstalované automatizované balíčky ml.
  1. Zadejte `pip freeze` a vyhledejte `tensorflow` , pokud se nachází, uvedená verze by měla být < 1,13
  1. Pokud uvedená verze není podporovanou verzí, `pip uninstall tensorflow` v příkazovém prostředí a zadejte y pro potvrzení.

## `jwt.exceptions.DecodeError`

Přesná chybová zpráva: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

Pro verze sady SDK <= 1.17.0 může instalace způsobit nepodporovanou verzi PyJWT. Ověřte, že verze PyJWT v prostředí conda pro automatizované ml je podporovanou verzí. To je PyJWT verze < 2.0.0.

Verzi PyJWT můžete ověřit následujícím způsobem:

1. Spusťte příkazové prostředí a aktivujte prostředí Conda, ve kterém jsou nainstalované automatizované balíčky ML.

1. Zadejte `pip freeze` a vyhledejte `PyJWT` , pokud se nachází, uvedená verze by měla být < 2.0.0

Pokud uvedená verze není podporovanou verzí:

1. Zvažte upgrade na nejnovější verzi sady AutoML SDK: `pip install -U azureml-sdk[automl]`

1. Pokud to není životaschopné, odinstalujte PyJWT z prostředí a nainstalujte správnou verzi následujícím způsobem:

    1. `pip uninstall PyJWT` v příkazovém prostředí a zadejte `y` pro potvrzení.
    1. Nainstalujte pomocí `pip install 'PyJWT<2.0.0'` .
  
## <a name="databricks"></a>Databricks
Přečtěte si, [jak nakonfigurovat experiment automatizovaného ml s datacihly](how-to-configure-databricks-automl-environment.md#troubleshooting).

## <a name="forecasting-r2-score-is-always-zero"></a>Skóre pro předpověď R2 je vždycky nula.

 K tomuto problému dochází, pokud mají poskytnuté školicí údaje časovou řadu, která obsahuje stejnou hodnotu pro poslední `n_cv_splits`  +  `forecasting_horizon` datové body.

Pokud je tento model očekáván v časové řadě, můžete přepínat primární metriku na **normalizovaný základní průměrnou chybu**.

## <a name="failed-deployment"></a>Neúspěšné nasazení

 U verzí <= 1.18.0 sady SDK se může základní image vytvořená pro nasazení selhat s následující chybou: `ImportError: cannot import name cached_property from werkzeug` .

  Problém můžete vyřešit podle následujících kroků:

  1. Stažení balíčku modelu
  1. Rozbalit balíček
  1. Nasazení pomocí prostředků unzip

## <a name="azure-functions-application"></a>Azure Functions aplikace
  
  Automatizované ML v současné době nepodporuje Azure Functions aplikace. 

## <a name="sample-notebook-failures"></a>Ukázky selhání poznámkových bloků

 Pokud ukázkový Poznámkový blok selhává s chybou, kterou vlastnost, metoda nebo knihovna neexistují:

* Ujistěte se, že je v Jupyter Notebook vybraná správná jádro. Jádro se zobrazí v pravém horním rohu stránky poznámkového bloku. Výchozí hodnota je *azure_automl*. Jádro se uloží jako součást poznámkového bloku. Pokud přepnete na nové prostředí Conda, musíte v poznámkovém bloku vybrat nové jádro.

  * V případě Azure Notebooks by měl být Python 3,6.
  * V místních prostředích conda by měl být název prostředí Conda, který jste zadali v automl_setup.

* Chcete-li zajistit, aby byl Poznámkový blok pro verzi sady SDK, kterou používáte,
  * Ověřte verzi sady SDK spuštěním `azureml.core.VERSION` Jupyter notebook buňky.
  * Předchozí verzi ukázkových poznámkových bloků si můžete stáhnout z GitHubu pomocí těchto kroků:
    1. Vybrat `Branch` tlačítko
    1. Přejít na `Tags` kartu
    1. Vyberte verzi.

## <a name="next-steps"></a>Další kroky

+ Přečtěte si další informace o [tom, jak vytvořit regresní model pomocí automatizovaného strojového učení](tutorial-auto-train-models.md) nebo [jak pomocí automatizovaného strojového učení na vzdáleném prostředku vyškolit](how-to-auto-train-remote.md).

+ Přečtěte si další informace o [tom, jak a kde model nasadit](how-to-deploy-and-where.md).
