---
title: Přiřazení spravovanou identitu přístup k prostředku Azure pomocí Powershellu
description: Podrobné pokyny pro přiřazování spravovanou identitu na jeden prostředek, přístup k do jiného prostředku pomocí Powershellu.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: daveba
ms.openlocfilehash: 7ed61ead475acb81da4434c880954e801492351b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081553"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Přiřazení přístupu spravovanou identitu prostředku pomocí Powershellu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po dokončení konfigurace k prostředku Azure s využitím spravované identity, můžete poskytnout přístup spravovaná identita na jiný prostředek, stejně jako jakýkoli zaregistrovaný objekt zabezpečení. Tento příklad ukazuje, jak udělit přístup spravované identity Azure virtuální počítač do účtu služby Azure storage pomocí Powershellu.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Nainstalujte [nejnovější verzi Azure Powershellu](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak již neučinili.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Využijte RBAC pro přiřazení přístupu spravovanou identitu do jiného prostředku

Po povolení identity spravované v prostředku Azure, [jako je například virtuální počítač Azure](qs-configure-powershell-windows-vm.md):

1. Přihlaste se k Azure s využitím `Connect-AzureRmAccount` rutiny. Použijte účet, který je přidružený k předplatnému Azure, ve které jste nakonfigurovali spravovaná identita:

   ```powershell
   Connect-AzureRmAccount
   ```
2. V tomto příkladu jsme dáváte přístup virtuálních počítačů Azure do účtu úložiště. Nejprve používáme [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) získat instanční objekt služby pro virtuální počítač s názvem `myVM`, která byla vytvořena, když jsme povolili spravované identity. Potom použijte [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) poskytnout virtuální počítač **čtečky** přístup na účet úložiště s názvem `myStorageAcct`:

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Další postup

- [Spravovaná identita pro prostředky Azure – přehled](overview.md)
- Povolit spravované identity na Virtuálním počítači Azure, najdete v článku [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Powershellu](qs-configure-powershell-windows-vm.md).
