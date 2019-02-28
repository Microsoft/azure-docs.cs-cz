---
title: 'Rychlý start: Vytvoření clusteru Průzkumník dat Azure a databáze pomocí rozhraní příkazového řádku'
description: Zjistěte, jak vytvořit cluster Průzkumník dat Azure a databáze služby pomocí rozhraní příkazového řádku Azure
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 357f0efcf7300545d10113c92702d9fed4aad049
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958010"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-the-cli"></a>Vytvoření clusteru Průzkumník dat Azure a databáze pomocí rozhraní příkazového řádku

Tento rychlý start popisuje vytvoření clusteru Průzkumník dat Azure a databáze pomocí rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, tento rychlý start vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Spuštěním příkazu `az --version` zkontrolujte svou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Konfigurovat parametry příkazového řádku

Následující kroky nejsou nutné, pokud spouštíte příkazy ve službě Azure Cloud Shell. Pokud používáte rozhraní příkazového řádku místně, postupujte podle těchto kroků pro přihlášení do Azure a nastavit aktuální předplatné:

1. Spuštěním následujícího příkazu se přihlaste k Azure:

    ```azurecli-interactive
    az login
    ```

2. Nastavte předplatné, ve kterém chcete vašeho clusteru, který se má vytvořit. Nahraďte `MyAzureSub` s názvem předplatné Azure, kterou chcete použít:

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
   | SKU | *D13_v2* | Skladová položka, která se použije pro váš cluster. |
   | resource-group | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |

    Existují další volitelné parametry, které můžete použít, jako je například kapacita clusteru.

2. Spusťte následující příkaz a zkontrolujte, zda byl úspěšně vytvořen cluster:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Pokud výsledek obsahuje `provisioningState` s `Succeeded` hodnotu, pak clusteru byl úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v Průzkumníku dat Azure clusteru

1. Vytvoření databáze pomocí následujícího příkazu:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | Název clusteru | *azureclitest* | Název clusteru s novou databází.|
   | jméno | *clidatabase* | Název databáze.|
   | resource-group | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |
   | Konfigurace soft-delete období | *3650:00:00:00* | Množství času, které data zůstanou k dispozici pro dotazy. |
   | Horká doby uložení v mezipaměti | *3650:00:00:00* | Množství času, které data zůstanou v mezipaměti. |

2. Spuštěním následujícího příkazu zobrazte databázi, kterou jste vytvořili:

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
