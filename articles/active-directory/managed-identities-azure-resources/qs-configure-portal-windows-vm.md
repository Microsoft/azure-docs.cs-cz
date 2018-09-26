---
title: Jak nakonfigurovat spravované identity pro prostředky Azure na Virtuálním počítači Azure pomocí webu Azure portal
description: Podrobné pokyny ke konfiguraci spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí webu Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: b73a79676be559ad491bd7bb16691369dd8fa271
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158627"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuální počítač pomocí webu Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak můžete povolit nebo zakázat systému a uživatelsky přiřazené identity spravované pro virtuální počítač (Virtuálním počítači Azure), pomocí webu Azure portal. 

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení řízení přístupu na základě rolí Azure:

    > [!NOTE]
    > Žádné další Azure vyžaduje přiřazení rolí adresáře AD.

    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) povolit a odeberte systém přiřadil spravovaná identita z virtuálního počítače Azure.

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se dozvíte, jak povolit a zakázat identitu spravované systém přiřadil pro virtuální počítač pomocí webu Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Povolit systém přiřadil spravovanou identitu při vytváření virtuálního počítače

Povolit systém přiřadil spravovanou identitu při vytváření virtuálního počítače, v části **správu** kartu **Identity** části, přepněte **se identita spravované služby** k  **Na**.  

![Povolit systém přiřadil identitu během vytváření virtuálních počítačů](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Najdete v následujících šablon rychlý start k vytvoření virtuálního počítače: 

- [Vytvoření virtuálního počítače Windows pomocí webu Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Povolit systém přiřadil spravovaná identita na existující virtuální počítač

Pokud chcete povolit systém přiřadil spravovaná identita na virtuálním počítači, který byl původně zřízený bez něj:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.

2. Přejděte na požadovaný virtuální počítač a vyberte **Identity**.

3. V části **přiřazenou systémem**, **stav**vyberte **na** a potom klikněte na tlačítko **Uložit**:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Odeberte systém přiřadil spravovaná identita z virtuálního počítače

Pokud máte virtuální počítač, který už musí systém přiřadil spravovaná identita:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač. 

2. Přejděte na požadovaný virtuální počítač a vyberte **Identity**.

3. V části **přiřazenou systémem**, **stav**vyberte **vypnout** a potom klikněte na tlačítko **Uložit**:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Uživatel přiřazenou spravované identity

 V této části se dozvíte, jak přidávat a odebírat spravované identity přiřazené uživateli z virtuálního počítače pomocí webu Azure portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Přiřazení uživatelsky přiřazené identity během vytváření virtuálního počítače

Na webu Azure portal v současné době nepodporuje přiřazování uživatelsky přiřazené spravovanou identitu při vytváření virtuálního počítače. Místo toho odkazovat na jeden z následujících článků rychlý start vytvoření virtuálního počítače k vytvoření virtuálního počítače a pak pokračujte k další části Podrobnosti o přiřazení spravovanou identitu uživatelsky přiřazené k virtuálnímu počítači:

- [Vytvoření virtuálního počítače Windows pomocí webu Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Přiřadit uživateli přiřazena spravovanou identitu do existujícího virtuálního počítače

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač a klikněte na tlačítko **Identity**, **přiřazené uživateli** a potom  **\+přidat**.

   ![Přidat spravovanou identitu uživatelsky přiřazené k virtuálnímu počítači](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klikněte na uživatelsky přiřazené identity chcete přidat do virtuálního počítače a pak klikněte na tlačítko **přidat**.

    ![Přidat spravovanou identitu uživatelsky přiřazené k virtuálnímu počítači](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Odeberte uživatelsky přiřazené spravovanou identitu z virtuálního počítače

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač a klikněte na tlačítko **Identity**, **přiřazené uživateli**, spravovat identitu, kterou chcete odstranit a potom klikněte na název přiřazený uživatel **odebrat** (klikněte na tlačítko  **Ano** v podokně potvrzení).

   ![Odeberte uživatelsky přiřazené identity spravované z virtuálního počítače](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Další postup

- Identity spravované virtuálního počítače Azure pomocí webu Azure portal, zadejte [přístup do jiného prostředku Azure](howto-assign-access-portal.md).

