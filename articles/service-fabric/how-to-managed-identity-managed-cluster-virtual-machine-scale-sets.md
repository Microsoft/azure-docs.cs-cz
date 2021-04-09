---
title: Přidání spravované identity do typu spravovaného uzlu clusteru Service Fabric (Preview)
description: V tomto článku se dozvíte, jak přidat spravovanou identitu do typu spravovaného uzlu clusteru Service Fabric.
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3ff5d66160ddbb037469378634826fd9eeae0c54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651642"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Přidání spravované identity do typu spravovaného uzlu clusteru Service Fabric (Preview)

Každý typ uzlu v Service Fabric spravovaném clusteru je zálohovaný pomocí sady škálování virtuálního počítače. Pokud chcete, aby se spravované identity používaly s typem uzlu spravovaného clusteru, `vmManagedIdentity` přidala se do definic typů uzlů, které obsahují seznam identit, které se dají použít, přidat vlastnost `userAssignedIdentities` . Funkce zrcadlí, jak se spravované identity dají použít v nespravovaných clusterech, jako je například použití spravované identity s [rozšířením Azure Key Vault Virtual Machine Scale set](../virtual-machines/extensions/key-vault-windows.md).


Příklad nasazení spravovaného clusteru Service Fabric, který využívá spravovanou identitu pro typ uzlu, najdete v [této šabloně](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). Seznam podporovaných oblastí najdete v tématu [Nejčastější dotazy ke spravovanému clusteru](./faq-managed-cluster.md#what-regions-are-supported-in-the-preview).

> [!NOTE]
> Pro tuto funkci se aktuálně podporují jenom uživatelsky přiřazené identity.

## <a name="prerequisites"></a>Požadavky

Než začnete:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Pokud máte v úmyslu používat PowerShell, [nainstalujte](/cli/azure/install-azure-cli) rozhraní příkazového řádku Azure CLI, ve kterém se budou spouštět příkazy odkazů CLI.

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

Uživatelem přiřazená identita může být definována v části Resources (prostředky) šablony Azure Resource Manager (ARM) pro vytváření při nasazení:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

nebo vytvořené prostřednictvím PowerShellu:

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Přidání přiřazení role s Service Fabric poskytovatele prostředků

Přidejte přiřazení role ke spravované identitě pomocí Service Fabric aplikace poskytovatele prostředků. Toto přiřazení umožňuje poskytovateli prostředků Service Fabric přiřadit identitu k sadě škálování virtuálních počítačů spravovaného clusteru. 

V případě potřeby je nutné použít následující hodnoty:

|Name|Odpovídající hodnota poskytovatele prostředků Service Fabric|
|----|-------------------------------------|
|ID aplikace|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|ID objektu|fbc587f2-66f5-4459-a027-bcd908b9d278|


|Název definice role|ID definice role|
|----|-------------------------------------|
|Spravovaný operátor identity|f1a07417-d97a-45cb-824c-7a7467783830
|



Toto přiřazení role lze definovat v oddílu Resources pomocí ID objektu a ID definice role:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

nebo vytvořená přes PowerShell pomocí ID aplikace a ID definice role:

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

nebo ID objektu a ID definice role:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>Přidání vlastností spravované identity do definice typu uzlu

Nakonec přidejte `vmManagedIdentity` `userAssignedIdentities` vlastnosti a do definice typu uzlu spravovaného clusteru. Nezapomeňte pro použít verzi **2021-01-01-Preview** nebo novější `apiVersion` .

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-01-01-preview",
    ...
    "properties": {
        "isPrimary" : true,
        "vmInstanceCount": 5,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2_v2",
        "vmImagePublisher" : "MicrosoftWindowsServer",
        "vmImageOffer" : "WindowsServer",
        "vmImageSku" : "2019-Datacenter",
        "vmImageVersion" : "latest",
        "vmManagedIdentity": {
            "userAssignedIdentities": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
            ]
        }
    }
}
```

Po nasazení se vytvořená spravovaná identita přidala do sady škálování virtuálního počítače typu určeného uzlu a dá se použít podle očekávání, stejně jako v jakémkoli nespravovaném clusteru.

## <a name="troubleshooting"></a>Řešení potíží

V případě nesprávného přidání přiřazení role bude při nasazení splněna následující chyba:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Chyba nasazení Azure Portal zobrazení klienta s ID objektu nebo aplikace SFRP nemá oprávnění k provedení aktivity správy identit":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení aplikace do spravovaného clusteru Service Fabric](./tutorial-managed-cluster-deploy-app.md)