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
ms.openlocfilehash: 81aa8153198f69abd1722f97462927a0c242d4e7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186155"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Nakonfigurujte identitu spravované služby virtuálního počítače pomocí webu Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a zakázat systému identity přiřazené pro virtuální počítač Azure, pomocí webu Azure portal. Přiřazení a odebrání uživatele identit přiřazených z virtuálních počítačů Azure prostřednictvím webu Azure Portal momentálně nepodporuje.

> [!NOTE]
> Operace identity přiřazené uživateli nejsou v současnosti podporované prostřednictvím webu Azure Portal. Vraťte se sem a přečtěte si nové informace. 

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení role:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) povolit a odebrání identita spravované služby virtuálního počítače Azure.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Identita spravované služby během vytváření virtuálního počítače Azure

Vytvoření virtuálního počítače prostřednictvím webu Azure portal v současné době nepodporuje operace Identity spravované služby. Místo toho najdete jednu z následujících článků rychlý start vytvoření virtuálního počítače k vytvoření virtuálního počítače:

- [Vytvoření virtuálního počítače Windows pomocí webu Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Pokračujte k další části Podrobnosti o povolení Identity spravované služby na virtuálním počítači.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Povolit identitu spravované služby na existujícím virtuálním počítači Azure

Pokud chcete povolit identitu na virtuálním počítači, který byl původně zřízený bez něho přiřazenou systémem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač.

2. Přejděte na požadovaný virtuální počítač a vyberte stránku "Konfigurace".

3. Povolit identitu přiřazenou systémem ve virtuálním počítači tak, že vyberete "Ano" v části "Identita spravované služby" a potom klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo informace k dokončení:

   > [!NOTE]
   > Přidání identity přiřazené uživateli k virtuálnímu počítači se momentálně nepodporuje prostřednictvím webu Azure Portal.

   ![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure Identity spravované služby

Pokud máte virtuální počítač se už nepotřebuje identitou přiřazenou systémem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač. 

2. Přejděte na požadovaný virtuální počítač a vyberte stránku "Konfigurace".

3. Zakázat systém přiřadil identity ve virtuálním počítači tak, že vyberete "Ne" v části "Identita spravované služby" a potom klikněte na Uložit. Tato operace může trvat 60 sekund nebo informace k dokončení:

    > [!NOTE]
    > Přidání identity přiřazené uživateli k virtuálnímu počítači se momentálně nepodporuje prostřednictvím webu Azure Portal.

   ![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Související obsah

- Přehled Identity spravované služby najdete v tématu [přehled](overview.md).

## <a name="next-steps"></a>Další postup

- Identita spravované služby virtuálním počítači Azure pomocí webu Azure portal, poskytnout [přístup do jiného prostředku Azure](howto-assign-access-portal.md).

