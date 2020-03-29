---
title: Konfigurace spravovaných identit ve škálovací sadě virtuálních strojů – Azure CLI – Azure AD
description: Krok za krokem pokyny pro konfiguraci systémových a uživatelem přiřazených spravovaných identit na škálovací sadě virtuálních strojů Azure pomocí rozhraní příkazového příkazového příkazu Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: MarkusVi
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
ms.openlocfilehash: 2832a8c584c0fbe707f22501809d772c6ffb970b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430093"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurace spravovaných identit pro prostředky Azure na škálovací sadě virtuálních strojů pomocí rozhraní příkazového příkazu Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující spravované identity pro operace prostředků Azure na škálovací sadě virtuálních strojů Azure pomocí azure cli:
- Povolení a zakázání spravované identity přiřazené systému na škálovací sadě virtuálních strojů Azure
- Přidání a odebrání spravované identity přiřazené uživateli ve škálovací sadě virtuálních strojů Azure


## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- K provedení operací správy v tomto článku váš účet potřebuje následující přiřazení řízení přístupu azure na základě rolí:

    > [!NOTE]
    > Nejsou vyžadována žádná další přiřazení rolí adresáře Azure AD.

    - [Přispěvatel virtuálního počítače](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pro vytvoření škálovací sady virtuálních strojů a povolení a odebrání spravované identity systému nebo uživatelem přiřazené ze škálovací sady virtuálních strojů.
    - [Role spravovaného přispěvatele identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) k vytvoření spravované identity přiřazené uživateli.
    - Role [spravovaného operátora identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pro přiřazení a odebrání spravované identity přiřazené uživateli z škálovací sady virtuálních strojů a do ní.
- Chcete-li spustit příklady skriptu vykreslování jízdou po nesek, máte tři možnosti:
    - Azure [Cloud Shell](../../cloud-shell/overview.md) použijte z webu Azure Portal (viz další část).
    - Pomocí vloženého prostředí Azure Cloud Shell použijte pomocí tlačítka "Try It", které se nachází v pravém horním rohu každého bloku kódu.
    - [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější), pokud dáváte přednost použití místní konzoly příkazového příkazu. 
      
      > [!NOTE]
      > Příkazy byly aktualizovány tak, aby odrážely nejnovější verzi [příkazového příkazu Azure .](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat systémem přiřazenou spravovanou identitu pro škálovací sadu virtuálních strojů Azure pomocí azure cli.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Povolení spravované identity přiřazené systémem při vytváření škálovací sady virtuálních strojů Azure

Vytvoření škálovací sady virtuálních strojů s povolenou spravovanou identitou přiřazenou systémem:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, pod kterým chcete nasadit škálovací sadu virtuálních strojů:

   ```azurecli-interactive
   az login
   ```

2. Vytvořte [skupinu prostředků](../../azure-resource-manager/management/overview.md#terminology) pro uzavření a nasazení škálovací sady virtuálních strojů a souvisejících prostředků pomocí [vytvoření skupiny az](/cli/azure/group/#az-group-create). Tento krok můžete přeskočit, pokud již máte skupinu prostředků, kterou chcete použít:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvořte škálovací sadu virtuálních strojů pomocí [vytvoření az vmss](/cli/azure/vmss/#az-vmss-create) . Následující příklad vytvoří škálovací sadu virtuálních strojů s názvem *myVMSS* se `--assign-identity` spravovanou identitou přiřazenou systémem, jak požaduje parametr. Parametry `--admin-username` a `--admin-password` určují uživatelské jméno a heslo účtu správce pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty odpovídajícím způsobem pro vaše prostředí: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolení spravované identity přiřazené systémem na existující škálovací sadě virtuálních strojů Azure

Pokud potřebujete povolit systémem přiřazenou spravovanou identitu na existující škálovací sadě virtuálních strojů Azure:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních strojů.

   ```azurecli-interactive
   az login
   ```

2. Pomocí [příkazu přiřazení identity vavs az](/cli/azure/vmss/identity/#az-vmss-identity-assign) povolit spravanou identitu přiřazenou systémem existujícímu virtuálnímu virtuálnímu mandu:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázání spravované identity přiřazené systémem ze škálovací sady virtuálních strojů Azure

Pokud máte škálovací sadu virtuálních strojů, která už nepotřebuje spravovanou identitu přiřazenou systémem, ale stále potřebuje spravované identity přiřazené uživatelem, použijte následující příkaz:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Pokud máte virtuální počítač, který už nepotřebuje spravovanou identitu přiřazenou systémem a nemá žádné spravované identity přiřazené uživateli, použijte následující příkaz:

> [!NOTE]
> Hodnota `none` rozlišuje malá a velká písmena. Musí to být malá písmena. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```



## <a name="user-assigned-managed-identity"></a>uživatelem přiřazená spravovaná identita

V této části se dozvíte, jak povolit a odebrat uživatelem přiřazenou spravovanou identitu pomocí rozhraní příkazového příkazu Azure.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživateli při vytváření škálovací sady virtuálních strojů

Tato část vás provede vytvořením škálovací sady virtuálních strojů a přiřazením spravované identity přiřazené uživateli k škálovací sadě virtuálních strojů. Pokud už máte sadu škálovacího stupnice virtuálního počítače, kterou chcete použít, přeskočte tuto část a pokračujte na další.

1. Tento krok můžete přeskočit, pokud již máte skupinu prostředků, kterou chcete použít. Vytvořte [skupinu prostředků](~/articles/azure-resource-manager/management/overview.md#terminology) pro uzavření a nasazení spravované identity přiřazené uživatelem pomocí [vytvoření skupiny az](/cli/azure/group/#az-group-create). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<LOCATION>` vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` určuje skupinu prostředků, ve které se spravovaná identita přiřazená uživatelem vytvoří, a parametr `-n` určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Odpověď obsahuje podrobnosti o vytvořené spravované identitě přiřazené uživateli, podobně jako následující. Hodnota `id` prostředku přiřazená uživatelem přiřazené spravované identitě se používá v následujícím kroku.

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

3. Vytvořte škálovací sadu virtuálních strojů pomocí [vytvoření az vmss](/cli/azure/vmss/#az-vmss-create). Následující příklad vytvoří škálovací sadu virtuálního počítače přidruženou k nové spravované `--assign-identity` identitě přiřazené uživateli, jak je určeno parametrem. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` a `<USER ASSIGNED IDENTITY>` vlastními hodnotami. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživateli k existující škálovací sadě virtuálních strojů

1. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` určuje skupinu prostředků, ve které se spravovaná identita přiřazená uživatelem vytvoří, a parametr `-n` určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpověď obsahuje podrobnosti o vytvořené spravované identitě přiřazené uživateli, podobně jako následující.

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

2. Přiřaďte uživatelem přiřazenou spravovanou identitu k škálovací sadě virtuálních strojů pomocí [přiřazení identity az vmss](/cli/azure/vmss/identity). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VIRTUAL MACHINE SCALE SET NAME>` vlastními hodnotami. Jedná `<USER ASSIGNED IDENTITY>` se o vlastnost prostředku `name` přiřazenou uživatelem, která byla vytvořena v předchozím kroku:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Odebrání spravované identity přiřazené uživateli ze škálovací sady virtuálních strojů Azure

Chcete-li odebrat uživatelem přiřazenou spravovanou identitu ze škálovací sady virtuálních strojů, použijte [odebrat identitu AZ VMSS](/cli/azure/vmss/identity#az-vmss-identity-remove). Pokud se jedná o jedinou uživatelem přiřazenou spravovanou identitu přiřazenou škálovací sadě virtuálního počítače, `UserAssigned` bude odebrána z hodnoty typu identity.  Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VIRTUAL MACHINE SCALE SET NAME>` vlastními hodnotami. Bude `<USER ASSIGNED IDENTITY>` to `name` vlastnost spravované identity přiřazené uživateli, která se nachází v části identity `az vmss identity show`škálovací sady virtuálních strojů pomocí :

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Pokud škálovací sada virtuálních strojů nemá spravovanou identitu přiřazenou systémem a chcete z ní odebrat všechny spravované identity přiřazené uživatelem, použijte následující příkaz:

> [!NOTE]
> Hodnota `none` rozlišuje malá a velká písmena. Musí to být malá písmena.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Pokud má škálovací sada virtuálního počítače spravované identity přiřazené systémem i uživatelem, můžete odebrat všechny identity přiřazené uživateli tak, že přepnete na použití pouze spravované identity přiřazené k systému. Použijte následující příkaz:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Další kroky

- [Přehled spravovaných identit pro prostředky Azure](overview.md)
- Vytvoření celé škálovací sady virtuálních strojů Azure najdete v tématu: 

  - [Vytvoření škálovací sady virtuálních strojů pomocí cli](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















