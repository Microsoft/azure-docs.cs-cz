---
title: Místní otestování Azure Stream Analytics úlohy pomocí ukázkových dat s využitím Visual Studio Code
description: Tento článek popisuje, jak testovat dotazy místně s použitím ukázkových dat pomocí Azure Stream Analyticsch nástrojů pro Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: 0122e67714efb201584a52f1cb763528dbb0b925
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903806"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Test Stream Analytics dotazy místně s použitím ukázkových dat pomocí Visual Studio Code

Pomocí Azure Stream Analytics nástrojů pro Visual Studio Code můžete testovat úlohy Stream Analytics lokálně pomocí ukázkových dat. Dotaz bude mít za následek soubory JSON ve složce **LocalRunOutputs** projektu.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [sadu .NET Core SDK](https://dotnet.microsoft.com/download) a restartujte Visual Studio Code.

* V tomto [rychlém](quick-create-visual-studio-code.md) startu se dozvíte, jak pomocí Visual Studio Code vytvořit úlohu Stream Analytics.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Nejprve musíte připravit ukázkové vstupní datové soubory. Pokud již máte v počítači nějaké soubory ukázkových dat, můžete tento krok přeskočit a přejít k následujícímu.

1. V horním řádku klikněte na **Náhled dat** ve vstupním konfiguračním souboru. Některá vstupní data budou načtena z IoT Hub a zobrazená v okně náhledu. Všimněte si, že může to chvíli trvat.

2. Po zobrazení dat klikněte na **Uložit jako** a uložte data do místního souboru.

 ![Náhled živého vstupu](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definování místního vstupu

1. Klikněte na **input.jsv** části vstupní složka v projektu Stream Analytics. Pak na horním řádku vyberte **Přidat místní vstup** .

    ![Přidat místní vstup z projektu](./media/quick-create-visual-studio-code/add-input-from-project.png)

    K otevření palety příkazů můžete použít také **kombinaci kláves CTRL + SHIFT + P** a zadat **ASA: přidat vstup**.

   ![Přidat vstup Stream Analytics v VS Code](./media/quick-create-visual-studio-code/add-input.png)

2. Vyberte **místní vstup**.

    ![Přidání místního vstupu do programu ASA v nástroji Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Vyberte **+ nový místní vstup**.

    ![Přidání nového místního vstupu do programu ASA v nástroji Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Zadejte stejný alias vstupu, který jste použili v dotazu.

    ![Přidat nový alias místního vstupu pro ASA](./media/vscode-local-run/new-local-input-alias.png)

5. V nově vygenerovaném **LocalInput_Input.js** souboru zadejte cestu k souboru, kde se nachází váš místní datový soubor.

    ![Zadejte cestu k místnímu souboru v aplikaci Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Vyberte možnost **Náhled dat** , abyste zobrazili náhled vstupních dat. Typ serializace pro vaše data se automaticky rozpozná, pokud je jeho JSON nebo CSV. Pomocí selektoru můžete zobrazit data v **tabulce** nebo v **nezpracovaném** formátu. Následující tabulka je příkladem dat ve **formátu tabulky**:

     ![Náhled místních dat ve formátu tabulky](./media/vscode-local-run/local-file-preview-table.png)

    V následující tabulce je příklad dat v **nezpracovaném formátu**:

    ![Náhled místních dat v nezpracovaném formátu](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Spustit dotazy místně

Vraťte se do editoru dotazů a vyberte **spustit místně**. Pak v rozevíracím seznamu vyberte **použít místní vstup** .

![V editoru dotazů vyberte spustit místně.](./media/vscode-local-run/run-locally.png)

![Použít místní vstup](./media/vscode-local-run/run-locally-use-local-input.png)

Výsledek je zobrazen v pravém okně. Můžete znovu spustit test kliknutím na tlačítko **Spustit** . Můžete také vybrat **otevřít ve složce** a zobrazit soubory výsledků v Průzkumníkovi souborů a dále je otevřít pomocí jiných nástrojů. Všimněte si, že soubory výsledků jsou k dispozici pouze ve formátu JSON.

![Zobrazit výsledky místního spuštění](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Další kroky

* [Testování Azure Stream Analytics úloh místně pomocí živého vstupu pomocí Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Prozkoumejte Azure Stream Analytics úlohy pomocí Visual Studio Code (Preview).](visual-studio-code-explore-jobs.md)
