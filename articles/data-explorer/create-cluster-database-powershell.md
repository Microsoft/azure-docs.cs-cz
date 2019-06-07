---
title: Vytvoření clusteru Průzkumník dat Azure a databáze pomocí prostředí PowerShell
description: Zjistěte, jak vytvořit cluster Průzkumník dat Azure a databáze služby pomocí prostředí PowerShell
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c8cc81b3c61e2faf65bf8a06c69ca7f2b906c9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497330"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Vytvoření clusteru Průzkumník dat Azure a databáze pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Použití Průzkumníku dat Azure, nejprve vytvoříte cluster a vytvořit jednu nebo více databází v tomto clusteru. Pak můžete ingestovat data (načíst) do databáze tak, aby u ní můžete spouštět dotazy. V tomto článku se vytvoření clusteru a databáze pomocí prostředí Powershell. Spuštěním rutin a skriptů Powershellu na Windows, Linux, nebo v [Azure Cloud Shell](../cloud-shell/overview.md) s [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) vytvoření a konfigurace clusterů Průzkumníka služby Azure Data a databáze.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte mít Azure CLI verze 2.0.4 nebo novější. Spuštěním příkazu `az --version` zkontrolujte svou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Konfigurovat parametry

Následující kroky nejsou nutné, pokud spouštíte příkazy ve službě Azure Cloud Shell. Pokud používáte rozhraní příkazového řádku místně, postupujte podle kroků 1 a 2 pro přihlášení do Azure a nastavit aktuální předplatné:

1. Spuštěním následujícího příkazu se přihlaste k Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Nastavte předplatné, ve kterém chcete vytvoření clusteru:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Při spuštění rozhraní příkazového řádku Azure, místně nebo ve službě Azure Cloud Shell, musíte nainstalovat modul Az.Kusto na vašem zařízení:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumník dat Azure

1. Vytvoření clusteru pomocí následujícího příkazu:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | Název | *mykustocluster* | Požadovaný název vašeho clusteru.|
   | Skladová jednotka (SKU) | *D13_v2* | Skladová položka, která se použije pro váš cluster. |
   | ResourceGroupName | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |

    Existují další volitelné parametry, které můžete použít, jako je například kapacita clusteru.

1. Spusťte následující příkaz a zkontrolujte, zda byl úspěšně vytvořen cluster:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Pokud výsledek obsahuje `provisioningState` s `Succeeded` hodnotu, pak clusteru byl úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v Průzkumníku dat Azure clusteru

1. Vytvoření databáze pomocí následujícího příkazu:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | Název clusteru | *mykustocluster* | Název clusteru s novou databází.|
   | Název | *mykustodatabase* | Název databáze.|
   | ResourceGroupName | *testrg* | Název skupiny prostředků, ve kterém se cluster vytvoří. |
   | SoftDeletePeriod | *3650:00:00:00* | Množství času, které data zůstanou k dispozici pro dotazy. |
   | HotCachePeriod | *3650:00:00:00* | Množství času, které data zůstanou v mezipaměti. |

1. Spuštěním následujícího příkazu zobrazte databázi, kterou jste vytvořili:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud budete chtít postupujte podle našich článků, zachovat prostředky, které jste vytvořili.
* Pokud chcete vyčistit prostředky, cluster odstraňte. Po odstranění clusteru se odstraní také všechny databáze v ní. Pomocí následujícího příkazu odstraňte cluster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Další postup

* [Další příkazy Az.Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Příjem dat s využitím dat Explorer .NET Standard SDK služby Azure (Preview)](net-standard-ingest-data.md)
