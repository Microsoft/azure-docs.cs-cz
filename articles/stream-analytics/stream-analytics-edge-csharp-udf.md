---
title: Kurz – zápis uživatelských funkcí jazyka C# pro úlohy Azure Stream Analytics ve Visual Studiu (Preview)
description: Tento kurz ukazuje, jak psát c# uživatelem definované funkce pro úlohy Stream Analytics v sadě Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 1d71f4c5616efb05efe2733c49507b085ca2dcf6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75426301"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Kurz: Napište uživatelem definovanou funkci C# pro úlohu Azure Stream Analytics (preview)

Uživatelem definované funkce jazyka C# vytvořené v sadě Visual Studio umožňují rozšířit dotazovací jazyk Azure Stream Analytics o vlastní funkce. V jazyce C# můžete opakovaně používat existující kód (včetně knihoven DLL) a využívat matematickou nebo komplexní logiku. Uživatelem definované funkce můžete implementovat třemi způsoby: jako soubory CodeBehind v projektu Stream Analytics, uživatelem definované funkce z místního projektu jazyka C# nebo uživatelem definované funkce z existujícího balíčku z účtu úložiště. V tomto kurzu se k implementaci základní funkce jazyka C# používá metoda se souborem CodeBehind. Funkce UDF pro úlohy Stream Analytics je aktuálně ve verzi preview a neměla by se používat v produkčních úlohách.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření uživatelem definované funkce jazyka C# s využitím souboru CodeBehind
> * Otestujte úlohu Stream Analytics místně.
> * Publikujte svou úlohu do Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste splnili následující požadavky:

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [Nástroje Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md) a sadu funkcí **Vývoj pro Azure** nebo **Ukládání a zpracování dat**.
* Podívejte se na existující [Stream Analytics Edge development guide], pokud vytváříte úlohu IoT Edge (stream-analytics-tools-for-visual-studio-edge-jobs.md).

## <a name="create-a-container-in-your-azure-storage-account"></a>Vytvoření kontejneru v účtu služby Azure Storage

Kontejner, který vytvoříte, bude použit k uložení kompilovaného balíčku Jazyka C#. Pokud vytvoříte úlohu Edge, tento účet úložiště se také použije k nasazení balíčku do zařízení IoT Edge. Pro každou úlohu Stream Analytics použijte vyhrazený kontejner. Opakované používání stejného kontejneru pro více úloh Stream Analytics Edge se nepodporuje. Pokud už máte účet úložiště s existujícími kontejnery, můžete je použít. Pokud ne, budete muset [vytvořit nový kontejner](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Vytvoření projektu Stream Analytics ve Visual Studiu

1. Spusťte Visual Studio.

2. Vyberte **Soubor > Nový > Projekt**.

3. V seznamu šablon vlevo vyberte **Stream Analytics**a pak vyberte **Azure Stream Analytics Edge Application** nebo Azure Stream **Analytics Application**.

4.  Zadejte **Název** projektu, **Umístění** a **Název řešení** a vyberte **OK**.

    ![Vytvoření projektu Azure Stream Analytics Edge v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Konfigurace cesty k balíčku sestavení

1. Otevřete sadu Visual Studio a přejděte do **Průzkumníka řešení**.

2. Dvakrát klikněte na konfigurační soubor úlohy `EdgeJobConfig.json`.

3. Rozbalte část **Konfigurace kódu definovaného uživatelem** a vyplňte do konfigurace následující navrhované hodnoty:

   |**Nastavení**|**Navrhovaná hodnota**|
   |-------|---------------|
   |Prostředek globálního nastavení úložiště|Zvolit zdroj dat z aktuálního účtu|
   |Předplatné globálního nastavení úložiště| < > předplatného|
   |Účet úložiště globálního nastavení úložiště| < > účtu úložiště|
   |Prostředek nastavení vlastního úložiště kódu|Zvolit zdroj dat z aktuálního účtu|
   |Účet úložiště vlastního úložiště kódu|< > účtu úložiště|
   |Kontejner nastavení úložiště vlastního kódu|< > skladovacího kontejneru|


## <a name="write-a-c-udf-with-codebehind"></a>Zápis uživatelem definované funkce jazyka C# s využitím souboru CodeBehind
Soubor CodeBehind je soubor Jazyka C# přidružený k jedinému skriptu dotazu ASA. Nástroje sady Visual Studio soubor CodeBehind po odeslání automaticky zkomprimují a nahrají do vašeho účtu úložiště Azure. Všechny třídy musí být definované jako *veřejné* a všechny objekty musí být definované jako *statické veřejné*.

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

1. Stáhněte si [ukázkový datový soubor simulátoru teploty](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

2. V **Průzkumníku řešení** rozbalte **Inputs**, klikněte pravým tlačítkem na soubor **Input.json** a vyberte **Přidat místní vstup**.

   ![Přidání místního vstupu do úlohy Stream Analytics ve Visual Studiu](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Zadejte cestu k místnímu vstupnímu souboru s ukázkovými daty, která jste si stáhli, a vyberte **Uložit**.

    ![Konfigurace místního vstupu pro úlohu Stream Analytics ve Visual Studiu](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. V editoru skriptů klikněte na **Spustit místně**. Jakmile místní spuštění úspěšně uloží výsledky výstupu, stisknutím libovolné klávesy zobrazte výsledky ve formátu tabulky. 

    ![Místní spuštění úlohy Azure Stream Analytics pomocí sady Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Můžete také vybrat **Otevřít složku výsledků** a zobrazit nezpracované soubory ve formátu JSON a CSV.

    ![Zobrazení výsledků místní úlohy Azure Stream Analytics pomocí sady Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Ladění uživatelem definované funkce
Uživatelem definovanou funkci jazyka C# můžete ladit místně stejným způsobem jako standardní kód jazyka C#. 

1. Přidejte do funkce jazyka C# zarážky.

    ![Přidání zarážek do uživatelem definované funkce Stream Analytics v sadě Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Stisknutím klávesy **F5** spusťte ladění. Program se podle očekávání bude zastavovat na zarážkách.

    ![Zobrazit výsledky ladění uživatelem definovaných funkcí Služby Stream Analytics](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publikování úlohy do Azure
Jakmile místně otestujete svůj dotaz, v editoru skriptů vyberte **Odeslat do Azure** a publikujte úlohu do Azure.

![Odeslání úlohy Stream Analytics Edge do Azure ze sady Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Nasazení do zařízení IoT Edge
Pokud jste se rozhodli vytvořit úlohu Edge Služby Streamování Analytics, teď ji můžete nasadit jako modul IoT Edge. Postupujte podle [rychlého startu pro IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart) a vytvořte službu IoT Hub, zaregistrujte zařízení IoT Edge a nainstalujte a spusťte na svém zařízení modul runtime IoT Edge. Pak postupujte podle kurzu [nasazení úlohy](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) a nasaďte svou úlohu Stream Analytics jako modul IoT Edge. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili jednoduchou uživatelem definovanou funkci Jazyka C# pomocí CodeBehind, publikovali jste úlohu do Azure a nasadili úlohu do zařízení Azure nebo IoT Edge. 

Další informace o různých způsobech použití uživatelem definovaných funkcí jazyka C# pro úlohy Stream Analytics najdete v tomto článku:

> [!div class="nextstepaction"]
> [Napište funkce Jazyka C# pro Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
