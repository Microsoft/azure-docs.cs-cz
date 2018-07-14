---
title: Konfigurace MSI ve Virtuálním počítači Azure pomocí webu Azure portal
description: Projděte pokyny ke konfiguraci Identity spravované služby (MSI) na virtuálním počítači Azure pomocí webu Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 54bb2466a99569c124128ad3e08f276a0bc0262d
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035261"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurace virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat MSI pro virtuální počítač Azure, pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Povolení MSI během vytváření virtuálního počítače Azure

Povolení MSI během vytváření virtuálního počítače na webu Azure Portal se nepodporuje v době době psaní tohoto textu. Místo toho najdete jednu z následujících článků rychlý start vytvoření virtuálního počítače k vytvoření virtuálního počítače:

- [Vytvoření virtuálního počítače Windows pomocí webu Azure portal](~/articles/virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](~/articles/virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Pokračujte k další části Podrobnosti o povolení MSI ve virtuálním počítači.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Povolení MSI na existujícím virtuálním počítači Azure

Pokud máte virtuální počítač, který byl původně zřízený bez MSI:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů".

2. Přejděte na požadovaný virtuální počítač.

2. Klikněte na stránce "Konfigurace", povolte výběrem možnosti "Ano" v "Identita spravované služby" MSI ve virtuálním počítači a pak klikněte na **Uložit**. Tato operace může trvat 60 sekund nebo informace k dokončení:

   ![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Odebrání MSI virtuálního počítače Azure

Pokud máte virtuální počítač, který už je Instalační služba MSI:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) použijte účet spojený s předplatným služby Azure, která obsahuje virtuální počítač. Také ujistěte se, že váš účet patří do role, která poskytuje oprávnění k zápisu na virtuálním počítači, jako je například "Přispěvatel virtuálních počítačů".

2. Přejděte na požadovaný virtuální počítač.

3. Klikněte na stránce "Konfigurace", odeberte MSI z virtuálního počítače tak, že vyberete "Ne" v části "Identita spravované služby" a pak klikněte na tlačítko **Uložit**. Tato operace může trvat 60 sekund nebo informace k dokončení:

   ![Snímek obrazovky stránky konfigurace](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).

## <a name="next-steps"></a>Další postup

- Pomocí webu Azure portal, zadejte MSI virtuálního počítače Azure na [přístup do jiného prostředku Azure](msi-howto-assign-access-portal.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.
