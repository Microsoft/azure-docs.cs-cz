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
ms.openlocfilehash: 93c532cf2864db28b580303ecefec8b6dbed65f6
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257755"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>Konfigurace virtuálního počítače škálovací sady identita spravované služby pomocí webu Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku jste se dozvíte, jak povolit a zakázat identitu pro škálovací sadu virtuálních počítačů přiřazenou systémem pomocí webu Azure portal. Přiřazení a odebrání uživatelsky přiřazených identit z škálovací sady virtuálních počítačů Azure prostřednictvím portálu Azure portal momentálně nepodporuje.

> [!NOTE]
> Operace identity přiřazené uživateli nejsou v současnosti podporované prostřednictvím webu Azure Portal. Vraťte se sem a přečtěte si nové informace.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md).
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení role:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) povolit a odebrat ze škálovací sady virtuálních počítačů spravovaných identitu přiřazenou systémem.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Identita spravované služby během vytváření škálovací sady virtuálních počítačů Azure

Vytvoření virtuálního počítače prostřednictvím webu Azure portal v současné době nepodporuje operace Identity spravované služby. Místo toho najdete následující virtuální počítač Azure škálovací sada vytvoření tohoto článku rychlý start nejdřív vytvoří škálovací sadu virtuálních počítačů Azure:

- [Vytvoření Škálovací sady virtuálních počítačů na webu Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

Pokračujte k další části Podrobnosti o povolení Identity spravované služby ve škálovací sadě virtuálních počítačů.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Povolit identitu spravované služby v existující VMMS Azure

Pokud chcete povolit identitu na virtuálním počítači, který byl původně zřízený bez něho přiřazenou systémem:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Přejděte na požadovaný virtuální počítač škálovací sady.

3. Povolit identitu přiřazenou systémem ve virtuálním počítači tak, že vyberete "Ano" v části "Identita spravované služby" a potom klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo informace k dokončení:

   [![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Odebrat identita spravované služby ze škálovací sady virtuálních počítačů Azure

Pokud máte škálovací sadu virtuálních počítačů, které už nepotřebuje identita spravované služby:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na škálovací sadu virtuálních počítačů.

2. Přejděte na požadovaný virtuální počítač škálovací sady.

3. Zakázat systém přiřadil identity ve virtuálním počítači tak, že vyberete "Ne" v části "Identita spravované služby" a potom klikněte na Uložit. Tato operace může trvat 60 sekund nebo informace k dokončení:

   ![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Související obsah

- Přehled Identity spravované služby najdete v tématu [přehled](overview.md).

## <a name="next-steps"></a>Další postup

- Pomocí webu Azure portal, poskytují Azure škálovací sady virtuálních počítačů identita spravované služby [přístup do jiného prostředku Azure](howto-assign-access-portal.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.
