---
title: Konfigurace Data Manager blockchain pomocí Azure Portal-služba Azure blockchain
description: Vytvoření a Správa blockchain Data Manager pro službu Azure blockchain pomocí Azure Portal.
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: a151c28d31bf0bb7f21185fb161315d42f9563d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85200677"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Konfigurace Blockchain Data Manageru s využitím webu Azure Portal

Nakonfigurujte blockchain Data Manager pro službu Azure blockchain pro zachycení dat blockchain a odeslání do Azure Event Grid tématu.

Pokud chcete nakonfigurovat instanci Data Manager blockchain, postupujte takto:

* Vytvoření instance blockchain Data Manager pro uzel transakce služby Azure blockchain
* Přidání aplikací blockchain

## <a name="prerequisites"></a>Požadavky

* Kompletní [rychlé zprovoznění: Vytvoření člena blockchain pomocí Azure Portal](create-member.md) nebo [rychlé zprovoznění: Vytvoření člena blockchain služby Azure BLOCKCHAIN pomocí Azure CLI](create-member-cli.md). Služba Azure blockchain Service úrovně *Standard* se doporučuje při použití blockchain data Manager.
* Vytvoření [tématu Event Grid](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Další informace o [obslužných rutinách událostí v Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Vytvořit instanci

Instance blockchain Data Manager se připojuje a monitoruje uzel transakcí služby Azure blockchain. Pouze uživatelé s přístupem k uzlu transakce mohou vytvořit připojení. Instance zachycuje všechna nepracovaná data bloku a nezpracovaná transakce z uzlu transakce. Blockchain Data Manager publikuje zprávu **RawBlockAndTransactionMsg** , která je nadmnožinou informací vrácených z dotazů web3. ETH [getblock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) a [gettransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) .

Odchozí připojení odesílá blockchain data do Azure Event Grid. Při vytváření instance nakonfigurujete jedno odchozí připojení. Blockchain Data Manager podporuje vícenásobná odchozí připojení Event Grid tématu pro všechny dané instance služby blockchain Data Manager. Blockchain data můžete odesílat do jednoho cíle nebo odesílat blockchain data do více cílů. Pokud chcete přidat další cíl, stačí do instance přidat další odchozí připojení.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejít na člen služby Azure blockchain, který se má připojit k blockchain Data Manager. Vyberte **Blockchain data Manager**.
1. Vyberte **Přidat**.

    ![Přidat Data Manager blockchain](./media/data-manager-portal/add-instance.png)

    Zadejte následující podrobnosti:

    Nastavení | Popis
    --------|------------
    Název | Zadejte jedinečný název pro připojenou blockchain Data Manager. Název Data Manager blockchain může obsahovat malá písmena a číslice a maximální délka je 20 znaků.
    Uzel transakce | Vyberte uzel transakce. V seznamu jsou jenom uzly transakcí, ke kterým máte přístup pro čtení.
    Název připojení | Zadejte jedinečný název odchozího připojení, kde se odesílají data blockchain transakce.
    Koncový bod služby Event Grid | V rámci stejného předplatného, jako je blockchain Data Manager instance, vyberte téma Event Grid.

1. Vyberte **OK**.

    Vytvoření instance blockchain Data Manager trvá méně než minutu. Po nasazení instance se automaticky spustí. Běžící instance blockchain Data Manager zachycuje události blockchain z uzlu transakce a odesílá data do odchozích připojení.

    Nová instance se zobrazí v seznamu instancí blockchain Data Manager pro člena služby Azure blockchain.

    ![Seznam instancí datových členů blockchain](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Přidat aplikaci blockchain

Pokud přidáte blockchain aplikaci, blockchain Data Manager dekódování události a stavu vlastnosti aplikace. V opačném případě jsou odesílána pouze nezpracovaná data bloku a nezpracované transakce. Blockchain Data Manager také zjišťuje adresy smluv při nasazení smlouvy. Do instance Data Manager blockchain můžete přidat více aplikací blockchain.

> [!IMPORTANT]
> V současné době nejsou aplikace blockchain, které deklarují [typy polí](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) soliding nebo [mapování typů](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) , plně podporovány. Vlastnosti deklarované jako pole nebo typy mapování nebudou v rámci zpráv *ContractPropertiesMsg* nebo *DecodedContractEventsMsg* dekódovat.

Blockchain Data Manager vyžaduje, aby se aplikace přidala do souboru s nasazeným bajtem a nasazeným souborem.

### <a name="get-contract-abi-and-bytecode"></a>Získání kontraktu ABI a bytového kódu

Smlouva ABI definuje rozhraní inteligentních kontraktů. Popisuje, jak pracovat s inteligentní smlouvou. K zkopírování kontraktu ABI do schránky můžete použít [rozšíření Azure blockchain Development Kit pro rozšíření ethereem](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) .

1. V podokně Průzkumník Visual Studio Code rozbalte složku **Build/Contracts** vašeho projektu Solid of.
1. Klikněte pravým tlačítkem na soubor JSON metadat kontraktu. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. Vyberte **Kopírovat kontrakt ABI**.

    ![Podokno Visual Studio Code s výběrem kopírovat kontrakt ABI](./media/data-manager-portal/abi-devkit.png)

    Kontrakt ABI je zkopírován do schránky.

1. Uložte pole **ABI** jako soubor JSON. Například *abi.jsna*. Tento soubor použijete v pozdějším kroku.

Blockchain Data Manager vyžaduje nasazený bytový kód pro inteligentní kontrakt. Nasazený bajt se liší od bajtového kódu inteligentního kontraktu. K zkopírování bytového kódu do schránky použijte rozšíření Azure blockchain Development Kit.

1. V podokně Průzkumník Visual Studio Code rozbalte složku **Build/Contracts** vašeho projektu Solid of.
1. Klikněte pravým tlačítkem na soubor JSON metadat kontraktu. Název souboru je název čipové smlouvy následovaný příponou **. JSON** .
1. Vyberte položku **Kopírovat bajtový kód transakce**.

    ![Podokno Visual Studio Code s výběrem kopírovat bajtovou část transakce](./media/data-manager-portal/bytecode-devkit.png)

    Kód bajtu je zkopírován do schránky.

1. Uložte hodnotu **bajtového kódu** jako soubor JSON. Například *bytecode.jsna*. Tento soubor použijete v pozdějším kroku.

Následující příklad ukazuje *abi.js* a *bytecode.js* souborů otevřených v editoru vs Code. Soubory by měly vypadat podobně.

![Příklad abi.jsa bytecode.jssouborů](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Vytvořit kontrakt ABI a adresu URL bytového kódu

Blockchain Data Manager vyžaduje, aby při přidávání aplikace byly v adrese URL přístupné soubory ABI a bytového kódu. Pomocí účtu Azure Storage můžete poskytnout soukromě přístupnou adresu URL.

#### <a name="create-storage-account"></a>Vytvoření účtu úložiště

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Nahrání souborů smluv

1. Vytvořte nový kontejner pro účet úložiště. Vyberte **kontejnery > kontejner**.

    ![Vytvoření kontejneru účtu úložiště](./media/data-manager-portal/create-container.png)

    | Pole | Popis |
    |-------|-------------|
    | Název  | Zadejte název kontejneru. Například *smartcontract* |
    | Úroveň veřejného přístupu | Zvolit *privátní (bez anonymního přístupu)* |

1. Kliknutím na **OK** kontejner vytvoříte.
1. Vyberte kontejner a pak vyberte **nahrát**.
1. Vyberte soubory JSON, které jste vytvořili v části [získání kontraktu ABI a bytového kódu](#get-contract-abi-and-bytecode) .

    ![Nahrát objekt BLOB](./media/data-manager-portal/upload-blobs.png)

    Vyberte **Nahrát**.

#### <a name="generate-url"></a>Vygenerovat adresu URL

Pro každý objekt BLOB vygenerujte sdílený přístupový podpis.

1. Vyberte objekt BLOB ABI JSON.
1. Vyberte **Generovat SAS** .
1. Nastavte požadovanou dobu vypršení platnosti podpisového podpisu a pak vyberte **Generovat token SAS objektu BLOB a adresu URL**.

    ![Generovat token SAS](./media/data-manager-portal/generate-sas.png)

1. Zkopírujte **adresu URL SAS objektu BLOB** a uložte ji pro další oddíl.
1. Zopakujte kroky pro [vygenerování adresy URL](#generate-url) pro objekt BLOB JSON kódu.

### <a name="add-application-to-instance"></a>Přidat aplikaci do instance

1. V seznamu instancí vyberte instanci blockchain Data Manager.
1. Vyberte **blockchain aplikace**.
1. Vyberte **Přidat**.

    ![Přidání aplikace blockchain](./media/data-manager-portal/add-application.png)

    Zadejte název aplikace blockchain a adresy URL pro kód a bytového kódu inteligentního kontraktu.

    Nastavení | Popis
    --------|------------
    Název | Zadejte jedinečný název, který má blockchain aplikace sledovat.
    ABI kontraktu | Cesta URL k souboru ABI kontraktu Další informace najdete v tématu [Vytvoření kontraktu ABI a adresy URL bytového kódu](#create-contract-abi-and-bytecode-url).
    Bajtový kód kontraktu | Cesta URL k souboru bytového kódu Další informace najdete v tématu [Vytvoření kontraktu ABI a adresy URL bytového kódu](#create-contract-abi-and-bytecode-url).

1. Vyberte **OK**.

    Po vytvoření aplikace se aplikace zobrazí v seznamu aplikací blockchain.

    ![Seznam aplikací blockchain](./media/data-manager-portal/artifact-list.png)

Můžete odstranit účet Azure Storage nebo ho použít ke konfiguraci dalších aplikací blockchain. Pokud chcete odstranit účet Azure Storage, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený účet úložiště a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

## <a name="stop-instance"></a>Zastavit instanci

Pokud chcete zastavit zachytávání událostí blockchain a odesílat data do odchozích připojení, zastavte instanci blockchain Manageru. Když se instance zastaví, neúčtují se za blockchain Data Manager žádné poplatky. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Přejít na **Přehled** a vybrat **zastavit**.

    ![Zastavit instanci](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Další kroky

Zkuste v dalším kurzu vytvořit blockchain Průzkumníka zpráv transakce pomocí blockchain Data Manager a Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Použití Blockchain Data Manageru k odesílání dat do Azure Cosmos DB](data-manager-cosmosdb.md)