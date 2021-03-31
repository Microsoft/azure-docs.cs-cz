---
title: Monitorování toků dat mapování
description: Jak vizuálně monitorovat toky dat mapování v Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/22/2020
ms.openlocfilehash: 9ca5ea5cdebe297af5081ae6e219935c56ba942e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004859"
---
# <a name="monitor-data-flows"></a>Monitorování toků dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po dokončení sestavování a ladění toku dat chcete tok dat naplánovat tak, aby se spouštěl podle plánu v kontextu kanálu. Kanál můžete naplánovat z Azure Data Factory pomocí triggerů. Nebo můžete použít možnost aktivovat nyní z Tvůrce kanálů Azure Data Factory ke spuštění jednoho spuštění za účelem testování toku dat v rámci kontextu kanálu.

Po spuštění kanálu můžete monitorovat kanál a všechny aktivity obsažené v kanálu včetně aktivity toku dat. Klikněte na ikonu monitorování na levém panelu uživatelského rozhraní Azure Data Factory. Zobrazí se obrazovka podobná této. Zvýrazněné ikony vám umožní přejít na aktivity v kanálu, včetně aktivity toku dat.

![Snímek obrazovky s ikonami pro výběr kanálů zobrazí další informace.](media/data-flow/mon001.png "Sledování toku dat")

Na této úrovni vidíte statistiku, včetně časů běhu a stavu. ID běhu na úrovni aktivity se liší od ID běhu na úrovni kanálu. ID spuštění na předchozí úrovni je pro kanál. Výběrem brýlí získáte podrobné informace o spuštění toku dat.

![Snímek obrazovky s ikonou brýlí zobrazí podrobnosti o spuštění toku dat.](media/data-flow/monitoring-details.png "Sledování toku dat")

Když jste v zobrazení monitorování grafického uzlu, můžete zobrazit zjednodušenou verzi grafu toku dat, která je jen pro zobrazení.

![Snímek obrazovky zobrazuje verzi grafu pouze pro zobrazení.](media/data-flow/mon003.png "Sledování toku dat")

Tady je video s přehledem monitorování výkonu datových toků z obrazovky monitorování ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Zobrazit plány spuštění toku dat

Po spuštění toku dat ve Sparku Azure Data Factory určí optimální cesty kódu na základě celého toku dat. V různých uzlech a datových oddílech se škálováním na více instancí můžou být taky cesty spouštění. Proto graf monitorování představuje návrh toku, přičemž vezme v úvahu cestu spuštění vašich transformací. Když vyberete jednotlivé uzly, uvidíte "seskupení", která reprezentují kód, který byl proveden společně na clusteru. Časování a počty, které vidíte, tyto skupiny zastupují na rozdíl od jednotlivých kroků v návrhu.

![Snímek obrazovky se zobrazí na stránce toku dat.](media/data-flow/mon004.png "Sledování toku dat")

* Když v okně sledování vyberete volné místo, v dolním podokně se zobrazí počty časování a řádků pro každou jímku a transformace, které vedly k datům jímky pro transformaci.

* Když vyberete jednotlivé transformace, dostanete další zpětnou vazbu na panel na pravé straně, který zobrazuje statistiky oddílu, počty sloupců, zkosení (jak rovnoměrně jsou data distribuovaná napříč oddíly), a špičatost (jak nárazové data).

* Když vyberete jímku v zobrazení uzlu, uvidíte, že se zobrazí čára. Existují tři různé metody, které jsou v průběhu toku dat shrnuty do jímky. Jsou to tyto:

  * Vypočítáno: použijete sloupec pro podmíněné zpracování nebo v rámci výrazu v toku dat, ale nebudete ho nakládat do jímky.
  * Derived: sloupec je nový sloupec, který jste vygenerovali ve vašem toku, to znamená, že se ve zdroji nenachází.
  * Namapováno: sloupec pochází ze zdroje a vaše mapování je na pole jímky.
  * Stav toku dat: aktuální stav provádění
  * Čas spuštění clusteru: doba, po kterou se získá výpočetní prostředí JIT ve službě JIT pro spuštění toku dat
  * Počet transformací: kolik kroků transformace se provádí v toku
  
![Snímek obrazovky ukazuje možnost aktualizace.](media/data-flow/monitornew.png "Nové monitorování toku dat")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>Celková doba zpracování jímky vs. doba zpracování transformace

Každá fáze transformace obsahuje celkovou dobu, po kterou je tato fáze dokončena, společně s každou dobou spuštění oddílu celkem. Po kliknutí na jímku se zobrazí "doba zpracování jímky". Tentokrát zahrnuje celkový čas transformace *a* i/O čas, který trvalo zápis dat do cílového úložiště. Rozdíl mezi časem zpracování jímky a celkem transformace je vstupně-výstupní čas pro zápis dat.

Pokud v zobrazení monitorování kanálu ADF otevřete výstup JSON z aktivity toku dat, můžete také zobrazit podrobné časování každého kroku transformace oddílu. JSON obsahuje časování milisekund pro každý oddíl, zatímco zobrazení monitorování uživatelského rozhraní je agregovaným načasováním oddílů přidaných dohromady:

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```

### <a name="post-processing-time"></a>Čas po zpracování

Když na mapě vyberete ikonu transformace jímky, v pravém dolním rohu se v panelu pro odesílání na pravé straně zobrazí další datový bod s názvem "doba zpracování". Toto je množství času stráveného prováděním úlohy v clusteru Spark *po* načtení dat, transformaci a zápis. Tento čas může zahrnovat uzavírání fondů připojení, vypnutí ovladače, odstraňování souborů, slučování souborů atd. Při provádění akcí ve vašem toku, jako je "přesunout soubory" a "výstup do jednoho souboru", se pravděpodobně zobrazí zvýšení hodnoty doba zpracování po zpracování.
  
## <a name="error-rows"></a>Řádky chyb

Ve výstupu monitorování se projeví povolení zpracování řádků chyb v jímky toku dat. Při nastavování jímky na hodnotu "zpráva o úspěchu při chybě" bude výstup monitorování zobrazovat počet úspěšných a neúspěšných řádků po kliknutí na uzel monitorování jímky.

![Snímek obrazovky zobrazuje řádky chyb.](media/data-flow/error-row-2.png "Úspěch monitorování řádku chyby")

Když vyberete možnost "ohlásit selhání při chybě", bude se stejný výstup zobrazovat pouze v textu monitorování aktivity výstup. Důvodem je skutečnost, že aktivita toku dat vrátí selhání pro provedení a podrobné zobrazení monitorování nebude k dispozici.

![Snímek obrazovky zobrazuje chybové řádky v aktivitě.](media/data-flow/error-rows-4.png "Chyba sledování řádku chyby")

## <a name="monitor-icons"></a>Monitorovat ikony

Tato ikona znamená, že data transformace již byla v clusteru uložena do mezipaměti, takže časování a cesta provádění poberou v úvahu:

![Snímek obrazovky se zobrazí ikona disku.](media/data-flow/mon005.png "Sledování toku dat")

V transformaci se také zobrazují ikony zelených kroužků. Představují Počet umyvadel, do kterých data přecházejí.
