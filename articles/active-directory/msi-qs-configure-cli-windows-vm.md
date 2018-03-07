---
title: "Postup konfigurace MSI ve virtuálním počítači Azure pomocí rozhraní příkazového řádku Azure"
description: "Krok podle podrobné pokyny pro konfiguraci a spravovaná služba Identity (MSI) ve virtuálním počítači Azure, pomocí rozhraní příkazového řádku Azure."
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: d5fc509efa0437af93326d716c37514be82e8af1
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat MSI pro virtuální počítač Azure, pomocí rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud chcete spustit skript příklady rozhraní příkazového řádku, máte tři možnosti:

- Použití [prostředí cloudu Azure](../cloud-shell/overview.md) z portálu Azure (viz další část).
- Používání embedded prostředí cloudu Azure prostřednictvím "Zkuste ho" tlačítko, umístěné v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Povolit MSI při vytváření virtuálního počítače Azure

Pro vytvoření virtuálního počítače povoleným MSI:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/#az_login). Používáte účet, který je přidružen k předplatnému Azure, pod kterou chcete nasadit virtuální počítač:

   ```azurecli-interactive
   az login
   ```

2. Vytvoření [skupiny prostředků](../azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení virtuálního počítače a jeho souvisejících prostředků pomocí [vytvořit skupinu az](/cli/azure/group/#az_group_create). Pokud už máte skupinu prostředků, které chcete použít místo toho můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvoření virtuálního počítače pomocí [vytvořit virtuální počítač az](/cli/azure/vm/#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *Můjvp* s MSI, jak vyžádala `--assign-identity` parametr. `--admin-username` a `--admin-password` parametry zadejte účet správce jméno a heslo pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty jako vhodné pro vaše prostředí: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Povolit MSI na existující virtuální počítač Azure

Pokud potřebujete povolit MSI na existující virtuální počítač:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/#az_login). Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```azurecli-interactive
   az login
   ```

2. Použití [az virtuálních počítačů přiřazení identity](/cli/azure/vm/#az_vm_assign_identity) s `--assign-identity` parametr přidat MSI do stávajícího virtuálního počítače:

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure MSI

Pokud máte virtuální počítač, který už nepotřebuje MSI:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/#az_login). Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```azurecli-interactive
   az login
   ```

2. Použití `-n ManagedIdentityExtensionForWindows` nebo `-n ManagedIdentityExtensionForLinux` přepínač (v závislosti na typu virtuálních počítačů) s [odstranit rozšíření virtuálního počítače az](https://docs.microsoft.com/cli/azure/vm/#assign-identity) odebrat soubor MSI:

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>Související obsah

- [Přehled spravované identita služby](msi-overview.md)
- Úplné vytvoření virtuálního počítače Azure Quickstarts najdete v části: 

  - [Vytvoření virtuálního počítače s Windows pomocí rozhraní příkazového řádku](../virtual-machines/windows/quick-create-cli.md)  
  - [Vytvořit virtuální počítač s Linuxem pomocí rozhraní příkazového řádku](../virtual-machines/linux/quick-create-cli.md) 

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
















