---
title: Použití Správce dat blockchainu k aktualizaci Azure Cosmos DB – Azure Blockchain Service
description: Použití Správce blockchainových dat pro službu Azure Blockchain Service k odesílání dat blockchainu do Azure Cosmos DB
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 483a5246274f63549dfb2914361ede6aa001e02e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79533177"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Kurz: Odeslání dat do Azure Cosmos DB pomocí Správce dat blockchainu

V tomto kurzu používáte Blockchain Data Manager pro službu Azure Blockchain service k záznamu dat transakcí blockchainu v Azure Cosmos DB. Blockchain Data Manager zachycuje, transformuje a doručuje data z blockchainové knihy do témat Azure Event Grid. Z Azure Event Grid použijete konektor aplikace Logiky Azure k vytváření dokumentů v databázi Azure Cosmos DB. Po dokončení kurzu můžete prozkoumat data transakcí blockchain v Azure Cosmos DB Data Explorer.

[![Detail transakce blockchainu](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření instance Správce dat blockchainu
> * Přidání blockchainové aplikace pro dekódování vlastností a událostí transakcí
> * Vytvoření účtu a databáze Azure Cosmos DB pro ukládání dat transakcí
> * Vytvoření aplikace Logiky Azure pro připojení tématu mřížky událostí Azure k Azure Cosmos DB
> * Odeslání transakce do hlavní knihy blockchainu
> * Zobrazení dekódovaných dat transakcí v Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [úvodní příručka: Vytvoření člena blockchainu pomocí portálu Azure](create-member.md) nebo [úvodního startu: Vytvoření člena blockchainu služby Azure Blockchain pomocí Azure CLI](create-member-cli.md)
* Kompletní [úvodní příručka: Pomocí kódu Visual Studia se můžete připojit k síti konsorcia Azure Blockchain Service](connect-vscode.md). Rychlý start vás provede instalací [Azure Blockchain Development Kit pro ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) a nastavením vývojového prostředí blockchainu.
* Kompletní [kurz: Pomocí kódu Visual Studio můžete vytvářet, vytvářet a nasazovat inteligentní kontrakty](send-transaction.md). Kurz prochází vytvoření ukázkové inteligentní smlouvy.
* Vytvoření [tématu mřížky událostí](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informace o [obslužných rutinách událostí v Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Vytvořit instanci

Instance Správce dat Blockchain propojuje a monitoruje transakční uzel služby Azure Blockchain Service. Instance zachytí všechna nezpracovaná data bloku a nezpracovaných transakcí z uzlu transakce. Odchozí připojení odesílá data blockchainu do Azure Event Grid. Při vytváření instance nakonfigurujete jedno odchozí připojení.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na člena služby Azure Blockchain Service, který jste vytvořili v požadovaném [úvodním panelu: Vytvořte člena blockchainu pomocí portálu Azure](create-member.md). Vyberte **Správce dat blockchainu**.
1. Vyberte **Přidat**.

    ![Přidat Správce dat blockchainu](./media/data-manager-cosmosdb/add-instance.png)

    Zadejte následující podrobnosti:

    Nastavení | Příklad | Popis
    --------|---------|------------
    Name (Název) | mywatcher | Zadejte jedinečný název připojeného Správce dat blockchainu.
    Uzel transakce | myblockchainmember | Zvolte výchozí uzel transakce člena služby Azure Blockchain Service, který jste vytvořili v předpokladu.
    Název připojení | cosmosdb | Zadejte jedinečný název odchozího připojení, kam jsou odesílána data transakcí blockchainu.
    Koncový bod mřížky událostí | myTopic | Vyberte téma mřížky událostí, které jste vytvořili v předpokladu. Poznámka: Instance Správce dat Blockchain a téma mřížky událostí musí být ve stejném předplatném.

1. Vyberte **OK**.

    Vytvoření instance Správce dat blockchainu trvá méně než minutu. Po nasazení instance se automaticky spustí. Spuštěná instance Správce dat Blockchain zachycuje události blockchainu z transakčního uzlu a odesílá data do sítě událostí.

## <a name="add-application"></a>Přidání aplikace

Přidejte aplikaci **helloblockchain** blockchain tak, aby Blockchain Data Manager dekóduje událost a stav vlastností. Blockchain Data Manager vyžaduje inteligentní kontrakt ABI a bytecode soubor přidat aplikaci.

### <a name="get-contract-abi-and-bytecode"></a>Získat smlouvu ABI a bytecode

Smlouva ABI definuje inteligentní smluvní rozhraní. Popisuje, jak pracovat s inteligentní smlouvy. Pomocí azure [blockchain development kit pro rozšíření Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) můžete zkopírovat smlouvu ABI do schránky.

1. V podokně průzkumníka kódu visual studia rozbalte složku **sestavení/kontraktů** projektu **Helloblockchain** Solidity, který jste vytvořili v [požadovaném kurzu: Pomocí kódu Visual Studia vytvořte, sestavujte a nasaďte inteligentní kontrakty](send-transaction.md).
1. Klikněte pravým tlačítkem myši na soubor JSON metadat smlouvy. Název souboru je inteligentní název smlouvy následovaný příponou **JSON.**
1. Vyberte **možnost Kopírovat smlouvu ABI**.

    ![Podokno Kódu sady Visual Studio s výběrem ABI smlouvy copy](./media/data-manager-cosmosdb/abi-devkit.png)

    Smlouva ABI je zkopírována do schránky.

1. Uložte pole **abi** jako soubor JSON. Například *abi.json*. Soubor se použije v pozdějším kroku.

Blockchain Data Manager vyžaduje nasazený bytecode pro inteligentní kontrakt. Nasazený bajtkód se liší od inteligentního kódu bytecode smlouvy. Rozšíření vývojové sady Azure blockchain slouží ke kopírování bajtového kódu do schránky.

1. V podokně průzkumníkkódu sady Visual Studio rozbalte složku **sestavení/smluv** projektu Solidity.
1. Klikněte pravým tlačítkem myši na soubor JSON metadat smlouvy. Název souboru je inteligentní název smlouvy následovaný příponou **JSON.**
1. Vyberte **možnost Kopírovat bajtový kód transakce**.

    ![Podokno Kódu sady Visual Studio s výběrem kopírovat transakční bajtokód](./media/data-manager-cosmosdb/bytecode-devkit.png)

    Bajtkód se zkopíruje do schránky.

1. Uložte hodnotu **bajtového kódu** jako soubor JSON. Například *bytecode.json*. Soubor se použije v pozdějším kroku.

Následující příklad ukazuje *abi.json* a *bytecode.json* soubory otevřené v editoru kódu VS. Soubory by měly vypadat podobně.

![Příklad souborů abi.json a bytecode.json](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Vytvoření adresy URL smlouvy ABI a bytecode

Blockchain Data Manager vyžaduje, aby soubory abi a bytecode smlouvy byly přístupné pomocí adresy URL při přidávání aplikace. Účet Azure Storage můžete použít k poskytnutí soukromě přístupné adresy URL.

#### <a name="create-storage-account"></a>Vytvoření účtu úložiště

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Nahrát soubory smluv

1. Vytvořte nový kontejner pro účet úložiště. Vyberte **kontejnery > kontejneru**.

    ![Vytvoření kontejneru účtu úložiště](./media/data-manager-cosmosdb/create-container.png)

    | Nastavení | Popis |
    |---------|-------------|
    | Name (Název)  | Zadejte název kontejneru. Například *smartcontract* |
    | Úroveň veřejného přístupu | Zvolte *Soukromé (bez anonymního přístupu)* |

1. Kliknutím na **OK** kontejner vytvoříte.
1. Vyberte kontejner a pak vyberte **Nahrát**.
1. Zvolte oba soubory JSON, které jste vytvořili v části [Získat aBI smlouvy a bytecode.](#get-contract-abi-and-bytecode)

    ![Nahrát objekt blob](./media/data-manager-cosmosdb/upload-blobs.png)

    Vyberte **Nahrát**.

#### <a name="generate-url"></a>Generovat adresu URL

Pro každý objekt blob vygenerujte sdílený přístupový podpis.

1. Vyberte objekt blob ABI JSON.
1. Vybrat **generovat SAS**
1. Nastavte požadovanou expiraci podpisu přístupu a pak vyberte **Generovat token SAS objektu blob a adresu URL**.

    ![Generovat token SAS](./media/data-manager-cosmosdb/generate-sas.png)

1. Zkopírujte **adresu URL objektu Blob SAS** a uložte ji do další části.
1. Opakujte kroky [generování adresy URL](#generate-url) pro objekt blob Bajtkódu JSON.

### <a name="add-helloblockchain-application-to-instance"></a>Přidat aplikaci helloblockchain do instance

1. Vyberte instanci Správce dat Blockchain ze seznamu instancí.
1. Vyberte **blockchainové aplikace**.
1. Vyberte **Přidat**.

    ![Přidání blockchainové aplikace](./media/data-manager-cosmosdb/add-application.png)

    Zadejte název blockchainové aplikace a inteligentní smlouvy ABI a adresy URL bajtového kódu.

    Nastavení | Popis
    --------|------------
    Name (Název) | Zadejte jedinečný název blockchainové aplikace ke sledování.
    Smlouva ABI | Cesta url k souboru ABI smlouvy. Další informace naleznete v [tématu Create contract ABI and bytecode URL](#create-contract-abi-and-bytecode-url).
    Kód bytecode smlouvy | Cesta URL k souboru bajtového kódu. Další informace naleznete v [tématu Create contract ABI and bytecode URL](#create-contract-abi-and-bytecode-url).

1. Vyberte **OK**.

    Jakmile je aplikace vytvořena, aplikace se zobrazí v seznamu blockchainových aplikací.

    ![Seznam aplikací blockchain](./media/data-manager-cosmosdb/artifact-list.png)

Účet Azure Storage můžete odstranit nebo ho použít ke konfiguraci dalších blockchainových aplikací. Pokud chcete odstranit účet Azure Storage, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený účet úložiště a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

## <a name="create-azure-cosmos-db"></a>Vytvoření služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Přidání databáze a kontejneru

Průzkumníka dat na webu Azure Portal můžete použít k vytvoření databáze a kontejneru.

1. V levém navigačním panelu na stránce účtu Azure Cosmos DB vyberte **Průzkumník dat** a pak vyberte **Nový kontejner**.
1. V podokně **Přidat kontejner** zadejte nastavení nového kontejneru.

    ![Přidání nastavení kontejneru](./media/data-manager-cosmosdb/add-container.png)

    | Nastavení | Popis
    |---------|-------------|
    | ID databáze | Jako název nové databáze zadejte **data blockchainu.** |
    | Propustnost | Ponechte propustnost na **400** jednotek požadavku za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.|
    | ID kontejneru | Jako název nového kontejneru zadejte **Zprávy.** |
    | Klíč oddílu | Jako klíč oddílu použijte **parametr /MessageType.** |

1. Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner, který jste vytvořili.

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

Azure Logic Apps vám pomůže naplánovat a automatizovat obchodní procesy a pracovní postupy, když potřebujete integrovat systémy a služby. Pomocí aplikace logiky můžete připojit Event Grid k Azure Cosmos DB.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Integrace** > **Aplikace logiky**.
1. Zadejte podrobnosti o tom, kde vytvořit aplikaci logiky. Až budete hotovi, vyberte **Vytvořit**.

    Další informace o vytváření aplikací logiky najdete v [tématu Vytváření automatizovaných pracovních postupů pomocí Aplikací logiky Azure](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Po nasazení aplikace Azure vyberte prostředek aplikace logiky.
1. V Návrháři aplikací **logiky**vyberte v části Šablony **možnost Blank Logic App**.

### <a name="add-event-grid-trigger"></a>Aktivační událost Přidat mřížku událostí

Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu. Pomocí aktivační události Azure Event Grid odešlete data transakcí blockchainu z Event Grid do Cosmos DB.

1. V Návrháři aplikací logiky vyhledejte a vyberte konektor **Azure Event Grid.**
1. Na kartě **Aktivační události** vyberte **Při výskytu události prostředku .**
1. Vytvořte připojení rozhraní API k tématu mřížky událostí.

    ![Nastavení aktivační události mřížky událostí](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Nastavení | Popis
    |---------|-------------|
    | Předplatné | Zvolte předplatné, které obsahuje téma mřížky událostí. |
    | Typ prostředku | Zvolte **Microsoft.EventGrid.Topics**. |
    | Název prostředku | Zvolte název tématu mřížky událostí, ve kterém správce dat blockchainu odesílá datové zprávy o transakcích. |

### <a name="add-cosmos-db-action"></a>Přidat akci Cosmos DB

Přidejte akci pro vytvoření dokumentu v Cosmos DB pro každou transakci. Pomocí typu zprávy transakce jako klíč oddílu zařazujte zprávy do kategorií.

1. Vyberte **Nový krok**.
1. V **poli Zvolte akci**vyhledejte azure **cosmos db**.
1. Zvolte **Azure Cosmos DB > akce > vytvořit nebo aktualizovat dokument**.
1. Vytvořte připojení rozhraní API k databázi Cosmos DB.

    ![Nastavení připojení Cosmos DB](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Nastavení | Popis
    |---------|-------------|
    | Název připojení | Zvolte předplatné, které obsahuje téma mřížky událostí. |
    | Účet DocumentDB | Zvolte účet DocumentDB, který jste vytvořili v části [Vytvořit účet Azure Cosmos DB.](#create-azure-cosmos-db) |

1. Zadejte **ID databáze** a **ID kolekce** pro azure cosmos DB, které jste vytvořili dříve v části Přidat [databázi a kontejner.](#add-a-database-and-container)

1. Vyberte nastavení **Dokument.** V rozbalovacím okně *Přidat dynamický obsah* vyberte **Výraz** a zkopírujte a vložte následující výraz:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    Výraz získá datovou část zprávy a nastaví ID na hodnotu časového razítka.

1. Vyberte **Přidat nový parametr** a zvolte **Hodnotu klíče oddílu**.
1. Nastavte **hodnotu klíče oddílu** na . `"@{triggerBody()['data']['MessageType']}"` Hodnota musí být obklopena dvojitými uvozovkami.

    ![Návrhář aplikací logiky s nastavením Cosmos DB](./media/data-manager-cosmosdb/create-action.png)

    Hodnota nastaví klíč oddílu na typ zprávy transakce.

1. Vyberte **Uložit**.

Aplikace logiky monitoruje téma mřížky událostí. Když je z Správce dat Blockchain odeslána nová zpráva o transakci, aplikace logiky vytvoří dokument v Cosmos DB.

## <a name="send-a-transaction"></a>Odeslání transakce

Dále odešlete transakci do hlavní knihy blockchainu a otestujte, co jste vytvořili. Použijte funkci **SendRequest** kontraktu **HelloBlockchain,** kterou jste vytvořili v [požadovaném kurzu: Pomocí kódu Visual Studia vytvořte, sestavujte a nasaďte inteligentní kontrakty](send-transaction.md).

1. Pomocí stránky inteligentní smluvní interakce Azure Blockchain Development Kit volejte funkci **SendRequest.** Klikněte pravým tlačítkem na **HelloBlockchain.sol** a z nabídky zvolte **Zobrazit stránku interakce inteligentní smlouvy.**

    ![Z nabídky zvolte Zobrazit stránku interakce inteligentní smlouvy.](./media/data-manager-cosmosdb/contract-interaction.png)

1. Zvolte **SendRequest** smluvní akce a zadejte **Hello, Blockchain!** pro parametr **requestMessage.** Vyberte **Execute** pro volání **sendrequest** funkce prostřednictvím transakce.

    ![Spustit akci SendRequest](./media/data-manager-cosmosdb/sendrequest-action.png)

Funkce SendRequest nastaví pole **RequestMessage** a **State.** Aktuální stav **requestMessage** je argument, který jste **předali Hello, Blockchain**. Hodnota pole **Stav** zůstává **Požadavek**.

## <a name="view-transaction-data"></a>Zobrazit data transakcí

Teď, když jste připojili správce blockchainových dat k Azure Cosmos DB, můžete zobrazit zprávy o transakcích blockchainu v Průzkumníku dat Cosmos DB.

1. Přejděte do zobrazení Průzkumníka dat Cosmos DB. Například **cosmosdb-blockchain > Data Explorer > > zprávy > položky blockchainu**.

    ![Průzkumník dat Cosmos DB](./media/data-manager-cosmosdb/data-explorer.png)

    Průzkumník dat uvádí seznam datových zpráv blockchain, které byly vytvořeny v databázi Cosmos DB.

1. Projděte si zprávy výběrem ID položky a najděte zprávu s odpovídající hodnota hash transakce.

    [![Detail transakce blockchainu](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Zpráva nezpracované transakce obsahuje podrobnosti o transakci. Informace o vlastnostech jsou však zašifrovány.

    Vzhledem k tomu, že jste do instance Správce dat Blockchain přidali inteligentní kontrakt HelloBlockchain, je také odeslán typ zprávy **ContractProperties,** který obsahuje informace o dekódovaných vlastnostech.

1. Najděte zprávu **ContractProperties** pro transakci. Měla by to být další zpráva v seznamu.

    [![Detail transakce blockchainu](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    Pole **DecodedProperties** obsahuje vlastnosti transakce.

Blahopřejeme! Úspěšně jste vytvořili průzkumník zpráv o transakcích pomocí Správce dat blockchainu a Azure Cosmos DB.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete odstranit prostředky a skupiny prostředků, které jste použili pro tento kurz. Odstranění skupiny prostředků:

1. Na webu Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
1. Vyberte **Odstranit skupinu prostředků**. Ověřte odstranění zadáním názvu skupiny prostředků a vyberte **odstranit**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o integraci s knihami blockchainu.

> [!div class="nextstepaction"]
> [Použití konektoru Ethereum Blockchain s aplikacemi Azure Logic Apps](ethereum-logic-app.md)
