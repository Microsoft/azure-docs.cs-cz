---
title: Konfigurace blockchain Data Manager pomocí Azure CLI – Azure blockchain Service
description: Vytvoření a Správa blockchain Data Manager pro službu Azure blockchain pomocí Azure CLI
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455936"
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

* Nainstalujte nejnovější rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) a přihlaste se pomocí `az login`.
* Kompletní [rychlé zprovoznění: použití Visual Studio Code pro připojení k síti konsorcia Azure blockchain](connect-vscode.md)
* Vytvoření [tématu Event Grid](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Další informace o [obslužných rutinách událostí v Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.51 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Vytvořit instanci

Instance blockchain Data Manager monitoruje uzel transakcí služby Azure blockchain. Instance zachycuje všechna nepracovaná data bloku a nezpracovaná transakce z uzlu transakce.

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
| jméno | Název instance Data Manager blockchain |
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

| Prvek | Popis |
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

## <a name="create-input"></a>Vytvořit zadání

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
| jméno | Název vstupu |
| Obor názvů | Použijte obor názvů poskytovatele **Microsoft. blockchain** . |
| typ prostředku | Typ prostředku pro vstup Data Manager blockchain jsou **vstupy**. |
| nadřazený | Cesta ke sledovacímu procesu, ke kterému je vstup přidružen. Například sledovací procesy **/mywatcher**. |
| je-full-Object | Určuje vlastnosti, které obsahují možnosti pro vstupní prostředek. |
| properties | Řetězec ve formátu JSON, který obsahuje vlastnosti pro vstupní prostředek. Může být předán jako řetězec nebo jako soubor. |

### <a name="input-examples"></a>Příklady vstupu

Příklad konfigurace JSON pro vytvoření vstupního prostředku v oblasti *východní USA* , která je připojená k \<mu členskému\>blockchain.

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

| Prvek | Popis |
|---------|-------------|
| location | Oblast, kde se má vytvořit vstupní prostředek |
| inputType | Typ hlavní knihy člena služby Azure blockchain. V současné době se podporuje **ethereem** . |
| resourceId | Uzel transakce, ke kterému je vstup připojen Nahraďte\>ID \<předplatného, \<skupinu prostředků\>a \<blockchain členů\> hodnotami pro prostředek uzlu transakce. Vstup se připojí k výchozímu uzlu transakce pro člena služby Azure blockchain. |

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
| jméno | Název výstupu. |
| Obor názvů | Použijte obor názvů poskytovatele **Microsoft. blockchain** . |
| typ prostředku | Typ prostředku pro výstup blockchain Data Manager je **výstupy**. |
| nadřazený | Cesta ke sledovacímu procesu, ke kterému je výstup přidružen Například sledovací procesy **/mywatcher**. |
| je-full-Object | Určuje, že vlastnosti obsahují možnosti pro výstupní prostředek. |
| properties | Řetězec ve formátu JSON obsahující vlastnosti výstupního prostředku. Může být předán jako řetězec nebo jako soubor. |

### <a name="output-examples"></a>Příklady výstupu

Příklad konfigurace JSON pro vytvoření výstupního prostředku v oblasti *východní USA* , která je připojená k tématu služby Event Grid s názvem \<\>.

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

| Prvek | Popis |
|---------|-------------|
| location | Oblast, ve které se má vytvořit výstupní prostředek. |
| outputType | Typ výstupu. V současné době se podporuje **EventGrid** . |
| resourceId | Prostředek, ke kterému je připojen výstup. Nahraďte\>ID \<předplatného, \<skupinu prostředků\>a \<blockchain členů\> s hodnotami pro prostředek Event gridu. |

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
| jméno | Název aplikace |
| Obor názvů | Použijte obor názvů poskytovatele **Microsoft. blockchain** . |
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

| Prvek | Popis |
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
| action | Spusťte sledovací proces pomocí rutiny **Start** . |
| identifikační | ID prostředku sledovacího procesu. Nahraďte \<ID předplatného\>, \<skupinu prostředků\>a \<název sledovacího procesu\> s hodnotami prostředku sledovacího procesu.|

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
| action | Pomocí **stop** zastavte sledovací proces. |
| identifikační | Název sledovacího procesu. Nahraďte \<ID předplatného\>, \<skupinu prostředků\>a \<název sledovacího procesu\> s hodnotami prostředku sledovacího procesu. |

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
| jméno | Název sledovacího procesu, který se má odstranit |
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
> [Použití Data Manager blockchain k odesílání dat do Azure Cosmos DB](data-manager-cosmosdb.md)
