---
title: Ingestování dat z IoT Hub do Azure Průzkumník dat
description: V tomto článku se dozvíte, jak pomocí IoT Hub ingestovat (načítat) data do Azure Průzkumník dat.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: cbe9aa2ea664d97df6008de05d6cb84da9771bcc
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166547"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>Ingestování dat z IoT Hub do Azure Průzkumník dat (Preview)

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Průzkumník dat nabízí ingestování (načítání dat) od IoT Hub, platformy pro streamování velkých objemů dat a služby ingestování IoT.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* Vytvořte [testovací cluster a databázi](create-cluster-database-portal.md) s názvem databáze *TestDB*.

* [Ukázková aplikace](https://github.com/Azure-Samples/azure-iot-samples-csharp) a dokumentace pro simulaci zařízení

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pro spuštění ukázkové aplikace.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registrace zařízení do IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Data Exploreru

Nyní vytvoříte tabulku v Azure Průzkumník dat, do které budou centra IoT odesílat data. Vytvoříte tabulku v clusteru a databázi zřízené v části [**požadavky**](#prerequisites).

1. V Azure Portal přejděte do svého clusteru a vyberte **dotaz**.

    ![Dotaz ADX na portálu](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Zkopírujte následující příkaz do okna a vyberte **Spustit** pro vytvoření tabulky (pole s testovacími daty), která přijme ingestovaná data.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Spuštění vytvářecího dotazu](media/ingest-data-iot-hub/run-create-query.png)

1. Zkopírujte do okna následující příkaz a vyberte možnost **Spustit** pro mapování příchozích dat JSON na názvy sloupců a datové typy tabulky (tabulka).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Připojení tabulky Azure Průzkumník dat ke službě IoT Hub

Nyní se připojíte k IoT Hub z Azure Průzkumník dat. Po dokončení tohoto připojení se data přenášená do datových proudů centra IoT zařadí do [cílové tabulky, kterou jste vytvořili](#create-a-target-table-in-azure-data-explorer).

1. Na panelu nástrojů vyberte **oznámení** , abyste ověřili, že nasazení IoT Hub bylo úspěšné.

1. V clusteru, který jste vytvořili, vyberte databáze a potom vyberte databázi, kterou jste vytvořili **TestDB**.
    
    ![Výběr testovací databáze](media/ingest-data-iot-hub/select-database.png)

1. Vyberte **přijímání dat** a **přidejte datové připojení**. Pak vyplňte formulář následujícími informacemi. Až budete hotovi, vyberte **vytvořit** .

    ![IoT Hub připojení](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Zdroj dat**:

    **Nastavení** | **Popis pole**
    |---|---|
    | Název datového připojení | Název připojení, které chcete vytvořit v Azure Průzkumník dat
    | IoT Hub | Název centra IoT Hub |
    | Zásada sdíleného přístupu | Název zásad sdíleného přístupu. Musí mít oprávnění ke čtení. |
    | Skupina uživatelů |  Skupina uživatelů definovaná v předdefinovaném koncovém bodu IoT Hub |
    | Vlastnosti systému událostí | Vlastnosti systému událostí IoT Hub |
    | | 

    > [!NOTE]
    > V případě ručního [převzetí služeb při selhání](/azure/iot-hub/iot-hub-ha-dr#manual-failover)je nutné datové připojení znovu vytvořit.

    **Cílová tabulka**:

    K dispozici jsou dvě možnosti směrování přijatých dat: *statické* a *dynamické*. 
    V tomto článku použijete statické směrování, kde zadáte název tabulky, formát dat a mapování. Proto nechte **moje data zahrnovat informace o směrování** bez výběru.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Table | *TestTable* | Tabulka, kterou jste vytvořili v **TestDB**. |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, VÍCEŘÁDKOVé JSON, PSV, SOH, SCSV, TSV a TXT. |
    | Mapování sloupců | *TestMapping* | Mapování, které jste vytvořili v **TestDB**, které mapuje příchozí data JSON na názvy sloupců a datové typy **TestDB**. Vyžaduje se pro JSON, VÍCEŘÁDKOVé JSON a AVRO a volitelné pro jiné formáty.|
    | | |

    > [!TIP]
    > Vyberte **moje data zahrnují informace o směrování** pro použití dynamického směrování, kde data obsahují nezbytné informace o směrování, jak je vidět v komentářích [ukázkové aplikace](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Pokud jsou nastaveny statické i dynamické vlastnosti, přepíší dynamické vlastnosti statické. 

## <a name="generate-sample-data-for-testing"></a>Generovat ukázková data pro testování

Aplikace simulovaného zařízení se připojí ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. Stáhněte si ukázkový projekt C# z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip a extrahujte archiv ZIP.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Pak přejděte do složky **iot-hub\Quickstarts\simulated-device**.

1. V libovolném textovém editoru otevřete soubor **SimulatedDevice.cs**.

    Nahraďte hodnotu `s_connectionString` proměnné připojovacím řetězcem zařízení z [registrace zařízení do IoT Hub](#register-a-device-to-the-iot-hub). Změny pak uložte do souboru **SimulatedDevice.cs**.

1. V okně místního terminálu pomocí následujících příkazů nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

1. Spuštěním následujícího příkazu v okně místního terminálu sestavte a spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet run
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    ![Spuštění simulovaného zařízení](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Kontrola toku dat

Aplikace, která generuje data, teď může zobrazit tok dat ze služby IoT Hub do tabulky v clusteru.

1. V Azure Portal se v rámci centra IoT zobrazuje špička aktivity v době, kdy aplikace běží.

    ![IoT Hub metriky](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Pokud chcete zkontrolovat, kolik zpráv se zatím dostalo do databáze, spusťte v testovací databázi následující dotaz.

    ```Kusto
    TestTable
    | count
    ```

1. Chcete-li zobrazit obsah zpráv, spusťte následující dotaz:

    ```Kusto
    TestTable
    ```

    Sada výsledků dotazu:
    
    ![Zobrazit výsledky zpracovaných dat](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Průzkumník dat má agregační (dávku) zásadu pro příjem dat, která je určená k optimalizaci procesu ingestování. Tato zásada je ve výchozím nastavení nakonfigurovaná na 5 minut nebo 500 MB dat, takže se může vyskytnout latence. Podívejte se na téma [zásady dávkování](/azure/kusto/concepts/batchingpolicy) pro možnosti agregace. 
    > * Nakonfigurujte tabulku tak, aby podporovala streamování, a odeberte prodlevu v době odezvy. Viz [zásada streamování](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neplánujete znovu použít IoT Hub, vyčistěte **test-hub-RG**, abyste se vyhnuli nákladům.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků** a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je nabídka vlevo sbalená, výběrem ![tlačítko Rozbalit](media/ingest-data-event-hub/expand.png) ji rozbalte.

   ![Výběr skupiny prostředků k odstranění](media/ingest-data-event-hub/delete-resources-select.png)

1. Ve skupině prostředků **test-resource-group** vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit (*test-resource-group*), a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

* [Dotazování na data v Azure Průzkumník dat](web-query-data.md)
