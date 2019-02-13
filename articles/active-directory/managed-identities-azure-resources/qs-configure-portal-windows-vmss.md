---
title: Jak nakonfigurovat spravované identity pro prostředky Azure ve škálovací sadě virtuálních počítačů
description: Podrobné pokyny ke konfiguraci spravovaných identit pro prostředky Azure na virtuální počítač škálovací sady s použitím webu Azure portal.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ab1f046e5ab6c5b777e9253dd21e47fa9d593a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187929"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuální počítač škálovací sady s použitím webu Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku pomocí Powershellu, se dozvíte, jak k provádění následujících spravovaných identit pro operace se prostředky Azure na škálovací sadu virtuálních počítačů:

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení řízení přístupu na základě rolí Azure:

    > [!NOTE]
    > Žádné další Azure vyžaduje přiřazení rolí adresáře AD.

    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) povolit a odeberte systém přiřadil spravovanou identitu ze škálovací sady virtuálních počítačů.

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se dozvíte, jak povolit a zakázat systém přiřadil spravovanou identitu pomocí webu Azure portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Povolit systém přiřadil spravovanou identitu při vytváření škálovací sady virtuálních počítačů

Na webu Azure portal v současné době nepodporuje povolení systém přiřadil spravovanou identitu při vytváření škálovací sady virtuálních počítačů. Místo toho najdete v následujících vytváření škálovací sady virtuálního počítače tohoto článku rychlý start nejdříve vytvořit škálovací sadu virtuálních počítačů a pak pokračujte k další části Podrobnosti o povolení systém přiřadil spravovaná identita na škálovací sadu virtuálních počítačů:

- [Vytvoření Škálovací sady virtuálních počítačů na webu Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Povolit systém přiřadil spravované identity v existující škálovací sady virtuálních počítačů

Pokud chcete povolit systém přiřadil spravovanou identitu ve škálovací sadě virtuálního počítače, který byl původně zřízený bez něj:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Přejděte na požadovaný virtuální počítač škálovací sady.

3. V části **přiřazenou systémem**, **stav**vyberte **na** a potom klikněte na tlačítko **Uložit**:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Odeberte systém přiřadil spravovanou identitu ze škálovací sady virtuálních počítačů

Pokud máte škálovací sady virtuálního počítače, který už je systém přiřadil spravovanou identitu:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na škálovací sadu virtuálních počítačů.

2. Přejděte na požadovaný virtuální počítač škálovací sady.

3. V části **přiřazenou systémem**, **stav**vyberte **vypnout** a potom klikněte na tlačítko **Uložit**:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>uživatel přiřazenou spravované identity

V této části se dozvíte, jak přidávat a odebírat spravované identity přiřazené uživateli z virtuálního počítače škálovací sady s použitím webu Azure portal.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřadit uživateli přiřazena spravovanou identitu při vytváření škálovací sady virtuálních počítačů

Na webu Azure portal v současné době nepodporuje přiřazování uživatelsky přiřazené spravovanou identitu při vytváření škálovací sady virtuálních počítačů. Místo toho najdete v následujících vytváření škálovací sady virtuálního počítače tohoto článku rychlý start k vytvoření škálovací sady virtuálních počítačů a přejít k další části Podrobnosti o přiřazení spravovanou identitu uživatele přiřadit k ní:

- [Vytvoření Škálovací sady virtuálních počítačů na webu Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Spravované identity přiřazené uživateli přiřadit existující škálovací sady virtuálních počítačů

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů.
2. Přejděte na požadovaný virtuální počítač škálovací sadu a klikněte na tlačítko **Identity**, **přiřazené uživateli** a potom  **\+přidat**.

   ![Přidat uživatelsky přiřazené identity VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klikněte na uživatelsky přiřazené identity chcete přidat do škálovací sady virtuálních počítačů a potom klikněte na tlačítko **přidat**.
   
   ![Přidat uživatelsky přiřazené identity VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Odeberte uživatelsky přiřazené spravovanou identitu ze škálovací sady virtuálních počítačů

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.
2. Přejděte na požadovaný virtuální počítač škálovací sadu a klikněte na tlačítko **Identity**, **přiřazené uživateli**, spravovat identitu, kterou chcete odstranit a potom klikněte na název přiřazený uživatel **odebrat** (klikněte na tlačítko **Ano** v podokně potvrzení).

   ![Odebrání uživatelsky přiřazené identity VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Další postup

- Pomocí webu Azure portal, poskytují Azure škálovací sady virtuálních počítačů spravovanou identitu [přístup do jiného prostředku Azure](howto-assign-access-portal.md).


