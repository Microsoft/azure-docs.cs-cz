---
title: Přiřazení přístupu ke spravované identitě k prostředku pomocí portálu Azure – Azure AD
description: Podrobné pokyny pro přiřazení spravované identity u jednoho prostředku přístup k jinému prostředku pomocí portálu Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547293"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Přiřazení přístupu ke spravované identitě k prostředku pomocí portálu Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po konfiguraci prostředku Azure se spravovanou identitou můžete udělit přístup spravované identity k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. Tento článek ukazuje, jak pomocí webu Azure portal udělit spravované identitě virtuálního počítače nebo škálovací sady virtuálních strojů přístup ke spravované identitě.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Přiřazení přístupu ke spravované identitě jinému prostředku pomocí RBAC

Po povolení spravované identity na prostředek Azure, jako je například [virtuální počítač Azure](qs-configure-portal-windows-vm.md) nebo Azure [VMSS](qs-configure-portal-windows-vmss.md):

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu přidruženého k předplatnému Azure, pod kterým jste nakonfigurovali spravovanou identitu.

2. Přejděte na požadovaný prostředek, u kterého chcete upravit řízení přístupu. V tomto příkladu poskytujeme virtuálnímu počítači Azure přístup k účtu úložiště, takže přejdeme na účet úložiště.

3. Vyberte stránku **Řízení přístupu (IAM)** prostředku a vyberte **+ Přidat přiřazení role**. Potom zadejte **roli**, **Přiřadit přístup a**zadejte odpovídající **odběr**. V oblasti kritérií vyhledávání byste měli vidět zdroj. Vyberte zdroj a vyberte **Uložit**. 

   ![Snímek obrazovky řízení přístupu (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, [přečtěte si, že najdete v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure](qs-configure-portal-windows-vm.md).
- Pokud chcete povolit spravovanou identitu na škálovací sadě virtuálních strojů Azure, přečtěte si informace [o konfiguraci spravovaných identit pro prostředky Azure na škálovací sadě virtuálních strojů pomocí portálu Azure](qs-configure-portal-windows-vmss.md).


