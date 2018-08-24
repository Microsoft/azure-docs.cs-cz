---
title: Postup konfigurace Identity spravované služby na Virtuálním počítači Azure pomocí webu Azure portal
description: Projděte pokyny ke konfiguraci Identity spravované služby na Virtuálním počítači Azure pomocí webu Azure portal.
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
ms.openlocfilehash: 7ac4dd41c8a1adb422539e0832715d59ec385694
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745555"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Nakonfigurujte identitu spravované služby virtuálního počítače pomocí webu Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak můžete povolit nebo zakázat systémových a uživatelských identity přiřazené pro virtuální počítač (Virtuálním počítači Azure), pomocí webu Azure portal. 

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení role:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) povolit a odebrat identitou přiřazenou systémem od virtuálního počítače Azure.

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem

V této části se dozvíte, jak můžete povolit nebo zakázat systému identity přiřazené pro virtuální počítač pomocí webu Azure portal.

### <a name="enable-system-assigned-identity-during-creation-of-a-vm"></a>Povolit při vytváření virtuálního počítače identitu přiřazenou systémem

Na webu Azure portal v současné době nepodporuje povolení identitou přiřazenou systémem během vytváření virtuálního počítače. Místo toho odkazovat na jeden z následujících článků rychlý start vytvoření virtuálního počítače k vytvoření virtuálního počítače a pak pokračujte k další části Podrobnosti o povolení identitou přiřazenou systémem ve virtuálním počítači:

- [Vytvoření virtuálního počítače Windows pomocí webu Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-identity-on-an-existing-vm"></a>Povolit identitu na existující virtuální počítač přiřazenou systémem

Pokud chcete povolit identitu na virtuálním počítači, který byl původně zřízený bez něho přiřazenou systémem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.

2. Přejděte na požadovaný virtuální počítač a vyberte stránku "Konfigurace".

3. Povolit identitu přiřazenou systémem ve virtuálním počítači tak, že vyberete "Ano" v části "Identita spravované služby" a potom klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo informace k dokončení:

   ![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-identity-from-a-vm"></a>Odeberte z virtuálního počítače identitu přiřazenou systémem

Pokud máte virtuální počítač se už nepotřebuje identitou přiřazenou systémem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač. 

2. Přejděte na požadovaný virtuální počítač a vyberte stránku "Konfigurace".

3. Zakázat systém přiřadil identity ve virtuálním počítači tak, že vyberete "Ne" v části "Identita spravované služby" a potom klikněte na Uložit. Tato operace může trvat 60 sekund nebo informace k dokončení:

   ![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

 V této části se dozvíte, jak přidávat a odebírat uživatele identity přiřazené z virtuálního počítače pomocí webu Azure portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Přiřazování identity přiřazené během vytváření virtuálního počítače uživateli

Na webu Azure portal v současné době nepodporuje přiřazování identity přiřazené během vytváření virtuálního počítače uživateli. Místo toho odkazovat na jeden z následujících článků rychlý start vytvoření virtuálního počítače k vytvoření virtuálního počítače a pak pokračujte k další části Podrobnosti o přiřazování identity přiřazené k virtuálnímu počítači uživateli:

- [Vytvoření virtuálního počítače Windows pomocí webu Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-identity-to-an-existing-vm"></a>Přiřazování identity přiřazené k existujícímu virtuálnímu počítači uživateli

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač a klikněte na tlačítko **Identity**, **přiřazené uživateli** a potom  **\+přidat**.

   ![Přidání identity přiřazené uživateli k virtuálnímu počítači](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klikněte na tlačítko identity přiřazené uživateli chcete přidat do virtuálního počítače a pak klikněte na tlačítko **přidat**.

    ![Přidání identity přiřazené uživateli k virtuálnímu počítači](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-vm"></a>Odebrání uživatele identity přiřazené z virtuálního počítače

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač a klikněte na tlačítko **Identity**, **přiřazené uživateli**, název identity přiřazené uživateli chcete odstranit a potom klikněte na tlačítko **odebrat** (klikněte na tlačítko **Ano** v podokně potvrzení).

   ![Odeberte z virtuálního počítače identity přiřazené uživateli](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="related-content"></a>Související obsah

- Přehled Identity spravované služby najdete v tématu [přehled](overview.md).

## <a name="next-steps"></a>Další postup

- Identita spravované služby virtuálním počítači Azure pomocí webu Azure portal, poskytnout [přístup do jiného prostředku Azure](howto-assign-access-portal.md).

