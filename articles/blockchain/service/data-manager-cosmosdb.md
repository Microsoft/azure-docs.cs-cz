---
title: Použití Data Manager blockchain k aktualizaci Azure Cosmos DB – Azure blockchain Service
description: Použití blockchain Data Manager pro službu Azure blockchain k odesílání dat blockchain do Azure Cosmos DB
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 69790787bc888448f2f40178bd12ee7058cc5892
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91283445"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Kurz: použití Data Manager blockchain k odesílání dat do Azure Cosmos DB

V tomto kurzu použijete blockchain Data Manager pro službu Azure blockchain k záznamu dat transakcí blockchain v Azure Cosmos DB. Blockchain Data Manager zachycuje, transformuje a doručuje Blockchainá data pro Azure Event Grid témata. Z Azure Event Grid k vytváření dokumentů v databázi Azure Cosmos DB používáte konektor aplikace logiky Azure. Po dokončení kurzu můžete prozkoumat data blockchain transakcí v Azure Cosmos DB Průzkumník dat.

[![Snímek obrazovky ukazuje podrobnosti transakce blockchain.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvoření instance Data Manager blockchain
> * Přidání aplikace blockchain k dekódování vlastností transakce a událostí
> * Vytvoření účtu Azure Cosmos DB a databáze pro uložení dat transakce
> * Vytvoření aplikace logiky Azure pro připojení tématu Azure Event Grid k Azure Cosmos DB
> * Odeslání transakce do hlavní knihy blockchain
> * Zobrazit dekódovat data transakce v Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Kompletní [rychlé zprovoznění: Vytvoření člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlé zprovoznění: Vytvoření člena blockchain služby Azure BLOCKCHAIN pomocí Azure CLI](create-member-cli.md)
* Kompletní [rychlé zprovoznění: pomocí Visual Studio Code se připojte k síti konsorcia služeb Azure blockchain](connect-vscode.md). Rychlý Start vás provede instalací [sady Azure blockchain Development Kit pro ethereem](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) a nastavením vašeho vývojového prostředí blockchain.
* Úplný [kurz: použijte Visual Studio Code k vytváření, sestavování a nasazování inteligentních smluv](send-transaction.md). Kurz vás provede vytvořením ukázkové inteligentní smlouvy.
* Vytvoření [tématu Event Grid](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Další informace o [obslužných rutinách událostí v Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Vytvořit instanci

Instance blockchain Data Manager se připojuje a monitoruje uzel transakcí služby Azure blockchain. Instance zachycuje všechna nepracovaná data bloku a nezpracovaná transakce z uzlu transakce. Odchozí připojení odesílá blockchain data do Azure Event Grid. Při vytváření instance nakonfigurujete jedno odchozí připojení.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejít na člen služby Azure blockchain, který jste vytvořili v části [rychlý Start: Vytvoření členu blockchain pomocí Azure Portal](create-member.md). Vyberte **Blockchain data Manager**.
1. Vyberte **Přidat**.

    ![Přidat Data Manager blockchain](./media/data-manager-cosmosdb/add-instance.png)

    Zadejte následující podrobnosti:

    Nastavení | Příklad | Popis
    --------|---------|------------
    Název | mywatcher | Zadejte jedinečný název pro připojenou blockchain Data Manager.
    Uzel transakce | myblockchainmember | Vyberte výchozí uzel transakce členu služby Azure blockchain, který jste vytvořili v požadovaném stavu.
    Název připojení | cosmosdb | Zadejte jedinečný název odchozího připojení, kde se odesílají data blockchain transakce.
    Koncový bod služby Event Grid | myTopic | Vyberte téma Event gridu, které jste vytvořili v požadovaném požadavku. Poznámka: blockchain Data Manager instance a téma Event Grid musí být ve stejném předplatném.

1. Vyberte **OK**.

    Vytvoření instance blockchain Data Manager trvá méně než minutu. Po nasazení instance se automaticky spustí. Běžící instance blockchain Data Manager zachycuje události blockchain z uzlu transakce a odesílá data do služby Event Grid.

## <a name="add-application"></a>Přidání aplikace

Přidejte aplikaci **helloblockchain** blockchain, aby blockchain data Manager dekódování událostí a stavu vlastností. Blockchain Data Manager vyžaduje, aby se k přidání aplikace přidal soubor ABI a bytového kódu Smart Contract

### <a name="get-contract-abi-and-bytecode"></a>Získání kontraktu ABI a bytového kódu

Smlouva ABI definuje rozhraní inteligentních kontraktů. Popisuje, jak pracovat s inteligentní smlouvou. K zkopírování kontraktu ABI do schránky můžete použít [rozšíření Azure blockchain Development Kit pro rozšíření ethereem](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) .

1. V podokně Visual Studio Code Průzkumník rozbalte složku **Build/Contracts** projektu **helloblockchain** Solid, který jste vytvořili v [kurzu požadavků: pomocí Visual Studio Code můžete vytvářet, sestavovat a nasazovat inteligentní smlouvy](send-transaction.md).
1. Klikněte pravým tlačítkem na soubor JSON metadat kontraktu. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. Vyberte **Kopírovat kontrakt ABI**.

    ![Podokno Visual Studio Code s výběrem kopírovat kontrakt ABI](./media/data-manager-cosmosdb/abi-devkit.png)

    Kontrakt ABI je zkopírován do schránky.

1. Uložte pole **ABI** jako soubor JSON. Například *abi.jsna*. Tento soubor použijete v pozdějším kroku.

Blockchain Data Manager vyžaduje nasazený bytový kód pro inteligentní kontrakt. Nasazený bajt se liší od bajtového kódu inteligentního kontraktu. K zkopírování bytového kódu do schránky použijte rozšíření Azure blockchain Development Kit.

1. V podokně Průzkumník Visual Studio Code rozbalte složku **Build/Contracts** vašeho projektu Solid of.
1. Klikněte pravým tlačítkem na soubor JSON metadat kontraktu. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. Vyberte položku **Kopírovat bajtový kód transakce**.

    ![Podokno Visual Studio Code s výběrem kopírovat bajtovou část transakce](./media/data-manager-cosmosdb/bytecode-devkit.png)

    Kód bajtu je zkopírován do schránky.

1. Uložte hodnotu **bajtového kódu** jako soubor JSON. Například *bytecode.jsna*. Tento soubor použijete v pozdějším kroku.

Následující příklad ukazuje *abi.js* a *bytecode.js* souborů otevřených v editoru vs Code. Soubory by měly vypadat podobně.

![Příklad abi.jsa bytecode.jssouborů](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Vytvořit kontrakt ABI a adresu URL bytového kódu

Blockchain Data Manager vyžaduje, aby při přidávání aplikace byly v adrese URL přístupné soubory ABI a bytového kódu. Pomocí účtu Azure Storage můžete poskytnout soukromě přístupnou adresu URL.

#### <a name="create-storage-account"></a>Vytvoření účtu úložiště

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Nahrání souborů smluv

1. Vytvořte nový kontejner pro účet úložiště. Vyberte **kontejnery > kontejner**.

    ![Vytvoření kontejneru účtu úložiště](./media/data-manager-cosmosdb/create-container.png)

    | Nastavení | Popis |
    |---------|-------------|
    | Název  | Zadejte název kontejneru. Například *smartcontract* |
    | Úroveň veřejného přístupu | Zvolit *privátní (bez anonymního přístupu)* |

1. Kliknutím na **OK** kontejner vytvoříte.
1. Vyberte kontejner a pak vyberte **nahrát**.
1. Vyberte soubory JSON, které jste vytvořili v části [získání kontraktu ABI a bytového kódu](#get-contract-abi-and-bytecode) .

    ![Nahrát objekt BLOB](./media/data-manager-cosmosdb/upload-blobs.png)

    Vyberte **Nahrát**.

#### <a name="generate-url"></a>Vygenerovat adresu URL

Pro každý objekt BLOB vygenerujte sdílený přístupový podpis.

1. Vyberte objekt BLOB ABI JSON.
1. Vyberte **Generovat SAS** .
1. Nastavte požadovanou dobu vypršení platnosti podpisového podpisu a pak vyberte **Generovat token SAS objektu BLOB a adresu URL**.

    ![Generovat token SAS](./media/data-manager-cosmosdb/generate-sas.png)

1. Zkopírujte **adresu URL SAS objektu BLOB** a uložte ji pro další oddíl.
1. Zopakujte kroky pro [vygenerování adresy URL](#generate-url) pro objekt BLOB JSON kódu.

### <a name="add-helloblockchain-application-to-instance"></a>Přidat aplikaci helloblockchain do instance

1. V seznamu instancí vyberte instanci blockchain Data Manager.
1. Vyberte **blockchain aplikace**.
1. Vyberte **Přidat**.

    ![Přidání aplikace blockchain](./media/data-manager-cosmosdb/add-application.png)

    Zadejte název aplikace blockchain a adresy URL pro kód a bytového kódu inteligentního kontraktu.

    Nastavení | Popis
    --------|------------
    Název | Zadejte jedinečný název, který má blockchain aplikace sledovat.
    ABI kontraktu | Cesta URL k souboru ABI kontraktu Další informace najdete v tématu [Vytvoření kontraktu ABI a adresy URL bytového kódu](#create-contract-abi-and-bytecode-url).
    Bajtový kód kontraktu | Cesta URL k souboru bytového kódu Další informace najdete v tématu [Vytvoření kontraktu ABI a adresy URL bytového kódu](#create-contract-abi-and-bytecode-url).

1. Vyberte **OK**.

    Po vytvoření aplikace se aplikace zobrazí v seznamu aplikací blockchain.

    ![Seznam aplikací blockchain](./media/data-manager-cosmosdb/artifact-list.png)

Můžete odstranit účet Azure Storage nebo ho použít ke konfiguraci dalších aplikací blockchain. Pokud chcete odstranit účet Azure Storage, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený účet úložiště a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

## <a name="create-azure-cosmos-db"></a>Vytvoření služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Přidání databáze a kontejneru

K vytvoření databáze a kontejneru můžete použít Průzkumník dat v Azure Portal.

1. V levém navigačním panelu na stránce Azure Cosmos DB účtu vyberte **Průzkumník dat** a pak vyberte **Nový kontejner**.
1. V podokně **Přidat kontejner** zadejte nastavení pro nový kontejner.

    ![Přidat nastavení kontejneru](./media/data-manager-cosmosdb/add-container.png)

    | Nastavení | Popis
    |---------|-------------|
    | ID databáze | Jako název nové databáze zadejte **blockchain-data** . |
    | Propustnost | Nechte propustnost na **400** jednotek žádostí za sekundu (ru/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.|
    | ID kontejneru | Jako název nového kontejneru zadejte **Messages** . |
    | Klíč oddílu | Jako klíč oddílu použijte **/MessageType** . |

1. Vyberte **OK**. Průzkumník dat zobrazí novou databázi a kontejner, který jste vytvořili.

## <a name="create-logic-app"></a>Vytvoření aplikace logiky

Azure Logic Apps pomáhá při plánování a automatizaci obchodních procesů a pracovních postupů, pokud potřebujete integrovat systémy a služby. Aplikaci logiky můžete použít k připojení Event Grid k Azure Cosmos DB.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Integrace** > **Aplikace logiky**.
1. Zadejte podrobné informace o tom, kde vytvořit aplikaci logiky. Až budete hotovi, vyberte **vytvořit**.

    Další informace o vytváření aplikací logiky najdete v tématu [Vytvoření automatizovaných pracovních postupů pomocí Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Až Azure nasadí vaši aplikaci, vyberte prostředek aplikace logiky.
1. V Návrháři Logic Apps v části **šablony**vyberte **prázdná aplikace logiky**.

### <a name="add-event-grid-trigger"></a>Přidat aktivační událost Event Grid

Každá aplikace logiky se musí spouštět triggerem, který se aktivuje při určité události nebo splnění určité podmínky. Pokaždé, když se trigger aktivuje, vytvoří modul Logic Apps instanci aplikace logiky pro spuštění vašeho pracovního postupu. Pomocí triggeru Azure Event Grid odesílá data transakcí blockchain z Event Grid na Cosmos DB.

1. V Návrháři Logic Apps vyhledejte a vyberte konektor **Azure Event Grid** .
1. Na kartě **triggery** vyberte, **kdy dojde k události prostředku**.
1. Vytvořte připojení rozhraní API k vašemu Event Grid tématu.

    ![Nastavení aktivační události Event gridu](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Nastavení | Popis
    |---------|-------------|
    | Předplatné | Vyberte předplatné, které obsahuje Event Grid téma. |
    | Typ prostředku | Vyberte **Microsoft. EventGrid. témata**. |
    | Název prostředku | Vyberte název Event Grid téma, kde Data Manager blockchain odesílá zprávy transakčních dat. |

### <a name="add-cosmos-db-action"></a>Přidat Cosmos DB akci

Přidejte akci pro vytvoření dokumentu v Cosmos DB pro každou transakci. Pro kategorizaci zpráv použijte typ zprávy transakce jako klíč oddílu.

1. Vyberte **Nový krok**.
1. Na **Vybrat akci**vyhledejte **Azure Cosmos DB**.
1. Vyberte **Azure Cosmos DB > akce > vytvořit nebo aktualizovat dokument**.
1. Vytvořte připojení rozhraní API k databázi Cosmos DB.

    ![Nastavení připojení Cosmos DB](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Nastavení | Popis
    |---------|-------------|
    | Název připojení | Vyberte předplatné, které obsahuje Event Grid téma. |
    | Účet DocumentDB | Vyberte účet DocumentDB, který jste vytvořili v části [Vytvoření účtu Azure Cosmos DB](#create-azure-cosmos-db) . |

1. Zadejte **ID databáze** a **id kolekce** pro Azure Cosmos DB, které jste vytvořili dříve v části [Přidání databáze a kontejneru](#add-a-database-and-container) .

1. Vyberte nastavení **dokumentu** . V rozbalovacím seznamu *Přidat dynamický obsah* vyberte **výraz** a zkopírujte a vložte následující výraz:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    Výraz Získá datovou část zprávy a nastaví ID na hodnotu časového razítka.

1. Vyberte **Přidat nový parametr** a zvolte **hodnota klíč oddílu**.
1. Nastavte **hodnotu klíče oddílu** na `"@{triggerBody()['data']['MessageType']}"` . Hodnota musí být obklopena dvojitými uvozovkami.

    ![Logic Apps návrháře s nastavením Cosmos DB](./media/data-manager-cosmosdb/create-action.png)

    Hodnota nastaví klíč oddílu na typ zprávy transakce.

1. Vyberte **Uložit**.

Aplikace logiky monitoruje téma Event Grid. Při odeslání nové zprávy transakce z Data Manager blockchain vytvoří aplikace logiky dokument v Cosmos DB.

## <a name="send-a-transaction"></a>Odeslat transakci

V dalším kroku odešlete transakci do hlavní knihy blockchain, kde otestujete, co jste vytvořili. Použijte funkci **SendRequest** kontraktu **HelloBlockchain** , kterou jste vytvořili v kurzu požadavků [: pomocí Visual Studio Code můžete vytvářet, sestavovat a nasazovat inteligentní smlouvy](send-transaction.md).

1. Pomocí stránky interakce inteligentního kontraktu služby Azure blockchain Development Kit můžete zavolat funkci **SendRequest** . Klikněte pravým tlačítkem na **HelloBlockchain. Sol** a v nabídce vyberte možnost **Zobrazit stránku interakce inteligentních kontraktů** .

    ![Zvolit zobrazení stránky interakce inteligentních kontraktů z nabídky](./media/data-manager-cosmosdb/contract-interaction.png)

1. Vyberte akci **SendRequest** kontrakt a zadejte **Hello, blockchain!** pro parametr **RequestMessage** . Vyberte možnost **Spustit** pro volání funkce **SendRequest** prostřednictvím transakce.

    ![Spustit SendRequest akci](./media/data-manager-cosmosdb/sendrequest-action.png)

Funkce SendRequest nastaví pole **RequestMessage** a **State** . Aktuální stav pro **RequestMessage** je argumentem, který jste předali **Hello, blockchain**. Hodnota pole **stav** zůstane **požadavek**.

## <a name="view-transaction-data"></a>Zobrazit data transakcí

Teď, když jste se připojili Data Manager blockchain k Azure Cosmos DB, můžete v Cosmos DB Průzkumník dat zobrazit zprávy o transakcích blockchain.

1. Přejít na Průzkumník dat zobrazení Cosmos DB. Například **cosmosdb-blockchain > Průzkumník dat > blockchain-Data > zprávy > položky**.

    ![Cosmos DB Průzkumník dat](./media/data-manager-cosmosdb/data-explorer.png)

    Průzkumník dat vypíše zprávy s blockchain daty, které byly vytvořeny v databázi Cosmos DB.

1. Procházejte zprávami tak, že vyberete ID položky a vyhledáte zprávu s hodnotou hash transakce odpovídajícího transakce.

    [![Snímek obrazovky ukazuje podrobnosti transakce blockchain pro vybranou položku.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Zpráva o nezpracované transakci obsahuje podrobnosti o transakci. Informace o vlastnostech jsou ale šifrované.

    Vzhledem k tomu, že jste přidali HelloBlockchain Smart Contract do instance blockchain Data Manager, pošle se také typ zprávy **ContractProperties** , který obsahuje Dekódovatelné informace o vlastnostech.

1. Vyhledejte zprávu **ContractProperties** pro transakci. Mělo by se jednat o další zprávu v seznamu.

    [![Podrobnosti transakce blockchain](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    Pole **DecodedProperties** obsahuje vlastnosti transakce.

Blahopřejeme! Úspěšně jste vytvořili Průzkumníka transakčních zpráv pomocí Data Manager blockchain a Azure Cosmos DB.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit prostředky a skupiny prostředků, které jste použili v tomto kurzu. Odstranění skupiny prostředků:

1. V Azure Portal přejděte do **skupiny prostředků** v levém navigačním podokně a vyberte skupinu prostředků, kterou chcete odstranit.
1. Vyberte **Odstranit skupinu prostředků**. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o integraci s blockchain účetními kniha.

> [!div class="nextstepaction"]
> [Použití konektoru Ethereem blockchain s Azure Logic Apps](ethereum-logic-app.md)
