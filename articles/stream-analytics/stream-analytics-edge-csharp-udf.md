---
title: Kurz – zápis uživatelem definovaných funkcí C# pro úlohy Azure Stream Analytics v aplikaci Visual Studio (Preview)
description: V tomto kurzu se dozvíte, jak psát funkce definované uživatelem v jazyce c# pro úlohy Stream Analytics v aplikaci Visual Studio.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 851229e441aa2fbdf7b6eec05390c0ce2b149da2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020464"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Kurz: zápis uživatelsky definované funkce jazyka C# pro Azure Stream Analytics úlohy (Preview)

Uživatelem definované funkce jazyka C# vytvořené v sadě Visual Studio umožňují rozšířit dotazovací jazyk Azure Stream Analytics o vlastní funkce. V jazyce C# můžete opakovaně používat existující kód (včetně knihoven DLL) a využívat matematickou nebo komplexní logiku. Uživatelem definované funkce můžete implementovat třemi způsoby: jako soubory CodeBehind v projektu Stream Analytics, uživatelem definované funkce z místního projektu jazyka C# nebo uživatelem definované funkce z existujícího balíčku z účtu úložiště. V tomto kurzu se k implementaci základní funkce jazyka C# používá metoda se souborem CodeBehind. Funkce UDF pro úlohy Stream Analytics je momentálně ve verzi Preview a neměla by se používat v produkčních úlohách.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření uživatelem definované funkce jazyka C# s využitím souboru CodeBehind
> * Otestujte úlohu Stream Analytics místně.
> * Publikujte svoji úlohu do Azure.

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že jste splnili následující požadavky:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [Nástroje Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md) a sadu funkcí **Vývoj pro Azure** nebo **Ukládání a zpracování dat**.
* Pokud vytváříte úlohu IoT Edge, podívejte se na stávající [Průvodce vývojem Stream Analytics Edge](stream-analytics-tools-for-visual-studio-edge-jobs.md) .

## <a name="create-a-container-in-your-azure-storage-account"></a>Vytvoření kontejneru v účtu služby Azure Storage

Kontejner, který vytvoříte, bude použit k uložení zkompilovaného balíčku jazyka C#. Pokud vytvoříte hraniční úlohu, použije se k nasazení balíčku do zařízení IoT Edge taky tento účet úložiště. Pro každou úlohu Stream Analytics použijte vyhrazený kontejner. Opakované používání stejného kontejneru pro více úloh Stream Analytics Edge se nepodporuje. Pokud už máte účet úložiště s existujícími kontejnery, můžete je použít. Pokud ne, budete muset [vytvořit nový kontejner](../storage/blobs/storage-quickstart-blobs-portal.md). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Vytvoření projektu Stream Analytics v aplikaci Visual Studio

1. Spusťte Visual Studio.

2. Vyberte **Soubor > Nový > Projekt**.

3. V seznamu šablon na levé straně vyberte **Stream Analytics** a pak vyberte **Azure Stream Analytics aplikace Edge** nebo **Azure Stream Analytics aplikaci**.

4.  Zadejte **Název** projektu, **Umístění** a **Název řešení** a vyberte **OK**.

    ![Vytvoření projektu Azure Stream Analytics Edge v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Konfigurace cesty k balíčku sestavení

1. Otevřete sadu Visual Studio a přejděte do **Průzkumníka řešení**.

2. Dvakrát klikněte na konfigurační soubor úlohy `EdgeJobConfig.json`.

3. Rozbalte část **Konfigurace kódu definovaného uživatelem** a vyplňte do konfigurace následující navrhované hodnoty:

   |**Nastavení**|**Navrhovaná hodnota**|
   |-------|---------------|
   |Prostředek nastavení globálního úložiště|Zvolit zdroj dat z aktuálního účtu|
   |Předplatné globálních nastavení úložiště| < vaše předplatné >|
   |Účet úložiště nastavení globálního úložiště| < svého účtu úložiště >|
   |Prostředek nastavení úložiště vlastního kódu|Zvolit zdroj dat z aktuálního účtu|
   |Vlastní nastavení úložiště kódu účet úložiště|< svého účtu úložiště >|
   |Kontejner nastavení vlastního úložiště kódu|< kontejneru úložiště >|


## <a name="write-a-c-udf-with-codebehind"></a>Zápis uživatelem definované funkce jazyka C# s využitím souboru CodeBehind
Soubor CodeBehind je soubor C# přidružený k jednomu skriptu dotazu ASA. Nástroje sady Visual Studio soubor CodeBehind po odeslání automaticky zkomprimují a nahrají do vašeho účtu úložiště Azure. Všechny třídy musí být definované jako *veřejné* a všechny objekty musí být definované jako *statické veřejné*.

1. V **Průzkumníku řešení** rozbalte soubor **Script.asql** a vyhledejte soubor CodeBehind **Script.asaql.cs**.

2. Nahraďte kód následující ukázkou:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Implementace uživatelem definované funkce

1. V **Průzkumníku řešení** otevřete soubor **Script.asaql**.

2. Stávající dotaz nahraďte následujícím dotazem:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Místní testování

1. Stáhněte si [soubor ukázkových dat simulátoru teploty](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

2. V **Průzkumníku řešení** rozbalte **Inputs**, klikněte pravým tlačítkem na soubor **Input.json** a vyberte **Přidat místní vstup**.

   ![Přidání místního vstupu do úlohy Stream Analytics v aplikaci Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Zadejte cestu k místnímu vstupnímu souboru s ukázkovými daty, která jste si stáhli, a vyberte **Uložit**.

    ![Konfigurace místního vstupu pro úlohu Stream Analytics v aplikaci Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. V editoru skriptů klikněte na **Spustit místně**. Jakmile místní spuštění úspěšně uloží výsledky výstupu, stisknutím libovolné klávesy zobrazte výsledky ve formátu tabulky. 

    ![Místní spuštění úlohy Azure Stream Analytics pomocí sady Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Můžete také vybrat **Otevřít složku výsledků** a zobrazit nezpracované soubory ve formátu JSON a CSV.

    ![Zobrazení výsledků místní úlohy Azure Stream Analytics pomocí sady Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Ladění uživatelem definované funkce
Uživatelem definovanou funkci jazyka C# můžete ladit místně stejným způsobem jako standardní kód jazyka C#. 

1. Přidejte do funkce jazyka C# zarážky.

    ![Přidání zarážek pro Stream Analytics uživatelsky definované funkci v aplikaci Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Stisknutím klávesy **F5** spusťte ladění. Program se podle očekávání bude zastavovat na zarážkách.

    ![Zobrazit Stream Analytics výsledky ladění uživatelem definovaných funkcí](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publikování úlohy do Azure
Jakmile místně otestujete svůj dotaz, v editoru skriptů vyberte **Odeslat do Azure** a publikujte úlohu do Azure.

![Odeslání úlohy Stream Analytics Edge do Azure ze sady Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Nasazení do zařízení IoT Edge
Pokud jste se rozhodli vytvořit úlohu Stream Analytics Edge, můžete ji teď nasadit jako modul IoT Edge. Postupujte podle [rychlého startu pro IoT Edge](../iot-edge/quickstart.md) a vytvořte službu IoT Hub, zaregistrujte zařízení IoT Edge a nainstalujte a spusťte na svém zařízení modul runtime IoT Edge. Pak postupujte podle kurzu [nasazení úlohy](../iot-edge/tutorial-deploy-stream-analytics.md#deploy-the-job) a nasaďte svou úlohu Stream Analytics jako modul IoT Edge. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili jednoduchou uživatelsky definovanou funkci jazyka C# pomocí CodeBehind, publikovali úlohu do Azure a nasadili úlohu do Azure nebo IoT Edge zařízení. 

Další informace o různých způsobech použití uživatelsky definovaných funkcí jazyka C# pro úlohy Stream Analytics najdete v tomto článku:

> [!div class="nextstepaction"]
> [Zápis funkcí C# pro Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)