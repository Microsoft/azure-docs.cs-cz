---
title: Co je služba Azure Machine Learning?
description: Přehled služby Azure Machine Learning – představuje řešení integrované, koncové až do konce datové vědy pro profesionální datové vědce, které k vývoji, experimentování a nasazování pokročilých analytických aplikací na úrovni cloudu.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: c3c0697af739c151f9aa7cbaed65283a365be7a7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077218"
---
# <a name="what-is-azure-machine-learning-service"></a>Co je služba Azure Machine Learning?

Služba Azure Machine Learning je Cloudová služba, která můžete použít k trénování, nasazení, automatizaci a správu modelů strojového učení, vše na široké měřítko, které cloud poskytuje.

## <a name="what-is-machine-learning"></a>Co je strojové učení?

Strojové učení je technika datové vědy, která umožňuje počítačům s využitím existujících dat předvídat budoucí chování, výsledky a trendy. Pomocí strojového učení se počítače můžou učit, aniž by byly explicitně naprogramované.

Díky předpovědím neboli predikcím ze strojového učení můžou být aplikace a zařízení chytřejší. Například při online nakupování je díky strojovému učení možné na základě již pořízeného zboží doporučit produkty, které by se vám mohly líbit. Nebo při protažení platební karty čtečkou porovnává strojové učení danou transakci s databází, a pomáhá tak odhalovat podvody. A takový robotický vysavač na základě strojového učení zjišťuje, jestli už v místnosti dostatečně uklidil.

## <a name="what-is-azure-machine-learning-service"></a>Co je služba Azure Machine Learning?

Služba Azure Machine Learning poskytuje cloudové prostředí, které můžete použít k vývoji, trénování, testování, nasazení, správě a sledování modelů strojového učení.

[ ![Pracovní postup služby Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

Služba Azure Machine Learning plně podporuje technologie Open Source, takže s komponentami strojového učení můžete používat desítky tisíc volně dostupných balíčků Pythonu, například TensorFlow a scikit-learn.
Podpora pro bohaté nástroje, jako například [poznámkové bloky Jupyter](http://jupyter.org) nebo [Azure Machine Learning pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview) rozšíření, usnadňuje interaktivní zkoumání dat, transformují je a potom vývoj a testování modely.
Služba Azure Machine Learning obsahuje rovněž funkce, které [automatizují generování a optimalizaci modelů](tutorial-auto-train-models.md) a pomáhají vám tak snadno a efektivně vytvářet přesné modely.

Služba Azure Machine Learning umožňuje spustit trénink na místním počítači a následně horizontálně navýšit kapacitu do cloudu. S mnoha dostupné [cílových výpočetních prostředí](how-to-set-up-training-targets.md) jako je Azure Machine Learning Compute a [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks)a s [pokročilé ladění služby hyperparameter](how-to-tune-hyperparameters.md), můžete vytvořit rychlejší a lepší modely Využijte sílu cloudu.

Až budete mít správný model, jednoduše ho nasadíte do kontejneru, jako je Docker. To znamená, že je snadno nasadit do Azure Container Instances nebo Azure Kubernetes Service nebo kontejneru můžete použít ve vlastním nasazení, ať už místní nebo v cloudu. Další informace najdete v tématu [nasazení a kde](how-to-deploy-and-where.md) dokumentu.
Během experimentování při hledání nejlepšího řešení můžete nasazené modely spravovat a sledovat různé běhy.
Po nasazení modelu můžete vrátit předpovědi v [v reálném čase](how-to-consume-web-service.md), nebo [asynchronně](how-to-run-batch-predictions.md) na velké množství data.

A pomocí rozšířeného [služby machine learning kanály](concept-ml-pipelines.md), můžete spolupracovat na všechny kroky pro přípravu dat, trénování modelů a vyhodnocení a nasazení.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Co můžu se službou Azure Machine Learning dělat?

Služba Azure Machine Learning můžete automaticky – train model a ladit ji za vás.
Příklad najdete v článku s [kurzem pro automatické trénování modelu klasifikace pomocí automatizovaného strojového učení Azure](tutorial-auto-train-models.md).

Pomocí Azure Machine Learning <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> pro Python, spolu s open sourcové balíčky Python, můžete vytvářet a trénování s velmi přesnými strojového učení a hloubkového učení modelů sami v pracovním prostoru služby Azure Machine Learning.
Na výběr máte celou řadu komponent strojového učení dostupných v Open Source balíčcích Pythonu, například:

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Jakmile máte model, použijte k vytvoření kontejneru (jako je Docker), který je možné nasadit místně pro účely testování. Po dokončení testování modelu je nasadit jako produkční webové služby v Azure Container Instances nebo Azure Kubernetes Service. Další informace najdete v tématu [nasazení a kde](how-to-deploy-and-where.md) dokumentu.

Potom můžete spravovat nasazené modelů pomocí [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) nebo [webu Azure portal](https://portal.azure.com/).
Model metriky, obsloužených a opětovné nasazení nové verze modelu můžete vyhodnotit všechny a současně zajistit sledování experimenty modelu.

Pokud chcete službu Azure Machine Learning začít používat, podívejte se na [Další kroky](#next-steps) níže.

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Jak se služba Azure Machine Learning liší od Studia?

Azure Machine Learning Studio je vizuální, myší ovládaný pracovní prostor umožňující spolupráci, ve kterém můžete sestavovat, testovat a nasazovat řešení strojového učení bez nutnosti psaní kódu. Používá předpřipravené a předkonfigurované algoritmy strojového učení a moduly pro zpracování dat.

Machine Learning Studio použijte, pokud chcete rychle a snadno experimentovat s modely strojového učení a předdefinované algoritmy strojového učení jsou pro vaše řešení dostačující.

Službu Machine Learning použijte, pokud pracujete v prostředí Python, chcete mít větší kontrolu nad algoritmy strojového učení nebo chcete použít Open Source knihovny strojového učení.

> [!NOTE]
> Modely vytvořené v Azure Machine Learning Studiu nelze nasadit ani spravovat službou Azure Machine Learning.

## <a name="free-trial"></a>Bezplatná zkušební verze
Pokud nejste předplatitel, můžete si [zdarma otevřít účet Azure](https://aka.ms/amlfree). Získáte kredity, které můžete využít na služby Azure. Po jejich vyčerpání si můžete účet ponechat a používat [bezplatné služby Azure](https://azure.microsoft.com/free/). Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby. Alternativně můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), což vám dává kredity každý měsíc, můžete použít pro placené služby Azure.

## <a name="next-steps"></a>Další postup

- Vytvořit pracovní prostor Machine Learning Service začít [pomocí webu Azure portal](quickstart-get-started.md) nebo [v Pythonu](quickstart-create-workspace-with-python.md).

- Postupujte podle kurzu komplexně [trénování a nasadit model klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).

- [Použití Azure Machine Learning pro automatické generování a automatické ladění modelu](tutorial-auto-train-models.md).

- Seznamte se s [kanály strojového učení](/azure/machine-learning/service/concept-ml-pipelines), které umožňují vytvářet, optimalizovat a spravovat scénáře strojového učení.

- Přečtěte si podrobné [architektura služby Azure Machine Learning a koncepty](concept-azure-machine-learning-architecture.md) článku.

- Další informace o produkty machine learningu od Microsoftu, najdete v části [jiných strojového učení produkty společnosti Microsoft](./overview-more-machine-learning.md).


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->
