---
title: 'Rychlý start: Začínáme v Pythonu'
titleSuffix: Azure Machine Learning service
description: Začínáme se službou Azure Machine Learning v Pythonu. Pomocí sady Python SDK k vytvoření pracovního prostoru, což je základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely machine learningu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 01/22/2019
ms.custom: seodec18
ms.openlocfilehash: e0c235a9fd3898fa4525651d514c77432627603c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238954"
---
# <a name="quickstart-use-the-python-sdk-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme s Azure Machine Learning pomocí sady Python SDK

V tomto článku se pomocí Azure Machine Learning SDK pro Python 3 a potom pomocí služby Azure Machine Learning [pracovní prostor](concept-azure-machine-learning-architecture.md). Pracovní prostor se základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely strojového učení pomocí Machine Learning.

Začnete tím, že nakonfigurujete vlastní prostředí Pythonu a Server poznámkového bloku Jupyter. Aby ho spustili bez instalace, najdete v článku [rychlý start: Začínáme s Azure Machine Learning pomocí webu Azure portal](quickstart-get-started.md). 

Zobrazte video verzi tohoto rychlého startu:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

V tomto rychlém startu:

* Nainstalujte Python SDK.
* Vytvořit pracovní prostor ve vašem předplatném Azure.
* Vytvořit pro tento pracovní prostor konfigurační soubor pro pozdější použití v jiných poznámkových blocích a skriptech.
* Napsat kód, který protokoluje hodnoty v pracovním prostoru.
* Zobrazit zaprotokolované hodnoty z vašeho pracovního prostoru.

Vytvořte pracovní prostor a konfigurační soubor, který chcete použít jako požadavky na další kurzy Machine Learning a články s postupy. Stejně jako u jiných služeb Azure, jsou spojeny s Machine learningem určitá omezení a kvóty. [Další informace o kvótách a postupu, jak požádat o zvýšení.](how-to-manage-quotas.md)

Do vašeho pracovního prostoru se automaticky přidají následující prostředky Azure, pokud jsou regionálně dostupné:
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!NOTE]
> Kód v tomto článku vyžaduje Azure Machine Learning SDK verze 1.0.2 nebo novější a byl testován s verze 1.0.8.


Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

## <a name="install-the-sdk"></a>Instalace sady SDK

> [!IMPORTANT]
> Tuto část přeskočte, pokud použijete virtuální počítač Azure datové vědy nebo Azure Databricks.
> * Azure virtuální počítače pro datové vědy vytvořený po 27. září 2018 se dodávají s předinstalovaným SDK pro Python.
> * V prostředí Azure Databricks používat [instalační postup, který Databricks](how-to-configure-environment.md#azure-databricks) místo.

Před instalací sady SDK doporučujeme vytvořit izolované prostředí Pythonu. Přestože tento článek používá [Miniconda](https://docs.conda.io/en/latest/miniconda.html), můžete také použít úplnou [Anaconda](https://www.anaconda.com/) nainstalované nebo [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Instalace aplikace Miniconda

[Stáhněte a nainstalujte Miniconda](https://docs.conda.io/en/latest/miniconda.html). Vyberte Python 3.7 nebo novější verze, chcete-li nainstalovat. Nevybírejte Python verze 2.x.  

### <a name="create-an-isolated-python-environment"></a>Vytvoření izolovaného prostředí Pythonu

1. Otevřete okno příkazového řádku a pak vytvořte nové prostředí conda s názvem *myenv* a nainstalujte Python 3.6. Azure Machine Learning SDK bude pracovat s Python verze 3.5.2 nebo novější, ale automatizované strojového učení komponenty nejsou plně funkční na Python 3.7.

    ```shell
    conda create -n myenv -y Python=3.6
    ```

1. Aktivujte prostředí.

    ```shell
    conda activate myenv
    ```

### <a name="install-the-sdk"></a>Instalace sady SDK

1. V prostředí aktivovaných conda instalace základních součástí sady SDK Machine Learning s možnostmi Poznámkový blok Jupyter.  Instalace trvá několik minut na dokončení závislosti na konfiguraci vašeho počítače.

  ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Instalace serveru Poznámkový blok Jupyter v prostředí conda.

  ```shell
    conda install nb_conda
    ```

1. Pro účely tohoto prostředí kurzy Azure Machine Learning, instalaci těchto balíčků.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Pro účely tohoto prostředí kurzy Azure Machine Learning, nainstalujte automatizované strojového učení komponenty.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Vytvoření pracovního prostoru v poznámkovém bloku Jupyter pomocí sady Python SDK.

1. Vytvoření a/nebo cd do adresáře, které chcete použít pro rychlý start a kurzy.

1. Chcete-li spustit Poznámkový blok Jupyter, zadejte tento příkaz:

    ```shell
    jupyter notebook
    ```

1. V okně prohlížeče vytvořte pomocí výchozího jádra `Python 3` nový poznámkový blok. 

1. Pokud chcete zobrazit verzi sady SDK, zadejte a potom spusťte následující kód Pythonu v buňce Poznámkový blok:

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Najít hodnotu `<azure-subscription-id>` parametr [seznam předplatných na webu Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Použijte libovolné předplatné, ve kterém máte roli vlastníka nebo přispěvatele.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   Při spouštění kódu vám může zobrazit výzva k přihlášení ke svému účtu Azure. Jakmile se přihlásíte, ověřovací token se uloží do místní mezipaměti.

1. Chcete-li zobrazit pracovní prostor podrobnosti, jako je například přidruženého úložiště, container registry a služby key vault, zadejte následující kód:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Zapsání konfiguračního souboru

Uložte podrobnosti pracovního prostoru v konfiguračním souboru do aktuálního adresáře. Tento soubor se nazývá *aml_config\config.json*.  

Tento konfigurační soubor pracovního prostoru usnadňuje stejného pracovního prostoru načíst později. Můžete ho načíst s jinými poznámkovými bloky a skripty ve stejném adresáři nebo podadresáři.  

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

To `write_config()` volání rozhraní API vytvoří konfigurační soubor do aktuálního adresáře. *Config.json* soubor obsahuje následující:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Použití pracovního prostoru

Spusťte kód, který používá základní rozhraní API sady SDK pro sledování spuštění experimentu:

1. Vytvoření experimentu v pracovním prostoru.
1. Přihlaste se ke experiment jednu hodnotu.
1. Seznam hodnot přihlaste experimentu.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>Zobrazení zaprotokolovaných výsledků
Po dokončení spuštění můžete experimentální spuštění zobrazit na webu Azure Portal. Tisk adresu URL, která přejde na výsledcích posledního spuštění, použijte následující kód:

```python
print(run.get_portal_url())
```

Pokud chcete zobrazit zaprotokolované hodnoty na webu Azure Portal v prohlížeči, použijte odkaz.

![Zaznamenané hodnoty na webu Azure Portal](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků 
>[!IMPORTANT]
>Můžete použít zdroje, které jste vytvořili jako požadavky na další kurzy Machine Learning a s postupy články.

Pokud nemáte v úmyslu používat prostředky, které jste vytvořili v tomto článku, je vyhnout poplatkům za odstraníte.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili prostředky, které je potřeba experimentovat a nasazovat modely. Spuštění kódu v poznámkovém bloku a prozkoumali historii spuštění pro kód ve vašem pracovním prostoru v cloudu.

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)

Můžete také prozkoumat [pokročilejší příklady na Githubu](https://aka.ms/aml-notebooks).
