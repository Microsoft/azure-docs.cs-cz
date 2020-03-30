---
title: Konfigurace spravovaných identit na virtuálním počítači Azure pomocí rozhraní příkazového příkazu Azure – Azure AD
description: Krok za krokem pokyny pro konfiguraci systému a uživatelem přiřazené spravované identity na virtuálním počítači Azure pomocí rozhraní příkazového příkazu Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f2efaceefc53b3c0b5dfd899baf9fd30fdf9a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244144"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí rozhraní příkazového příkazu Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu. 

V tomto článku se pomocí příkazového příkazu k onomu Azure dozvíte, jak provádět následující spravované identity pro operace prostředků Azure na virtuálním počítači Azure:

- Povolení a zakázání spravované identity přiřazené k systému na virtuálním počítači Azure
- Přidání a odebrání spravované identity přiřazené uživateli na virtuálním počítači Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Chcete-li spustit příklady skriptu vykreslování jízdou po nesek, máte tři možnosti:
    - Azure [Cloud Shell](../../cloud-shell/overview.md) použijte z webu Azure Portal (viz další část).
    - Pomocí vloženého prostředí Azure Cloud Shell použijte pomocí tlačítka "Try It", které se nachází v pravém horním rohu každého bloku kódu.
    - [Nainstalujte nejnovější verzi azure cli,](https://docs.microsoft.com/cli/azure/install-azure-cli) pokud dáváte přednost použití místní konzoly příkazového příkazového příkazu. 
      
      > [!NOTE]
      > Příkazy byly aktualizovány tak, aby odrážely nejnovější verzi [příkazového příkazu Azure .](https://docs.microsoft.com/cli/azure/install-azure-cli)     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat systémem přiřazenou spravovanou identitu na virtuálním počítači Azure pomocí azure cli.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Povolení spravované identity přiřazené systémem při vytváření virtuálního počítače Azure

K vytvoření virtuálního počítače Azure s povolenou spravovanou identitou přiřazenou systémem potřebuje váš účet přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet přidružený k předplatnému Azure, ve kterém chcete virtuální počítač nasadit:

   ```azurecli-interactive
   az login
   ```

2. Pomocí příkazu [az group create](/cli/azure/group/#az-group-create) vytvořte [skupinu prostředků](../../azure-resource-manager/management/overview.md#terminology) pro nasazení a uchování virtuálního počítače a souvisejících prostředků. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm/#az-vm-create). Následující příklad vytvoří virtuální ho s názvem *myVM* se systémem přiřazenou `--assign-identity` spravovanou identitou, jak požaduje parametr. Parametry `--admin-username` a `--admin-password` určují uživatelské jméno a heslo účtu správce pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty odpovídajícím způsobem pro vaše prostředí: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Povolení spravované identity přiřazené systémem na existujícím virtuálním počítači Azure

Chcete-li povolit systémem přiřazenou spravovanou identitu na virtuálním počítači, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, který obsahuje virtuální počítač.

   ```azurecli-interactive
   az login
   ```

2. Použijte [přiřazení identity virtuálního va az](/cli/azure/vm/identity/) s příkazem `identity assign` povolit systémově přiřazenou identitu existujícímu virtuálnímu virtuálnímu jevu:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Zakázání systémově přiřazené identity z virtuálního počítače Azure

Chcete-li zakázat systémem přiřazenou spravovanou identitu na virtuálním počítači, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

Pokud máte virtuální počítač, který už nepotřebuje systémově přiřazenou identitu, ale stále potřebuje uživatelem přiřazené identity, použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Pokud máte virtuální počítač, který už nepotřebuje identitu přiřazenou systémem a nemá žádné identity přiřazené uživateli, použijte následující příkaz:

> [!NOTE]
> Hodnota `none` rozlišuje malá a velká písmena. Musí to být malá písmena. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat uživatelem přiřazenou spravovanou identitu z virtuálního počítače Azure pomocí rozhraní příkazového příkazu Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Přiřazení spravované identity přiřazené uživateli při vytváření virtuálního počítače Azure

Chcete-li přiřadit uživatelem přiřazenou identitu virtuálnímu počítači během jeho vytváření, váš účet potřebuje přiřazení rolí [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [Operátor spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Tento krok můžete přeskočit, pokud již máte skupinu prostředků, kterou chcete použít. Vytvořte [skupinu prostředků](~/articles/azure-resource-manager/management/overview.md#terminology) pro uzavření a nasazení spravované identity přiřazené uživatelem pomocí [vytvoření skupiny az](/cli/azure/group/#az-group-create). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<LOCATION>` vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` určuje skupinu prostředků, ve které se spravovaná identita přiřazená uživatelem vytvoří, a parametr `-n` určuje její název.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Odpověď obsahuje podrobnosti o vytvořené spravované identitě přiřazené uživateli, podobně jako následující. Hodnota ID prostředku přiřazená uživatelem přiřazené spravované identitě se používá v následujícím kroku.

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

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm/#az-vm-create). Následující příklad vytvoří virtuální ho svirtuálním mandatorním uvedením přidruženéka k nové identitě přiřazené uživateli, jak je určeno parametrem. `--assign-identity` Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Přiřazení spravované identity přiřazené uživateli k existujícímu virtuálnímu počítači Azure

Chcete-li přiřadit uživatelem přiřazenou identitu k virtuálnímu počítači, váš účet potřebuje přiřazení rolí [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [Operátor spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

1. Vytvořte identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` určuje skupinu prostředků, kde je vytvořena identita `-n` přiřazená uživateli, a parametr určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

    > [!IMPORTANT]
    > Vytváření spravovaných identit přiřazených uživatelem se speciálními znaky (tj. podtržítkem) v názvu není aktuálně podporováno. Použijte alfanumerické znaky. Vraťte se sem a přečtěte si nové informace.  Další informace naleznete [v častých dotazech a známých problémech](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpověď obsahuje podrobnosti o vytvořené spravované identitě přiřazené uživateli, podobně jako následující. 

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

2. Přiřaďte uživatelem přiřazenou identitu k virtuálnímu počítači pomocí [přiřazení identity vz vm](/cli/azure/vm). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. Jedná `<USER ASSIGNED IDENTITY NAME>` se o vlastnost prostředku `name` spravované identity přiřazenou uživatelem, která byla vytvořena v předchozím kroku:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání spravované identity přiřazené uživateli z virtuálního počítače Azure

Chcete-li odebrat uživatelem přiřazenou identitu virtuálnímu počítači, váš účet potřebuje přiřazení role [Přispěvatel virtuálního počítače.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) 

Pokud se jedná o jedinou uživatelem přiřazenou `UserAssigned` spravovanou identitu přiřazenou virtuálnímu počítači, bude odebrána z hodnoty typu identity.  Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. Bude `<USER ASSIGNED IDENTITY>` to `name` vlastnost identity přiřazené uživateli, která se nachází v části identity virtuálního počítače pomocí `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Pokud váš virtuální počítač nemá spravanou identitu přiřazenou systémem a chcete z něj odebrat všechny identity přiřazené uživateli, použijte následující příkaz:

> [!NOTE]
> Hodnota `none` rozlišuje malá a velká písmena. Musí to být malá písmena.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Pokud má váš virtuální počítač přiřazené i uživatelem přiřazené identity, můžete odebrat všechny uživatelem přiřazené identity přepnutím na použití pouze systémově přiřazené. Použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Další kroky
- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Pro úplné vytvoření virtuálního počítače Azure Rychlé starty, najdete v tématu: 
  - [Vytvoření virtuálního počítače s Windows pomocí cli](../../virtual-machines/windows/quick-create-cli.md)  
  - [Vytvoření virtuálního počítače s Linuxem pomocí cli](../../virtual-machines/linux/quick-create-cli.md) 

















