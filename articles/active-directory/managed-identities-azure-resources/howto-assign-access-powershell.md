---
title: Přiřazení přístupu ke spravované identitě k prostředku pomocí PowerShellu – Azure AD
description: Krok za krokem pokyny pro přiřazení spravované identity na jeden prostředek, přístup k jinému prostředku, pomocí Prostředí PowerShell.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547247"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Přiřazení přístupu ke spravované identitě k prostředku pomocí Prostředí PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Jakmile nakonfigurujete prostředek Azure se spravovanou identitou, můžete spravovanou identitu udělit jinému prostředku, stejně jako jakýkoli objekt zabezpečení. Tento příklad ukazuje, jak udělit spravované identity virtuálního počítače Azure přístup k účtu úložiště Azure pomocí PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud jste to ještě neudělali, nainstalujte [nejnovější verzi Azure PowerShellu.](/powershell/azure/install-az-ps)

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Přiřazení přístupu ke spravované identitě jinému prostředku pomocí RBAC

Po povolení spravované identity na prostředek Azure, [jako je například virtuální počítač Azure](qs-configure-powershell-windows-vm.md):

1. Přihlaste se `Connect-AzAccount` k Azure pomocí rutiny. Použijte účet, který je přidružený k předplatnému Azure, pod kterým jste nakonfigurovali spravovanou identitu:

   ```powershell
   Connect-AzAccount
   ```
2. V tomto příkladu poskytujeme virtuálnímu počítači Azure přístup k účtu úložiště. Nejprve použijeme [Get-AzVM](/powershell/module/az.compute/get-azvm) k získání instančního objektu pro virtuální ho virtuálního zařízení s názvem `myVM`, který byl vytvořen, když jsme povolili spravovanou identitu. Potom použijte [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) udělit **vaplikaci pro** čtení `myStorageAcct`virtuálních zařízení přístup k účtu úložiště s názvem :

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, [přečtěte si tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](qs-configure-powershell-windows-vm.md).
