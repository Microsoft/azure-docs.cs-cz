---
title: Postup konfigurace systému a uživatelsky přiřazené identity spravované na Virtuálním počítači Azure pomocí Azure CLI
description: Projděte pokyny ke konfiguraci systému a uživatelsky přiřazené identity spravované na Virtuálním počítači Azure pomocí Azure CLI.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: eb30fc16145bb8fedba4760bbab4ef0ab0800bc9
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027971"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku pomocí rozhraní příkazového řádku Azure, se dozvíte, jak k provádění následujících spravovaných identit pro prostředky Azure operace na Virtuálním počítači Azure:

- Povolení a zákaz systém přiřadil spravovaná identita na Virtuálním počítači Azure
- Přidání a odebrání uživatelsky přiřazené spravovanou identitu na Virtuálním počítači Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) k vytvoření virtuálního počítače a povolit a odeberte systém a/nebo uživatelsky přiřazené identity spravované z virtuálního počítače Azure.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření uživatel přiřazenou se identita spravované.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat uživatel přiřazenou se identita spravované od a do virtuálního počítače.
- Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:
    - Použití [Azure Cloud Shell](../../cloud-shell/overview.md) z portálu Azure portal (viz další část).
    - Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
    - [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Pokud byste radši chtěli použít místní konzoly příkazového řádku.
      
      > [!NOTE]
      > Příkazy byly aktualizovány tak, aby odrážely nejnovější verzi [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).     
        

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se dozvíte, jak můžete povolit nebo zakázat systém přiřadil spravovaná identita na Virtuálním počítači Azure pomocí Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Povolit systém přiřadil spravovanou identitu při vytváření virtuálního počítače Azure

Vytvoření virtuálního počítače Azure s systém přiřadil spravovaná identita povolen:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet přidružený k předplatnému Azure, ve kterém chcete virtuální počítač nasadit:

   ```azurecli-interactive
   az login
   ```

2. Pomocí příkazu [az group create](/cli/azure/group/#az-group-create) vytvořte [skupinu prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro nasazení a uchování virtuálního počítače a souvisejících prostředků. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm/#az-vm-create). Následující příklad vytvoří virtuální počítač s názvem *myVM* s systém přiřadil se identita spravované, jak to požadoval `--assign-identity` parametru. Parametry `--admin-username` a `--admin-password` určují uživatelské jméno a heslo účtu správce pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty odpovídajícím způsobem pro vaše prostředí: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Povolit systém přiřadil spravovaná identita na existujícím virtuálním počítači Azure

Pokud je potřeba povolit systém přiřadil spravovaná identita na existující virtuální počítač:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az-login). Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač.

   ```azurecli-interactive
   az login
   ```

2. Použití [az vm identity přiřadit](/cli/azure/vm/identity/#az-vm-identity-assign) s `identity assign` příkaz Povolit identitu systém přiřadil k existujícímu virtuálnímu počítači:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Zakázat identitu systém přiřadil z virtuálního počítače Azure

Pokud máte virtuální počítač se už nepotřebuje systém přiřadil identity, ale stále potřebuje uživatelsky přiřazené identity, použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Pokud máte virtuální počítač, který už je systém přiřadil identity a nemá žádné uživatelsky přiřazené identity, použijte následující příkaz:

> [!NOTE]
> Hodnota `none` je velká a malá písmena. Musí obsahovat malá písmena. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```

K odebrání spravované identity pro prostředky Azure rozšíření virtuálního počítače, uživatele `-n ManagedIdentityExtensionForWindows` nebo `-n ManagedIdentityExtensionForLinux` přepnout (v závislosti na typu virtuálních počítačů) s [odstranění rozšíření az vm](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-managed-identity"></a>Uživatel přiřazenou spravované identity

V této části se dozvíte, jak přidávat a odebírat uživatelsky přiřazené spravovanou identitu virtuálního počítače Azure pomocí Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Přiřadit uživateli přiřazena spravovanou identitu při vytváření virtuálního počítače Azure

Tato část vás provede vytvořením virtuálního počítače s přiřazením uživatelsky přiřazené identity spravované. Pokud už máte virtuální počítač, který chcete použít, tuto část přeskočit a pokračovat na další.

1. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit. Vytvoření [skupiny prostředků](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pro nasazení vaší uživatelsky přiřazené spravovanou identitu a členství ve skupině pomocí [az skupiny vytvořit](/cli/azure/group/#az-group-create). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<LOCATION>` vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvoření uživatelsky přiřazené identity spravované pomocí [vytvoření az identity](/cli/azure/identity#az-identity-create).  `-g` Parametr určuje skupinu prostředků, ve kterém se vytvoří spravované identity přiřazené uživateli, a `-n` parametr určuje její název.    
    
   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Odpověď obsahuje podrobnosti o uživateli přiřazena spravovanou identitu vytvořené, podobně jako následující. Hodnota id prostředku přiřazená uživateli přiřazena spravovaná identita se používá v následujícím kroku.

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

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm/#az-vm-create). Následující příklad vytvoří virtuální počítač přidružený k nové uživatelsky přiřazené identity, jak jsou určené `--assign-identity` parametru. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Spravované identity přiřazené uživateli přiřadit existující virtuální počítač Azure

1. Vytvořte identitu přiřazenou uživatelem pomocí příkazu [az identity create](/cli/azure/identity#az-identity-create).  `-g` Parametr určuje skupinu prostředků, ve kterém je vytvoření uživatelsky přiřazené identity a `-n` parametr určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

    > [!IMPORTANT]
    > Vytvoření uživatelsky přiřazené identity spravované pomocí speciální znaky (třeba podtržítko) v názvu se momentálně nepodporuje. Použijte prosím alfanumerické znaky. Vraťte se sem a přečtěte si nové informace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   Odpověď obsahuje podrobnosti o uživateli přiřazena spravovanou identitu vytvořené, podobně jako následující. 

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

2. Přiřazení k virtuálnímu počítači pomocí uživatelsky přiřazené identity [az vm identity přiřadit](/cli/azure/vm#az-vm-identity-assign). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY NAME>` Uživatelsky přiřazené spravované identity prostředku je `name` vlastnost, protože vytvořili v předchozím kroku:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure spravované identity přiřazené uživateli

Odebrání virtuálního počítače pomocí spravované identity přiřazené uživateli [az identita virtuálního počítače odeberte](/cli/azure/vm#az-vm-identity-remove). Pokud je to jediná uživatelsky přiřazené identity přiřazené k virtuálnímu počítači, spravované `UserAssigned` se odebere z hodnoty typu identity.  Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. `<USER ASSIGNED IDENTITY>` Bude uživatelsky přiřazené identity `name` vlastnosti, které najdete v části Identita virtuálního počítače pomocí `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Pokud váš virtuální počítač nemá systém přiřadil spravovanou identitu a z něj odebrat všechny uživatelsky přiřazené identity, použijte následující příkaz:

> [!NOTE]
> Hodnota `none` je velká a malá písmena. Musí obsahovat malá písmena.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Pokud má virtuální počítač i systém přiřadil a uživatelsky přiřazené identity, můžete odebrat všechny uživatelsky přiřazené identity přepnutím na používání jenom systém přiřadil. Použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Další postup
- [Spravované identity pro prostředky Azure – přehled](overview.md)
- Úplné vytvoření virtuálního počítače Azure rychlých startů naleznete v tématu: 
  - [Vytvoření virtuálního počítače Windows pomocí rozhraní příkazového řádku](../../virtual-machines/windows/quick-create-cli.md)  
  - [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku](../../virtual-machines/linux/quick-create-cli.md) 

















