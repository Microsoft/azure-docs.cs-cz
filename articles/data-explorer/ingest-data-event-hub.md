---
title: Ingestování dat z centra událostí do Azure Průzkumník dat
description: V tomto článku se dozvíte, jak pomocí centra událostí ingestovat (načítat) data do Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: a83e2163c9aa970932f2eea8e2e04a715107ac7f
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950252"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Ingestování dat z centra událostí do Azure Průzkumník dat

Azure Průzkumník dat je rychlá a vysoce škálovatelná služba průzkumu dat pro data protokolů a telemetrie. Azure Průzkumník dat nabízí ingestování (načítání dat) od Event Hubs, platformy pro streamování velkých objemů dat a služby ingestování událostí. [Event Hubs](/azure/event-hubs/event-hubs-about) může zpracovávat miliony událostí za sekundu téměř v reálném čase. V tomto článku vytvoříte centrum událostí, připojíte se k němu z Azure Průzkumník dat a Prohlédněte si tok dat prostřednictvím systému.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* [Testovací cluster a databáze](create-cluster-database-portal.md).

* [Ukázková aplikace](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , která generuje data a odesílá je do centra událostí. Stáhněte si ukázkovou aplikaci do svého systému.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pro spuštění ukázkové aplikace.

## <a name="sign-in-to-the-azure-portal"></a>Přihlaste se k Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

V tomto článku vygenerujete ukázková data a odešlete je do centra událostí. Prvním krokem je vytvoření centra událostí. Můžete to provést pomocí šablony Azure Resource Manager v Azure Portal.

1. Chcete-li vytvořit centrum událostí, použijte následující tlačítko ke spuštění nasazení. Klikněte pravým tlačítkem myši a vyberte **otevřít v novém okně**, abyste mohli postupovat podle zbývajících kroků v tomto článku.

    [@no__t – 1Deploy do Azure](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Tlačítkem **nasadit do Azure** přejdete na Azure Portal a vyplníte formulář nasazení.

    ![Nasazení do Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. Vyberte předplatné, ve kterém chcete vytvořit centrum událostí, a vytvořte skupinu prostředků s názvem *test-hub-RG*.

    ![Vytvoření skupiny prostředků](media/ingest-data-event-hub/create-resource-group.png)

1. Vyplňte formulář s následujícími informacemi.

    ![Formulář nasazení](media/ingest-data-event-hub/deployment-form.png)

    Použijte výchozí hodnoty pro všechna nastavení, která nejsou uvedená v následující tabulce.

    **Nastavením** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Formě | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro centrum událostí.|
    | Skupina prostředků | *test – centrum – RG* | Vytvořte novou skupinu prostředků. |
    | Umístění | *Západní USA* | Pro tento článek vyberte *západní USA* . V případě produkčního systému vyberte oblast, která nejlépe vyhovuje vašim potřebám. Vytvořte obor názvů centra událostí ve stejném umístění jako cluster Kusto pro nejlepší výkon (nejdůležitější pro obory názvů centra událostí s vysokou propustností).
    | Název oboru názvů | Jedinečný název oboru názvů | Vyberte jedinečný název, který identifikuje váš obor názvů. Například *mytestnamespace*. Název domény *ServiceBus.Windows.NET* je připojen k názvu, který zadáte. Název může obsahovat jenom písmena, číslice a spojovníky. Název musí začínat písmenem a musí končit písmenem nebo číslicí. Hodnota musí mít délku 6 až 50 znaků.
    | Název centra událostí | *Centrum testování* | Centrum událostí je umístěno pod oborem názvů, který poskytuje jedinečný obor kontejneru. Název centra událostí musí být v rámci oboru názvů jedinečný. |
    | Název skupiny uživatelů | *Skupina testů* | Skupiny uživatelů umožňují, aby každý z nich měl samostatné zobrazení datového proudu událostí. |
    | | |

1. Vyberte **koupit**a potvrďte, že ve svém předplatném vytváříte prostředky.

1. Na panelu nástrojů vyberte **oznámení** pro monitorování procesu zřizování. Úspěšné dokončení nasazení může trvat několik minut, ale teď můžete přejít k dalšímu kroku.

    ![Oznámení](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Vytvoření cílové tabulky v Azure Průzkumník dat

Nyní vytvoříte tabulku ve službě Azure Průzkumník dat, do které Event Hubs budou posílat data. Vytvoříte tabulku v clusteru a databázi zřízené v části **požadavky**.

1. V Azure Portal přejděte na svůj cluster a pak vyberte **dotaz**.

    ![Odkaz na dotaz aplikace](media/ingest-data-event-hub/query-explorer-link.png)

1. Zkopírujte následující příkaz do okna a vyberte **Spustit** pro vytvoření tabulky (pole s testovacími daty), která přijme ingestovaná data.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Spustit dotaz Create](media/ingest-data-event-hub/run-create-query.png)

1. Zkopírujte do okna následující příkaz a vyberte možnost **Spustit** pro mapování příchozích dat JSON na názvy sloupců a datové typy tabulky (tabulka).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

## <a name="connect-to-the-event-hub"></a>Připojení k centru událostí

Nyní se připojíte k centru událostí z Azure Průzkumník dat. Když je toto připojení na místě, data, která se do centra událostí přecházejí do datových proudů, která jste vytvořili dříve v tomto článku, se streamují do služby.

1. Na panelu nástrojů vyberte **oznámení** , abyste ověřili, že nasazení centra událostí bylo úspěšné.

1. V clusteru, který jste vytvořili, vyberte **databáze** a pak **TestDatabase**.

    ![Vybrat testovací databázi](media/ingest-data-event-hub/select-test-database.png)

1. Vyberte **přijímání dat** a **přidejte datové připojení**. Pak vyplňte formulář následujícími informacemi. Po dokončení vyberte **vytvořit** .

    ![Připojení centra událostí](media/ingest-data-event-hub/event-hub-connection.png)

    Zdroj dat:

    **Nastavením** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název datového připojení | *test-centrum – připojení* | Název připojení, které chcete vytvořit v Azure Průzkumník dat.|
    | Obor názvů centra událostí | Jedinečný název oboru názvů | Název, který jste zvolili dříve, který identifikuje váš obor názvů. |
    | Centrum událostí | *Centrum testování* | Centrum událostí, které jste vytvořili. |
    | Skupina uživatelů | *Skupina testů* | Skupina uživatelů definovaná v centru událostí, které jste vytvořili. |
    | Vlastnosti systému událostí | Vyberte příslušné vlastnosti. | [Vlastnosti systému centra událostí](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations) |
    | | |

    Cílová tabulka:

    K dispozici jsou dvě možnosti směrování přijatých dat: *statické* a *dynamické*. 
    V tomto článku použijete statické směrování, kde zadáte název tabulky, formát dat a mapování. Proto nechte **moje data zahrnovat informace o směrování** bez výběru.

     **Nastavením** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Tabulka | *Testovací navýšení* | Tabulka, kterou jste vytvořili v **TestDatabase**. |
    | Formát dat | *JSON* | Podporované formáty jsou Avro, CSV, JSON, VÍCEŘÁDKOVé JSON, PSV, SOHSV, SCSV, TSV, TSVE a TXT. Podporované možnosti komprese: GZip |
    | Mapování sloupce | *TestMapping* | Mapování, které jste vytvořili v **TestDatabase**, které mapuje příchozí data JSON na názvy sloupců a datové typy **testovacího**typu. Vyžaduje se pro JSON, VÍCEŘÁDKOVé JSON nebo AVRO a volitelné pro jiné formáty.|
    | | |

    > [!NOTE]
    > * Vyberte **moje data zahrnují informace o směrování** pro použití dynamického směrování, kde data obsahují nezbytné informace o směrování, jak je vidět v komentářích [ukázkové aplikace](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) . Pokud jsou nastaveny statické i dynamické vlastnosti, přepíší dynamické vlastnosti statické. 
    > * Ingestují se jenom události zařazené do fronty po vytvoření datového připojení.

## <a name="copy-the-connection-string"></a>Zkopírování připojovacího řetězce

Když spustíte [ukázkovou aplikaci](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) uvedenou v části požadavky, budete potřebovat připojovací řetězec pro obor názvů centra událostí.

1. V části obor názvů centra událostí, který jste vytvořili, vyberte **zásady sdíleného přístupu**a pak **RootManageSharedAccessKey**.

    ![Zásady sdíleného přístupu](media/ingest-data-event-hub/shared-access-policies.png)

1. Zkopírování **připojovacího řetězce – primární klíč** Vložíte ho do další části.

    ![Připojovací řetězec](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Generování ukázkových dat

K vygenerování dat použijte [ukázkovou aplikaci](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , kterou jste stáhli.

1. Otevřete ukázkové řešení aplikace v aplikaci Visual Studio.

1. V souboru *program.cs* aktualizujte konstantu `connectionString` na připojovací řetězec, který jste zkopírovali z oboru názvů centra událostí.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Sestavte a spusťte aplikaci. Aplikace pošle zprávy do centra událostí a vytiskne stav každých deset sekund.

1. Až aplikace pošle několik zpráv, přejděte k dalšímu kroku: kontrola toku dat do centra událostí a v testovací tabulce.

## <a name="review-the-data-flow"></a>Kontrola toku dat

V aplikaci, která generuje data, teď můžete vidět tok těchto dat z centra událostí do tabulky v clusteru.

1. V Azure Portal se v centru událostí zobrazuje špička aktivity, zatímco aplikace běží.

    ![Graf centra událostí](media/ingest-data-event-hub/event-hub-graph.png)

1. Chcete-li zkontrolovat, kolik zpráv bylo v databázi zatím provedeno, spusťte v testovací databázi následující dotaz.

    ```Kusto
    TestTable
    | count
    ```

1. Chcete-li zobrazit obsah zpráv, spusťte následující dotaz:

    ```Kusto
    TestTable
    ```

    Sada výsledků by měla vypadat takto:

    ![Sada výsledků zpráv](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * Azure Průzkumník dat má agregační (dávku) zásadu pro příjem dat, která je určená k optimalizaci procesu ingestování. Tato zásada je ve výchozím nastavení nakonfigurovaná na 5 minut nebo 500 MB dat, takže se může vyskytnout latence. Podívejte se na téma [zásady dávkování](/azure/kusto/concepts/batchingpolicy) pro možnosti agregace. 
    > * Ingestování centra událostí zahrnuje dobu odezvy centra událostí o velikosti 10 sekund nebo 1 MB. 
    > * Nakonfigurujte tabulku tak, aby podporovala streamování, a odeberte prodlevu v době odezvy. Viz [zásada streamování](/azure/kusto/concepts/streamingingestionpolicy). 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neplánujete znovu použít centrum událostí, vyčistěte **test-hub-RG**, abyste se vyhnuli nákladům.

1. V Azure Portal vyberte **skupiny prostředků** na zcela vlevo a pak vyberte skupinu prostředků, kterou jste vytvořili.  

    Pokud je levá nabídka sbalená, vyberte ![Tlačítko Rozbalit](media/ingest-data-event-hub/expand.png) a rozbalte ji.

   ![Vyberte skupinu prostředků, kterou chcete odstranit.](media/ingest-data-event-hub/delete-resources-select.png)

1. V části **test-Resource-Group**vyberte **Odstranit skupinu prostředků**.

1. V novém okně zadejte název skupiny prostředků, kterou chcete odstranit (*test-hub-RG*), a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* [Dotazování na data v Azure Průzkumník dat](web-query-data.md)
