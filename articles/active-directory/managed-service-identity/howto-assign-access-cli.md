---
title: Přiřazení přístupu MSI k prostředku Azure, pomocí rozhraní příkazového řádku Azure
description: Podrobné pokyny pro přiřazování MSI na jeden prostředek, přístup k jiný zdroj, pomocí rozhraní příkazového řádku Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: a5da06eac7f4680282aad305f57cb9ca1c9d5730
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424428"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Přiřadit Identity spravované služby (MSI) přístup k prostředku pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po dokončení konfigurace k prostředku Azure pomocí MSI, můžete poskytnout přístup MSI do jiného prostředku, stejně jako libovolný objekt zabezpečení. Tento příklad ukazuje, jak poskytnout přístup MSI sadu škálování virtuálního počítače nebo virtuálního počítače Azure do účtu služby Azure storage pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:

- Použití [Azure Cloud Shell](../../cloud-shell/overview.md) z portálu Azure portal (viz další část).
- Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly příkazového řádku. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Využijte RBAC pro přiřazení přístupu MSI k jinému prostředku

Po povolení MSI v prostředku Azure, jako například [virtuálních počítačů Azure](qs-configure-cli-windows-vm.md) nebo [škálovací sady virtuálních počítačů Azure](qs-configure-cli-windows-vmss.md): 

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, ve které chcete nasadit škálovací sadu virtuálního počítače nebo virtuálního počítače:

   ```azurecli-interactive
   az login
   ```

2. V tomto příkladu nabízíme přístupu virtuálních počítačů Azure do účtu úložiště. Nejprve používáme [az resource list](/cli/azure/resource/#az-resource-list) získat instanční objekt služby pro virtuální počítač s názvem "myVM":

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

## <a name="troubleshooting"></a>Řešení potíží

Pokud soubor MSI pro prostředek není uveden v seznamu dostupných identit, ověřte správně povolené MSI. V našem případě můžeme přejít zpět na virtuálním počítači Azure nebo škálovací sady virtuálních počítačů [webu Azure portal](https://portal.azure.com) a:

- Podívejte se na stránce "Konfigurace" a zajistit MSI, povoleno = "Yes".
- Podívejte se na stránce "Rozšíření" a zajistit úspěšné nasazení rozšíření MSI (**rozšíření** stránka není dostupná pro škálovací sady virtuálních počítačů Azure).

Pokud je buď nesprávný, budete muset znovu znovu provádět nasazení MSI pro váš prostředek ani řešení potíží s nasazení se nezdařilo.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](overview.md).
- Pokud chcete povolit MSI ve virtuálním počítači Azure, najdete v článku [konfigurace Azure VM Identity spravované služby (MSI) pomocí Azure CLI](qs-configure-cli-windows-vm.md).
- Povolení MSI na škálovací sadu virtuálních počítačů Azure, najdete v článku [konfigurace Azure virtuální počítač Škálovací nastavení Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vmss.md)

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.

