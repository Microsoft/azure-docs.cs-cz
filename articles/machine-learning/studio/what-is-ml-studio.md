---
title: Co je
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio je nástroj přetažení myší rychle vytvářet modely z připravené k použití knihovny algoritmů a modulů.
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: 7a2bd8ebf2e96cf2251d98501bbf42007d43a660
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60345510"
---
# <a name="what-is-azure-machine-learning-studio"></a>Co je Azure Machine Learning Studio?
Microsoft Azure Machine Learning Studio je nástroj pro spolupráci, ve kterém je možné přetahováním vytvářet, testovat a nasazovat řešení prediktivní analýzy dat. Machine Learning Studio publikuje modely jako webové služby, které je možné snadno využívat ve vlastních aplikacích nebo nástrojích BI, například v Excelu.

Machine Learning Studio je místo, kde se setkává datová věda, prediktivní analýza, cloudové prostředky a vaše data.


## <a name="the-machine-learning-studio-interactive-workspace"></a>Interaktivní pracovní prostor Machine Learning Studio
K vývoji modelu prediktivní analýzy zpravidla použijete data z jednoho nebo více zdrojů, transformujete a analyzujete je prostřednictvím různých statistických funkcí a funkcí pro manipulaci s daty a vygenerujete sadu výsledků. Vývoj takového modelu je iterativní proces. Úpravou různých funkcí a jejich parametrů se výsledky zpřesňují, dokud nebudete přesvědčeni, že máte natrénován efektivní model.

**Azure Machine Learning Studio** nabízí interaktivní vizuální pracovní prostor, abyste mohli snadno vytvářet, testovat a iterovat model prediktivní analýzy. Při práci přetahujete ***datové sady*** a analytické ***moduly*** na interaktivní ***plátno***, kde je vzájemně propojujete. Tím vzniká experiment, který pak spustíte v nástroji Machine Learning Studio. Iterace návrhu modelu probíhá tak, že experiment upravíte, v případě potřeby uložíte kopii, a spustíte jej znovu. Jakmile budete připraveni, můžete ***výukový experiment*** převést na ***prediktivní experiment*** a publikovat jej jako ***webovou službu***, aby k vašemu modelu měli přístup i jiní uživatelé.

Nevyžaduje se žádné programování – model prediktivní analýzy se konstruuje vizuálním propojováním datových sad a modulů.

![Azure Machine Learning studio diagram: Vytváření experimentů, čtení dat pro mnoho zdrojů, zápis dat se stanoveným skóre, zápis modelů.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Stažení diagramu s přehledem nástroje Machine Learning Studio
Stáhněte si diagram s **přehledem možností nástroje Machine Learning Studio**, který na nejvyšší úrovni znázorňuje schéma funkcí nástroje Machine Learning Studio. Pokud ho chcete mít po ruce, můžete si ho vytisknout ve velikosti tabloid (11 × 17 palců).

**Diagram tady si můžete stáhněte: [Microsoft Azure Machine Learning Studio přehled možností](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Microsoft Azure Machine Learning Studio přehled možností](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Začínáme s nástrojem Machine Learning Studio
Když poprvé vstoupíte do nástroje [Machine Learning Studio](https://studio.azureml.net), zobrazí se stránka **Domů**. Zde si můžete zobrazit dokumentaci, videa a webináře a najdete tu i další přínosné materiály.

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
Kliknutím na **Galerie** přejdete na ke **[galerii Azure AI](https://gallery.azure.ai/)**. Galerie je místo, kde komunita datových vědců a vývojářů sdílí řešení vytvořená pomocí komponent sady Cortana Intelligence Suite.

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

Kompletní postup vytváření řešení prediktivní analýzy, najdete v části [vývoj prediktivního řešení pomocí Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Datové sady
Datová sada obsahuje data, která byla nahrána do nástroje Machine Learning Studio, aby je bylo možné použít v procesu modelování. Součástí nástroje Machine Learning Studio je několik ukázkových datových sad, se kterými můžete experimentovat. Dle potřeby můžete nahrávat další datové sady. Zde jsou některé příklady dodávaných datových sad:

* **Data o spotřebě paliv u různých automobilů** – Hodnoty spotřeby paliva u automobilů na jednotku vzdálenosti (MPG) stanovené podle počtu válců, koňských sil apod.
* **Data o rakovině prsu** – Diagnostická data rakoviny prsu
* **Data o lesních požárech** – Rozsahy lesních požárů v severovýchodním Portugalsku

Během vytváření experimentu je možné si vybírat ze seznamu datových sad, které jsou k dispozici v nabídce nalevo od plátna.

Seznam ukázkových datových sad obsažených v nástroji Machine Learning Studio, najdete v tématu o [využití ukázkových datových sad v nástroji Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Moduly
Modul je algoritmus, který je možné provést na datech. Machine Learning Studio obsahuje několik modulů od funkcí pro příjem dat po procesy trénování, stanovení skóre a ověřování. Zde jsou některé příklady dodávaných modulů:

* [Převod na ARFF][convert-to-arff] – Převede serializovanou datovou sadu .NET do formátu ARFF (Attribute-Relation File Format).
* [Výpočet základních statistik][elementary-statistics] – Vypočítá základní statistiky, jako je střední hodnota, směrodatná odchylka atd.
* [Lineární regrese][linear-regression] – Vytvoří online model lineární regrese na základě klesání gradientu.
* [Určení skóre modelu][score-model] – Stanoví skóre pro trénovaný klasifikační nebo regresní model.

Během vytváření experimentu je možné si vybírat ze seznamu modulů, které jsou k dispozici v nabídce nalevo od plátna.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když na plátnu vyberete modul, parametry modulu se zobrazí v podokně **Vlastnosti** napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat.

Nějaké Nápověda procházení rozsáhlé knihovny dostupných algoritmů strojového učení, naleznete v tématu [jak zvolit algoritmy pro Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Nasazení webové služby prediktivní analýzy
Jakmile je váš model prediktivní analýzy připraven, můžete jej přímo v nástroji Machine Learning Studio nasadit jako webovou službu. Další podrobnosti k tomuto procesu najdete v tématu [Nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md).

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Čím se liší od služby Azure Machine Learning Machine Learning Studio?

[Služba Azure Machine Learning](../service/overview-what-is-azure-ml.md) poskytuje sady SDK a služby, a rychle pro přípravu dat, trénovat a nasazovat modely strojového učení. Zlepšete produktivitu a snižte náklady díky automatickému škálování výpočetních funkcí a kanálů. K těmto funkcím můžete přistupovat z opensourcových architektur Pythonu, jako jsou PyTorch, TensorFlow a scikit-learn. 

Machine Learning Studio použijte, pokud chcete rychle a snadno experimentovat s modely strojového učení a předdefinované algoritmy strojového učení jsou pro vaše řešení dostačující.

Službu Machine Learning použijte, pokud pracujete v prostředí Python, chcete mít větší kontrolu nad algoritmy strojového učení nebo chcete použít Open Source knihovny strojového učení.

> [!NOTE]
> Modelů sestavených v nástroji Azure Machine Learning Studio nelze nasadit nebo spravované službou Azure Machine Learning.

## <a name="free-trial"></a>Bezplatná zkušební verze

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Další postup
Vás seznámí se základy prediktivní analýzy a strojové učení s využitím [podrobné quickstart](create-experiment.md) a [na základě ukázek](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
