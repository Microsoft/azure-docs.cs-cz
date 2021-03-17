---
title: Konfigurace spravovaných identit v sadě škálování virtuálního počítače – Azure CLI – Azure AD
description: Podrobné pokyny pro konfiguraci spravovaných identit systémových a uživatelem definovaných identit v sadě škálování virtuálních počítačů Azure pomocí Azure CLI.
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
ms.openlocfilehash: 0f88eccc524ffac424f8f5048990f693aa67613d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590932"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurace spravovaných identit pro prostředky Azure v sadě škálování virtuálního počítače pomocí Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující spravované identity pro operace prostředků Azure v sadě škálování virtuálních počítačů Azure pomocí rozhraní příkazového řádku Azure:
- Povolení a zakázání spravované identity přiřazené systémem v sadě škálování virtuálních počítačů Azure
- Přidání a odebrání spravované identity přiřazené uživatelem v sadě škálování virtuálních počítačů Azure

Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si téma [co jsou spravované identity pro prostředky Azure?](overview.md). Další informace o spravovaných typech identity přiřazených systémem a uživatelem definovaných uživatelů najdete v tématu [spravované identity typu](overview.md#managed-identity-types).

- K provedení operací správy v tomto článku potřebuje váš účet následující přiřazení řízení přístupu na základě role Azure:

  - [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) , aby vytvořil sadu škálování virtuálního počítače a povolil a odebral systémovou a/nebo uživatelsky spravovanou identitu ze sady škálování virtuálního počítače.

  - Role [Přispěvatel spravovaných identit](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) k vytvoření spravované identity přiřazené uživatelem.

  - Role [operátora spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) k přiřazení a odebrání spravované identity přiřazené uživatelem z a do sady škálování virtuálního počítače.

  > [!NOTE]
  > Nevyžadují se žádné další přiřazení role adresáře Azure AD.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravovanou identitu přiřazenou systémem pro sadu škálování virtuálního počítače Azure pomocí Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření sady škálování virtuálních počítačů Azure

Vytvoření sady škálování virtuálního počítače s povolenou spravovanou identitou přiřazenou systémem:

1. Vytvořte [skupinu prostředků](../../azure-resource-manager/management/overview.md#terminology) pro omezení a nasazení sady škálování virtuálních počítačů a souvisejících prostředků pomocí [AZ Group Create](/cli/azure/group/#az-group-create). Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. [Vytvořte](/cli/azure/vmss/#az-vmss-create) sadu škálování virtuálního počítače. Následující příklad vytvoří sadu škálování virtuálního počítače s názvem *myVMSS* s spravovanou identitou přiřazenou systémem, jak je požadováno `--assign-identity` parametrem. Parametry `--admin-username` a `--admin-password` určují uživatelské jméno a heslo účtu správce pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty odpovídajícím způsobem pro vaše prostředí: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolit spravovanou identitu přiřazenou systémem v existující sadě škálování virtuálních počítačů Azure

Pokud potřebujete [Povolit](/cli/azure/vmss/identity/#az-vmss-identity-assign) spravovanou identitu přiřazenou systémem v existující sadě škálování virtuálního počítače Azure:

```azurecli-interactive
az vmss identity assign -g myResourceGroup -n myVMSS
```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat spravovanou identitu přiřazenou systémem ze sady škálování virtuálních počítačů Azure

Pokud máte sadu škálování virtuálního počítače, která už nepotřebuje spravovanou identitu přiřazenou systémem, ale přesto potřebuje spravované identity přiřazené uživatelem, použijte následující příkaz:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Pokud máte virtuální počítač, který už nepotřebuje spravovanou identitu přiřazenou systémem a nemá žádné spravované identity přiřazené uživatelem, použijte následující příkaz:

> [!NOTE]
> Hodnota rozlišuje `none` velká a malá písmena. Musí být malými písmeny. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak povolit a odebrat spravovanou identitu přiřazenou uživatelem pomocí Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživatelem během vytváření sady škálování virtuálního počítače

V této části se seznámíte s vytvořením sady škálování virtuálního počítače a přiřazením spravované identity přiřazené uživatelem do sady škálování virtuálního počítače. Pokud už máte sadu škálování virtuálního počítače, kterou chcete použít, přeskočte tuto část a pokračujte na další.

1. Pokud již máte skupinu prostředků, kterou byste chtěli použít, můžete tento krok přeskočit. Vytvořte [skupinu prostředků](~/articles/azure-resource-manager/management/overview.md#terminology) pro zahrnutí a nasazení spravované identity přiřazené uživatelem pomocí [AZ Group Create](/cli/azure/group/#az-group-create). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<LOCATION>` vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` určuje skupinu prostředků, ve které se spravovaná identita přiřazená uživatelem vytvoří, a parametr `-n` určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Odpověď obsahuje podrobnosti o vytvořené spravované identitě přiřazené uživatelem, podobně jako v následujícím příkladu. Hodnota prostředku `id` přiřazená spravované identitě přiřazené uživateli se používá v následujícím kroku.

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

3. [Vytvořte](/cli/azure/vmss/#az-vmss-create) sadu škálování virtuálního počítače. Následující příklad vytvoří sadu škálování virtuálního počítače přidruženou k nové spravované identitě přiřazené uživatelem, jak je uvedeno v `--assign-identity` parametru. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` a `<USER ASSIGNED IDENTITY>` vlastními hodnotami. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživatelem do existující sady škálování virtuálních počítačů

1. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` určuje skupinu prostředků, ve které se spravovaná identita přiřazená uživatelem vytvoří, a parametr `-n` určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpověď obsahuje podrobnosti o vytvořené spravované identitě přiřazené uživatelem, podobně jako v následujícím příkladu.

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

2. [Přiřaďte](/cli/azure/vmss/identity) k sadě škálování virtuálního počítače spravovanou identitu přiřazenou uživatelem. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VIRTUAL MACHINE SCALE SET NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY>`Je uživatelem přiřazená vlastnost prostředku identity `name` , jak je vytvořená v předchozím kroku:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Odebrání spravované identity přiřazené uživatelem ze sady škálování virtuálních počítačů Azure

K [Odebrání](/cli/azure/vmss/identity#az-vmss-identity-remove) spravované identity přiřazené uživatelem ze sady škálování virtuálního počítače použijte `az vmss identity remove` . Pokud se jedná o jedinou spravovanou identitu přiřazenou uživatelem přiřazenou k sadě škálování virtuálního počítače, odebere se `UserAssigned` z hodnoty typ identity.  Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VIRTUAL MACHINE SCALE SET NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY>`Bude to uživatelská vlastnost přiřazená uživatelem `name` , kterou najdete v části Identita sady škálování virtuálních počítačů pomocí `az vmss identity show` :

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Pokud vaše sada škálování virtuálního počítače nemá spravovanou identitu přiřazenou systémem a chcete z ní odebrat všechny spravované identity přiřazené uživatelem, použijte následující příkaz:

> [!NOTE]
> Hodnota rozlišuje `none` velká a malá písmena. Musí být malými písmeny.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Pokud vaše sada škálování virtuálního počítače obsahuje spravované identity přiřazené systémem i uživatelem, můžete odebrat všechny identity přiřazené uživatelem, a to přepnutím na použití spravované identity přiřazené systémem. Použijte následující příkaz:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Úplný úvodní režim pro vytváření škálování virtuálních počítačů Azure najdete v tématu [Vytvoření sady škálování virtuálních počítačů pomocí](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set) rozhraní PŘÍKAZového řádku.
