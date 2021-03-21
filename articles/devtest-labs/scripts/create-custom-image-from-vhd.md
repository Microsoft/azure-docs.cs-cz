---
title: PowerShell – vytvoření vlastní image ze souboru VHD v Azure Lab Services
description: Tento skript PowerShellu vytvoří vlastní image ze souboru VHD v Azure Lab Services.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: d22e6e1d226e826bf114a0fdb378879b828d9b4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022230"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>Použití PowerShellu k vytvoření vlastní image ze souboru VHD v Azure Lab Services

Tento ukázkový skript PowerShellu vytvoří vlastní image ze souboru VHD v Azure Lab Services

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Předpoklady
* **Testovací prostředí**. Tento skript vyžaduje, abyste měli existující testovací prostředí. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Získá prostředky. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Získá seznam přístupových klíčů pro účet služby Azure Storage. |
| [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | Přidá nasazení Azure do skupiny prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).

Další Azure Lab Services ukázkových skriptech PowerShellu najdete v [ukázkách Azure Lab Services PowerShellu](../samples-powershell.md).
