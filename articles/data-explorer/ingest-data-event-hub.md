---
title: 'Rychlý start: Ingestování dat z centra událostí do Azure Data Exploreru'
description: V tomto rychlém startu se dozvíte, jak ingestovat (načíst) data do Azure Data Exploreru z centra událostí.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: efaf551d134d339205d40966cb84f41b408559bd
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394174"
---
# <a name="quickstart-ingest-data-from-event-hub-into-azure-data-explorer"></a>Rychlý start: Ingestování dat z centra událostí do Azure Data Exploreru

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí ingestování (načítání) dat ze služby Event Hubs, platformy pro streamování velkých objemů dat a služby pro ingestování událostí. Dokáže zpracovat miliony událostí za sekundu prakticky v reálném čase. V tomto rychlém startu vytvoříte centrum událostí, připojíte se k němu z Azure Data Exploreru a podíváte se na tok dat v rámci systému.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Kromě předplatného Azure budete k dokončení tohoto rychlého startu potřebovat:

* [Testovací cluster a databázi](create-cluster-database-portal.md)

* [Ukázkovou aplikaci](https://github.com/Azure-Samples/event-hubs-dotnet-ingest), která generuje data

* [Visual Studio 2017 verze 15.3.2 nebo vyšší](https://www.visualstudio.com/vs/) ke spuštění ukázkové aplikace

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

V tomto rychlém startu vygenerujete ukázková data a odešlete je do centra událostí. Prvním krokem je vytvoření centra událostí. To provedete pomocí šablony Azure Resource Manageru (ARM) na webu Azure Portal.

1. Výběrem následujícího tlačítka spusťte nasazení.

    [![Nasazení do Azure](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Výběrem tlačítka **Deploy to Azure** (Nasadit do Azure) přejdete na web Azure Portal, kde vyplníte formulář nasazení.

    ![Nasazení do Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. Vyberte předplatné, ve kterém chcete centrum událostí vytvořit, a vytvořte skupinu prostředků s názvem *test-hub-rg*.

    ![Vytvoření skupiny prostředků](media/ingest-data-event-hub/create-resource-group.png)

1. Do formuláře zadejte následující informace.

    ![Formulář nasazení](media/ingest-data-event-hub/deployment-form.png)

    Pro všechna nastavení, která nejsou uvedená v následující tabulce, použijte výchozí hodnoty.

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svoje centrum událostí.|
    | Skupina prostředků | *test-hub-rg* | Vytvořte novou skupinu prostředků. |
    | Umístění | *Západní USA* | V tomto rychlém startu vyberte *USA – západ*. Pro produkční systém vyberte oblast, která nejlépe vyhovuje vašim potřebám.
    | Název oboru názvů | Jedinečný název oboru názvů | Zvolte jedinečný název, který identifikuje váš obor názvů. Například *mytestnamespace*. K názvu, který zadáte, bude připojen název domény *servicebus.windows.net*. Název může obsahovat pouze písmena, číslice a pomlčky. Musí začínat písmenem a končit písmenem nebo číslicí. Počet znaků musí být mezi 6 a 50.
    | Název centra událostí | *test-hub* | Centrum událostí se nachází v rámci oboru názvů, který poskytuje jedinečný kontejner oboru. Název centra událostí musí být v rámci oboru názvů jedinečný. |
    | Název skupiny uživatelů | *test-group* | Skupiny uživatelů umožňují, aby měla každá z aplikací samostatné zobrazení streamu událostí. |
    | | |

1. Výběrem možnosti **Purchase** (Zakoupit) potvrdíte vytvoření prostředků ve vašem předplatném.

1. Pokud chcete proces zřizování monitorovat, vyberte na panelu nástrojů **Oznámení** (ikona zvonku). Úspěšné nasazení může trvat několik minut, můžete teď ale přejít na další krok.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Data Exploreru

Teď v Azure Data Exploreru vytvoříte tabulku, do které bude služba Event Hubs odesílat data. Tabulku vytvoříte v clusteru a databázi, které jste zřídili v části **Požadavky**.

1. Na portálu Azure Portal vyberte v rámci svého clusteru možnost **Dotaz**.

    ![Dotaz – odkaz aplikace](media/ingest-data-event-hub/query-explorer-link.png)

1. Zkopírujte do okna následující příkaz a vyberte **Spustit**.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Spuštění vytvářecího dotazu](media/ingest-data-event-hub/run-create-query.png)

1. Zkopírujte do okna následující příkaz a vyberte **Spustit**.

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```
    Tento příkaz namapuje příchozí data JSON na názvy sloupců a datové typy použité při vytváření tabulky.

## <a name="connect-to-the-event-hub"></a>Připojení k centru událostí

Teď se z Azure Data Exploreru připojíte k centru událostí, aby se data tekoucí do centra událostí streamovala do testovací tabulky.

1. Výběrem možnosti **Oznámení** na panelu nástrojů ověřte úspěšné nasazení centra událostí.

1. V rámci clusteru, který jste vytvořili, vyberte **Databáze** a pak **TestDatabase**.

    ![Výběr testovací databáze](media/ingest-data-event-hub/select-test-database.png)

1. Vyberte **Ingestace dat** a pak **Přidat datové připojení**.

    ![Přijímání dat](media/ingest-data-event-hub/data-ingestion-create.png)

1. Do formuláře zadejte následující informace a vyberte **Vytvořit**.

    ![Připojení centra událostí](media/ingest-data-event-hub/event-hub-connection.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *test-hub-connection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | Obor názvů centra událostí | Jedinečný název oboru názvů | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | Centrum událostí | *test-hub* | Centrum událostí, které jste vytvořili |
    | Skupina uživatelů | *test-group* | Skupina uživatelů, kterou jste definovali v centrum událostí, které jste vytvořili |
    | Table | *TestTable* | Tabulka, kterou jste vytvořili v databázi **TestDatabase** |
    | Formát dat | *JSON* | Jsou podporované formáty JSON a CSV. |
    | Mapování sloupců | *TestMapping* | Mapování, které jste vytvořili v databázi **TestDatabase**. |

    Pro účely tohoto rychlého startu použijte *statické směrování* z centra událostí, kde zadáte název tabulky, formát souboru a mapování. Můžete také použít dynamické směrování, kde aplikace nastaví tyto vlastnosti.

## <a name="copy-the-connection-string"></a>Zkopírování připojovacího řetězce

Při spuštění aplikace pro vygenerování ukázkových dat potřebujete připojovací řetězec pro obor názvů centra událostí.

1. V rámci oboru názvů centra událostí, který jste vytvořili, vyberte **Zásady sdíleného přístupu** a pak vyberte **RootManageSharedAccessKey**.

    ![Zásady sdíleného přístupu](media/ingest-data-event-hub/shared-access-policies.png)

1. Zkopírujte **připojovací řetězec – primární klíč**.

    ![Připojovací řetězec](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generování ukázkových dat

Teď, když je Azure Data Explorer propojený s centrem událostí, si pomocí ukázkové aplikace, kterou jste si stáhli, vygenerujete data.

1. Otevřete řešení ukázkové aplikace v sadě Visual Studio.

1. V souboru *program.cs* aktualizujte konstantu `connectionString` na připojovací řetězec, který jste zkopírovali z oboru názvů centra událostí.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Sestavte a spusťte aplikaci. Aplikace odešle zprávy do centra událostí a každých deset sekund vypíše stav.

1. Po tom, co aplikace odešle několik zpráv, přejděte na další krok: kontrola toku dat do centra událostí a testovací tabulky.

## <a name="review-the-data-flow"></a>Kontrola toku dat

1. Na portálu Azure Portal v rámci vašeho centra událostí uvidíte při běhu aplikace špičkový nárůst aktivity.

    ![Graf centra událostí](media/ingest-data-event-hub/event-hub-graph.png)

1. Přejděte zpět do aplikace a po 99. zprávě ji zastavte.

1. Spuštěním následujícího dotazu v testovací databázi zkontrolujte, kolik zpráv se zatím dostalo do databáze.

    ```Kusto
    TestTable
    | count
    ```

1. Spuštěním následujícího dotazu si zobrazte obsah zpráv.

    ```Kusto
    TestTable
    ```

    Výsledek by měl vypadat následovně.

    ![Sada výsledků dotazu na zprávy](media/ingest-data-event-hub/message-result-set.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už centrum událostí nebudete chtít dál používat, vyčistěte **test-hub-rg**, abyste zabránili vzniku dalších nákladů.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků** a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je nabídka vlevo sbalená, výběrem ![tlačítko Rozbalit](media/ingest-data-event-hub/expand.png) ji rozbalte.

   ![Výběr skupiny prostředků k odstranění](media/ingest-data-event-hub/delete-resources-select.png)

1. Ve skupině prostředků **test-resource-group** vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit (*test-resource-group*), a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý start: Dotazování na data v Azure Data Exploreru](web-query-data.md)
