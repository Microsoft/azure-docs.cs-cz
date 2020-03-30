---
title: Nasazení aplikace se spravovanou identitou přiřazenou uživatelem
description: Tento článek ukazuje, jak nasadit aplikaci Service Fabric s uživatelem přiřazenou spravovanou identitou
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: a5eeaf0d6420fa36c0a78f7553ddfd82197d8ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610331"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Nasazení aplikace Service Fabric s uživatelem přiřazenou spravovanou identitou (preview)

K nasazení aplikace Service Fabric se spravovanou identitou je potřeba aplikaci nasadit prostřednictvím Správce prostředků Azure, obvykle se šablonou Azure Resource Manager. Další informace o tom, jak nasadit aplikaci Service Fabric prostřednictvím Správce prostředků Azure, najdete v [tématu Správa aplikací a služeb jako prostředků Azure Resource Manager](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Aplikace, které nejsou nasazené jako prostředek **Azure, nemohou** mít spravované identity. 
>
> Nasazení aplikace Service Fabric se spravovanou `"2019-06-01-preview"`identitou je podporováno verzí rozhraní API . Můžete také použít stejnou verzi rozhraní API pro typ aplikace, verzi typu aplikace a prostředky služby.
>

## <a name="user-assigned-identity"></a>Identita přiřazená uživateli

Chcete-li povolit aplikaci s identitou přiřazenou k **uživateli,** přidejte nejprve vlastnost identity do prostředku aplikace s **typem userAssigned** a odkazovaných identit přiřazených uživatelem. Potom přidejte oddíl **managedIdentities** do oddílu **vlastností** pro prostředek **aplikace,** který obsahuje seznam popisného názvu, do mapování principalId pro každou z uživatelem přiřazených identit. Další informace o identitách přiřazených uživatelům naleznete v [tématu Vytvoření, seznam nebo odstranění spravované identity přiřazené uživateli](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell).

### <a name="application-template"></a>Šablona aplikace

Chcete-li povolit aplikaci s identitou Přiřazená uživatel, nejprve přidejte vlastnost **identity** do prostředku aplikace s typem **userAssigned** a odkazovaným uživatelem přiřazenými identitami a potom přidejte objekt **managedIdentities** uvnitř oddílu **vlastností,** který obsahuje seznam popisného názvu do mapování principalId pro každý uživatel přiřazené identity.

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

Ve výše uvedeném příkladu se název prostředku uživatele přiřazené identity používá jako popisný název spravované identity pro aplikaci. Následující příklady předpokládají, že skutečný popisný název je "AdminUser".

### <a name="application-package"></a>Balíček aplikace

1. Pro každou identitu `managedIdentities` definovanou v části v šabloně Azure Resource Manager přidejte `<ManagedIdentity>` značku do manifestu aplikace v části **Objekty zabezpečení.** Atribut `Name` musí odpovídat `name` vlastnosti `managedIdentities` definované v části.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. V části **ServiceManifestImport** přidejte **identityBindingPolicy** pro službu, která používá spravovanou identitu. Tato zásada `AdminUser` mapuje identitu na název identity specifické pro službu, který je třeba přidat do manifestu služby později.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Aktualizujte manifest služby a přidejte **managedidentity** uvnitř `ServiceIdentityRef` části `IdentityBindingPolicy` **Prostředky** s názvem odpovídajícím v manifestu aplikace:

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

* [Použití kódu aplikace spravované identity v aplikaci Service Fabric](how-to-managed-identity-service-fabric-app-code.md)
* [Jak udělit přístup aplikací Service Fabric k jiným prostředkům Azure](how-to-grant-access-other-resources.md)
