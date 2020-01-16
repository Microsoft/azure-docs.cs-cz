---
title: Vytvoření clusteru a databáze Azure Průzkumník dat pomocí prostředí PowerShell
description: Naučte se vytvářet cluster a databázi Azure Průzkumník dat pomocí prostředí PowerShell.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: d4561d49c37298a2b1a7f6c6542d78c3e19a145c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978342"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Vytvoření clusteru a databáze Azure Průzkumník dat pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Šablona ARM](create-cluster-database-resource-manager.md)  

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. V tomto článku vytvoříte cluster a databázi pomocí prostředí PowerShell. Rutiny a skripty prostředí PowerShell můžete v systému Windows, Linux nebo v [Azure Cloud Shell](../cloud-shell/overview.md) spustit pomocí [AZ. Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) a vytvořit a nakonfigurovat clustery a databáze Azure Průzkumník dat.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, musíte mít Azure CLI verze 2.0.4 nebo novější. Spuštěním příkazu `az --version` zkontrolujte svou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Konfigurovat parametry

Následující kroky se nevyžadují, pokud spouštíte příkazy v Azure Cloud Shell. Pokud používáte rozhraní příkazového řádku místně, přihlaste se k Azure a nastavte aktuální předplatné podle kroků 1 & 2:

1. Spuštěním následujícího příkazu se přihlaste k Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Nastavte předplatné, ve kterém chcete vytvořit cluster:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Pokud používáte Azure CLI místně nebo v Azure Cloud Shell, musíte na svém zařízení nainstalovat modul AZ. Kusto:

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Azure Průzkumník dat

1. Vytvořte cluster pomocí následujícího příkazu:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | Name (Název) | *mykustocluster* | Požadovaný název clusteru.|
   | SKU | *D13_v2* | SKU, které bude použito pro váš cluster. |
   | ResourceGroupName | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |

    Existují další nepovinné parametry, které můžete použít, například kapacitu clusteru.

1. Spusťte následující příkaz a ověřte, zda byl cluster úspěšně vytvořen:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Pokud výsledek obsahuje `provisioningState` s hodnotou `Succeeded`, cluster se úspěšně vytvořil.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Azure Průzkumník dat

1. Vytvořte databázi pomocí následujícího příkazu:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | Název clusteru | *mykustocluster* | Název clusteru, ve kterém se databáze vytvoří.|
   | Name (Název) | *mykustodatabase* | Název vaší databáze.|
   | ResourceGroupName | *testrg* | Název skupiny prostředků, ve které se cluster vytvoří. |
   | SoftDeletePeriod | *3650:00:00:00* | Doba, po kterou budou data uchována k dispozici pro dotaz. |
   | HotCachePeriod | *3650:00:00:00* | Doba, po kterou budou data uchována v mezipaměti. |

1. Spuštěním následujícího příkazu zobrazíte databázi, kterou jste vytvořili:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud plánujete postupovat podle našich dalších článků, ponechejte prostředky, které jste vytvořili.
* Pokud chcete vyčistit prostředky, odstraňte cluster. Když odstraníte cluster, odstraní se i všechny jeho databáze. Pomocí následujícího příkazu odstraňte cluster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Další kroky

* [Další příkazy AZ. Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Ingestování dat pomocí sady Azure Průzkumník dat .NET Standard SDK (Preview)](net-standard-ingest-data.md)
