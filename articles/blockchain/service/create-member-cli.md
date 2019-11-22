---
title: Vytvoření členu služby Azure blockchain – Azure CLI
description: Vytvoření členu služby Azure blockchain pro konsorcium blockchain pomocí Azure CLI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 894c6392c302e1be7c57b85f3f923ee1ba5467f6
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286930"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Rychlý Start: Vytvoření člena blockchain služby Azure blockchain pomocí Azure CLI

V tomto rychlém startu nasadíte nového člena blockchain a konsorcia ve službě Azure blockchain pomocí Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.51 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Vytvoření člena blockchainu

Vytvořte člena blockchain ve službě Azure blockchain, která spouští protokol hlavní knihy kvora v nové konsorcium. Existuje několik parametrů a vlastností, které je třeba předat. Nahraďte ukázkové parametry hodnotami.

```azurecli-interactive
az resource create \
                    --resource-group myResourceGroup \
                    --name myblockchainmember \
                    --resource-type Microsoft.Blockchain/blockchainMembers \
                    --is-full-object \
                    --properties '{"location":"westus2", "properties":{"password":"strongMemberAccountPassword@1", "protocol":"Quorum", "consortium":"myConsortiumName", "consortiumManagementAccountPassword":"strongConsortiumManagementPassword@1"}, "sku":{"name":"S0"}}'
```

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. Použijte skupinu prostředků, kterou jste vytvořili v předchozí části.
| **name** | Jedinečný název, který identifikuje svého člena blockchain služby Azure blockchain. Název se používá pro adresu veřejného koncového bodu. Například, `myblockchainmember.blockchain.azure.com`.
| **location** | Oblast Azure, ve které je vytvořen člen blockchain. Například, `westus2`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure.
| **Heslo** | Heslo pro výchozí uzel transakce člena Při připojování k výchozímu koncovému bodu transakčního uzlu blockchain člena použijte heslo pro základní ověřování.
| **consortium** | Název konsorcia, která se má připojit nebo vytvořit
| **consortiumAccountPassword** | Heslo účtu konsorcia se označuje také jako heslo k členskému účtu. Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia.
| **skuName** | Typ vrstvy Pro Basic použijte S0 for Standard a B0.

Vytvoření člena blockchain a podpůrných prostředků trvá přibližně 10 minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člen blockchain, který jste vytvořili pro další rychlý Start nebo kurz. Pokud už je nepotřebujete, můžete prostředky odstranit tak, že odstraníte `myResourceGroup` skupinu prostředků, kterou jste vytvořili ve službě Azure blockchain.

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny související prostředky.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili člen služby Azure blockchain a nový konsorcium. Vyzkoušejte si další rychlý Start pro použití Azure blockchain Development Kit pro Ethereem pro připojení k konsorciu ve službě Azure blockchain.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k připojení k síti konsorcia Azure blockchain](connect-vscode.md)
