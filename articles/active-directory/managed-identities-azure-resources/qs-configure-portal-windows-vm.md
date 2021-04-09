---
title: Konfigurace spravovaných identit pomocí Azure Portal – Azure AD
description: Podrobné pokyny pro konfiguraci spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78c973537e958b7199c137c81e72091f70b84ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590983"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a zakázat spravované identity uživatelů a uživatelských identit pro virtuální počítač Azure (VM) pomocí Azure Portal. 

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravovanou identitu přiřazenou systémem pro virtuální počítač pomocí Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Povolení spravované identity přiřazené systémem při vytváření virtuálního počítače

Aby bylo možné během vytváření virtuálního počítače povolit spravovanou identitu přiřazenou systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

- Na kartě **Správa** v části **Identita** přepněte **identitu spravované služby** na **zapnuto**.  

![Při vytváření virtuálního počítače povolit identitu přiřazenou systémem](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Další informace o vytvoření virtuálního počítače najdete v následujících rychlých startech: 

- [Vytvoření virtuálního počítače s Windows pomocí webu Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Povolení spravované identity přiřazené systémem na existujícím virtuálním počítači

Aby se povolila spravovaná identita přiřazená systémem na virtuálním počítači, který se původně zřídil bez něho, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, které obsahuje virtuální počítač.

2. Přejděte k požadovanému virtuálnímu počítači a vyberte možnost **Identita**.

3. V části **přiřazeno systému**, **stav**, vyberte **zapnuto** a potom klikněte na **Uložit**:

   ![Snímek obrazovky zobrazující stránku identita (Preview) se stavem přiřazeno systémem nastaveným na zapnuto](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Odebrání spravované identity přiřazené systémem z virtuálního počítače

Pokud chcete z virtuálního počítače odebrat spravovanou identitu přiřazenou systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

Pokud máte virtuální počítač, který už nepotřebuje spravovanou identitu přiřazenou systémem:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, které obsahuje virtuální počítač. 

2. Přejděte k požadovanému virtuálnímu počítači a vyberte možnost **Identita**.

3. V části **přiřazeno systému**, **stav** vyberte **vypnuto** a potom klikněte na **Uložit**:

   ![Snímek obrazovky konfigurační stránky](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

 V této části se dozvíte, jak přidat a odebrat spravovanou identitu přiřazenou uživatelem z virtuálního počítače pomocí Azure Portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Přiřazení uživatelsky přiřazené identity během vytváření virtuálního počítače

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

V současné době Azure Portal nepodporuje přiřazení spravované identity přiřazené uživatelem během vytváření virtuálního počítače. Místo toho použijte jeden z následujících článků rychlý Start pro vytvoření virtuálního počítače, abyste nejdřív vytvořili virtuální počítač, a potom přejděte k další části, kde najdete podrobnosti o přiřazení spravované identity přiřazené uživatelem k virtuálnímu počítači:

- [Vytvoření virtuálního počítače s Windows pomocí webu Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Přiřazení spravované identity přiřazené uživateli k existujícímu virtuálnímu počítači

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, které obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač, klikněte na **Identita**, **přiřazený uživatel** a pak na **\+ Přidat**.

   ![Snímek obrazovky zobrazující stránku identita s vybraným uživatelem přiřazenou a zvýrazněným tlačítkem Přidat](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klikněte na identitu přiřazenou uživateli, kterou chcete přidat k virtuálnímu počítači, a pak klikněte na **Přidat**.

    ![Přidání spravované identity přiřazené uživatelem do virtuálního počítače](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Odebrání spravované identity přiřazené uživatelem z virtuálního počítače

K odebrání uživatelsky přiřazené identity z virtuálního počítače potřebuje váš účet přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, které obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač a klikněte na **Identita**, **přiřazený uživatel**, název spravované identity přiřazené uživatelem, kterou chcete odstranit, a pak klikněte na **Odebrat** (v podokně potvrzení klikněte na **Ano** ).

   ![Odebrání spravované identity přiřazené uživatelem z virtuálního počítače](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Další kroky

- Pomocí Azure Portal udělte spravované identitě virtuálního počítače Azure [přístup k jinému prostředku Azure](howto-assign-access-portal.md).