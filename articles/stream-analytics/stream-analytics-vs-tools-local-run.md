---
title: Testování dotazů Azure Stream Analytics místně pomocí sady Visual Studio
description: Tento článek popisuje postup testování dotazů místně pomocí Azure Stream Analytics Tools for Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: db26dd12d7c44f6079ee38364a4e9e240d2e7bc8
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42054261"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testování dotazů Stream Analytics místně pomocí sady Visual Studio

Nástroje Azure Stream Analytics pro Visual Studio můžete použít k otestování vaší úlohy Stream Analytics místně s ukázkovými daty.

Použijte tento [rychlý Start](stream-analytics-quick-create-vs.md) informace o vytvoření úlohy Stream Analytics pomocí sady Visual Studio.

## <a name="test-your-query"></a>Otestování dotazu

V projektu Azure Stream Analytics, klikněte dvakrát na **Script.asaql** otevřete skript v editoru. Můžete kompilovat dotaz, jestli jsou všechny chyby syntaxe. Editor dotazů podporuje technologii IntelliSense, barevné zvýrazňování syntaxe a značka chyby.

![Editor dotazů](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Přidat místní vstup

Pokud chcete ověřit váš dotaz proti místní statická data, klikněte pravým tlačítkem na vstup a vyberte **přidat místní vstup**.
   
![Přidat místní vstup](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
V místním okně vyberte ukázková data z místní cestu a **Uložit**.
   
![Přidat místní vstup](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Soubor s názvem **local_EntryStream.json** je automaticky přidán do složky vstupy.
   
![Seznam souborů místní vstupní složky](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Vyberte **spustit místně** v editoru dotazů. Nebo stisknutím klávesy F5.
   
![Spustit místně](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Výstup může zobrazit ve formátu tabulky přímo ze sady Visual Studio.

![Výstup ve formátu tabulky](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Můžete najít výstupní cesty z výstupu konzoly. Stisknutím libovolné klávesy otevřete složku výsledků.
   
![Místní spuštění](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Kontrola výsledků v místní složce.
   
![Výsledek místní složky](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Ukázkový vstup
Může také shromažďovat ukázková vstupní data z vstupního zdroje do místního souboru. Klikněte pravým tlačítkem na vstupní konfigurační soubor a vyberte **ukázková Data**. 

![Ukázková data](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Můžete pouze ukázková data streamování z Event Hubs a centra IoT hub. Dalších vstupních zdrojů nejsou podporovány. V místním dialogovém okně zadejte místní cestu k uložení ukázkových dat a vyberte **ukázka**.

![Vzorová data konfigurace](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Zobrazí se průběh na **výstup** okna. 

![Ukázkový výstup dat](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Další postup

* [Chcete-li zobrazit úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-vs-tools.md)
* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Kurz: Nasazení úlohy Azure Stream Analytics s CI/CD pomocí VSTS](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Průběžná integrace a vývoj s použitím nástrojů Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)