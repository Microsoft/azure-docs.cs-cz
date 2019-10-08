---
title: Azure Data Factory mapování vizuálního monitorování toku dat
description: Jak vizuálně monitorovat toky Azure Data Factory dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 41b4b7cd911bad40055fcf527c186f8de9466cdc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030239"
---
# <a name="monitor-data-flows"></a>Monitorování toků dat



Po dokončení sestavování a ladění toku dat budete chtít tok dat naplánovat tak, aby se spouštěl podle plánu v kontextu kanálu. Kanál můžete naplánovat z Azure Data Factory pomocí triggerů. Nebo můžete použít možnost aktivovat nyní z Tvůrce kanálů Azure Data Factory ke spuštění jednoho spuštění za účelem testování toku dat v rámci kontextu kanálu.

Po spuštění kanálu budete moct monitorovat kanál a všechny aktivity obsažené v kanálu, včetně aktivity toku dat. Klikněte na ikonu monitorování na levém panelu uživatelského rozhraní Azure Data Factory. Zobrazí se obrazovka podobná té. Zvýrazněné ikony vám umožní přejít na aktivity v kanálu, včetně aktivity toku dat.

(media/data-flow/mon001.png "Monitorování toku dat") ![monitorování toku dat]

Na této úrovni uvidíte statistiky, včetně časů běhu a stavu. ID spuštění na úrovni aktivity se liší od ID běhu na úrovni kanálu. ID spuštění na předchozí úrovni je pro kanál. Kliknutím na brýlí získáte podrobné informace o spuštění toku dat.

(media/data-flow/mon002.png "Monitorování toku dat") ![monitorování toku dat]

Když jste v zobrazení monitorování grafického uzlu, zobrazí se zjednodušená verze grafu toku dat, která je jen pro zobrazení.

(media/data-flow/mon003.png "Monitorování toku dat") ![monitorování toku dat]

## <a name="view-data-flow-execution-plans"></a>Zobrazit plány spuštění toku dat

Po spuštění toku dat ve Sparku Azure Data Factory určí optimální cesty kódu na základě celého toku dat. V různých uzlech a datových oddílech se škálováním na více instancí můžou být taky cesty spouštění. Proto graf monitorování představuje návrh toku, přičemž vezme v úvahu cestu spuštění vašich transformací. Po kliknutí na jednotlivé uzly se zobrazí "seskupení", která představuje kód, který byl spuštěn společně v clusteru. Časování a počty, které vidíte, tyto skupiny zastupují na rozdíl od jednotlivých kroků v návrhu.

(media/data-flow/mon004.png "Monitorování toku dat") ![monitorování toku dat]

* Když kliknete na volné místo v okně monitorování, v dolním podokně se zobrazí počty časování a řádků pro každou jímku a transformace, které vedly k datům jímky pro transformaci.

* Když vyberete jednotlivé transformace, dostanete další zpětnou vazbu na panel na pravé straně, který zobrazuje statistiky oddílu, počty sloupců, zkosení (jak rovnoměrně jsou data distribuovaná napříč oddíly), a špičatost (jak nárazové data).

* Po kliknutí na jímku v zobrazení uzlu se zobrazí řádek sloupce. Existují tři různé metody, které jsou v průběhu toku dat shrnuty do jímky. Jsou to tyto:

  * Vypočítáno: použijete sloupec pro podmíněné zpracování nebo v rámci výrazu v toku dat, ale nebudete ho do jímky nakládat.
  * Derived: sloupec je nový sloupec, který jste vygenerovali ve vašem toku, to znamená, že se ve zdroji nenachází.
  * Namapováno: sloupec pochází ze zdroje a vaše mapování je na pole jímky.
  
## <a name="monitor-icons"></a>Monitorovat ikony

Tato ikona znamená, že data transformace již byla v clusteru uložena do mezipaměti, takže časování a cesta provádění poberou v úvahu:

(media/data-flow/mon004.png "Monitorování toku dat") ![monitorování toku dat]

V transformaci se zobrazí taky ikony zelených kroužků. Představují Počet umyvadel, do kterých data přecházejí.
