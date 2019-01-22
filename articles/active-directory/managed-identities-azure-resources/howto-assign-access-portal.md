---
title: Přiřazení spravovaných identit přístup k prostředku Azure pomocí webu Azure portal
description: Podrobné pokyny pro přiřazování spravovanou identitu na jeden přístup k prostředkům na jiný prostředek, pomocí webu Azure portal.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: ae01087e0430049036da74a36d6155826c02aa00
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436361"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Spravovaná identita přístup k prostředku přiřadit i pomocí webu Azure portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po dokončení konfigurace k prostředku Azure s využitím spravované identity, můžete poskytnout přístup spravovaná identita na jiný prostředek, stejně jako jakýkoli zaregistrovaný objekt zabezpečení. Tento článek ukazuje, jak poskytnout přístup spravovanou identitu sadu škálování virtuálního počítače nebo virtuálního počítače Azure do účtu služby Azure storage pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Využijte RBAC pro přiřazení přístupu spravovanou identitu do jiného prostředku

Po povolení identity spravované v prostředku Azure, jako například [virtuálního počítače Azure](qs-configure-portal-windows-vm.md) nebo [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu přidružených k předplatnému Azure, ve které jste nakonfigurovali spravovaná identita.

2. Přejděte na požadovaný prostředek, na který chcete upravit řízení přístupu. V tomto příkladu nabízíme přístupu virtuálních počítačů Azure do účtu úložiště, takže přejdeme k účtu úložiště.

3. Vyberte **řízení přístupu (IAM)** stránce prostředků a vyberte **+ přidat přiřazení role**. Zadejte **Role**, **přiřadit přístup k**a zadejte odpovídající **předplatné**. V rámci oblasti kritéria hledání měli byste vidět prostředku. Vyberte prostředek a vyberte **Uložit**. 

   ![Snímek obrazovky přístupu (IAM) ovládacího prvku](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Další postup

- [Spravovaná identita pro prostředky Azure – přehled](overview.md)
- Povolit spravované identity na virtuálním počítači Azure, najdete v článku [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí webu Azure portal](qs-configure-portal-windows-vm.md).
- Povolit spravované identity na škálovací sadu virtuálních počítačů Azure, najdete v článku [konfigurace spravovaných identit pro prostředky Azure na virtuální počítač škálovací sady s použitím webu Azure portal](qs-configure-portal-windows-vmss.md).


