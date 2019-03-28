---
title: Vytvoření pracovního prostoru
titleSuffix: Azure Machine Learning service
description: Pomocí webu Azure portal, sady SDK, šablonu nebo rozhraní příkazového řádku k vytvoření pracovního prostoru služby Azure Machine Learning. Tento pracovní prostor poskytuje centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte, když používáte službu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 03/21/2019
ms.openlocfilehash: e55c898cdbb45dc7ff3466c61e968e3ae52be0cb
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521002"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Vytvořit pracovní prostor služby Azure Machine Learning

Pokud chcete používat službu Azure Machine Learning, je nutné [ **pracovního prostoru služby Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace).  Tento pracovní prostor je prostředek nejvyšší úrovně služby a vám poskytne centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte. 

V tomto článku se dozvíte, jak vytvořit pracovní prostor pomocí některé z těchto metod: 
* [Webu Azure portal](#portal) rozhraní
* [Azure Machine Learning sady SDK pro Python](#sdk)
* Šablony Azure Resource Manageru
* [Azure Machine Learning rozhraní příkazového řádku](#cli)

Pracovní prostor, který vytvoříte pomocí postupu v tady může sloužit jako předpokladem pro další kurzy a články s postupy. 

Při vytváření pracovního prostoru jsou přidány následující prostředky Azure, automaticky (pokud jsou regionálně dostupné):
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Stejně jako u jiných služeb Azure, jsou spojeny s Machine learningem určitá omezení a kvóty. [Další informace o kvótách a postupu, jak požádat o zvýšení.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Požadavky
Chcete-li vytvořit pracovní prostor, budete potřebovat předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

## <a name="portal"></a> Azure portal

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Bez ohledu na to, jak byl vytvořen, můžete zobrazit v pracovním prostoru [webu Azure portal](https://portal.azure.com/).  Zobrazit [zobrazení pracovního prostoru](how-to-manage-workspace.md#view) podrobnosti.

## <a name="sdk"></a> Python SDK

Vytvoření pracovního prostoru pomocí sady Python SDK. Je třeba nejprve nainstalovat sadu SDK.

> [!IMPORTANT]
> Instalace sady SDK přeskočí, pokud použijete virtuální počítač Azure datové vědy nebo Azure Databricks.
> * Azure virtuální počítače pro datové vědy vytvořený po 27. září 2018 se dodávají s předinstalovaným SDK pro Python. Přeskočit instalaci a začněte s [vytvořit pracovní prostor se sadou SDK](#sdk-create).
> * V prostředí Azure Databricks používat [instalační postup, který Databricks](how-to-configure-environment.md#azure-databricks) místo.

>[!NOTE]
> Tyto pokyny použijte k instalaci a použití sady SDK ze svého místního počítače. Použít Jupyter ve vzdáleném virtuálním počítači, nastavení vzdálené plochy nebo X Terminálové relaci.

Před instalací sady SDK doporučujeme vytvořit izolované prostředí Pythonu. Přestože tento článek používá [Miniconda](https://docs.conda.io/en/latest/miniconda.html), můžete také použít úplnou [Anaconda](https://www.anaconda.com/) nainstalované nebo [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Pokyny v tomto článku se nainstaluje všechny balíčky, které potřebujete možnost spouštět poznámkové bloky rychlý start a kurzů.  Další ukázkové poznámkové bloky může vyžadovat instalaci dalších součástí.  Další informace o těchto součástech naleznete v tématu [nainstalovat sadu SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Instalace aplikace Miniconda

[Stáhněte a nainstalujte Miniconda](https://docs.conda.io/en/latest/miniconda.html). Vyberte verzi Pythonu 3.7 k instalaci. Nevybírejte Python verze 2.x.  

### <a name="create-an-isolated-python-environment"></a>Vytvoření izolovaného prostředí Pythonu

1. Otevřete okno příkazového řádku a pak vytvořte nové prostředí conda s názvem *myenv* a nainstalujte Python 3.6.5. Azure Machine Learning SDK bude pracovat s Python verze 3.5.2 nebo novější, ale automatizované strojového učení komponenty nejsou plně funkční na Python 3.7.  To bude trvat několik minut vytvořit prostředí, zatímco se stáhnou součásti a balíčky.

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Aktivujte prostředí.

    ```shell
    conda activate myenv
    ```

1. Povolení specifických pro prostředí ipython jádra:

    ```shell
    conda install notebook ipykernel
    ```

    Pak vytvořte jádra:

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Instalace sady SDK

1. V prostředí aktivovaných conda instalace základních součástí sady SDK Machine Learning s možnostmi Poznámkový blok Jupyter. Instalace trvá několik minut na dokončení závislosti na konfiguraci vašeho počítače.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Pro účely tohoto prostředí kurzy Azure Machine Learning, instalaci těchto balíčků.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Pro účely tohoto prostředí kurzy Azure Machine Learning, nainstalujte automatizované strojového učení komponenty.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> V některé nástroje příkazového řádku můžete potřebovat pro přidání uvozovek následujícím způsobem:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> Vytvoření pracovního prostoru se sadou SDK

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


### <a name="write-a-configuration-file"></a>Zapsání konfiguračního souboru

Uložte podrobnosti pracovního prostoru v konfiguračním souboru do aktuálního adresáře. Tento soubor se nazývá *aml_config/config.json*.  

Tento konfigurační soubor pracovního prostoru usnadňuje stejného pracovního prostoru načíst později. Můžete načíst pomocí jiných poznámkových bloků a skriptů ve stejném adresáři nebo podadresář pomocí kódu `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

To `write_config()` volání rozhraní API vytvoří konfigurační soubor do aktuálního adresáře. *Config.json* soubor obsahuje následující:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Použití pracovního prostoru v skriptů Pythonu nebo poznámkových bloků Jupyter, které jsou umístěny v jiných adresářích, zkopírujte tento soubor do tohoto adresáře. Soubor může být ve stejném adresáři, podadresáři s názvem *aml_config*, nebo v nadřazeném adresáři.

## <a name="resource-manager-template"></a>Šablony Resource Manageru

Vytvoření pracovního prostoru s využitím šablony najdete v tématu [vytvořit pracovní prostor služby Azure Machine Learning pomocí šablony](how-to-create-workspace-template.md)

## <a name="cli"></a>Rozhraní příkazového řádku

Vytvoření pracovního prostoru pomocí rozhraní příkazového řádku najdete v tématu [pomocí rozšíření rozhraní příkazového řádku pro službu Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

* Bez ohledu na to, jak byl vytvořen, můžete zobrazit v pracovním prostoru [webu Azure portal](https://portal.azure.com/).  Zobrazit [zobrazení pracovního prostoru](how-to-manage-workspace.md#view) podrobnosti.

* Vyzkoušejte si pracovní prostor s těchto rychlých startů a kurzů.

    * Rychlý start: [Spusťte Poznámkový blok Jupyter v cloudu](quickstart-run-cloud-notebook.md).
    * Rychlý start: [Spusťte Poznámkový blok Jupyter na vlastním serveru](quickstart-run-local-notebook.md).
    * Dvojdílného kurzu: [Trénování](tutorial-train-models-with-aml.md) a [nasazení](tutorial-deploy-models-with-aml.md) režimu klasifikace obrázků.
    * Dvojdílného kurzu: [Příprava dat](tutorial-data-prep.md) a [pomocí automatizovaných machine learning](tutorial-auto-train-models.md) sestaví regresní model.

* Další informace o tom, jak [nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md).

* Další informace o [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk).
