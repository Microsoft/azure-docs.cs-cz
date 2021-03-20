---
title: Testování Azure Stream Analytics dotazů lokálně v aplikaci Visual Studio
description: Tento článek popisuje, jak testovat dotazy místně pomocí Azure Stream Analyticsch nástrojů pro Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: b856826761f355e896cae48aa4a6fb62f5947e0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019920"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testování Stream Analytics dotazů v místním prostředí se sadou Visual Studio

Pomocí nástrojů Azure Stream Analytics pro Visual Studio můžete testovat úlohy Stream Analytics místně pomocí ukázkových dat nebo [živých dat](stream-analytics-live-data-local-testing.md). 

V tomto [rychlém](stream-analytics-quick-create-vs.md) startu se dozvíte, jak pomocí sady Visual Studio vytvořit úlohu Stream Analytics.

## <a name="test-your-query"></a>Otestování dotazu

V projektu Azure Stream Analytics poklikejte na **script. asaql** , aby se skript otevřel v editoru. Můžete zkompilovat dotaz a zjistit, zda nejsou k dispozici žádné chyby syntaxe. Editor dotazů podporuje technologii IntelliSense, barevné zvýrazňování syntaxe a značku chyby.

![Editor dotazů](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Přidat místní vstup

Pokud chcete dotaz ověřit u místních statických dat, klikněte pravým tlačítkem na vstup a vyberte **Přidat místní vstup**.
   
![Snímek obrazovky, který zvýrazní možnost přidat místní vstupní nabídku.](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
V automaticky otevíraném okně vyberte ze své místní cesty možnost Ukázková data a **uložte**.
   
![Přidat místní vstup](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Do složky vstupy se automaticky přidá soubor s názvem **local_EntryStream.js** .
   
![Seznam souborů místní vstupní složky](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
V editoru dotazů vyberte **spustit místně** . Můžete také stisknout klávesu F5.
   
![Spustit místně](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Výstup lze zobrazit ve formátu tabulky přímo ze sady Visual Studio.

![Výstup ve formátu tabulky](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Výstupní cestu můžete najít z výstupu konzoly. Stisknutím libovolné klávesy otevřete složku výsledků.
   
![Místní spuštění](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Podívejte se na výsledky v místní složce.
   
![Výsledek místní složky](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Ukázkový vstup
Ukázková vstupní data můžete také shromažďovat ze vstupních zdrojů do místního souboru. Klikněte pravým tlačítkem na vstupní konfigurační soubor a vyberte možnost **ukázková data**. 

![Ukázková data](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Můžete jenom vzorkovat streamování dat z Event Hubs nebo centra IoT. Jiné vstupní zdroje nejsou podporovány. V automaticky otevíraném dialogovém okně vyplňte místní cestu a uložte tak ukázková data a vyberte **Sample (ukázka**).

![Konfigurace ukázkových dat](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Průběh můžete zobrazit v okně **výstup** . 

![Výstup ukázkových dat](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)
* [Místní testování živých dat pomocí Azure Stream Analyticsch nástrojů pro Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)
* [Průběžná integrace a vývoj s použitím nástrojů Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
