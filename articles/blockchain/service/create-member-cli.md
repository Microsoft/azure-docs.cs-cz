---
title: Vytvoření člena služby Azure Blockchain – Azure CLI
description: Vytvořte člena služby Azure Blockchain Service pro konsorcium blockchain pomocí azure cli.
ms.date: 03/12/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 0a3cf3d7c7f3dc0b8ece6fd6a466e42ae970b61c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79214738"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Úvodní příručka: Vytvoření člena blockchainové služby Azure Blockchain service pomocí azure CLI

V tomto rychlém startu nasadíte nového člena blockchainu a konsorcia ve službě Azure Blockchain service pomocí azure cli.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné [https://shell.azure.com/bash](https://shell.azure.com/bash)kartě prohlížeče tak, že přejdete na . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a použití příkazového příkazu k místnímu použití, tento rychlý start vyžaduje Azure CLI verze 2.0.51 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete nainstalovat nebo upgradovat, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Vytvoření člena blockchainu

Člen služby Azure Blockchain Je uzel blockchainu v privátní blockchainové síti konsorcia. Při zřizování člena můžete vytvořit nebo se připojit k síti konsorcia. Potřebujete alespoň jednoho člena pro síť konsorcia. Počet členů blockchainu, které účastníci potřebují, závisí na vašem scénáři. Účastníci konsorcia mohou mít jednoho nebo více členů blockchainu nebo mohou členy sdílet s ostatními účastníky. Další informace o konsorciích najdete v [tématu Konsorcium služby Azure Blockchain Service](consortium.md).

Existuje několik parametrů a vlastností, které musíte předat. Nahraďte ukázkové parametry hodnotami.

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
| **skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure Blockchain Service. Použijte skupinu prostředků, kterou jste vytvořili v předchozí části.
| **Jméno** | Jedinečný název, který identifikuje vašeho člena blockchainové služby Azure Blockchain Service. Název se používá pro adresu veřejného koncového bodu. Například, `myblockchainmember.blockchain.azure.com`.
| **Umístění** | Oblast Azure, kde se vytvoří člen blockchainu. Například, `westus2`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure.
| **heslo** | Heslo pro výchozí uzel transakce člena. Heslo použijte pro základní ověřování při připojování k výchozímu koncovému bodu koncového uzlu výchozího transakčního uzlu člena blockchainu.
| **Konsorcium** | Název konsorcia připojit nebo vytvořit. Další informace o konsorciích najdete v [tématu Konsorcium služby Azure Blockchain Service](consortium.md).
| **konsorciumAccountPassword** | Heslo účtu konsorcia se také označuje jako heslo členského účtu. Heslo členského účtu se používá k šifrování soukromého klíče pro účet Ethereum, který je vytvořen pro vašeho člena. Pro správu konsorcia se používá heslo členského účtu a členského účtu.
| **skuName** | Typ úrovně. Použijte S0 pro standard a B0 pro základní. Vrstvu *Basic* použijte pro vývoj, testování a testování konceptů. Použijte *úroveň Standard* pro nasazení výrobní třídy.

Vytvoření člena blockchainu a podpůrných zdrojů trvá asi 10 minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člena blockchainu, který jste vytvořili, pro další rychlý start nebo kurz. Pokud již není potřeba, můžete odstranit prostředky `myResourceGroup` odstraněním skupiny prostředků, které jste vytvořili pro rychlý start.

Chcete-li odebrat skupinu prostředků a všechny související prostředky, spusťte následující příkaz.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili člena služby Azure Blockchain Service a nové konsorcium. Zkuste další rychlý start použít Azure Blockchain Development Kit pro ethereum připojit k členu služby Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Připojení ke službě Azure Blockchain Service pomocí kódu Visual Studia](connect-vscode.md)
