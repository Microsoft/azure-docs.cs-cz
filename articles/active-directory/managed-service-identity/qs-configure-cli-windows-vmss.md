---
title: Postup konfigurace systému a uživatele přiřazené identit na VMSS Azure pomocí rozhraní příkazového řádku Azure
description: Podrobné pokyny ke konfiguraci systému a uživatele přiřadit identit na VMSS Azure pomocí rozhraní příkazového řádku Azure.
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 667d62f4ad7c8e4853204c238ec889935daf82ba
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurace virtuálního počítače sady škálování spravované služby Identity (MSI) pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující operace identita spravované služby na služby Azure Virtual Machine škálování nastavit (VMSS), pomocí rozhraní příkazového řádku Azure:
- Povolení a zákaz systému přiřazené identita na VMSS Azure
- Přidání a odebrání uživatele přiřazené identita na VMSS Azure


## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systémem přiřazený a uživatel s přiřazenou identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.

Pokud chcete spustit skript příklady rozhraní příkazového řádku, máte tři možnosti:

- Použití [prostředí cloudu Azure](../../cloud-shell/overview.md) z portálu Azure (viz další část).
- Používání embedded prostředí cloudu Azure prostřednictvím "Zkuste ho" tlačítko, umístěné v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Systém přiřazené identity

V této části zjistěte, jak povolit a zakázat systému přiřazené identity pro VMSS Azure pomocí rozhraní příkazového řádku Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Povolit identita systémové přiřazené během vytváření škálovací sady virtuálního počítače Azure

Pokud chcete vytvořit škálování virtuálních počítačů, nastavit pomocí systému přiřazené identity povoleno:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Používáte účet, který je přidružen k předplatnému Azure, pod kterou chcete nasadit škálovací sadu virtuálních počítačů:

   ```azurecli-interactive
   az login
   ```

2. Vytvoření [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení vaší škálovací sadu virtuálních počítačů a jeho souvisejících prostředků pomocí [vytvořit skupinu az](/cli/azure/group/#az_group_create). Pokud už máte skupinu prostředků, které chcete použít místo toho můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvoření škálování virtuálních počítačů, nastavit pomocí [vytvořit az vmss](/cli/azure/vmss/#az_vmss_create) . Následující příklad vytvoří škálování virtuálních počítačů, nastavit s názvem *myVMSS* s identitou systému, které jsou přiřazeny, jak vyžádala `--assign-identity` parametr. `--admin-username` a `--admin-password` parametry zadejte účet správce jméno a heslo pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty jako vhodné pro vaše prostředí: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolit identita systémové přiřazené na existující sadu škálování virtuálního počítače Azure

Pokud potřebujete povolit identitě systému přiřazené na existující sadu škálování virtuálního počítače Azure:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

   ```azurecli-interactive
   az login
   ```

2. Použití [az vmss identity přiřadit](/cli/azure/vmss/identity/#az_vmss_identity_assign) příkaz, který povolí identitu systému přiřazené k existující virtuální počítač:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat identita systémové přiřazené z sady škálování virtuálního počítače Azure

> [!NOTE]
> Zakázání spravované identita služby z sadu škálování virtuálního počítače není aktuálně podporováno. Do té doby můžete přepnout mezi použitím přiřazené systému a přiřadit identity uživatelů. Vraťte se zpět pro aktualizace.

Pokud máte škálovací sadu virtuálních počítačů, které už nepotřebuje systému přiřazené identity, ale stále potřebuje přiřazeného identity uživatele, proveďte následující příkaz:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='UserAssigned' 
```

Chcete-li odebrat rozšíření virtuálního počítače MSI, použijte [odebrat az vmss identity](/cli/azure/vmss/identity/#az_vmss_remove_identity) příkaz k odebrání identitě systému přiřazené VMSS:

   ```azurecli-interactive
   az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
   ```

## <a name="user-assigned-identity"></a>Uživatel s přiřazenou identity

V této části zjistěte, jak povolit a odebrat uživatele přiřazené identitu pomocí rozhraní příkazového řádku Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Přiřazení uživatele přiřazené identity během vytváření služby Azure VMSS

Tato část vás provede procesem vytvoření VMSS a přiřazení přiřazená VMSS identity uživatele. Pokud již máte VMSS, kterou chcete použít, tuto část přeskočit a pokračovat na další.

1. Pokud už máte skupinu prostředků, kterou jste chtěli použít, můžete tento krok přeskočit. Vytvoření [skupiny prostředků](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení vaší identity uživatele přiřazené pomocí [vytvořit skupinu az](/cli/azure/group/#az_group_create). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<LOCATION>` hodnoty parametrů s vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvoření uživatele přiřazená pomocí identity [vytvoření az identity](/cli/azure/identity#az-identity-create).  `-g` Parametr určuje skupinu prostředků, kde se má vytvořit uživatel s přiřazenou identitu, a `-n` parametr určuje její název. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` hodnoty parametrů s vlastními hodnotami:

    > [!IMPORTANT]
    > Vytvoření identity uživatele přiřazené podporuje pouze alfanumerické znaky a spojovníky (0 – 9 nebo a-z nebo A-Z nebo -) znaků. Kromě toho název by měl být omezený na 24 znaků pro přiřazení virtuálního počítače nebo VMSS správně fungovat. Vraťte se zpět pro aktualizace. Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)


    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Odpověď obsahuje podrobnosti pro identitu uživatele přiřazené vytvořili, podobný následujícímu. Prostředek `id` hodnotu přiřazenou k identitě uživatele přiřazené se používá v dalším kroku.

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

3. Vytvoření pomocí VMSS [vytvořit az vmss](/cli/azure/vmss/#az-vmss-create). Následující příklad vytvoří VMSS, spojené s novou identitu uživatele, které jsou přiřazené podle specifikace `--assign-identity` parametr. Nezapomeňte nahradit `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>`, a `<USER ASSIGNED IDENTITY ID>` hodnoty parametrů s vlastními hodnotami. Pro `<USER ASSIGNED IDENTITY ID>`, použijte identitu uživatele přiřazené prostředků `id` vlastnost vytvořili v předchozím kroku: 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Přiřazení uživatele přiřazené identity existující virtuální počítač Azure

1. Vytvoření uživatele přiřazená pomocí identity [vytvoření az identity](/cli/azure/identity#az-identity-create).  `-g` Parametr určuje skupinu prostředků, kde se má vytvořit uživatel s přiřazenou identitu, a `-n` parametr určuje její název. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` hodnoty parametrů s vlastními hodnotami:

    > [!IMPORTANT]
    > Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu není aktuálně podporován. Použijte alfanumerické znaky. Vraťte se zpět pro aktualizace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Odpověď obsahuje podrobnosti pro identitu uživatele přiřazené vytvořili, podobný následujícímu. Prostředek `id` hodnotu přiřazenou k identitě uživatele přiřazené se používá v dalším kroku.

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

2. Přiřaďte identitu uživatele přiřazené k VMSS pomocí [az vmss identity přiřadit](/cli/azure/vmss/identity#az_vm_assign_identity). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. `<USER ASSIGNED IDENTITY ID>` Bude uživatel s přiřazenou identity prostředek `id` vlastnost, protože vytvořili v předchozím kroku:

    ```azurecli-interactive
    az vmss assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Odebrání uživatele přiřazené identity z VMSS Azure

> [!NOTE]
>  Odebrání všech identit uživatelů přiřazené z sadu škálování virtuálního počítače není aktuálně podporováno, pokud používáte systém přiřazené identity. 

Pokud vaše VMSS má více identit přiřazeného uživatele, můžete odebrat všechny uzly s výjimkou na posledním místě pomocí [odebrat az vmss identity](/cli/azure/vmss/identity#az-vmss-identity-remove). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. `<MSI NAME>` Je identita uživatele přiřazené název vlastnosti, která najdete v části Identita používání virtuálních počítačů `az vm show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Pokud vaše VMSS systému přiřadit i přiřazeného identity uživatele, můžete odebrat všechny uživatelské přiřadila přepínání identity používat pouze systém přiřazen. Použijte následující příkaz: 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null
```

## <a name="next-steps"></a>Další postup

- [Přehled spravované identita služby](overview.md)
- Pro úplnou Azure vytváření rychlý start sady škálování virtuálních počítačů najdete v tématu: 

  - [Vytvoření sady škálování virtuálního počítače pomocí rozhraní příkazového řádku](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















