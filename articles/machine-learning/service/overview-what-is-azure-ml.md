---
title: Co je
titleSuffix: Azure Machine Learning service
description: Přehled služby Azure Machine Learning – představuje řešení integrované, koncové až do konce datové vědy pro profesionální datové vědce, které k vývoji, experimentování a nasazování pokročilých analytických aplikací na úrovni cloudu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: c9ebacd8caf992874969106df58531538ea399ed
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510434"
---
# <a name="what-is-azure-machine-learning-service"></a>Co je služba Azure Machine Learning?

Služba Azure Machine Learning je Cloudová služba, který použijete k natrénování, nasazení, automatizaci a správu modelů strojového učení, vše na široké měřítko, které cloud poskytuje.

## <a name="what-is-machine-learning"></a>Co je strojové učení?

Strojové učení je technika datové vědy, která umožňuje počítačům s využitím existujících dat předvídat budoucí chování, výsledky a trendy. Díky využití strojového učení, počítače můžou učit, aniž by byly explicitně naprogramované.

Díky předpovědím neboli predikcím ze strojového učení můžou být aplikace a zařízení chytřejší. Například při online nákupu, machine learning pomáhá zboží doporučit produkty, které můžete podle toho, co jste zakoupili. Nebo při protažení platební karty čtečkou porovnává strojové učení danou transakci s databází, a pomáhá tak odhalovat podvody. A takový robotický vysavač na základě strojového učení zjišťuje, jestli už v místnosti dostatečně uklidil.

## <a name="what-is-azure-machine-learning-service"></a>Co je služba Azure Machine Learning?

Služba Azure Machine Learning poskytuje prostředí založené na cloudu, které můžete použít ke pro přípravu dat, trénování, testování, nasazení, správě a sledování modelů strojového učení. Spustit kurz na místním počítači a potom horizontální navýšení kapacity na cloudu. Služba plně podporuje open source technologií, jako je například PyTorch, TensorFlow a scikit-informace a je možné pro libovolný typ služby machine learning z klasického ml pro obsáhlý learning, pod dohledem a supervize. 

Prozkoumat a přípravě dat, trénování a testování modely a nasadit je pomocí bohatých nástrojů, například:
+ A [vizuální rozhraní](ui-quickstart-run-experiment.md) v je možné přetáhnout n modulů k sestavení své experimenty a nasazovat modely
+ [Poznámkové bloky Jupyter](https://jupyter.org) způsoby, které můžete používat [sady SDK](https://docs.microsoft.com/azure/machine-learning/service/#reference) k psaní vlastního kódu, jako například [tyto ukázkové poznámkové bloky](https://aka.ms/aml-notebooks)
+ [Rozšíření sady Visual Studio Code](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Co můžu se službou Azure Machine Learning dělat?

Použít <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning Python SDK</a> s open sourcové balíčky Pythonu, nebo použijte [vizuální rozhraní (preview)](ui-quickstart-run-experiment.md) k vytvoření a trénování s velmi přesnými strojového učení a modely obsáhlého learningu sami v rámci služby Azure Machine Learning pracovního prostoru.

Můžete si vybrat z mnoha learning součásti počítače k dispozici v open sourcové balíčky Pythonu, například <a href="https://scikit-learn.org/stable/" target="_blank">Scikit poučení</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>a <a href="https://mxnet.io" target="_blank">MXNet</a>.

Ať už napsat kód, nebo můžete využít vizuální rozhraní, můžete sledovat více spuštění, jak experimentovat najít nejlepší řešení, stejně jako správu nasazených modelů.

### <a name="code-first-experience"></a>Založeno na kódu prostředí

Spustit kurz místního počítače <a href="https://aka.ms/aml-sdk" target="_blank">Azure Machine Learning Python SDK</a> a potom horizontální navýšení kapacity na cloudu. S mnoha dostupné [cílových výpočetních prostředí](how-to-set-up-training-targets.md), jako je Azure Machine Learning Compute a [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks)a s [pokročilé ladění služby hyperparameter](how-to-tune-hyperparameters.md), můžete vytvořit lepší modely rychleji s využitím výkonu cloudu.

Můžete také [automatizovat trénování modelu a ladění](tutorial-auto-train-models.md) pomocí sady SDK.

### <a name="code-free--low-code-experience"></a>Kód bez kódu nebo s nízkou prostředí

Bez kódu školení, vyzkoušejte:

+ Vizuální rozhraní pro experimentování a nasazování přetáhněte n
    
    ![Vizuální rozhraní pro službu Azure Machine Learning](media/overview-what-is-azure-ml/visual-interface.png)

+ Možnost Azure portal pro automatizované experimentů v ML

### <a name="operationalization-mlops"></a>Operacionalizace (MLOps)

Až budete mít správný modelu, můžete snadno ve webové službě, na zařízení IoT nebo z Power BI. Další informace najdete v článku na [nasazení a kde](how-to-deploy-and-where.md). 

Pak můžete spravovat své modely nasazené pomocí [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) nebo [webu Azure portal](https://portal.azure.com/). 

Tyto modely mohou být spotřebovány a vrátit předpovědi v [reálném čase](how-to-consume-web-service.md) nebo [asynchronně](how-to-run-batch-predictions.md) na velké množství data.

A pomocí rozšířeného [služby machine learning kanály](concept-ml-pipelines.md), můžete spolupracovat na každý krok od přípravy dat, trénování modelu a vyhodnocení, až po nasazení.

Chcete-li začít používat službu Azure Machine Learning, přečtěte si téma [další kroky](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Jak služba Azure Machine Learning se liší od Studio?

[Machine Learning Studio](../studio/what-is-ml-studio.md) je spolupráci, přetáhněte myší vizuální pracovní prostor ve kterém můžete vytvářet, testování a nasazení řešení strojového učení, aniž byste museli psát kód. Používá algoritmů předkonfigurovaná a předem připravených strojového učení a moduly pro zpracování dat, stejně jako vlastnický výpočetní platformě.

Služba Azure Machine Learning poskytuje obě sady SDK **- a -** visual interface(preview), chcete-li rychle pro přípravu dat, trénovat a nasazovat modely machine learningu. Toto rozhraní visual (preview) poskytuje fungují na podobném principu přetahování myší do sady Studio. Na rozdíl od proprietární výpočetní platforma sady Studio, ale vizuální rozhraní používá výpočetních prostředků a je plně integrován do služby Azure Machine Learning.

Tady je rychlý porovnání.

|| Machine Learning Studio | Služba Azure Machine Learning:<br/>Vizuální rozhraní|
|---| --- | --- |
|| Obecně dostupná (GA) | Ve verzi Preview|
|Moduly pro rozhraní| mnoho | Počáteční sadu Oblíbené moduly|
|Cílových výpočetních prostředí pro školení| Cílové výpočetní prostředí proprietární, pouze Podpora procesoru| Podporuje Azure Machine Learning compute, GPU nebo CPU.<br/>(Další výpočetní prostředí nepodporuje v sadě Windows SDK)|
|Nasazení cílových výpočetních prostředí| Formát vlastnické webové služby, nelze přizpůsobit | Možnosti zabezpečení organizace a službě Azure Kubernetes. <br/>([Další výpočetní prostředí](how-to-deploy-and-where.md) podporováno v sadě Windows SDK) |
|Cvičení modelu automatizované a hyperparametrů | Ne | Ještě nejsou součástí vizuální rozhraní. <br/> (Podporováno v sadě SDK a Azure portal.) | 

Vyzkoušejte si službu rozhraní visual (preview) [rychlý start: Příprava a vizualizace dat bez psaní kódu](ui-quickstart-run-experiment.md)

> [!NOTE]
> Modely vytvořené v sadě Studio nelze nasadit nebo spravované službou Azure Machine Learning. Modely vytvořené a nasazené ve vizuální rozhraní služby však můžete spravovat prostřednictvím pracovního prostoru služby Azure Machine Learning.

## <a name="free-trial"></a>Bezplatná zkušební verze

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

Získáte kredity, které můžete využít na služby Azure. Po jejich vyčerpání si můžete účet ponechat a používat [bezplatné služby Azure](https://azure.microsoft.com/free/). Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby. Nebo [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), které získáte kredity každý měsíc, můžete použít pro placené služby Azure.

## <a name="next-steps"></a>Další postup

- [Vytvořit pracovní prostor služby Machine Learning](setup-create-workspace.md) začít.

- Postupujte podle najdete kurzy: 
  + [Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning](tutorial-train-models-with-aml.md) 
  + [Příprava dat a používat automatizované machine learningu automaticky – train regresní model](tutorial-data-prep.md)

- Seznamte se s [kanály strojového učení](/azure/machine-learning/service/concept-ml-pipelines), které umožňují vytvářet, optimalizovat a spravovat scénáře strojového učení.

- Přečtěte si podrobné [architektura služby Azure Machine Learning a koncepty](concept-azure-machine-learning-architecture.md) článku.

- Další informace najdete v tématu [jiných strojového učení produkty společnosti Microsoft](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
