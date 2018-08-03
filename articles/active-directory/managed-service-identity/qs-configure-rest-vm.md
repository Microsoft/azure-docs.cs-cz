---
title: Přiřadit konfigurace systémových a uživatelských identit na Virtuálním počítači Azure pomocí rozhraní REST
description: Podrobné pokyny ke konfiguraci systémových a uživatelských identit přiřazených k Virtuálnímu počítači Azure pomocí příkazu CURL k volání rozhraní REST API.
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
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: 7926944f329665af2df287d120bd9f4a8ee78380
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433931"
---
# <a name="configure-managed-identity-on-an-azure-vm-using-rest-api-calls"></a>Konfigurace Identity spravované na Virtuálním počítači Azure pomocí volání rozhraní REST API

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Poskytuje spravovanou identitu služby Azure s identitou systému automaticky spravované v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující Identity spravované operace na Virtuálním počítači Azure, aby volání koncového bodu REST Azure Resource Manageru pomocí příkazu CURL:

- Povolit nebo zakázat identitu na Virtuálním počítači Azure přiřazenou systémem
- Přidávat a odebírat uživatele přiřazeny identitu na Virtuálním počítači Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) k vytvoření virtuálního počítače a povolit a odeberte systém a/nebo uživatel spravované identity přiřazené z virtuálního počítače Azure.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role pro vytvoření identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat identity přiřazené uživateli, ze kterých a do virtuálního počítače.
- Pokud používáte Windows, nainstalujte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) na webu Azure Portal.
- [Instalace Azure CLI místní konzoly](/azure/install-azure-cli), pokud použijete [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo [operačním systémem distribuce Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Pokud používáte místní konzoly příkazového řádku Azure, přihlaste se do Azure s využitím `az login` pomocí účtu, který je spojen s Azure identit přiřazených předplatné, které chcete spravovat systém nebo uživatel.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem

V této části se dozvíte, jak můžete povolit nebo zakázat na Virtuálním počítači Azure pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru identitu přiřazenou systémem.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Povolit systém identity přiřazené během vytváření virtuálního počítače Azure

K vytvoření virtuálního počítače Azure s povolené identitu přiřazenou systémem, potřebujete vytvořit virtuální počítač a načtení přístupového tokenu do používáme nástroj CURL k volání koncového bodu Resource Manageru s přiřazenou hodnotu typu identity systémem.

1. Pomocí příkazu [az group create](/cli/azure/group/#az-group-create) vytvořte [skupinu prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro nasazení a uchování virtuálního počítače a souvisejících prostředků. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Vytvoření [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pro váš virtuální počítač:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače se systémem spravované identity přiřazené.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvoření virtuálního počítače pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru. Následující příklad vytvoří virtuální počítač s názvem *myVM* s identitou přiřazenou systémem, protože identifikované v textu požadavku hodnotu `"identity":{"type":"SystemAssigned"}`. Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Povolit v existujícím virtuálním počítači Azure identitu přiřazenou systémem

Pokud chcete povolit identitu na existující virtuální počítač přiřazenou systémem, musíte k získání přístupového tokenu a poté používáme nástroj CURL k volání koncového bodu REST Resource Manageru, a aktualizovat typ identity.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače se systémem spravované identity přiřazené.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Použijte následující příkaz CURL k volání koncového bodu REST Azure Resource Manageru tak, aby povolovala identitou přiřazenou systémem ve virtuálním počítači identifikované v textu požadavku hodnotu `{"identity":{"type":"SystemAssigned"}` pro virtuální počítač s názvem *myVM*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
   
   > [!IMPORTANT]
   > Abyste zajistili, není odstranili všechny stávající uživatel přiřazený spravovaných identit, které jsou přiřazeny k virtuálnímu počítači, budete muset seznam uživatelsky přiřazených identit pomocí tohoto příkazu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte jakékoli uživatelsky přiřazených identit přiřazené k virtuálnímu počítači v jsme uvedli, `identity` hodnoty v odpovědi, přejděte ke kroku 3, který ukazuje, jak zachovat uživatelsky přiřazených identit při povolování identitou přiřazenou systémem ve virtuálním počítači.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Pokud chcete povolit identitu na virtuálním počítači s existující uživatelsky přiřazených identit přiřazenou systémem, je třeba přidat `SystemAssigned` k `type` hodnotu.  
   
   Například, pokud má virtuální počítač uživatelsky přiřazených identit `ID1` a `ID2` přiřazené, a chcete přidat identitou přiřazenou systémem k virtuálnímu počítači, použijte následující volání CURL. Nahraďte `<ACCESS TOKEN>` a `<SUBSCRIPTION ID>` s hodnotami, které jsou vhodné pro vaše prostředí.
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Zakázat z virtuálního počítače Azure identitu přiřazenou systémem

Zakázat identitu přiřazenou systémem na existující virtuální počítač, budete potřebovat k získání přístupového tokenu a poté používáme nástroj CURL k volání koncového bodu REST Resource Manageru, a aktualizovat typ identity k `None`.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače se systémem spravované identity přiřazené.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualizace virtuálního počítače pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru zakázat identitu přiřazenou systémem.  Následující příklad zakazuje identitou přiřazenou systémem jsme uvedli v textu požadavku hodnotou `{"identity":{"type":"None"}}` z virtuálního počítače s názvem *myVM*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   > [!IMPORTANT]
   > Abyste zajistili, není odstranili všechny stávající uživatel přiřazený spravovaných identit, které jsou přiřazeny k virtuálnímu počítači, budete muset seznam uživatelsky přiřazených identit pomocí tohoto příkazu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte jakékoli uživatelsky přiřazených identit přiřazené k virtuálnímu počítači v jsme uvedli, `identity` hodnoty v odpovědi, přejděte ke kroku 3, který ukazuje, jak zachovat uživatelsky přiřazených identit při zakazování identitou přiřazenou systémem ve virtuálním počítači.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Chcete-li odebrat z virtuálního počítače, který má uživatelsky přiřazených identit identitu přiřazenou systémem, odeberte `SystemAssigned` z `{"identity":{"type:" "}}` hodnotu při zachování `UserAssigned` hodnotu a `identityIds` pole, která definuje, jaké uživatelsky přiřazených identit jsou přiřazeny k virtuálnímu počítači.

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části se dozvíte, jak přidávat a odebírat uživatele přiřazeny identitu na Virtuálním počítači Azure pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Přiřazování identity přiřazené během vytváření virtuálního počítače Azure uživateli

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače se systémem spravované identity přiřazené.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Vytvoření [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pro váš virtuální počítač:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače se systémem spravované identity přiřazené.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvoření identity přiřazené uživateli pomocí pokynů uvedených zde: [vytvoření spravované identity přiřazené uživateli](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Vytvoření virtuálního počítače pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru. Následující příklad vytvoří virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup* s využitím identity přiřazené uživateli `ID1`, jak identifikované v textu požadavku hodnotu `"identity":{"type":"UserAssigned"}`. Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Přiřazování identity přiřazené do stávajícího virtuálního počítače Azure uživateli

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače se systémem spravované identity přiřazené.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Vytvoření identity přiřazené uživateli pomocí pokynů uvedených zde [vytvoření spravované identity přiřazené uživateli](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3.  Aby neprovedete odstranění existujícího uživatele nebo systém přiřadil spravovaných identit, které jsou přiřazeny k virtuálnímu počítači potřebujete seznam typů identity přiřazené k virtuálnímu počítači s použitím následující příkaz CURL. Pokud máte spravované identity přiřazené do škálovací sady virtuálního počítače, jsou uvedeny v části v `identity` hodnotu.

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Pokud máte všechny uživatele nebo přiřazenou systémem identit přiřazené k virtuálnímu počítači v jsme uvedli `identity` hodnoty v odpovědi, přejděte ke kroku 5, který ukazuje, jak zachovat uživatelsky přiřazených identit při povolování identitou přiřazenou systémem ve virtuálním počítači.

4. Pokud nemáte k dispozici žádný uživatel přiřazené identity přiřazené k vašemu virtuálnímu počítači, použijte následující příkaz CURL k volání koncového bodu REST Azure Resource Manageru k přiřazování identity přiřazené k virtuálnímu počítači první uživateli.  Pokud máte uživatele přiřazeny identity(s) přiřazené k virtuálnímu počítači, přejděte k dalšímu kroku, který ukazuje, jak přidat více uživatelsky přiřazených identit k virtuálnímu počítači.

   Následující příklad přiřadí identity přiřazené uživateli, `ID1` k virtuálnímu počítači s názvem *myVM* ve skupině prostředků *myResourceGroup*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Pokud máte uživatele nebo přiřazenou systémem identit přiřazené k vašemu virtuálnímu počítači, budete muset přidat nové identity přiřazené uživateli na `identityIDs` pole při současném zachování uživatele a přiřazenou systémem identit, které momentálně nejsou přiřazené k virtuálnímu počítači.

   Pro příklad: Pokud máte systém přiřazenou identity a identity přiřazené uživateli `ID1` aktuálně přiřazené k vašemu virtuálnímu počítači a chcete přidat identitu uživatele `ID2` , použijte následující příkaz CURL. Nahraďte `<ACCESS TOKEN>` s hodnotou jste obdrželi v krocích při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Odebrání uživatele identity přiřazené z virtuálního počítače Azure

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače se systémem spravované identity přiřazené.

   ```azurecli-interactive
   az account get-access-token
   ```

2. K zajištění, že není odstranit všechny existující uživatele přiřazeny spravovaných identit, které chcete zachovat přiřazené k virtuálnímu počítači nebo odebrat identitu přiřazenou systémem, je třeba seznam spravovaných identit pomocí příkazu CURL: 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   Pokud máte spravované identity přiřazené k virtuálnímu počítači, jsou uvedeny v odpovědi v `identity` hodnotu.

   Například, pokud máte uživatelsky přiřazených identit `ID1` a `ID2` přiřazené k vašemu virtuálnímu počítači a pouze chcete mít `ID1` přiřazeny a zachovat identitu přiřazenou systémem, jako přiřazení uživatele přiřazeny použijete stejný příkaz CURL k virtuálnímu počítači jenom zachování identity spravované `ID1` hodnotu a udržovat `SystemAssigned` hodnotu. Tato operace odebere `ID2` identity přiřazené uživateli z virtuálního počítače při zachování identitou přiřazenou systémem.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Pokud váš virtuální počítač má přiřazenou systémem a identity přiřazené uživateli, můžete odebrat všechny uživatele identit přiřazených přepnutím používat jen systém přiřazené, pomocí následujícího příkazu:

```bash
curl 'https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Pokud váš virtuální počítač má pouze uživatele identit přiřazených a chcete odebrat je všechny, použijte následující příkaz:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>Další postup

Informace o tom, jak vytvořit, vypsat nebo odstranit uživatele přiřazeny, pomocí rozhraní REST naleznete v tématu:

- [Vytváření, seznamu a odstraňování identity přiřazené uživateli, volání rozhraní REST API](how-to-manage-ua-identity-rest.md)