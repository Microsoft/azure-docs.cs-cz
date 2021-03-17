---
title: Co můžu dělat s Machine Learning Studio (Classic) – Azure
description: Machine Learning Studio (Classic) je nástroj pro přetahování, který umožňuje rychle sestavovat modely z knihovny připravených algoritmů a modulů.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: ad0550dc20edfbc10fb8877ad049c27fc2edf086
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517531"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>Co se dá dělat s Machine Learning Studio (Classic)?

**platí pro:** ![ Toto je značka zaškrtnutí, což znamená, že se tento článek týká Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ Toto je X, což znamená, že se tento článek týká Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Machine Learning Studio (Classic) je nástroj pro přetahování, který můžete použít k sestavení, otestování a nasazení modelů strojového učení. Studio (Classic) zveřejňuje modely jako webové služby, které je možné snadno spotřebovat vlastními aplikacemi nebo nástroji BI, jako je Excel.


## <a name="studio-classic--interactive-workspace"></a>Interaktivní pracovní prostor Studio (Classic)

Chcete-li vytvořit model prediktivní analýzy, obvykle používáte data z jednoho nebo více zdrojů, transformovat a analyzovat tato data prostřednictvím různých funkcí pro manipulaci s daty a statistické funkce a vygenerujeme sadu výsledků. Vývoj podobného modelu, jako je tento, je iterativní proces. Úpravou různých funkcí a jejich parametrů se výsledky zpřesňují, až budete mít trénovaný a účinný model, se kterým budete spokojeni.

Machine Learning Studio (Classic) poskytuje interaktivní a vizuální pracovní prostor pro snadné sestavování, testování a iteraci v modelu prediktivní analýzy. Moduly můžete přetahovat ***datové sady** _ a analytické _*_moduly_*_ na interaktivní plátno a spojit je s ní a vytvořit _*_experiment_*_, který spustíte v Machine Learning Studio (Classic). Iterace návrhu modelu probíhá tak, že experiment upravíte, v případě potřeby uložíte kopii, a spustíte jej znovu. Až budete připraveni, můžete převést svůj _*_školicí experiment_*_ na _*_prediktivní experiment_*_ a pak ho publikovat jako *_webovou službu_* _ *, aby k vašemu modelu měli přístup i jiní uživatelé.

Není nutné žádné programování, vizuálně propojit datové sady a moduly a vytvořit model prediktivní analýzy.

![Diagram Machine Learning Studio (klasický): vytvářet experimenty, číst data pro mnoho zdrojů, zapisovat data s skóre a zapisovat modely.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Stáhnout diagram s přehledem ML Studio (Classic)
Stáhněte si diagram s **přehledem možností Microsoft ml Studio (Classic)** a Získejte podrobný přehled možností Machine Learning Studio (Classic). Pokud ho chcete mít po ruce, můžete si ho vytisknout ve velikosti tabloid (11 × 17 palců).

**Diagram si můžete stáhnout tady: [Přehled](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
 ![ možností Microsoft Machine Learning Studio (Classic). Přehled možností Microsoft Machine Learning Studio (Classic)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Komponenty experimentu studia (Classic)
Experiment sestává z datových sad, které poskytují data analytickým modulům. Ty pak lze vzájemně propojit a vytvořit tak model prediktivní analýzy. Platný experiment má konkrétně tyto charakteristiky:

* Experiment obsahuje alespoň jednu datovou sadu a jeden modul.
* Datová sady je možné připojit jen k modulům.
* Moduly je možné připojovat jak k datovým sadám, tak k jiným modulům.
* Všechny vstupní porty modulů musí určitým způsobem souviset s tokem dat.
* Musí být nastaveny všechny povinné parametry jednotlivých modulů.

Experiment můžete vytvořit zcela od začátku, ale také můžete využít existující ukázku jako šablonu. Další informace najdete v tématu [Vytváření nových experimentů se strojovým učením na základě kopírování příkladů experimentů](sample-experiments.md).

Příklad vytvoření experimentu najdete [v tématu Vytvoření experimentu v Machine Learning Studio (Classic)](create-experiment.md).

Úplnější návod k vytváření řešení prediktivní analýzy najdete v tématu [vývoj prediktivního řešení pomocí Machine Learning Studio (Classic)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Datové sady
Datová sada je data, která byla nahrána do Machine Learning Studio (Classic), aby ji bylo možné použít v procesu modelování. K dispozici je několik ukázkových datových sad, které jsou součástí Machine Learning Studio (Classic), abyste se mohli experimentovat s a můžete nahrávat více datových sad, jak je potřebujete. Zde jsou některé příklady dodávaných datových sad:

* **Data o spotřebě paliv u různých automobilů** – Hodnoty spotřeby paliva u automobilů na jednotku vzdálenosti (MPG) stanovené podle počtu válců, koňských sil apod.
* **Data o rakovině prsu** – Diagnostická data rakoviny prsu
* **Data o lesních požárech** – Rozsahy lesních požárů v severovýchodním Portugalsku

Při sestavování experimentu si můžete vybrat ze seznamu datových sad, které jsou k dispozici nalevo od plátna.

Seznam ukázkových datových sad, které jsou součástí Machine Learning Studio (Classic), najdete v tématu [použití ukázkových datových sad v Machine Learning Studio (Classic)](use-sample-datasets.md).

### <a name="modules"></a>Moduly
Modul je algoritmus, který je možné provést na datech. Machine Learning Studio (Classic) má několik modulů od vstupních funkcí dat až po procesy školení, bodování a ověřování. Zde jsou některé příklady dodávaných modulů:

* [Převod na ARFF][convert-to-arff] – Převede serializovanou datovou sadu .NET do formátu ARFF (Attribute-Relation File Format).
* [Výpočet základních statistik][elementary-statistics] – Vypočítá základní statistiky, jako je střední hodnota, směrodatná odchylka atd.
* [Lineární regrese][linear-regression] – Vytvoří online model lineární regrese na základě klesání gradientu.
* [Určení skóre modelu][score-model] – Stanoví skóre pro trénovaný klasifikační nebo regresní model.

Při sestavování experimentu si můžete vybrat ze seznamu modulů, které jsou k dispozici nalevo od plátna.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když na plátnu vyberete modul, parametry modulu se zobrazí v podokně **Vlastnosti** napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat.

Další informace o tom, jak se dá použít rozsáhlá knihovna algoritmů strojového učení, najdete v tématu [postup výběru algoritmů pro Microsoft Machine Learning Studio (Classic)](../how-to-select-algorithms.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Nasazení webové služby prediktivní analýzy
Jakmile je model prediktivní analýzy připravený, můžete ho nasadit jako webovou službu přímo z Machine Learning Studio (Classic). Další informace o tomto procesu najdete v tématu [nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Další kroky
Seznamte se se základy prediktivní analýzy a strojového učení s využitím [podrobných rychlých](create-experiment.md) a [sestavování ukázek](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: /azure/machine-learning/studio-module-reference/convert-to-arff
[elementary-statistics]: /azure/machine-learning/studio-module-reference/compute-elementary-statistics
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[score-model]: /azure/machine-learning/studio-module-reference/score-model