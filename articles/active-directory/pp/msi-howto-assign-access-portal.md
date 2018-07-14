---
title: Přiřazení přístupu MSI k prostředku Azure, pomocí webu Azure portal
description: Podrobné pokyny pro přiřazování MSI na jeden přístup k prostředkům na jiný prostředek, pomocí webu Azure portal.
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
ms.openlocfilehash: 2af83762fdcd3302f2af9b08585febab42387d79
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035978"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Identita spravované služby se přístup k prostředku přiřadit i pomocí webu Azure portal

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Po dokončení konfigurace k prostředku Azure s Identity spravované služby (MSI), můžete poskytnout přístup MSI do jiného prostředku, stejně jako jakýkoli zaregistrovaný objekt zabezpečení. Tento článek ukazuje, jak předat přístupu MSI virtuálního počítače Azure na účtu služby Azure storage pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Využijte RBAC pro přiřazení přístupu MSI k jinému prostředku

Po povolení MSI v prostředku Azure, [jako je například virtuální počítač Azure](msi-qs-configure-portal-windows-vm.md):

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu přidružených k předplatnému Azure, ve které jste nakonfigurovali MSI.

2. Přejděte na požadovaný prostředek, na který chcete upravit řízení přístupu. V tomto příkladu nabízíme přístupu virtuálních počítačů Azure do účtu úložiště, takže přejdeme k účtu úložiště.

3. Vyberte **řízení přístupu (IAM)** stránce prostředků a vyberte **+ přidat**. Zadejte **Role**, **přiřazení přístupu k virtuálnímu počítači**a zadejte odpovídající **předplatné** a **skupiny prostředků** Pokud je prostředek umístěn. V rámci oblasti kritéria hledání měli byste vidět prostředku. Vyberte prostředek a vyberte **Uložit**. 

   ![Snímek obrazovky přístupu (IAM) ovládacího prvku](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. Budete přesměrováni zpět na hlavní **řízení přístupu (IAM)** stránku, kde uvidíte nový záznam pro zdroje MSI. V tomto příkladu má "SimpleWinVM" virtuálního počítače ze skupiny prostředků ukázka **Přispěvatel** přístup k účtu úložiště.

   ![Snímek obrazovky přístupu (IAM) ovládacího prvku](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>Řešení potíží

Pokud soubor MSI pro prostředek není uveden v seznamu dostupných identit, ověřte správně povolené MSI. V našem případě můžeme přejít zpět na virtuálním počítači Azure a zkontrolujte následující:

- Podívejte se na **konfigurace** stránce a ujistěte se, že hodnota **MSI povolené** je **Ano**.
- Podívejte se na **rozšíření** stránce a ujistěte se, že rozšíření MSI úspěšné nasazení.

Pokud je buď nesprávný, potřebujete znovu znovu provádět nasazení MSI pro váš prostředek ani řešení potíží s nasazení se nezdařilo.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Povolení MSI ve Virtuálním počítači Azure, najdete v článku [konfigurace Azure VM Identity spravované služby (MSI) pomocí webu Azure portal](msi-qs-configure-portal-windows-vm.md).


