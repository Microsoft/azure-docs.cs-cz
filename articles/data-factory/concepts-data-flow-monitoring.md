---
title: Mapování vizuálního monitorování toku dat
description: Jak vizuálně sledovat toky dat Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: dea0f9a038958ea747147a179020545f2f6922a2
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605348"
---
# <a name="monitor-data-flows"></a>Sledování toků dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Po dokončení vytváření a ladění toku dat, budete chtít naplánovat tok dat spustit podle plánu v rámci kanálu. Kanál můžete naplánovat z Azure Data Factory pomocí aktivačních událostí. Nebo můžete použít možnost Trigger Now z Azure Data Factory Pipeline Builder k provedení spuštění jednoho spuštění k testování toku dat v kontextu kanálu.

Při spuštění kanálu budete moci sledovat kanál a všechny aktivity obsažené v kanálu, včetně aktivity toku dat. Klikněte na ikonu monitoru v levém panelu Azure Data Factory. Uvidíte obrazovku podobnou té níže. Zvýrazněné ikony vám umožní přejít k podrobnostem o aktivitách v kanálu, včetně aktivity toku dat.

![Monitorování toku dat](media/data-flow/mon001.png "Sledování toku dat")

Zobrazí se také statistiky na této úrovni, včetně časů spuštění a stavu. Id spuštění na úrovni aktivity se liší od ID spuštění na úrovni kanálu. ID spuštění na předchozí úrovni je pro kanál. Kliknutím na brýle získáte podrobné informace o provádění toku dat.

![Monitorování toku dat](media/data-flow/mon002.png "Sledování toku dat")

Pokud se zobrazí zobrazení monitorování grafického uzlu, zobrazí se zjednodušená verze grafu toku dat pouze zjednodušená.

![Monitorování toku dat](media/data-flow/mon003.png "Sledování toku dat")

## <a name="view-data-flow-execution-plans"></a>Zobrazit plány provádění toku dat

Když se váš tok dat spustí ve Sparku, Azure Data Factory určuje optimální cesty kódu na základě celého toku dat. Kromě toho může dojít ke spuštění cesty na různých uzlech horizontálního navýšení kapacity a datové oddíly. Proto graf monitorování představuje návrh toku, s přihlédnutím k cestě provádění transformace. Po kliknutí na jednotlivé uzly se zobrazí "seskupení", které představují kód, který byl proveden společně v clusteru. Časování a počty, které vidíte představují tyto skupiny na rozdíl od jednotlivých kroků v návrhu.

![Monitorování toku dat](media/data-flow/mon004.png "Sledování toku dat")

* Když kliknete na otevřené místo v okně monitorování, statistiky v dolním podokně zobrazí časování a počty řádků pro každý jímky a transformace, které vedly k datům jímky pro linie transformace.

* Když vyberete jednotlivé transformace, obdržíte další zpětnou vazbu na pravém panelu, který zobrazuje statistiky oddílů, počty sloupců, šikmost (jak rovnoměrně jsou data rozložená mezi oddíly) a špičatou (jak špičaté jsou data).

* Když kliknete na dřez v zobrazení uzlu, uvidíte řádek sloupce. Existují tři různé metody, které sloupce jsou akumulovány v celém toku dat přistát v jímce. Jsou to tyto:

  * Vypočítané: Sloupec se používá pro podmíněné zpracování nebo v rámci výrazu v toku dat, ale nepřistanete v jímce
  * Odvozeno: Sloupec je nový sloupec, který jste vygenerovali ve vašem toku, tj.
  * Mapováno: Sloupec pochází ze zdroje a vaše jsou mapování na dřez pole
  * Stav toku dat: Aktuální stav provádění
  * Doba spuštění clusteru: Doba získání výpočetního prostředí JIT Spark pro spuštění toku dat
  * Počet transformací: Kolik transformačních kroků se provádí ve vašem toku
  
![Monitorování toku dat](media/data-flow/monitornew.png "Monitorování toku dat Nové")  
  
## <a name="monitor-icons"></a>Ikony monitoru

Tato ikona znamená, že data transformace byla již uložena v mezipaměti v clusteru, takže časování a cesta spuštění vzali v úvahu:

![Monitorování toku dat](media/data-flow/mon004.png "Sledování toku dat")

V transformaci uvidíte také ikony zeleného kruhu. Představují počet počtu jímky, které data proudí do.
