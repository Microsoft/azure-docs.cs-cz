---
title: Vytvoření členu služby Azure blockchain – Azure CLI
description: Vytvoření členu služby Azure blockchain pro konsorcium blockchain pomocí Azure CLI.
ms.date: 03/12/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 0a3cf3d7c7f3dc0b8ece6fd6a466e42ae970b61c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214738"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Rychlý Start: Vytvoření člena blockchain služby Azure blockchain pomocí Azure CLI

V tomto rychlém startu nasadíte nového člena blockchain a konsorcia ve službě Azure blockchain pomocí Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče na adrese [https://shell.azure.com/bash](https://shell.azure.com/bash). Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložit je do Cloud Shellu a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.51 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvořit skupinu prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Vytvoření členu blockchain

Člen služby Azure Blockchain je blockchain uzel v privátní síti konsorcia blockchain. Při zřizování člena můžete vytvořit nebo připojit se k síti konsorcia. Pro síť konsorcia potřebujete alespoň jednoho člena. Počet členů blockchain, které účastníci potřebují, závisí na vašem scénáři. Účastníci konsorcia mohou mít jednoho nebo více členů blockchain nebo mohou sdílet členy s ostatními účastníky. Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).

Existuje několik parametrů a vlastností, které je třeba předat. Nahraďte ukázkové parametry hodnotami.

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
| **Skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. Použijte skupinu prostředků, kterou jste vytvořili v předchozí části.
| **Jméno** | Jedinečný název, který identifikuje svého člena blockchain služby Azure blockchain. Název se používá pro adresu veřejného koncového bodu. například `myblockchainmember.blockchain.azure.com`.
| **location** | Oblast Azure, ve které je vytvořen člen blockchain. například `westus2`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure.
| **zadáno** | Heslo pro výchozí uzel transakce člena Při připojování k výchozímu koncovému bodu transakčního uzlu blockchain člena použijte heslo pro základní ověřování.
| **konsorci** | Název konsorcia, která se má připojit nebo vytvořit Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).
| **consortiumAccountPassword** | Heslo účtu konsorcia se označuje také jako heslo k členskému účtu. Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia.
| **skuName** | Typ vrstvy Pro Basic použijte S0 for Standard a B0. Využijte *základní* vrstvu pro vývoj, testování a kontrolu konceptů. Použijte úroveň *Standard* pro nasazení produkčních úrovní.

Vytvoření člena blockchain a podpůrných prostředků trvá přibližně 10 minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člen blockchain, který jste vytvořili pro další rychlý Start nebo kurz. Pokud už je nepotřebujete, můžete prostředky odstranit tak, že odstraníte `myResourceGroup` skupinu prostředků, kterou jste vytvořili pro rychlý Start.

Spuštěním následujícího příkazu odeberte skupinu prostředků a všechny související prostředky.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili člen služby Azure blockchain a nový konsorcium. Vyzkoušejte si další rychlý Start pro použití Azure blockchain Development Kit pro Ethereem pro připojení k členu služby Azure blockchain.

> [!div class="nextstepaction"]
> [Použití Visual Studio Code k připojení ke službě Azure blockchain](connect-vscode.md)
