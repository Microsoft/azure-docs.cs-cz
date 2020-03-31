---
title: Přiřazení přístupu ke spravované identitě k prostředku pomocí azure cli – Azure AD
description: Krok za krokem pokyny pro přiřazení spravované identity na jeden prostředek, přístup k jinému prostředku, pomocí Azure CLI.
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
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b241ac223fd1eb9df2b0a914726d8f37df5f4d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547376"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Přiřazení přístupu ke spravované identitě k prostředku pomocí azure cli

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Jakmile nakonfigurujete prostředek Azure se spravovanou identitou, můžete spravovanou identitu udělit jinému prostředku, stejně jako jakýkoli objekt zabezpečení. Tento příklad ukazuje, jak udělit spravované identitě sady virtuálních zařízení Azure nebo škálovací sady virtuálních strojů k účtu úložiště Azure pomocí azure cli.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Chcete-li spustit příklady skriptu vykreslování jízdou po nesek, máte tři možnosti:
    - Azure [Cloud Shell](../../cloud-shell/overview.md) použijte z webu Azure Portal (viz další část).
    - Pomocí vloženého prostředí Azure Cloud Shell použijte pomocí tlačítka "Try It", které se nachází v pravém horním rohu každého bloku kódu.
    - [Nainstalujte nejnovější verzi Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli) pokud dáváte přednost použití místní konzoly příkazového příkazového příkazu. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Přiřazení přístupu ke spravované identitě jinému prostředku pomocí RBAC

Po povolení spravované identity na prostředek Azure, jako je virtuální [počítač Azure](qs-configure-cli-windows-vm.md) nebo [sada škálování virtuálního počítače Azure](qs-configure-cli-windows-vmss.md): 

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, pod kterým chcete nasadit škálovací sadu virtuálních počítačů nebo virtuálních strojů:

   ```azurecli-interactive
   az login
   ```

2. V tomto příkladu poskytujeme virtuálnímu počítači Azure přístup k účtu úložiště. Nejprve použijeme [seznam prostředků az](/cli/azure/resource/#az-resource-list) k získání instančního objektu pro virtuální počítač s názvem myVM:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Pro škálovací sadu virtuálních strojů Azure je příkaz stejný s výjimkou zde, získáte instanční objekt pro škálovací sadu virtuálních strojů s názvem "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Jakmile budete mít ID instančního [objektu,](/cli/azure/role/assignment#az-role-assignment-create) použijte přiřazení role az vytvořit poskytnout virtuální stroj nebo virtuální počítač škálovací sada "Reader" přístup k účtu úložiště s názvem "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, [přečtěte si, že najdete v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí rozhraní příkazového příkazu Azure](qs-configure-cli-windows-vm.md).
- Pokud chcete povolit spravovanou identitu na škálovací sadě virtuálních strojů Azure, přečtěte si informace [o konfiguraci spravovaných identit pro prostředky Azure na škálovací sadě virtuálních strojů pomocí rozhraní příkazového příkazu Azure](qs-configure-cli-windows-vmss.md).
