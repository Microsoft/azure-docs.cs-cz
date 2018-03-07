---
title: "Přiřazení přístupu MSI pro prostředek služby Azure, pomocí portálu Azure"
description: "Podrobné pokyny pro přiřazení MSI na jeden přístup k prostředkům na jiný prostředek, a to pomocí portálu Azure."
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 6522a5ef7f1e1af89ee70ee74041e1e69614ebca
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Přiřadit identitu služby spravovat přístup k prostředku pomocí portálu Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Po nakonfigurování jste prostředek služby Azure s identita spravované služby (MSI), můžete zajistit přístup MSI pro jiný prostředek, stejně jako jakýkoli zaregistrovaný objekt zabezpečení. Tento článek ukazuje, jak poskytnout přístup MSI sadě škálování virtuálního počítače nebo virtuální počítač Azure k účtu úložiště Azure pomocí portálu Azure.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Používat funkci RBAC přiřazení přístupu MSI pro jiný prostředek

Můžete po povolení MSI na prostředek služby Azure, například [virtuálního počítače Azure](msi-qs-configure-portal-windows-vm.md) nebo [Azure VMSS](msi-qs-configure-portal-windows-vmss.md):

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným Azure, pod kterým jste nakonfigurovali soubor MSI.

2. Přejděte do požadovaného prostředku, na který chcete upravit řízení přístupu. V tomto příkladu nabízíme virtuální počítač Azure a Azure škálovací sady virtuálních počítačů přístup k účtu úložiště, takže jsme přejděte na účet úložiště.

3. Pro virtuální počítač Azure, vyberte **přístup k ovládacímu prvku (IAM)** prostředků a vyberte **+ přidat**. Zadejte **Role**, **přiřadit přístup k virtuálnímu počítači**a zadejte odpovídající **předplatné** a **skupiny prostředků** kde se nachází na prostředek. V oblasti kritérií hledání měli byste vidět prostředku. Vyberte prostředek a vyberte **Uložit**. 

   ![Snímek obrazovky přístup ovládacího prvku (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Pro sadu škálování virtuálního počítače Azure, vyberte **přístup k ovládacímu prvku (IAM)** prostředků a vyberte **+ přidat**. Zadejte **Role**, **přiřadit přístup**. V oblasti kritérií hledání vyhledejte vaší škálovací sadu virtuálních počítačů. Vyberte prostředek a vyberte **Uložit**.
   
   ![Snímek obrazovky přístup ovládacího prvku (IAM)](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Jste vráceni do hlavní **přístup k ovládacímu prvku (IAM)** stránky, kde uvidíte nový záznam pro prostředku MSI.

    Virtuální počítač Azure:

   ![Snímek obrazovky přístup ovládacího prvku (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Sady škálování virtuálního počítače Azure:

    ![Snímek obrazovky přístup ovládacího prvku (IAM)](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>Poradce při potížích

Pokud soubor MSI pro prostředek nezobrazuje v seznamu dostupných identit, ověřte, aby byla správně povolená soubor MSI. V našem případě jsme přejděte zpět na virtuálním počítači Azure a zkontrolujte následující:

- Podívejte se na **konfigurace** stránky a ujistěte se, že hodnota **MSI povoleno** je **Ano**.
- Podívejte se na **rozšíření** stránky a ujistěte se, že příponou MSI úspěšně nasazena (**rozšíření** stránka není k dispozici pro sadu škálování virtuálního počítače Azure).

Pokud je buď nesprávný, musíte může znovu nasaďte MSI v prostředku znovu, nebo vyřešit potíže s selhání nasazení.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Povolit MSI na virtuální počítač Azure, najdete v části [konfigurace Azure virtuálního počítače spravované služby Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md).
- Povolit MSI na sadu škálování virtuálního počítače Azure, najdete v části [konfigurace Azure Virtual Machine škálování nastavit spravované služby Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vmss.md)


