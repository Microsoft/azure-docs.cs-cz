---
title: Jak model strojového učení stane webové služby
titleSuffix: Azure Machine Learning Studio
description: Přehled o mechanismu jak experimentovat vaše postupuje model Azure Machine Learning z vývojového zprovozněné webové službě.
services: machine-learning
ms.service: machine-learning
ms.component: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: d084917f247aa20f59aea5179efa045838d7a2af
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076889"
---
# <a name="how-a-machine-learning-studio-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Jak model Machine Learning Studio postupuje od experimentu ke zprovozněné službě webové
Azure Machine Learning Studio poskytuje interaktivní plátno, která umožňuje vyvíjet, spouštět, testovat a iterovat ***experimentovat*** představující model prediktivní analýzy. Existují nejrůznější moduly, které můžete:

* Vstupní data do svého experimentu
* Práce s daty
* Trénování modelu pomocí algoritmů strojového učení
* Ohodnocení modelu
* Výsledky vyhodnocení
* Konečné výstupní hodnoty

Jakmile budete spokojeni s experimentu, ji můžete nasadit jako ***Classic Azure Machine Learning Web service*** nebo ***nové Azure Machine Learning Web service*** tak, aby uživatelé mohli zasílat nová data a přijímat zpět výsledky.

V tomto článku jsme poskytují přehled o mechanismu jak experimentovat vaše postupuje model Machine Learning z vývojového zprovozněné webové službě.

> [!NOTE]
> Existují jiné způsoby, jak vyvíjet a nasazovat modely strojového učení, ale tento článek se zaměřuje na tom, jak používat Machine Learning Studio. Například popis toho, jak vytvořit prediktivní webové služby classic s jazykem R najdete v blogovém příspěvku [sestavení a nasazení prediktivní webové aplikace pomocí nástroje RStudio a Azure Machine Learning studio](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
>
>

Zatímco Azure Machine Learning Studio je usnadňuje vývoj a nasazení *model prediktivní analýzy*, je možné pomocí vyvinout experiment, který neobsahuje model prediktivní analýzy. Experiment může například pouze vstupní data, pracovat s ní a pak vypíše výsledky. Stejně jako experiment prediktivní analýzy můžete nasadit tento – prediktivní experiment jako webové služby, ale je jednodušší proces, protože experiment není školení nebo vyhodnocení modelu strojového učení. I když není typické použití nástroje Studio tímto způsobem, jsme budete jej zahrnout do diskuse tak, že můžeme poskytnout úplné vysvětlení fungování Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Vývoj a nasazování prediktivní webové služby
Tady jsou v jednotlivých fázích, které následuje Typická řešení a nasaďte ji pomocí Machine Learning Studio:

![Tok nasazení](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Obrázek 1 – fáze model typické prediktivní analýzy*

### <a name="the-training-experiment"></a>Výukového experimentu
***Výukového experimentu*** je počáteční fáze vývoje webovou službu v nástroji Machine Learning Studio. Účelem výukového experimentu je vám místo pro vývoj, testování, iterovat a nakonec trénování model strojového učení. Můžete dokonce tréninku více modelů současně lze najít nejlepší řešení, ale po dokončení můžete experimentovat vyberete jeden školení model a eliminovat rest z experimentu. Příklad vývoje experiment prediktivní analýzy, najdete v části [vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning](walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>Prediktivní experiment
Jakmile budete mít trénovaný model v experimentu školení, klikněte na tlačítko **nastavení webové služby** a vyberte **prediktivní webová služba** v nástroji Machine Learning Studio zahájit proces převodu trénování Experimentováním ***prediktivní experiment***. Účelem prediktivní experiment je trénovaný model použít ke stanovení skóre pro nová data s cílem nakonec stávají zprovozněný jako Azure webové služby.

Tento převod se automaticky provede následující kroky:

* Převést sadu moduly používané k trénování do jednoho modulu a uložte ho jako trénovaného modelu
* Odstranit všechny nadbytečné moduly, které nesouvisí s bodování
* Přidat vstupní a výstupní porty, které budou používat konečné webové služby

Můžou existovat další změny, které chcete připravit prediktivní experiment nasadit jako webovou službu. Například pokud chcete webovou službu do výstupního pouze podmnožinu výsledků, můžete přidat před na výstupní port modulu filtrování.

V tomto procesu převodu se zahodí výukového experimentu. Po dokončení procesu se v sadě Studio máte dvě karty: jeden pro výukového experimentu a jeden pro prediktivní experiment. Tímto způsobem můžete provádět změny výukového experimentu předtím, než nasazení webové služby a znovu sestavte prediktivní experiment. Nebo můžete uložit kopii tohoto výukového experimentu spustit další řádek experimentování.

> [!NOTE]
> Po kliknutí na **prediktivní webová služba** spuštění automatického procesu pro převod výukového experimentu na prediktivní experiment, a to funguje dobře ve většině případů. Pokud je komplexní výukového experimentu (například máte více cest k trénování, která se do programu dohromady), budete pravděpodobně chtít provést tento převod ručně. Další informace najdete v tématu [přípravu modelu pro nasazení v Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).
>
>

### <a name="the-web-service"></a>Webová služba
Jakmile budete spokojeni, prediktivní experiment je připraven, můžete nasadit službu jako buď klasickou webovou službou nebo nové webové služby založené na Azure Resource Manageru. Pro zprovoznění modelu a nasadit ho jako *klasická Machine Learning webová služba*, klikněte na tlačítko **nasadit webovou službu** a vyberte **nasazení webové služby [Classic]**. Nasadit jako *nové Machine Learning webová služba*, klikněte na tlačítko **nasadit webovou službu** a vyberte **nasazení [nové] webová služba**. Uživatelé teď můžete odesílat data do modelu pomocí rozhraní REST API služby Web a získat zpět výsledky. Další informace najdete v tématu o [využívání webové služby Azure Machine Learning](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Typické případy: vytváření – prediktivní webové služby
Pokud není trénování experimentu model prediktivní analýzy, pak nemusíte vytvářet výukového experimentu a bodovací experiment – je právě jednoho experimentu a ji můžete nasadit jako webovou službu. Machine Learning Studio zjistí, zda obsahuje experiment prediktivní model díky analýze modulů, které jste použili.

Poté, co jste provést iteraci experimentu a spokojeni s ní:

1. Klikněte na tlačítko **nastavení webové služby** a vyberte **Přetrénování webová služba** – vstup a výstup uzly jsou přidány automaticky
2. Klikněte na tlačítko **spuštění**
3. Klikněte na tlačítko **nasadit webovou službu** a vyberte **nasazení webové služby [Classic]** nebo **nasazení [nové] webová služba** v závislosti na prostředí, do které chcete nasadit.

Webová služba je momentálně nasazené, a můžete používat a spravovat stejně jako prediktivní webové služby.

## <a name="updating-your-web-service"></a>Aktualizuje se vaše webová služba
Teď, když jste nasadit experiment jako webové služby, co dělat, pokud je třeba ji aktualizovat?

To záleží na co je potřeba aktualizovat:

**Požadujete změnu vstup nebo výstup, nebo chcete změnit, jak webová služba zpracovává data**

Pokud nejsou Změna modelu, ale jsou stačí, když změníte způsob, jakým zpracovává data webové služby, můžete upravit prediktivní experiment a pak klikněte na tlačítko **nasadit webovou službu** a vyberte **nasazení webové služby [Classic]** nebo **nasazení [nové] webová služba** znovu. Webová služba je zastavena, se nasadí aktualizované prediktivní experiment a restartovat webovou službu.

Zde naleznete příklad: Předpokládejme, že prediktivní experiment vrátí celý řádek vstupní data s predikované výsledky. Můžete rozhodnout, který má webová služba právě vrátit výsledek. Proto můžete přidat **sloupce projektu** modulu v prediktivní experiment, bezprostředně před na výstupní port pro vyloučení sloupce mimo výsledek. Po kliknutí na **nasadit webovou službu** a vyberte **nasazení webové služby [Classic]** nebo **nasazení [nové] webová služba** znovu, webová služba se aktualizuje.

**Chcete programovém přeučení modelů s novými daty**

Pokud chcete zachovat váš model strojového učení, ale chcete přeučování s novými daty, máte dvě možnosti:

1. **Přeučování modelu je spuštěna webová služba** – Pokud chcete přeučování váš model prediktivní webová služba je spuštěna, můžete to provést tak, že několik úprav výukového experimentu, aby byl ***přetrénování Experimentujte***, pak můžete nasadit jako  ***retraining webové* služby**. Pokyny, jak to udělat, najdete v části [modelů Machine Learning Přeučování](retrain-models-programmatically.md).
2. **Přejděte zpět do původní výukového experimentu a použití různých trénovacích dat k vývoji modelu** – prediktivní experiment je propojena k webové službě, ale výukového experimentu není propojena přímo tímto způsobem. Pokud upravíte původní výukového experimentu a klikněte na tlačítko **nastavení webové služby**, se vytvoří *nové* prediktivní experiment, který se vytvoří při nasazení, *nové* Web Služba. Právě neaktualizuje původní webové služby.

   Pokud je potřeba upravit výukového experimentu, otevřete ho a klikněte na tlačítko **uložit jako** k vytvoření kopie. To nechte beze změny původní výukový experiment prediktivní experiment a webové služby. Nyní můžete vytvořit novou webovou službu se změnami. Po nasazení nové webové služby, potom se můžete rozhodnout, jestli se má zastavit předchozí webovou službu nebo je Uchovávejte společně s novým systémem.

**Chcete pro trénování modelu různé**

Pokud chcete změnit váš původním prediktivní experiment, jako je například výběr jiné algoritmu strojového učení, zkusit školení různé metody, atd., pak budete muset použijte druhý postup popsaný výše pro přetrénování modelu: otevřít školení experiment, klikněte na tlačítko **uložit jako** vytvořit kopii, a poté spusťte nové rozhodli vývoj modelu, vytváření prediktivní experiment a nasazení webové služby. Tím se vytvoří nové webové služby nesouvisející k původnímu – můžete rozhodnout, které z nich nebo obě běžela.

## <a name="next-steps"></a>Další kroky
Podrobné informace o procesu vývoje a experimentu najdete v následujících článcích:

* Převod experiment - [přípravu modelu pro nasazení v Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md)
* nasazení webové služby - [nasazení webové služby Azure Machine Learning](publish-a-machine-learning-web-service.md)
* přetrénování modelu - [Machine Learning Přeučování modelů prostřednictvím kódu programu](retrain-models-programmatically.md)

Příklady celého procesu naleznete v tématu:

* [Kurz strojového učení: Vytvoření prvního experimentu v nástroji Azure Machine Learning Studio](create-experiment.md)
* [Návod: Vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

