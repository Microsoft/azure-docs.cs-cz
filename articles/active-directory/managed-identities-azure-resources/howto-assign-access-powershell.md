---
title: Přiřazení spravované identity k prostředku pomocí PowerShellu – Azure AD
description: Podrobné pokyny pro přiřazení spravované identity k jednomu prostředku, přístup k jinému prostředku pomocí PowerShellu
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fc5df641f27f8d604f7b5647736128856a3ecd51
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764364"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Přiřazení spravované identity k prostředku pomocí PowerShellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po nakonfigurování prostředku Azure pomocí spravované identity můžete spravované identitě udělit přístup k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. V tomto příkladu se dozvíte, jak dát spravované identitě virtuálního počítače Azure přístup k účtu Azure Storage pomocí PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Chcete-li spustit ukázkové skripty, máte dvě možnosti:
    - Použijte [Azure Cloud Shell](../../cloud-shell/overview.md), který můžete otevřít pomocí tlačítka **vyzkoušet** v pravém horním rohu bloků kódu.
    - Spusťte skripty místně pomocí instalace nejnovější verze [Azure PowerShell](/powershell/azure/install-az-ps)a pak se přihlaste k Azure pomocí `Connect-AzAccount` . 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Pomocí Azure RBAC přiřaďte přístup spravované identity k jinému prostředku.

1. Povolte spravovanou identitu na prostředku Azure, [jako je třeba virtuální počítač Azure](qs-configure-powershell-windows-vm.md).

1. V tomto příkladu poskytujeme přístup k virtuálnímu počítači Azure pro účet úložiště. Nejprve pomocí [Get-AzVM](/powershell/module/az.compute/get-azvm) Získejte instanční objekt pro virtuální počítač s názvem `myVM` , který byl vytvořen, když jsme povolili spravovanou identitu. Pak pomocí [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) udělte **čtenářům** virtuálního počítače přístup k účtu úložiště s názvem `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Další kroky

- [Přehled spravované identity pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](qs-configure-powershell-windows-vm.md).
