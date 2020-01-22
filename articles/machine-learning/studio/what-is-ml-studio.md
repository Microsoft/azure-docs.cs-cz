---
title: Co je ML Studio (Classic)
titleSuffix: Azure
description: Azure Machine Learning Studio (Classic) je nástroj pro přetahování, který umožňuje rychle vytvářet modely z připravené knihovny algoritmů a modulů.
services: machine-learning
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 10/17/2019
ms.openlocfilehash: f3be29dc222e0e9b98ed51d8f921fb9926b018ca
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314062"
---
# <a name="what-is-machine-learning-studio-classic"></a>Co je Machine Learning Studio (Classic)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (Classic) je nástroj pro spolupráci, který lze použít k sestavování, testování a nasazení prediktivních analytických řešení na vašich datech. Azure Machine Learning Studio (Classic) zveřejňují modely jako webové služby, které můžou snadno využívat vlastní aplikace nebo nástroje BI, jako je Excel.

Machine Learning Studio (Classic) je místo, kde se doplňují datové vědy, prediktivní analýzy, cloudové prostředky a vaše data.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Interaktivní pracovní prostor Machine Learning Studio (klasický)
Chcete-li vytvořit model prediktivní analýzy, obvykle používáte data z jednoho nebo více zdrojů, transformovat a analyzovat tato data prostřednictvím různých funkcí pro manipulaci s daty a statistické funkce a vygenerujeme sadu výsledků. Vývoj takového modelu je iterativní proces. Úpravou různých funkcí a jejich parametrů se výsledky zpřesňují, dokud nebudete přesvědčeni, že máte natrénován efektivní model.

Azure Machine Learning Studio (Classic) poskytuje interaktivní a vizuální pracovní prostor pro snadné sestavování, testování a iteraci v modelu prediktivní analýzy. ***Datové sady*** a ***moduly*** pro analýzu můžete přetahovat na interaktivní plátno, čímž je spojíte dohromady, abyste mohli vytvořit ***experiment***, který spustíte v Machine Learning Studio (Classic). Iterace návrhu modelu probíhá tak, že experiment upravíte, v případě potřeby uložíte kopii, a spustíte jej znovu. Jakmile budete připraveni, můžete ***výukový experiment*** převést na ***prediktivní experiment*** a publikovat jej jako ***webovou službu***, aby k vašemu modelu měli přístup i jiní uživatelé.

Není nutné žádné programování, vizuálně propojit datové sady a moduly a vytvořit model prediktivní analýzy.

![Diagram Azure Machine Learning Studio (klasický): vytvářet experimenty, číst data pro mnoho zdrojů, zapisovat data s skóre a zapisovat modely.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

<a name="compare"></a>
## <a name="how-does-machine-learning-studio-classic-differ-from-azure-machine-learning"></a>Jak se liší Machine Learning Studio (klasické) od Azure Machine Learning?

[Azure Machine Learning](../overview-what-is-azure-ml.md) **poskytuje sady SDK i nástroj** pro Azure Machine Learning Designer pro rychlé přípravu dat, výuku a nasazení modelů strojového učení. Návrhář nabízí podobné prostředí pro přetahování do studia (Classic). Na rozdíl od proprietární výpočetní platformy studia (Classic) ale Návrhář používá vaše vlastní výpočetní prostředky a je plně integrovaný do Azure Machine Learning.

Tady je rychlé porovnání:

|| Machine Learning Studio (Classic) | Azure Machine Learning |
|---| --- | --- |
| Rozhraní přetažení | Ano | Ano – [návrhář Azure Machine Learning](../concept-designer.md) |
| Experiment | Škálovatelné (limit pro školicí data z 10 GB) | Škálování s cílem výpočetního prostředí |
| Moduly pro rozhraní přetažení | Počtu | Počáteční sada oblíbených [modulů](../algorithm-module-reference/module-reference.md)|
|Školení výpočetních cílů| Proprietární výpočetní cíl, jenom podpora procesoru| Podporuje výpočetní virtuální počítače s Azure Machine Learning (GPU nebo CPU) a notebook.<br/>([Další výpočetní prostředky podporované v sadě SDK](../concept-compute-target.md#train))|
|Inferencing výpočetní cíle| Speciální formát webové služby, není přizpůsobitelný |  Služba Azure Kubernetes a služba AML COMPUTE <br/>([Další výpočetní prostředky podporované v sadě SDK](../how-to-deploy-and-where.md)) |
| Kanál ML | Nepodporováno | Podporované [kanály](../concept-ml-pipelines.md) |
| MLOps | Základní Správa modelů a nasazení | Konfigurovatelné nasazení – model a správa verzí a kanálů |
| Formát modelu | Speciální formát, jenom Studio (Classic) | Standardní formát v závislosti na typu úlohy školení |
|Automatizované školení modelů a ladění parametrů | Ne | Ještě není v návrháři. <br/> ([Podporováno na úvodní stránce sady SDK a pracovního prostoru](../concept-automated-ml.md)) | 

Vyzkoušejte si návrháře s [kurzem: předpověď ceny automobilu pomocí návrháře](../tutorial-designer-automobile-price-train-score.md)

> [!NOTE]
> Modely vytvořené v nástroji Studio (Classic) se nedají nasadit ani spravovat pomocí Azure Machine Learning. Modely vytvořené a nasazené v Návrháři ale můžete spravovat prostřednictvím pracovního prostoru Azure Machine Learning.

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Stáhnout diagram s přehledem Machine Learning Studio (Classic)
Stáhněte si diagram s **přehledem možností Microsoft Azure Machine Learning Studio (Classic)** a získejte nejdůležitější pohled na možnosti Machine Learning Studio (Classic). Pokud ho chcete mít po ruce, můžete si ho vytisknout ve velikosti tabloid (11 × 17 palců).

**Tento diagram si můžete stáhnout tady: [přehled možností Microsoft Azure Machine Learning Studio (classic)](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf) **
![Microsoft Azure Machine Learning Studio (classic) Přehled možností](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Komponenty experimentu studia (Classic)
Experiment sestává z datových sad, které poskytují data analytickým modulům. Ty pak lze vzájemně propojit a vytvořit tak model prediktivní analýzy. Platný experiment má konkrétně tyto charakteristiky:

* Experiment obsahuje alespoň jednu datovou sadu a jeden modul.
* Datová sady je možné připojit jen k modulům.
* Moduly je možné připojovat jak k datovým sadám, tak k jiným modulům.
* Všechny vstupní porty modulů musí určitým způsobem souviset s tokem dat.
* Musí být nastaveny všechny povinné parametry jednotlivých modulů.

Experiment můžete vytvořit zcela od začátku, ale také můžete využít existující ukázku jako šablonu. Další informace najdete v tématu [Vytváření nových experimentů se strojovým učením na základě kopírování příkladů experimentů](sample-experiments.md).

Příklad vytvoření experimentu najdete [v tématu Vytvoření jednoduchého experimentu v Azure Machine Learning Studio (Classic)](create-experiment.md).

Úplnější návod k vytváření řešení prediktivní analýzy najdete v tématu [vývoj prediktivního řešení pomocí Azure Machine Learning Studio (Classic)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Datové sady
Datová sada je data, která byla nahrána do Machine Learning Studio (Classic), aby ji bylo možné použít v procesu modelování. K dispozici je několik ukázkových datových sad, které jsou součástí Machine Learning Studio (Classic), abyste se mohli experimentovat s a můžete nahrávat více datových sad, jak je potřebujete. Zde jsou některé příklady dodávaných datových sad:

* **Data o spotřebě paliv u různých automobilů** – Hodnoty spotřeby paliva u automobilů na jednotku vzdálenosti (MPG) stanovené podle počtu válců, koňských sil apod.
* **Data o rakovině prsu** – Diagnostická data rakoviny prsu
* **Data o lesních požárech** – Rozsahy lesních požárů v severovýchodním Portugalsku

Při sestavování experimentu si můžete vybrat ze seznamu datových sad, které jsou k dispozici nalevo od plátna.

Seznam ukázkových datových sad, které jsou součástí Machine Learning Studio (Classic), najdete v tématu [použití ukázkových datových sad v Azure Machine Learning Studio (Classic)](use-sample-datasets.md).

### <a name="modules"></a>Moduly
Modul je algoritmus, který je možné provést na datech. Azure Machine Learning Studio (Classic) má několik modulů od vstupních funkcí dat až po procesy školení, bodování a ověřování. Zde jsou některé příklady dodávaných modulů:

* [Převést na arff][convert-to-arff] – převede serializovanou datovou sadu .NET na formát souboru relace atributu (arff).
* [Základní statistiky COMPUTE][elementary-statistics] – vypočítá základní statistiky, jako je střední hodnota, směrodatná odchylka atd.
* [Lineární regrese][linear-regression] – vytvoří model lineární regrese založený na klesání přechodu z online režimu.
* [Skóre modelu][score-model] – vyhodnotí vyškolený model klasifikace nebo regrese.

Při sestavování experimentu si můžete vybrat ze seznamu modulů, které jsou k dispozici nalevo od plátna.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když na plátnu vyberete modul, parametry modulu se zobrazí v podokně **Vlastnosti** napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat.

Další informace o tom, jak se dá využít rozsáhlá knihovna algoritmů strojového učení, najdete v tématu [Jak zvolit algoritmy pro Microsoft Azure Machine Learning Studio (Classic)](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Nasazení webové služby prediktivní analýzy
Jakmile je model prediktivní analýzy připravený, můžete ho nasadit jako webovou službu přímo z Machine Learning Studio (Classic). Další informace o tomto procesu najdete v tématu [nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Další kroky
Seznamte se se základy prediktivní analýzy a strojového učení s využitím [podrobných rychlých](create-experiment.md) a [sestavování ukázek](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
