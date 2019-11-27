---
title: Přiřazení spravované identity k prostředku pomocí PowerShellu – Azure AD
description: Podrobné pokyny pro přiřazení spravované identity k jednomu prostředku a přístup k jinému prostředku pomocí PowerShellu
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2283ac076ef761fd098d75e7120e6557a959574
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547247"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Přiřazení spravované identity k prostředku pomocí PowerShellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po nakonfigurování prostředku Azure pomocí spravované identity můžete spravované identitě udělit přístup k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. V tomto příkladu se dozvíte, jak dát spravované identitě virtuálního počítače Azure přístup k účtu Azure Storage pomocí PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud jste to ještě neudělali, nainstalujte [nejnovější verzi Azure PowerShell](/powershell/azure/install-az-ps) .

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Přiřaďte přístup spravované identity k jinému prostředku pomocí RBAC.

Po povolení spravované identity na prostředku Azure, jako je [třeba virtuální počítač Azure](qs-configure-powershell-windows-vm.md):

1. Přihlaste se k Azure pomocí rutiny `Connect-AzAccount`. Použijte účet, který je přidružený k předplatnému Azure, v rámci kterého jste nakonfigurovali spravovanou identitu:

   ```powershell
   Connect-AzAccount
   ```
2. V tomto příkladu poskytujeme přístup k virtuálnímu počítači Azure pro účet úložiště. Nejprve pomocí [Get-AzVM](/powershell/module/az.compute/get-azvm) Získejte instanční objekt pro virtuální počítač s názvem `myVM`, který byl vytvořen, když jsme povolili spravovanou identitu. Pak pomocí [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) udělte **čtenářům** virtuálního počítače přístup k účtu úložiště s názvem `myStorageAcct`:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Další kroky

- [Přehled spravované identity pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](qs-configure-powershell-windows-vm.md).
