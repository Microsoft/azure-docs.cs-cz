---
title: Iterativní vývoj a ladění ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vyvíjet a ladit kanály Data Factory zavádět postupně na webu Azure portal.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.openlocfilehash: c80b67ac5575360a6556befd3fd2a6a5c8e562c0
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015759"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterativní vývoj a ladění pomocí služby Azure Data Factory

Azure Data Factory umožňuje opakovaně vyvíjet a ladit kanály Data Factory.

Úvod osm minut a ukázku této funkce podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Funkce iterativní ladění
Vytváření kanálů a testovacích běhů pomocí **ladění** schopností na plátně kanálu, aniž byste museli napsat jediný řádek kódu.

![Ladění funkce na plátno kanálu](media/iterative-development-debugging/iterative-development-image1.png)

Zobrazení výsledků testu běží v **výstup** okno na plátně kanálu.

![Okno výstup na plátně kanálu](media/iterative-development-debugging/iterative-development-image2.png)

Po úspěšném spuštění testu do kanálu přidat další aktivity a pokračovat v ladění iteračním způsobem. Můžete také **zrušit** testovacího běhu, zatímco bude probíhat.

![Zrušit spuštění testu](media/iterative-development-debugging/iterative-development-image3.png)

Když testovacích běhů, není nutné publikovat provedené změny do služby data factory, před výběrem **ladění**. Tato funkce je užitečná ve scénářích, kde chcete Ujistěte se, že změny fungovat podle očekávání, před aktualizací pracovního postupu data factory.

> [!IMPORTANT]
> Výběr **ladění** ve skutečnosti spouští kanál. Tak například pokud kanál obsahuje aktivitu kopírování, testovací běh kopíruje data ze zdroje do cíle. V důsledku toho doporučujeme použít složky testu v vaše aktivity kopírování a dalšími aktivitami při ladění. Poté, co jste ladit kanálu, přepněte do skutečných složek, které chcete použít v běžném provozu.

## <a name="visualizing-debug-runs"></a>Detekční spuštění ladění

Můžete vizualizovat všechna spuštění ladění, které jsou v průběhu pro svou datovou továrnu na jednom místě. Vyberte **zobrazení ladění spustí** v pravém horním rohu stránky. Tato funkce je užitečná v situacích, kdy máte hlavní kanály rutinního spuštění ladění pro podřízené kanály a má jedno zobrazení pro všechny aktivní ladicí běhy.

![Vyberte ikonu zobrazení aktivní ladicí spuštění](media/iterative-development-debugging/view-debug-runs-image1.png)

![Seznam ukázek aktivní ladicí spuštění](media/iterative-development-debugging/view-debug-runs-image2.png)

## <a name="monitoring-debug-runs"></a>Spustí monitorování ladění

Zahájilo se spuštění testu **ladění** funkce nejsou k dispozici v seznamu **monitorování** kartu. Je možné jenom viz spustí aktivovaných pomocí **aktivovat**, **plán**, nebo **Přeskakujícího okna** aktivuje v **monitorování** kartu. Zobrazí poslední testovací běh spuštěné s **ladění** možnosti **výstup** okno na plátně kanálu.

## <a name="setting-breakpoints-for-debugging"></a>Nastavení zarážky pro ladění

Data Factory také umožňuje ladit, dokud se nedostanete konkrétní aktivity na plátno kanálu. Stačí vložit zarážku na aktivitu, do kterého chcete otestovat a vyberte **ladění**. Data Factory zajišťuje, že test běží pouze až do zarážky aktivity na plátno kanálu. To *ladění, dokud* funkce je užitečná, pokud chcete otestovat celý kanál, ale pouze podmnožinu aktivity v kanálu.

![Zarážky na plátno kanálu](media/iterative-development-debugging/iterative-development-image4.png)

Pokud chcete nastavit zarážku, vyberte prvek na plátno kanálu. A *ladění, dokud* možnost se zobrazí jako prázdný červené kolečko v pravém horním rohu elementu.

![Před nastavením zarážky NAV vybraný element](media/iterative-development-debugging/iterative-development-image5.png)

Po výběru *ladění, dokud* možnost, se změní na plného červené kolečko k označení je povolena zarážka.

![Po nastavení zarážky na vybraný element](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Další postup
[Průběžná integrace a nasazování ve službě Azure Data Factory](continuous-integration-deployment.md)
