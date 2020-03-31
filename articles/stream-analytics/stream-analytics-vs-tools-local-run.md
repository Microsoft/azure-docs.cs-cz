---
title: Testování dotazů Azure Stream Analytics místně ve Visual Studiu
description: Tento článek popisuje, jak testovat dotazy místně pomocí nástrojů Azure Stream Analytics pro Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 34c8555356d5c0142d7b677c8119fe66806ab064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834901"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Test Stream Analytics dotazy místně s Visual Studio

Pomocí nástrojů Azure Stream Analytics pro Visual Studio můžete své úlohy Stream Analytics testovat místně pomocí ukázkových dat nebo [živých dat](stream-analytics-live-data-local-testing.md). 

Pomocí tohoto [úvodního panelu](stream-analytics-quick-create-vs.md) se dozvíte, jak vytvořit úlohu Stream Analytics pomocí sady Visual Studio.

## <a name="test-your-query"></a>Otestování dotazu

V projektu Azure Stream Analytics poklikejte na **Soubor Script.asaql** a otevřete skript v editoru. Můžete zkompilovat dotaz a zjistit, zda existují nějaké chyby syntaxe. Editor dotazů podporuje technologii IntelliSense, zbarvení syntaxe a značku chyby.

![Editor dotazů](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Přidat místní vstup

Chcete-li ověřit dotaz na základě místních statických dat, klepněte pravým tlačítkem myši na vstup a vyberte přidat **místní vstup**.
   
![Přidat místní vstup](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
V rozbalovacím okně vyberte ukázková data z místní cesty a **Uložit**.
   
![Přidat místní vstup](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Soubor s názvem **local_EntryStream.json** je přidán automaticky do složky vstupy.
   
![Seznam souborů místní vstupní složky](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
V editoru dotazů vyberte **Spustit místně.** Nebo můžete stisknout klávesu F5.
   
![Spustit místně](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Výstup lze zobrazit ve formátu tabulky přímo z visual studia.

![Výstup ve formátu tabulky](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Výstupní cestu najdete z výstupu konzoly. Stisknutím libovolné klávesy otevřete složku výsledků.
   
![Místní spuštění](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Zkontrolujte výsledky v místní složce.
   
![Výsledek místní složky](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Vstup vzorku
Můžete také shromažďovat ukázková vstupní data ze vstupních zdrojů do místního souboru. Klepněte pravým tlačítkem myši na vstupní konfigurační soubor a vyberte **ukázková data**. 

![Ukázková data](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Můžete vzorkovat pouze streamování dat z event hubů nebo ioT hubů. Jiné vstupní zdroje nejsou podporovány. V rozbalovacím dialogovém okně vyplňte místní cestu k uložení ukázkových dat a vyberte **Ukázka**.

![Ukázková konfigurace dat](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Průběh můžete vidět v okně **Výstup.** 

![Ukázkový výstup dat](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Vytvoření úlohy Stream Analytics pomocí Visual Studia](stream-analytics-quick-create-vs.md)
* [Zobrazení úloh Azure Stream Analytics pomocí Visual Studia](stream-analytics-vs-tools.md)
* [Testování živých dat místně pomocí nástrojů Azure Stream Analytics pro Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)
* [Kurz: Nasazení úlohy Azure Stream Analytics s CI/CD pomocí Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Průběžná integrace a vývoj s použitím nástrojů Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
