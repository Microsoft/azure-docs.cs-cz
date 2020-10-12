---
title: Konfigurace blockchain Data Manager pomocí Azure CLI – Azure blockchain Service
description: Vytvoření a Správa blockchain Data Manager pro službu Azure blockchain pomocí Azure CLI
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: f067f4413f6ad8541cd36a7581f9243bed4e195f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87023734"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Konfigurace Blockchain Data Manageru s využitím Azure CLI

Konfigurace blockchain Data Manager pro službu Azure blockchain pro zachycení blockchain dat odeslání do Azure Event Grid tématu.

Pokud chcete nakonfigurovat instanci Data Manager blockchain, postupujte takto:

* Vytvoření instance blockchain Manageru
* Vytvoří vstup pro uzel transakce služby Azure blockchain.
* Vytvoření výstupu Azure Event Grid tématu
* Přidání aplikace blockchain
* Spustit instanci

## <a name="prerequisites"></a>Požadavky

* Nainstalujte nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a přihlaste se pomocí `az login` .
* Kompletní [rychlé zprovoznění: pomocí Visual Studio Code se připojte k síti konsorcia služeb Azure blockchain](connect-vscode.md). Služba Azure blockchain Service úrovně *Standard* se doporučuje při použití blockchain data Manager.
* Vytvoření [tématu Event Grid](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Další informace o [obslužných rutinách událostí v Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.51 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Vytvořit instanci

Instance blockchain Data Manager monitoruje uzel transakcí služby Azure blockchain. Instance zachycuje všechna nepracovaná data bloku a nezpracovaná transakce z uzlu transakce. Blockchain Data Manager publikuje zprávu **RawBlockAndTransactionMsg** , která je nadmnožinou informací vrácených z dotazů web3. ETH [getblock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) a [gettransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) .

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parametr | Popis |
|-----------|-------------|
| resource-group | Název skupiny prostředků, kde se má vytvořit instance Data Manager blockchain |
| name | Název instance Data Manager blockchain |
| typ prostředku | Typ prostředku pro instanci blockchain Data Manager je **Microsoft. blockchain/sledovací**procesy. |
| je-full-Object | Určuje, že vlastnosti obsahují možnosti pro prostředek sledovacího procesu. |
| properties | Řetězec ve formátu JSON, který obsahuje vlastnosti prostředku sledovacího procesu. Může být předán jako řetězec nebo jako soubor.  |

### <a name="create-instance-examples"></a>Vytvořit příklady instancí

Příklad konfigurace JSON pro vytvoření instance blockchain Manageru v oblasti **východní USA** .

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Element | Popis |
|---------|-------------|
| location | Oblast, kde se má vytvořit prostředek sledovacího procesu |
| properties | Vlastnosti, které se mají nastavit při vytváření prostředku sledovacího procesu |

Vytvořte instanci blockchain Data Manager s názvem *mywatcher* s použitím řetězce JSON pro konfiguraci.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Vytvořte instanci blockchain Data Manager s názvem *mywatcher* pomocí konfiguračního souboru JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Vytvořit vstup

Vstup připojuje blockchain Data Manager k uzlu transakce služby Azure blockchain. Pouze uživatelé s přístupem k uzlu transakce mohou vytvořit připojení.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parametr | Popis |
|-----------|-------------|
| resource-group | Název skupiny prostředků, kde se má vytvořit vstupní prostředek |
| name | Název vstupu |
| namespace | Použijte obor názvů poskytovatele **Microsoft. blockchain** . |
| typ prostředku | Typ prostředku pro vstup Data Manager blockchain jsou **vstupy**. |
| nadřazený | Cesta ke sledovacímu procesu, ke kterému je vstup přidružen. Například sledovací procesy **/mywatcher**. |
| je-full-Object | Určuje vlastnosti, které obsahují možnosti pro vstupní prostředek. |
| properties | Řetězec ve formátu JSON, který obsahuje vlastnosti pro vstupní prostředek. Může být předán jako řetězec nebo jako soubor. |

### <a name="input-examples"></a>Příklady vstupu

Příklad konfigurace JSON pro vytvoření vstupního prostředku v oblasti *východní USA* , která je připojená k \<Blockchain member\> .

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Element | Popis |
|---------|-------------|
| location | Oblast, kde se má vytvořit vstupní prostředek |
| inputType | Typ hlavní knihy člena služby Azure blockchain. V současné době se podporuje **ethereem** . |
| resourceId | Uzel transakce, ke kterému je vstup připojen \<Subscription ID\>Hodnoty, \<Resource group\> a nahraďte \<Blockchain member\> hodnotami pro prostředek uzlu transakce. Vstup se připojí k výchozímu uzlu transakce pro člena služby Azure blockchain. |

Vytvořte vstup s názvem *myInput* pro *MYWATCHER* pomocí řetězce JSON pro konfiguraci.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Vytvořte vstup s názvem *myInput* pro *mywatcher* pomocí konfiguračního souboru JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Vytvořit výstup

Odchozí připojení odesílá blockchain data do Azure Event Grid. Blockchain data můžete odesílat do jednoho cíle nebo odesílat blockchain data do více cílů. Blockchain Data Manager podporuje vícenásobná odchozí připojení Event Grid tématu pro všechny dané instance služby blockchain Data Manager.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parametr | Popis |
|-----------|-------------|
| resource-group | Název skupiny prostředků, kde se má vytvořit výstupní prostředek |
| name | Název výstupu. |
| namespace | Použijte obor názvů poskytovatele **Microsoft. blockchain** . |
| typ prostředku | Typ prostředku pro výstup blockchain Data Manager je **výstupy**. |
| nadřazený | Cesta ke sledovacímu procesu, ke kterému je výstup přidružen Například sledovací procesy **/mywatcher**. |
| je-full-Object | Určuje, že vlastnosti obsahují možnosti pro výstupní prostředek. |
| properties | Řetězec ve formátu JSON obsahující vlastnosti výstupního prostředku. Může být předán jako řetězec nebo jako soubor. |

### <a name="output-examples"></a>Příklady výstupu

Příklad konfigurace JSON pro vytvoření výstupního prostředku v oblasti *východní USA* , která je připojená k tématu Event Grid s názvem \<event grid topic\> .

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Element | Popis |
|---------|-------------|
| location | Oblast, ve které se má vytvořit výstupní prostředek. |
| outputType | Typ výstupu. V současné době se podporuje **EventGrid** . |
| resourceId | Prostředek, ke kterému je připojen výstup. \<Subscription ID\>Hodnoty, \<Resource group\> a nahraďte \<Blockchain member\> hodnotami pro prostředek Event gridu. |

Vytvořte výstup s názvem *MyOutput* pro *mywatcher* , který se připojí k tématu Event gridu pomocí konfiguračního řetězce JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Vytvořte výstup s názvem *MyOutput* pro *mywatcher* , který se připojí k tématu Event gridu pomocí konfiguračního souboru JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Přidat aplikaci blockchain

Pokud přidáte blockchain aplikaci, blockchain Data Manager dekódování události a stavu vlastnosti aplikace. V opačném případě jsou odesílána pouze nezpracovaná data bloku a nezpracované transakce. Blockchain Data Manager také zjišťuje adresy smluv při nasazení smlouvy. Do instance Data Manager blockchain můžete přidat více aplikací blockchain.


> [!IMPORTANT]
> V současné době nejsou aplikace blockchain, které deklarují [typy polí](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) soliding nebo [mapování typů](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) , plně podporovány. Vlastnosti deklarované jako pole nebo typy mapování nebudou v rámci zpráv *ContractPropertiesMsg* nebo *DecodedContractEventsMsg* dekódovat.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parametr | Popis |
|-----------|-------------|
| resource-group | Název skupiny prostředků, kde se má vytvořit prostředek aplikace |
| name | Název aplikace |
| namespace | Použijte obor názvů poskytovatele **Microsoft. blockchain** . |
| typ prostředku | Typ prostředku pro aplikaci blockchain Data Manager je **artefakty**. |
| nadřazený | Cesta ke sledovacímu procesu, ke kterému je aplikace přidružena Například sledovací procesy **/mywatcher**. |
| je-full-Object | Určuje, že vlastnosti obsahují možnosti pro prostředek aplikace. |
| properties | Řetězec ve formátu JSON obsahující vlastnosti prostředku aplikace Může být předán jako řetězec nebo jako soubor. |

### <a name="blockchain-application-examples"></a>Příklady aplikace blockchain

Příklad konfigurace JSON pro vytvoření prostředku aplikace v oblasti *východní USA* , která sleduje inteligentní kontrakt definovaný v kontraktu ABI a bytového kódu.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Element | Popis |
|---------|-------------|
| location | Oblast, kde se má vytvořit prostředek aplikace |
| artifactType | Typ aplikace V současné době se podporuje **EthereumSmartContract** . |
| abiFileUrl | Adresa URL pro soubor JSON pro inteligentní kontrakt Další informace o získání kontraktu ABI a vytvoření adresy URL najdete v tématu [získání kontraktu ABI a bytového kódu](data-manager-portal.md#get-contract-abi-and-bytecode) a [Vytvoření kontraktu ABI a adresy URL bytového kódu](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | Adresa URL souboru JSON pro bajtový identifikátor nasazené inteligentní smlouvy Další informace o získání nasazeného bajtového kódu inteligentního kontraktu a vytvoření adresy URL najdete v tématu [získání kontraktu ABI a bytového kódu](data-manager-portal.md#get-contract-abi-and-bytecode) a [Vytvoření kontraktu ABI a adresy URL bytového kódu](data-manager-portal.md#create-contract-abi-and-bytecode-url). Poznámka: blockchain Data Manager vyžaduje **nasazený bajt**. |
| queryTargetTypes | Typy publikovaných zpráv. Zadání **ContractProperties** publikuje typ zprávy *ContractPropertiesMsg* . Zadání **ContractEvents** publikuje typ zprávy *DecodedContractEventsMsg* . Poznámka: typy zpráv *RawBlockAndTransactionMsg* a *RawTransactionContractCreationMsg* jsou vždy publikovány. |

Vytvořte aplikaci s názvem *MyApplication* pro *mywatcher* , která sleduje inteligentní kontrakt definovaný řetězcem JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Vytvořte aplikaci s názvem *MyApplication* pro *mywatcher* , která sleduje inteligentní kontrakt definovaný pomocí konfiguračního souboru JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Spustit instanci

Když je spuštěná, instance blockchain Manager monitoruje události blockchain z definovaných vstupů a odesílá data do definovaných výstupů.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametr | Popis |
|-----------|-------------|
| akce | Spusťte sledovací proces pomocí rutiny **Start** . |
| identifikační | ID prostředku sledovacího procesu. \<Subscription ID\>Hodnoty, \<Resource group\> a nahraďte \<Watcher name\> hodnotou prostředku sledovacího procesu.|

### <a name="start-instance-example"></a>Příklad spuštění instance

Spusťte instanci blockchain Data Manager s názvem *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Zastavit instanci

Zastavte instanci blockchain Data Manager.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametr | Popis |
|-----------|-------------|
| akce | Pomocí **stop** zastavte sledovací proces. |
| identifikační | Název sledovacího procesu. \<Subscription ID\>Hodnoty, \<Resource group\> a nahraďte \<Watcher name\> hodnotou prostředku sledovacího procesu. |

### <a name="stop-watcher-example"></a>Příklad zastavení sledovacího procesu

Zastavte instanci s názvem *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Odstranit instanci

Odstraní instanci Data Manager blockchain.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parametr | Popis |
|-----------|-------------|
| resource-group | Název skupiny prostředků sledovacího procesu, který se má odstranit |
| name | Název sledovacího procesu, který se má odstranit |
| typ prostředku | Typ prostředku pro sledovací proces blockchain Data Manager je **Microsoft. blockchain/sledovací**procesy. |

### <a name="delete-instance-example"></a>Příklad odstranění instance

Odstraňte instanci s názvem *mywatcher* ve skupině prostředků *myRG* .

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Další kroky

Zkuste v dalším kurzu vytvořit blockchain Průzkumníka zpráv transakce pomocí blockchain Data Manager a Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Použití Blockchain Data Manageru k odesílání dat do Azure Cosmos DB](data-manager-cosmosdb.md)
