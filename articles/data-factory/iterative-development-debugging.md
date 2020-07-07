---
title: Iterativní vývoj a ladění v Azure Data Factory
description: Naučte se vyvíjet a ladit kanály Data Factory v Azure Portal iterativním způsobem.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81411514"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterativní vývoj a ladění pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory umožňuje iterativní vývoj a ladění Data Factory kanálů.

Po dobu osmi minut a ukázku této funkce se podívejte na toto video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Funkce iterativního ladění
Vytvářejte kanály a provádějte testovací běhy pomocí možnosti **ladění** na plátně kanálu, aniž byste museli psát jediný řádek kódu.

![Možnost ladění na plátně kanálu](media/iterative-development-debugging/iterative-development-image1.png)

Zobrazte výsledky testovacích běhů v okně **výstup** plátna kanálu.

![Okno výstup plátna kanálu](media/iterative-development-debugging/iterative-development-image2.png)

Po úspěšném spuštění testu přidejte do kanálu další aktivity a pokračujte v ladění iterativním způsobem. Můžete také **Zrušit** testovací běh, dokud probíhá.

![Zrušení testovacího běhu](media/iterative-development-debugging/iterative-development-image3.png)

Při spuštění testů není nutné publikovat změny do objektu pro vytváření dat před výběrem příkazu **ladit**. Tato funkce je užitečná ve scénářích, ve kterých se chcete ujistit, že změny fungují podle očekávání, než aktualizujete pracovní postup datové továrny.

> [!IMPORTANT]
> Když vyberete **ladit** , kanál se ve skutečnosti spustí. Pokud například kanál obsahuje aktivitu kopírování, testovací běh zkopíruje data ze zdroje do cíle. V důsledku toho doporučujeme, abyste při ladění používali testovací složky v aktivitách kopírování a dalších aktivitách. Po ladění kanálu přepněte do vlastních složek, které chcete použít při normálním provozu.

## <a name="visualizing-debug-runs"></a>Vizualizace běhů ladění

Můžete vizualizovat všechna spuštění ladění, která pro datovou továrnu právě probíhá na jednom místě. V pravém horním rohu stránky vyberte **Zobrazit ladicí běhy** . Tato funkce je užitečná ve scénářích, kde jsou hlavní kanály spouštěny z ladicích běhů pro podřízené kanály, a vy chcete zobrazit všechna aktivní ladicí běhy v jednom zobrazení.

![Vyberte ikonu Zobrazit aktivní ladicí běhy.](media/iterative-development-debugging/view-debug-runs-image1.png)

![Ukázkový seznam aktivních běhů ladění](media/iterative-development-debugging/view-debug-runs-image2.png)

Pokud máte aktivní relace ladění toku dat, tyto relace se zobrazí v dolní části aktivního okna ladění. Můžete vybrat aktivní relaci toku dat a zastavit příslušný cluster.

![Ukázkový seznam spuštění ladění aktivního toku dat](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Monitorování běhů ladění

Testovací běhy iniciované funkcí **ladění** nejsou k dispozici v seznamu na kartě **monitorování** . Na kartě **monitorování** můžete zobrazit jenom běhy aktivované aktivačními **událostmi**triggeru, **plánovaného**nebo **bubnového okna** . Poslední testovací běh iniciované funkcí **ladění** můžete zobrazit v okně **výstup** plátna kanálu.

## <a name="setting-breakpoints-for-debugging"></a>Nastavení zarážek pro ladění

Data Factory také umožňuje ladit, dokud nedosáhnete určité aktivity na plátně kanálu. Pouze umístěte zarážku na aktivitu, dokud nechcete testovat, a vyberte **ladit**. Data Factory zajistí, že se test spustí pouze do aktivity zarážky na plátně kanálu. Tento *ladicí program* je použitelný, dokud nechcete testovat celý kanál, ale pouze podmnožinu aktivit uvnitř kanálu.

![Zarážky na plátně kanálu](media/iterative-development-debugging/iterative-development-image4.png)

Chcete-li nastavit zarážku, vyberte element na plátně kanálu. *Ladění, dokud* se možnost neobjeví jako prázdný červený kruh v pravém horním rohu elementu.

![Před nastavením zarážky pro vybraný element](media/iterative-development-debugging/iterative-development-image5.png)

Po výběru možnosti *ladit* do této možnosti se změní na vyplněný červený kroužek, aby označoval, že je povolená zarážka.

![Po nastavení zarážky u vybraného elementu](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Další kroky
[Průběžná integrace a nasazování v Azure Data Factory](continuous-integration-deployment.md)
