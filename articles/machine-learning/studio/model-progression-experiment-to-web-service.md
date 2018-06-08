---
title: Jak model Azure Machine Learning stane webové služby | Microsoft Docs
description: Přehled mechanismů jak vaše postupuje model Azure Machine Learning z vývojovém experimentovat operationalized webové služby.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 25e0c025-f8b0-44ab-beaf-d0f2d485eb91
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 7b9aec2815f836b3b220de37fe6428c54d39c3e5
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835568"
---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Jak model Machine Learning hodnoty z experimentu webové služby operationalized
Azure Machine Learning Studio poskytuje interaktivní plátno, která umožňuje vyvíjet, spuštění, testovat a iterovat ***experimentovat*** představující model prediktivní analýzy. Existují širokou škálu moduly, které můžou:

* Vstupní data do experimentu
* Manipulovat s daty
* Cvičení modelu pomocí algoritmy strojového učení
* Ohodnocení modelu
* Vyhodnoťte výsledky
* Hodnoty finální výstup

Jakmile budete spokojeni s experimentu, můžete nasadit jako ***Classic Azure Machine Learning webové služby*** nebo ***nové Azure Machine Learning webové služby*** , aby uživatelé mohli odeslat nová data a zobrazí zpět výsledky.

V tomto článku jsme poskytnutí přehledu o mechanismů jak vaše postupuje model Machine Learning z vývojovém experimentovat operationalized webové služby.

> [!NOTE]
> Existují jiné způsoby k vývoji a nasazení modelů machine learning, ale tento článek se zaměřuje na tom, jak používáte Machine Learning Studio. Například popis postupu vytvoření klasického prediktivní webové služby s R najdete v tématu v příspěvku blogu [sestavení & nasadit prediktivní webové aplikace pomocí Rstudia a Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
> 
> 

Zatímco Azure Machine Learning Studio slouží k vývoji a zavedení *model prediktivní analýzy*, je možné pomocí vyvinout experiment, která nezahrnuje model prediktivní analýzy. Experiment může například právě vstupní data, používat a pak vypsání výsledků. Stejně jako experiment prediktivní analýzy můžete nasadit tento není prediktivní experiment jako webovou službu, ale je jednodušší proces, protože není experimentu cvičení nebo vyhodnocování model machine learning. I když není typické použití Studio tímto způsobem, budete je zahrnuta v diskusi tak, aby bylo možné přidělit podrobné informace o tom, jak funguje Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Vývoj a nasazení prediktivní webové služby
Zde jsou fází, které následuje typický řešení při vývoji a nasadit pomocí Machine Learning Studio:

![Tok nasazení](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Obrázek 1 – fáze model typické prediktivní analýzy*

### <a name="the-training-experiment"></a>Výukový experiment
***Výukový experiment*** je počáteční fáze vývoje webové služby v nástroji Machine Learning Studio. Účelem výukový experiment je tak, abyste získali místo pro vývoj, testování, iterace a nakonec cvičení strojového učení modelu. Vám může i cvičení více modelů současně jako vyhledejte nejlepším řešením, avšak po dokončení můžete experimentovat vyberte jeden Trénink modelu a eliminovat rest z experimentu. Příklad vývoj experiment prediktivní analýzy, naleznete v části [vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning](walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>Prediktivní experiment
Jakmile máte modulu trained model v experimentu školení, klikněte na tlačítko **nastavit webové služby** a vyberte **webové služby prediktivní** v nástroji Machine Learning Studio zahájíte proces převodu vaší školení experiment k ***prediktivní experiment***. Účelem prediktivní experiment je skóre pro nová data, s cílem nakonec stane operationalized jako Azure webové služby pomocí trained model.

Tento převod je možné provést pro vás provede následujícími kroky:

* Převést sadu moduly používané pro školení do jediného modulu a uložte ho jako modulu trained model.
* Odstranit všechny nadbytečné moduly, které nesouvisí s vyhodnocování
* Přidat vstupní a výstupní porty, které bude používat případné webová služba

Mohou existovat další změny, které chcete připravit prediktivní experiment nasadit jako webovou službu. Například pokud chcete webovou službu na výstup jenom podmnožinu výsledků, můžete přidat filtrování modul před na výstupní port.

V tomto procesu převodu není výukový experiment zahodí. Po dokončení procesu v sadě Studio máte dvě karty: jeden pro výukový experiment a jeden pro prediktivní experiment. Tímto způsobem můžete provádět změny výukový experiment před nasazením webové služby a znovu sestavte prediktivní experiment. Nebo můžete uložit kopii výukový experiment spustit další řádek experimenty.

> [!NOTE]
> Když kliknete na tlačítko **webové služby prediktivní** spustíte pomocí automatického procesu převést výukový experiment prediktivní experiment, a to funguje dobře ve většině případů. Pokud je komplexní výukový experiment (například máte více cest k trénování, které se můžete připojit společně), budete pravděpodobně chtít udělat tento převod ručně. Další informace najdete v tématu [postup přípravy modelu pro nasazení v nástroji Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).
> 
> 

### <a name="the-web-service"></a>Webové služby
Jakmile budete spokojeni, prediktivní experiment je připraven, můžete nasadit služby jako buď Classic webovou službu nebo nové webové služby založené na Azure Resource Manager. Pro zprovoznění modelu po nasazení jako *Classic Machine Learning webové služby*, klikněte na tlačítko **nasazení webové služby** a vyberte **nasazení webové služby [Classic]**. K nasazení jako *nové Machine Learning webové služby*, klikněte na tlačítko **nasazení webové služby** a vyberte **nasazení [nové] webové služby**. Uživatelé teď můžete posílat data do modelu pomocí webové služby REST API a výsledky zobrazí zpět. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Obvyklý případ: vytváření-prediktivní webové služby
Pokud není cvičení experimentu model prediktivní analýzy a pak není nutné vytvořit výukový experiment a vyhodnocování experimentu – je jedním experiment a můžete nasadit jako webovou službu. Machine Learning Studio zjistí, zda experimentu obsahuje model prediktivní analýzou moduly, které jste použili.

Poté, co jste vstupní v experimentu a spokojeni s ním:

1. Klikněte na tlačítko **nastavit webové služby** a vyberte **Retraining webové služby** – vstup a výstup uzly se přidávají automaticky
2. Klikněte na tlačítko **spustit**
3. Klikněte na tlačítko **nasazení webové služby** a vyberte **nasazení webové služby [Classic]** nebo **nasazení [nové] webové služby** v závislosti na prostředí, do které chcete nasadit.

Webová služba je nyní nasadit, a můžete používat a spravovat stejně jako prediktivní webové služby.

## <a name="updating-your-web-service"></a>Aktualizace webové služby
Teď, když máte ukázku nasazenou experimentu jako webovou službu, jak postupovat, pokud je třeba ji aktualizovat?

To závisí na tom co je potřeba aktualizovat:

**Chcete změnit vstup nebo výstup, nebo chcete upravit, jak webová služba zpracovává data**

Pokud nejsou Změna modelu, ale pouze upravit jak webová služba zpracovává data, můžete upravit prediktivní experiment a pak klikněte na tlačítko **nasazení webové služby** a vyberte **nasazení webové služby [Classic]** nebo **nasazení [nové] webové služby** znovu. Webová služba je zastavena, je nasazený aktualizované prediktivní experiment a restartování webové služby.

Tady je příklad: Předpokládejme, že prediktivní experiment vrátí celý řádek vstupních dat s předpokládaných výsledek. Můžete rozhodnout, že chcete pouze vrátí výsledek webovou službou. Proto můžete přidat **sloupce projektu** modulu v prediktivní experiment bezprostředně před na výstupní port pro vyloučení sloupce mimo výsledek. Když kliknete na tlačítko **nasazení webové služby** a vyberte **nasazení webové služby [Classic]** nebo **nasazení [nové] webové služby** znovu aktualizovat webovou službu.

**Chcete přeučování model s nová data**

Pokud chcete zachovat vaše strojového učení modelu, ale chcete přeučování s nová data, máte dvě možnosti:

1. **Přeučování modelu, když běží webová služba** – Pokud chcete přeučování modelu, když běží prediktivní webové služby, můžete k tomu tak, že několik úpravy výukový experiment, aby bylo ***retraining experiment***, pak je můžete nasadit jako  ***retraining webové* služby**. Pokyny o tom, jak to udělat najdete v tématu [Machine Learning Přeučování modelů prostřednictvím kódu programu](retrain-models-programmatically.md).
2. **Přejděte zpět na původní výukový experiment a použití různých Cvičná data k vývoji modelu** – prediktivní experiment je propojena k webové službě, ale výukový experiment nejsou přímo spojeny tímto způsobem. Upravíte-li původní výukový experiment a klikněte na tlačítko **nastavit webové služby**, vytvoří *nové* prediktivní experiment, který se vytvoří při nasazení, *nové* webové Služba. Právě neaktualizuje původní webové služby.
   
   Pokud budete muset upravit výukový experiment, otevřete ho a klikněte na tlačítko **uložit jako** k vytvoření kopie. Tato akce ponechat beze změn původní výukový experiment prediktivní experiment a webové služby. Nyní můžete vytvořit novou webovou službu s vašimi změnami. Jakmile nasadíte novou webovou službu, můžete se rozhodnout, zda pro webovou službu předchozí zastavení nebo ponechat běh spolu s novým.

**Chcete-li ke cvičení jiného modelu**

Pokud chcete provést změny vašeho původní prediktivní experiment, jako je výběr jiný algoritmus strojového učení, při různých školení metoda, atd., pak je nutné postupovat druhý postup popisuje výše pro retraining modelu: Otevřete cvičení experiment, klikněte na tlačítko **uložit jako** vytvořit kopii, a poté spustit dolů novou cestu vývoji modelu prediktivní experiment vytváření a nasazení webové služby. Tím se vytvoří nové webové služby, které nejsou k původnímu – můžete rozhodnout, která nebo obojí, a dál běžet.

## <a name="next-steps"></a>Další kroky
Další informace o procesu vývoje a experiment naleznete v následujících článcích:

* převádění experimentu - [postup přípravy modelu pro nasazení v nástroji Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md)
* nasazení webové služby - [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md)
* přeučení modelů - [Machine Learning Přeučování modelů prostřednictvím kódu programu](retrain-models-programmatically.md)

Příklady celého procesu naleznete v tématu:

* [Kurz strojového učení: vytvoření prvního experimentu v nástroji Azure Machine Learning Studio](create-experiment.md)
* [Návod: Vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

