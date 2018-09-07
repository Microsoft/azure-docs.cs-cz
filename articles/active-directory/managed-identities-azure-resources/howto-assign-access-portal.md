---
title: Přiřazení přístupu MSI k prostředku Azure, pomocí webu Azure portal
description: Podrobné pokyny pro přiřazování MSI na jeden přístup k prostředkům na jiný prostředek, pomocí webu Azure portal.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: b6423a80b0405bcaabd4f1fc8e26dc3be0adadb0
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028031"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Identita spravované služby se přístup k prostředku přiřadit i pomocí webu Azure portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po dokončení konfigurace k prostředku Azure s Identity spravované služby (MSI), můžete poskytnout přístup MSI do jiného prostředku, stejně jako jakýkoli zaregistrovaný objekt zabezpečení. Tento článek ukazuje, jak poskytnout přístup MSI sadu škálování virtuálního počítače nebo virtuálního počítače Azure do účtu služby Azure storage pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Využijte RBAC pro přiřazení přístupu MSI k jinému prostředku

Po povolení MSI v prostředku Azure, jako například [virtuálního počítače Azure](qs-configure-portal-windows-vm.md) nebo [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu přidružených k předplatnému Azure, ve které jste nakonfigurovali MSI.

2. Přejděte na požadovaný prostředek, na který chcete upravit řízení přístupu. V tomto příkladu nabízíme virtuálním počítači Azure a Azure škálovací sady virtuálních počítačů přístup k účtu úložiště, takže přejdeme k účtu úložiště.

3. Pro virtuální počítač Azure, vyberte **řízení přístupu (IAM)** stránce prostředků a vyberte **+ přidat**. Zadejte **Role**, **přiřazení přístupu k virtuálnímu počítači**a zadejte odpovídající **předplatné** a **skupiny prostředků** Pokud je prostředek umístěn. V rámci oblasti kritéria hledání měli byste vidět prostředku. Vyberte prostředek a vyberte **Uložit**. 

   ![Snímek obrazovky přístupu (IAM) ovládacího prvku](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Pro škálovací sadu virtuálních počítačů Azure, vyberte **řízení přístupu (IAM)** stránce prostředků a vyberte **+ přidat**. Zadejte **Role**, **přiřadit přístup k**. V rámci oblasti kritérií hledání hledejte škálovací sadu virtuálních počítačů. Vyberte prostředek a vyberte **Uložit**.
   
   ![Snímek obrazovky přístupu (IAM) ovládacího prvku](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. Budete přesměrováni zpět na hlavní **řízení přístupu (IAM)** stránku, kde uvidíte nový záznam pro zdroje MSI.

    Virtuální počítač Azure:

   ![Snímek obrazovky přístupu (IAM) ovládacího prvku](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Škálovací sada virtuálních počítačů Azure:

    ![Snímek obrazovky přístupu (IAM) ovládacího prvku](./media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>Řešení potíží

Pokud soubor MSI pro prostředek není uveden v seznamu dostupných identit, ověřte správně povolené MSI. V našem případě můžeme přejít zpět na virtuálním počítači Azure a zkontrolujte následující:

- Podívejte se na **konfigurace** stránce a ujistěte se, že hodnota **MSI povolené** je **Ano**.
- Podívejte se na **rozšíření** stránce a ujistěte se, že rozšíření MSI úspěšné nasazení (**rozšíření** stránka není dostupná pro škálovací sady virtuálních počítačů Azure).

Pokud je buď nesprávný, potřebujete znovu znovu provádět nasazení MSI pro váš prostředek ani řešení potíží s nasazení se nezdařilo.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](overview.md).
- Pokud chcete povolit MSI ve virtuálním počítači Azure, najdete v článku [konfigurace Azure VM Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vm.md).
- Povolení MSI na škálovací sadu virtuálních počítačů Azure, najdete v článku [konfigurace Azure virtuální počítač Škálovací nastavení Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vmss.md)


