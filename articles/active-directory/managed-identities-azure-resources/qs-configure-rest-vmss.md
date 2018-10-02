---
title: Postup konfigurace systému a uživatelsky přiřazené identity spravované v VMSS Azure pomocí rozhraní REST
description: Podrobné pokyny ke konfiguraci systému a uživatelsky přiřazené identity spravované v VMSS Azure pomocí příkazu CURL k zajištění REST API volá.
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
ms.openlocfilehash: e76375ac06824d03e519ba06da838a0707922280
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017412"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuální počítač škálovací sady s použitím volání rozhraní REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure s identitou systému automaticky spravované v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru, se dozvíte, jak k provádění následujících spravovaných identit pro operace se prostředky Azure na škálovací sadu virtuálních počítačů:

- Povolení a zákaz systém přiřadil spravovaná identita na škálovací sadu virtuálních počítačů Azure
- Přidání a odebrání uživatelsky přiřazené spravovanou identitu na škálovací sadu virtuálních počítačů Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení řízení přístupu na základě rolí Azure:

    > [!NOTE]
    > Žádné další Azure vyžaduje přiřazení rolí adresáře AD.

    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) vytvořit škálovací sadu virtuálních počítačů a povolit a odeberte systém a/nebo uživatelsky přiřazené identity spravované ze škálovací sady virtuálních počítačů.
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření uživatel přiřazenou se identita spravované.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roli přiřadit a odebrat uživatelsky přiřazené identity od a do škálovací sady virtuálních počítačů.
- Pokud používáte Windows, nainstalujte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) na webu Azure Portal.
- [Instalace Azure CLI místní konzoly](/cli/azure/install-azure-cli), pokud použijete [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo [operačním systémem distribuce Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Pokud používáte místní konzoly příkazového řádku Azure, přihlaste se do Azure s využitím `az login` pomocí účtu, který je přidružený k předplatnému Azure chcete ke správě systému nebo uživatelsky přiřazené identity spravované.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se dozvíte, jak můžete povolit nebo zakázat systém přiřadil spravovaná identita na virtuální počítač škálovací sadu pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Povolit systém přiřadil spravovanou identitu při vytváření škálovací sady virtuálních počítačů

K vytvoření virtuálního počítače škálovací sady s povolen systém přiřadil spravovanou identitu, potřebujete vytvořit škálovací sadu virtuálních počítačů a načtení přístupového tokenu do používáme nástroj CURL k volání koncového bodu Resource Manageru s hodnotou typu systém přiřadil spravovaná identita.

1. Vytvoření [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení škálovací sady virtuálních počítačů a její související prostředky, pomocí [vytvořit skupiny az](/cli/azure/group/#az-group-create). Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Vytvoření [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) škálovací virtuálního počítače nastavte:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sady s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvoření virtuálního počítače škálovací sady pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru. Následující příklad vytvoří škálovací sadu virtuálních počítačů *myVMSS* v *myResourceGroup* s systém přiřadil spravované identity, jako identifikované v textu požadavku hodnotu `"identity":{"type":"SystemAssigned"}`. Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-managed-identity-on-a-existing-virtual-machine-scale-set"></a>Povolit systém přiřadil spravované identity v existující škálovací sady virtuálních počítačů

Chcete-li systém přiřadil spravovaná identita v existující škálovací sady virtuálních počítačů, musíte k získání přístupového tokenu a poté používáme nástroj CURL k volání koncového bodu REST Resource Manageru, a aktualizovat typ identity.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sady s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Použít následující příkaz CURL k volání koncového bodu REST Azure Resource Manageru umožňují systém přiřadil spravovanou identitu ve virtuálním počítači škálovací sadu jsme uvedli v textu požadavku hodnotou `{"identity":{"type":"SystemAssigned"}` pro virtuální počítač škálovací sadu s názvem *myVMSS*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
   
   > [!IMPORTANT]
   > Aby neodstraňujte žádné existující uživatelsky přiřazené spravované identity, které jsou přiřazeny do škálovací sady virtuálních počítačů, budete muset seznam uživatelsky přiřazené identity spravované pomocí tohoto příkazu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte přiřazená uživatelem spravované identity přiřazené do virtuálního počítače škálovací sady v jsme uvedli, `identity` hodnoty v odpovědi, přejděte ke kroku 3, který ukazuje, jak zachovat uživatelsky přiřazené identity spravované při povolování systém přiřadil spravovaná identita na škálovací sadu virtuálních počítačů.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Pokud chcete povolit systém přiřadil spravovaná identita na virtuální počítač škálovací sadu s existující spravované uživatelsky přiřazené identity, je třeba přidat `SystemAssigned` k `type` hodnotu.  
   
   Například pokud vaše škálovací sada virtuálních počítačů má uživatel přiřazenou spravovat identity `ID1` a `ID2` přiřazené, a chcete přidat systém přiřadil spravovanou identitu do škálovací sady virtuálního počítače, použijte následující volání CURL. Nahraďte `<ACCESS TOKEN>` a `<SUBSCRIPTION ID>` s hodnotami, které jsou vhodné pro vaše prostředí.

   Verze rozhraní API `2018-06-01` ukládá spravované uživatelsky přiřazené identity v `userAssignedIdentities` hodnoty ve slovníku formátu, nikoli `identityIds` hodnotu ve formátu pole používá ve verzi rozhraní API `2017-12-01`.
   
   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```
   
   **ROZHRANÍ API VERZE 2017-12-01**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Zakázat systém přiřadil spravovanou identitu ze škálovací sady virtuálních počítačů

Zakázat systém přiřadil identitou v existující škálovací sady virtuálních počítačů, budete potřebovat k získání přístupového tokenu a poté používáme nástroj CURL k volání koncového bodu REST Resource Manageru, a aktualizovat typ identity k `None`.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sady s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualizujte virtuální počítač škálovací sadě pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru zakázat systém přiřadil spravovaná identita.  Následující příklad zakazuje systém přiřadil spravovanou identitu, která jsou identifikované v textu požadavku hodnotu `{"identity":{"type":"None"}}` z virtuálního počítače škálovací sadu s názvem *myVMSS*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   > [!IMPORTANT]
   > Aby neodstraňujte žádné existující uživatelsky přiřazené spravované identity, které jsou přiřazeny do škálovací sady virtuálních počítačů, budete muset seznam uživatelsky přiřazené identity spravované pomocí tohoto příkazu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte jakékoli uživatelsky přiřazené spravovanou identitu přiřazenou škálovací sadu virtuálních počítačů, přejděte ke kroku 3, který ukazuje, jak uchovávat spravované uživatelsky přiřazené identity při odebírání systém přiřadil spravovanou identitu ze škálovací sady virtuálních počítačů.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Odebrat ze škálovací sady virtuálního počítače, který má uživatelsky přiřazené identity spravované systém přiřadil spravovanou identitu, odeberte `SystemAssigned` z `{"identity":{"type:" "}}` hodnotu při zachování `UserAssigned` hodnotu a `userAssignedIdentities` slovník hodnot, pokud jste používáte **rozhraní API verze 2018-06-01**. Pokud používáte **rozhraní API verze 2017-12-01** nebo starší, nechat `identityIds` pole.

## <a name="user-assigned-managed-identity"></a>Uživatel přiřazenou spravované identity

V této části se dozvíte, jak přidávat a odebírat uživatele přiřazeny spravovaná identita na virtuální počítač škálovací sadu pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřadit uživateli přiřazena spravovanou identitu při vytváření škálovací sady virtuálních počítačů

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sady s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Vytvoření [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) škálovací virtuálního počítače nastavte:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sady s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvoření uživatelsky přiřazené spravovanou identitu pomocí pokynů uvedených zde: [vytvoření uživatelsky přiřazené identity spravované](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Vytvoření virtuálního počítače škálovací sady pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru. Následující příklad vytvoří škálovací sadu virtuálních počítačů *myVMSS* ve skupině prostředků *myResourceGroup* pomocí spravované identity přiřazené uživateli `ID1`, jsme uvedli v textu požadavku hodnotou `"identity":{"type":"UserAssigned"}`. Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
 
   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   **ROZHRANÍ API VERZE 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Přiřadit uživateli přiřazena spravovanou identitu do existující škálovací sady virtuálních počítačů Azure

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sady s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Vytvoření uživatelsky přiřazené spravovanou identitu pomocí pokynů uvedených zde [vytvoření uživatelsky přiřazené identity spravované](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Zajistěte, aby že neprovedete odstranění existujícího uživatele nebo systém přiřadil spravovaných identit, které jsou přiřazeny do škálovací sady virtuálních počítačů, budete muset seznamu identit, které typy přiřazené ke škálování virtuálního počítače nastavte pomocí následující příkaz CURL. Pokud máte spravované identity přiřazené do škálovací sady virtuálního počítače, jsou uvedeny v `identity` hodnotu.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Pokud nemáte žádný uživatel nebo systém přiřadil spravovaný identity přiřazené do virtuálního počítače škálovací nastavení, použijte následující příkaz CURL k volání koncového bodu REST Azure Resource Manageru přiřadit první spravovaná identita uživatelsky přiřazené k virtuálnímu počítači škálovací sady.  Pokud máte uživatele nebo systém přiřadil spravované identity(s) přiřazené do škálovací sady virtuálních počítačů, přejděte ke kroku 5, který ukazuje, jak přidat více spravovaných uživatelsky přiřazené identity pro škálovací sadu virtuálních počítačů současně zachovává systém přiřadil spravované Identita.

   Následující příklad přiřadí uživateli přiřazena spravovanou identitu, `ID1` do virtuálního počítače škálovací sady s názvem *myVMSS* ve skupině prostředků *myResourceGroup*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   **ROZHRANÍ API VERZE 2018-06-01**

    ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   
    
   **ROZHRANÍ API VERZE 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Pokud máte existující uživatelsky přiřazené nebo systém přiřadil spravovaná identita přiřazená škálovací sadu virtuálních počítačů:
   
   **ROZHRANÍ API VERZE 2018-06-01**

   Přidat spravovanou identitu pro uživatelsky přiřazené `userAssignedIdentities` hodnota slovníku.

   Například, pokud máte systém přiřadil spravovanou identitu a uživatelsky přiřazené spravovanou identitu `ID1` aktuálně přiřazená virtuální počítač škálovací a chcete přidat spravované uživatelsky přiřazené identity `ID2` do ní:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **ROZHRANÍ API VERZE 2017-12-01**

   Zachovat uživatelem přidělenou spravované identity byste chtěli zachovat v `identityIds` hodnota pole při přidávání nové uživatelsky přiřazené spravovaná identita.

   Například, pokud máte systém přiřadil identity a uživatel přiřazenou spravovanou identitu `ID1` aktuálně přiřazeno škálovací sadu virtuálních počítačů a chcete přidat spravované uživatelsky přiřazené identity `ID2` do ní: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Odeberte uživatelsky přiřazené spravovanou identitu ze škálovací sady virtuálních počítačů

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače škálovací sady s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. K zajištění, že není odstranit všechny existující uživatelsky přiřazené spravované identity, které chcete zachovat přiřazené do škálovací sady virtuálních počítačů nebo odeberte systém přiřadil spravovanou identitu, budete muset seznamu spravovaných identit pomocí následující příkaz CURL : 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Pokud máte spravované identity přiřazené k virtuálnímu počítači, jsou uvedeny v odpovědi v `identity` hodnotu. 
    
   Například, pokud máte uživatelsky přiřazené identity spravované `ID1` a `ID2` přiřazeny do škálovací sady virtuálních počítačů, a chcete zachovat `ID1` přiřazeny a zachovat systém přiřadil spravovaná identita:

   **ROZHRANÍ API VERZE 2018-06-01**

   Přidat `null` uživatel přiřazenou spravovaných identit, které chcete odebrat:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **ROZHRANÍ API VERZE 2017-12-01**

   Zachovat jenom přiřazené uživatele identity(s) byste chtěli zachovat v spravovaných `identityIds` pole:   

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Pokud vaše škálovací sada virtuálních počítačů jsou obě systém přiřadil a uživatelsky přiřazené identity spravované, můžete odebrat všechno, co uživatel přiřazenou spravovaných identit přepnutím na použití pouze systém přiřadil pomocí následujícího příkazu:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Pokud vaše škálovací sada virtuálních počítačů má jenom přiřazené uživatele spravované identity a chcete odebrat je všechny, použijte následující příkaz:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Další postup

Informace o tom, jak vytvořit, vypsat nebo odstranit uživatelem přidělenou spravovaných identit pomocí rozhraní REST najdete tady:

- [Vytvoření seznamu nebo odstranit uživatelem přidělenou spravovanou identitu volání rozhraní REST API](how-to-manage-ua-identity-rest.md)
