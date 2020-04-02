---
title: Konfigurace Správce dat blockchainu pomocí rozhraní Příkazového příkazového příkazu Azure – služba Azure Blockchain Service
description: Vytvoření a správa správce blockchainových dat pro službu Azure Blockchain pomocí azure cli
ms.date: 03/30/2020
ms.topic: article
ms.reviewer: ravastra
ms.openlocfilehash: 5ceca96f760ab62ca7f3df9ad26139b9b4a3e5be
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529593"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Konfigurace Blockchain Data Manageru s využitím Azure CLI

Nakonfigurujte Správce blockchainových dat pro službu Azure Blockchain service tak, aby zaznamenávala data blockchainu, odesílejte je do tématu Sítě událostí Azure.

Chcete-li nakonfigurovat instanci Správce dat blockchainu, můžete:

* Vytvoření instance Správce blockchainu
* Vytvoření vstupu do transakčního uzlu služby Azure Blockchain Service
* Vytvoření výstupu do tématu mřížky událostí Azure
* Přidání blockchainové aplikace
* Spuštění instance

## <a name="prerequisites"></a>Požadavky

* Nainstalujte nejnovější [azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) `az login`a přihlášení pomocí .
* Kompletní [úvodní příručka: Pomocí kódu Visual Studia se můžete připojit k síti konsorcia Azure Blockchain Service](connect-vscode.md). Úroveň Azure Blockchain Service *Standard* se doporučuje při použití Správce dat blockchainu.
* Vytvoření [tématu mřížky událostí](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informace o [obslužných rutinách událostí v Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/bash](https://shell.azure.com/bash)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a použití příkazového příkazu k místnímu použití, tento rychlý start vyžaduje Azure CLI verze 2.0.51 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete nainstalovat nebo upgradovat, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Vytvořit instanci

Instance Správce dat Blockchain monitoruje transakční uzel služby Azure Blockchain Service. Instance zachytí všechna nezpracovaná data bloku a nezpracovaných transakcí z uzlu transakce.

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
| resource-group | Název skupiny prostředků, kde se má vytvořit instance Správce dat blockchainu. |
| jméno | Název instance Správce dat blockchainu. |
| typ prostředku | Typ prostředku pro instanci Správce dat blockchainu je **Microsoft.blockchain/watchers**. |
| je-full-objekt | Označuje, že vlastnosti obsahují možnosti pro prostředek sledovacího procesu. |
| properties | Řetězec ve formátu JSON obsahující vlastnosti prostředku sledovacího procesu. Může být předán jako řetězec nebo soubor.  |

### <a name="create-instance-examples"></a>Vytvořit příklady instancí

Příklad konfigurace JSON pro vytvoření instance Správce blockchainu v oblasti **Východní USA.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Element | Popis |
|---------|-------------|
| location | Oblast, kde chcete vytvořit prostředek sledovacího procesu |
| properties | Vlastnosti, které chcete nastavit při vytváření prostředku sledovacího procesu |

Vytvořte instanci Správce dat blockchain s názvem *mywatcher* pomocí řetězce JSON pro konfiguraci.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Vytvořte instanci Správce dat blockchainu s názvem *mywatcher* pomocí konfiguračního souboru JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Vytvořit vstup

Vstup propojuje Správce blockchainových dat s transakčním uznamem služby Azure Blockchain Service. Připojení mohou vytvořit pouze uživatelé s přístupem k uzlu transakce.

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
| resource-group | Název skupiny prostředků, kde se má vstupní prostředek vytvořit. |
| jméno | Název vstupu. |
| namespace | Použijte obor názvů poskytovatele **Microsoft.Blockchain.** |
| typ prostředku | Typ prostředku pro vstup Správce dat blockchainu je **vstupy**. |
| Nadřazené | Cesta k sledovacímu procesu, ke kterému je vstup přidružen. Například **pozorovatelé/ mywatcher**. |
| je-full-objekt | Označuje, že vlastnosti obsahují možnosti vstupního prostředku. |
| properties | Řetězec ve formátu JSON obsahující vlastnosti vstupního prostředku. Může být předán jako řetězec nebo soubor. |

### <a name="input-examples"></a>Vstupní příklady

Konfigurace JSON příklad vytvořit vstupní prostředek v oblasti *východní USA,* který je připojen k \<blockchain člen\>.

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
| location | Oblast, kde chcete vytvořit vstupní prostředek. |
| inputType | Typ hlavní knihy člena služby Azure Blockchain Service. V současné době je **podporováno ethereum.** |
| resourceId | Transakční uzel, ke kterému je vstup připojen. \<Nahraďte\>ID\>předplatného \<, \<\> skupinu prostředků a člena blockchain u hodnot prostředku transakčního uzlu. Vstup se připojí k výchozímu uzlu transakce pro člena služby Azure Blockchain Service. |

Vytvořte vstup s názvem *myInput* pro *mywatcher* pomocí řetězce JSON pro konfiguraci.

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

Odchozí připojení odesílá data blockchainu do Azure Event Grid. Data blockchainu můžete odesílat do jednoho cíle nebo data blockchainu do více destinací. Blockchain Data Manager podporuje více odchozích připojení Event Grid Topic pro danou instanci Blockchain Data Manager.

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
| resource-group | Název skupiny prostředků, kde se má vytvořit výstupní prostředek. |
| jméno | Název výstupu. |
| namespace | Použijte obor názvů poskytovatele **Microsoft.Blockchain.** |
| typ prostředku | Typ prostředku pro výstup Správce dat Blockchain je **výstupy**. |
| Nadřazené | Cesta k sledovacímu procesu, ke kterému je výstup přidružen. Například **pozorovatelé/ mywatcher**. |
| je-full-objekt | Označuje, že vlastnosti obsahují možnosti výstupního prostředku. |
| properties | Řetězec ve formátu JSON obsahující vlastnosti výstupního prostředku. Může být předán jako řetězec nebo soubor. |

### <a name="output-examples"></a>Příklady výstupu

Konfigurační příklad JSON pro vytvoření výstupního prostředku v oblasti \<USA –\> *východ,* který je připojen k tématu mřížky událostí s názvem téma mřížky událostí .

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
| location | Oblast, kde chcete vytvořit výstupní prostředek. |
| outputType | Typ výstupu. V současné době je **podporována eventgrid.** |
| resourceId | Prostředek, ke kterému je připojen výstup. \<Nahraďte\>ID\>předplatného \<, \<\> skupinu prostředků a člena blockchainu hodnotami pro prostředek mřížky událostí. |

Vytvořte výstup s názvem *myoutput* pro *mywatcher,* který se připojuje k tématu mřížky událostí pomocí konfiguračního řetězce JSON.

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

Vytvořte výstup s názvem *myoutput* pro *mywatcher,* který se připojuje k tématu mřížky událostí pomocí konfiguračního souboru JSON.

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

## <a name="add-blockchain-application"></a>Přidání blockchainové aplikace

Pokud přidáte blockchainovou aplikaci, Blockchain Data Manager dekóduje stav události a vlastnosti pro aplikaci. V opačném případě jsou odeslána pouze nezpracovaná data bloku a nezpracovaných transakcí. Blockchain Data Manager také zjišťuje adresy smluv při nasazení smlouvy. Do instance Správce dat Blockchain můžete přidat více blockchainových aplikací.


> [!IMPORTANT]
> V současné době nejsou plně podporovány blockchainové aplikace, které deklarují [typy polí](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) Solidity nebo [typy mapování.](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) Vlastnosti deklarované jako typy polí nebo mapování nebudou dekódovány ve *zprávách ContractPropertiesMsg* nebo *DecodedContractEventsMsg.*

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
| resource-group | Název skupiny prostředků, kde se má prostředek aplikace vytvořit. |
| jméno | Název aplikace. |
| namespace | Použijte obor názvů poskytovatele **Microsoft.Blockchain.** |
| typ prostředku | Typ prostředku pro aplikaci Blockchain Data Manager jsou **artefakty**. |
| Nadřazené | Cesta k sledovacímu procesu, ke kterému je aplikace přidružena. Například **pozorovatelé/ mywatcher**. |
| je-full-objekt | Označuje, že vlastnosti obsahují možnosti pro prostředek aplikace. |
| properties | Řetězec ve formátu JSON obsahující vlastnosti prostředku aplikace. Může být předán jako řetězec nebo soubor. |

### <a name="blockchain-application-examples"></a>Příklady aplikací blockchain

Konfigurace JSON příklad vytvořit prostředek aplikace v oblasti *usa – východ,* který monitoruje inteligentní kontrakt definovaný smlouvou ABI a bytecode.

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
| location | Oblast, kde chcete vytvořit prostředek aplikace. |
| artifactType | Typ aplikace. V současné době **ethereumSmartContract** je podporován. |
| abiFileUrl | ADRESA URL pro inteligentní kontrakt ABI JSON soubor. Další informace o získání smlouvy ABI a vytvoření adresy URL naleznete v tématu [Získání smlouvy ABI a bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) a [Vytvořit smlouvu ABI a adresu URL bytecode](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| adrese bytecodeFileUrl | Adresa URL pro inteligentní kontrakt nasazený soubor JSON bajtového kódu. Další informace o získání inteligentní smlouvy nasazený bytecode a vytvoření adresy URL naleznete v [tématu Získání smlouvy ABI a bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) a [Vytvořit smlouvy ABI a adresu URL bytecode](data-manager-portal.md#create-contract-abi-and-bytecode-url). Poznámka: Správce blockchainových dat vyžaduje **nasazený bytecode**. |
| queryTargetTypes | Publikované typy zpráv. Zadání **Vlastnosti smlouvy** publikuje Typ zprávy *ContractPropertiesMsg.* Zadání **ContractEvents** publikuje *DecodedContractEventsMsg* typ zprávy. Poznámka: *RawBlockAndTransactionMsg* a *RawTransactionContractCreationMsg* typy zpráv jsou vždy publikovány. |

Vytvořte aplikaci s názvem *myApplication* pro *mywatcher,* která monitoruje inteligentní kontrakt definovaný řetězcem JSON.

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

Vytvořte aplikaci s názvem *myApplication* pro *mywatcher,* která sleduje inteligentní kontrakt definovaný pomocí konfiguračního souboru JSON.

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

## <a name="start-instance"></a>Počáteční instance

Při spuštění instance Blockchain Manager monitoruje události blockchainu z definovaných vstupů a odesílá data do definovaných výstupů.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametr | Popis |
|-----------|-------------|
| action | Pomocí **funkce Start** spusťte sledovací proces. |
| Id | ID prostředku sledovacího procesu. \<Nahraďte\>ID\>předplatného \<, \<\> skupinu prostředků a název sledovacího procesu hodnotami prostředku sledovacího procesu.|

### <a name="start-instance-example"></a>Příklad instance Start

Spusťte instanci Správce dat blockchainu s názvem *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Instance Stop

Zastavte instanci Správce dat blockchainu.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametr | Popis |
|-----------|-------------|
| action | Pomocí **stop** zastavit sledovací ho. |
| Id | Jméno pozorovatele. \<Nahraďte\>ID\>předplatného \<, \<\> skupinu prostředků a název sledovacího procesu hodnotami prostředku sledovacího procesu. |

### <a name="stop-watcher-example"></a>Příklad sledování stop

Zastavit instanci s názvem *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Odstranit instanci

Odstraňte instanci Správce dat blockchainu.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parametr | Popis |
|-----------|-------------|
| resource-group | Název skupiny prostředků sledovacího procesu, který má být odstraněn. |
| jméno | Název sledovacího procesu, který má být odstraněn. |
| typ prostředku | Typ prostředku pro sledování Správce dat blockchainu je **Microsoft.blockchain/watchers**. |

### <a name="delete-instance-example"></a>Odstranit příklad instance

Odstraňte instanci s názvem *mywatcher* ve skupině prostředků *myRG.*

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Další kroky

Vyzkoušejte další kurz, který vytvoří průzkumník zpráv o transakcích blockchainu pomocí Správce dat Blockchain a Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Použití Blockchain Data Manageru k odesílání dat do Azure Cosmos DB](data-manager-cosmosdb.md)
