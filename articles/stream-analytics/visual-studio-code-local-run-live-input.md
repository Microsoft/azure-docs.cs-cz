---
title: Testování dotazů Azure Stream Analytics místně proti vstupu živého datového proudu pomocí kódu Visual Studia
description: Tento článek popisuje, jak testovat dotazy místně proti vstupu živého datového proudu pomocí Nástroje Azure Stream Analytics pro Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660847"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Test Stream Analytics dotazy místně proti vstupu živého datového proudu pomocí kódu Sady Visual Studio

Nástroje Azure Stream Analytics pro kód Visual Studia můžete použít k lokálnímu testování úloh Stream Analytics proti vstupu živého přenosu. Vstup může pocházet ze zdroje, jako je Azure Event Hubs nebo Azure IoT Hub. Výstupní výsledky jsou odesílány jako soubory JSON do složky v projektu s názvem **LocalRunOutputs**.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte sadu [.NET Core SDK](https://dotnet.microsoft.com/download) a restartujte kód sady Visual Studio.

* Pomocí [tohoto rychlého startu](quick-create-vs-code.md) se dozvíte, jak vytvořit úlohu Stream Analytics pomocí kódu Visual Studia.

## <a name="define-a-live-stream-input"></a>Definování vstupu živého datového proudu

1. Klikněte pravým tlačítkem myši na složku **Vstupy** v projektu Stream Analytics. Pak vyberte **ASA: Přidat vstup** z kontextové nabídky.

   ![Přidání vstupu ze složky Vstupy](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   Můžete také vybrat **Ctrl+Shift+P,** chcete-li otevřít paletu příkazů a zadat **ASA: Přidat vstup**.

   ![Přidání vstupu Stream Analytics v kódu Visual Studia](./media/quick-create-vs-code/add-input.png)

2. Z rozevíracího seznamu zvolte typ vstupního zdroje.

   ![Vybrat centrum IoT jako vstupní možnost](./media/quick-create-vs-code/iot-hub.png)

3. Pokud jste přidali vstup z palety příkazů, zvolte skript dotazu Stream Analytics, který bude tento vstup používat. Měla by být automaticky naplněna cestou souboru k **myASAproj.asaql**.

   ![Výběr skriptu Stream Analytics v kódu Visual Studia](./media/quick-create-vs-code/asa-script.png)

4. Z rozbalovací nabídky zvolte **Vybrat ze svých předplatných Azure.**

    ![Výběr z předplatných](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Nakonfigurujte nově generovaný soubor JSON. Funkci CodeLens můžete použít k zadání řetězce, výběru z rozevíracího seznamu nebo změně textu přímo v souboru. Následující snímek obrazovky ukazuje **Vybrat z vašich předplatných** jako příklad.

   ![Konfigurace vstupu v kódu sady Visual Studio](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>Náhled vstupu

Chcete-li se ujistit, že vstupní data přicházejí, vyberte **náhled dat** v konfiguračním souboru živého vstupu z horního řádku. Některá vstupní data pocházejí z centra IoT a zobrazují se v okně náhledu. Zobrazení náhledu může trvat několik sekund.

 ![Náhled živého vstupu](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Spouštět dotazy místně

Vraťte se do editoru dotazů a **vyberte Spustit místně**. Pak v rozevíracím seznamu vyberte **Použít živý vstup.**

![V editoru dotazů vyberte možnost Spustit místně.](./media/vscode-local-run/run-locally.png)

![Vyberte možnost "Použít živý vstup"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Výsledek se zobrazí v pravém okně a aktualizuje se každé 3 sekundy. Můžete vybrat **Spustit a** otestovat znovu. Můžete také vybrat **Otevřít ve složce,** abyste viděli soubory výsledků v Průzkumníkovi souborů a otevřeli je pomocí kódu Visual Studia nebo nástroje, jako je Excel. Všimněte si, že soubory výsledků jsou k dispozici pouze ve formátu JSON.

Výchozí čas pro úlohu začít vytvářet výstup je nastavena na **nyní**. Čas můžete přizpůsobit výběrem tlačítka **Čas spuštění výstupu** v okně výsledků.

![Zobrazit výsledek místního spuštění](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Další kroky

* [Prozkoumejte úlohy Azure Stream Analytics pomocí kódu Visual Studia (preview)](visual-studio-code-explore-jobs.md)

* [Nastavení kanálů CI/CD pomocí balíčku npm](setup-cicd-vs-code.md)
