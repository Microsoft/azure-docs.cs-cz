---
title: Postup konfigurace MSI ve virtuálním počítači Azure pomocí portálu Azure
description: Krok podle podrobné pokyny pro konfiguraci a spravovaná služba Identity (MSI) ve virtuálním počítači Azure, pomocí portálu Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 62b8504f5c10f338539d263bb231cf96eb405ba6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a zakázat systému přiřazené identity pro virtuální počítač Azure, pomocí portálu Azure. Přiřazení a odebrání uživatele přiřazené identit z virtuálních počítačů Azure přes portál Azure aktuálně nepodporuje.

> [!NOTE]
> Uživatel s přiřazenou identity operations v současné době nejsou podporovány prostřednictvím portálu Azure. Vraťte se zpět pro aktualizace. 

## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Identita spravované služby během vytváření virtuální počítač Azure

Vytvoření virtuálního počítače prostřednictvím portálu Azure v současné době nepodporuje operace identita spravované služby. Místo toho přejděte k některému z následujících článků rychlý start vytvoření virtuálního počítače nejprve vytvořit virtuální počítač:

- [Vytvoření virtuálního počítače s Windows pomocí portálu Azure](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvořit virtuální počítač s Linuxem pomocí portálu Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Přejděte k části Další informace o povolení spravované identita služby ve virtuálním počítači.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Povolit spravované služby Identita na existující virtuální počítač Azure

Chcete-li povolit systém přiřadit identita na virtuální počítač, který byl původně zřizovat bez ho:

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů".

2. Přejděte na požadovaný virtuální počítač a vyberte stránku "Konfigurace".

3. Povolit identity přiřazené systému ve virtuálním počítači tak, že vyberete "Ano" v části "Identita spravované služby" a pak klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo více k dokončení:

    > [!NOTE]
    > Přidání identitu uživatele přiřazené k virtuálnímu počítači se aktuálně nepodporuje prostřednictvím portálu Azure.

   ![Snímek obrazovky stránky konfigurace](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure identita spravované služby

Pokud máte virtuální počítač, který už nepotřebuje systému přiřazené identity:

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů".

2. Přejděte na požadovaný virtuální počítač a vyberte stránku "Konfigurace".

3. Zakázat systému přiřazené identity ve virtuálním počítači tak, že vyberete "Ne" v části "Identita spravované služby" a potom klikněte na Uložit. Tato operace může trvat 60 sekund nebo více k dokončení:

    > [!NOTE]
    > Přidání identitu uživatele přiřazené k virtuálnímu počítači se aktuálně nepodporuje prostřednictvím portálu Azure.

   ![Snímek obrazovky stránky konfigurace](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Související obsah

- Přehled identita spravované služby najdete v tématu [přehled](overview.md).

## <a name="next-steps"></a>Další postup

- Pomocí portálu Azure, přidělte virtuálnímu počítači Azure MSI [přístup k jiným prostředkem Azure](howto-assign-access-portal.md).

