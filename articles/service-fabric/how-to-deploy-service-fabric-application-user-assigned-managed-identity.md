---
title: Azure Service Fabric – nasazení aplikace pomocí spravované identity přiřazené uživatelem | Microsoft Docs
description: V tomto článku se dozvíte, jak nasadit aplikaci Service Fabric s uživatelem přiřazenou spravovanou identitou.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: f8dfaa39f02aefbdda1f34afa5011ce5fadbae49
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624916"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Nasazení aplikace Service Fabric s uživatelem přiřazenou spravovanou identitou (Preview)

Chcete-li nasadit aplikaci Service Fabric se spravovanou identitou, musí být aplikace nasazena prostřednictvím Azure Resource Manager, obvykle se šablonou Azure Resource Manager. Další informace o tom, jak nasadit aplikaci Service Fabric prostřednictvím Azure Resource Manager, najdete v tématu [Správa aplikací a služeb jako Azure Resource Manager prostředků](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Aplikace, které nejsou nasazené jako prostředek Azure , nemůžou mít spravované identity. 
>
> Nasazení aplikace Service Fabric se spravovanou identitou podporuje verze `"2019-06-01-preview"`rozhraní API. Můžete použít také stejnou verzi rozhraní API pro typ aplikace, verzi typu aplikace a prostředky služby.
>

## <a name="user-assigned-identity"></a>Identita přiřazená uživatelem

Chcete-li povolit aplikaci s uživatelem přiřazenou identitou, přidejte do prostředku aplikace vlastnost **identity** s typem **userAssigned** a odkazovanými identitami přiřazenými uživatelem. Pak přidejte část **managedIdentities** do oddílu **Properties (vlastnosti** ) prostředku **aplikace** , který obsahuje seznam popisného názvu pro principalId mapování pro každou identitu přiřazenou uživateli.

### <a name="application-template"></a>Šablona aplikace

Chcete-li povolit aplikaci s identitou přiřazenou uživateli, přidejte do prostředku aplikace vlastnost **identity** s typem **userAssigned** a odkazovanými identitami přiřazenými uživatelem a přidejte objekt **managedIdentities** dovnitř  **oddíl vlastností** , který obsahuje seznam popisného názvu pro principalId mapování pro každou identitu přiřazenou uživatelem.

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

V předchozím příkladu se jako popisný název spravované identity aplikace používá název prostředku přiřazené identitě uživatele. V následujících příkladech se předpokládá, že skutečný popisný název je "AdminUser".

### <a name="application-package"></a>Balíček aplikace

1. Pro každou identitu definovanou v `managedIdentities` části šablony Azure Resource Manager `<ManagedIdentity>` přidejte značku v manifestu aplikace v části **objekty zabezpečení** . Atribut musí odpovídat vlastnosti definované v `managedIdentities`oddílu. `name` `Name`

    **Souboru ApplicationManifest. XML**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. V části **ServiceManifestImport** přidejte **IdentityBindingPolicy** pro službu, která používá spravovanou identitu. Tato zásada mapuje `AdminUser` identitu na název identity specifický pro službu, kterou je třeba přidat do manifestu služby později.

    **Souboru ApplicationManifest. XML**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Aktualizujte manifest služby, aby přidal **ManagedIdentity** do části Resources ( **prostředky** ) s `ServiceIdentityRef` názvem, `IdentityBindingPolicy` který odpovídá názvu v manifestu aplikace:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Další postup

* [Použití spravované identity v Service Fabric kódu aplikace](how-to-managed-identity-service-fabric-app-code.md)
* [Postup udělení přístupu aplikace Service Fabric k jiným prostředkům Azure](how-to-grant-access-other-resources.md)