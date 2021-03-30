---
title: Vytvoření členu služby Azure blockchain – Azure CLI
description: Vytvoření členu služby Azure blockchain pro konsorcium blockchain pomocí Azure CLI.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3442c3b6023edcde97aabcb13e91120ba6811027
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323069"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Rychlý Start: Vytvoření člena blockchain služby Azure blockchain pomocí Azure CLI

V tomto rychlém startu nasadíte nového člena blockchain a konsorcia ve službě Azure blockchain pomocí Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Žádné

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.51 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Příprava prostředí

1. Přihlaste se.

    Pokud používáte místní instalaci rozhraní příkazového řádku, přihlaste se pomocí příkazu [az login](/cli/azure/reference-index#az-login).

    ```azurecli
    az login
    ```

    Dokončete proces ověřování podle kroků zobrazených v terminálu.

1. Nainstalujte rozšíření Azure CLI.

    Když pracujete s odkazy na rozšíření rozhraní příkazového řádku Azure (Azure CLI), musíte nejdřív rozšíření nainstalovat.  Rozšíření rozhraní příkazového řádku Azure poskytují přístup k experimentálním a předběžným příkazům, které ještě nebyly dodány jako součást základního rozhraní příkazového řádku.  Další informace o rozšířeních, včetně aktualizace a odinstalace, najdete v tématu [Využití rozšíření v Azure CLI](/cli/azure/azure-cli-extensions-overview).

    Nainstalujte [rozšíření pro službu Azure blockchain](/cli/azure/ext/blockchain/blockchain) spuštěním následujícího příkazu:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Vytvořte skupinu prostředků.

    Služba Azure blockchain, stejně jako všechny prostředky Azure, musí být nasazená do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

    V tomto rychlém startu vytvořte skupinu prostředků s názvem _myResourceGroup_ v umístění _eastus_ pomocí následujícího příkazu [AZ Group Create](/cli/azure/group#az-group-create) :

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Vytvoření členu blockchain

Člen služby Azure Blockchain je blockchain uzel v privátní síti konsorcia blockchain. Při zřizování člena můžete vytvořit nebo připojit se k síti konsorcia. Pro síť konsorcia potřebujete alespoň jednoho člena. Počet členů blockchain, které účastníci potřebují, závisí na vašem scénáři. Účastníci konsorcia mohou mít jednoho nebo více členů blockchain nebo mohou sdílet členy s ostatními účastníky. Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).

Existuje několik parametrů a vlastností, které je třeba předat. Nahraďte ukázkové parametry hodnotami.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. Použijte skupinu prostředků, kterou jste vytvořili v předchozí části.
| **Jméno** | Jedinečný název, který identifikuje svého člena blockchain služby Azure blockchain. Název se používá pro adresu veřejného koncového bodu. Například, `myblockchainmember.blockchain.azure.com`.
| **oblasti** | Oblast Azure, ve které je vytvořen člen blockchain. Například, `westus2`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure. Funkce nemusí být k dispozici v některých oblastech. Azure blockchain Data Manager je k dispozici v následujících oblastech Azure: Východní USA a Západní Evropa.
| **heslo** | Heslo pro výchozí uzel transakce člena Při připojování k výchozímu koncovému bodu transakčního uzlu blockchain člena použijte heslo pro základní ověřování.
| **protokol** | Protokol blockchain V současné době je protokol *kvora* podporován.
| **konsorci** | Název konsorcia, která se má připojit nebo vytvořit Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md).
| **Konsorcium-Správa-účet-heslo** | Heslo účtu konsorcia se označuje také jako heslo k členskému účtu. Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia.
| **skladové** | Typ vrstvy *Standard* nebo *Basic*. Využijte *základní* vrstvu pro vývoj, testování a kontrolu konceptů. Použijte úroveň *Standard* pro nasazení produkčních úrovní. Úroveň *Standard* použijte také v případě, že používáte blockchain data Manager nebo odesíláte velký objem privátních transakcí. Změna cenové úrovně mezi základními a standardními po vytvoření členů není podporována.

Vytvoření člena blockchain a podpůrných prostředků trvá přibližně 10 minut.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Můžete použít člen blockchain, který jste vytvořili pro další rychlý Start nebo kurz. Pokud už je nepotřebujete, můžete prostředky odstranit odstraněním `myResourceGroup` skupiny prostředků, kterou jste vytvořili pro rychlý Start.

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
