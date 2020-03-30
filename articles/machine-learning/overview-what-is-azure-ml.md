---
title: Co je Azure Machine Learning
description: Přehled Azure Machine Learning – integrované komplexní řešení pro datové vědy pro profesionální datové vědce, které bude vyvíjet, experimentovat a nasazovat pokročilé analytické aplikace v cloudovém měřítku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 11/04/2019
ms.openlocfilehash: b8dbbb2810277bef20cb3b9b47a63deeea3e0ff9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241609"
---
# <a name="what-is-azure-machine-learning"></a>Co je Azure Machine Learning?

V tomto článku se dozvíte o Azure Machine Learning, cloudové prostředí, které můžete použít k trénování, nasazování, automatizaci, správě a sledování modelů ML. 

Azure Machine Learning se dá použít pro jakýkoli druh strojového učení, od klasického ml až po hloubkové učení, učení pod dohledem a učení bez dozoru. Ať už dáváte přednost psaní kódu Pythonu nebo R nebo nula kódu/nízkého kódu, jako je [návrhář](tutorial-designer-automobile-price-train-score.md), můžete vytvářet, trénovat a sledovat vysoce přesné modely strojového učení a hloubkového učení v pracovním prostoru Azure Machine Learning. 

Začněte trénovat na místním počítači a pak škálujte do cloudu. 

Služba také spolupracuje s oblíbenými open-source nástroji, jako jsou PyTorch, TensorFlow a scikit-learn.

> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

> [!Tip]
> **Zkušební verze zdarma!**  Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes. Získáte kredity, které můžete využít na služby Azure. Po jejich vyčerpání si můžete účet ponechat a používat [bezplatné služby Azure](https://azure.microsoft.com/free/). Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.


## <a name="what-is-machine-learning"></a>Co je strojové učení?

Strojové učení je technika datové vědy, která umožňuje počítačům s využitím existujících dat předvídat budoucí chování, výsledky a trendy. Pomocí strojového učení se počítače učí, aniž by byly explicitně naprogramovány.

Díky předpovědím neboli predikcím ze strojového učení můžou být aplikace a zařízení chytřejší. Když například nakupujete online, pomáhá strojové učení doporučit další produkty, které můžete chtít, na základě toho, co jste si koupili. Nebo při protažení platební karty čtečkou porovnává strojové učení danou transakci s databází, a pomáhá tak odhalovat podvody. A takový robotický vysavač na základě strojového učení zjišťuje, jestli už v místnosti dostatečně uklidil.

## <a name="machine-learning-tools-to-fit-each-task"></a>Nástroje strojového učení, které se hodí pro každý úkol 

Azure Machine Learning poskytuje všem vývojářům nástrojů a datovým vědcům potřebné pracovní postupy strojového učení, včetně:
+ [Návrhář Azure Machine Learning](tutorial-designer-automobile-price-train-score.md) (preview): moduly přetažení mj.

+ Jupyter notebooky: použijte náš [příklad notebooků](https://aka.ms/aml-notebooks) nebo si vytvořte vlastní poznámkové bloky využít naše <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">SDK pro ukázky Pythonu</a> pro vaše strojové učení. 

+ R skripty nebo poznámkové bloky, ve kterých používáte <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">SDK pro R</a> napsat vlastní kód nebo použít moduly R v návrháři.

+ [Rozšíření editoru Visual Studio Code](tutorial-setup-vscode-extension.md)

+ [Cli strojového učení](reference-azure-machine-learning-cli.md)

+ Open-source architektury jako PyTorch, TensorFlow, a scikit-learn a mnoho dalších

Můžete dokonce použít [MLflow ke sledování metrik a nasazení modelů](how-to-use-mlflow.md) nebo Kubeflow k vytváření kanálů [pracovních postupů od konce](https://www.kubeflow.org/docs/azure/).

## <a name="build-ml-models-in-python-or-r"></a>Vytváření modelů ML v Pythonu nebo R

Začněte školení na místním počítači pomocí Azure Machine Learning <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Python SDK</a> nebo <a href="https://azure.github.io/azureml-sdk-for-r/reference/index.html" target="_blank">R SDK</a>. Potom můžete horizontální navýšení kapacity do cloudu. 

Díky mnoha [dostupným výpočetním cílům](how-to-set-up-training-targets.md), jako jsou Azure Machine Learning Compute a [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), a s [pokročilými službami ladění hyperparametrů](how-to-tune-hyperparameters.md)můžete vytvářet lepší modely rychleji pomocí výkonu cloudu.

Můžete také [automatizovat trénování modelu a ladění](tutorial-auto-train-models.md) pomocí sady SDK.

## <a name="build-ml-models-with-no-code-tools"></a>Vytváření modelů ML pomocí nástrojů bez kódu

Pro školení a nasazení bez kódu nebo s nízkým kódem vyzkoušejte:

+ **Návrhář Azure Machine Learning (preview)**

  Pomocí návrháře můžete připravovat data, trénovat, testovat, nasazovat, spravovat a sledovat modely strojového učení bez psaní kódu. Není vyžadováno žádné programování, vizuálně připojíte datové sady a moduly k vytvoření modelu. Vyzkoušejte [kurz návrháře](tutorial-designer-automobile-price-train-score.md).

  Další informace najdete v [článku přehled návrháře Azure Machine Learning](concept-designer.md). 

  ![Příklad návrháře Azure Machine Learning](./media/overview-what-is-azure-ml/designer-drag-and-drop.gif)

+ **Automatizované ui strojového učení**

  Přečtěte si, jak vytvářet [automatizované experimenty ML](tutorial-first-experiment-automated-ml.md) ve snadno použitelném rozhraní. 

  [![Navigační podokno Studia Azure strojového učení](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

## <a name="mlops-deploy--lifecycle-management"></a>MLOps: Nasazení správy životního cyklu &
Když máte správný model, můžete ho snadno používat ve webové službě, na zařízení IoT nebo z Power BI. Další informace naleznete v článku o [nasazení a kde](how-to-deploy-and-where.md).

Pak můžete spravovat nasazené modely pomocí [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk), Azure Machine Learning [studio](https://ml.azure.com)nebo [strojového učení CLI](reference-azure-machine-learning-cli.md).

Tyto modely mohou být spotřebovány a vrátit předpovědi v [reálném čase](how-to-consume-web-service.md) nebo [asynchronně](how-to-use-parallel-run-step.md) na velké množství dat.

A s [pokročilými kanály strojového učení](concept-ml-pipelines.md)můžete spolupracovat na každém kroku od přípravy dat, školení a hodnocení modelů až po nasazení. Potrubí umožňují:

* Automatizace komplexního procesu strojového učení v cloudu
* Opětovné použití součástí a opětovné spuštění kroků pouze v případě potřeby
* Použití různých výpočetních prostředků v každém kroku
* Spuštění úloh dávkového vyhodnocování

Pokud chcete pomocí skriptů automatizovat pracovní postup strojového učení, poskytuje [příkazové příkazové číslo strojového učení](reference-azure-machine-learning-cli.md) nástroje příkazového řádku, které provádějí běžné úkoly, jako je například odeslání trénovacího běhu nebo nasazení modelu.

Pokud chcete začít používat Azure Machine Learning, přečtěte si [další postup](#next-steps).

## <a name="integration-with-other-services"></a>Integrace s dalšími službami

Azure Machine Learning spolupracuje s dalšími službami na platformě Azure a také se integruje s open source nástroji, jako jsou Git a MLFlow.

+ Výpočetní cíle, jako je __služba Azure Kubernetes Service__, Instance __kontejnerů Azure__, Azure __Databricks__, __Azure Data Lake Analytics__a __Azure HDInsight__. Další informace o výpočetních cílech najdete v tématu [Co jsou výpočetní cíle?](concept-compute-target.md).
+ __Azure Event Grid__. Další informace najdete [v tématu Spotřebovávat události Azure Machine Learning](concept-event-grid-integration.md).
+ __Azure Monitor__. Další informace najdete [v tématu Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).
+ Úložiště dat, jako jsou __účty Azure Storage__, Azure Data Lake __Storage__, Azure __SQL Database__, Azure Database __for PostgreSQL__a __Azure Open Datasets__. Další informace najdete [v tématu Přístup k datům ve službách úložiště Azure](how-to-access-data.md) a Vytváření [datových sad pomocí otevřených datových sad Azure](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets).
+ __Virtuální sítě Azure__. Další informace najdete [v tématu Zabezpečené experimentování a odvození ve virtuální síti](how-to-enable-virtual-network.md).
+ __Azure kanály__. Další informace najdete [v tématu Trénování a nasazování modelů strojového učení](/azure/devops/pipelines/targets/azure-machine-learning).
+ __Protokoly úložiště Git__. Další informace naleznete v tématu [Integrace Gitu](concept-train-model-git-integration.md).
+ __MLFlow__. Další informace najdete v tématu [MLflow sledovat metriky a nasazovat modely](how-to-use-mlflow.md) 
+ __Kubeflow__. Další informace naleznete v [tématu sestavení kanálů pracovních postupů od konce](https://www.kubeflow.org/docs/azure/).

### <a name="secure-communications"></a>Bezpečná komunikace

Váš účet Azure Storage, výpočetní cíle a další prostředky lze bezpečně použít uvnitř virtuální sítě k trénování modelů a provádění odvození. Další informace najdete [v tématu Zabezpečené experimentování a odvození ve virtuální síti](how-to-enable-virtual-network.md).

## <a name="basic--enterprise-editions"></a><a name="sku"></a>Základní & edice Enterprise

Azure Machine Learning nabízí dvě edice přizpůsobené vašim potřebám strojového učení:
+ Základní (obecně dostupné)
+ Organizace (náhled)

Tyto edice určují, které nástroje strojového učení jsou k dispozici vývojářům a datovým vědcům z jejich pracovního prostoru.   

Základní pracovní prostory umožňují pokračovat v používání Azure Machine Learning a platit za jenom prostředky Azure spotřebované během procesu strojového učení. Pracovní prostory enterprise edition se budou účtovat jenom za jejich spotřebu Azure, zatímco edice je ve verzi Preview. Další informace o tom, co je k dispozici, najdete v [přehledu edice](https://azure.microsoft.com/pricing/details/machine-learning/)Azure Machine Learning & stránce o cenách . 

Edici přiřadíte při každém vytvoření pracovního prostoru. A již existující pracovní prostory byly převedeny na základní edici pro vás. Základní vydání obsahuje všechny funkce, které byly již obecně dostupné od října 2019. Všechny experimenty v pracovních prostorech, které byly vytvořeny pomocí funkcí edice Enterprise, budou i nadále k dispozici jen pro čtení, dokud neupgradujete na Enterprise. Přečtěte si, jak [upgradovat základní pracovní prostor na edici Enterprise](how-to-manage-workspace.md#upgrade). 

Zákazníci jsou zodpovědní za náklady vzniklé na výpočetní prostředky a další prostředky Azure během této doby.

## <a name="next-steps"></a>Další kroky

- Vytvořte svůj první experiment s preferovanou metodou:
  + [Použití poznámkových bloků Pythonu k trénování & nasazení modelů ML](tutorial-1st-experiment-sdk-setup.md)
  + [Použití R Markdown utrénovat & nasazení modelů ML](tutorial-1st-r-experiment.md) 
  + [Použití automatizovaného strojového učení k trénování & nasazení modelů ML](tutorial-first-experiment-automated-ml.md) 
  + [Pomocí funkcí přetažení & přetažení návrháře můžete trénovat & nasazení](tutorial-designer-automobile-price-train-score.md) 
  + [Použití cli strojového učení k trénování a nasazování modelu](tutorial-train-deploy-model-cli.md)

- Seznamte se s [kanály strojového učení](concept-ml-pipelines.md), které umožňují vytvářet, optimalizovat a spravovat scénáře strojového učení.

- Přečtěte si podrobný článek [o architektuře a konceptech Azure Machine Learning.](concept-azure-machine-learning-architecture.md)
