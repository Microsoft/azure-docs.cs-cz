---
title: Vytvoření účtu úložiště objektů blob bloku – Azure Storage | Dokumentace Microsoftu
description: Ukazuje, jak vytvořit účet úložiště objektů blob bloku Azure s charakteristikami výkonu premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9d8fb8f5f470dc47088efb30b7f823a0b8c624c8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140999"
---
# <a name="create-a-block-blob-storage-account"></a>Vytvoření účtu úložiště objektů blob bloku

Typ účtu úložiště objektů blob bloku umožňuje vytvářet objekty BLOB bloku s charakteristikami výkonu premium. Tento typ účtu úložiště je optimalizována pro úlohy s vysokou transakce plateb nebo, které vyžadují velmi rychlé zpracování čas přístupu. Tento článek ukazuje, jak vytvořit účet úložiště objektů blob bloku pomocí webu Azure portal, rozhraní příkazového řádku Azure nebo Azure Powershellu.

Další informace o účtech úložiště objektů blob bloku, v tématu [přehled účtu Azure storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Vytvoření účtu na webu Azure Portal

K vytvoření účtu úložiště objektů blob bloku na webu Azure Portal, postupujte podle těchto kroků:

1. Na webu Azure Portal, vyberte **všechny služby** > **úložiště** kategorie > **účty úložiště**.

1. V části **účty úložiště**vyberte **přidat**.

1. V **předplatné** pole, vyberte předplatné, ve kterém chcete vytvořit účet úložiště.

1. V **skupiny prostředků** pole, vyberte existující skupinu prostředků nebo vyberte **vytvořit nový**a zadejte název pro novou skupinu prostředků.

1. V **název účtu úložiště** pole, zadejte název pro účet. Mějte na paměti následující pokyny:

   - Název musí být jedinečný v Azure.
   - Název musí být 3 až 24 znaků.
   - Název může obsahovat pouze číslice a malá písmena.

1. V **umístění** pole, vyberte umístění pro účet úložiště nebo použijte výchozí umístění.

1. Pro zbývající nastavení nakonfigurujte následující nastavení:

   |Pole     |Hodnota  |
   |---------|---------|
   |**Výkon**    |  Vyberte **Premium**.   |
   |**Druh účtu**    | Vyberte **BlockBlobStorage**.      |
   |**Replikace**    |  Ponechejte výchozí nastavení **místně redundantní úložiště (LRS)**.      |

   ![Zobrazuje uživatelské rozhraní pro vytvoření účtu úložiště objektů blob bloku portálu](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Vyberte **zkontrolujte + vytvořit** ke kontrole nastavení účtu úložiště.

1. Vyberte **Vytvořit**.

## <a name="create-account-using-azure-powershell"></a>Vytvoření účtu pomocí Azure Powershellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Spusťte relaci prostředí Windows PowerShell aplikace se zvýšenými oprávněními (Spustit jako správce).

1. Spuštěním následujícího příkazu zkontrolujte nejnovější verzi `Az` nainstalovaný modul prostředí PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Otevřete novou konzolu Powershellu a přihlaste se pomocí svého účtu Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. V případě potřeby vytvořte novou skupinu prostředků. Nahraďte hodnoty v uvozovkách a spusťte následující příkaz.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Vytvoření účtu úložiště objektů blob bloku. Nahraďte hodnoty v uvozovkách a spusťte následující příkaz.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Vytvoření účtu pomocí rozhraní příkazového řádku Azure

Vytvoření účtu objektu blob bloku pomocí rozhraní příkazového řádku Azure, musíte nejprve nainstalovat Azure CLI verze. 2.0.46 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

1. Přihlaste se ke svému předplatnému Azure.

   ```azurecli
   az login
   ```

1. V případě potřeby vytvořte novou skupinu prostředků. Nahraďte hodnoty v závorkách (včetně závorek) a spusťte následující příkaz.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Vytvoření účtu úložiště objektů blob bloku. Nahraďte hodnoty v závorkách (včetně závorek) a spusťte následující příkaz.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Další postup

- Další informace o účtech úložiště najdete v [přehledu účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
