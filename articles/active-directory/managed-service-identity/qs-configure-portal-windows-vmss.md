---
title: Konfigurace MSI ve virtuálním počítači Azure měřítku nastavit pomocí portálu Azure
description: Krok podle podrobné pokyny ke konfiguraci a spravovaná služba Identity (MSI) na VMSS Azure, pomocí portálu Azure.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: c915c692a12781538e10d367d40e3efe473a6853
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurace VMSS spravované služby Identity (MSI) pomocí portálu Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a zakázat systému přiřazené identity pro VMSS, pomocí portálu Azure. Přiřazení a odebrání uživatele přiřazenou identit ze služby Azure VMSS přes portál Azure aktuálně nepodporuje.

> [!NOTE]
> Uživatel s přiřazenou identity operations v současné době nejsou podporovány prostřednictvím portálu Azure. Vraťte se zpět pro aktualizace.

## <a name="prerequisites"></a>Požadavky


- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Identita spravované služby během vytváření škálovací sady virtuálního počítače Azure

Vytvoření virtuálního počítače prostřednictvím portálu Azure v současné době nepodporuje operace identita spravované služby. Místo toho prosím naleznete v následující virtuální počítač Azure škálování sadu vytvoření rychlý start článku nejprve vytvořit sadu škálování virtuálního počítače Azure:

- [Vytvořit sadu škálování virtuálního počítače na portálu Azure](../../virtual-machine-scale-sets/quick-create-portal.md)  

Přejděte k části Další informace o povolení MSI na škálovací sadu virtuálních počítačů.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Povolit spravované služby Identita na existující VMMS Azure

Chcete-li povolit systém přiřadit identita na virtuální počítač, který byl původně zřizovat bez ho:

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Přejděte do sady škálování požadovaný virtuální počítač.

3. Povolit identity přiřazené systému ve virtuálním počítači tak, že vyberete "Ano" v části "Identita spravované služby" a pak klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo více k dokončení:

   [![Snímek obrazovky stránky konfigurace](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Odeberte spravované identitu služby z sady škálování virtuálního počítače Azure

Pokud máte škálovací sadu virtuálních počítačů, které už nepotřebuje MSI:

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu na škálovací sadu virtuálních počítačů.

2. Přejděte do sady škálování požadovaný virtuální počítač.

3. Zakázat systému přiřazené identity ve virtuálním počítači tak, že vyberete "Ne" v části "Identita spravované služby" a potom klikněte na Uložit. Tato operace může trvat 60 sekund nebo více k dokončení:

   ![Snímek obrazovky stránky konfigurace](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Související obsah

- Přehled identita spravované služby najdete v tématu [přehled](overview.md).

## <a name="next-steps"></a>Další postup

- Pomocí portálu Azure, udělte Azure škálovací sady virtuálních počítačů MSI [přístup k jiným prostředkem Azure](howto-assign-access-portal.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
