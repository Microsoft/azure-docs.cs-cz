---
title: Přiřazení spravované identity k prostředku pomocí Azure CLI – Azure AD
description: Podrobné pokyny pro přiřazení spravované identity k jednomu prostředku a přístup k jinému prostředku pomocí Azure CLI.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3b06ce76ae77aa62b20b707a736e8e20e5f6c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99090040"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Přiřazení spravované identity k prostředku pomocí Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po nakonfigurování prostředku Azure pomocí spravované identity můžete spravované identitě udělit přístup k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. V tomto příkladu se dozvíte, jak poskytnout přístup k účtu Azure Storage pomocí rozhraní příkazového řádku Azure pro virtuální počítač Azure nebo spravovanou identitu sady škálování virtuálního počítače.

Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si téma [co jsou spravované identity pro prostředky Azure?](overview.md). Další informace o spravovaných typech identity přiřazených systémem a uživatelem definovaných uživatelů najdete v tématu [spravované identity typu](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Pomocí Azure RBAC přiřaďte přístup spravované identity k jinému prostředku.

Po povolení spravované identity na prostředku Azure, jako je třeba [virtuální počítač Azure](qs-configure-cli-windows-vm.md) nebo [sada škálování virtuálního počítače Azure](qs-configure-cli-windows-vmss.md): 

1. V tomto příkladu poskytujeme virtuálnímu počítači Azure přístup k účtu úložiště. Nejprve k získání instančního objektu pro virtuální počítač s názvem myVM používáme [AZ Resource list](/cli/azure/resource/#az-resource-list) :

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   V případě sady škálování virtuálních počítačů Azure je příkaz stejný, s výjimkou této služby, získáte instanční objekt pro sadu škálování virtuálního počítače s názvem "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Jakmile budete mít ID instančního objektu, použijte příkaz [AZ role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) a udělte tak virtuálnímu počítači nebo čtečce sady škálování virtuálního počítače přístup k účtu úložiště s názvem "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure CLI](qs-configure-cli-windows-vm.md).
- Postup povolení spravované identity v sadě škálování virtuálních počítačů Azure najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure v sadě škálování virtuálního počítače pomocí](qs-configure-cli-windows-vmss.md)rozhraní příkazového řádku Azure.
