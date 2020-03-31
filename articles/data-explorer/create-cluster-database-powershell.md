---
title: Vytvoření clusteru Průzkumníka dat Azure & DB pomocí Powershellu
description: Zjistěte, jak vytvořit cluster a databázi Průzkumníka dat Azure pomocí PowerShellu
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 690c3e281e65f54f240c70f7a6e5038f54102c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560588"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Vytvoření clusteru a databáze Průzkumníka dat Azure pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Arm šablona](create-cluster-database-resource-manager.md)  

Azure Data Explorer je rychlá, plně spravovaná služba analýzy dat pro analýzy velkých objemů dat v reálném čase, která se streamují z aplikací, webů, zařízení IoT a dalších. Pokud chcete použít Azure Data Explorer, musíte nejdříve vytvořit cluster a v něm vytvořit jednu nebo více databází. Do databáze potom ingestujete (načtete) data, abyste se na ně mohli dotazovat spouštěním dotazů. V tomto článku vytvoříte cluster a databázi pomocí prostředí Powershell. Pomocí [aplikace Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) můžete spouštět rutiny a skripty prostředí PowerShell ve Windows, Linuxu nebo Azure [Cloud Shellu](../cloud-shell/overview.md) a vytvářet a konfigurovat clustery a databáze Azure Data Exploreru.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně, tento článek vyžaduje Azure CLI verze 2.0.4 nebo novější. Spuštěním příkazu `az --version` zkontrolujte svou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Konfigurace parametrů

Následující kroky nejsou povinné, pokud používáte příkazy v Azure Cloud Shell. Pokud používáte příkazové příkazové příkazy místně, postupujte podle kroků 1 & 2, abyste se přihlásili k Azure a nastavili aktuální předplatné:

1. Spuštěním následujícího příkazu se přihlaste k Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Nastavte předplatné na místo, kde chcete vytvořit cluster:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Když běžíazure CLI místně nebo v Prostředí Azure Cloud Shell, musíte nainstalovat modul Az.Kusto na vašem zařízení:

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Vytvoření clusteru Průzkumníka dat Azure

1. Vytvořte cluster pomocí následujícího příkazu:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | Name (Název) | *mykustocluster* | Požadovaný název clusteru.|
   | Skladová jednotka (SKU) | *D13_v2* | Skladová položka, která bude použita pro váš cluster. |
   | ResourceGroupName | *testrg* | Název skupiny prostředků, kde bude vytvořen cluster. |

    Existují další volitelné parametry, které můžete použít, například kapacitu clusteru.

1. Spuštěním následujícího příkazu zkontrolujte, zda byl cluster úspěšně vytvořen:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Pokud výsledek `provisioningState` obsahuje `Succeeded` s hodnotou, byl cluster úspěšně vytvořen.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Vytvoření databáze v clusteru Průzkumníka dat Azure

1. Vytvořte databázi pomocí následujícího příkazu:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
   |---|---|---|
   | Název clusteru | *mykustocluster* | Název clusteru, kde bude databáze vytvořena.|
   | Name (Název) | *mykustodatabáze* | Název databáze.|
   | ResourceGroupName | *testrg* | Název skupiny prostředků, kde bude vytvořen cluster. |
   | SoftDeletePeriod | *3650:00:00:00* | Doba, po kterou budou data k dispozici pro dotaz. |
   | HotCachePeriod | *3650:00:00:00* | Doba, po kterou budou data uložena v mezipaměti. |

1. Chcete-li zobrazit databázi, kterou jste vytvořili, spusťte následující příkaz:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

Nyní máte cluster a databázi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

* Pokud máte v plánu sledovat naše další články, uchovávejte zdroje, které jste vytvořili.
* Chcete-li vyčistit prostředky, odstraňte cluster. Když odstraníte cluster, odstraní také všechny databáze v něm. K odstranění clusteru použijte následující příkaz:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Další kroky

* [Další příkazy Az.Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Ingestování dat pomocí azure data exploreru .NET Standard SDK (Preview)](net-standard-ingest-data.md)
