---
title: Iterační vývoji a ladění v Azure Data Factory | Microsoft Docs
description: Naučte se vyvíjet a ladit kanály pro vytváření dat interaktivně na portálu Azure.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 05/14/2018
ms.topic: article
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: e403afa5c870b2d007a8c5e1d46162cd899ead29
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterační vývoji a ladění pomocí Azure Data Factory

Azure Data Factory umožňuje interaktivně vyvíjet a ladit kanály Data Factory.

Osm minutu úvod a ukázkový této funkce najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Iterační funkce ladění
Vytvoření kanálů a testování spustí pomocí **ladění** schopnosti na plátno kanálu bez nutnosti napsat jediný řádek kódu.

![Ladění schopností na plátně kanálu](media/iterative-development-debugging/iterative-development-image1.png)

Zobrazení výsledků svůj test běží v **výstup** okno na plátno kanálu.

![Výstup – okno plátna kanálu](media/iterative-development-debugging/iterative-development-image2.png)

Po úspěšném provedení testu, spuštění, přidejte další aktivity do kanálu a pokračovat, ladění iterativní způsobem. Můžete také **zrušit** testu spustit, když probíhá.

![Zrušit spustit test](media/iterative-development-debugging/iterative-development-image3.png)

Při testování běží, není nutné publikovat změny k objektu pro vytváření dat, než budete moci zvolit **ladění**. To je užitečné v situacích, ve které chcete Ujistěte se, že změny fungují podle očekávání, než aktualizujete pracovního postupu data factory.

## <a name="more-info-about-debugging"></a>Další informace o ladění

1. Že test běží initiated s **ladění** funkce nejsou k dispozici v seznamu na **monitorování** kartě. Můžete pouze najdete spouští spouštěná s **aktivační událost teď**, **plán**, nebo **Přeskakujícího okna** aktivuje v **monitorování** kartě. Zobrazí poslední test spusťte initiated s **ladění** funkci **výstup** okno na plátno kanálu.

2. Výběr **ladění** skutečně spustí kanálu. Ano například pokud kanál obsahuje aktivitu kopírování, testovací běh kopíruje data ze zdroje do cíle. V důsledku toho doporučujeme používat testovací složek ve vaší kopie a ostatní aktivity, při ladění. Poté, co jste ladit kanálu, přepněte na skutečné složky, které chcete používat v běžném provozu.

## <a name="setting-breakpoints-for-debugging"></a>Nastavení zarážek pro ladění

Objekt pro vytváření dat umožňuje taky ladění, dokud se nedostanete konkrétní aktivitu na plátně kanálu. Právě umístí zarážku aktivity, do kterého chcete otestovat a vyberte **ladění**. Objekt pro vytváření dat zajišťuje, že test běží pouze do aktivity zarážek na plátně kanálu. To *ladění, dokud* funkce je užitečná, když nechcete, aby k testování celého kanálu, ale jenom podmnožinu aktivity v kanálu.

![Zarážky na plátně kanálu](media/iterative-development-debugging/iterative-development-image4.png)

Pokud chcete nastavit zarážky, vyberte prvek na plátně kanálu. A *ladění, dokud* možnost se zobrazí jako prázdný červené kolečko v pravém horním rohu elementu.

![Před nastavením zarážku na vybraný element](media/iterative-development-debugging/iterative-development-image5.png)

Po výběru *ladění, dokud* možnost, změní se na vyplněný červené kolečko udávajících na zarážce je povoleno.

![Po nastavení boru přerušení na vybraný element](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Další postup
[Průběžnou integraci a nasazení v Azure Data Factory](continuous-integration-deployment.md)
