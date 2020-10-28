---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: f2e95a53f7dd188e1103a47cba097716cfad9484
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746230"
---
Azure Disk Encryption můžete povolit a spravovat prostřednictvím rozhraní příkazového [řádku Azure](/cli/azure) a [Azure PowerShell](/powershell/azure/new-azureps-module-az). K tomu je potřeba nainstalovat nástroje místně a připojit se k předplatnému Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2,0](/cli/azure) je nástroj příkazového řádku pro správu prostředků Azure. Rozhraní příkazového řádku je navržené k flexibilnímu dotazování dat, podpoře dlouhotrvajících operací jako neblokujících procesů a snadného skriptování. Můžete ji nainstalovat místně pomocí postupu v části [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Pokud se chcete [přihlásit ke svému účtu Azure pomocí Azure CLI](/cli/azure/authenticate-azure-cli), použijte příkaz [AZ Login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Pokud chcete vybrat tenanta pro přihlášení, použijte:
    
```azurecli
az login --tenant <tenant>
```

Pokud máte více předplatných a chcete zadat konkrétní konkrétní, získejte seznam předplatných pomocí [AZ Account list](/cli/azure/account#az-account-list) a určete pomocí [AZ Account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Další informace najdete v tématu Začínáme [s Azure CLI 2,0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az) poskytuje sadu rutin, které používají model [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) ke správě prostředků Azure. Můžete ji použít v prohlížeči pomocí [Azure Cloud Shell](/azure/cloud-shell/overview)nebo ji můžete nainstalovat na místní počítač podle pokynů v tématu [instalace Azure PowerShell modulu](/powershell/azure/install-az-ps). 

Pokud jste ho již nainstalovali místně, ujistěte se, že používáte nejnovější verzi sady Azure PowerShell SDK ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [Azure PowerShell verze](https://github.com/Azure/azure-powershell/releases).

Pokud se chcete [přihlásit ke svému účtu Azure pomocí Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), použijte rutinu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Pokud máte více předplatných a chcete ho zadat, použijte k jejich zobrazení rutinu [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) a potom rutinu [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Spuštěním rutiny [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) ověříte, že jste vybrali správné předplatné.

Pokud chcete potvrdit, že se nainstalují rutiny Azure Disk Encryption, použijte rutinu [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Další informace najdete v tématu [Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps). 
