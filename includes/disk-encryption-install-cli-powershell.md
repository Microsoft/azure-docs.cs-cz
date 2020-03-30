---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72511460"
---
Azure Disk Encryption můžete povolit a spravovat prostřednictvím [Azure CLI](/cli/azure) a [Azure PowerShell](/powershell/azure/new-azureps-module-az). Chcete-li tak učinit, musíte nainstalovat nástroje místně a připojit se k předplatnému Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) je nástroj příkazového řádku pro správu prostředků Azure. ClI je navržen tak, aby flexibilně dotazovat data, podporovat dlouhotrvající operace jako neblokující procesy a usnadnit skriptování. Můžete ji nainstalovat místně podle následujících kroků v [instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Pokud [se chcete přihlásit ke svému účtu Azure pomocí azure cli](/cli/azure/authenticate-azure-cli), použijte příkaz [az přihlášení.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli
az login
```

Pokud chcete vybrat klienta, který se má přihlásit, použijte:
    
```azurecli
az login --tenant <tenant>
```

Pokud máte více předplatných a chcete zadat konkrétní, získejte seznam odběrů se [seznamem účtů az](/cli/azure/account#az-account-list) a zadejte s [nastaveným účtem AZ](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Další informace najdete [v tématu Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Modul az Azure PowerShell](/powershell/azure/new-azureps-module-az) poskytuje sadu rutin, které používají model [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) pro správu prostředků Azure. Můžete ji použít ve svém prohlížeči s [Azure Cloud Shell](/azure/cloud-shell/overview), nebo můžete nainstalovat na místním počítači pomocí pokynů v nainstalovat modul Azure [PowerShell](/powershell/azure/install-az-ps). 

Pokud už máte nainstalovaný místně, ujistěte se, že ke konfiguraci šifrování disku Azure používáte nejnovější verzi sady Azure PowerShell SDK. Stáhněte si nejnovější verzi [Azure PowerShellu](https://github.com/Azure/azure-powershell/releases).

Pokud [se chcete přihlásit ke svému účtu Azure pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps?view=azps-2.5.0), použijte rutinu [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Pokud máte více předplatných a chcete zadat jeden, použijte [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) rutina jejich seznam, následovaný [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) rutina:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Spuštění rutiny [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) ověří, zda bylo vybráno správné předplatné.

Chcete-li potvrdit, že jsou nainstalovány rutiny Azure Disk Encryption, použijte rutinu [get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Další informace najdete [v tématu Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps). 