---
title: Iterativní vývoj a ladění v Azure Data Factory
description: Zjistěte, jak iterativně vyvíjet a ladit kanály Data Factory na webu Azure Portal.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 49e406f0730189439bb1c2529b23d8ad6d760a2f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411514"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterativní vývoj a ladění pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory umožňuje iterativně vyvíjet a ladit kanály Data Factory.

Osmiminutový úvod a ukázku této funkce najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Iterativní ladicí funkce
Vytvořte kanály a proveďte testovací běhy pomocí funkce **ladění** na plátně kanálu bez psaní jediného řádku kódu.

![Možnost ladění na plátně kanálu](media/iterative-development-debugging/iterative-development-image1.png)

Výsledky testovacích běhů můžete zobrazit v okně **Výstup** na plátně kanálu.

![Výstupní okno plátna potrubí](media/iterative-development-debugging/iterative-development-image2.png)

Po úspěšném spuštění testu přidejte další aktivity do kanálu a pokračujte v ladění iterativním způsobem. Můžete také **zrušit** zkušební běh, zatímco probíhá.

![Zrušení zkušebního běhu](media/iterative-development-debugging/iterative-development-image3.png)

Při provádění testů není nutné publikovat změny v datové továrně, než vyberete **možnost Ladění**. Tato funkce je užitečná ve scénářích, kde se chcete ujistit, že změny fungují podle očekávání před aktualizací pracovního postupu factory dat.

> [!IMPORTANT]
> **Výběr** ladění skutečně spustí kanál. Takže například pokud kanál obsahuje aktivitu kopírování, spuštění testu zkopíruje data ze zdroje do cíle. V důsledku toho doporučujeme použít testovací složky v aktivitách kopírování a další aktivity při ladění. Po odladění kanálu přepněte na skutečné složky, které chcete použít v normálních operacích.

## <a name="visualizing-debug-runs"></a>Vizualizace spuštění ladění

Můžete vizualizovat všechny ladění spustí, které probíhají pro vaši data factory na jednom místě. V pravém horním rohu stránky vyberte **Zobrazit ladicí běhy.** Tato funkce je užitečná v situacích, kdy hlavní kanály zahajují spuštění ladění pro podřízené kanály a chcete, aby jedno zobrazení vidělo všechny spuštěné aktivní ladění.

![Vyberte ikonu Zobrazit aktivní ladění spustí](media/iterative-development-debugging/view-debug-runs-image1.png)

![Ukázkový seznam spuštěných aktivních ladění](media/iterative-development-debugging/view-debug-runs-image2.png)

Pokud máte aktivní relace ladění toku dat, tyto relace se zobrazí v dolní části aktivního okna ladění. Můžete vybrat aktivní relaci toku dat a zastavit příslušný cluster.

![Ukázkový seznam spuštění ladění aktivního toku dat](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Sledování ladění běží

Spuštění testů zahájených pomocí funkce **ladění** nejsou k dispozici v seznamu na kartě **Monitor.** Spuštění spuštěná lze zobrazit pouze pomocí aktivačních událostí **Trigger Now**, **Schedule**nebo **Tumbling Window** na kartě **Monitor.** Poslední spuštění testu, které bylo zahájeno s možností **ladění,** se zobrazí v okně **Výstup** na plátně kanálu.

## <a name="setting-breakpoints-for-debugging"></a>Nastavení zarážek pro ladění

Data Factory také umožňuje ladit, dokud nedosáhnete určité aktivity na plátně kanálu. Stačí umístit zarážku na aktivitu, do které chcete testovat, a vyberte **ladění**. Data Factory zajišťuje, že test běží pouze do aktivity zarážky na plátně kanálu. Tato funkce *Ladění Do* je užitečná, pokud nechcete otestovat celý kanál, ale pouze podmnožinu aktivit uvnitř kanálu.

![Zarážky na plátně potrubí](media/iterative-development-debugging/iterative-development-image4.png)

Chcete-li nastavit zarážku, vyberte prvek na plátně kanálu. Možnost *Ladění Dokud* se zobrazí jako prázdný červený kruh v pravém horním rohu prvku.

![Před nastavením zarážky u vybraného prvku](media/iterative-development-debugging/iterative-development-image5.png)

Po výběru *možnosti Ladění do* se změní na vyplněný červený kruh, což znamená, že je povolena zarážky.

![Po nastavení zarážky na vybraném prvku](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Další kroky
[Průběžná integrace a nasazení v Azure Data Factory](continuous-integration-deployment.md)
