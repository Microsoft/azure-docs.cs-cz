---
title: "Postup konfigurace MSI ve virtuálním počítači Azure pomocí portálu Azure"
description: "Krok podle podrobné pokyny pro konfiguraci a spravovaná služba Identity (MSI) ve virtuálním počítači Azure, pomocí portálu Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: f2891874fc6f894cfa141bd28c7c9edd92d7968c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat MSI pro virtuální počítač Azure, pomocí portálu Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Povolit MSI při vytváření virtuálního počítače Azure

Od verze době psaní tohoto textu není podporována povolení MSI při vytváření virtuálního počítače na portálu Azure. Místo toho přejděte k některému z následujících článků rychlý start vytvoření virtuálního počítače nejprve vytvořit virtuální počítač:

- [Vytvoření virtuálního počítače s Windows pomocí portálu Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvořit virtuální počítač s Linuxem pomocí portálu Azure](../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Přejděte k části Další informace o povolení MSI ve virtuálním počítači.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Povolit MSI na existující virtuální počítač Azure

Pokud máte virtuální počítač, který byl původně zřizovat bez souboru MSI:

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů".

2. Přejděte na požadovaný virtuální počítač.

2. Klikněte na stránce "Konfigurace", povolit MSI ve virtuálním počítači tak, že vyberete "Ano" v části "Identita spravované služby" a pak klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo více k dokončení:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure MSI

Pokud máte virtuální počítač, který už nepotřebuje MSI:

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů".

2. Přejděte na požadovaný virtuální počítač.

3. Klikněte na stránce "Konfigurace", odeberte MSI z virtuálního počítače tak, že vyberete "Ne" v části "Identita spravované služby", a pak klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo více k dokončení:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).

## <a name="next-steps"></a>Další postup

- Pomocí portálu Azure, přidělte virtuálnímu počítači Azure MSI [přístup k jiným prostředkem Azure](msi-howto-assign-access-portal.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
