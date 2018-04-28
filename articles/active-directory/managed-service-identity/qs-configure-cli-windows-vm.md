---
title: Postup konfigurace systému a uživatele přiřazené identit na virtuální počítač Azure pomocí rozhraní příkazového řádku Azure
description: Podrobné pokyny ke konfiguraci systému a uživatele přiřazené identit na virtuální počítač Azure, pomocí rozhraní příkazového řádku Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 0f167fef2303ff3b552b9924db3eae9a07c1cb69
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-managed-service-identity-msi-on-an-azure-vm-using-azure-cli"></a>Konfigurace Identity spravované služby (MSI) na virtuální počítač Azure pomocí rozhraní příkazového řádku Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identita spravované služby poskytuje Azure služby automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku se naučíte, jak provádět následující operace identita spravované služby ve virtuálním počítači Azure, pomocí rozhraní příkazového řádku Azure:
- Povolení a zákaz systému přiřazené identita na virtuální počítač Azure
- Přidání a odebrání uživatele přiřazené identita na virtuální počítač Azure

## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systémem přiřazený a uživatel s přiřazenou identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.
- Pokud chcete spustit skript příklady rozhraní příkazového řádku, máte tři možnosti:

    - Použití [prostředí cloudu Azure](../../cloud-shell/overview.md) z portálu Azure (viz další část).
    - Používání embedded prostředí cloudu Azure prostřednictvím "Zkuste ho" tlačítko, umístěné v pravém horním rohu každé blok kódu.
    - [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Systém přiřazené identity

V této části se dozvíte, jak povolení a zakázání systému přiřazené identita na virtuální počítač Azure pomocí rozhraní příkazového řádku Azure.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Povolit systém přiřazené identity během vytváření virtuální počítač Azure

Chcete-li vytvořit virtuální počítač Azure se systémem přiřadit identity povoleno:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Používáte účet, který je přidružen k předplatnému Azure, pod kterou chcete nasadit virtuální počítač:

   ```azurecli-interactive
   az login
   ```

2. Vytvoření [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení virtuálního počítače a jeho souvisejících prostředků pomocí [vytvořit skupinu az](/cli/azure/group/#az_group_create). Pokud už máte skupinu prostředků, které chcete použít místo toho můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvoření virtuálního počítače pomocí [vytvořit virtuální počítač az](/cli/azure/vm/#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *Můjvp* s identitou systému, které jsou přiřazeny, jak vyžádala `--assign-identity` parametr. `--admin-username` a `--admin-password` parametry zadejte účet správce jméno a heslo pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty jako vhodné pro vaše prostředí: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Povolit systém přiřazené identita na existující virtuální počítač Azure

Pokud potřebujete povolit identitě systému přiřazené na existující virtuální počítač:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Použijte účet, který je přidružený k předplatnému Azure, která obsahuje virtuální počítač. Dále zkontrolujte, zda váš účet patří do role, která vám dává oprávnění k zápisu do virtuálního počítače, jako je například "Přispěvatel virtuálních počítačů":

   ```azurecli-interactive
   az login
   ```

2. Použití [identita virtuálního počítače az přiřadit](/cli/azure/vm/identity/#az_vm_identity_assign) s `identity assign` identitě systému přiřazené k existující virtuální počítač povolit příkazem:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Zakázat systému přiřazené identity z virtuálního počítače Azure

> [!NOTE]
> Zakázání spravované identita služby z virtuálního počítače není aktuálně podporováno. Do té doby můžete přepnout mezi použitím přiřazené systému a přiřadit identity uživatelů.

Pokud máte virtuální počítač, který už nepotřebuje systému přiřazené identity, ale stále potřebuje přiřazeného identity uživatele, použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```
Chcete-li odebrat rozšíření virtuálního počítače MSI, uživatel `-n ManagedIdentityExtensionForWindows` nebo `-n ManagedIdentityExtensionForLinux` přepínač (v závislosti na typu virtuálních počítačů) s [odstranit rozšíření virtuálního počítače az](https://docs.microsoft.com/cli/azure/vm/#assign-identity):

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Identita uživatele přiřazeno

V této části se dozvíte, jak přidávat a odebírat uživatele přiřazené identity z virtuálního počítače Azure, pomocí rozhraní příkazového řádku Azure.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Přiřazení uživatele přiřazené identity při vytváření virtuálního počítače Azure

Tato část vás provede procesem vytvoření virtuálního počítače s přiřazením přiřazené identity uživatele. Pokud již máte virtuální počítač, který chcete použít, tuto část přeskočit a pokračovat na další.

1. Pokud už máte skupinu prostředků, kterou jste chtěli použít, můžete tento krok přeskočit. Vytvoření [skupiny prostředků](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pro nasazení MSI a členství ve skupině pomocí [vytvořit skupinu az](/cli/azure/group/#az_group_create). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<LOCATION>` hodnoty parametrů s vlastními hodnotami. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Vytvoření uživatele přiřazená pomocí identity [vytvoření az identity](/cli/azure/identity#az_identity_create).  `-g` Parametr určuje skupinu prostředků, kde se má vytvořit uživatel s přiřazenou identitu, a `-n` parametr určuje její název.
    
    > [!IMPORTANT]
    > Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu není aktuálně podporován. Použijte alfanumerické znaky. Vraťte se zpět pro aktualizace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```azurecli-interactive
    az identity create -g myResourceGroup -n myUserAssignedIdentity
    ```
Odpověď obsahuje podrobnosti pro identitu uživatele přiřazené vytvořili, podobný následujícímu. Hodnota id prostředku, který je přiřazen k uživateli přiřadit identity se používá v dalším kroku.

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

3. Vytvoření virtuálního počítače pomocí [vytvořit virtuální počítač az](/cli/azure/vm/#az_vm_create). Následující příklad vytvoří virtuální počítač spojené s novou identitu uživatele, které jsou přiřazené podle specifikace `--assign-identity` parametr. Nezapomeňte nahradit `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>`, a `<MSI ID>` hodnoty parametrů s vlastními hodnotami. Pro `<MSI ID>`, použijte identitu uživatele přiřazené prostředků `id` vlastnost vytvořili v předchozím kroku: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Přiřazení uživatele přiřazené identity existující virtuální počítač Azure

1. Vytvoření uživatele přiřazená pomocí identity [vytvoření az identity](/cli/azure/identity#az-identity-create).  `-g` Parametr určuje skupinu prostředků, kde se má vytvořit uživatel s přiřazenou identitu, a `-n` parametr určuje její název. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<MSI NAME>` hodnoty parametrů s vlastními hodnotami:

    > [!IMPORTANT]
    > Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu není aktuálně podporován. Použijte alfanumerické znaky. Vraťte se zpět pro aktualizace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Odpověď obsahuje podrobnosti o uživatel s přiřazenou MSI vytvořili, podobný následujícímu. Prostředek `id` hodnotu přiřazenou k identitě uživatele přiřazené se používá v dalším kroku.

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

2. Přiřaďte identitu uživatele přiřazené pro virtuální počítač pomocí [identita virtuálního počítače az přiřadit](/cli/azure/vm#az-vm-identity-assign). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. `<MSI ID>` Bude uživatel s přiřazenou identity prostředek `id` vlastnost, protože vytvořili v předchozím kroku:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Odebrání uživatele přiřazené identity z virtuálního počítače Azure

> [!NOTE]
> Odebrání všech identit uživatelů přiřazené z virtuálního počítače není aktuálně podporováno, pokud používáte systém přiřazené identity.

Pokud virtuální počítač má více identit přiřazeného uživatele, můžete odebrat všechny uzly s výjimkou na posledním místě pomocí [identita az virtuálního počítače odeberte](/cli/azure/vm#az-vm-identity-remove). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. `<MSI NAME>` Bude uživatel s přiřazenou identity `name` vlastnosti, které najdete v části Identita virtuální počítač pomocí `az vm show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```
Pokud virtuální počítač má systém přiřadit i přiřazeného identity uživatele, můžete odebrat všechny uživatelské přiřadila přepínání identity používat pouze systém přiřazen. Použijte následující příkaz:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Související obsah
- [Přehled spravované identita služby](overview.md)
- Úplné vytvoření virtuálního počítače Azure Quickstarts najdete v části: 
  - [Vytvoření virtuálního počítače s Windows pomocí rozhraní příkazového řádku](../../virtual-machines/windows/quick-create-cli.md)  
  - [Vytvořit virtuální počítač s Linuxem pomocí rozhraní příkazového řádku](../../virtual-machines/linux/quick-create-cli.md) 

















