---
title: Vytvoření clusteru a databáze Azure Průzkumník dat pomocí Azure CLI
description: Naučte se vytvářet cluster a databázi Azure Průzkumník dat pomocí Azure CLI.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: bd53a8e29254af617b6cfa68935a191a50fc526c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326771"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Vytvoření clusteru a databáze Azure Průzkumník dat pomocí Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Šablona ARM](create-cluster-database-resource-manager.md)

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. V tomto článku vytvoříte cluster a databázi pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, musíte mít Azure CLI verze 2.0.4 nebo novější. Spuštěním příkazu `az --version` zkontrolujte svou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Konfigurace parametrů rozhraní příkazového řádku

Následující kroky se nevyžadují, pokud spouštíte příkazy v Azure Cloud Shell. Pokud používáte rozhraní příkazového řádku místně, přihlaste se k Azure a nastavte aktuální předplatné pomocí těchto kroků:

1. Spuštěním následujícího příkazu se přihlaste k Azure:

    ```azurecli-interactive
    az login
    ```

1. Nastavte předplatné, ve kterém chcete cluster vytvořit. Nahraďte `MyAzureSub` názvem předplatného Azure, které chcete použít:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Azure Průzkumník dat

1. Vytvořte cluster pomocí následujícího příkazu:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | name | *azureclitest* | Požadovaný název clusteru.|
   | SKU | *D13_v2* | SKU, které bude použito pro váš cluster. |
   | resource-group | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |

    Existují další nepovinné parametry, které můžete použít, například kapacitu clusteru.

1. Spusťte následující příkaz a ověřte, zda byl cluster úspěšně vytvořen:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Pokud výsledek obsahuje `provisioningState` `Succeeded` hodnotu, cluster se úspěšně vytvořil.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Azure Průzkumník dat

1. Vytvořte databázi pomocí následujícího příkazu:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | název clusteru | *azureclitest* | Název clusteru, ve kterém se databáze vytvoří.|
   | name | *clidatabase* | Název vaší databáze.|
   | resource-group | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |
   | Obnovitelné odstranění – období | *P365D* | Určuje dobu, po kterou budou data udržována k dispozici pro dotaz. Další informace najdete v tématu [zásady uchovávání](/azure/kusto/concepts/retentionpolicy) informací. |
   | Hot cache – období | *P31D* | Označuje dobu, po kterou budou data uchována v mezipaměti. Další informace najdete v tématu [zásady mezipaměti](/azure/kusto/concepts/cachepolicy) . |

1. Spuštěním následujícího příkazu zobrazíte databázi, kterou jste vytvořili:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud plánujete postupovat podle našich dalších článků, ponechejte prostředky, které jste vytvořili.
* Pokud chcete vyčistit prostředky, odstraňte cluster. Když odstraníte cluster, odstraní se i všechny jeho databáze. Pomocí následujícího příkazu odstraňte cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí knihovny Pythonu v Azure Průzkumník dat](python-ingest-data.md)
