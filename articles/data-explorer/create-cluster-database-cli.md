---
title: 'Rychlý start: Vytvoření clusteru Průzkumník dat Azure a databáze pomocí rozhraní příkazového řádku'
description: V tomto rychlém startu se dozvíte, jak k vytvoření clusteru Průzkumník dat Azure a databáze pomocí Azure CLI
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 9e0ae547df34594674dc03702310a1537717a4ed
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881112"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-cli"></a>Vytvoření clusteru Průzkumník dat Azure a databáze pomocí rozhraní příkazového řádku

Tento rychlý start popisuje způsob vytvoření clusteru Průzkumník dat Azure a databáze pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure místně tento rychlý start vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Spuštěním příkazu `az --version` zkontrolujte svou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="configure-the-cli-parameters"></a>Konfigurovat parametry příkazového řádku

Pokud spouštíte příkazy ve službě Cloud Shell, následující kroky nemusíte provádět. Pokud používáte rozhraní příkazového řádku místně, provedením následujících kroků se přihlaste k Azure a nastavte své aktuální předplatné:

1. Spuštěním následujícího příkazu se přihlaste k Azure:

    ```azurecli-interactive
    az login
    ```

2. Nastavte předplatné, ve kterém chcete cluster vytvořit. Nahraďte `MyAzureSub` názvem předplatného Azure, které chcete použít:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumník dat Azure

1. Vytvoření clusteru pomocí následujícího příkazu:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | jméno | *azureclitest* | Požadovaný název vašeho clusteru.|
   | SKU | *D13_v2* | SKU, které se použije pro váš cluster. |
   | resource-group | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |

    Existují další volitelné parametry, které můžete použít, jako je například kapacita clusteru.

2. Spusťte následující příkaz a zkontrolujte, zda byl úspěšně vytvořen cluster:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Pokud výsledek obsahuje "provisioningState" s hodnotou "ÚSPĚCH", pak clusteru se úspěšně vytvořil.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v Průzkumníku dat Azure clusteru

1. Vytvoření databáze pomocí následujícího příkazu:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | Název clusteru | *azureclitest* | Název clusteru s novou databází.|
   | jméno | *clidatabase* | Požadovaný název vaší databáze.|
   | resource-group | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |
   | Konfigurace soft-delete období | *3650:00:00:00* | Množství času, které se mají data ukládat tak, aby byl k dispozici pro dotazy. |
   | Horká doby uložení v mezipaměti | *3650:00:00:00* | Množství času, které se mají data ukládat v mezipaměti. |

2. Spuštěním následujícího příkazu databáze, že kterou jste vytvořili:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud plánujete postupovat podle našich dalších rychlých startů a kurzů, vytvořené prostředky zachovejte.
* Pokud chcete vyčistit prostředky, cluster odstraňte. Po odstranění clusteru se odstraní také všechny databáze v ní. Pomocí následujícího příkazu odstraňte cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Ingestování dat pomocí knihovny Python Průzkumník dat Azure](python-ingest-data.md)
