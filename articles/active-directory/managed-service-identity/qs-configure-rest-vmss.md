---
title: Přiřadit konfigurace systémových a uživatelských identit na VMSS Azure pomocí rozhraní REST
description: Podrobné pokyny ke konfiguraci systémových a uživatelských identit přiřazených na VMSS Azure pomocí příkazu CURL k volání rozhraní REST API.
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
ms.openlocfilehash: c4ccd75f679e58dd42f136bf1199eda88dc3f087
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431711"
---
# <a name="configure-managed-identity-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Identita spravované konfigurace pro sadu škálování virtuálního počítače pomocí volání rozhraní REST API

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Poskytuje spravovanou identitu služby Azure s identitou systému automaticky spravované v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak provádět následující operace Identity spravované v škálovací sadu virtuálních počítačů pro volání do koncového bodu REST Azure Resource Manageru pomocí příkazu CURL:

- Povolit a zakázat identitu přiřazenou systémem na škálovací sadu virtuálních počítačů Azure
- Přidání a odebrání identity přiřazené uživateli na škálovací sadu virtuálních počítačů Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) chcete vytvořit škálovací sadu virtuálních počítačů a povolit a odeberte systém a/nebo uživatel přiřazenou spravovanou identitu ze škálovací sady virtuálních počítačů.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role pro vytvoření identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat identity přiřazené uživateli, ze kterých a do škálovací sady virtuálních počítačů.
- Pokud používáte Windows, nainstalujte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) na webu Azure Portal.
- [Instalace Azure CLI místní konzoly](/azure/install-azure-cli), pokud použijete [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo [operačním systémem distribuce Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Pokud používáte místní konzoly příkazového řádku Azure, přihlaste se do Azure s využitím `az login` pomocí účtu, který je spojen s Azure identit přiřazených předplatné, které chcete spravovat systém nebo uživatel.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identitu přiřazenou systémem

V této části se dozvíte, jak můžete povolit nebo zakázat na virtuální počítač škálovací sadu pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru identitu přiřazenou systémem.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Povolit identitu přiřazenou systémem při vytváření škálovací sady virtuálních počítačů

K vytvoření virtuálního počítače škálovací sady s povolené identitu přiřazenou systémem, potřebujete vytvořit škálovací sadu virtuálních počítačů a načtení přístupového tokenu do používáme nástroj CURL k volání koncového bodu Resource Manageru s přiřazenou hodnotu typu identity systémem.

1. Vytvoření [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení škálovací sady virtuálních počítačů a její související prostředky, pomocí [vytvořit skupiny az](/cli/azure/group/#az-group-create). Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Vytvoření [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) škálovací virtuálního počítače nastavte:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sadě pomocí spravované identity přiřazené systému.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvoření virtuálního počítače škálovací sady pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru. Následující příklad vytvoří škálovací sadu virtuálních počítačů *myVMSS* v *myResourceGroup* s identitou přiřazenou systémem, protože identifikované v textu požadavku hodnotu `"identity":{"type":"SystemAssigned"}`. Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Povolit identitu přiřazenou systémem v existující škálovací sady virtuálních počítačů Azure

Pokud chcete povolit identitu v existující škálovací sady virtuálních počítačů přiřazenou systémem, musíte k získání přístupového tokenu a poté používáme nástroj CURL k volání koncového bodu REST Resource Manageru, a aktualizovat typ identity.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sadě pomocí spravované identity přiřazené systému.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Použít následující příkaz CURL k volání koncového bodu REST Azure Resource Manageru se povolit identitu přiřazenou systémem ve virtuálním počítači škálovací sadu jsme uvedli v textu požadavku hodnotou `{"identity":{"type":"SystemAssigned"}` pro škálovací sadu virtuálních počítačů  *myVMSS*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
   
   > [!IMPORTANT]
   > Aby nechcete odstranit všechny existující uživatelské spravovaných identit, které jsou přiřazeny do škálovací sady virtuálních počítačů přiřazených, budete muset seznam uživatelsky přiřazených identit pomocí tohoto příkazu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte jakékoli uživatelsky přiřazených identit přiřazená škálovací sady v jsme uvedli, virtuálních počítačů `identity` hodnota v odpovědi, přejděte ke kroku 3, který ukazuje, jak zachovat uživatelsky přiřazených identit při povolování identitou přiřazenou systémem na vaše virtuální škálovací sady počítačů.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Pokud chcete povolit identitu na škálovací sadu s existující uživatelsky přiřazených identit virtuálních počítačů přiřazenou systémem, je třeba přidat `SystemAssigned` k `type` hodnotu.  
   
   Například pokud vaše škálovací sada virtuálních počítačů má uživatelsky přiřazených identit `ID1` a `ID2` přiřazené, a chcete přidat identitou přiřazenou systémem pro škálovací sadu virtuálních počítačů, použijte následující volání CURL. Nahraďte `<ACCESS TOKEN>` a `<SUBSCRIPTION ID>` s hodnotami, které jsou vhodné pro vaše prostředí.
   
   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Zakázat identitu přiřazenou systémem ze škálovací sady virtuálních počítačů Azure

Se zakázat identitu přiřazenou systémem v existující škálovací sady virtuálních počítačů, budete potřebovat k získání přístupového tokenu a poté používáme nástroj CURL k volání koncového bodu REST Resource Manageru, a aktualizovat typ identity k `None`.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sadě pomocí spravované identity přiřazené systému.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualizujte virtuální počítač škálovací sadě pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru zakázat identitu přiřazenou systémem.  Následující příklad zakazuje identitou přiřazenou systémem jsme uvedli v textu požadavku hodnotou `{"identity":{"type":"None"}}` z virtuálního počítače škálovací sadu s názvem *myVMSS*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   > [!IMPORTANT]
   > Aby nechcete odstranit všechny existující uživatelské spravovaných identit, které jsou přiřazeny do škálovací sady virtuálních počítačů přiřazených, budete muset seznam uživatelsky přiřazených identit pomocí tohoto příkazu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte jakékoli identity, které jsou přiřazeny do škálovací sady virtuálního počítače přiřazené uživateli, přejděte ke kroku 3, který ukazuje, jak zachovat uživatele identit přiřazených při odebírání systému identity přiřazené ze škálovací sady virtuálních počítačů.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Chcete-li odebrat ze škálovací sady virtuálního počítače, který má uživatelsky přiřazených identit identitu přiřazenou systémem, odeberte `SystemAssigned` z `{"identity":{"type:" "}}` hodnotu při zachování `UserAssigned` hodnotu a `identityIds` pole definuje co uživatelsky přiřazených identit jsou přiřazeny do škálovací sady virtuálních počítačů.

## <a name="user-assigned-identity"></a>Identity přiřazené uživateli

V této části se dozvíte, jak přidávat a odebírat uživatele přiřazeny identitu na virtuální počítač škálovací sadu pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřadit identity přiřazené uživateli při vytváření škálovací sady virtuálních počítačů

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sadě pomocí spravované identity přiřazené systému.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Vytvoření [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) škálovací virtuálního počítače nastavte:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sadě pomocí spravované identity přiřazené systému.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvoření identity přiřazené uživateli pomocí pokynů uvedených zde: [vytvoření spravované identity přiřazené uživateli](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Vytvoření virtuálního počítače škálovací sady pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru. Následující příklad vytvoří škálovací sadu virtuálních počítačů *myVMSS* ve skupině prostředků *myResourceGroup* s využitím identity přiřazené uživateli `ID1`, jsme uvedli v textu požadavku podle Hodnota `"identity":{"type":"UserAssigned"}`. Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Identity přiřazené uživateli přiřadit existující škálovací sady virtuálních počítačů Azure

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sadě pomocí spravované identity přiřazené systému.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Vytvoření identity přiřazené uživateli pomocí pokynů uvedených zde [vytvoření spravované identity přiřazené uživateli](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Ujistěte se, že neodstraníte existujícího uživatele nebo systému přiřadili spravovaných identit, které jsou přiřazeny do škálovací sady virtuálních počítačů, budete muset seznamu identit, které typy přiřazené ke škálování virtuálního počítače nastavte pomocí následující příkaz CURL. Pokud máte spravované identity přiřazené do škálovací sady virtuálního počítače, jsou uvedeny v části v `identity` hodnotu.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Pokud nemáte k dispozici všechny uživatele nebo systému identit, které jsou přiřazeny do škálovací sady virtuálních počítačů přiřazených, použijte následující příkaz CURL k volání koncového bodu REST Azure Resource Manageru k přiřazování identity přiřazené do škálovací sady virtuálních počítačů první uživateli.  Pokud máte uživatele, nebo systém přiřadil identity(s) přiřazené do škálovací sady virtuálních počítačů, přejděte ke kroku 5, který ukazuje, jak přidat více uživatelsky přiřazených identit do virtuálního počítače škálovací sady současně zachovává identitou přiřazenou systémem.

   Následující příklad přiřadí identity přiřazené uživateli, `ID1` do virtuálního počítače škálovací sady s názvem *myVMSS* ve skupině prostředků *myResourceGroup*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Pokud máte uživatele nebo přiřazenou systémem identit přiřazená škálovací sadu virtuálních počítačů, budete muset přidat nové identity přiřazené uživateli na `identityIDs` pole při současném zachování uživatele a přiřazenou systémem identit, které jsou přiřazeny k virtuální škálovací sady počítačů.

   Například pokud máte systém přiřadil identity a identity přiřazené uživateli `ID1` aktuálně přiřazeno škálovací sadu virtuálních počítačů a chcete přidat identitu uživatele `ID2` , použijte následující příkaz CURL. Nahraďte `<ACCESS TOKEN>` s hodnotou jste obdrželi v krocích při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Identity přiřazené uživateli odebrat ze škálovací sady virtuálních počítačů

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sadě pomocí spravované identity přiřazené systému.

   ```azurecli-interactive
   az account get-access-token
   ```

2. K zajištění, že není odstranit všechny existující uživatele přiřazeny spravovaných identit, které chcete zachovat přiřazené do škálovací sady virtuálního počítače nebo odebrat identitu přiřazenou systémem, je třeba seznam spravovaných identit pomocí příkazu CURL: 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Pokud máte spravované identity přiřazené k virtuálnímu počítači, jsou uvedeny v odpovědi v `identity` hodnotu. 
    
   Například, pokud máte uživatelsky přiřazených identit `ID1` a `ID2` přiřazeny do škálovací sady virtuálních počítačů a pouze chcete mít `ID1` přiřazeny a zachovat identitu přiřazenou systémem, použijte stejný příkaz CURL jako Přiřazení uživatele přiřazeno škálovací sadu virtuálních počítačů spravovaná identita nastavena pouze zachování `ID1` hodnotu a udržovat `SystemAssigned` hodnotu. Tato operace odebere `ID2` identity přiřazené uživateli z virtuálního počítače škálovací při zachování identitou přiřazenou systémem.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Pokud vaše škálovací sada virtuálních počítačů má přiřazenou systémem a identity přiřazené uživateli, můžete odebrat všechny uživatele identit přiřazených přepnutím používat jen systém přiřazené, pomocí následujícího příkazu:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Pokud byste chtěli odebrat je všechny vaše škálovací sada virtuálních počítačů má pouze uživatele identit přiřazených, použijte následující příkaz:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Další postup

Informace o tom, jak vytvořit, vypsat nebo odstranit uživatele přiřazeny, pomocí rozhraní REST naleznete v tématu:

- [Vytváření, seznamu a odstraňování identity přiřazené uživateli, volání rozhraní REST API](how-to-manage-ua-identity-rest.md)