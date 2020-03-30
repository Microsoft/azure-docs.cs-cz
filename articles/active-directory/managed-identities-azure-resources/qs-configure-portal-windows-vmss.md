---
title: Konfigurace spravovaných identit na Azure VMSS – Azure AD
description: Krok za krokem pro konfiguraci spravovaných identit pro prostředky Azure na škálovací sadě virtuálních strojů pomocí portálu Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32d8944b55794412eb322697469933030e2fbb56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184036"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Konfigurace spravovaných identit pro prostředky Azure na škálovací sadě virtuálních strojů pomocí portálu Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu. 

V tomto článku se pomocí Prostředí PowerShell dozvíte, jak provádět následující spravované identity pro operace prostředků Azure na škálovací sadě virtuálních strojů:

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- K provedení operací správy v tomto článku váš účet potřebuje následující přiřazení řízení přístupu azure na základě rolí:

    > [!NOTE]
    > Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

    - [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pro povolení a odebrání spravované identity přiřazené systému z škálovací sady virtuálních strojů.

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravované identity přiřazené systémem pomocí portálu Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Povolení spravované identity přiřazené systémem při vytváření škálovací sady virtuálních strojů

V současné době portál Azure nepodporuje povolení spravované identity přiřazené systému během vytváření škálovací sady virtuálních strojů. Místo toho se podívejte na následující vytvoření škálovací sady virtuálních strojů, abyste nejprve vytvořili škálovací sadu virtuálních strojů, a pak přejděte k další části, kde najdete podrobnosti o povolení spravované identity přiřazené systému ve škálovací sadě virtuálních strojů:

- [Vytvoření škálovací sady virtuálních strojů na webu Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Povolení spravované identity přiřazené systémem na existující škálovací sadě virtuálních strojů

Povolení spravované identity přiřazené systémem na škálovací sadě virtuálních strojů, která byla původně zřízena bez ní:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, který obsahuje škálovací sadu virtuálních strojů.

2. Přejděte na požadovanou škálovací sadu virtuálních strojů.

3. V části **Systém přiřazený**, **Stav**, vyberte **Zapnout** a pak klepněte na **Uložit**:

   ![Snímek obrazovky konfigurační stránky](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Odebrání spravované identity přiřazené systému ze škálovací sady virtuálních strojů

Pokud máte škálovací sadu virtuálních strojů, která už nepotřebuje spravovanou identitu přiřazenou systémem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, který obsahuje škálovací sadu virtuálních strojů. Také se ujistěte, že váš účet patří do role, která vám dává oprávnění k zápisu na škálovací sadu virtuálních strojů.

2. Přejděte na požadovanou škálovací sadu virtuálních strojů.

3. V části **Systém přiřazený**, **Stav**, vyberte **Vypnout** a pak klepněte na **Uložit**:

   ![Snímek obrazovky konfigurační stránky](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat uživatelem přiřazenou spravovanou identitu ze škálovací sady virtuálních strojů pomocí portálu Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživateli při vytváření škálovací sady virtuálních strojů

V současné době portál Azure nepodporuje přiřazování uživatelem přiřazené spravované identity během vytváření škálovací sady virtuálních strojů. Místo toho se podívejte na následující vytvoření škálovací sady virtuálních strojů, abyste nejprve vytvořili škálovací sadu virtuálních strojů, a pak přejděte k další části podrobností o přiřazení spravované identity přiřazené uživateli:

- [Vytvoření škálovací sady virtuálních strojů na webu Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživateli k existující škálovací sadě virtuálních strojů

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, který obsahuje škálovací sadu virtuálních strojů.
2. Přejděte na požadovanou škálovací sadu virtuálních strojů ** \+** a klikněte na **Identita**, Uživatel **přiřazený** a potom přidat .

   ![Přidání identity přiřazené uživateli do Služby VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klikněte na identitu přiřazenou uživatelem, kterou chcete přidat do škálovací sady virtuálních strojů, a potom klikněte na **Přidat**.
   
   ![Přidání identity přiřazené uživateli do Služby VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Odebrání spravované identity přiřazené uživateli ze škálovací sady virtuálních strojů

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, který obsahuje virtuální počítač.
2. Přejděte na požadovanou škálovací sadu virtuálních strojů a klikněte na **Identita**, **Přiřazený uživatel**, název spravované identity přiřazené uživateli, kterou chcete odstranit, a potom klikněte na **Odebrat** (v potvrzovacím podokně klikněte na **Ano).**

   ![Odebrání identity přiřazené uživateli z vmss.](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Další kroky

- Pomocí portálu Azure udělit škálování škálování Azure škálovat přístup k identitě [spravované](howto-assign-access-portal.md)identity na jiný prostředek Azure .


