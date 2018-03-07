---
title: "Konfigurace MSI ve virtuálním počítači Azure měřítku nastavit pomocí rozhraní příkazového řádku Azure"
description: "Krok podle podrobné pokyny ke konfiguraci a spravovaná služba Identity (MSI) na Azure sadu škálování virtuálního počítače, pomocí rozhraní příkazového řádku Azure."
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: c58ad9cc8bfa16b11201735bcc513e6dd692a2a9
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurace virtuálního počítače sady škálování spravované služby Identity (MSI) pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak povolit a odebrat MSI horizontálního virtuální počítač Azure nastavit pomocí rozhraní příkazového řádku Azure.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud chcete spustit skript příklady rozhraní příkazového řádku, máte tři možnosti:

- Použití [prostředí cloudu Azure](../cloud-shell/overview.md) z portálu Azure (viz další část).
- Používání embedded prostředí cloudu Azure prostřednictvím "Zkuste ho" tlačítko, umístěné v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Povolit MSI během vytváření škálovací sady virtuálního počítače Azure

Chcete-li vytvořit povoleným MSI škálování virtuálních počítačů nastavte:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/#az_login). Používáte účet, který je přidružen k předplatnému Azure, pod kterou chcete nasadit škálovací sadu virtuálních počítačů:

   ```azurecli-interactive
   az login
   ```

2. Vytvoření [skupiny prostředků](../azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení vaší škálovací sadu virtuálních počítačů a jeho souvisejících prostředků pomocí [vytvořit skupinu az](/cli/azure/group/#az_group_create). Pokud už máte skupinu prostředků, které chcete použít místo toho můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvoření škálování virtuálních počítačů, nastavit pomocí [vytvořit az vmss](/cli/azure/vmss/#az_vmss_create) . Následující příklad vytvoří škálování virtuálních počítačů, nastavit s názvem *myVMSS* s MSI, jak vyžádala `--assign-identity` parametr. `--admin-username` a `--admin-password` parametry zadejte účet správce jméno a heslo pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty jako vhodné pro vaše prostředí: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>Povolit MSI na existující sadu škálování virtuálního počítače Azure

Pokud potřebujete povolit MSI na existující sadu škálování virtuálního počítače Azure:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/#az_login). Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

   ```azurecli-interactive
   az login
   ```

2. Použití [az vmss přiřazení identity](/cli/azure/vm/#az_vmss_assign_identity) s `--assign-identity` parametr přidat MSI do stávajícího virtuálního počítače:

   ```azurecli-interactive
   az vmss assign-identity -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Odebrání MSI sadu škálování virtuálního počítače Azure

Pokud máte škálovací sadu virtuálních počítačů, které už nepotřebuje MSI:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/#az_login). Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

   ```azurecli-interactive
   az login
   ```

2. Použití `--identities` přepínač s [az vmss remove-identity](/cli/azure/vmss/#az_vmss_remove_identity) odebrat soubor MSI:

   ```azurecli-interactive
   az vmss remove-identity -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>Další postup

- [Přehled spravované identita služby](msi-overview.md)
- Pro úplnou Azure vytváření rychlý start sady škálování virtuálních počítačů najdete v tématu: 

  - [Vytvoření sady škálování virtuálního počítače pomocí rozhraní příkazového řádku](../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
















