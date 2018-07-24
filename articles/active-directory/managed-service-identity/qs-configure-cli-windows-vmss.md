---
title: Přiřadit konfigurace systémových a uživatelských identit na VMSS Azure pomocí Azure CLI
description: Podrobné pokyny ke konfiguraci systémových a uživatelských identit přiřazených na VMSS Azure pomocí Azure CLI.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: fe0b2531ef4bb85513d63207b903ee14b6652fc0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216255"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurace virtuálního počítače škálovací sady Identity spravované služby (MSI) pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující operace identita spravované služby se na Azure virtuální počítač Škálovací nastavit (VMSS), pomocí rozhraní příkazového řádku Azure:
- Povolit nebo zakázat identitu na Azure VMSS přiřazenou systémem
- Přidávat a odebírat uživatele přiřazeny identitu na Azure VMSS


## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) chcete vytvořit škálovací sadu virtuálních počítačů a povolit a odeberte systém a/nebo uživatel přiřazenou spravovanou identitu ze škálovací sady virtuálních počítačů.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role pro vytvoření identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat identity přiřazené uživateli, ze kterých a do škálovací sady virtuálních počítačů.
- Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:
    - Použití [Azure Cloud Shell](../../cloud-shell/overview.md) z portálu Azure portal (viz další část).
    - Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
    - [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly příkazového řádku. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem

V této části se dozvíte, jak můžete povolit nebo zakázat systému identity přiřazené pro VMSS Azure pomocí Azure CLI.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Povolit identitu přiřazenou systémem při vytváření škálovací sady virtuálních počítačů Azure

Vytvoření virtuálního počítače škálovací sady s přiřazenou identity povoleno systémem:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az_login). Použijte účet, který je přidružený k předplatnému Azure, ve které chcete nasadit škálovací sadu virtuálních počítačů:

   ```azurecli-interactive
   az login
   ```

2. Vytvoření [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení škálovací sady virtuálních počítačů a její související prostředky, pomocí [vytvořit skupiny az](/cli/azure/group/#az_group_create). Pokud už máte skupinu prostředků, kterou chcete použít místo toho můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvoření virtuálního počítače škálovací sady s použitím [az vmss vytvořit](/cli/azure/vmss/#az_vmss_create) . Následující příklad vytvoří škálovací sadu virtuálních počítačů *myVMSS* s identitou přiřazenou systémem, jak to požadoval `--assign-identity` parametru. Parametry `--admin-username` a `--admin-password` určují uživatelské jméno a heslo účtu správce pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty odpovídajícím způsobem pro vaše prostředí: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolit identitu přiřazenou systémem v existující škálovací sady virtuálních počítačů Azure

Pokud je potřeba povolit identitu přiřazenou systémem v existující škálovací sady virtuálních počítačů Azure:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az_login). Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

   ```azurecli-interactive
   az login
   ```

2. Použití [az vmss identity přiřadit](/cli/azure/vmss/identity/#az_vmss_identity_assign) příkaz, který umožní identitou přiřazenou systémem do existujícího virtuálního počítače:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat identitu přiřazenou systémem ze škálovací sady virtuálních počítačů Azure

> [!NOTE]
> Zakáže identita spravované služby ze Škálovací sady virtuálních počítačů se aktuálně nepodporuje. Do té doby můžete přepínat mezi použitím systém přiřadil a přiřazené identity uživatele. Vraťte se sem a přečtěte si nové informace.

Pokud máte škálovací sadu virtuálních počítačů, které už nepotřebuje identitu přiřazenou systémem, ale stále potřebuje identity přiřazené uživateli, proveďte následující příkaz:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='UserAssigned' 
```

Chcete-li odebrat rozšíření MSI virtuálního počítače, použijte [az vmss identity odebrat](/cli/azure/vmss/identity/#az_vmss_remove_identity) příkazu k odebrání identitou přiřazenou systémem VMSS:

   ```azurecli-interactive
   az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
   ```

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části se dozvíte, jak povolit a odebrání identity přiřazené uživateli, pomocí Azure CLI.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Přiřadit uživatele identity přiřazené během vytváření Azure VMSS

Tato část vás provede vytváření VMSS a přiřazení uživatele VMSS přiřazené identity. Pokud už máte VMSS, kterou chcete použít, tuto část přeskočit a pokračovat na další.

1. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit. Vytvoření [skupiny prostředků](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení vaší identity přiřazené uživateli pomocí [vytvořit skupiny az](/cli/azure/group/#az_group_create). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<LOCATION>` vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvoření uživatele přiřadit pomocí identity [vytvoření az identity](/cli/azure/identity#az-identity-create).  `-g` Parametr určuje skupinu prostředků, ve kterém se vytvoří identity přiřazené uživateli, a `-n` parametr určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Odpověď obsahuje podrobnosti o identity přiřazené uživateli vytvořené, podobně jako následující. Prostředek `id` hodnota přiřazená k identity přiřazené uživateli se používá v následujícím kroku.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Vytvoření pomocí VMSS [az vmss vytvořit](/cli/azure/vmss/#az-vmss-create). Následující příklad vytvoří VMSS přidružené k nové identity přiřazené uživateli, jak jsou určené `--assign-identity` parametru. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` a `<USER ASSIGNED IDENTITY ID>` vlastními hodnotami. Pro `<USER ASSIGNED IDENTITY ID>`, používat identity přiřazené uživateli prostředek `id` vlastnost vytvořenou v předchozím kroku: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Přiřazování identity přiřazené do stávajícího virtuálního počítače Azure uživateli

1. Vytvoření uživatele přiřadit pomocí identity [vytvoření az identity](/cli/azure/identity#az-identity-create).  `-g` Parametr určuje skupinu prostředků, ve kterém se vytvoří identity přiřazené uživateli, a `-n` parametr určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

    > [!IMPORTANT]
    > Vytváří se uživatel přiřazené identity se speciálními znaky (třeba podtržítko) v názvu se momentálně nepodporuje. Použijte prosím alfanumerické znaky. Vraťte se sem a přečtěte si nové informace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Odpověď obsahuje podrobnosti o identity přiřazené uživateli vytvořené, podobně jako následující. Prostředek `id` hodnota přiřazená k identity přiřazené uživateli se používá v následujícím kroku.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Přiřadit identity přiřazené uživateli na VMSS pomocí [az vmss identity přiřadit](/cli/azure/vmss/identity#az_vm_assign_identity). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VMSS NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY ID>` Bude prostředek identity přiřazené uživateli `id` vlastnost, protože vytvořili v předchozím kroku:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Odebrání uživatele ze Azure VMSS identity přiřazené

> [!NOTE]
>  Odebrání všech uživatelsky přiřazených identit ze Škálovací sady virtuálních počítačů se aktuálně nepodporuje, pokud máte identitu přiřazenou systémem. 

Pokud vaše VMSS má více uživatelsky přiřazených identit, můžete odebrat všechny kromě poslední pomocí [az vmss identity odebrat](/cli/azure/vmss/identity#az-vmss-identity-remove). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VMSS NAME>` vlastními hodnotami. `<MSI NAME>` Je vlastnost name identity přiřazené uživateli, které najdete v části Identita virtuálního počítače pomocí `az vm show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <MSI NAME>
```
Pokud vaše VMSS má přiřazenou systémem a identity přiřazené uživateli, můžete odebrat všechny uživatele identit přiřazených přepnutím používat pouze přiřazenou systémem. Použijte následující příkaz: 

```azurecli-interactive
az vmss update -n <VMSS NAME> -g <RESOURCE GROUP> --set identity.type='SystemAssigned' identity.identityIds=null
```

## <a name="next-steps"></a>Další postup

- [Přehled spravované Identity služby](overview.md)
- Pro úplnou Azure škálovací sady virtuálních počítačů vytváření rychlý start, naleznete v tématu: 

  - [Vytvoření Škálovací sady virtuálních počítačů pomocí rozhraní příkazového řádku](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















