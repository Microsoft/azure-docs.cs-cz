---
title: Konfigurace spravovaných identit v VMSS – Azure AD
description: Podrobné pokyny pro konfiguraci spravovaných identit pro prostředky Azure v sadě škálování virtuálního počítače pomocí Azure Portal.
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
ms.date: 02/20/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43b986b6bb25dff1227247b1d249ce565553877a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91329129"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Konfigurace spravovaných identit pro prostředky Azure v sadě škálování virtuálních počítačů pomocí Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se naučíte, jak provádět následující spravované identity pro operace prostředků Azure ve službě škálování virtuálního počítače pomocí PowerShellu:

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- K provedení operací správy v tomto článku potřebuje váš účet následující přiřazení rolí Azure:

    > [!NOTE]
    > Nevyžadují se žádné další přiřazení role adresáře Azure AD.

    - [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) , který umožňuje povolit a odebrat spravovanou identitu přiřazenou systémem ze sady škálování virtuálního počítače.

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravovanou identitu přiřazenou systémem pomocí Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření sady škálování virtuálního počítače

V současné době Azure Portal nepodporuje povolení spravované identity přiřazené systémem během vytváření sady škálování virtuálního počítače. Místo toho si přečtěte následující článek rychlý Start vytvoření služby Virtual Machine Scale set, abyste nejdřív vytvořili sadu škálování virtuálního počítače, a potom přejděte k další části, kde najdete podrobnosti o povolení spravované identity přiřazené systémem v sadě škálování virtuálního počítače:

- [Vytvořte v Azure Portal sadu škálování virtuálního počítače.](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Povolit spravovanou identitu přiřazenou systémem v existující sadě škálování virtuálního počítače

Povolení spravované identity přiřazené systémem v sadě škálování virtuálního počítače, která byla původně zřízena bez těchto akcí:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, které obsahuje sadu škálování virtuálního počítače.

2. Přejděte na požadovanou sadu škálování virtuálního počítače.

3. V části **přiřazeno systému**, **stav**, vyberte **zapnuto** a potom klikněte na **Uložit**:

   ![Snímek obrazovky zobrazuje stránku identita (Preview) s vybranou možností systémem, stav zapnuto a zvýrazněné tlačítko Uložit.](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Odebrat spravovanou identitu přiřazenou systémem ze sady škálování virtuálních počítačů

Pokud máte sadu škálování virtuálního počítače, která už nepotřebuje spravovanou identitu přiřazenou systémem:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, které obsahuje sadu škálování virtuálního počítače. Také se ujistěte, že váš účet patří do role, která vám poskytne oprávnění k zápisu do sady škálování virtuálních počítačů.

2. Přejděte na požadovanou sadu škálování virtuálního počítače.

3. V části **přiřazeno systému**, **stav**vyberte **vypnuto** a potom klikněte na **Uložit**:

   ![Snímek obrazovky konfigurační stránky](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat spravovanou identitu přiřazenou uživatelem ze sady škálování virtuálních počítačů pomocí Azure Portal.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživatelem během vytváření sady škálování virtuálního počítače

V současné době Azure Portal nepodporuje přiřazení spravované identity přiřazené uživatelem během vytváření sady škálování virtuálního počítače. Místo toho si přečtěte následující článek rychlý Start pro vytvoření služby Virtual Machine Scale set, abyste nejdřív vytvořili sadu škálování virtuálního počítače, a potom přejděte k další části, kde najdete podrobnosti o přiřazení spravované identity přiřazené uživatelem:

- [Vytvořte v Azure Portal sadu škálování virtuálního počítače.](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživatelem do existující sady škálování virtuálních počítačů

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, které obsahuje sadu škálování virtuálního počítače.
2. Přejděte na požadovanou sadu škálování virtuálního počítače a klikněte na **Identita**, **přiřazený uživatel** a pak na ** \+ Přidat**.

   ![Snímek obrazovky zobrazující stránku identita s vybraným uživatelem přiřazenou a zvýrazněným tlačítkem Přidat](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klikněte na identitu přiřazenou uživateli, kterou chcete přidat do sady škálování virtuálního počítače, a pak klikněte na **Přidat**.
   
   ![Přidání identity přiřazené uživatelem do VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Odebrání spravované identity přiřazené uživatelem ze sady škálování virtuálních počítačů

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, které obsahuje virtuální počítač.
2. Přejděte na požadovanou sadu škálování virtuálního počítače a klikněte na **Identita**, **přiřazený uživatel**, název spravované identity přiřazené uživatelem, kterou chcete odstranit, a potom klikněte na **Odebrat** (v podokně potvrzení klikněte na **Ano** ).

   ![Odebrání uživatelsky přiřazené identity z VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Další kroky

- Pomocí Azure Portal udělte [přístup k jinému prostředku Azure](howto-assign-access-portal.md)pomocí spravované identity sady škálování virtuálních počítačů Azure.