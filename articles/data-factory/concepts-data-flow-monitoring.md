---
title: Azure Data Factory mapování způsobem se předávají Data vizuální monitorování
description: Vizuální monitorování služby Azure Data Factory Data proudí
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 1497e6b85d3f577064b7a90fb1bcf5cbeb4a1f40
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213285"
---
# <a name="monitor-data-flows"></a>Toky dat monitorování

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Po dokončení sestavování a ladění váš tok dat můžete naplánovat toku dat a spustit podle plánu v rámci kanálu. Můžete naplánovat kanál ze služby Azure Data Factory pomocí aktivačních procedur. Nebo můžete použít možnost aktivovat z Tvůrce kanálu Azure Data Factory k provedení jednoho běžet na testování toku dat v rámci kanálu.

Po spuštění kanálu, budou moct monitorování kanálu a všechny aktivity obsažené v kanálu aktivitu toku dat včetně. Klikněte na ikonu na levém panelu Uživatelské rozhraní služby Azure Data Factory. Zobrazí se obrazovka podobná následující. Zvýrazněný ikony vám umožní přejít k podrobnostem aktivity v kanálu, včetně toku dat aktivit.

<img src="media/data-flow/mon001.png" width="800">

Zobrazí se v této stats úrovni jako dobře inculding časy spuštění a stav. ID spuštění na úrovni aktivity se liší, že ID spuštění na úrovni kanálu. ID spuštění na předchozí úrovni je pro příslušný kanál. Kliknutím brýlí vám poskytne podrobné podrobnosti na spuštění toku vaše data.

<img src="media/data-flow/mon002.png" width="800">

Pokud jste v uzlu grafické zobrazení monitorování, zobrazí se zjednodušenou verzi jen pro čtení dat grafu toku.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>Plánům spuštění toku dat zobrazení

Při spuštění toku dat ve službě Databricks, Azure Data Factory určuje optimální kód cesty založené na entirity datový tok. Kromě toho cesty spuštění může dojít v různých uzlech horizontální navýšení kapacity a datové oddíly. Proto monitorování grafu představuje návrhu vašeho toku, zohledněním postupu provádění transformace. Po kliknutí na jednotlivé uzly se zobrazí seskupení"", které představují kód, který se spustil společně v clusteru. Časování a počty, které se zobrazí představují jednotlivé kroky v návrhu na rozdíl od těchto skupin.

<img src="media/data-flow/mon004.png" width="800"> 

* Po kliknutí na volného prostoru v okně monitorování statistik v dolním podokně se zobrazí časování a počty řádků pro každý jímky a transformace, které vedly k jímky dat pro transformaci rodokmenu.

* Když vyberete jednotlivé transformace, zobrazí se další zpětnou vazbu na pravém panelu, který zobrazuje oddílu, statistiky, počty sloupců, zešikmení (jak se data rovnoměrně napříč oddíly), a míra fluktuace (jak spikey jsou data).

* Když kliknete na jímce ve zobrazení uzlů, zobrazí se sloupec rodokmenu. Existují tři různé metody, že sloupce počítají v průběhu toku dat objevil v jímce. Jsou to tyto:

  * Vypočítat: Použít sloupec pro podmíněného zpracování, nebo ve výrazu v toku dat, ale není dostat se do jímky
  * Odvozené: Sloupec je nový sloupec, který jste vygenerovali v toku, například nebylo k dispozici ve zdroji
  * Mapovat: Sloupec pochází ze zdroje a se mapování na pole jímky
  
## <a name="monitor-icons"></a>Monitory

Tato ikona znamená, že transformace dat byl již ukládány do mezipaměti v clusteru, tedy časování a cesta k provádění trvalo, který v úvahu:

<img src="media/data-flow/mon005.png" width="800"> 

Zobrazí se také zeleném kroužku ikony v transformace. Představují počet jímky, které se data přenášejí do.
