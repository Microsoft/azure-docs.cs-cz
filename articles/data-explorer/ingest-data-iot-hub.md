---
title: Ingestování dat z IoT Hub do Azure Průzkumník dat
description: V tomto článku se dozvíte, jak pomocí IoT Hub ingestovat (načítat) data do Azure Průzkumník dat.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 327fd5352a3f067638c7f9ceb51e2de9e284d845
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947853"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>Ingestování dat z IoT Hub do Azure Průzkumník dat (Preview)

Azure Průzkumník dat je rychlá a vysoce škálovatelná služba průzkumu dat pro data protokolů a telemetrie. Azure Průzkumník dat nabízí ingestování (načítání dat) od IoT Hub, platformy pro streamování velkých objemů dat a služby ingestování IoT.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* Vytvořte [testovací cluster a databázi](create-cluster-database-portal.md) s názvem databáze *TestDB*.

* [Ukázková aplikace](https://github.com/Azure-Samples/azure-iot-samples-csharp) a dokumentace pro simulaci zařízení

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pro spuštění ukázkové aplikace.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registrace zařízení do IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Průzkumník dat

Nyní vytvoříte tabulku v Azure Průzkumník dat, do které budou centra IoT odesílat data. Vytvoříte tabulku v clusteru a databázi zřízené v části [**požadavky**](#prerequisites).

1. V Azure Portal přejděte do svého clusteru a vyberte **dotaz**.

    ![Dotaz ADX na portálu](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Zkopírujte následující příkaz do okna a vyberte **Spustit** pro vytvoření tabulky (pole s testovacími daty), která přijme ingestovaná data.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Spustit dotaz Create](media/ingest-data-iot-hub/run-create-query.png)

1. Zkopírujte do okna následující příkaz a vyberte možnost **Spustit** pro mapování příchozích dat JSON na názvy sloupců a datové typy tabulky (tabulka).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Připojení tabulky Azure Průzkumník dat ke službě IoT Hub

Nyní se připojíte k IoT Hub z Azure Průzkumník dat. Po dokončení tohoto připojení se data přenášená do datových proudů centra IoT [zařadí do cílové tabulky, kterou jste vytvořili](#create-a-target-table-in-azure-data-explorer).

1. Na panelu nástrojů vyberte **oznámení** , abyste ověřili, že nasazení IoT Hub bylo úspěšné.

1. V clusteru, který jste vytvořili, **Vyberte databáze a potom vyberte** databázi, kterou jste vytvořili **TestDB**.
    
    ![Vybrat testovací databázi](media/ingest-data-iot-hub/select-database.png)

1. Vyberte **přijímání dat** a **přidejte datové připojení**. Pak vyplňte formulář následujícími informacemi. Až budete hotovi, vyberte **vytvořit** .

    ![IoT Hub připojení](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Zdroj dat**:

    **Nastavením** | **Popis pole**
    |---|---|
    | Název datového připojení | Název připojení, které chcete vytvořit v Azure Průzkumník dat
    | IoT Hub | Název IoT Hub |
    | Zásady sdíleného přístupu | Název zásad sdíleného přístupu. Musí mít oprávnění ke čtení. |
    | Skupina uživatelů |  Skupina uživatelů definovaná v předdefinovaném koncovém bodu IoT Hub |
    | Vlastnosti systému událostí | [IoT Hub vlastností systému událostí](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages) , pokud existuje více záznamů na událost, budou do prvního z nich přidány vlastnosti systému.|
    | | 

    > [!NOTE]
    > V případě [ručního převzetí služeb při selhání](/azure/iot-hub/iot-hub-ha-dr#manual-failover)je nutné datové připojení znovu vytvořit.

    **Cílová tabulka**:

    K dispozici jsou dvě možnosti směrování přijatých dat: *statické* a *dynamické*. 
    V tomto článku použijete statické směrování, kde zadáte název tabulky, formát dat a mapování. Proto nechte **moje data zahrnovat informace o směrování** bez výběru.

     **Nastavením** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Tabulka | *Testovací navýšení* | Tabulka, kterou jste vytvořili v **TestDB**. |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, VÍCEŘÁDKOVé JSON, PSV, SOHSV, SCSV, TSV, TSVE a TXT. |
    | Mapování sloupce | *TestMapping* | Mapování, které jste vytvořili v **TestDB**, které mapuje příchozí data JSON na názvy sloupců a datové typy **TestDB**. Vyžaduje se pro JSON, VÍCEŘÁDKOVé JSON a AVRO a volitelné pro jiné formáty.|
    | | |

    > [!NOTE]
    > * Vyberte **moje data zahrnují informace o směrování** pro použití dynamického směrování, kde data obsahují nezbytné informace o směrování, jak je vidět v komentářích [ukázkové aplikace](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Pokud jsou nastaveny statické i dynamické vlastnosti, přepíší dynamické vlastnosti statické. 
    > * Ingestují se jenom události zařazené do fronty po vytvoření datového připojení.

## <a name="generate-sample-data-for-testing"></a>Generovat ukázková data pro testování

Aplikace simulovaného zařízení se připojí ke koncovému bodu specifickému pro zařízení ve službě IoT Hub a odešle simulaci simulované teploty a vlhkosti.

1. Stáhněte si ukázkový C# projekt z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip a Extrahujte archiv zip.

1. V místním okně terminálu přejděte do kořenové složky ukázkového C# projektu. Pak přejděte do složky **IoT-hub\Quickstarts\simulated-Device** .

1. Otevřete soubor **SimulatedDevice.cs** v textovém editoru podle vašeho výběru.

    Nahraďte hodnotu proměnné `s_connectionString` připojovacím řetězcem zařízení a [Zaregistrujte zařízení do IoT Hub](#register-a-device-to-the-iot-hub). Potom uložte změny do souboru **SimulatedDevice.cs** .

1. V okně místního terminálu spuštěním následujících příkazů nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

1. V okně místního terminálu spusťte následující příkaz, který sestaví a spustí aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet run
    ```

    Následující snímek obrazovky ukazuje výstup jako aplikace simulovaného zařízení odesílá telemetrii do služby IoT Hub:

    ![Spuštění simulovaného zařízení](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Kontrola toku dat

Aplikace, která generuje data, teď může zobrazit tok dat ze služby IoT Hub do tabulky v clusteru.

1. V Azure Portal se v rámci centra IoT zobrazuje špička aktivity v době, kdy aplikace běží.

    ![IoT Hub metriky](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Chcete-li zkontrolovat, kolik zpráv bylo v databázi zatím provedeno, spusťte v testovací databázi následující dotaz.

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

1. V Azure Portal vyberte **skupiny prostředků** na zcela vlevo a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je levá nabídka sbalená, vyberte ![Tlačítko Rozbalit](media/ingest-data-event-hub/expand.png) a rozbalte ji.

   ![Vyberte skupinu prostředků, kterou chcete odstranit.](media/ingest-data-event-hub/delete-resources-select.png)

1. V části **test-Resource-Group**vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit (*test-hub-RG*), a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Dotazování na data v Azure Průzkumník dat](web-query-data.md)
