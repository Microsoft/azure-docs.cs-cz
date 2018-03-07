---
title: "Tom, jak přiřadit MSI přístup k prostředku Azure, pomocí rozhraní příkazového řádku Azure"
description: "Podrobné pokyny pro přiřazení MSI na jeden prostředek, přístup k jiný prostředek, pomocí rozhraní příkazového řádku Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: 90a7ec3059b6905e4aa660f538c299f3e8dedaae
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Přiřadit identita spravované služby (MSI) přístup k prostředku pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Po konfiguraci prostředek služby Azure s MSI, můžete zajistit přístup MSI pro jiný prostředek, stejně jako libovolný zaregistrovaný objekt zabezpečení. Tento příklad ukazuje, jak poskytnout přístup MSI sadě škálování virtuálního počítače nebo virtuální počítač Azure na účet úložiště Azure, pomocí rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud chcete spustit skript příklady rozhraní příkazového řádku, máte tři možnosti:

- Použití [prostředí cloudu Azure](../cloud-shell/overview.md) z portálu Azure (viz další část).
- Používání embedded prostředí cloudu Azure prostřednictvím "Zkuste ho" tlačítko, umístěné v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Používat funkci RBAC přiřazení přístupu MSI pro jiný prostředek

Můžete po povolení MSI na prostředek služby Azure, například [virtuální počítač Azure](msi-qs-configure-cli-windows-vm.md) nebo [škálovací sadu virtuálních počítačů Azure](msi-qs-configure-cli-windows-vmss.md): 

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/#az_login). Používáte účet, který je přidružen k předplatnému Azure, pod kterou chcete nasadit škálovací sadu virtuálních počítačů nebo virtuální počítač:

   ```azurecli-interactive
   az login
   ```

2. V tomto příkladu jsme se Azure virtuálnímu počítači udělíte přístup k účtu úložiště. Nejprve používáme [seznam zdrojů az](/cli/azure/resource/#az_resource_list) jak získat objekt služby pro virtuální počítač s názvem "Můjvp":

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Pro sadu škálování virtuálního počítače Azure příkaz je stejné s výjimkou tady, získejte objekt služby pro škálovací sadu virtuálních počítačů s názvem "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Jakmile máte ID objektu zabezpečení služby, použijte [vytvořit přiřazení role az](/cli/azure/role/assignment#az_role_assignment_create) chcete udělit virtuálního počítače nebo virtuálního počítače nastavte "Čtečky" přístup na účet úložiště s názvem "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Poradce při potížích

Pokud soubor MSI pro prostředek nezobrazuje v seznamu dostupných identit, ověřte, aby byla správně povolená soubor MSI. V našem případě jsme se vrátit k virtuální počítač Azure nebo sad škálování virtuálního počítače [portál Azure](https://portal.azure.com) a:

- Podívejte se na stránce "Konfigurace" a zkontrolujte MSI, povoleno = "Yes".
- Podívejte se na stránce "Rozšíření" a zkontrolujte příponou MSI úspěšně nasazena (**rozšíření** stránka není k dispozici pro sadu škálování virtuálního počítače Azure).

Pokud je buď nesprávný, musíte znovu nasaďte MSI v prostředku znovu, nebo vyřešit potíže s selhání nasazení.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Povolit MSI na virtuální počítač Azure, najdete v části [konfigurace Azure virtuálního počítače spravované služby Identity (MSI) pomocí rozhraní příkazového řádku Azure](msi-qs-configure-cli-windows-vm.md).
- Povolit MSI na sadu škálování virtuálního počítače Azure, najdete v části [konfigurace Azure Virtual Machine škálování nastavit spravované služby Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vmss.md)

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.

