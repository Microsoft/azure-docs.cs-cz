---
title: Přiřazení spravované identity k prostředku pomocí Azure Portal – Azure AD
description: Podrobné pokyny pro přiřazení spravované identity k jednomu prostředku přístup k jinému prostředku pomocí Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24c97909870c4d76b07ec837e5f624a509bd1f2
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "74547293"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Přiřazení spravované identity k prostředku pomocí Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po nakonfigurování prostředku Azure pomocí spravované identity můžete spravované identitě udělit přístup k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. V tomto článku se dozvíte, jak udělit přístup ke spravovaným identitám virtuálního počítače Azure nebo ke službě Azure Storage ve službě Virtual Machine Scale pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Přiřaďte přístup spravované identity k jinému prostředku pomocí RBAC.

Po povolení spravované identity na prostředku Azure, jako je třeba [virtuální počítač Azure](qs-configure-portal-windows-vm.md) nebo [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, v rámci kterého jste nakonfigurovali spravovanou identitu.

2. Přejděte k požadovanému prostředku, u kterého chcete změnit řízení přístupu. V tomto příkladu poskytujeme virtuálnímu počítači Azure přístup k účtu úložiště, takže přejdeme na účet úložiště.

3. Vyberte stránku **řízení přístupu (IAM)** daného prostředku a vyberte **+ Přidat přiřazení role**. Pak zadejte **roli**, **přiřaďte přístup k**a zadejte odpovídající **předplatné**. V oblasti kritérií hledání byste měli vidět prostředek. Vyberte prostředek a vyberte **Uložit**. 

   ![Snímek obrazovky řízení přístupu (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Další kroky

- [Přehled spravované identity pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na](qs-configure-portal-windows-vm.md)virtuálním počítači pomocí Azure Portal.
- Postup povolení spravované identity v sadě škálování virtuálních počítačů Azure najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure v sadě škálování virtuálních počítačů pomocí Azure Portal](qs-configure-portal-windows-vmss.md).


