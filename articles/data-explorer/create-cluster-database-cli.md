---
title: Vytvoření clusteru Azure Data Explorer & DB pomocí rozhraní příkazového příkazu Azure
description: Zjistěte, jak vytvořit cluster a databázi Průzkumníka dat Azure pomocí rozhraní příkazového příkazu Azure
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 6b8c2924e50da095c3bc5c7db2d2bf48ef5a27c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561931"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Vytvoření clusteru a databáze Azure Data Explorer pomocí rozhraní příkazového příkazu Azure

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Arm šablona](create-cluster-database-resource-manager.md)

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Data Explorer, musíte nejdříve vytvořit cluster a v něm vytvořit jednu nebo více databází. Do databáze potom ingestujete (načtete) data, abyste se na ně mohli dotazovat spouštěním dotazů. V tomto článku vytvoříte cluster a databázi pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, tento článek vyžaduje Azure CLI verze 2.0.4 nebo novější. Spuštěním příkazu `az --version` zkontrolujte svou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Konfigurace parametrů rozhraní příkazového řádku

Následující kroky nejsou povinné, pokud používáte příkazy v Azure Cloud Shell. Pokud používáte příkazové příkazové příkazy místně, přihlaste se k Azure a nastavte aktuální předplatné takto:

1. Spuštěním následujícího příkazu se přihlaste k Azure:

    ```azurecli-interactive
    az login
    ```

1. Nastavte předplatné na místo, kde chcete vytvořit cluster. Nahraďte `MyAzureSub` název předplatného Azure, které chcete použít:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumníka dat Azure

1. Vytvořte cluster pomocí následujícího příkazu:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | jméno | *azurový* | Požadovaný název clusteru.|
   | Sku | *D13_v2* | Skladová položka, která bude použita pro váš cluster. |
   | resource-group | *testrg* | Název skupiny prostředků, kde bude vytvořen cluster. |

    Existují další volitelné parametry, které můžete použít, například kapacitu clusteru.

1. Spuštěním následujícího příkazu zkontrolujte, zda byl cluster úspěšně vytvořen:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Pokud výsledek `provisioningState` obsahuje `Succeeded` s hodnotou, byl cluster úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Průzkumníka dat Azure

1. Vytvořte databázi pomocí následujícího příkazu:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | název clusteru | *azurový* | Název clusteru, kde bude databáze vytvořena.|
   | jméno | *clidatabáze* | Název databáze.|
   | resource-group | *testrg* | Název skupiny prostředků, kde bude vytvořen cluster. |
   | období s odstraňováním a odstraněním | *P365D* | Označuje dobu, po kterou budou data k dispozici pro dotaz. Další informace naleznete v [zásadách uchovávání informací.](/azure/kusto/concepts/retentionpolicy) |
   | doba aktivní mezipaměti | *P31D* | Označuje dobu, po kterou budou data uložena v mezipaměti. Další informace naleznete v [zásadách mezipaměti.](/azure/kusto/concepts/cachepolicy) |

1. Chcete-li zobrazit databázi, kterou jste vytvořili, spusťte následující příkaz:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Nyní máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud máte v plánu sledovat naše další články, uchovávejte zdroje, které jste vytvořili.
* Chcete-li vyčistit prostředky, odstraňte cluster. Když odstraníte cluster, odstraní také všechny databáze v něm. K odstranění clusteru použijte následující příkaz:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Další kroky

* [Ingestování dat pomocí knihovny Pythonu Průzkumníka dat Azure](python-ingest-data.md)
