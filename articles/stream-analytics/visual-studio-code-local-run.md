---
title: Testování úlohy Azure Stream Analytics místně s ukázkovými daty pomocí kódu Visual Studia
description: Tento článek popisuje, jak testovat dotazy místně s ukázkovými daty pomocí nástrojů Azure Stream Analytics pro kód Visual Studia.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486466"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Test Stream Analytics dotazy místně s ukázkovými daty pomocí kódu Sady Visual Studio

Pomocí nástrojů Azure Stream Analytics pro kód Visual Studia můžete své úlohy Stream Analytics testovat místně s ukázkovými daty. Výsledky dotazu můžete najít v souborech JSON ve složce **LocalRunOutputs** vašeho projektu.

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [základní sadu SDK rozhraní .NET](https://dotnet.microsoft.com/download) a restartujte kód sady Visual Studio.

* Pomocí tohoto [rychlého startu](quick-create-vs-code.md) se dozvíte, jak vytvořit úlohu Stream Analytics pomocí kódu Sady Visual Studio.

## <a name="prepare-sample-data"></a>Příprava ukázkových dat

Nejprve je třeba připravit ukázkové vstupní datové soubory. Pokud již máte v počítači nějaké ukázkové datové soubory, můžete tento krok přeskočit a přejít k dalšímu.

1. Z horního řádku klikněte na **Náhled dat** ve vstupním konfiguračním souboru. Některá vstupní data budou načtena ze aplikace IoT Hub a zobrazena v okně náhledu. Všimněte si, že to může chvíli trvat.

2. Po zobrazení dat klikněte na **Uložit jako** a uložte je do místního souboru.

 ![Náhled živého vstupu](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definování místního vstupu

1. Klikněte na **input.json** v části Vstupy ve složce Stream Analytics v projektu Stream Analytics. Pak vyberte **Přidat místní vstup** z horního řádku.

    ![Přidání místního vstupu z projektu](./media/quick-create-vs-code/add-input-from-project.png)

    Můžete také použít **Ctrl +Shift+P** otevřít paletu příkazů a zadat **ASA: Přidat vstup**.

   ![Přidání vstupu Stream Analytics v kódu VS](./media/quick-create-vs-code/add-input.png)

2. Vyberte **místní vstup**.

    ![Přidání místního vstupu ASA v kódu sady Visual Studio](./media/vscode-local-run/add-local-input.png)

3. Vyberte **+ Nový místní vstup**.

    ![Přidání nového místního vstupu ASA v kódu sady Visual Studio](./media/vscode-local-run/add-new-local-input.png)

4. Zadejte stejný vstupní alias, který jste použili v dotazu.

    ![Přidání nového místního vstupního aliasu ASA](./media/vscode-local-run/new-local-input-alias.png)

5. V nově generovaném souboru **LocalInput_Input.json** zadejte cestu k souboru, kde je umístěn místní datový soubor.

    ![Zadání cesty k místnímu souboru v sadě Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Vyberte **Náhled dat,** chcete-li zobrazit náhled vstupních dat. Typ serializace pro vaše data je automaticky rozpoznán, pokud jeho JSON nebo CSV. Pomocí voliče můžete zobrazit data ve formátu **Tabulka** nebo **Nezpracovaný.** Následující tabulka je příkladem dat ve **formátu tabulka**:

     ![Náhled místních dat ve formátu tabulky](./media/vscode-local-run/local-file-preview-table.png)

    Následující tabulka je příkladem dat ve **formátu Raw**:

    ![Náhled místních dat v nezpracovaném formátu](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Spouštět dotazy místně

Vraťte se do editoru dotazů a **vyberte Spustit místně**. Pak vyberte **Použít místní vstup** z rozevíracího seznamu.

![Vybrat spustit místně v editoru dotazů](./media/vscode-local-run/run-locally.png)

![Použít místní vstup](./media/vscode-local-run/run-locally-use-local-input.png)

Výsledek se zobrazí v pravém okně. Chcete-li znovu otestovat, můžete klepnout na tlačítko **Spustit.** Můžete také vybrat **Otevřít ve složce,** abyste viděli soubory výsledků v průzkumníku souborů a dále je otevřeli pomocí jiných nástrojů. Všimněte si, že soubory výsledků jsou k dispozici pouze ve formátu JSON.

![Zobrazit výsledek místního spuštění](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Další kroky

* [Testování úloh Azure Stream Analytics místně pomocí živého vstupu pomocí kódu Visual Studia](visual-studio-code-local-run-live-input.md)

* [Prozkoumejte úlohy Azure Stream Analytics pomocí kódu Visual Studia (Preview)](visual-studio-code-explore-jobs.md)
