---
title: Konfigurace Identity spravované služby v měřítku virtuálních počítačů Azure pomocí webu Azure portal
description: Projděte pokyny ke konfiguraci Identity spravované služby na Azure VMSS, pomocí webu Azure portal.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: bfd63262a1d5568223b4e4077e2f8c987b7ec0d4
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746732"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Konfigurace virtuálního počítače škálovací sady identita spravované služby pomocí webu Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak můžete povolit nebo zakázat systémových a uživatelských přiřazené identity pro virtuální počítač škálovací sady s použitím webu Azure portal.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení role:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) povolit a odebrat ze škálovací sady virtuálních počítačů spravovaných identitu přiřazenou systémem.

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem 

V této části se dozvíte, jak povolit a zakázat identitu na škálovací sadu pomocí webu Azure portal virtuálních počítačů přiřazenou systémem.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Povolit identitu přiřazenou systémem při vytváření škálovací sady virtuálních počítačů

Na webu Azure portal v současné době nepodporuje povolení systém identity přiřazené při vytváření škálovací sady virtuálních počítačů. Místo toho najdete následující virtuální počítače škálovací sady vytváření tohoto článku rychlý start nejdříve vytvořit škálovací sadu virtuálních počítačů a pak pokračujte k další části Podrobnosti o povolení identitu na škálovací sadu virtuálních počítačů přiřazenou systémem:

- [Vytvoření Škálovací sady virtuálních počítačů na webu Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>Povolit identitu přiřazenou systémem v existující škálovací sady virtuálních počítačů

Pokud chcete povolit identitu přiřazenou systémem ve škálovací sadě virtuálního počítače, který byl původně zřízený bez něj:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Přejděte na požadovaný virtuální počítač škálovací sady.

3. Povolit identitu přiřazenou systémem ve virtuálním počítači tak, že vyberete "Ano" v části "Identita spravované služby" a potom klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo informace k dokončení:

   [![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>Odebrání škálovací sadu virtuálních počítačů s identitou přiřazenou systémem

Pokud máte virtuální počítač škálovací sadu, která už potřebuje identitu přiřazenou systémem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na škálovací sadu virtuálních počítačů.

2. Přejděte na požadovaný virtuální počítač škálovací sady.

3. Zakázat systém přiřadil identity ve virtuálním počítači tak, že vyberete "Ne" v části "Identita spravované služby" a potom klikněte na Uložit. Tato operace může trvat 60 sekund nebo informace k dokončení:

   ![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části se dozvíte, jak přidávat a odebírat uživatele identity přiřazené z virtuálního počítače škálovací sady s použitím webu Azure portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřadit identity přiřazené uživateli při vytváření škálovací sady virtuálních počítačů

Na webu Azure portal v současné době nepodporuje přiřazování identity přiřazené při vytváření škálovací sady virtuálních počítačů uživateli. Místo toho najdete následující virtuální počítače škálovací sady vytváření tohoto článku rychlý start nejdříve vytvořit škálovací sadu virtuálních počítačů a pak pokračujte k další části Podrobnosti o přiřazování identity přiřazené k ní uživatel:

- [Vytvoření Škálovací sady virtuálních počítačů na webu Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>Identity přiřazené uživateli přiřadit existující škálovací sady virtuálních počítačů

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů.
2. Přejděte na požadovaný virtuální počítač škálovací sadu a klikněte na tlačítko **Identity**, **přiřazené uživateli** a potom  **\+přidat**.

   ![Přidání identity přiřazené uživateli na VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klikněte na tlačítko identity přiřazené uživateli chcete přidat do škálovací sady virtuálních počítačů a potom klikněte na tlačítko **přidat**.
   
   ![Přidání identity přiřazené uživateli na VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Identity přiřazené uživateli odebrat ze škálovací sady virtuálních počítačů

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač škálovací sadu a klikněte na tlačítko **Identity**, **přiřazené uživateli**, název identity přiřazené uživateli chcete odstranit a potom klikněte na tlačítko **odebrat** () Klikněte na tlačítko **Ano** v podokně potvrzení).

   ![Odebrat z VMSS identity přiřazené uživateli](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>Související obsah

- Přehled Identity spravované služby najdete v tématu [přehled](overview.md).

## <a name="next-steps"></a>Další postup

- Pomocí webu Azure portal, poskytují Azure škálovací sady virtuálních počítačů identita spravované služby [přístup do jiného prostředku Azure](howto-assign-access-portal.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.
