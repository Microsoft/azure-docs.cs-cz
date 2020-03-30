---
title: Konfigurace Správce dat blockchainu pomocí portálu Azure – Služba Azure Blockchain
description: Vytvářejte a spravujte Blockchain Data Manager pro službu Azure Blockchain pomocí portálu Azure.
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 03c22a7a23f1579a846746f21ce048b3425399c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273160"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Konfigurace Blockchain Data Manageru s využitím webu Azure Portal

Nakonfigurujte Správce blockchainových dat pro službu Azure Blockchain service tak, aby zaznamenávala data blockchainu a odesílala je do tématu Sítě událostí Azure.

Chcete-li nakonfigurovat instanci Správce dat blockchainu, můžete:

* Vytvoření instance Správce dat blockchainu pro transakční uzel služby Azure Blockchain Service
* Přidejte své blockchainové aplikace

## <a name="prerequisites"></a>Požadavky

* Kompletní [úvodní příručka: Vytvoření člena blockchainu pomocí portálu Azure](create-member.md) nebo [úvodního startu: Vytvoření člena blockchainu služby Azure Blockchain pomocí Azure CLI](create-member-cli.md)
* Vytvoření [tématu mřížky událostí](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informace o [obslužných rutinách událostí v Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Vytvořit instanci

Instance Správce dat Blockchain propojuje a monitoruje transakční uzel služby Azure Blockchain Service. Připojení mohou vytvořit pouze uživatelé s přístupem k uzlu transakce. Instance zachytí všechna nezpracovaná data bloku a nezpracovaných transakcí z uzlu transakce.

Odchozí připojení odesílá data blockchainu do Azure Event Grid. Při vytváření instance nakonfigurujete jedno odchozí připojení. Blockchain Data Manager podporuje více odchozích připojení Event Grid Topic pro danou instanci Blockchain Data Manager. Data blockchainu můžete odesílat do jednoho cíle nebo data blockchainu do více destinací. Chcete-li přidat další cíl, stačí k instanci přidat další odchozí připojení.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na člena služby Azure Blockchain Service, kterého chcete připojit ke Správci dat Blockchain. Vyberte **Správce dat blockchainu**.
1. Vyberte **Přidat**.

    ![Přidat Správce dat blockchainu](./media/data-manager-portal/add-instance.png)

    Zadejte následující podrobnosti:

    Nastavení | Popis
    --------|------------
    Name (Název) | Zadejte jedinečný název připojeného Správce dat blockchainu. Název Správce dat blockchainu může obsahovat malá písmena a čísla a má maximální délku 20 znaků.
    Uzel transakce | Zvolte uzel transakce. Jsou uvedeny pouze uzly transakcí, které máte přístup pro čtení.
    Název připojení | Zadejte jedinečný název odchozího připojení, kam jsou odesílána data transakcí blockchainu.
    Koncový bod mřížky událostí | Zvolte téma mřížky událostí ve stejném předplatném jako instance Správce dat Blockchain.

1. Vyberte **OK**.

    Vytvoření instance Správce dat blockchainu trvá méně než minutu. Po nasazení instance se automaticky spustí. Spuštěná instance Správce dat Blockchain zachycuje události blockchainu z transakčního uzlu a odesílá data odchozím připojením.

    Nová instance se zobrazí v seznamu instancí Správce dat Blockchain pro člena služby Azure Blockchain.

    ![Seznam instancí datových členů blockchainu](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Přidání blockchainové aplikace

Pokud přidáte blockchainovou aplikaci, Blockchain Data Manager dekóduje stav události a vlastnosti pro aplikaci. V opačném případě jsou odeslána pouze nezpracovaná data bloku a nezpracovaných transakcí. Blockchain Data Manager také zjišťuje adresy smluv při nasazení smlouvy. Do instance Správce dat Blockchain můžete přidat více blockchainových aplikací.

> [!IMPORTANT]
> V současné době nejsou plně podporovány blockchainové aplikace, které deklarují [typy polí](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) Solidity nebo [typy mapování.](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) Vlastnosti deklarované jako typy polí nebo mapování nebudou dekódovány ve *zprávách ContractPropertiesMsg* nebo *DecodedContractEventsMsg.*

Blockchain Data Manager vyžaduje inteligentní kontrakt ABI a nasazený soubor bytecode pro přidání aplikace.

### <a name="get-contract-abi-and-bytecode"></a>Získat abi a bytecode smlouvy

Smlouva ABI definuje inteligentní smluvní rozhraní. Popisuje, jak pracovat s inteligentní smlouvy. Pomocí azure [blockchain development kit pro rozšíření Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) můžete zkopírovat smlouvu ABI do schránky.

1. V podokně průzkumníkkódu sady Visual Studio rozbalte složku **sestavení/smluv** projektu Solidity.
1. Klikněte pravým tlačítkem myši na soubor JSON metadat smlouvy. Název souboru je inteligentní název smlouvy následovaný příponou **JSON.**
1. Vyberte **možnost Kopírovat smlouvu ABI**.

    ![Podokno Kódu sady Visual Studio s výběrem ABI smlouvy copy](./media/data-manager-portal/abi-devkit.png)

    Smlouva ABI je zkopírována do schránky.

1. Uložte pole **abi** jako soubor JSON. Například *abi.json*. Soubor se použije v pozdějším kroku.

Blockchain Data Manager vyžaduje nasazený bytecode pro inteligentní kontrakt. Nasazený bajtkód se liší od inteligentního kódu bytecode smlouvy. Rozšíření vývojové sady Azure blockchain slouží ke kopírování bajtového kódu do schránky.

1. V podokně průzkumníkkódu sady Visual Studio rozbalte složku **sestavení/smluv** projektu Solidity.
1. Klikněte pravým tlačítkem myši na soubor JSON metadat smlouvy. Název souboru je inteligentní název smlouvy následovaný příponou **JSON.**
1. Vyberte **možnost Kopírovat bajtový kód transakce**.

    ![Podokno Kódu sady Visual Studio s výběrem kopírovat transakční bajtokód](./media/data-manager-portal/bytecode-devkit.png)

    Bajtkód se zkopíruje do schránky.

1. Uložte hodnotu **bajtového kódu** jako soubor JSON. Například *bytecode.json*. Soubor se použije v pozdějším kroku.

Následující příklad ukazuje *abi.json* a *bytecode.json* soubory otevřené v editoru kódu VS. Soubory by měly vypadat podobně.

![Příklad souborů abi.json a bytecode.json](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Vytvoření adresy URL smlouvy ABI a bytecode

Blockchain Data Manager vyžaduje, aby soubory abi a bytecode smlouvy byly přístupné pomocí adresy URL při přidávání aplikace. Účet Azure Storage můžete použít k poskytnutí soukromě přístupné adresy URL.

#### <a name="create-storage-account"></a>Vytvoření účtu úložiště

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Nahrát soubory smluv

1. Vytvořte nový kontejner pro účet úložiště. Vyberte **kontejnery > kontejneru**.

    ![Vytvoření kontejneru účtu úložiště](./media/data-manager-portal/create-container.png)

    | Pole | Popis |
    |-------|-------------|
    | Name (Název)  | Zadejte název kontejneru. Například *smartcontract* |
    | Úroveň veřejného přístupu | Zvolte *Soukromé (bez anonymního přístupu)* |

1. Kliknutím na **OK** kontejner vytvoříte.
1. Vyberte kontejner a pak vyberte **Nahrát**.
1. Zvolte oba soubory JSON, které jste vytvořili v části [Získat aBI smlouvy a bytecode.](#get-contract-abi-and-bytecode)

    ![Nahrát objekt blob](./media/data-manager-portal/upload-blobs.png)

    Vyberte **Nahrát**.

#### <a name="generate-url"></a>Generovat adresu URL

Pro každý objekt blob vygenerujte sdílený přístupový podpis.

1. Vyberte objekt blob ABI JSON.
1. Vybrat **generovat SAS**
1. Nastavte požadovanou expiraci podpisu přístupu a pak vyberte **Generovat token SAS objektu blob a adresu URL**.

    ![Generovat token SAS](./media/data-manager-portal/generate-sas.png)

1. Zkopírujte **adresu URL objektu Blob SAS** a uložte ji do další části.
1. Opakujte kroky [generování adresy URL](#generate-url) pro objekt blob Bajtkódu JSON.

### <a name="add-application-to-instance"></a>Přidání aplikace do instance

1. Vyberte instanci Správce dat Blockchain ze seznamu instancí.
1. Vyberte **blockchainové aplikace**.
1. Vyberte **Přidat**.

    ![Přidání blockchainové aplikace](./media/data-manager-portal/add-application.png)

    Zadejte název blockchainové aplikace a inteligentní smlouvy ABI a adresy URL bajtového kódu.

    Nastavení | Popis
    --------|------------
    Name (Název) | Zadejte jedinečný název blockchainové aplikace ke sledování.
    Smlouva ABI | Cesta url k souboru ABI smlouvy. Další informace naleznete v [tématu Create contract ABI and bytecode URL](#create-contract-abi-and-bytecode-url).
    Kód bytecode smlouvy | Cesta URL k souboru bajtového kódu. Další informace naleznete v [tématu Create contract ABI and bytecode URL](#create-contract-abi-and-bytecode-url).

1. Vyberte **OK**.

    Jakmile je aplikace vytvořena, aplikace se zobrazí v seznamu blockchainových aplikací.

    ![Seznam aplikací blockchain](./media/data-manager-portal/artifact-list.png)

Účet Azure Storage můžete odstranit nebo ho použít ke konfiguraci dalších blockchainových aplikací. Pokud chcete odstranit účet Azure Storage, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený účet úložiště a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

## <a name="stop-instance"></a>Instance Stop

Zastavte instanci Správce blockchainu, pokud chcete zastavit zachycování událostí blockchainu a odesílání dat do odchozích připojení. Když je instance zastavena, za Správce dat Blockchain ucházejí žádné poplatky. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Přejděte na **Přehled** a vyberte **Zastavit**.

    ![Instance Stop](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Další kroky

Vyzkoušejte další kurz, který vytvoří průzkumník zpráv o transakcích blockchainu pomocí Správce dat Blockchain a Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Použití Blockchain Data Manageru k odesílání dat do Azure Cosmos DB](data-manager-cosmosdb.md)