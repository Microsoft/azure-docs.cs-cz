---
title: Nasazení aplikace pomocí spravované identity přiřazené uživatelem
description: V tomto článku se dozvíte, jak nasadit aplikaci Service Fabric s uživatelem přiřazenou spravovanou identitou.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 79d8654733b580be96d59e78f31105077929ac78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260077"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Nasazení aplikace Service Fabric se spravovanou identitou User-Assigned

Chcete-li nasadit aplikaci Service Fabric se spravovanou identitou, musí být aplikace nasazena prostřednictvím Azure Resource Manager, obvykle se šablonou Azure Resource Manager. Další informace o tom, jak nasadit aplikaci Service Fabric prostřednictvím Azure Resource Manager, najdete v tématu [Správa aplikací a služeb jako Azure Resource Manager prostředků](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Aplikace, které nejsou nasazené jako prostředek Azure, **nemůžou** mít spravované identity. 
>
> Nasazení aplikace Service Fabric se spravovanou identitou podporuje verze rozhraní API `"2019-06-01-preview"` . Můžete použít také stejnou verzi rozhraní API pro typ aplikace, verzi typu aplikace a prostředky služby.
>

## <a name="user-assigned-identity"></a>User-Assigned identity

Chcete-li povolit aplikaci s identitou User-Assigned, přidejte nejprve do prostředku aplikace vlastnost **identity** typu **userAssigned** a odkazované uživatelem přiřazené identity. Pak přidejte část **managedIdentities** do oddílu **Properties (vlastnosti** ) prostředku **aplikace** , který obsahuje seznam popisného názvu pro principalId mapování pro každou identitu přiřazenou uživateli. Další informace o identitách přiřazených uživateli najdete v tématu [Vytvoření, vypsání nebo odstranění spravované identity přiřazené uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

### <a name="application-template"></a>Šablona aplikace

Pokud chcete povolit aplikaci s identitou přiřazenou uživateli, nejdřív přidejte do prostředku aplikace vlastnost **identity** s typem **userAssigned** a odkazovanými identitami přiřazenými uživatelem a pak přidejte objekt **managedIdentities** do oddílu **Properties (vlastnosti** ), který obsahuje seznam popisného názvu pro principalId mapování pro každou identitu přiřazenou uživatelem.

```json
{
  "apiVersion": "2019-06-01-preview",
  "type": "Microsoft.ServiceFabric/clusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "typeName": "[parameters('applicationTypeName')]",
    "typeVersion": "[parameters('applicationTypeVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

V předchozím příkladu se jako popisný název spravované identity aplikace používá název prostředku přiřazené identitě uživatele. V následujících příkladech se předpokládá, že skutečný popisný název je "AdminUser".

### <a name="application-package"></a>Balíček aplikace

1. Pro každou identitu definovanou v `managedIdentities` části šablony Azure Resource Manager přidejte `<ManagedIdentity>` značku v manifestu aplikace v části **objekty zabezpečení** . `Name`Atribut musí odpovídat `name` vlastnosti definované v `managedIdentities` oddílu.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. V části **ServiceManifestImport** přidejte **IdentityBindingPolicy** pro službu, která používá spravovanou identitu. Tato zásada mapuje `AdminUser` identitu na název identity specifický pro službu, kterou je třeba přidat do manifestu služby později.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Aktualizujte manifest služby, aby přidal **ManagedIdentity** do části **Resources (prostředky** ) s názvem, který odpovídá názvu `ServiceIdentityRef` v `IdentityBindingPolicy` manifestu aplikace:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Další kroky

* [Použití spravované identity v Service Fabric kódu aplikace](how-to-managed-identity-service-fabric-app-code.md)
* [Postup udělení přístupu aplikace Service Fabric k jiným prostředkům Azure](how-to-grant-access-other-resources.md)
