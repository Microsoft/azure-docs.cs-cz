---
title: Konfigurace spravovaných identit na virtuálním počítači Azure pomocí Azure CLI – Azure AD
description: Podrobné pokyny pro konfiguraci spravovaných identit systémových a uživatelem na virtuálním počítači Azure pomocí Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90b18c2556796f23be0c1135b0bad0d53368a46d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590949"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se pomocí Azure CLI naučíte, jak provádět následující spravované identity pro operace prostředků Azure na virtuálním počítači Azure:

- Povolení a zakázání spravované identity přiřazené systémem na virtuálním počítači Azure
- Přidání a odebrání spravované identity přiřazené uživatelem na virtuálním počítači Azure

Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si téma [co jsou spravované identity pro prostředky Azure?](overview.md). Další informace o spravovaných typech identity přiřazených systémem a uživatelem definovaných uživatelů najdete v tématu [spravované identity typu](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravovanou identitu přiřazenou systémem na virtuálním počítači Azure pomocí Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření virtuálního počítače Azure

Aby bylo možné vytvořit virtuální počítač Azure s povolenou spravovanou identitou přiřazenou systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Pomocí příkazu [az group create](/cli/azure/group/#az-group-create) vytvořte [skupinu prostředků](../../azure-resource-manager/management/overview.md#terminology) pro nasazení a uchování virtuálního počítače a souvisejících prostředků. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm/#az-vm-create). Následující příklad vytvoří virtuální počítač s názvem *myVM* se spravovanou identitou přiřazenou systémem, jak je požadováno `--assign-identity` parametrem. Parametry `--admin-username` a `--admin-password` určují uživatelské jméno a heslo účtu správce pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty odpovídajícím způsobem pro vaše prostředí: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Povolení spravované identity přiřazené systémem na stávajícím virtuálním počítači Azure

Aby se na virtuálním počítači povolila spravovaná identita přiřazená systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, které obsahuje virtuální počítač.

   ```azurecli-interactive
   az login
   ```

2. Pomocí příkazu [AZ VM identity Assign](/cli/azure/vm/identity/) příkazem `identity assign` Povolte identitu přiřazenou systému k existujícímu virtuálnímu počítači:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Zakázání identity přiřazené systémem z virtuálního počítače Azure

Aby se na virtuálním počítači zakázala spravovaná identita přiřazená systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

Pokud máte virtuální počítač, který už nepotřebuje identitu přiřazenou systémem, ale potřebuje identity přiřazené uživatelem, použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Pokud máte virtuální počítač, který už nepotřebuje identitu přiřazenou systémem a nemá žádné identity přiřazené uživatelem, použijte následující příkaz:

> [!NOTE]
> Hodnota rozlišuje `none` velká a malá písmena. Musí být malými písmeny. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat spravovanou identitu přiřazenou uživatelem z virtuálního počítače Azure pomocí Azure CLI. Pokud vytvoříte spravovanou identitu přiřazenou uživatelem v jiném RG než na svém VIRTUÁLNÍm počítači. Pro přiřazení k vašemu VIRTUÁLNÍmu počítači budete muset použít adresu URL spravované identity.
tj.--identity "/Subscriptions/ <SUBID> /ResourceGroups/ <RESROURCEGROUP> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>"

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Přiřazení spravované identity přiřazené uživatelem během vytváření virtuálního počítače Azure

Aby bylo možné přiřadit uživatelem přiřazenou identitu k VIRTUÁLNÍmu počítači během jejího vytvoření, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a [spravovaného operátoru identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Pokud již máte skupinu prostředků, kterou byste chtěli použít, můžete tento krok přeskočit. Vytvořte [skupinu prostředků](~/articles/azure-resource-manager/management/overview.md#terminology) pro zahrnutí a nasazení spravované identity přiřazené uživatelem pomocí [AZ Group Create](/cli/azure/group/#az-group-create). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<LOCATION>` vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` určuje skupinu prostředků, ve které se spravovaná identita přiřazená uživatelem vytvoří, a parametr `-n` určuje její název.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Odpověď obsahuje podrobnosti o vytvořené spravované identitě přiřazené uživatelem, podobně jako v následujícím příkladu. V následujícím kroku se používá hodnota ID prostředku přiřazená spravované identitě přiřazené uživateli.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
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

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm/#az-vm-create). Následující příklad vytvoří virtuální počítač přidružený k nové uživatelsky přiřazené identitě, jak je určeno `--assign-identity` parametrem. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Přiřazení spravované identity přiřazené uživateli k existujícímu virtuálnímu počítači Azure

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Vytvořte identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  `-g`Parametr určuje skupinu prostředků, ve které je vytvořena uživatelem přiřazená identita, a `-n` parametr určí jeho název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

    > [!IMPORTANT]
    > Vytváření uživatelem přiřazených spravovaných identit se speciálními znaky (tj. podtržítko) v názvu se momentálně nepodporuje. Používejte prosím alfanumerické znaky. Vraťte se sem a přečtěte si nové informace.  Další informace najdete v tématu [Nejčastější dotazy a známé problémy](known-issues.md) .

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpověď obsahuje podrobnosti o vytvořené spravované identitě přiřazené uživatelem, podobně jako v následujícím příkladu. 

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

2. Přiřaďte k VIRTUÁLNÍmu počítači identitu přiřazenou uživatelem pomocí [AZ VM identity Assign](/cli/azure/vm). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY NAME>`Je uživatelem přiřazená vlastnost prostředku spravované identity `name` , jak je vytvořená v předchozím kroku. Pokud jste vytvořili spravovanou identitu přiřazenou uživatelem v jiném RG, než je váš virtuální počítač. Budete muset použít adresu URL spravované identity.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání spravované identity přiřazené uživatelem z virtuálního počítače Azure

K odebrání uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači vyžaduje váš účet přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . 

Pokud se jedná o jedinou spravovanou identitu přiřazenou uživatelem, která je přiřazená k virtuálnímu počítači, odebere se `UserAssigned` z hodnoty typ identity.  Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY>`Bude to uživatelská vlastnost identity přiřazená uživatelem `name` , kterou najdete v části Identita virtuálního počítače pomocí `az vm identity show` :

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Pokud váš virtuální počítač nemá spravovanou identitu přiřazenou systémem a chcete z něj odebrat všechny identity přiřazené uživatelem, použijte následující příkaz:

> [!NOTE]
> Hodnota rozlišuje `none` velká a malá písmena. Musí být malými písmeny.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Pokud má váš virtuální počítač identity přiřazené systémem i uživatelem přiřazené identity, můžete odebrat všechny identity přiřazené uživatelem, a to přepnutím na použití pouze přiřazené systémem. Použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Další kroky
- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Kompletní rychlé starty pro vytváření virtuálních počítačů Azure najdete v těchto tématech: 
  - [Vytvoření virtuálního počítače s Windows pomocí rozhraní příkazového řádku](../../virtual-machines/windows/quick-create-cli.md)  
  - [Vytvoření virtuálního počítače se systémem Linux pomocí rozhraní příkazového řádku](../../virtual-machines/linux/quick-create-cli.md)
