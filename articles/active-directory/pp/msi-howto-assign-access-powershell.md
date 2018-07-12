---
title: Přiřazení přístupu MSI k prostředku Azure, pomocí Powershellu
description: Podrobné pokyny pro přiřazování MSI na jeden prostředek, přístup k do jiného prostředku pomocí Powershellu.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac8cca1e80defca33a879db5d4c160362314931a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610958"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>Přiřadit Identity spravované služby (MSI) přístup k prostředku pomocí Powershellu

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Po dokončení konfigurace k prostředku Azure pomocí MSI, můžete poskytnout přístup MSI do jiného prostředku, stejně jako libovolný objekt zabezpečení. Tento příklad ukazuje, jak udělit přístupu MSI virtuálního počítače Azure pro účet úložiště Azure pomocí Powershellu.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Také nainstalujte [prostředí Azure PowerShell verze 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) Pokud jste tak již neučinili.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Využijte RBAC pro přiřazení přístupu MSI k jinému prostředku

Po povolení MSI v prostředku Azure, [jako je například virtuální počítač Azure](msi-qs-configure-powershell-windows-vm.md):

1. Přihlaste se k Azure s využitím `Connect-AzureRmAccount` rutiny. Použijte účet, který je přidružený k předplatnému Azure, ve které jste nakonfigurovali MSI:

   ```powershell
   Connect-AzureRmAccount
   ```
2. V tomto příkladu jsme dáváte přístup virtuálních počítačů Azure do účtu úložiště. Nejprve používáme [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) získat instanční objekt služby pro virtuální počítač s názvem "myVM", který byl vytvořen, když jsme povolili MSI. Potom použijeme [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) poskytují virtuální počítač "Čtečky" přístup k účtu úložiště s názvem "myStorageAcct":

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>Řešení potíží

Pokud soubor MSI pro prostředek není uveden v seznamu dostupných identit, ověřte správně povolené MSI. V našem případě můžeme přejít zpět virtuálního počítače Azure v [webu Azure portal](https://portal.azure.com) a:

- Podívejte se na stránce "Konfigurace" a zajistit MSI, povoleno = "Yes".
- Podívejte se na stránce "Rozšíření" a zajistit úspěšné nasazení rozšíření MSI.

Pokud je buď nesprávný, budete muset znovu znovu provádět nasazení MSI pro váš prostředek ani řešení potíží s nasazení se nezdařilo.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Povolení MSI ve Virtuálním počítači Azure, najdete v článku [konfigurace Azure VM Identity spravované služby (MSI) pomocí prostředí PowerShell](msi-qs-configure-powershell-windows-vm.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.

