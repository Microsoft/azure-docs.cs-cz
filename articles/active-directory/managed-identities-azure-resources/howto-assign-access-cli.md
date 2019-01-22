---
title: Přiřazení spravovanou identitu přístup k prostředku Azure pomocí Azure CLI
description: Podrobné pokyny pro přiřazování spravovanou identitu na jeden prostředek, přístup k jiný zdroj, pomocí rozhraní příkazového řádku Azure.
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
ms.date: 12/06/2017
ms.author: daveba
ms.openlocfilehash: ffed9e388b41b6c19ae8ed22b9c843f5ac8ceb3e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430411"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Přiřazení přístupu spravovanou identitu prostředku pomocí rozhraní příkazového řádku Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po dokončení konfigurace k prostředku Azure s využitím spravované identity, můžete poskytnout přístup spravovaná identita na jiný prostředek, stejně jako jakýkoli zaregistrovaný objekt zabezpečení. Tento příklad ukazuje, jak poskytnout přístup spravovanou identitu sadu škálování virtuálního počítače nebo virtuálního počítače Azure do účtu služby Azure storage pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:
    - Použití [Azure Cloud Shell](../../cloud-shell/overview.md) z portálu Azure portal (viz další část).
    - Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
    - [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Pokud byste radši chtěli použít místní konzoly příkazového řádku. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Využijte RBAC pro přiřazení přístupu spravovanou identitu do jiného prostředku

Po povolení identity spravované v prostředku Azure, jako například [virtuálních počítačů Azure](qs-configure-cli-windows-vm.md) nebo [škálovací sady virtuálních počítačů Azure](qs-configure-cli-windows-vmss.md): 

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, ve které chcete nasadit škálovací sadu virtuálního počítače nebo virtuálního počítače:

   ```azurecli-interactive
   az login
   ```

2. V tomto příkladu nabízíme přístupu virtuálních počítačů Azure do účtu úložiště. Nejprve používáme [az resource list](/cli/azure/resource/#az-resource-list) zobrazíte instanční objekt služby pro virtuální počítač s názvem můjvp přesměrovat:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Pro škálovací sadu virtuálních počítačů Azure příkaz je stejné, až sem, získejte objekt služby pro škálovací sadu virtuálních počítačů s názvem "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Jakmile máte ID instančního objektu služby, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az-role-assignment-create) virtuálního počítače nebo virtuálního počítače škálovací nastavte možnost Čtenář,"přístup na účet úložiště s názvem"myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Další postup

- [Spravované identity pro prostředky Azure – přehled](overview.md)
- Povolit spravované identity na virtuálním počítači Azure, najdete v článku [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Azure CLI](qs-configure-cli-windows-vm.md).
- Povolit spravované identity na škálovací sadu virtuálních počítačů Azure, najdete v článku [konfigurace spravovaných identit pro prostředky Azure na virtuální počítač škálovací sady pomocí Azure CLI](qs-configure-cli-windows-vmss.md).