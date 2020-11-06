---
title: Vývoj s využitím autoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: Naučte se nastavit vývojové prostředí v Azure Machine Learning a Azure Databricks. Používejte sady SDK Azure ML pro datacihly a datacihly pomocí autoML.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef8ee7718aabb443fda6cd7b276ee53472261913
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424104"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Nastavení vývojového prostředí pomocí Azure Databricks a autoML v Azure Machine Learning 

Naučte se konfigurovat vývojové prostředí v Azure Machine Learning, které používá Azure Databricks a automatizované ML.

Azure Databricks je ideální pro spouštění rozsáhlých pracovních postupů strojového učení ve velkém měřítku na škálovatelné Apache Spark platformě v cloudu Azure. Nabízí prostředí založené na poznámkovém bloku s PROCESORem nebo výpočetním clusterem založeným na GPU.

Informace o dalších prostředích pro vývoj ve strojovém učení najdete v tématu [Nastavení vývojového prostředí](how-to-configure-environment.md)v jazyce Python.


## <a name="prerequisite"></a>Požadavek

Azure Machine Learning pracovní prostor. Pokud ho ještě nemáte, můžete vytvořit Azure Machine Learning pracovní prostor prostřednictvím šablon [Azure Portal](how-to-manage-workspace.md), [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)a [Azure Resource Manager](how-to-create-workspace-template.md).


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks s Azure Machine Learning a autoML

Azure Databricks se integruje s Azure Machine Learning a jeho možnostmi autoML. 

Můžete použít Azure Databricks:

+ Pro výuku modelu pomocí Spark MLlib a nasazení modelu do ACI/AKS.
+ Pomocí [automatizovaných funkcí strojového učení](concept-automated-ml.md) s využitím sady Azure ml SDK.
+ Jako cíl výpočetní služby z [Azure Machine Learningho kanálu](concept-ml-pipelines.md).

## <a name="set-up-a-databricks-cluster"></a>Nastavení clusteru datacihly

Vytvořte [cluster datacihly](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Některá nastavení platí jenom v případě, že nainstalujete sadu SDK pro automatizované Machine Learning na datacihly.

**Vytvoření clusteru trvá několik minut.**

Použijte tato nastavení:

| Nastavení |Platí pro| Hodnota |
|----|---|---|
| Název clusteru |stál| yourclustername |
| Verze Databricks Runtime |stál| Runtime 7,1 (Scala 2,21, Spark 3.0.0) – ne ML|
| Verze Pythonu |stál| 3 |
| Typ pracovního procesu <br>(určuje maximální počet souběžných iterací) |Automatizované strojové učení<br>pouze| Preferovaný virtuální počítač pro optimalizaci paměti |
| Pracovníků |stál| 2 nebo vyšší |
| Povolit automatické škálování |Automatizované strojové učení<br>pouze| Zrušte zaškrtnutí políčka  |

Než budete pokračovat, počkejte, dokud cluster neběží.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Přidání sady Azure ML SDK do datacihlů

Po spuštění clusteru [vytvořte knihovnu](https://docs.databricks.com/user-guide/libraries.html#create-a-library) , která připojí příslušný balíček sady Azure Machine Learning SDK k vašemu clusteru. 

Pokud chcete používat automatizovanou ML, přejděte k [části Přidání sady Azure ml SDK pomocí AutoML](#add-the-azure-ml-sdk-with-automl-to-databricks).


1. Klikněte pravým tlačítkem na složku aktuální pracovní prostor, kam chcete uložit knihovnu. Vyberte **vytvořit**  >  **knihovnu**.
    
    > [!TIP]
    > Pokud máte starou verzi sady SDK, zrušte její výběr z nainstalovaných knihoven clusteru a přesuňte se do koše. Nainstalujte novou verzi sady SDK a restartujte cluster. Pokud po restartování dojde k problému, odpojte a znovu připojte svůj cluster.

1. Vyberte následující možnost (žádné jiné instalace sady SDK nejsou podporovány)

   |&nbsp;Doplňky balíčku sady SDK &nbsp;|Zdroj|&nbsp;Název PyPI&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Pro datacihly| Nahrát vejce Pythonu nebo PyPI | AzureML-SDK [datacihly]|

   > [!WARNING]
   > Nelze nainstalovat žádné další doplňky sady SDK. Vyberte pouze možnost [ `databricks` ].

   * Nevybírejte možnost **Připojit automaticky ke všem clusterům**.
   * Vedle názvu clusteru vyberte  **připojit** .

1. Monitoruje chyby, dokud se změny stavu **nepřipojí** , což může trvat několik minut.  Pokud tento krok neproběhne úspěšně:

   Zkuste cluster restartovat pomocí:
   1. V levém podokně vyberte **clustery**.
   1. V tabulce vyberte název vašeho clusteru.
   1. Na kartě **knihovny** vyberte **restartovat**.

   Úspěšná instalace vypadá následovně: 

  ![Sada SDK Azure Machine Learning pro datacihly](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Přidání sady Azure ML SDK s AutoML do datacihlů
Pokud byl cluster vytvořen pomocí Databricks Runtime 7,1 nebo vyšší ( *ne* ml), spusťte následující příkaz v první buňce poznámkového bloku a nainstalujte sadu AML SDK.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Pro Databricks Runtime 7,0 a nižší nainstalujte sadu Azure Machine Learning SDK pomocí [skriptu init](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).

### <a name="automl-config-settings"></a>Nastavení konfigurace AutoML

V AutoML config při použití Azure Databricks přidejte následující parametry:

- ```max_concurrent_iterations``` vychází z počtu pracovních uzlů v clusteru.
- ```spark_context=sc``` je založen na výchozím kontextu Sparku.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>Poznámkové bloky ML, které pracují s Azure Databricks

Vyzkoušet:
+ I když je k dispozici mnoho ukázkových poznámkových bloků, **fungují pouze [Tyto ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) s Azure Databricks.**

+ Tyto ukázky importujte přímo z pracovního prostoru. Viz níže: ![ Vyberte importovat ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ panel importu.](./media/how-to-configure-environment/azure-db-import.png)

+ Naučte se [vytvářet kanály s datacihlami jako výpočetní](how-to-create-your-first-pipeline.md)prostředí.

## <a name="next-steps"></a>Další kroky

- [Výuku modelu](tutorial-train-models-with-aml.md) na Azure Machine Learning s datovou sadou mnist ručně zapsaných
- Referenční informace o [sadě Azure Machine Learning SDK pro Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)najdete v tématu.
