---
title: Konfigurace spravovaných identit na virtuálním počítači Azure pomocí REST – Azure AD
description: Podrobné pokyny pro konfiguraci systémových a uživatelem přiřazených identit na virtuálním počítači Azure pomocí technologie KUDRLINKOU k provádění REST API volání.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b74e7d312133c24daad448e029a3c3d4cbdce79
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773060"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí volání REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou systému v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se pomocí objektu KUDRLINKOU pro volání Azure Resource Manager koncového bodu REST naučíte provádět následující spravované identity pro operace prostředků Azure na virtuálním počítači Azure:

- Povolení a zakázání spravované identity přiřazené systémem na virtuálním počítači Azure
- Přidání a odebrání spravované identity přiřazené uživatelem na virtuálním počítači Azure

Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si téma [co jsou spravované identity pro prostředky Azure?](overview.md). Další informace o spravovaných typech identity přiřazených systémem a uživatelem definovaných uživatelů najdete v tématu [spravované identity typu](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

V této části se dozvíte, jak povolit a zakázat spravovanou identitu přiřazenou systémem na virtuálním počítači Azure pomocí technologie KUDRLINKOU k volání Azure Resource Manager koncového bodu REST.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Povolit spravovanou identitu přiřazenou systémem během vytváření virtuálního počítače Azure

Aby bylo možné vytvořit virtuální počítač Azure s povolenou spravovanou identitou přiřazenou systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Pomocí příkazu [az group create](/cli/azure/group/#az_group_create) vytvořte [skupinu prostředků](../../azure-resource-manager/management/overview.md#terminology) pro nasazení a uchování virtuálního počítače a souvisejících prostředků. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Vytvoření [síťového rozhraní](/cli/azure/network/nic#az_network_nic_create) pro virtuální počítač:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Pomocí Azure Cloud Shell vytvořte virtuální počítač pomocí metody KUDRLINKOU pro volání koncového bodu Azure Resource Manager REST. Následující příklad vytvoří virtuální počítač s názvem *myVM* se spravovanou identitou přiřazenou systémem, jak je uvedeno v těle žádosti podle hodnoty `"identity":{"type":"SystemAssigned"}` . Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste požádali o přístupový token nosiče a hodnotu, která je `<SUBSCRIPTION ID>` pro vaše prostředí vhodná.

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
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 
   
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

Aby se povolila spravovaná identita přiřazená systémem na virtuálním počítači, který se původně zřídil bez něho, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Pomocí následujícího příkazu KUDRLINKOU zavolejte Azure Resource Manager koncový bod REST, aby se na VIRTUÁLNÍm počítači povolila spravovaná identita přiřazená systémem, jak je identifikované v těle požadavku hodnotou `{"identity":{"type":"SystemAssigned"}` pro virtuální počítač s názvem *myVM*.  Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste požádali o přístupový token nosiče a hodnotu, která je `<SUBSCRIPTION ID>` pro vaše prostředí vhodná.
   
   > [!IMPORTANT]
   > Aby se zajistilo, že neodstraníte žádné existující spravované identity přiřazené uživatelem, které jsou přiřazené k virtuálnímu počítači, musíte pomocí tohoto příkazu SLOŽENÉho příkazu Zobrazit seznam spravovaných identit přiřazených uživateli: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Pokud máte k virtuálnímu počítači přiřazené spravované identity přiřazené uživatelem, jak je identifikuje `identity` hodnota v odpovědi, přejděte ke kroku 3, který vám ukáže, jak uchovávat spravované identity přiřazené uživatelem a zároveň na svém virtuálním počítači povolit spravovanou identitu přiřazenou systémem.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 
   
   **Text žádosti**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Pokud chcete povolit spravovanou identitu přiřazenou systémem na virtuálním počítači s existujícími spravovanými identitami přiřazenými uživatelem, musíte `SystemAssigned` do této `type` hodnoty přidat.  
   
   Pokud například váš virtuální počítač obsahuje spravované identity přiřazené uživateli `ID1` a `ID2` k tomuto virtuálnímu počítači chcete přidat spravovanou identitu přiřazenou systémem, použijte následující volání kudrlinkou. `<ACCESS TOKEN>`Hodnoty a nahraďte `<SUBSCRIPTION ID>` hodnotami, které jsou vhodné pro vaše prostředí.

   Verze rozhraní API `2018-06-01` ukládá uživatelem přiřazené identity do `userAssignedIdentities` hodnoty ve formátu slovníku na rozdíl od `identityIds` hodnoty ve formátu pole používaném ve verzi rozhraní API `2017-12-01` .
   
   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

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

Aby se na virtuálním počítači zakázala spravovaná identita přiřazená systémem, váš účet potřebuje přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualizujte virtuální počítač pomocí metody KUDRLINKOU a zavolejte tak Azure Resource Manager koncový bod REST, aby se zakázala spravovaná identita přiřazená systémem.  Následující příklad zakáže spravovanou identitu přiřazenou systémem, která je v těle žádosti identifikovaná hodnotou `{"identity":{"type":"None"}}` z virtuálního počítače s názvem *myVM*.  Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste požádali o přístupový token nosiče a hodnotu, která je `<SUBSCRIPTION ID>` pro vaše prostředí vhodná.

   > [!IMPORTANT]
   > Aby se zajistilo, že neodstraníte žádné existující spravované identity přiřazené uživatelem, které jsou přiřazené k virtuálnímu počítači, musíte pomocí tohoto příkazu SLOŽENÉho příkazu Zobrazit seznam spravovaných identit přiřazených uživateli: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Pokud máte k virtuálnímu počítači přiřazené žádné spravované identity přiřazené uživatelem, jak je uvedeno v `identity` hodnotě odpovědi, přejděte ke kroku 3, který vám ukáže, jak zachovat uživatelem přiřazené spravované identity a zároveň na svém virtuálním počítači zablokovat spravovanou identitu přiřazenou systémem.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

   **Text žádosti**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Pokud chcete odebrat spravovanou identitu přiřazenou systémem z virtuálního počítače, který má uživatelsky přiřazené spravované identity, odeberte `SystemAssigned` `{"identity":{"type:" "}}` hodnotu z hodnoty a zachová hodnoty slovníku, `UserAssigned` `userAssignedIdentities` Pokud používáte **rozhraní API verze 2018-06-01**. Pokud používáte **rozhraní API verze 2017-12-01** nebo starší, nechejte `identityIds` pole.

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

V této části se dozvíte, jak přidat a odebrat spravovanou identitu přiřazenou uživatelem na virtuálním počítači Azure pomocí objektu KUDRLINKOU pro volání Azure Resource Manager koncového bodu REST.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Přiřazení spravované identity přiřazené uživatelem během vytváření virtuálního počítače Azure

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

1. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Vytvoření [síťového rozhraní](/cli/azure/network/nic#az_network_nic_create) pro virtuální počítač:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Načte přístupový token nosiče, který použijete v dalším kroku v autorizační hlavičce k vytvoření virtuálního počítače pomocí spravované identity přiřazené systémem.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Pomocí pokynů, které najdete tady, vytvořte uživatelem přiřazenou spravovanou identitu: [vytvořte spravovanou identitu přiřazenou uživatelem](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Vytvořte virtuální počítač pomocí metody KUDRLINKOU pro volání Azure Resource Manager koncového bodu REST. Následující příklad vytvoří virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup* s uživatelem přiřazenou spravovanou identitou `ID1` , jak je uvedeno v těle žádosti podle hodnoty `"identity":{"type":"UserAssigned"}` . Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste požádali o přístupový token nosiče a hodnotu, která je `<SUBSCRIPTION ID>` pro vaše prostředí vhodná.
 
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
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

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
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

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

K přiřazení uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači potřebuje váš účet [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a přiřazení rolí [spravovaného operátoru identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) . Nevyžadují se žádné další přiřazení role adresáře Azure AD.

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
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.

    Pokud máte ke svému virtuálnímu počítači přiřazenou nějaké spravované identity přiřazené uživatelem nebo systémem, které jsou identifikované v `identity` hodnotě odpovědi, přejděte na krok 5, který vám ukáže, jak zachovat spravovanou identitu přiřazenou systémem při přidávání uživatelem přiřazené spravované identity na virtuálním počítači.

4. Pokud nemáte ke svému VIRTUÁLNÍmu počítači přiřazené žádné spravované identity přiřazené uživatelem, použijte následující příkaz, který zavolá Azure Resource Manager koncový bod REST a přiřadí k virtuálnímu počítači první spravovanou identitu přiřazenou uživatelem.

   V následujícím příkladu se přiřadí spravovaná identita přiřazená uživateli `ID1` k virtuálnímu počítači s názvem *myVM* ve skupině prostředků *myResourceGroup*.  Nahraďte `<ACCESS TOKEN>` hodnotou, kterou jste obdrželi v předchozím kroku, pokud jste požádali o přístupový token nosiče a hodnotu, která je `<SUBSCRIPTION ID>` pro vaše prostředí vhodná.

   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        |
 
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

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

   Přidejte spravovanou identitu přiřazenou uživatelem do `userAssignedIdentities` hodnoty Dictionary.
    
   Například pokud máte spravovanou identitu přiřazenou systémem a aktuálně přiřazenou spravovanou identitu přiřazenou `ID1` k vašemu virtuálnímu počítači, do které chcete přidat spravovanou identitu přiřazenou uživatelem `ID2` :

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

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

   Zachovejte uživatelsky přiřazené spravované identity, které byste chtěli zachovat v `identityIds` hodnotě pole při přidávání nové spravované identity přiřazené uživatelem.

   Například pokud máte spravovanou identitu přiřazenou systémem a aktuálně přiřazenou spravovanou identitu přiřazenou `ID1` k vašemu virtuálnímu počítači, do které chcete přidat spravovanou identitu přiřazenou uživatelem `ID2` : 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

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

K odebrání uživatelsky přiřazené identity k VIRTUÁLNÍmu počítači vyžaduje váš účet přiřazení role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .

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
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.
 
   Pokud máte k virtuálnímu počítači přiřazené spravované identity, jsou uvedené v odpovědi v `identity` hodnotě.

   Například pokud máte uživatelem přiřazené spravované identity `ID1` a máte `ID2` přiřazený k vašemu virtuálnímu počítači a chcete zachovat `ID1` přiřazenou identitu systému a zachovat ji:
   
   **ROZHRANÍ API VERZE 2018-06-01**

   Přidejte `null` do uživatelem přiřazené spravované identity, kterou chcete odebrat:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

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

   Zachovejte pouze uživatelsky přiřazené spravované identity, které chcete zachovat v `identityIds` poli:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky požadavku**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        | 

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
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Hlavičky požadavku**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token. | 

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
|*Autorizace*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.| 

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
