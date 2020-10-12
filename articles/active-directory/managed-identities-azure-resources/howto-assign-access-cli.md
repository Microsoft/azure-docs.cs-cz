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
ms.date: 12/06/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13c69dda1e300bcff95b6a017fdeb308a6bbf3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969257"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Přiřazení spravované identity k prostředku pomocí Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po nakonfigurování prostředku Azure pomocí spravované identity můžete spravované identitě udělit přístup k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. V tomto příkladu se dozvíte, jak poskytnout přístup k účtu Azure Storage pomocí rozhraní příkazového řádku Azure pro virtuální počítač Azure nebo spravovanou identitu sady škálování virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Chcete-li spustit ukázkové skripty, máte dvě možnosti:
    - Použijte [Azure Cloud Shell](../../cloud-shell/overview.md), který můžete otevřít pomocí tlačítka **vyzkoušet** v pravém horním rohu bloků kódu.
    - Spusťte skripty místně pomocí instalace nejnovější verze rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)a pak se přihlaste k Azure pomocí [AZ Login](/cli/azure/reference-index#az-login). Použijte účet přidružený k předplatnému Azure, ve kterém byste chtěli vytvářet prostředky.

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
