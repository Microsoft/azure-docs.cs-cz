---
title: Konfigurace spravovaných identit pomocí portálu Azure – Azure AD
description: Krok za krokem pro konfiguraci spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí portálu Azure.
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
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75971dbd35a6b29306bc87988423ab662a343705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266660"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a zakázat spravované identity přiřazené systému a uživatelem pro virtuální počítač Azure (VM) pomocí portálu Azure. 

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat systémem přiřazenou spravovanou identitu pro virtuální počítač pomocí portálu Azure.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Povolení spravované identity přiřazené systémem při vytváření virtuálního počítače

Chcete-li povolit systémem přiřazenou spravovanou identitu na virtuálním počítači během jeho vytváření, váš účet potřebuje přiřazení role [přispěvatele virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

- Na kartě **Správa** v části **Identita** přepněte **identitu spravované služby** **na Zapnuto**.  

![Povolení systémově přiřazené identity během vytváření virtuálních mís](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Chcete-li vytvořit virtuální hod, postupujte takto: 

- [Vytvoření virtuálního počítače s Windows pomocí webu Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Povolení spravované identity přiřazené systémem na existujícím virtuálním počítači

Chcete-li povolit systémem přiřazenou spravovanou identitu na virtuálním počítači, který byl původně zřízen bez něj, váš účet potřebuje přiřazení role [přispěvatele virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, který obsahuje virtuální počítač.

2. Přejděte na požadovaný virtuální počítač a vyberte **identita**.

3. V části **Systém přiřazený**, **Stav**, vyberte **Zapnout** a pak klepněte na **Uložit**:

   ![Snímek obrazovky konfigurační stránky](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Odebrání spravované identity přiřazené systémem z virtuálního aplikace

Chcete-li odebrat systémem přiřazenou spravovanou identitu z virtuálního počítače, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

Pokud máte virtuální počítač, který už nepotřebuje spravovanou identitu přiřazenou systémem:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, který obsahuje virtuální počítač. 

2. Přejděte na požadovaný virtuální počítač a vyberte **identita**.

3. V části **Systém přiřazený**, **Stav**, vyberte **Vypnout** a pak klepněte na **Uložit**:

   ![Snímek obrazovky konfigurační stránky](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

 V této části se dozvíte, jak přidat a odebrat uživatelem přiřazenou spravovanou identitu z virtuálního počítače pomocí portálu Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Přiřazení identity přiřazené uživateli při vytváření virtuálního virtuálního mísy

Chcete-li přiřadit uživatelem přiřazenou identitu k virtuálnímu počítači, váš účet potřebuje přiřazení rolí [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [Operátor spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

V současné době portál Azure nepodporuje přiřazování uživatelem přiřazené spravované identity během vytváření virtuálního počítače. Místo toho se podívejte na jeden z následujících článků rychlého spuštění vytvoření virtuálního počítači, abyste nejprve vytvořili virtuální počítač, a pak přejděte k další části podrobností o přiřazení spravované identity přiřazené uživateli k virtuálnímu počítači:

- [Vytvoření virtuálního počítače s Windows pomocí webu Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Přiřazení spravované identity přiřazené uživateli k existujícímu virtuálnímu virtuálnímu mněmu

Chcete-li přiřadit uživatelem přiřazenou identitu k virtuálnímu počítači, váš účet potřebuje přiřazení rolí [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [Operátor spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, který obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální virtuální ms a ** \+** klikněte na **Identita**, Uživatel **přiřazený** a potom Přidat .

   ![Přidání spravované identity přiřazené uživateli do virtuálního aplikace](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klikněte na identitu přiřazenou uživatelem, kterou chcete přidat do virtuálního virtuálního uživatele, a potom klikněte na **Přidat**.

    ![Přidání spravované identity přiřazené uživateli do virtuálního aplikace](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Odebrání spravované identity přiřazené uživateli z virtuálního aplikace

Chcete-li odebrat identitu přiřazenou uživateli z virtuálního počítače, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, který obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální virtuální ms a klikněte na **Identita**, **Přiřazený uživatel**, název spravované identity přiřazené uživateli, kterou chcete odstranit, a klikněte na **Odebrat** (v potvrzovacím podokně klikněte na **Ano).**

   ![Odebrání spravované identity přiřazené uživateli z virtuálního aplikace](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Další kroky

- Pomocí portálu Azure udělit přístup spravované identity virtuálního počítače Azure [k jinému prostředku Azure](howto-assign-access-portal.md).

