---
title: Co je ML Studio (klasika)
titleSuffix: Azure
description: Azure Machine Learning Studio (klasický) je nástroj pro přetahování pro rychlé vytváření modelů z připravené knihovny algoritmů a modulů.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: c1772ceb514e46542129759711f2d45db39abf82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371966"
---
# <a name="what-is-machine-learning-studio-classic"></a>Co je Machine Learning Studio (klasika)?

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (klasika) je kolaborativní nástroj pro přetahování, který můžete použít k vytváření, testování a nasazování prediktivních analytických řešení na vašich datech. Azure Machine Learning Studio (klasické) publikuje modely jako webové služby, které lze snadno spotřebovat vlastní aplikace nebo nástroje BI, jako je Excel.

Machine Learning Studio (klasické) je místo, kde se setkávají datové vědy, prediktivní analýzy, cloudové prostředky a vaše data.

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Interaktivní pracovní prostor Machine Learning Studio (klasický)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Chcete-li vyvinout model prediktivní analýzy, obvykle používáte data z jednoho nebo více zdrojů, transformujete a analyzujete tato data prostřednictvím různých funkcí manipulace s daty a statistických funkcí a generujete sadu výsledků. Vývoj takového modelu je iterativní proces. Úpravou různých funkcí a jejich parametrů se výsledky zpřesňují, dokud nebudete přesvědčeni, že máte natrénován efektivní model.

Azure Machine Learning Studio (classic) vám poskytuje interaktivní vizuální pracovní prostor pro snadné sestavení, testování a iterovat na modelu prediktivní analýzy. ***Přetažením datových sad*** a ***modulů analýzy*** na interaktivní plátno je spojujete a vytvoříte ***experiment***, který spustíte ve studiu Machine Learning Studio (klasika). Iterace návrhu modelu probíhá tak, že experiment upravíte, v případě potřeby uložíte kopii, a spustíte jej znovu. Až budete připraveni, můžete ***výukový experiment*** převést na ***prediktivní experiment*** a publikovat jej jako ***webovou službu***, aby k vašemu modelu měli přístup i jiní uživatelé.

Neexistuje žádné programování potřebné, vizuálně připojit datové sady a moduly k vytvoření modelu prediktivní analýzy.

![Azure Machine Learning Studio (klasický) diagram: Vytvářet experimenty, číst data pro mnoho zdrojů, psát s kórovaná data, psát modely.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>Stažení diagramu přehledu Machine Learning Studio (klasické)
Stáhněte si diagram **přehledu funkcí Microsoft Azure Machine Learning Studio (klasické)** a získejte přehled funkcí na vysoké úrovni o možnostech Machine Learning Studia (klasické). Pokud ho chcete mít po ruce, můžete si ho vytisknout ve velikosti tabloid (11 × 17 palců).

**Stáhněte si diagram zde: [Microsoft Azure Machine Learning Studio (klasický) Přehled](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![schopností Microsoft Azure Machine Learning Studio (klasický) Přehled schopností](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Součásti studiového (klasického) experimentu
Experiment sestává z datových sad, které poskytují data analytickým modulům. Ty pak lze vzájemně propojit a vytvořit tak model prediktivní analýzy. Platný experiment má konkrétně tyto charakteristiky:

* Experiment obsahuje alespoň jednu datovou sadu a jeden modul.
* Datová sady je možné připojit jen k modulům.
* Moduly je možné připojovat jak k datovým sadám, tak k jiným modulům.
* Všechny vstupní porty modulů musí určitým způsobem souviset s tokem dat.
* Musí být nastaveny všechny povinné parametry jednotlivých modulů.

Experiment můžete vytvořit zcela od začátku, ale také můžete využít existující ukázku jako šablonu. Další informace najdete v tématu [Vytváření nových experimentů se strojovým učením na základě kopírování příkladů experimentů](sample-experiments.md).

Příklad vytvoření experimentu najdete [v tématu Vytvoření jednoduchého experimentu v Azure Machine Learning Studio (klasické).](create-experiment.md)

Podrobnější návod k vytvoření řešení prediktivní analýzy najdete v [tématu Vývoj prediktivního řešení s Azure Machine Learning Studio (klasické).](tutorial-part1-credit-risk.md)

### <a name="datasets"></a>Datové sady
Datová sada je data, která byla odeslána do Machine Learning Studio (klasické), takže je lze použít v procesu modelování. V machine learningovém studiu (klasickém) je k dispozici řada ukázkových datových sad, se kterými můžete experimentovat, a můžete nahrát další datové sady podle potřeby. Zde jsou některé příklady dodávaných datových sad:

* **Data o spotřebě paliv u různých automobilů** – Hodnoty spotřeby paliva u automobilů na jednotku vzdálenosti (MPG) stanovené podle počtu válců, koňských sil apod.
* **Data o rakovině prsu** – Diagnostická data rakoviny prsu
* **Data o lesních požárech** – Rozsahy lesních požárů v severovýchodním Portugalsku

Při vytváření experimentu si můžete vybrat ze seznamu datových sad dostupných nalevo od plátna.

Seznam ukázkových datových sad zahrnutých v Machine Learning Studio (klasické) najdete v [tématu Použití ukázkových datových sad v Azure Machine Learning Studio (klasické).](use-sample-datasets.md)

### <a name="modules"></a>Moduly
Modul je algoritmus, který je možné provést na datech. Azure Machine Learning Studio (classic) má řadu modulů od funkcí příchozího přenosu dat až po procesy školení, vyhodnocování a ověřování. Zde jsou některé příklady dodávaných modulů:

* [Převod na ARFF][convert-to-arff] – Převede serializovanou datovou sadu .NET do formátu ARFF (Attribute-Relation File Format).
* [Výpočet základních statistik][elementary-statistics] – Vypočítá základní statistiky, jako je střední hodnota, směrodatná odchylka atd.
* [Lineární regrese][linear-regression] – Vytvoří online model lineární regrese na základě klesání gradientu.
* [Určení skóre modelu][score-model] – Stanoví skóre pro trénovaný klasifikační nebo regresní model.

Při vytváření experimentu si můžete vybrat ze seznamu modulů dostupných nalevo od plátna.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když na plátnu vyberete modul, parametry modulu se zobrazí v podokně **Vlastnosti** napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat.

Některé nápovědy k navigaci prostřednictvím velké knihovny algoritmů strojového učení, které jsou k dispozici, najdete v [tématu Jak vybrat algoritmy pro Microsoft Azure Machine Learning Studio (klasické)](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Nasazení webové služby prediktivní analýzy
Jakmile je váš model prediktivní analýzy připraven, můžete jej nasadit jako webovou službu přímo z Machine Learning Studio (klasika). Další informace o tomto procesu najdete [v tématu Nasazení webové služby Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Další kroky
Základy prediktivní analýzy a strojového učení se můžete naučit pomocí [podrobného rychlého startu](create-experiment.md) a [na základě ukázek](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
