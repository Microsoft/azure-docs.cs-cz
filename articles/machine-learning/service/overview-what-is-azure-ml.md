---
title: Co je Azure Machine Learning
description: Přehled Azure Machine Learning – integrované, komplexní řešení datové vědy pro profesionální odborníky na data pro vývoj, experimentování a nasazování pokročilých analytických aplikací na úrovni cloudu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: c845966c86659c0ff983bf33c492a67dd99275f0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692932"
---
# <a name="what-is-azure-machine-learning"></a>Co je Azure Machine Learning?

Azure Machine Learning je cloudová služba, která slouží ke školení, nasazení, automatizaci a správě modelů strojového učení, a to vše v široké škále poskytované cloudem.

## <a name="what-is-machine-learning"></a>Co je machine learning?

Strojové učení je technika datové vědy, která umožňuje počítačům s využitím existujících dat předvídat budoucí chování, výsledky a trendy. Pomocí strojového učení se počítače učí bez explicitního programování.

Díky předpovědím neboli predikcím ze strojového učení můžou být aplikace a zařízení chytřejší. Například při nakupování online pomůže strojové učení doporučit další produkty, které byste mohli chtít v závislosti na zakoupení. Nebo při protažení platební karty čtečkou porovnává strojové učení danou transakci s databází, a pomáhá tak odhalovat podvody. A takový robotický vysavač na základě strojového učení zjišťuje, jestli už v místnosti dostatečně uklidil.

## <a name="what-is-azure-machine-learning"></a>Co je Azure Machine Learning?

Azure Machine Learning poskytuje cloudové prostředí, které můžete použít k přípravu dat, výuce, testování, nasazování, správě a sledování modelů strojového učení. Spusťte školení na místním počítači a pak nahorizontální navýšení kapacity do cloudu. Služba plně podporuje Open Source technologie, jako jsou PyTorch, TensorFlow a scikit-Learning a dá se použít pro jakýkoliv druh strojového učení, od klasického ml až po obsáhlý Learning, pod dohledem a bez dohledu.

Prozkoumejte a připravte modely dat, výukové a testovací modely a nasaďte je pomocí bohatých nástrojů, jako jsou:
+ [Vizuální rozhraní](ui-tutorial-automobile-price-train-score.md) , ve kterém můžete přetahovat moduly na více systémů, abyste mohli sestavovat experimenty a pak nasazovat modely
+ [Jupyter poznámkové bloky](https://jupyter.org) , ve kterých používáte sady [SDK](https://docs.microsoft.com/azure/machine-learning) k psaní vlastního kódu, například [těchto ukázkových poznámkových bloků](https://aka.ms/aml-notebooks)
+ [Rozšíření Visual Studio Code](how-to-vscode-tools.md)


> [!VIDEO https://channel9.msdn.com/Events/Connect/Microsoft-Connect--2018/D240/player]

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>Co můžu se službou Azure Machine Learning dělat?

Pomocí <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">sady SDK Azure Machine Learning Pythonu</a> s open source balíčky Pythonu nebo pomocí [vizuálního rozhraní (Preview)](ui-tutorial-automobile-price-train-score.md) můžete vytvářet a proškolovat vysoce přesné modely strojového učení a hloubkového učení ve službě Azure Machine Learning Stejných.

Můžete si vybrat z mnoha součástí strojového učení, které jsou dostupné v open source balíčcích Pythonu, například <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-</a>Learning, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a>a <a href="https://mxnet.io" target="_blank">MXNet</a>.

Bez ohledu na to, jestli píšete kód nebo použijete vizuální rozhraní, můžete sledovat více běhů během experimentů, abyste našli nejlepší řešení a mohli spravovat nasazené modely.

### <a name="code-first-experience"></a>Prostředí s prvním kódem

Spusťte na svém místním počítači školení pomocí <a href="https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py" target="_blank">Azure Machine Learning Python SDK</a> a nahorizontálního navýšení kapacity do cloudu. Díky mnoha dostupným [výpočetním cílům](how-to-set-up-training-targets.md), jako je Azure Machine Learning compute a [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), a s [pokročilými službami ladění pomocí parametrů](how-to-tune-hyperparameters.md)můžete vytvářet lepší modely rychleji pomocí výkonného cloudu.

Můžete také [automatizovat školicí a optimalizační model](tutorial-auto-train-models.md) pomocí sady SDK.

### <a name="ui-based-low-code-experience"></a>Prostředí s nízkým kódem na základě uživatelského rozhraní

V případě školení a nasazení bez kódu zkuste:

+ Vytváření [automatizovaných experimentů ml](tutorial-first-experiment-automated-ml.md) v snadno použitelném rozhraní.
+ [Experimentování s přetahováním přetažení do vizuálního rozhraní](ui-tutorial-automobile-price-train-score.md).
  ![Visual rozhraní pro Azure Machine Learning](media/overview-what-is-azure-ml/visual-interface.png)



### <a name="operationalization-mlops"></a>Provozní operace (MLOps)

Pokud máte správný model, můžete ho snadno použít ve webové službě, v zařízení IoT nebo v Power BI. Další informace najdete v článku věnovaném [nasazení a umístění](how-to-deploy-and-where.md).

Pak můžete nasadit nasazené modely pomocí [sady Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk), [Azure Portal](https://portal.azure.com/) nebo [cílové stránky pracovního prostoru (Preview)](https://ml.azure.com).

Tyto modely lze spotřebovat a vracet předpovědi v [reálném čase](how-to-consume-web-service.md) nebo [asynchronně](how-to-run-batch-predictions.md) u velkých objemů dat.

A díky pokročilým [kanálům strojového učení](concept-ml-pipelines.md)můžete v rámci přípravy dat, školení a vyhodnocení modelu spolupracovat na všech krocích prostřednictvím nasazení. Kanály umožňují:

* Automatizace kompletního procesu strojového učení v cloudu
* Znovu použít komponenty a v případě potřeby znovu spustit pouze kroky
* Použití různých výpočetních prostředků v každém kroku
* Spustit úlohy dávkového vyhodnocování

Pokud chcete začít používat Azure Machine Learning, přečtěte si [Další kroky](#next-steps).

## <a name="how-does-azure-machine-learning-differ-from-studio"></a>Jak se Azure Machine Learning liší od studia?

[Machine Learning Studio](../studio/what-is-ml-studio.md) je spolupráce, přetahováním vizuálního pracovního prostoru, kde můžete sestavovat, testovat a nasazovat řešení strojového učení, aniž byste museli psát kód. Používá předem sestavené a předem nakonfigurované algoritmy strojového učení a moduly pro zpracování dat a také speciální výpočetní platformu.

Azure Machine Learning **poskytuje sady SDK i vizuální** rozhraní (Preview) pro rychlé přípravu dat, výuku a nasazení modelů strojového učení. Toto vizuální rozhraní (Preview) nabízí podobné prostředí pro přetahování do studia. Na rozdíl od proprietární výpočetní platformy v studiu ale používá vizuální rozhraní vaše vlastní výpočetní prostředky a je plně integrovaná do Azure Machine Learning.

Tady je rychlé porovnání.

|| Machine Learning Studio | Azure Machine Learning:<br/>Vizuální rozhraní|
|---| --- | --- |
|| Všeobecně dostupná (GA) | Ve verzi Preview|
|Rozhraní přetažení| Ano | Ano|
|Začátku| Škálování (limit pro školicí data pro 10GB) | Škálování s cílem výpočetního prostředí|
|Moduly pro rozhraní| Počtu | Počáteční sada oblíbených modulů|
|Školení výpočetních cílů| Proprietární výpočetní cíl, jenom procesor|AML COMPUTE (GPU/CPU)<br/> Virtuální počítače poznámkového bloku |
|Inferencing výpočetní cíle| Speciální formát webové služby, není přizpůsobitelný | Služba Azure Kubernetes (Inferencing v reálném čase) <br/>COMPUTE AML (Batch Inferencing) |
|Kanál ML| Nepodporováno | Vytváření kanálů <br/> publikovaný kanál <br/> Koncový bod kanálu <br/> [Další informace o kanálu ML](concept-ml-pipelines.md)|
|Operace ML| Základní Správa modelů a nasazení | Konfigurovatelné nasazení, model a správa verzí kanálu|
|Model| Speciální formát. Nelze použít mimo Studio | Standardní formát, různé závisí na úloze školení.|
|Automatizované školení modelů a ladění parametrů | Ne | Ještě není v vizuálním rozhraní. <br/> (Podporováno v sadě Python SDK a na cílové stránce pracovního prostoru.) |

Vyzkoušejte si vizuální rozhraní (Preview) s [kurzem: předpověď ceny automobilu pomocí vizuálního rozhraní](ui-tutorial-automobile-price-train-score.md).

> [!NOTE]
> Modely vytvořené v nástroji Studio nejde nasadit nebo spravovat pomocí Azure Machine Learning. Modely vytvořené a nasazené v rámci vizuálního rozhraní služby je ale možné spravovat prostřednictvím pracovního prostoru Azure Machine Learning.

## <a name="free-trial"></a>Bezplatná zkušební verze

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

Získáte kredity, které můžete využít na služby Azure. Po jejich vyčerpání si můžete účet ponechat a používat [bezplatné služby Azure](https://azure.microsoft.com/free/). Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby. Nebo můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), které vám každý měsíc poskytnou kredity, které můžete použít pro placené služby Azure.

## <a name="next-steps"></a>Další kroky

- Začněte tím, [že vytvoříte pracovní prostor služby Machine Learning](how-to-manage-workspace.md) .

- Sledujte kurzy s plnou délkou:
  + [Vytvoření pracovního prostoru a výuka prvního modelu ML](tutorial-1st-experiment-sdk-setup.md)
  + [Výuka modelu klasifikace obrázků pomocí Azure Machine Learning](tutorial-train-models-with-aml.md)


- Seznamte se s [kanály strojového učení](/azure/machine-learning/service/concept-ml-pipelines), které umožňují vytvářet, optimalizovat a spravovat scénáře strojového učení.

- Přečtěte si podrobnou [architekturu Azure Machine Learning a koncepty](concept-azure-machine-learning-architecture.md) .

- Další informace najdete v tématu [Další produkty pro strojové učení od Microsoftu](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
