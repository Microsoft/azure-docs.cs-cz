---
title: Zápis uživatelem definované funkce jazyka C# pro úlohu Azure Stream Analytics Edge v sadě Visual Studio (Preview)
description: Zjistěte, jak v sadě Visual Studio psát uživatelem definované funkce jazyka C# pro úlohy Azure Stream Analytics Edge.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 5597109a65a8af88bf286977d039656635565ed9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086895"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-edge-job-preview"></a>Kurz: Zápis C# uživatelem definovanou funkci pro úlohu Azure Stream Analytics Edge (Preview)

Uživatelem definované funkce jazyka C# vytvořené v sadě Visual Studio umožňují rozšířit dotazovací jazyk Azure Stream Analytics o vlastní funkce. V jazyce C# můžete opakovaně používat existující kód (včetně knihoven DLL) a využívat matematickou nebo komplexní logiku. Uživatelem definované funkce můžete implementovat třemi způsoby: jako soubory CodeBehind v projektu Stream Analytics, uživatelem definované funkce z místního projektu jazyka C# nebo uživatelem definované funkce z existujícího balíčku z účtu úložiště. V tomto kurzu se k implementaci základní funkce jazyka C# používá metoda se souborem CodeBehind. Funkce uživatelem definovaných funkcí pro úlohy Stream Analytics Edge je v současné době ve verzi a neměla by se používat v produkčních úlohách.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření uživatelem definované funkce jazyka C# s využitím souboru CodeBehind
> * Místní testování úlohy Stream Analytics Edge
> * Publikování úlohy Edge do Azure

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste splnili následující požadavky:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [Nástroje Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md) a sadu funkcí **Vývoj pro Azure** nebo **Ukládání a zpracování dat**.
* Prohlédněte si stávající [příručku pro vývojáře pro Stream Analytics Edge](stream-analytics-tools-for-visual-studio-edge-jobs.md).

## <a name="create-a-container-in-your-azure-storage-account"></a>Vytvoření kontejneru v účtu služby Azure Storage

Kontejner, který vytvoříte, se použije k uložení zkompilovaného balíčku jazyka C# a nasazení tohoto balíčku do zařízení IoT Edge. Pro každou úlohu Stream Analytics použijte vyhrazený kontejner. Opakované používání stejného kontejneru pro více úloh Stream Analytics Edge se nepodporuje. Pokud už máte účet úložiště s existujícími kontejnery, můžete je použít. Pokud ne, budete muset [vytvořit nový kontejner](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-edge-project-in-visual-studio"></a>Vytvoření projektu Stream Analytics Edge v sadě Visual Studio

1. Spusťte Visual Studio.

2. Vyberte **Soubor > Nový > Projekt**.

3. V seznamu šablon na levé straně vyberte **Stream Analytics** a pak vyberte **Aplikace Azure Stream Analytics Edge**.

4.  Zadejte **Název** projektu, **Umístění** a **Název řešení** a vyberte **OK**.

    ![Vytvoření projektu Azure Stream Analytics Edge v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Konfigurace cesty k balíčku sestavení

1. Otevřete sadu Visual Studio a přejděte do **Průzkumníka řešení**.

2. Dvakrát klikněte na konfigurační soubor úlohy `EdgeJobConfig.json`.

3. Rozbalte část **Konfigurace kódu definovaného uživatelem** a vyplňte do konfigurace následující navrhované hodnoty:

    |**Nastavení**  |**Navrhovaná hodnota**  |
    |---------|---------|
    |Zdroj sestavení  |  Odkaz na místní projekt nebo soubor CodeBehind   |
    |Prostředek  |  Zvolit data z aktuálního účtu   |
    |Předplatné  |  Zvolte vaše předplatné.   |
    |Účet úložiště  |  Vyberte svůj účet úložiště   |
    |Kontejner  |  Zvolte kontejner, který jste vytvořili ve svém účtu úložiště.   |

    ![Konfigurace úlohy Azure Stream Analytics Edge v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-edge-job-config.png)


## <a name="write-a-c-udf-with-codebehind"></a>Zápis uživatelem definované funkce jazyka C# s využitím souboru CodeBehind
Soubor CodeBehind je soubor jazyka C# přidružený k jednomu skriptu dotazu ASA Edge. Nástroje sady Visual Studio soubor CodeBehind po odeslání automaticky zkomprimují a nahrají do vašeho účtu úložiště Azure. Všechny třídy musí být definované jako *veřejné* a všechny objekty musí být definované jako *statické veřejné*.

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

1. Stáhněte si [datový soubor ukázkového simulátoru teploty](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json) hraničních zařízení.

2. V **Průzkumníku řešení** rozbalte **Inputs**, klikněte pravým tlačítkem na soubor **Input.json** a vyberte **Přidat místní vstup**.

   ![Přidat místní vstup do úlohy Stream Analytics v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Zadejte cestu k místnímu vstupnímu souboru s ukázkovými daty, která jste si stáhli, a vyberte **Uložit**.

    ![Místní konfigurace vstupu úlohy Stream Analytics v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. V editoru skriptů klikněte na **Spustit místně**. Jakmile místní spuštění úspěšně uloží výsledky výstupu, stisknutím libovolné klávesy zobrazte výsledky ve formátu tabulky. 

    ![Místní spuštění úlohy Azure Stream Analytics pomocí sady Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Můžete také vybrat **Otevřít složku výsledků** a zobrazit nezpracované soubory ve formátu JSON a CSV.

    ![Zobrazení výsledků místní úlohy Azure Stream Analytics pomocí sady Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Ladění uživatelem definované funkce
Uživatelem definovanou funkci jazyka C# můžete ladit místně stejným způsobem jako standardní kód jazyka C#. 

1. Přidejte do funkce jazyka C# zarážky.

    ![Přidat zarážky do Stream Analytics uživatelem definované funkce v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Stisknutím klávesy **F5** spusťte ladění. Program se podle očekávání bude zastavovat na zarážkách.

    ![Zobrazit Stream Analytics uživatelem definované funkce ladění výsledky](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publikování úlohy do Azure
Jakmile místně otestujete svůj dotaz, v editoru skriptů vyberte **Odeslat do Azure** a publikujte úlohu do Azure.

![Odeslání úlohy Stream Analytics Edge do Azure ze sady Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Nasazení do zařízení IoT Edge
Vaše úloha Stream Analytics je teď připravená na nasazení jako modul IoT Edge. Postupujte podle [rychlého startu pro IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart) a vytvořte službu IoT Hub, zaregistrujte zařízení IoT Edge a nainstalujte a spusťte na svém zařízení modul runtime IoT Edge. Pak postupujte podle kurzu [nasazení úlohy](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) a nasaďte svou úlohu Stream Analytics jako modul IoT Edge. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili jednoduchou uživatelem definovanou funkci jazyka C# s využitím souboru CodeBehind, publikovali jste svou úlohu do Azure a pomocí portálu IoT Hub jste úlohu nasadili do zařízení IoT Edge. 

Další informace o různých způsobech používání uživatelem definovaných funkcí jazyka C# pro úlohy Stream Analytics Edge najdete v tomto článku:

> [!div class="nextstepaction"]
> [Zápis C# funkce pro Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
