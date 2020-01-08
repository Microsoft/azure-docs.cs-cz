---
title: Konfigurace spravovaných identit v Azure VMSS pomocí REST – Azure AD
description: Podrobné pokyny pro konfiguraci systémových a uživatelem přiřazených identit na platformě Azure VMSS pomocí technologie KUDRLINKOU k provádění REST API volání.
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
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: dce9894b26d03c351a2209792cc076de91feba54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429984"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Konfigurace spravovaných identit pro prostředky Azure v sadě škálování virtuálních počítačů pomocí REST API volání

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou systému v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se pomocí objektu KUDRLINKOU pro volání Azure Resource Managerho koncového bodu REST naučíte provádět následující spravované identity pro operace prostředků Azure v sadě škálování virtuálního počítače:

- Povolení a zakázání spravované identity přiřazené systémem v sadě škálování virtuálních počítačů Azure
- Přidání a odebrání spravované identity přiřazené uživatelem v sadě škálování virtuálních počítačů Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- K provedení operací správy v tomto článku potřebuje váš účet následující přiřazení řízení přístupu na základě rolí Azure:

    > [!NOTE]
    > Nevyžadují se žádné další přiřazení role adresáře Azure AD.

    - [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) , aby vytvořil sadu škálování virtuálního počítače a povolil a odebral systémovou a/nebo uživatelsky spravovanou identitu ze sady škálování virtuálního počítače.
    - Role [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) k vytvoření spravované identity přiřazené uživatelem.
    - Role [operátora spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pro přiřazení a odebrání uživatelsky přiřazené identity z a do sady škálování virtuálního počítače.
- Pokud používáte systém Windows, nainstalujte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) v Azure Portal.
- Pokud používáte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo systém pro [distribuci Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [nainstalujte místní konzolu Azure CLI](/cli/azure/install-azure-cli).
- Pokud používáte místní konzolu Azure CLI, přihlaste se k Azure pomocí `az login` s účtem, který je přidružený k předplatnému Azure. chcete spravovat spravované identity v systému nebo uživateli.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravovanou identitu přiřazenou systémem v sadě škálování virtuálního počítače pomocí funkce KUDRLINKOU pro volání Azure Resource Manager koncového bodu REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření sady škálování virtuálního počítače

Chcete-li vytvořit sadu škálování virtuálního počítače s povolenou spravovanou identitou přiřazenou systémem, je nutné vytvořit sadu škálování virtuálního počítače a načíst přístupový token pro použití prvku KUDRLINKOU k volání Správce prostředků koncového bodu s hodnotou spravovaného typu identity přiřazené systémem.

1. Vytvořte [skupinu prostředků](../../azure-resource-manager/management/overview.md#terminology) pro omezení a nasazení sady škálování virtuálních počítačů a souvisejících prostředků pomocí [AZ Group Create](/cli/azure/group/#az-group-create). Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Vytvořte [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pro sadu škálování virtuálního počítače:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření sady škálování virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvořte sadu škálování virtuálního počítače pomocí OBLÉ pro volání koncového bodu REST Azure Resource Manager. Následující příklad vytvoří v *myResourceGroup* sadu škálování virtuálního počítače s názvem *myVMSS* , která má spravovanou identitu přiřazenou systémem, jak je uvedeno v těle žádosti hodnotou `"identity":{"type":"SystemAssigned"}`. Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Povolit spravovanou identitu přiřazenou systémem v existující sadě škálování virtuálního počítače

Pokud chcete v existující sadě škálování virtuálního počítače povolit spravovanou identitu přiřazenou systémem, musíte získat přístupový token a pak pomocí objektu KUDRLINKOU volat Správce prostředků koncový bod REST pro aktualizaci typu identity.

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření sady škálování virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Pomocí následujícího příkazu KUDRLINKOU zavolejte Azure Resource Manager koncový bod REST, abyste ve své sadě škálování virtuálního počítače povolili spravovanou identitu určenou systémem, jak je uvedeno v textu žádosti `{"identity":{"type":"SystemAssigned"}` pro sadu škálování virtuálního počítače s názvem *myVMSS*.  Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.
   
   > [!IMPORTANT]
   > K tomu, abyste se ujistili, že neodstraňujete žádné existující spravované identity přiřazené uživatelem, které jsou přiřazené k sadě škálování virtuálních počítačů, musíte pomocí tohoto příkazu SLOŽENÉho příkazu vytvořit seznam spravovaných identit přiřazených uživateli: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte ke službě škálování virtuálního počítače přiřazené nějaké spravované identity přiřazené uživatelem, které jsou identifikované v hodnotě `identity` v odpovědi, přejděte ke kroku 3, který vám ukáže, jak uchovávat spravované identity přiřazené uživatelem a zároveň povolit spravovanou identitu přiřazenou systémem v sadě škálování virtuálního počítače.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Pokud chcete povolit spravovanou identitu přiřazenou systémem v sadě škálování virtuálního počítače s existujícími spravovanými identitami přiřazenými uživatelem, musíte do `type` hodnoty přidat `SystemAssigned`.  
   
   Pokud má vaše sada škálování virtuálního počítače například `ID1` a `ID2` jim přiřazené spravované identity a vy chcete přidat spravovanou identitu přiřazenou systémem do sady škálování virtuálního počítače, použijte následující volání OBLÉ. Nahraďte `<ACCESS TOKEN>` a `<SUBSCRIPTION ID>` hodnotami, které jsou vhodné pro vaše prostředí.

   Verze rozhraní API `2018-06-01` ukládá uživatelem přiřazené identity ve formátu slovníku `userAssignedIdentities` ve formátu slovníku na rozdíl od `identityIds` hodnoty ve formátu pole používaném v rozhraní API verze `2017-12-01`.
   
   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. |
 
   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```
   
   **ROZHRANÍ API VERZE 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Zakázat spravovanou identitu přiřazenou systémem ze sady škálování virtuálních počítačů

Pokud chcete zakázat identitu přiřazenou systémem u existující sady škálování virtuálních počítačů, musíte získat přístupový token a pak pomocí prvku KUDRLINKOU volat Správce prostředků koncový bod REST pro aktualizaci typu identity na `None`.

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření sady škálování virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualizujte sadu škálování virtuálního počítače pomocí metody KUDRLINKOU a zavolejte Azure Resource Manager koncovému bodu REST, aby se zakázala spravovaná identita přiřazená systémem.  Následující příklad zakáže spravovanou identitu přiřazenou systémem, která je označená v těle žádosti, hodnotou `{"identity":{"type":"None"}}` ze sady škálování virtuálního počítače s názvem *myVMSS*.  Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.

   > [!IMPORTANT]
   > K tomu, abyste se ujistili, že neodstraňujete žádné existující spravované identity přiřazené uživatelem, které jsou přiřazené k sadě škálování virtuálních počítačů, musíte pomocí tohoto příkazu SLOŽENÉho příkazu vytvořit seznam spravovaných identit přiřazených uživateli: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte k sadě škálování virtuálního počítače přiřazenou spravovanou identitu přiřazenou uživatelem, přejděte ke kroku 3, který vám ukáže, jak zachovat uživatelem přiřazené spravované identity a zároveň odebrat spravovanou identitu přiřazenou systémem ze sady škálování virtuálních počítačů.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Pokud chcete odebrat spravovanou identitu přiřazenou systémem ze sady škálování virtuálního počítače, která má uživatelsky přiřazené spravované identity, odeberte `SystemAssigned` z `{"identity":{"type:" "}}` hodnoty a zároveň zachováte `UserAssigned` hodnotu a hodnoty `userAssignedIdentities` slovníku, pokud používáte **rozhraní API verze 2018-06-01**. Pokud používáte **rozhraní API verze 2017-12-01** nebo starší, nechejte pole `identityIds`.

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat spravovanou identitu přiřazenou uživatelem v sadě škálování virtuálního počítače pomocí funkce KUDRLINKOU pro volání Azure Resource Manager koncového bodu REST.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživatelem během vytváření sady škálování virtuálního počítače

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření sady škálování virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Vytvořte [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pro sadu škálování virtuálního počítače:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření sady škálování virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Pomocí pokynů, které najdete tady, vytvořte uživatelem přiřazenou spravovanou identitu: [vytvořte spravovanou identitu přiřazenou uživatelem](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Vytvořte sadu škálování virtuálního počítače pomocí OBLÉ pro volání koncového bodu REST Azure Resource Manager. V následujícím příkladu se vytvoří sada škálování virtuálního počítače s názvem *myVMSS* ve skupině prostředků *myResourceGroup* s uživatelem přiřazenou spravovanou identitou `ID1`, jak je uvedeno v těle žádosti hodnotou `"identity":{"type":"UserAssigned"}`. Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.
 
   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **ROZHRANÍ API VERZE 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. |
 
   **Text žádosti**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
             "storageProfile":{
                "imageReference":{
                   "sku":"2016-Datacenter",
                   "publisher":"MicrosoftWindowsServer",
                   "version":"latest",
                   "offer":"WindowsServer"
                },
                "osDisk":{
                   "caching":"ReadWrite",
                   "managedDisk":{
                      "storageAccountType":"Standard_LRS"
                   },
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Přiřazení spravované identity přiřazené uživatelem do existující sady škálování virtuálních počítačů Azure

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření sady škálování virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Pomocí pokynů, které najdete tady, vytvořte uživatelem přiřazenou spravovanou identitu a [vytvořte spravovanou identitu přiřazenou uživatelem](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Abyste se ujistili, že jste neodstranili stávající spravované identity uživatelů nebo systémem přiřazené k sadě škálování virtuálních počítačů, je potřeba uvést typy identit přiřazené k sadě škálování virtuálního počítače pomocí následujícího příkazu. Pokud máte spravované identity přiřazené k sadě škálování virtuálních počítačů, jsou uvedené v `identity` hodnotě.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. |   
 

4. Pokud nemáte žádné spravované identity přiřazené uživateli nebo systémem přiřazené k sadě škálování virtuálních počítačů, použijte následující příkaz, který zavolá Azure Resource Manager koncový bod REST pro přiřazení první spravované identity přiřazené uživateli k virtuálnímu počítači. sada škálování.  Pokud máte ke službě škálování virtuálního počítače přiřazené spravované identity uživatele nebo systému, přeskočte na krok 5, který vám ukáže, jak do sady škálování virtuálních počítačů přidat víc uživatelských identit přiřazených uživatelem, a zároveň udržovat spravované systémy přiřazené systémem. odcizen.

   Následující příklad přiřadí spravovanou identitu přiřazenou uživatelem `ID1` k sadě škálování virtuálního počítače s názvem *myVMSS* ve skupině prostředků *myResourceGroup*.  Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.

   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **ROZHRANÍ API VERZE 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Pokud máte přiřazenou spravovanou identitu přiřazenou uživatelem nebo systémem přiřazenou k vaší sadě škálování virtuálního počítače:
   
   **ROZHRANÍ API VERZE 2018-06-01**

   Přidejte spravovanou identitu přiřazenou uživatelem do hodnoty `userAssignedIdentities` slovníku.

   Například pokud máte spravovanou identitu přiřazenou systémem a uživatelem přiřazenou spravovanou identitu `ID1` aktuálně přiřazenou ke škálování virtuálního počítače a chcete přidat spravované identity přiřazené uživatelem `ID2`:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **ROZHRANÍ API VERZE 2017-12-01**

   Zachovejte spravované identity přiřazené uživatelem, které chcete zachovat v hodnotě `identityIds` pole při přidávání nové spravované identity přiřazené uživatelem.

   Například pokud máte identitu přiřazenou systémem a uživatelem přiřazenou spravovanou identitu `ID1` aktuálně přiřazenou k vaší sadě škálování virtuálního počítače a chcete do ní přidat spravovanou identitu přiřazenou uživatelem, `ID2`:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Odebrání spravované identity přiřazené uživatelem ze sady škálování virtuálních počítačů

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření sady škálování virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Abyste se ujistili, že neodstraníte žádné existující spravované identity přiřazené uživatelem, které byste chtěli zachovat pro sadu škálování virtuálního počítače, nebo odebrat spravovanou identitu přiřazenou systémem, musíte zobrazit seznam spravovaných identit pomocí následujícího příkazu KUDRLINKOU. :

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. |
   
   Pokud máte přiřazené spravované identity k virtuálnímu počítači, zobrazí se v odpovědi v hodnotě `identity`. 
    
   Například pokud máte uživatelem přiřazené identity `ID1` a `ID2` přiřazeni k sadě škálování virtuálního počítače a chcete zachovat `ID1` přiřazenou a zachovat spravovanou identitu přiřazenou systémem:

   **ROZHRANÍ API VERZE 2018-06-01**

   Přidejte `null` k uživatelem přiřazené spravované identitě, kterou chcete odebrat:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **ROZHRANÍ API VERZE 2017-12-01**

   Zachovejte pouze uživatelsky přiřazené spravované identity, které chcete ponechat v poli `identityIds`:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Pokud vaše sada škálování virtuálního počítače obsahuje spravované identity přiřazené systémem i uživatelem, můžete odebrat všechny spravované identity přiřazené uživatelem, a to tak, že pomocí následujícího příkazu přepnete jenom na přiřazené systémem:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Hlavičky požadavku**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

**Text žádosti**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Pokud má vaše sada škálování virtuálního počítače jenom spravované identity přiřazené uživatelem a Vy byste je chtěli odebrat, použijte následující příkaz:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Hlavičky požadavku**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`. | 

**Text žádosti**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak vytvořit, vypsat nebo odstranit spravované identity přiřazené uživatelem pomocí REST, najdete v těchto tématech:

- [Vytvoření, vypsání nebo odstranění spravované identity přiřazené uživatelem pomocí REST API volání](how-to-manage-ua-identity-rest.md)
