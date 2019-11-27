---
title: Přiřazení spravované identity k prostředku pomocí Azure Portal – Azure AD
description: Podrobné pokyny pro přiřazování spravovanou identitu na jeden přístup k prostředkům na jiný prostředek, pomocí webu Azure portal.
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
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547293"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Spravovaná identita přístup k prostředku přiřadit i pomocí webu Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po dokončení konfigurace k prostředku Azure s využitím spravované identity, můžete poskytnout přístup spravovaná identita na jiný prostředek, stejně jako jakýkoli zaregistrovaný objekt zabezpečení. Tento článek ukazuje, jak poskytnout přístup spravovanou identitu sadu škálování virtuálního počítače nebo virtuálního počítače Azure do účtu služby Azure storage pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Využijte RBAC pro přiřazení přístupu spravovanou identitu do jiného prostředku

Po povolení spravované identity na prostředku Azure, jako je třeba [virtuální počítač Azure](qs-configure-portal-windows-vm.md) nebo [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, v rámci kterého jste nakonfigurovali spravovanou identitu.

2. Přejděte na požadovaný prostředek, na který chcete upravit řízení přístupu. V tomto příkladu nabízíme přístupu virtuálních počítačů Azure do účtu úložiště, takže přejdeme k účtu úložiště.

3. Vyberte stránku **řízení přístupu (IAM)** daného prostředku a vyberte **+ Přidat přiřazení role**. Pak zadejte **roli**, **přiřaďte přístup k**a zadejte odpovídající **předplatné**. V rámci oblasti kritéria hledání měli byste vidět prostředku. Vyberte prostředek a vyberte **Uložit**. 

   ![Snímek obrazovky přístupu (IAM) ovládacího prvku](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Další kroky

- [Přehled spravované identity pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na](qs-configure-portal-windows-vm.md)virtuálním počítači pomocí Azure Portal.
- Postup povolení spravované identity v sadě škálování virtuálních počítačů Azure najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure v sadě škálování virtuálních počítačů pomocí Azure Portal](qs-configure-portal-windows-vmss.md).


