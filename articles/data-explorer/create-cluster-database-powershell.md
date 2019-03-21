---
title: 'Rychlý start: Vytvoření clusteru Průzkumník dat Azure a databáze pomocí prostředí PowerShell'
description: Zjistěte, jak vytvořit cluster Průzkumník dat Azure a databáze služby pomocí prostředí PowerShell
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287506"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Vytvoření clusteru Průzkumník dat Azure a databáze pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Tento rychlý start popisuje způsob vytvoření clusteru Průzkumník dat Azure a databáze pomocí prostředí PowerShell.

Spuštěním rutin a skriptů Powershellu na Windows, Linux, nebo v [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) vytvořit a nakonfigurovat [Průzkumník dat Azure](https://docs.microsoft.com/azure/kusto/ ).

The [**Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto ). Pomocí Azure Powershellu a **Az.Kusto**, můžete provádět následující úlohy:

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="configure-parameters"></a>Konfigurovat parametry

Následující kroky nejsou nutné, pokud spouštíte příkazy ve službě Azure Cloud Shell. Pokud používáte rozhraní příkazového řádku místně, postupujte podle těchto kroků pro přihlášení do Azure a nastavit aktuální předplatné:

1. Spuštěním následujícího příkazu se přihlaste k Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Nastavte předplatné, ve kterém chcete vašeho clusteru, který se má vytvořit.

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Instalace modulu Az.Kusto na vašem zařízení:
    
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

2. Spusťte následující příkaz a zkontrolujte, zda byl úspěšně vytvořen cluster:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
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

2. Spuštěním následujícího příkazu zobrazte databázi, kterou jste vytvořili:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

Teď máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud plánujete postupovat podle našich dalších rychlých startů a kurzů, vytvořené prostředky zachovejte.
* Pokud chcete vyčistit prostředky, cluster odstraňte. Po odstranění clusteru se odstraní také všechny databáze v ní. Pomocí následujícího příkazu odstraňte cluster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Další postup

Můžete najít další příkazy Az.Kusto [ **zde**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Příjem dat s využitím dat Explorer .NET Standard SDK služby Azure (Preview)](net-standard-ingest-data.md)
