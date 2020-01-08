---
title: Konfigurace spravovaných identit na virtuálním počítači Azure pomocí REST – Azure AD
description: Podrobné pokyny pro konfiguraci systémových a uživatelem přiřazených identit na virtuálním počítači Azure pomocí objektu KUDRLINKOU pro REST API volání.
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
ms.openlocfilehash: 9f975595e935a5c0254450168aa295e6e7366a94
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430003"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí volání REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou systému v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se pomocí objektu KUDRLINKOU pro volání Azure Resource Manager koncového bodu REST naučíte provádět následující spravované identity pro operace prostředků Azure na virtuálním počítači Azure:

- Povolení a zakázání spravované identity přiřazené systémem na virtuálním počítači Azure
- Přidání a odebrání spravované identity přiřazené uživatelem na virtuálním počítači Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud používáte systém Windows, nainstalujte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) v Azure Portal.
- Pokud používáte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo systém pro [distribuci Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [nainstalujte místní konzolu Azure CLI](/cli/azure/install-azure-cli).
- Pokud používáte místní konzolu Azure CLI, přihlaste se k Azure pomocí `az login` s účtem, který je přidružený k předplatnému Azure. chcete spravovat spravované identity v systému nebo uživateli.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravovanou identitu přiřazenou systémem na virtuálním počítači Azure pomocí technologie KUDRLINKOU k volání Azure Resource Manager koncového bodu REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření virtuálního počítače Azure

Aby bylo možné vytvořit virtuální počítač Azure s povolenou spravovanou identitou přiřazenou systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Pomocí příkazu [az group create](/cli/azure/group/#az-group-create) vytvořte [skupinu prostředků](../../azure-resource-manager/management/overview.md#terminology) pro nasazení a uchování virtuálního počítače a souvisejících prostředků. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Vytvoření [síťového rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pro virtuální počítač:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvořte virtuální počítač pomocí metody KUDRLINKOU pro volání Azure Resource Manager koncového bodu REST. Následující příklad vytvoří virtuální počítač s názvem *myVM* se spravovanou identitou přiřazenou systémem, jak je uvedeno v těle žádosti hodnotou `"identity":{"type":"SystemAssigned"}`. Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Hlavičky požadavku**
   
   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 
   
   **Text žádosti**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Povolení identity přiřazené systémem na stávajícím virtuálním počítači Azure

Aby se povolila spravovaná identita přiřazená systémem na virtuálním počítači, který se původně zřídil bez něho, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Pomocí následujícího příkazu KUDRLINKOU zavolejte Azure Resource Manager koncový bod REST, aby se na VIRTUÁLNÍm počítači povolila spravovaná identita přiřazená systémem, jak je identifikované v těle žádosti `{"identity":{"type":"SystemAssigned"}` pro virtuální počítač s názvem *myVM*.  Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.
   
   > [!IMPORTANT]
   > Aby se zajistilo, že neodstraníte žádné existující spravované identity přiřazené uživatelem, které jsou přiřazené k virtuálnímu počítači, musíte pomocí tohoto příkazu SLOŽENÉho příkazu Zobrazit seznam spravovaných identit přiřazených uživateli: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte k virtuálnímu počítači přiřazené nějaké spravované identity přiřazené uživatelem, jak je identifikované v odpovědi na hodnotu `identity`, přeskočte na krok 3, který vám ukáže, jak uchovávat spravované identity přiřazené uživatelem a zároveň na svém VIRTUÁLNÍm počítači povolit spravovanou identitu přiřazenou systémem.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 
   
   **Text žádosti**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Pokud chcete povolit spravovanou identitu přiřazenou systémem na virtuálním počítači s existujícími spravovanými identitami přiřazenými uživatelem, musíte do `type` hodnoty přidat `SystemAssigned`.  
   
   Pokud například váš virtuální počítač obsahuje spravované identity přiřazené uživatelem `ID1` a `ID2` k tomuto virtuálnímu počítači, a chcete do virtuálního počítače přidat spravovanou identitu přiřazenou systémem, použijte následující volání KUDRLINKOU. Nahraďte `<ACCESS TOKEN>` a `<SUBSCRIPTION ID>` hodnotami, které jsou vhodné pro vaše prostředí.

   Verze rozhraní API `2018-06-01` ukládá uživatelem přiřazené identity ve formátu slovníku `userAssignedIdentities` ve formátu slovníku na rozdíl od `identityIds` hodnoty ve formátu pole používaném v rozhraní API verze `2017-12-01`.
   
   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

   **Text žádosti**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **ROZHRANÍ API VERZE 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

   **Text žádosti**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Zakázat spravovanou identitu přiřazenou systémem z virtuálního počítače Azure

Aby se na virtuálním počítači zakázala spravovaná identita přiřazená systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualizujte virtuální počítač pomocí metody KUDRLINKOU a zavolejte tak Azure Resource Manager koncový bod REST, aby se zakázala spravovaná identita přiřazená systémem.  Následující příklad zakáže spravovanou identitu přiřazenou systémem, která je označená v těle žádosti, hodnotou `{"identity":{"type":"None"}}` z virtuálního počítače s názvem *myVM*.  Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.

   > [!IMPORTANT]
   > Aby se zajistilo, že neodstraníte žádné existující spravované identity přiřazené uživatelem, které jsou přiřazené k virtuálnímu počítači, musíte pomocí tohoto příkazu SLOŽENÉho příkazu Zobrazit seznam spravovaných identit přiřazených uživateli: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte k virtuálnímu počítači přiřazené nějaké spravované identity přiřazené uživatelem, jak je identifikované v odpovědi na hodnotu `identity`, přeskočte na krok 3, který vám ukáže, jak zachovat uživatelem přiřazené spravované identity a zároveň na svém VIRTUÁLNÍm počítači zablokovat spravovanou identitu přiřazenou systémem.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

   **Text žádosti**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Pokud chcete odebrat spravovanou identitu přiřazenou systémem z virtuálního počítače, který má uživatelsky přiřazené spravované identity, odeberte `SystemAssigned` z `{"identity":{"type:" "}}` hodnoty a zachováte `UserAssigned` hodnotu a hodnoty `userAssignedIdentities` slovníku, pokud používáte **rozhraní API verze 2018-06-01**. Pokud používáte **rozhraní API verze 2017-12-01** nebo starší, nechejte pole `identityIds`.

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat spravovanou identitu přiřazenou uživatelem na virtuálním počítači Azure pomocí objektu KUDRLINKOU pro volání Azure Resource Manager koncového bodu REST.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Přiřazení spravované identity přiřazené uživatelem během vytváření virtuálního počítače Azure

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Vytvoření [síťového rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pro virtuální počítač:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Pomocí pokynů, které najdete tady, vytvořte uživatelem přiřazenou spravovanou identitu: [vytvořte spravovanou identitu přiřazenou uživatelem](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Vytvořte virtuální počítač pomocí metody KUDRLINKOU pro volání Azure Resource Manager koncového bodu REST. Následující příklad vytvoří virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup* s uživatelem přiřazenou spravovanou identitou `ID1`, jak je uvedeno v těle žádosti hodnotou `"identity":{"type":"UserAssigned"}`. Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.
 
   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

   **Text žádosti**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **ROZHRANÍ API VERZE 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

   **Text žádosti**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
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
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Přiřazení spravované identity přiřazené uživateli k existujícímu virtuálnímu počítači Azure

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Pomocí pokynů, které najdete tady, vytvořte uživatelem přiřazenou spravovanou identitu a [vytvořte spravovanou identitu přiřazenou uživatelem](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. K tomu, abyste se ujistili, že neodstraňujete stávající spravované identity uživatelů nebo systémem přiřazené k virtuálnímu počítači, musíte zobrazit seznam typů identit přiřazených k virtuálnímu počítači pomocí následujícího příkazu. Pokud máte spravované identity přiřazené k sadě škálování virtuálních počítačů, jsou uvedené v části v poli `identity` hodnota.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.

    Pokud máte ke svému virtuálnímu počítači přiřazenou nějaké spravované identity přiřazené uživateli nebo systémem, jak je identifikované v odpovědi na hodnotu `identity`, přeskočte na krok 5, který vám ukáže, jak zachovat spravovanou identitu přiřazenou systémem při přidávání uživatelsky přiřazené spravované identity na VIRTUÁLNÍm počítači.

4. Pokud nemáte ke svému VIRTUÁLNÍmu počítači přiřazené žádné spravované identity přiřazené uživatelem, použijte následující příkaz, který zavolá Azure Resource Manager koncový bod REST a přiřadí k virtuálnímu počítači první spravovanou identitu přiřazenou uživatelem.

   Následující příklady přiřadí spravovanou identitu přiřazenou uživatelem `ID1` k virtuálnímu počítači s názvem *myVM* ve skupině prostředků *myResourceGroup*.  Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste si vyžádali přístupový token nosiče a hodnotu `<SUBSCRIPTION ID>`, jak je to vhodné pro vaše prostředí.

   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        |
 
   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **ROZHRANÍ API VERZE 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

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

5. Pokud máte ke svému VIRTUÁLNÍmu počítači přiřazenou stávající spravovanou identitu přiřazenou uživatelem nebo systémem:
   
   **ROZHRANÍ API VERZE 2018-06-01**

   Přidejte spravovanou identitu přiřazenou uživatelem do hodnoty `userAssignedIdentities` slovníku.
    
   Pokud máte například spravovanou identitu přiřazenou systémem a uživatelem přiřazenou spravovanou identitu `ID1` aktuálně přiřazenou k vašemu VIRTUÁLNÍmu počítači a chcete do ní přidat spravovanou identitu přiřazenou uživatelem, `ID2`:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

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

   Pokud máte například spravovanou identitu přiřazenou systémem a uživatelem přiřazenou spravovanou identitu `ID1` aktuálně přiřazenou k vašemu VIRTUÁLNÍmu počítači a chcete do ní přidat spravovanou identitu přiřazenou uživatelem, `ID2`: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání spravované identity přiřazené uživatelem z virtuálního počítače Azure

K odebrání uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači vyžaduje váš účet přiřazení role [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) .

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Abyste se ujistili, že neodstraníte žádné existující spravované identity přiřazené uživatelem, které byste chtěli zachovat přiřazený virtuálnímu počítači nebo odebrat spravovanou identitu přiřazenou systémem, musíte vypsat spravované identity pomocí následujícího příkazu SLOŽENÉho příkazu: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.
 
   Pokud máte přiřazené spravované identity k virtuálnímu počítači, zobrazí se v odpovědi v hodnotě `identity`.

   Například pokud máte uživatelem přiřazené spravované identity `ID1` a `ID2` k vašemu VIRTUÁLNÍmu počítači, a chcete zachovat `ID1` přiřazenou a zachovat identitu přiřazenou systémem:
   
   **ROZHRANÍ API VERZE 2018-06-01**

   Přidejte `null` k uživatelem přiřazené spravované identitě, kterou chcete odebrat:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.        | 

   **Text žádosti**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Pokud má váš virtuální počítač spravované identity přiřazené systémem i uživatelem, můžete odebrat všechny spravované identity přiřazené uživatelem tak, že přepnete na použití spravované identity přiřazené systémem, a to pomocí následujícího příkazu:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
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
    
Pokud má váš virtuální počítač jenom spravované identity přiřazené uživatelem a Vy byste ho chtěli odebrat, použijte následující příkaz:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Hlavičky požadavku**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastavte na platný přístupový token `Bearer`.| 

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

- [Vytvoření, vypsání nebo odstranění spravovaných identit přiřazených uživatelem pomocí REST API volání](how-to-manage-ua-identity-rest.md)
