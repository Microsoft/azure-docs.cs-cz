---
title: Přiřazení spravované identity k prostředku pomocí Azure Portal – Azure AD
description: Podrobné pokyny pro přiřazení spravované identity k jednomu prostředku přístup k jinému prostředku pomocí Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6584754edf3ff7ae31c3b9ace72baf16459dbc44
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359985"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Přiřazení spravované identity k prostředku pomocí Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po nakonfigurování prostředku Azure pomocí spravované identity můžete spravované identitě udělit přístup k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. V tomto článku se dozvíte, jak udělit přístup ke spravovaným identitám virtuálního počítače Azure nebo ke službě Azure Storage ve službě Virtual Machine Scale pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Pomocí Azure RBAC přiřaďte přístup spravované identity k jinému prostředku.

Po povolení spravované identity v prostředku Azure, jako je třeba virtuální počítač [Azure](qs-configure-portal-windows-vm.md) nebo [sada škálování virtuálního počítače Azure](qs-configure-portal-windows-vmss.md):

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, v rámci kterého jste nakonfigurovali spravovanou identitu.

2. Přejděte k požadovanému prostředku, u kterého chcete změnit řízení přístupu. V tomto příkladu poskytujeme virtuálnímu počítači Azure přístup k účtu úložiště, takže přejdeme na účet úložiště.

3. Vyberte stránku **řízení přístupu (IAM)** daného prostředku a vyberte **+ Přidat přiřazení role**. Pak zadejte **roli** , **přiřaďte přístup k** a zadejte odpovídající **předplatné**. V oblasti kritérií hledání byste měli vidět prostředek. Vyberte prostředek a vyberte **Uložit**. 

   ![Snímek obrazovky řízení přístupu (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Další kroky

- [Přehled spravované identity pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na](qs-configure-portal-windows-vm.md)virtuálním počítači pomocí Azure Portal.
- Postup povolení spravované identity v sadě škálování virtuálních počítačů Azure najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure v sadě škálování virtuálních počítačů pomocí Azure Portal](qs-configure-portal-windows-vmss.md).


