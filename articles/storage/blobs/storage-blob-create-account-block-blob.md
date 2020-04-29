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
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79536900"
---
# <a name="create-a-blockblobstorage-account"></a>Vytvoření účtu BlockBlobStorage

Typ účtu BlockBlobStorage umožňuje vytvářet objekty blob bloku s charakteristikami výkonu Premium. Tento typ účtu úložiště je optimalizovaný pro úlohy s vysokými sazbami transakcí nebo, které vyžadují velmi rychlý přístup krát. Tento článek ukazuje, jak vytvořit účet BlockBlobStorage pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Další informace o účtech BlockBlobStorage najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Žádné.

# <a name="powershell"></a>[Prostředí](#tab/azure-powershell)

Tento článek s postupem vyžaduje Azure PowerShell modul AZ verze 1.2.0 nebo novější. Aktuální verzi zjistíte spuštěním `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Můžete se přihlásit k Azure a spustit příkazy rozhraní příkazového řádku Azure CLI jedním ze dvou způsobů:

- Příkazy rozhraní příkazového řádku můžete spustit z Azure Portal v Azure Cloud Shell.
- Můžete nainstalovat rozhraní příkazového řádku a spustit příkazy rozhraní příkazového řádku místně.

### <a name="use-azure-cloud-shell"></a>Použití Azure Cloud Shellu

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Rozhraní příkazového řádku Azure je předem nainstalované a nakonfigurované pro použití s vaším účtem. V nabídce v pravé horní části Azure Portal klikněte na tlačítko **Cloud Shell** :

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Tlačítko spustí interaktivní prostředí, které můžete použít ke spuštění kroků popsaných v tomto článku s návody:

[![Snímek obrazovky zobrazující okno Cloud Shell na portálu](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Rozhraní příkazového řádku Azure můžete také nainstalovat a používat místně. Tento článek s návody vyžaduje, abyste spustili Azure CLI verze 2.0.46 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

# <a name="portal"></a>[Portál](#tab/azure-portal)

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

# <a name="powershell"></a>[Prostředí](#tab/azure-powershell)

Přihlaste se k předplatnému `Connect-AzAccount` Azure pomocí příkazu a podle pokynů na obrazovce proveďte ověření.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete spustit Azure Cloud Shell, přihlaste se k [Azure Portal](https://portal.azure.com).

K místní instalaci rozhraní příkazového řádku se přihlaste spuštěním příkazu [AZ Login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Vytvoření účtu BlockBlobStorage

## <a name="portal"></a>[Portál](#tab/azure-portal)
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
   |**Replikace**    |  Ponechte výchozí nastavení **místně redundantního úložiště (LRS)**.      |

   ![Zobrazuje uživatelské rozhraní portálu pro vytvoření účtu úložiště objektů blob bloku.](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Výběrem možnosti **zkontrolovat + vytvořit** zkontrolujte nastavení účtu úložiště.

1. Vyberte **Vytvořit**.

## <a name="azure-powershell"></a>[Prostředí Azure PowerShell](#tab/azure-powershell)

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

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vytvořit účet bloku BLOB pomocí rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat rozhraní příkazového řádku Azure CLI v. 2.0.46 nebo novější verze. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

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

---

## <a name="next-steps"></a>Další kroky

- Další informace o účtech úložiště najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Další informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
