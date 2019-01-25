---
title: Postup konfigurace systému a uživatelsky přiřazené identity spravované na Virtuálním počítači Azure pomocí rozhraní REST
description: Podrobné pokyny ke konfiguraci systému a uživatelsky přiřazené identity spravované na Virtuálním počítači Azure pomocí příkazu CURL k zajištění REST API volá.
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
ms.date: 06/25/2018
ms.author: priyamo
ms.openlocfilehash: 0f24d153f47920cad15f7c26161a694d318c24d0
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884502"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí volání rozhraní REST API

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure s identitou systému automaticky spravované v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru, se dozvíte, jak k provádění následujících spravovaných identit pro prostředky Azure operace na Virtuálním počítači Azure:

- Povolení a zákaz systém přiřadil spravovaná identita na Virtuálním počítači Azure
- Přidání a odebrání uživatelsky přiřazené spravovanou identitu na Virtuálním počítači Azure

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud používáte Windows, nainstalujte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) na webu Azure Portal.
- [Instalace Azure CLI místní konzoly](/cli/azure/install-azure-cli), pokud použijete [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo [operačním systémem distribuce Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Pokud používáte místní konzoly příkazového řádku Azure, přihlaste se do Azure s využitím `az login` pomocí účtu, který je přidružený k předplatnému Azure chcete ke správě systému nebo uživatelsky přiřazené identity spravované.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Systém přiřadil spravované identity

V této části se dozvíte, jak můžete povolit nebo zakázat systém přiřadil spravovaná identita na Virtuálním počítači Azure pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Povolit systém přiřadil spravovanou identitu při vytváření virtuálního počítače Azure

Chcete-li vytvořit virtuální počítač Azure s využitím systém přiřadil spravovanou identitu povolené, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) přiřazení role.  Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

1. Pomocí příkazu [az group create](/cli/azure/group/#az-group-create) vytvořte [skupinu prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro nasazení a uchování virtuálního počítače a souvisejících prostředků. Pokud už máte skupinu prostředků, kterou chcete použít, můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Vytvoření [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pro váš virtuální počítač:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvoření virtuálního počítače pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru. Následující příklad vytvoří virtuální počítač s názvem *myVM* s systém přiřadil spravované identity, jako identifikované v textu požadavku hodnotu `"identity":{"type":"SystemAssigned"}`. Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Hlavičky žádosti**
   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 
   
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

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Povolit systém přiřadil identitu na existujícím virtuálním počítači Azure

Aby systém přiřadil spravovaná identita na virtuálním počítači, který byl původně zřízený bez ní, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) přiřazení role.  Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Použijte následující příkaz CURL k volání koncového bodu REST Azure Resource Manageru tak, aby povolovala systém přiřadil spravovaná identita na vašem virtuálním počítači identifikované v textu požadavku hodnotu `{"identity":{"type":"SystemAssigned"}` pro virtuální počítač s názvem *myVM*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
   
   > [!IMPORTANT]
   > Aby nechcete odstranit všechny existující uživatelsky přiřazené spravované identity, které jsou přiřazeny k virtuálnímu počítači, budete muset seznam uživatelsky přiřazené identity spravované pomocí tohoto příkazu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte přiřazená uživatelem spravované identity přiřazené k virtuálnímu počítači v jsme uvedli, `identity` hodnoty v odpovědi, přejděte ke kroku 3, který ukazuje, jak zachovat uživatelsky přiřazené identity spravované při povolování systém přiřadil spravovaná identita na vašem virtuálním počítači.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky žádosti**
   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 
   
   **Text žádosti**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Pokud chcete povolit systém přiřadil spravovaná identita na virtuálním počítači s existující spravované uživatelsky přiřazené identity, je třeba přidat `SystemAssigned` k `type` hodnotu.  
   
   Například, pokud má virtuální počítač uživatelsky přiřazené identity spravované `ID1` a `ID2` přiřazené, a chcete přidat spravovanou identitu systém přiřadil k virtuálnímu počítači, použijte následující volání CURL. Nahraďte `<ACCESS TOKEN>` a `<SUBSCRIPTION ID>` s hodnotami, které jsou vhodné pro vaše prostředí.

   Verze rozhraní API `2018-06-01` ukládá spravované uživatelsky přiřazené identity v `userAssignedIdentities` hodnoty ve slovníku formátu, nikoli `identityIds` hodnotu ve formátu pole používá ve verzi rozhraní API `2017-12-01`.
   
   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 

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
    
   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 
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

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Zakázat spravovanou identitu systém přiřadil z virtuálního počítače Azure

Pokud chcete zakázat systém přiřadil spravovaná identita na virtuálním počítači, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) přiřazení role.  Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aktualizace virtuálního počítače pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru k zakázání systém přiřadil spravovaná identita.  Následující příklad zakazuje systém přiřadil spravovanou identitu, která jsou identifikované v textu požadavku hodnotu `{"identity":{"type":"None"}}` z virtuálního počítače s názvem *myVM*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   > [!IMPORTANT]
   > Aby nechcete odstranit všechny existující uživatelsky přiřazené spravované identity, které jsou přiřazeny k virtuálnímu počítači, budete muset seznam uživatelsky přiřazené identity spravované pomocí tohoto příkazu CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Pokud máte přiřazená uživatelem spravované identity přiřazené k virtuálnímu počítači v jsme uvedli, `identity` hodnoty v odpovědi, přejděte ke kroku 3, který ukazuje, jak zachovat uživatelsky přiřazené identity spravované při zakazování systém přiřadil spravovaná identita na vašem virtuálním počítači.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 

   **Text žádosti**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Odebrat z virtuálního počítače, který má uživatelsky přiřazené identity spravované systém přiřadil spravovanou identitu, odeberte `SystemAssigned` z `{"identity":{"type:" "}}` hodnotu při zachování `UserAssigned` hodnotu a `userAssignedIdentities` slovník hodnot, pokud používáte **Rozhraní API verze 2018-06-01**. Pokud používáte **rozhraní API verze 2017-12-01** nebo starší, nechat `identityIds` pole.

## <a name="user-assigned-managed-identity"></a>Uživatel přiřazenou spravované identity

V této části se dozvíte, jak přidávat a odebírat uživatele přiřazeny spravovaná identita na Virtuálním počítači Azure pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Přiřadit uživateli přiřazena spravovanou identitu při vytváření virtuálního počítače Azure

K virtuálnímu počítači přiřadit uživatelsky přiřazené identity, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [operátor spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-operator) přiřazení rolí. Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Vytvoření [síťové rozhraní](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) pro váš virtuální počítač:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Vytvoření uživatelsky přiřazené spravovanou identitu pomocí pokynů uvedených zde: [Vytvoření uživatelsky přiřazené identity spravované](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Vytvoření virtuálního počítače pomocí příkazu CURL k volání koncového bodu REST Azure Resource Manageru. Následující příklad vytvoří virtuální počítač s názvem *myVM* ve skupině prostředků *myResourceGroup* pomocí spravované identity přiřazené uživateli `ID1`, jak identifikované v textu požadavku hodnotu `"identity":{"type":"UserAssigned"}`. Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.
 
   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 

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

   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Spravované identity přiřazené uživateli přiřadit existující virtuální počítač Azure

K virtuálnímu počítači přiřadit uživatelsky přiřazené identity, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) a [operátor spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-operator) přiřazení rolí. Žádné další Azure AD přiřazení rolí adresáře se vyžadují.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Vytvoření uživatelsky přiřazené spravovanou identitu pomocí pokynů uvedených zde [vytvoření uživatelsky přiřazené identity spravované](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Zajistěte, aby že neprovedete odstranění existujícího uživatele nebo systém přiřadil spravovaných identit, které jsou přiřazeny k virtuálnímu počítači, budete muset seznam typů identity přiřazené k virtuálnímu počítači s použitím následující příkaz CURL. Pokud máte spravované identity přiřazené do škálovací sady virtuálního počítače, jsou uvedeny v části v `identity` hodnotu.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky žádosti**
   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.

    Pokud máte všechny uživatele nebo systém přiřadil spravované identity přiřazené k virtuálnímu počítači v jsme uvedli `identity` hodnota v odpovědi, přejděte ke kroku 5, který ukazuje, jak zachovat p, systém přiřadil spravovanou identitu při přidávání uživatelsky přiřazené spravovanou identitu na váš virtuální počítač.

4. Pokud nemáte k dispozici žádné uživatelsky přiřazené spravované identity přiřazené k vašemu virtuálnímu počítači, použijte následující příkaz CURL k volání koncového bodu REST Azure Resource Manageru přiřadit první spravovaná identita uživatelsky přiřazené k virtuálnímu počítači.

   Následující příklady přiřadí uživateli přiřazena spravovanou identitu, `ID1` k virtuálnímu počítači s názvem *myVM* ve skupině prostředků *myResourceGroup*.  Nahraďte `<ACCESS TOKEN>` s hodnotou jste získali v předchozím kroku při vyžádání nosný token a `<SUBSCRIPTION ID>` hodnotu podle požadavků vašeho prostředí.

   **ROZHRANÍ API VERZE 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        |
 
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
   
   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 

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

5. Pokud máte existující uživatelsky přiřazené nebo systém přiřadil spravovaná identita přiřazené k vašemu virtuálnímu počítači:
   
   **ROZHRANÍ API VERZE 2018-06-01**

   Přidat spravovanou identitu pro uživatelsky přiřazené `userAssignedIdentities` hodnota slovníku.
    
   Například, pokud máte systém přiřadil spravovanou identitu a uživatelsky přiřazené spravovanou identitu `ID1` aktuálně přiřazené k vašemu virtuálnímu počítači a chcete přidat spravované uživatelsky přiřazené identity `ID2` do ní:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 

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

   Zachovat uživatelem přidělenou spravované identity byste chtěli zachovat v `identityIds` hodnota pole při přidávání nové uživatelsky přiřazené spravovaná identita.

   Například, pokud máte systém přiřadil spravovanou identitu a uživatelsky přiřazené spravovanou identitu `ID1` aktuálně přiřazené k vašemu virtuálnímu počítači a chcete přidat spravované uživatelsky přiřazené identity `ID2` do ní: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Odebrání virtuálního počítače Azure spravované identity přiřazené uživateli

Chcete-li odebrat uživatelsky přiřazené identity virtuálního počítače, musí váš účet [Přispěvatel virtuálních počítačů](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) přiřazení role.

1. Načtěte přístupový token nosiče, který použijete v dalším kroku v hlavičce autorizace k vytvoření virtuálního počítače s systém přiřadil spravovanou identitu.

   ```azurecli-interactive
   az account get-access-token
   ```

2. K zajištění, že není odstranit všechny existující uživatelsky přiřazené spravované identity, které chcete zachovat přiřazené k virtuálnímu počítači nebo odeberte systém přiřadil spravovanou identitu, je třeba seznam spravovaných identit pomocí příkazu CURL: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky žádosti**
   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.
 
   Pokud máte spravované identity přiřazené k virtuálnímu počítači, jsou uvedeny v odpovědi v `identity` hodnotu.

   Například, pokud máte uživatelsky přiřazené identity spravované `ID1` a `ID2` přiřazené k vašemu virtuálnímu počítači a chcete zachovat `ID1` přiřazeny a zachování identity systém přiřadil:
   
   **ROZHRANÍ API VERZE 2018-06-01**

   Přidat `null` uživatel přiřazenou spravovaných identit, které chcete odebrat:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 

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

   Zachovat jenom přiřazené uživatele identity(s) byste chtěli zachovat v spravovaných `identityIds` pole:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Hlavičky žádosti**

   |Hlavička požadavku  |Popis  |
   |---------|---------|
   |*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
   |*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.        | 

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

Pokud má virtuální počítač, jak systém přiřadil a uživatelsky přiřazené identity spravované, můžete odebrat všechny přiřazené uživatele spravované identity přepnutím používat jen systém přiřadil spravovanou identitu pomocí následujícího příkazu:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Hlavičky žádosti**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token. | 

**Text žádosti**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Pokud má virtuální počítač jenom přiřazené uživatele spravované identity a chcete odebrat je všechny, použijte následující příkaz:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Hlavičky žádosti**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Content-Type*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Autorizace*     | Povinná hodnota. Nastaven na platné `Bearer` přístupový token.| 

**Text žádosti**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Další postup

Informace o tom, jak vytvořit, vypsat nebo odstranit uživatelem přidělenou spravovaných identit pomocí rozhraní REST najdete tady:

- [Vytvoření seznamu nebo odstranit uživatelem přidělenou spravovaných identit, volání rozhraní REST API](how-to-manage-ua-identity-rest.md)
