---
title: Konfigurace MSI ve Virtuálním počítači Azure pomocí webu Azure portal
description: Projděte pokyny ke konfiguraci Identity spravované služby (MSI) na virtuálním počítači Azure pomocí webu Azure portal.
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
ms.openlocfilehash: 9d9c18d576f3975d4c8272efff7161366f3f53fe
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901353"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurace virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a zakázat systému identity přiřazené pro virtuální počítač Azure, pomocí webu Azure portal. Přiřazení a odebrání uživatele identit přiřazených z virtuálních počítačů Azure prostřednictvím webu Azure Portal momentálně nepodporuje.

> [!NOTE]
> Operace identity přiřazené uživateli nejsou v současnosti podporované prostřednictvím webu Azure Portal. Sledujte novinky. 

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Identita spravované služby během vytváření virtuálního počítače Azure

Vytvoření virtuálního počítače prostřednictvím webu Azure portal v současné době nepodporuje operace Identity spravované služby. Místo toho najdete jednu z následujících článků rychlý start vytvoření virtuálního počítače k vytvoření virtuálního počítače:

- [Vytvoření virtuálního počítače Windows pomocí webu Azure portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Pokračujte k další části Podrobnosti o povolení Identity spravované služby na virtuálním počítači.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Povolit identitu spravované služby na existujícím virtuálním počítači Azure

Pokud chcete povolit identitu na virtuálním počítači, který byl původně zřízený bez něho přiřazenou systémem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů".

2. Přejděte na požadovaný virtuální počítač a vyberte stránku "Konfigurace".

3. Povolit identitu přiřazenou systémem ve virtuálním počítači tak, že vyberete "Ano" v části "Identita spravované služby" a potom klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo informace k dokončení:

    > [!NOTE]
    > Přidání identity přiřazené uživateli k virtuálnímu počítači se momentálně nepodporuje prostřednictvím webu Azure Portal.

   ![Snímek obrazovky stránky konfigurace](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure Identity spravované služby

Pokud máte virtuální počítač se už nepotřebuje identitou přiřazenou systémem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů".

2. Přejděte na požadovaný virtuální počítač a vyberte stránku "Konfigurace".

3. Zakázat systém přiřadil identity ve virtuálním počítači tak, že vyberete "Ne" v části "Identita spravované služby" a potom klikněte na Uložit. Tato operace může trvat 60 sekund nebo informace k dokončení:

    > [!NOTE]
    > Přidání identity přiřazené uživateli k virtuálnímu počítači se momentálně nepodporuje prostřednictvím webu Azure Portal.

   ![Snímek obrazovky stránky konfigurace](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Související obsah

- Přehled Identity spravované služby najdete v tématu [přehled](overview.md).

## <a name="next-steps"></a>Další postup

- Pomocí webu Azure portal, zadejte MSI virtuálního počítače Azure na [přístup do jiného prostředku Azure](howto-assign-access-portal.md).

