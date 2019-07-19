---
title: Ingestování dat z centra událostí do Azure Průzkumník dat
description: V tomto článku se dozvíte, jak pomocí centra událostí ingestovat (načítat) data do Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8e13e9f95fac8d2e651755ade126417acc6d97da
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311620"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Ingestování dat z centra událostí do Azure Průzkumník dat

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí ingestování (načítání) dat ze služby Event Hubs, platformy pro streamování velkých objemů dat a služby pro ingestování událostí. [Event Hubs](/azure/event-hubs/event-hubs-about) může zpracovávat miliony událostí za sekundu téměř v reálném čase. V tomto článku vytvoříte centrum událostí, připojíte se k němu z Azure Průzkumník dat a Prohlédněte si tok dat prostřednictvím systému.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* [Testovací cluster a databáze](create-cluster-database-portal.md).

* [Ukázková aplikace](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , která generuje data a odesílá je do centra událostí. Stáhněte si ukázkovou aplikaci do svého systému.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pro spuštění ukázkové aplikace.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

V tomto článku vygenerujete ukázková data a odešlete je do centra událostí. Prvním krokem je vytvoření centra událostí. To provedete pomocí šablony Azure Resource Manageru na webu Azure Portal.

1. Chcete-li vytvořit centrum událostí, použijte následující tlačítko ke spuštění nasazení. Klikněte pravým tlačítkem myši a vyberte **otevřít v novém okně**, abyste mohli postupovat podle zbývajících kroků v tomto článku.

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
    | Subscription | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svoje centrum událostí.|
    | Resource group | *test-hub-rg* | Vytvořte novou skupinu prostředků. |
    | Location | *Západní USA* | Pro tento článek vyberte *západní USA* . Pro produkční systém vyberte oblast, která nejlépe vyhovuje vašim potřebám. Vytvořte obor názvů centra událostí ve stejném umístění jako cluster Kusto pro nejlepší výkon (nejdůležitější pro obory názvů centra událostí s vysokou propustností).
    | Název oboru názvů | Jedinečný název oboru názvů | Zvolte jedinečný název, který identifikuje váš obor názvů. Například *mytestnamespace*. K názvu, který zadáte, bude připojen název domény *servicebus.windows.net*. Název může obsahovat pouze písmena, číslice a pomlčky. Musí začínat písmenem a končit písmenem nebo číslicí. Počet znaků musí být mezi 6 a 50.
    | Název centra událostí | *test-hub* | Centrum událostí se nachází v rámci oboru názvů, který poskytuje jedinečný kontejner oboru. Název centra událostí musí být v rámci oboru názvů jedinečný. |
    | Název skupiny uživatelů | *test-group* | Skupiny uživatelů umožňují, aby měla každá z aplikací samostatné zobrazení streamu událostí. |
    | | |

1. Výběrem možnosti **Purchase** (Zakoupit) potvrdíte vytvoření prostředků ve vašem předplatném.

1. Pokud chcete proces zřizování monitorovat, vyberte na panelu nástrojů **Oznámení**. Úspěšné nasazení může trvat několik minut, můžete teď ale přejít na další krok.

    ![Oznámení](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Data Exploreru

Teď v Azure Data Exploreru vytvoříte tabulku, do které bude služba Event Hubs odesílat data. Tabulku vytvoříte v clusteru a databázi, které jste zřídili v části **Požadavky**.

1. Na webu Azure Portal přejděte ke svému clusteru a vyberte **Dotaz**.

    ![Dotaz – odkaz aplikace](media/ingest-data-event-hub/query-explorer-link.png)

1. Zkopírujte následující příkaz do okna a vyberte **Spustit** pro vytvoření tabulky (pole s testovacími daty), která přijme ingestovaná data.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Spuštění vytvářecího dotazu](media/ingest-data-event-hub/run-create-query.png)

1. Zkopírujte do okna následující příkaz a vyberte možnost **Spustit** pro mapování příchozích dat JSON na názvy sloupců a datové typy tabulky (tabulka).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

## <a name="connect-to-the-event-hub"></a>Připojení k centru událostí

Teď se můžete z Azure Data Exploreru připojit k centru událostí. Po navázání připojení se budou data, která tečou do centra událostí, streamovat do testovací tabulky, kterou jste vytvořili v dřívější části tohoto článku.

1. Výběrem možnosti **Oznámení** na panelu nástrojů ověřte úspěšné nasazení centra událostí.

1. V rámci clusteru, který jste vytvořili, vyberte **Databáze** a pak **TestDatabase**.

    ![Výběr testovací databáze](media/ingest-data-event-hub/select-test-database.png)

1. Vyberte **přijímání dat** a **přidejte datové připojení**. Pak vyplňte formulář následujícími informacemi. Po dokončení vyberte **vytvořit** .

    ![Připojení centra událostí](media/ingest-data-event-hub/event-hub-connection.png)

    Zdroj dat:

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *test-hub-connection* | Název připojení, které chcete vytvořit v Azure Data Exploreru|
    | Obor názvů centra událostí | Jedinečný název oboru názvů | Název, který jste zvolili dříve a který identifikuje váš obor názvů |
    | Centrum událostí | *test-hub* | Centrum událostí, které jste vytvořili |
    | Skupina uživatelů | *test-group* | Skupina uživatelů, kterou jste definovali v centrum událostí, které jste vytvořili |
    | | |

    Cílová tabulka:

    K dispozici jsou dvě možnosti směrování přijatých dat: *statické* a *dynamické*. 
    V tomto článku použijete statické směrování, kde zadáte název tabulky, formát dat a mapování. Proto nechte **moje data zahrnovat informace o směrování** bez výběru.

     **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Table | *TestTable* | Tabulka, kterou jste vytvořili v databázi **TestDatabase** |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, VÍCEŘÁDKOVé JSON, PSV, SOH, SCSV, TSV a TXT. |
    | Mapování sloupců | *TestMapping* | Mapování, které jste vytvořili v databázi **TestDatabase** a které mapuje příchozí data JSON na názvy sloupců a datové typy tabulky **TestTable**. Vyžaduje se pro JSON, VÍCEŘÁDKOVé JSON nebo AVRO a volitelné pro jiné formáty.|
    | | |

    > [!NOTE]
    > Vyberte **moje data zahrnují informace o směrování** pro použití dynamického směrování, kde data obsahují nezbytné informace o směrování, jak je vidět v komentářích [ukázkové aplikace](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Pokud jsou nastaveny statické i dynamické vlastnosti, přepíší dynamické vlastnosti statické. 

## <a name="copy-the-connection-string"></a>Zkopírování připojovacího řetězce

Při spuštění [ukázkové aplikace](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) uvedené v části Požadavky potřebujete připojovací řetězec pro obor názvů centra událostí.

1. V rámci oboru názvů centra událostí, který jste vytvořili, vyberte **Zásady sdíleného přístupu** a pak vyberte **RootManageSharedAccessKey**.

    ![Zásady sdíleného přístupu](media/ingest-data-event-hub/shared-access-policies.png)

1. Zkopírujte **připojovací řetězec – primární klíč**. Tuto hodnotu vložíte v další části.

    ![Připojovací řetězec](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generování ukázkových dat

K vygenerování dat použijte [ukázkovou aplikaci](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , kterou jste stáhli.

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

Když teď aplikace generuje data, můžete zobrazit tok těchto dat z centra událostí do tabulky ve vašem clusteru.

1. Na portálu Azure Portal v rámci vašeho centra událostí uvidíte při běhu aplikace špičkový nárůst aktivity.

    ![Graf centra událostí](media/ingest-data-event-hub/event-hub-graph.png)

1. Pokud chcete zkontrolovat, kolik zpráv se zatím dostalo do databáze, spusťte v testovací databázi následující dotaz.

    ```Kusto
    TestTable
    | count
    ```

1. Chcete-li zobrazit obsah zpráv, spusťte následující dotaz:

    ```Kusto
    TestTable
    ```

    Sada výsledků by měla vypadat takto:

    ![Sada výsledků dotazu na zprávy](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * Azure Průzkumník dat má agregační (dávku) zásadu pro příjem dat, která je určená k optimalizaci procesu ingestování. Tato zásada je ve výchozím nastavení nakonfigurovaná na 5 minut nebo 500 MB dat, takže se může vyskytnout latence. Podívejte se na téma [zásady dávkování](/azure/kusto/concepts/batchingpolicy) pro možnosti agregace. 
    > * Ingestování centra událostí zahrnuje dobu odezvy centra událostí o velikosti 10 sekund nebo 1 MB. 
    > * Nakonfigurujte tabulku tak, aby podporovala streamování, a odeberte prodlevu v době odezvy. Viz [zásada streamování](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už centrum událostí nebudete chtít dál používat, vyčistěte **test-hub-rg**, abyste zabránili vzniku dalších nákladů.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků** a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je nabídka vlevo sbalená, výběrem ![tlačítko Rozbalit](media/ingest-data-event-hub/expand.png) ji rozbalte.

   ![Výběr skupiny prostředků k odstranění](media/ingest-data-event-hub/delete-resources-select.png)

1. Ve skupině prostředků **test-resource-group** vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit (*test-resource-group*), a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Dotazování na data v Azure Průzkumník dat](web-query-data.md)
