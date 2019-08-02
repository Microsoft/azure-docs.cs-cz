---
title: Co je
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio je nástroj pro přetahování, který slouží k rychlému vytváření modelů z připravené knihovny algoritmů a modulů.
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: 4ec9cff652bf1badf526d490547ad78de31ac5da
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678001"
---
# <a name="what-is-azure-machine-learning-studio"></a>Co je Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio je nástroj pro spolupráci, ve kterém je možné přetahováním vytvářet, testovat a nasazovat řešení prediktivní analýzy dat. Machine Learning Studio publikuje modely jako webové služby, které je možné snadno využívat ve vlastních aplikacích nebo nástrojích BI, například v Excelu.

Machine Learning Studio je místo, kde se setkává datová věda, prediktivní analýza, cloudové prostředky a vaše data.


## <a name="the-machine-learning-studio-interactive-workspace"></a>Interaktivní pracovní prostor Machine Learning Studio
Chcete-li vytvořit model prediktivní analýzy, obvykle používáte data z jednoho nebo více zdrojů, transformovat a analyzovat tato data prostřednictvím různých funkcí pro manipulaci s daty a statistické funkce a vygenerujeme sadu výsledků. Vývoj takového modelu je iterativní proces. Úpravou různých funkcí a jejich parametrů se výsledky zpřesňují, dokud nebudete přesvědčeni, že máte natrénován efektivní model.

**Azure Machine Learning Studio** nabízí interaktivní vizuální pracovní prostor, abyste mohli snadno vytvářet, testovat a iterovat model prediktivní analýzy. Při práci přetahujete ***datové sady*** a analytické ***moduly*** na interaktivní ***plátno***, kde je vzájemně propojujete. Tím vzniká experiment, který pak spustíte v nástroji Machine Learning Studio. Iterace návrhu modelu probíhá tak, že experiment upravíte, v případě potřeby uložíte kopii, a spustíte jej znovu. Jakmile budete připraveni, můžete ***výukový experiment*** převést na ***prediktivní experiment*** a publikovat jej jako ***webovou službu***, aby k vašemu modelu měli přístup i jiní uživatelé.

Nevyžaduje se žádné programování – model prediktivní analýzy se konstruuje vizuálním propojováním datových sad a modulů.

![Diagram Azure Machine Learning Studio: Vytvářejte experimenty, čtěte data pro mnoho zdrojů a zapište data s skóre a napište modely.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Stažení diagramu s přehledem nástroje Machine Learning Studio
Stáhněte si diagram s **přehledem možností nástroje Machine Learning Studio**, který na nejvyšší úrovni znázorňuje schéma funkcí nástroje Machine Learning Studio. Pokud ho chcete mít po ruce, můžete si ho vytisknout ve velikosti tabloid (11 × 17 palců).

**Diagram si můžete stáhnout tady: [](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)Přehled**možností
MicrosoftAzureMachineLearningStudioMicrosoftAzureMachineLearningStudiopřehledmožností![](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Začínáme s nástrojem Machine Learning Studio
Při prvním zadání [Machine Learning Studio](https://studio.azureml.net)se zobrazí domovská stránka . Tady si můžete zobrazit dokumentaci, videa a webináře a najít další cenné prostředky.

Vlevo nahoře klikněte na nabídku ![Nabídka](./media/what-is-ml-studio/menu.png) a zobrazí se několik možností.
### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
Tady jsou dvě možnosti – **Domů**, což je stránka, na které jste začali, a **Studio**.

Kliknutím na **Studio** přejdete do nástroje **Azure Machine Learning Studio**. Nejprve se zobrazí výzva, abyste se přihlásili účtem Microsoft, případně školním nebo pracovním účtem. Jakmile se přihlásíte, na levé straně uvidíte následující karty:

* **PROJEKTY** – Kolekce experimentů, datových sad, poznámkových bloků a jiných prostředků představující jeden objekt
* **EXPERIMENTY** – Experimenty, které jste vytvořili a spustili nebo uložili jako koncepty
* **WEBOVÉ SLUŽBY** – Webové služby, které jste nasadili z experimentů
* **POZNÁMKOVÉ BLOKY** – Jupyter Notebooks, které jste vytvořili
* **DATOVÉ SADY** – Datové sady, které jste nahráli do nástroje Studio
* **TRÉNOVANÉ MODELY** – Modely, které jste v experimentech natrénovali a uložili je v nástroji Studio
* **NASTAVENÍ** – Kolekce nastavení, kterou můžete použít ke konfiguraci účtu a prostředků

### <a name="gallery"></a>Galerie
Kliknutím na **Galerie** přejdete na ke **[galerii Azure AI](https://gallery.azure.ai/)** . Galerie je místo, kde komunita datových vědců a vývojářů sdílí řešení vytvořená pomocí komponent sady Cortana Intelligence Suite.

Další informace o této galerii najdete v tématu věnovaném [sdílení a hledání řešení v galerii Azure AI](gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Komponenty experimentu
Experiment sestává z datových sad, které poskytují data analytickým modulům. Ty pak lze vzájemně propojit a vytvořit tak model prediktivní analýzy. Platný experiment má konkrétně tyto charakteristiky:

* Experiment obsahuje alespoň jednu datovou sadu a jeden modul.
* Datová sady je možné připojit jen k modulům.
* Moduly je možné připojovat jak k datovým sadám, tak k jiným modulům.
* Všechny vstupní porty modulů musí určitým způsobem souviset s tokem dat.
* Musí být nastaveny všechny povinné parametry jednotlivých modulů.

Experiment můžete vytvořit zcela od začátku, ale také můžete využít existující ukázku jako šablonu. Další informace najdete v tématu [Vytváření nových experimentů se strojovým učením na základě kopírování příkladů experimentů](sample-experiments.md).

Příklad vytvoření jednoduchého experimentu najdete v tématu [Vytvoření jednoduchého experimentu v nástroji Azure Machine Learning Studio](create-experiment.md).

Úplnější návod k vytváření řešení prediktivní analýzy najdete v tématu [vývoj prediktivního řešení pomocí Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Datové sady
Datová sada obsahuje data, která byla nahrána do nástroje Machine Learning Studio, aby je bylo možné použít v procesu modelování. Součástí nástroje Machine Learning Studio je několik ukázkových datových sad, se kterými můžete experimentovat. Dle potřeby můžete nahrávat další datové sady. Zde jsou některé příklady dodávaných datových sad:

* **Data o spotřebě paliv u různých automobilů** – Hodnoty spotřeby paliva u automobilů na jednotku vzdálenosti (MPG) stanovené podle počtu válců, koňských sil apod.
* **Data o rakovině prsu** – Diagnostická data rakoviny prsu
* **Data o lesních požárech** – Rozsahy lesních požárů v severovýchodním Portugalsku

Při sestavování experimentu si můžete vybrat ze seznamu datových sad, které jsou k dispozici nalevo od plátna.

Seznam ukázkových datových sad obsažených v nástroji Machine Learning Studio, najdete v tématu o [využití ukázkových datových sad v nástroji Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Moduly
Modul je algoritmus, který je možné provést na datech. Machine Learning Studio obsahuje několik modulů od funkcí pro příjem dat po procesy trénování, stanovení skóre a ověřování. Zde jsou některé příklady dodávaných modulů:

* [Převést na arff][convert-to-arff] – převede serializovanou datovou sadu .NET na formát souboru relace atributu (arff).
* [Základní statistiky COMPUTE][elementary-statistics] – vypočítá základní statistiky, jako je střední hodnota, směrodatná odchylka atd.
* [Lineární regrese][linear-regression] – vytvoří model lineární regrese založený na klesání přechodu z online režimu.
* [Skóre modelu][score-model] – vyhodnotí vyškolený model klasifikace nebo regrese.

Během vytváření experimentu je možné si vybírat ze seznamu modulů, které jsou k dispozici v nabídce nalevo od plátna.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když na plátnu vyberete modul, parametry modulu se zobrazí v podokně **Vlastnosti** napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat.

Další informace o tom, jak se dá využít rozsáhlá knihovna algoritmů strojového učení, najdete v tématu [Jak zvolit algoritmy pro Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Nasazení webové služby prediktivní analýzy
Jakmile je váš model prediktivní analýzy připraven, můžete jej přímo v nástroji Machine Learning Studio nasadit jako webovou službu. Další podrobnosti k tomuto procesu najdete v tématu [Nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Jak se Machine Learning Studio liší od služby Azure Machine Learning?

[Služba Azure Machine Learning](../service/overview-what-is-azure-ml.md) poskytuje **Jak sady SDK** , tak i vizuální rozhraní (Preview) k rychlému přípravu dat, výuce a nasazování modelů strojového učení. Toto vizuální rozhraní (Preview) nabízí podobné prostředí pro přetahování do studia. Na rozdíl od proprietární výpočetní platformy v studiu ale používá vizuální rozhraní vaše vlastní výpočetní prostředky a je plně integrovaná do Azure Machine Learning služby.

Tady je rychlé porovnání.

|| Machine Learning Studio | Služba Azure Machine Learning:<br/>Vizuální rozhraní|
|---| --- | --- |
|| Všeobecně dostupná (GA) | Ve verzi Preview|
|Moduly pro rozhraní| Počtu | Počáteční sada oblíbených modulů|
|Školení výpočetních cílů| Proprietární výpočetní cíl, jenom podpora procesoru| Podporuje Azure Machine Learning výpočetní prostředky, GPU nebo CPU.<br/>(Další výpočetní prostředky podporované v sadě SDK)|
|Cíle výpočtů nasazení| Speciální formát webové služby, není přizpůsobitelný | Možnosti podnikového zabezpečení & služby Azure Kubernetes. <br/>([Další výpočetní](../service/how-to-deploy-and-where.md) prostředky podporované v sadě SDK) |
|Automatizované školení modelů a ladění parametrů | Ne | Ještě není v vizuálním rozhraní. <br/> (Podporováno v sadě SDK a Azure Portal.) | 

Vyzkoušejte si vizuální rozhraní (Preview) s [kurzem: Předpověď ceny automobilu pomocí vizuálního rozhraní](../service/ui-tutorial-automobile-price-train-score.md)

> [!NOTE]
> Modely vytvořené v nástroji Studio nejde nasadit nebo spravovat pomocí služby Azure Machine Learning Service. Modely vytvořené a nasazené v rámci vizuálního rozhraní služby je ale možné spravovat prostřednictvím pracovního prostoru služby Azure Machine Learning.

## <a name="free-trial"></a>Bezplatná zkušební verze

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Další postup
Seznamte se se základy prediktivní analýzy a strojového učení s využitím [podrobných rychlých](create-experiment.md) a sestavování [ukázek](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
