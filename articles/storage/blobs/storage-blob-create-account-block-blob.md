---
title: Vytvoření účtu úložiště objektů blob bloku – Azure Storage | Dokumenty společnosti Microsoft
description: Ukazuje, jak vytvořit účet Azure BlockBlobStorage s vlastnostmi výkonu premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6303644ada5c6f093611dba94daf8006f8cc5819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536900"
---
# <a name="create-a-blockblobstorage-account"></a>Vytvoření účtu BlockBlobStorage

Typ účtu BlockBlobStorage umožňuje vytvářet objekty BLOB bloku s vlastnostmi výkonu premium. Tento typ účtu úložiště je optimalizován pro úlohy s vysokou sazby transakcí nebo které vyžadují velmi rychlý přístup. Tento článek ukazuje, jak vytvořit účet BlockBlobStorage pomocí portálu Azure, Azure CLI nebo Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

Další informace o účtech BlockBlobStorage najdete v [tématu Přehled účtu úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Žádné.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento článek s návody vyžaduje modul Azure PowerShell Az verze 1.2.0 nebo novější. Aktuální verzi zjistíte spuštěním `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

K Azure se můžete přihlásit a spouštět příkazy azure cli jedním ze dvou způsobů:

- Příkazy příkazového příkazu můžete spouštět z portálu Azure v Azure Cloud Shellu.
- Můžete nainstalovat příkaz příkazpříkazy příkazového příkazu a spustit příkazy příkazového příkazu místně.

### <a name="use-azure-cloud-shell"></a>Použití Azure Cloud Shellu

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Nastavení příkazového odpočtů Azure je předinstalované a nakonfigurované pro použití s vaším účtem. Klikněte na tlačítko **Cloud Shell** v nabídce v pravé horní části portálu Azure:

[![Prostředí cloudshellu](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Tlačítko spustí interaktivní prostředí, které můžete použít ke spuštění kroků popsaných v tomto článku s postupy:

[![Snímek obrazovky s oknem Cloud Shell na portálu](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Rozhraní příkazového řádku Azure můžete také nainstalovat a používat místně. Tento článek s návody vyžaduje, abyste spouštěli Azure CLI verze 2.0.46 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

# <a name="portal"></a>[Portál](#tab/azure-portal)

Přihlaste se k [portálu Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Přihlaste se k `Connect-AzAccount` předplatnému Azure pomocí příkazu a postupujte podle pokynů na obrazovce k ověření.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete spustit Azure Cloud Shell, přihlaste se na [portál Azure](https://portal.azure.com).

Chcete-li se přihlásit k místní instalaci příkazu příkazu CLI, spusťte příkaz [az login:](/cli/azure/reference-index#az-login)

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Vytvoření účtu BlockBlobStorage

## <a name="portal"></a>[Portál](#tab/azure-portal)
Pokud chcete na webu Azure Portal vytvořit účet BlockBlobStorage, postupujte takto:

1. Na webu Azure Portal vyberte **Všechny služby** > kategorii **Úložiště** > **účty úložiště**.

1. V části **Účty úložiště**vyberte **Přidat**.

1. V poli **Předplatné** vyberte předplatné, ve kterém chcete vytvořit účet úložiště.

1. V poli **Skupina zdrojů** vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový**a zadejte název nové skupiny prostředků.

1. Do pole **Název účtu úložiště** zadejte název účtu. Všimněte si následujících pokynů:

   - Název musí být jedinečný v rámci Azure.
   - Název musí být dlouhý tři až 24 znaků.
   - Název může obsahovat pouze čísla a malá písmena.

1. V poli **Umístění** vyberte umístění pro účet úložiště nebo použijte výchozí umístění.

1. Pro zbývající nastavení nakonfigurujte následující:

   |Pole     |Hodnota  |
   |---------|---------|
   |**Výkon**    |  Vyberte **možnost Premium**.   |
   |**Druh účtu**    | Vyberte **blockblobstorage**.      |
   |**Replikace**    |  Ponechte výchozí nastavení **místně redundantního úložiště (LRS).**      |

   ![Zobrazuje portálové ui pro vytvoření účtu úložiště objektů blob bloku.](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Chcete-li zkontrolovat nastavení účtu úložiště, vyberte **Zkontrolovat + vytvořit.**

1. Vyberte **Vytvořit**.

## <a name="azure-powershell"></a>[Azure Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Otevřete relaci prostředí Windows PowerShell se zvýšenými oprávněními (Spustit jako správce).

1. Spusťte následující příkaz a ujistěte `Az` se, že je nainstalovaná nejnovější verze modulu PowerShell.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Otevřete novou konzoli PowerShellu a přihlaste se pomocí svého účtu Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. V případě potřeby vytvořte novou skupinu prostředků. Nahraďte hodnoty v nabídkách a spusťte následující příkaz.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Vytvořte účet BlockBlobStorage. Nahraďte hodnoty v nabídkách a spusťte následující příkaz.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li vytvořit účet objektu blob bloku pomocí azure CLI, musíte nejprve nainstalovat Azure CLI v. 2.0.46 nebo novější verze. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

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

1. Vytvořte účet BlockBlobStorage. Nahraďte hodnoty v závorkách (včetně závorek) a spusťte následující příkaz.

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

- Další informace o účtech úložiště najdete v tématu [Přehled účtu úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Další informace o skupinách prostředků najdete [v tématu Přehled Správce prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
