---
title: Vytvoření služby Azure Blockchain pomocí Azure CLI
description: Použijte Azure Blockchain k vytvoření členu blockchainu pomocí rozhraní příkazového řádku Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 88b0336d7be1852d1bb2ae522f3d37ebed8d1fa0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026925"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Rychlý start: Vytvořit člena blockchain služba Blockchain v Azure pomocí Azure CLI

Služba Azure Blockchain je blockchain platformu, která vám pomůže provádět obchodní logiku v rámci inteligentní kontraktu. V tomto rychlém startu se dozvíte, jak začít vytvořením blockchain členu pomocí Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud chcete nainstalovat a používat rozhraní příkazového řádku místně, tento rychlý start vyžaduje použití Azure CLI verze 2.0.51 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [nainstalovat rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>Vytvoření blockchainové členu

Vytvořte člena blockchain v Azure Blockchain Service, na kterém běží hlavní knihy protokol kvora v nové consortium.

Existuje několik parametry a vlastnosti, které je potřeba předat. Nahraďte hodnoty následujících parametrů.

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Název skupiny prostředků, ve kterém jsou vytvořeny prostředky služeb Azure Blockchain. Použijte skupinu prostředků, kterou jste vytvořili v předchozí části.
| **name** | Jedinečný název, který identifikuje vaši službu Azure Blockchain blockchain člena. Název se používá pro adresu veřejný koncový bod. Například, `myblockchainmember.blockchain.azure.com`.
| **location** | Oblasti Azure, ve kterém je vytvořena blockchain člena. Například, `eastus`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure.
| **Heslo** | Heslo účtu člena. Člen heslo účtu se používá k ověření na člen blockchain veřejný koncový bod pomocí základního ověřování.
| **consortium** | Název consortium připojit nebo vytvořit.
| **consortiumManagementAccountPassword** | Heslo pro správu consortium. Používá se pro připojení k konsorcium.
| **skuName** | Typ vrstvy. Použijte S0 Standard a B0 Basic.

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties '{ "location": "eastus", "properties": {"password": "strongMemberAccountPassword@1", "protocol": "Quorum", "consortium": "myConsortiumName", "consortiumManagementAccountPassword": "strongConsortiumManagementPassword@1" }, "sku": { "name": "S0" } }'
```

Vytvoření člen blockchain a podpůrných prostředků trvá asi 10 minut.

Následující příklad výstupu ukazuje úspěšného operace vytvoření.

```json
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/mymembername",
  "kind": null,
  "location": "eastus",
  "name": "mymembername",
  "properties": {
    "ConsortiumMemberDisplayName": "mymembername",
    "consortium": "myConsortiumName",
    "consortiumManagementAccountAddress": "0xfe5fbb9d1036298abf415282f52397ade5d5beef",
    "consortiumManagementAccountPassword": null,
    "consortiumRole": "ADMIN",
    "dns": "mymembername.blockchain.azure.com",
    "protocol": "Quorum",
    "provisioningState": "Succeeded",
    "userName": "mymembername",
    "validatorNodesSku": {
      "capacity": 2
    }
  },
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "S0",
    "tier": "Standard"
  },
  "type": "Microsoft.Blockchain/blockchainMembers"
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pro další rychlý start nebo kurz, můžete použít blockchain člena, který jste vytvořili. Pokud už nepotřebujete prostředky můžete odstranit tak, že odstraníte `myResourceGroup` skupiny prostředků vytvořené službou Azure Blockchain.

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

Teď, když člen blockchain, kterou jste vytvořili, zkuste použít jeden z připojení k transakci uzel šablon rychlý start pro [Geth](connect-geth.md), [MetaMask](connect-metamask.md), nebo [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Použít pro připojení k Truffle síť služby Azure Blockchain](connect-truffle.md)
