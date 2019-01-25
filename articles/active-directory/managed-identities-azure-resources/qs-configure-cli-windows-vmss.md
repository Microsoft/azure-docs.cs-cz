---
title: Postup konfigurace systému a uživatelsky přiřazené identity spravované v VMSS Azure pomocí Azure CLI
description: Projděte pokyny ke konfiguraci systému a uživatelsky přiřazené identity spravované v VMSS Azure pomocí Azure CLI.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: priyamo
ms.openlocfilehash: a7ec171bc2722446b736afc9a84b37d7022684e9
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886842"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuální počítač škálovací sady s použitím rozhraní příkazového řádku Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak k provádění následujících spravovaných identit pro operace prostředků Azure na Azure virtuální počítač Škálovací nastavit (VMSS), pomocí rozhraní příkazového řádku Azure:
- Povolení a zákaz systém přiřadil spravovaná identita na Azure VMSS
- Přidání a odebrání uživatelsky přiřazené spravovanou identitu na Azure VMSS


## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení řízení přístupu na základě rolí Azure:

    > [!NOTE]
    > Žádné další Azure vyžaduje přiřazení rolí adresáře AD.

    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) vytvořit škálovací sadu virtuálních počítačů a povolit a odeberte systém a/nebo uživatelsky přiřazené identity spravované ze škálovací sady virtuálních počítačů.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření uživatel přiřazenou se identita spravované.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat uživatel přiřazenou se identita spravované od a do škálovací sady virtuálních počítačů.
- Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:
    - Použití [Azure Cloud Shell](../../cloud-shell/overview.md) z portálu Azure portal (viz další část).
    - Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
    - [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly příkazového řádku. 
      
      > [!NOTE]
      > Příkazy byly aktualizovány tak, aby odrážely nejnovější verzi [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se dozvíte, jak můžete povolit nebo zakázat systém přiřadil spravovanou identitu pro VMSS Azure pomocí Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Povolit systém přiřadil spravovanou identitu při vytváření škálovací sady virtuálních počítačů Azure

Vytvoření virtuálního počítače škálovací sady s systém přiřadil spravovaná identita povolen:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, ve které chcete nasadit škálovací sadu virtuálních počítačů:

   ```azurecli-interactive
   az login
   ```

2. Vytvoření [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení škálovací sady virtuálních počítačů a její související prostředky, pomocí [vytvořit skupiny az](/cli/azure/group/#az-group-create). Pokud už máte skupinu prostředků, kterou chcete použít místo toho můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvoření virtuálního počítače škálovací sady s použitím [az vmss vytvořit](/cli/azure/vmss/#az-vmss-create) . Následující příklad vytvoří škálovací sadu virtuálních počítačů *myVMSS* s systém přiřadil se identita spravované, jak to požadoval `--assign-identity` parametru. Parametry `--admin-username` a `--admin-password` určují uživatelské jméno a heslo účtu správce pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty odpovídajícím způsobem pro vaše prostředí: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolit systém přiřadil spravované identity v existující škálovací sady virtuálních počítačů Azure

Pokud je potřeba povolit systém přiřadil spravovaná identita v existující škálovací sady virtuálních počítačů Azure:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, který obsahuje škálovací sadu virtuálních počítačů.

   ```azurecli-interactive
   az login
   ```

2. Použití [az vmss identity přiřadit](/cli/azure/vmss/identity/#az-vmss-identity-assign) příkaz, který umožní systém přiřadil spravované identity existujícího virtuálního počítače:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat spravovanou identitu systém přiřadil z škálovací sady virtuálních počítačů Azure

Pokud máte škálovací sadu virtuálních počítačů, která už nepotřebuje systém přiřadil spravovanou identitu, ale stále potřebuje uživatelsky přiřazené identity spravované, použijte následující příkaz:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Pokud máte virtuální počítač, který už je systém přiřadil spravovanou identitu a nemá žádné uživatelsky přiřazené spravované identity, použijte následující příkaz:

> [!NOTE]
> Hodnota `none` je velká a malá písmena. Musí obsahovat malá písmena. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

Chcete-li odebrat spravovaných identit pro rozšíření virtuálního počítače prostředky Azure (plánovaná k převedení na zastaralého v lednu 2019), použijte [az vmss identity odebrat](/cli/azure/vmss/identity/#az-vmss-remove-identity) příkazu odeberte systém přiřadil spravovaných identit VMSS:

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-managed-identity"></a>Uživatel přiřazenou spravované identity

V této části se dozvíte, jak povolit a odeberte uživatelsky přiřazené spravovanou identitu pomocí Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřadit uživateli přiřazena spravovanou identitu při vytváření škálovací sady virtuálních počítačů

Tato část vás provede vytváření VMSS a přiřazení uživatelsky přiřazené identity spravované VMSS. Pokud už máte VMSS, kterou chcete použít, tuto část přeskočit a pokračovat na další.

1. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit. Vytvoření [skupiny prostředků](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pro nasazení vaší uživatelsky přiřazené spravovanou identitu a členství ve skupině pomocí [az skupiny vytvořit](/cli/azure/group/#az-group-create). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<LOCATION>` vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` určuje skupinu prostředků, ve které se spravovaná identita přiřazená uživatelem vytvoří, a parametr `-n` určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Odpověď obsahuje podrobnosti o uživateli přiřazena spravovanou identitu vytvořené, podobně jako následující. Prostředek `id` hodnota přiřazená k uživateli přiřazena spravovaná identita se používá v následujícím kroku.

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

3. Vytvoření pomocí VMSS [az vmss vytvořit](/cli/azure/vmss/#az-vmss-create). Následující příklad vytvoří VMSS přidružené k nové uživatelsky přiřazené spravované identity, jak jsou určené `--assign-identity` parametru. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` a `<USER ASSIGNED IDENTITY>` vlastními hodnotami. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Spravované identity přiřazené uživateli přiřadit existující škálovací sady virtuálních počítačů

1. Vytvořte spravovanou identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  Parametr `-g` určuje skupinu prostředků, ve které se spravovaná identita přiřazená uživatelem vytvoří, a parametr `-n` určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

    > [!IMPORTANT]
    > Vytvoření uživatelsky přiřazené identity spravované pomocí speciální znaky (třeba podtržítko) v názvu se momentálně nepodporuje. Použijte prosím alfanumerické znaky. Vraťte se sem a přečtěte si nové informace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Odpověď obsahuje podrobnosti o uživateli přiřazena spravovanou identitu vytvořené, podobně jako následující.

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

2. Přiřadit uživateli přiřazena spravovaná identita na VMSS pomocí [az vmss identity přiřadit](/cli/azure/vmss/identity#az-vm-assign-identity). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VMSS NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY>` Uživatelsky přiřazené identity prostředku je `name` vlastnost, protože vytvořili v předchozím kroku:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Odeberte uživatelsky přiřazené spravovanou identitu ze škálovací sady virtuálních počítačů Azure

K odebrání spravovanou identitu uživatele přiřadit k využívání sady škálování virtuálního počítače [az vmss identity odebrat](/cli/azure/vmss/identity#az-vmss-identity-remove). Pokud je to jediná uživatelsky přiřazené identity přiřazené do škálovací sady virtuálních počítačů spravované `UserAssigned` se odebere z hodnoty typu identity.  Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VMSS NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY>` Bude uživatel přiřazenou spravované identity `name` vlastnosti, které najdete v části Identita škálovací sady virtuálních počítačů `az vmss identity show`:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
```

Pokud vaše škálovací sada virtuálních počítačů nemá systém přiřadil spravovat identity a chcete z něj odebrat všechny přiřazené uživatele spravované identity, použijte následující příkaz:

> [!NOTE]
> Hodnota `none` je velká a malá písmena. Musí obsahovat malá písmena.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Pokud vaše škálovací sada virtuálních počítačů jsou obě systém přiřadil a uživatelsky přiřazené identity spravované můžete odebrat všechny uživatelsky přiřazené identity přepnutím používat jen systém přiřadil spravovaná identita. Použijte následující příkaz:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Další postup

- [Spravované identity pro prostředky Azure – přehled](overview.md)
- Pro úplnou Azure škálovací sady virtuálních počítačů vytváření rychlý start, naleznete v tématu: 

  - [Vytvoření Škálovací sady virtuálních počítačů pomocí rozhraní příkazového řádku](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















