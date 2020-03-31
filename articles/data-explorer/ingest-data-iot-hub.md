---
title: Ingestování dat z IoT Hubu do Azure Data Exploreru
description: V tomto článku se dozvíte, jak ingestovat (načíst) data do Průzkumníka dat Azure z IoT Hubu.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 78455c90bab694b77a5e4a56d0b40518867d8d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188369"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Ingestování dat z IoT Hubu do Azure Data Exploreru 

> [!div class="op_single_selector"]
> * [Portál](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Šablona Azure Resource Manageru](data-connection-iot-hub-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí ingestování (načítání dat) z IoT Hub, platformy pro streamování velkých objemů dat a služby ingestování IoT.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Vytvořte [testovací cluster a databázi](create-cluster-database-portal.md) s názvem databáze *testdb*.
* [Ukázková aplikace](https://github.com/Azure-Samples/azure-iot-samples-csharp) a dokumentace pro simulaci zařízení.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pro spuštění ukázkové aplikace.

## <a name="create-an-iot-hub"></a>Vytvoření centra pro vytváření služeb pro více a více

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registrace zařízení do centra IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Data Exploreru

Teď v Azure Data Exploreru vytvoříte tabulku, do které budou služby IoT Hubodesílat data. Tabulku vytvoříte v clusteru a databázi zřízené v [**části Požadavky**](#prerequisites).

1. Na webu Azure Portal přejděte do svého clusteru a vyberte **Dotaz**.

    ![Dotaz ADX na portálu](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Zkopírujte následující příkaz do okna a vyberte **Spustit,** chcete-li vytvořit tabulku (TestTable), která obdrží příjem dat.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Spuštění vytvářecího dotazu](media/ingest-data-iot-hub/run-create-query.png)

1. Zkopírujte následující příkaz do okna a vyberte **Spustit,** chcete-li namapovat příchozí data JSON na názvy sloupců a datové typy tabulky (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Připojení tabulky Průzkumníka dat Azure k centru IoT hub

Teď se připojíte k IoT Hubu z Azure Data Exploreru. Po dokončení tohoto připojení data, která proudí do datových proudů iot hub do [cílové tabulky, kterou jste vytvořili](#create-a-target-table-in-azure-data-explorer).

1. Vyberte **Oznámení** na panelu nástrojů a ověřte, zda bylo nasazení služby IoT Hub úspěšné.

1. V rámci vytvořeného clusteru vyberte **databáze a** vyberte databázi, kterou jste **vytvořili testdb**.
    
    ![Výběr testovací databáze](media/ingest-data-iot-hub/select-database.png)

1. Vyberte **Položku Přijímdat** data a **Přidat datové připojení**. Poté vyplňte formulář s následujícími informacemi. Až budete hotovi, vyberte **Vytvořit.**

    ![Připojení k centru IoT](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Zdroj dat**:

    **Nastavení** | **Popis pole**
    |---|---|
    | Název datového připojení | Název připojení, které chcete vytvořit v Průzkumníku dat Azure
    | IoT Hub | Název ioT hubu |
    | Zásady sdíleného přístupu | Název zásady sdíleného přístupu. Musí mít oprávnění ke čtení. |
    | Skupina uživatelů |  Skupina spotřebitelů definovaná ve integrovaném koncovém bodu ioT hubu |
    | Vlastnosti systému událostí | [Vlastnosti systému událostí služby IoT Hub](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). Při přidávání vlastností systému [vytvořte](/azure/kusto/management/create-table-command) nebo [aktualizujte](/azure/kusto/management/alter-table-command) schéma tabulky a [mapování](/azure/kusto/management/mappings) tak, aby zahrnovalo vybrané vlastnosti. | | | 

    > [!NOTE]
    > V případě [ručního převzetí služeb při selhání](/azure/iot-hub/iot-hub-ha-dr#manual-failover)je nutné datové připojení znovu vytvořit.

    **Cílová tabulka**:

    Existují dvě možnosti směrování ingestovaných dat: *statické* a *dynamické*. 
    Pro tento článek použijete statické směrování, kde zadáte název tabulky, formát dat a mapování. Proto ponechat **moje data včetně směrování informace** nevybrané.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Table | *TestTable* | Tabulka, kterou jste vytvořili v **testdb**. |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE a TXT. |
    | Mapování sloupců | *TestMapping* | [Mapování,](/azure/kusto/management/mappings) které jste vytvořili v **testdb**, který mapuje příchozí json data na názvy sloupců a datové typy **testdb**. Vyžadováno pro JSON, MULTILINE JSON a AVRO a volitelné pro jiné formáty.|
    | | |

    > [!NOTE]
    > * Vyberte **Moje data včetně informací o směrování,** chcete-li použít dynamické směrování, kde vaše data obsahují potřebné informace o směrování, jak je vidět v ukázkových komentářích [aplikace.](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) Pokud jsou nastaveny statické i dynamické vlastnosti, dynamické vlastnosti přepíší statické vlastnosti. 
    > * Po vytvoření datového připojení jsou ingestovány pouze události zařazené do fronty.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Generovat ukázková data pro testování

Aplikace simulovaného zařízení se připojuje ke koncovému bodu vašeho centra IoT pro konkrétní zařízení a odesílá simulovaná telemetrická data o teplotě a vlhkosti vzduchu.

1. Stáhněte si ukázkový projekt C# z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip a extrahujte archiv ZIP.

1. V okně místního terminálu přejděte do kořenové složky ukázkového projektu jazyka C#. Pak přejděte do složky **iot-hub\Quickstarts\simulated-device**.

1. V libovolném textovém editoru otevřete soubor **SimulatedDevice.cs**.

    Nahraďte hodnotu `s_connectionString` proměnné připojovacím řetězcem zařízení z registrace zařízení do [služby IoT Hub](#register-a-device-to-the-iot-hub). Změny pak uložte do souboru **SimulatedDevice.cs**.

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

Díky datům generujícím aplikaci teď uvidíte tok dat z centra IoT hub do tabulky ve vašem clusteru.

1. Na webu Azure Portal v rámci služby IoT hub uvidíte nárůst aktivity, když je aplikace spuštěná.

    ![Metriky IoT Hubu](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Pokud chcete zkontrolovat, kolik zpráv se zatím dostalo do databáze, spusťte v testovací databázi následující dotaz.

    ```Kusto
    TestTable
    | count
    ```

1. Chcete-li zobrazit obsah zpráv, spusťte následující dotaz:

    ```Kusto
    TestTable
    ```

    Sada výsledků:
    
    ![Zobrazit výsledky ingtovaných dat](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer má zásady agregace (dávkování) pro ingestování dat, určené k optimalizaci procesu ingestování. Zásada je ve výchozím nastavení nakonfigurována na 5 minut nebo 500 MB dat, takže může docházet k latenci. Viz [zásady dávkování](/azure/kusto/concepts/batchingpolicy) pro možnosti agregace. 
    > * Nakonfigurujte tabulku tak, aby podporovala streamování a odstranila prodlevu v době odezvy. Viz [zásady streamování](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nemáte v plánu znovu použít ioT hub, vyčistěte **test-hub-rg**, abyste se vyhnuli vzniku nákladů.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků** a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je nabídka vlevo sbalená, výběrem ![tlačítko Rozbalit](media/ingest-data-event-hub/expand.png) ji rozbalte.

   ![Výběr skupiny prostředků k odstranění](media/ingest-data-event-hub/delete-resources-select.png)

1. Ve skupině prostředků **test-resource-group** vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit (*test-resource-group*), a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Dotazovat se na data v Průzkumníku dat Azure](web-query-data.md)
