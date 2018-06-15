---
title: Přiřazení přístupu MSI pro prostředek služby Azure, pomocí portálu Azure
description: Podrobné pokyny pro přiřazení MSI na jeden přístup k prostředkům na jiný prostředek, a to pomocí portálu Azure.
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
ms.openlocfilehash: 83a56793d08632918a75f6580360a9dd148d7316
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
ms.locfileid: "28978833"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Přiřadit identitu služby spravovat přístup k prostředku pomocí portálu Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Po nakonfigurování jste prostředek služby Azure s identita spravované služby (MSI), můžete zajistit přístup MSI pro jiný prostředek, stejně jako jakýkoli zaregistrovaný objekt zabezpečení. Tento článek ukazuje, jak poskytnout přístup MSI Azure virtuálního počítače k účtu úložiště Azure pomocí portálu Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Používat funkci RBAC přiřazení přístupu MSI pro jiný prostředek

Po povolení MSI na prostředek Azure [například virtuální počítač Azure](msi-qs-configure-portal-windows-vm.md):

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu spojené s předplatným Azure, pod kterým jste nakonfigurovali soubor MSI.

2. Přejděte do požadovaného prostředku, na který chcete upravit řízení přístupu. V tomto příkladu nabízíme virtuálního počítače Azure přístup k účtu úložiště, proto jsme přejděte na účet úložiště.

3. Vyberte **přístup k ovládacímu prvku (IAM)** prostředků a vyberte **+ přidat**. Zadejte **Role**, **přiřadit přístup k virtuálnímu počítači**a zadejte odpovídající **předplatné** a **skupiny prostředků** kde se nachází na prostředek. V oblasti kritérií hledání měli byste vidět prostředku. Vyberte prostředek a vyberte **Uložit**. 

   ![Snímek obrazovky přístup ovládacího prvku (IAM)](~/articles/active-directory/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. Jste vráceni do hlavní **přístup k ovládacímu prvku (IAM)** stránky, kde uvidíte nový záznam pro prostředku MSI. V tomto příkladu má "SimpleWinVM" virtuální počítač ze skupiny prostředků ukázku **Přispěvatel** přístup k účtu úložiště.

   ![Snímek obrazovky přístup ovládacího prvku (IAM)](~/articles/active-directory/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>Řešení potíží

Pokud soubor MSI pro prostředek nezobrazuje v seznamu dostupných identit, ověřte, aby byla správně povolená soubor MSI. V našem případě jsme můžete přejít zpět na virtuálním počítači Azure a zkontrolujte následující:

- Podívejte se na **konfigurace** stránky a ujistěte se, že hodnota **MSI povoleno** je **Ano**.
- Podívejte se na **rozšíření** stránky a ujistěte se, že příponou MSI úspěšně nasazena.

Pokud je buď nesprávný, musíte může znovu nasaďte MSI v prostředku znovu, nebo vyřešit potíže s selhání nasazení.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Pokud chcete povolit MSI ve virtuálním počítači Azure, najdete v části [konfigurace Azure virtuálního počítače spravované služby Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md).


