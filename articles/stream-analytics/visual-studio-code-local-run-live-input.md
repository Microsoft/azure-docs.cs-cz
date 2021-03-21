---
title: Test Azure Stream Analytics dotazy místně proti vstupu živého datového proudu pomocí Visual Studio Code
description: Tento článek popisuje, jak testovat dotazy místně proti vstupu živého streamu pomocí Azure Stream Analyticsch nástrojů pro Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: dd4966ee75e9dc0ff401823e4291f8d299c8893c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93122890"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Test Stream Analytics dotazy místně proti vstupu živého datového proudu pomocí Visual Studio Code

Můžete použít Azure Stream Analytics nástroje pro Visual Studio Code k otestování Stream Analytics úloh místně proti vstupu do živého streamu. Vstup může pocházet ze zdroje, jako je Azure Event Hubs nebo Azure IoT Hub. Výsledky výstupu se odesílají jako soubory JSON do složky ve vašem projektu s názvem **LocalRunOutputs**.

## <a name="prerequisites"></a>Předpoklady

* Nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download) a restartujte Visual Studio Code.

* V [tomto rychlém](quick-create-visual-studio-code.md) startu se dozvíte, jak vytvořit Stream Analyticsovou úlohu pomocí Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Definice vstupu živého streamu

1. V projektu Stream Analytics klikněte pravým tlačítkem na složku **vstupy** . Pak vyberte **ASA: přidat vstup** z místní nabídky.

   ![Přidat vstup ze složky Inputs](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

   Můžete také vybrat **CTRL + SHIFT + P** a otevřít paletu příkazů a zadat **ASA: přidat vstup**.

   ![Přidat vstup Stream Analytics v Visual Studio Code](./media/quick-create-visual-studio-code/add-input.png)

2. Z rozevíracího seznamu vyberte typ zdroje vstupu.

   ![Jako vstupní možnost vyberte IoT Hub.](./media/quick-create-visual-studio-code/iot-hub.png)

3. Pokud jste přidali vstup z palety příkazů, vyberte Stream Analytics skript dotazu, který bude používat vstup. Měl by se automaticky naplnit cestou k souboru **myASAproj. asaql**.

   ![Vyberte Stream Analytics skript v Visual Studio Code](./media/quick-create-visual-studio-code/asa-script.png)

4. V rozevírací nabídce **Vyberte vybrat z předplatných Azure** .

    ![Vybrat z předplatných](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Nakonfigurujte nově vygenerovaný soubor JSON. Můžete použít funkci CodeLens, která vám umožní zadat řetězec, vybrat z rozevíracího seznamu nebo změnit text přímo v souboru. Následující snímek obrazovky ukazuje jako příklad **možnost vybrat z vašich předplatných** .

   ![Konfigurace vstupu v Visual Studio Code](./media/quick-create-visual-studio-code/configure-input.png)

## <a name="preview-input"></a>Náhled vstupu

Aby se zajistilo, že vstupní data přicházejí, vyberte v horním řádku možnost **Zobrazit data** v konfiguračním souboru živého vstupu. Některá vstupní data přicházejí ze služby IoT Hub a zobrazují se v okně náhledu. Zobrazení náhledu může trvat několik sekund.

 ![Náhled živého vstupu](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Spustit dotazy místně

Vraťte se do editoru dotazů a vyberte **spustit místně**. Pak v rozevíracím seznamu vyberte **použít živý vstup** .

![V editoru dotazů vyberte spustit místně.](./media/vscode-local-run/run-locally.png)

![Vyberte použít živý vstup.](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Výsledek se zobrazí v pravém okně a každé 3 sekundy se aktualizuje. Můžete znovu vybrat možnost **Spustit** pro otestování. Můžete také vybrat **otevřít ve složce** a zobrazit soubory výsledků v Průzkumníkovi souborů a otevřít je pomocí Visual Studio Code nebo nástroje, jako je Excel. Všimněte si, že soubory výsledků jsou k dispozici pouze ve formátu JSON.

Výchozí čas, kdy se má úloha začít vytvářet výstup, je nastavená na hodnotu **nyní**. Čas můžete přizpůsobit tak, že v okně výsledků vyberete tlačítko **čas spuštění výstupu** .

![Zobrazit výsledky místního spuštění](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Další kroky

* [Prozkoumejte Azure Stream Analytics úlohy pomocí Visual Studio Code (Preview).](visual-studio-code-explore-jobs.md)

* [Nastavení kanálů CI/CD pomocí balíčku npm](./cicd-overview.md)