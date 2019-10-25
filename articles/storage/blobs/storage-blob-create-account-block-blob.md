---
title: Vytvořte účet úložiště objektů blob bloku – Azure Storage | Microsoft Docs
description: Ukazuje, jak vytvořit účet Azure BlockBlobStorage s charakteristikou výkonu Premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1df1d5180d951e7a720ec82c548438892a47a426
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881863"
---
# <a name="create-a-blockblobstorage-account"></a>Vytvoření účtu BlockBlobStorage

Typ účtu BlockBlobStorage umožňuje vytvářet objekty blob bloku s charakteristikami výkonu Premium. Tento typ účtu úložiště je optimalizovaný pro úlohy s vysokými sazbami transakcí nebo, které vyžadují velmi rychlý přístup krát. Tento článek ukazuje, jak vytvořit účet BlockBlobStorage pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell.

Další informace o účtech BlockBlobStorage najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Pokud chcete v Azure Portal vytvořit účet BlockBlobStorage, postupujte následovně:

1. V Azure Portal vyberte **všechny služby** > kategorii **úložiště** > **účty úložiště**.

1. V části **účty úložiště**vyberte **Přidat**.

1. V poli **předplatné** vyberte předplatné, ve kterém chcete účet úložiště vytvořit.

1. V poli **Skupina prostředků** vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**a zadejte název nové skupiny prostředků.

1. Do pole **název účtu úložiště** zadejte název účtu. Pamatujte na následující pokyny:

   - Název musí být v rámci Azure jedinečný.
   - Název musí být dlouhý 3 až 24 znaků.
   - Název může obsahovat jenom číslice a malá písmena.

1. V poli **umístění** vyberte umístění pro účet úložiště nebo použijte výchozí umístění.

1. V případě zbývajících nastavení proveďte následující konfiguraci:

   |Pole     |Hodnota  |
   |---------|---------|
   |**Výkon**    |  Vyberte **Premium**.   |
   |**Druh účtu**    | Vyberte **BlockBlobStorage**.      |
   |**Replikace**    |  Ponechte výchozí nastavení **místně redundantního úložiště (LRS)** .      |

   ![Zobrazuje uživatelské rozhraní portálu pro vytvoření účtu úložiště objektů blob bloku.](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte nastavení účtu úložiště.

1. Vyberte **Create** (Vytvořit).

## <a name="azure-powershelltabazure-powershell"></a>[Azure Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními (Spustit jako správce).

1. Spusťte následující příkaz, abyste se ujistili, že je nainstalovaná nejnovější verze modulu `Az` PowerShellu.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Otevřete novou konzolu PowerShellu a přihlaste se pomocí svého účtu Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. V případě potřeby vytvořte novou skupinu prostředků. Hodnoty v uvozovkách nahraďte a spusťte následující příkaz.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Vytvořte účet BlockBlobStorage. Hodnoty v uvozovkách nahraďte a spusťte následující příkaz.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vytvořit účet bloku BLOB pomocí rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat rozhraní příkazového řádku Azure CLI v. 2.0.46 nebo novější verze. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

1. Přihlaste se ke svému předplatnému Azure.

   ```azurecli
   az login
   ```

1. V případě potřeby vytvořte novou skupinu prostředků. Hodnoty v závorkách (včetně závorek) nahraďte a spusťte následující příkaz.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Vytvořte účet BlockBlobStorage. Hodnoty v závorkách (včetně závorek) nahraďte a spusťte následující příkaz.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Další kroky

- Další informace o účtech úložiště najdete v [přehledu účtu úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
