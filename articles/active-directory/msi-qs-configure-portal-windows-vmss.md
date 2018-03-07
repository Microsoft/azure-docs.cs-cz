---
title: "Konfigurace MSI ve virtuálním počítači Azure měřítku nastavit pomocí portálu Azure"
description: "Krok podle podrobné pokyny ke konfiguraci a spravovaná služba Identity (MSI) na VMSS Azure, pomocí portálu Azure."
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 4808165aa760be7e397bd3601e958419780e0004
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurace Azure Virtual Machine škálování nastavit spravované služby Identity (MSI) pomocí portálu Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, povolení a odebrat MSI pro sadu škálování virtuálního počítače Azure pomocí portálu Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>Povolit MSI během vytváření škálovací sady virtuálního počítače Azure

Od verze době psaní tohoto textu není podporována povolení MSI během vytváření škálování virtuálních počítačů, nastavte na portálu Azure. Místo toho prosím naleznete v následující virtuální počítač Azure škálování sadu vytvoření rychlý start článku nejprve vytvořit sadu škálování virtuálního počítače Azure:

- [Vytvořit sadu škálování virtuálního počítače na portálu Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-create-portal.md)  

Přejděte k části Další informace o povolení MSI na škálovací sadu virtuálních počítačů.

## <a name="enable-msi-on-an-existing-azure-vmms"></a>Povolit MSI v existující VMMS Azure

Pokud máte škálovací sadu virtuálních počítačů, která byla původně zřizovat bez souboru MSI:

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů.

2. Přejděte do sady škálování požadovaný virtuální počítač.

3. Klikněte **konfigurace** povolte na škálování virtuálních počítačů, nastavit tak, že vyberete MSI **Ano** v části "Identita spravované služby", pak klikněte na **Uložit**. Tato operace může trvat 60 sekund nebo více k dokončení:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Odebrání MSI sadu škálování virtuálního počítače Azure

Pokud máte škálovací sadu virtuálních počítačů, které už nepotřebuje MSI:

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným služby Azure, který obsahuje škálovací sadu virtuálních počítačů. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu na škálovací sadu virtuálních počítačů.

2. Přejděte do sady škálování požadovaný virtuální počítač.

3. Klikněte na tlačítko **konfigurace** stránky, odeberte ze škálování virtuálních počítačů, nastavit tak, že vyberete MSI **ne** pod **identita spravované služby**, pak klikněte na tlačítko **uložit** . Tato operace může trvat 60 sekund nebo více k dokončení:

   ![Snímek obrazovky stránky konfigurace](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>Další postup

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Pomocí portálu Azure, udělte Azure škálovací sady virtuálních počítačů MSI [přístup k jiným prostředkem Azure](msi-howto-assign-access-portal.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
