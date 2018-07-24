---
title: Přiřadit konfigurace systémových a uživatelských identit na Virtuálním počítači Azure pomocí Azure CLI
description: Podrobné pokyny ke konfiguraci systémových a uživatelských identit přiřazených na virtuálním počítači Azure pomocí Azure CLI.
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
ms.openlocfilehash: 5d91e543f370d65337c0298fb6421d5ffa85eda0
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216219"
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Konfigurace Identity spravované služby (MSI) na Virtuálním počítači Azure pomocí Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující operace Identity spravované služby na virtuálním počítači Azure pomocí Azure CLI:
- Povolit nebo zakázat identitu na Virtuálním počítači Azure přiřazenou systémem
- Přidávat a odebírat uživatele přiřazeny identitu na Virtuálním počítači Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) k vytvoření virtuálního počítače a povolit a odeberte systém a/nebo uživatel spravované identity přiřazené z virtuálního počítače Azure.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role pro vytvoření identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat identity přiřazené uživateli, ze kterých a do virtuálního počítače.
- Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:
    - Použití [Azure Cloud Shell](../../cloud-shell/overview.md) z portálu Azure portal (viz další část).
    - Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
    - [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly příkazového řádku. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem

V této části se dozvíte, jak můžete povolit nebo zakázat identitu na Virtuálním počítači Azure pomocí Azure CLI přiřazenou systémem.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Povolit systém identity přiřazené během vytváření virtuálního počítače Azure

Vytvoření virtuálního počítače Azure s přiřazenou identity povoleno systémem:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az_login). Použijte účet přidružený k předplatnému Azure, ve kterém chcete virtuální počítač nasadit:

   ```azurecli-interactive
   az login
   ```

2. Pomocí příkazu [az group create](/cli/azure/group/#az_group_create) vytvořte [skupinu prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro nasazení a uchování virtuálního počítače a souvisejících prostředků. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm/#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVM* s identitou přiřazenou systémem, jak to požadoval `--assign-identity` parametru. Parametry `--admin-username` a `--admin-password` určují uživatelské jméno a heslo účtu správce pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty odpovídajícím způsobem pro vaše prostředí: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Povolit v existujícím virtuálním počítači Azure identitu přiřazenou systémem

Pokud je potřeba povolit identitu přiřazenou systémem na existující virtuální počítač:

1. Pokud používáte Azure CLI v místní konzole, nejprve se přihlaste k Azure pomocí příkazu [az login](/cli/azure/reference-index#az_login). Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač.

   ```azurecli-interactive
   az login
   ```

2. Použití [az vm identity přiřadit](/cli/azure/vm/identity/#az_vm_identity_assign) s `identity assign` příkaz Povolit identitu přiřazenou systémem do existujícího virtuálního počítače:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Zakázat systému identity přiřazené z virtuálního počítače Azure

> [!NOTE]
> Zakáže identita spravované služby z virtuálního počítače není aktuálně podporováno. Do té doby můžete přepínat mezi použitím systém přiřadil a přiřazené identity uživatele.

Pokud máte virtuální počítač se už nepotřebuje identitu přiřazenou systémem, ale stále potřebuje identity přiřazené uživateli, použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Odebrat rozšíření MSI virtuálního počítače, uživatele `-n ManagedIdentityExtensionForWindows` nebo `-n ManagedIdentityExtensionForLinux` přepnout (v závislosti na typu virtuálních počítačů) s [odstranění rozšíření az vm](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Identita uživatele přiřazeno

V této části se dozvíte, jak přidávat a odebírat uživatele identity přiřazené z virtuálního počítače Azure pomocí Azure CLI.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Přiřazování identity přiřazené během vytváření virtuálního počítače Azure uživateli

Tato část vás provede vytvořením virtuálního počítače s přiřazením identity přiřazené uživateli. Pokud už máte virtuální počítač, který chcete použít, tuto část přeskočit a pokračovat na další.

1. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit. Vytvoření [skupiny prostředků](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pro nasazení MSI a členství ve skupině pomocí [az skupiny vytvořit](/cli/azure/group/#az_group_create). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<LOCATION>` vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvoření uživatele přiřadit pomocí identity [vytvoření az identity](/cli/azure/identity#az_identity_create).  `-g` Parametr určuje skupinu prostředků, ve kterém se vytvoří identity přiřazené uživateli, a `-n` parametr určuje její název.    
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g myResourceGroup -n myUserAssignedIdentity
```
Odpověď obsahuje podrobnosti o identity přiřazené uživateli vytvořené, podobně jako následující. Hodnota id prostředku přiřazená identity přiřazené uživateli se používá v následujícím kroku.

```json
{
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
}
```

3. Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm/#az_vm_create). Následující příklad vytvoří virtuální počítač přidružený k nové identity přiřazené uživateli, jak jsou určené `--assign-identity` parametru. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` a `<MSI ID>` vlastními hodnotami. Pro `<MSI ID>`, používat identity přiřazené uživateli prostředek `id` vlastnost vytvořenou v předchozím kroku: 

```azurecli-interactive 
az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Přiřazování identity přiřazené do stávajícího virtuálního počítače Azure uživateli

1. Vytvoření uživatele přiřadit pomocí identity [vytvoření az identity](/cli/azure/identity#az-identity-create).  `-g` Parametr určuje skupinu prostředků, ve kterém se vytvoří identity přiřazené uživateli, a `-n` parametr určuje její název. Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<MSI NAME>` vlastními hodnotami:

    > [!IMPORTANT]
    > Vytváří se uživatel přiřazené identity se speciálními znaky (třeba podtržítko) v názvu se momentálně nepodporuje. Použijte prosím alfanumerické znaky. Vraťte se sem a přečtěte si nové informace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Odpověď obsahuje podrobnosti o uživateli přiřadit MSI vytvořené, podobně jako následující. Prostředek `id` hodnota přiřazená k identity přiřazené uživateli se používá v následujícím kroku.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Přiřazení k virtuálnímu počítači pomocí identity přiřazené uživateli [az vm identity přiřadit](/cli/azure/vm#az-vm-identity-assign). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. `<MSI ID>` Bude prostředek identity přiřazené uživateli `id` vlastnost, protože vytvořili v předchozím kroku:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Odebrání uživatele identity přiřazené z virtuálního počítače Azure

> [!NOTE]
> Odebrání všech uživatelsky přiřazených identit z virtuálního počítače se aktuálně nepodporuje, pokud máte identitu přiřazenou systémem.

Pokud se váš virtuální počítač má více uživatelsky přiřazených identit, můžete odebrat všechny kromě poslední pomocí [az identita virtuálního počítače odeberte](/cli/azure/vm#az-vm-identity-remove). Nezapomeňte nahradit hodnoty parametrů `<RESOURCE GROUP>` a `<VM NAME>` vlastními hodnotami. `<MSI NAME>` Bude identity přiřazené uživateli `name` vlastnosti, které najdete v části Identita virtuálního počítače pomocí `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Pokud váš virtuální počítač má přiřazenou systémem a identity přiřazené uživateli, můžete odebrat všechny uživatele identit přiřazených přepnutím používat pouze přiřazenou systémem. Použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Související obsah
- [Přehled spravované Identity služby](overview.md)
- Úplné vytvoření virtuálního počítače Azure rychlých startů naleznete v tématu: 
  - [Vytvoření virtuálního počítače Windows pomocí rozhraní příkazového řádku](../../virtual-machines/windows/quick-create-cli.md)  
  - [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku](../../virtual-machines/linux/quick-create-cli.md) 

















