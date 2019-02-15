---
title: Co je
titleSuffix: Azure Machine Learning service
description: Přehled služby Azure Machine Learning – představuje řešení integrované, koncové až do konce datové vědy pro profesionální datové vědce, které k vývoji, experimentování a nasazování pokročilých analytických aplikací na úrovni cloudu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 36e0ca360cfbdfc77ce0a1768222f43f8864537c
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267253"
---
# <a name="what-is-azure-machine-learning-service"></a>Co je služba Azure Machine Learning?

Služba Azure Machine Learning je Cloudová služba, který použijete k natrénování, nasazení, automatizaci a správu modelů strojového učení, vše na široké měřítko, které cloud poskytuje.

## <a name="what-is-machine-learning"></a>Co je strojové učení?

Strojové učení je technika datové vědy, která umožňuje počítačům s využitím existujících dat předvídat budoucí chování, výsledky a trendy. Díky využití strojového učení, počítače můžou učit, aniž by byly explicitně naprogramované.

Díky předpovědím neboli predikcím ze strojového učení můžou být aplikace a zařízení chytřejší. Například při online nákupu, machine learning pomáhá zboží doporučit produkty, které můžete podle toho, co jste zakoupili. Nebo při protažení platební karty čtečkou porovnává strojové učení danou transakci s databází, a pomáhá tak odhalovat podvody. A takový robotický vysavač na základě strojového učení zjišťuje, jestli už v místnosti dostatečně uklidil.

## <a name="what-is-azure-machine-learning-service"></a>Co je služba Azure Machine Learning?

Služba Azure Machine Learning poskytuje prostředí založené na cloudu, které můžete použít ke pro přípravu dat, trénování, testování, nasazení, správě a sledování modelů strojového učení.

[ ![Pracovního postupu služby azure Machine Learning](./media/overview-what-is-azure-ml/aml.png)] (. / media/overview-what-is-azure-ml/aml.png#lightbox)

Služba Azure Machine Learning plně podporuje technologie open source. Proto můžete použít desítky tisíc open sourcové balíčky Pythonu pomocí strojového učení komponenty. Mezi příklady patří PyTorch, TensorFlow a scikit-informace.
Podpora pro bohaté nástroje usnadňuje interaktivně zkoumat a připravit data a pak vývoji a testování modely. Mezi příklady patří [poznámkové bloky Jupyter](http://jupyter.org) nebo [Azure Machine Learning pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview) rozšíření.
Služba Azure Machine Learning obsahuje rovněž funkce, které [automatizují generování a optimalizaci modelů](tutorial-auto-train-models.md) a pomáhají vám tak snadno a efektivně vytvářet přesné modely.

Pomocí služby Azure Machine Learning můžete spustit školení na místním počítači a potom horizontální navýšení kapacity na cloudu. S mnoha dostupné [cílových výpočetních prostředí](how-to-set-up-training-targets.md), jako je Azure Machine Learning Compute a [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks)a s [pokročilé ladění služby hyperparameter](how-to-tune-hyperparameters.md), můžete vytvořit lepší modely rychleji s využitím výkonu cloudu.

Až budete mít správný model, jednoduše ho nasadíte do kontejneru, jako je Docker. Proto je snadno nasadit do Azure Container Instances nebo Azure Kubernetes Service. Nebo můžete použít kontejner ve vlastním nasazení, ať už místní nebo v cloudu. Další informace najdete v článku na [nasazení a kde](how-to-deploy-and-where.md).

Můžete spravovat nasazených modelů a sledovat více spuštění, jak experimentovat se najít nejlepší řešení.
Po nasazení modelu můžete vrátit předpovědi v [reálném čase](how-to-consume-web-service.md) nebo [asynchronně](how-to-run-batch-predictions.md) na velké množství data.

A pomocí rozšířeného [služby machine learning kanály](concept-ml-pipelines.md), můžete spolupracovat na všechny kroky pro přípravu dat, trénování modelů a vyhodnocení a nasazení.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Co můžu se službou Azure Machine Learning dělat?

Použití <a href="https://aka.ms/aml-sdk" target="_blank">hlavní Python SDK</a> a <a href="https://aka.ms/data-prep-sdk" target="_blank">sady SDK pro Data Prep</a> pro Azure Machine Learning open sourcové balíčky Pythonu, tak můžete sestavit a trénování s velmi přesnými strojového učení a modely obsáhlého learningu sami v rámci služby Azure Machine Learning pracovního prostoru.
Můžete vybrat z mnoha strojového učení komponenty, které jsou dostupné v open sourcové balíčky Pythonu, jako jsou následující příklady:

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Služba Azure Machine Learning může taky autotrain modelu a automatické ladění ho za vás.
Příklad najdete v tématu [Vyškolíme model pomocí automatizovaných strojového učení regrese](tutorial-auto-train-models.md).

Jakmile budete mít model, použijte k vytvoření kontejneru, jako je Docker, který je možné nasadit místně pro účely testování. Po dokončení testování je tento model nasadíme jako webovou službu produkčního prostředí v Azure Container Instances nebo Azure Kubernetes Service. Další informace najdete v článku na [nasazení a kde](how-to-deploy-and-where.md).

Pak můžete spravovat své modely nasazené pomocí [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) nebo [webu Azure portal](https://portal.azure.com/).
Model metriky, obsloužených a opětovné nasazení nové verze modelu můžete vyhodnotit všechny a současně zajistit sledování experimenty modelu.

Chcete-li začít používat službu Azure Machine Learning, přečtěte si téma [další kroky](#next-steps).

## <a name="how-is-azure-machine-learning-service-different-from-machine-learning-studio"></a>Čím se liší od Machine Learning Studio služby Azure Machine Learning?

Azure Machine Learning Studio je vizuální, myší ovládaný pracovní prostor umožňující spolupráci, ve kterém můžete sestavovat, testovat a nasazovat řešení strojového učení bez nutnosti psaní kódu. Používá předkonfigurovaná a předem připravených algoritmů strojového učení a zpracování dat moduly. 

Machine Learning Studio použijte, pokud chcete rychle a snadno experimentovat s modely strojového učení a předdefinované algoritmy strojového učení jsou pro vaše řešení dostačující.

Službu Machine Learning použijte, pokud pracujete v prostředí Python, chcete mít větší kontrolu nad algoritmy strojového učení nebo chcete použít Open Source knihovny strojového učení.

> [!NOTE]
> Modelů sestavených v nástroji Azure Machine Learning Studio nelze nasadit nebo spravované službou Azure Machine Learning.

## <a name="free-trial"></a>Bezplatná zkušební verze

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

Získáte kredity, které můžete využít na služby Azure. Po jejich vyčerpání si můžete účet ponechat a používat [bezplatné služby Azure](https://azure.microsoft.com/free/). Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby. Nebo [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), které získáte kredity každý měsíc, můžete použít pro placené služby Azure.

## <a name="next-steps"></a>Další postup

- Vytvoření pracovního prostoru služby Machine Learning, abyste mohli začít [pomocí webu Azure portal](quickstart-get-started.md) (ne instalace přístup) nebo [v Pythonu](quickstart-create-workspace-with-python.md) (sada SDK instalovat přístup).

- Postupujte podle najdete kurzy: 
  + [Trénování modelu klasifikace obrázků pomocí služby Azure Machine Learning](tutorial-train-models-with-aml.md) 
  + [Příprava dat a používat automatizované machine learningu automaticky – train regresní model](tutorial-data-prep.md)
  
- Použití [sady SDK služby Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk) připravit vaše data.

- Seznamte se s [kanály strojového učení](/azure/machine-learning/service/concept-ml-pipelines), které umožňují vytvářet, optimalizovat a spravovat scénáře strojového učení.

- Přečtěte si podrobné [architektura služby Azure Machine Learning a koncepty](concept-azure-machine-learning-architecture.md) článku.

- Další informace najdete v tématu [jiných strojového učení produkty společnosti Microsoft](./overview-more-machine-learning.md).
