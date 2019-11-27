---
title: Přiřazení spravované identity k prostředku pomocí PowerShellu – Azure AD
description: Podrobné pokyny pro přiřazování spravovanou identitu na jeden prostředek, přístup k do jiného prostředku pomocí Powershellu.
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
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Přiřazení přístupu spravovanou identitu prostředku pomocí Powershellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po dokončení konfigurace k prostředku Azure s využitím spravované identity, můžete poskytnout přístup spravovaná identita na jiný prostředek, stejně jako jakýkoli zaregistrovaný objekt zabezpečení. Tento příklad ukazuje, jak udělit přístup spravované identity Azure virtuální počítač do účtu služby Azure storage pomocí Powershellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud jste to ještě neudělali, nainstalujte [nejnovější verzi Azure PowerShell](/powershell/azure/install-az-ps) .

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Využijte RBAC pro přiřazení přístupu spravovanou identitu do jiného prostředku

Po povolení spravované identity na prostředku Azure, jako je [třeba virtuální počítač Azure](qs-configure-powershell-windows-vm.md):

1. Přihlaste se k Azure pomocí rutiny `Connect-AzAccount`. Použijte účet, který je přidružený k předplatnému Azure, ve které jste nakonfigurovali spravovaná identita:

   ```powershell
   Connect-AzAccount
   ```
2. V tomto příkladu jsme dáváte přístup virtuálních počítačů Azure do účtu úložiště. Nejprve pomocí [Get-AzVM](/powershell/module/az.compute/get-azvm) Získejte instanční objekt pro virtuální počítač s názvem `myVM`, který byl vytvořen, když jsme povolili spravovanou identitu. Pak pomocí [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) udělte **čtenářům** virtuálního počítače přístup k účtu úložiště s názvem `myStorageAcct`:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Další kroky

- [Přehled spravované identity pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](qs-configure-powershell-windows-vm.md).
