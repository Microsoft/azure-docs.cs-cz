---
title: "Řízení přístupu na základě role se zbytkem – Azure AD | Microsoft Docs"
description: "Správa řízení přístupu na základě rolí pomocí rozhraní REST API"
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: 
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: active-directory
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: rolyon
ms.openlocfilehash: ba25340e41cefe2b7847a39a6c9182cd0fc057d3
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-the-rest-api"></a>Správa řízení přístupu na základě rolí pomocí rozhraní REST API
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

S na základě rolí řízení přístupu (RBAC), můžete definovat přístupu pro uživatele, skupiny a objekty služby podle přiřazení rolí v určitém rozsahu. Tento článek popisuje, jak můžete spravovat přístup pomocí rozhraní REST API.

## <a name="list-all-role-assignments"></a>Zobrazí seznam všech přiřazení rolí
Zobrazí seznam všech přiřazení role v zadaném oboru a subscopes.

K přiřazení rolí seznamu je nutné mít přístup k `Microsoft.Authorization/roleAssignments/read` operace v oboru. Mezi integrované role mají přístup k této operace. Další informace o přiřazení rolí a správu přístupu k prostředkům Azure najdete v tématu [řízení přístupu](role-based-access-control-configure.md).

### <a name="request"></a>Žádost
Použití **získat** metoda s následující identifikátor URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&$filter={filter}

V rámci identifikátoru URI proveďte následující náhradu k přizpůsobení vaší žádosti:

1. Nahraďte *{oboru}* s oborem, pro který chcete zobrazit seznam přiřazení rolí. Následující příklady ukazují, jak lze určit obor pro různé úrovně:

   * Předplatné: /subscriptions/ {id předplatného}  
   * Skupina prostředků: /subscriptions/ {id předplatného} / Skupinyprostředků/myresourcegroup1  
   * Prostředek: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Nahraďte *{api-version}* s 2015-07-01.
3. Nahraďte *{filtru}* s podmínku, kterou chcete použít pro filtrování seznamu přiřazení role:

   * Seznam přiřazení rolí pro pouze zadaný obor, není včetně přiřazení rolí v subscopes: `atScope()`    
   * Seznam přiřazení rolí pro konkrétního uživatele, skupinu nebo aplikaci: `principalId%20eq%20'{objectId of user, group, or service principal}'`  
   * Seznam přiřazení rolí pro konkrétního uživatele, včetně těch, které jsou zděděno od skupiny | `assignedTo('{objectId of user}')`

### <a name="response"></a>Odpověď
Stavový kód: 200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role-assignment"></a>Získat informace o přiřazení role
Získá informace o přiřazení jedné role určeného identifikátor přiřazení role.

Chcete-li získat informace o přiřazení role, musíte mít přístup k `Microsoft.Authorization/roleAssignments/read` operaci. Mezi integrované role mají přístup k této operace. Další informace o přiřazení rolí a správu přístupu k prostředkům Azure najdete v tématu [řízení přístupu](role-based-access-control-configure.md).

### <a name="request"></a>Žádost
Použití **získat** metoda s následující identifikátor URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

V rámci identifikátoru URI proveďte následující náhradu k přizpůsobení vaší žádosti:

1. Nahraďte *{oboru}* s oborem, pro který chcete zobrazit seznam přiřazení rolí. Následující příklady ukazují, jak lze určit obor pro různé úrovně:

   * Předplatné: /subscriptions/ {id předplatného}  
   * Skupina prostředků: /subscriptions/ {id předplatného} / Skupinyprostředků/myresourcegroup1  
   * Prostředek: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Nahraďte *{id role přiřazení}* s identifikátorem GUID přiřazení role.
3. Nahraďte *{api-version}* s 2015-07-01.

### <a name="response"></a>Odpověď
Stavový kód: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## <a name="create-a-role-assignment"></a>Umožňuje vytvořit přiřazení Role
Vytvořte přiřazení role v zadaném oboru pro zadaný objekt zabezpečení udělení zadané roli.

Pokud chcete vytvořit přiřazení role, musíte mít přístup k `Microsoft.Authorization/roleAssignments/write` operaci. Z předdefinovaných rolí pouze *vlastníka* a *správce přístupu uživatelů* mají udělen přístup k této operaci. Další informace o přiřazení rolí a správu přístupu k prostředkům Azure najdete v tématu [řízení přístupu](role-based-access-control-configure.md).

### <a name="request"></a>Žádost
Použití **PUT** metoda s následující identifikátor URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

V rámci identifikátoru URI proveďte následující náhradu k přizpůsobení vaší žádosti:

1. Nahraďte *{oboru}* k oboru, ve kterém chcete vytvořit přiřazení role. Když vytvoříte přiřazení role v nadřazeném oboru, všechny podřízené obory dědí stejné přiřazení role. Následující příklady ukazují, jak lze určit obor pro různé úrovně:

   * Předplatné: /subscriptions/ {id předplatného}  
   * Skupina prostředků: /subscriptions/ {id předplatného} / Skupinyprostředků/myresourcegroup1   
   * Prostředek: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Nahraďte *{id role přiřazení}* s nový identifikátor GUID, který se stane identifikátor GUID nové přiřazení role.
3. Nahraďte *{api-version}* s 2015-07-01.

Tělo žádosti zadejte hodnoty v následujícím formátu:

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| Název elementu | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| hodnoty vlastnosti roleDefinitionId |Ano |Řetězec |Identifikátor role. Formát identifikátoru je: `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId |Ano |Řetězec |objectId přiřazenou roli hlavního Azure AD (uživatele, skupinu nebo objekt služby). |

### <a name="response"></a>Odpověď
Stavový kód: 201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## <a name="delete-a-role-assignment"></a>Umožňuje odstranit přiřazení Role
Umožňuje odstranit přiřazení role v zadaném oboru.

Pokud chcete odstranit přiřazení role, musíte mít přístup k `Microsoft.Authorization/roleAssignments/delete` operaci. Z předdefinovaných rolí pouze *vlastníka* a *správce přístupu uživatelů* mají udělen přístup k této operaci. Další informace o přiřazení rolí a správu přístupu k prostředkům Azure najdete v tématu [řízení přístupu](role-based-access-control-configure.md).

### <a name="request"></a>Žádost
Použití **odstranit** metoda s následující identifikátor URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}

V rámci identifikátoru URI proveďte následující náhradu k přizpůsobení vaší žádosti:

1. Nahraďte *{oboru}* k oboru, ve kterém chcete vytvořit přiřazení role. Následující příklady ukazují, jak lze určit obor pro různé úrovně:

   * Předplatné: /subscriptions/ {id předplatného}  
   * Skupina prostředků: /subscriptions/ {id předplatného} / Skupinyprostředků/myresourcegroup1  
   * Prostředek: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Nahraďte *{id role přiřazení}* s id přiřazení role identifikátor GUID.
3. Nahraďte *{api-version}* s 2015-07-01.

### <a name="response"></a>Odpověď
Stavový kód: 200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## <a name="list-all-roles"></a>Zobrazí seznam všech rolí
Obsahuje seznam všech rolí, které jsou k dispozici pro přiřazení v zadaném oboru.

Seznam rolí musíte mít přístup k `Microsoft.Authorization/roleDefinitions/read` operace v oboru. Mezi integrované role mají přístup k této operace. Další informace o přiřazení rolí a správu přístupu k prostředkům Azure najdete v tématu [řízení přístupu](role-based-access-control-configure.md).

### <a name="request"></a>Žádost
Použití **získat** metoda s následující identifikátor URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&$filter={filter}

V rámci identifikátoru URI proveďte následující náhradu k přizpůsobení vaší žádosti:

1. Nahraďte *{oboru}* s oborem, pro který chcete zobrazit seznam rolí. Následující příklady ukazují, jak lze určit obor pro různé úrovně:

   * Předplatné: /subscriptions/ {id předplatného}  
   * Skupina prostředků: /subscriptions/ {id předplatného} / Skupinyprostředků/myresourcegroup1  
   * /Subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 prostředků  
2. Nahraďte *{api-version}* s 2015-07-01.
3. Nahraďte *{filtru}* s podmínku, kterou chcete použít pro filtrování seznamu rolí:

   * Seznam rolí, které jsou k dispozici pro přiřazení v zadaném oboru a všechny její podřízené obory: `atScopeAndBelow()`
   * Vyhledejte roli pomocí přesný zobrazovaný název: `roleName%20eq%20'{role-display-name}'`. Pomocí formuláře kódovaná adresou URL přesný zobrazovaného názvu role. Pro instanci, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="response"></a>Odpověď
Stavový kód: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="get-information-about-a-role"></a>Získat informace o roli
Získá informace o jedné role určeného identifikátor definice role. Chcete-li získat informace o jedné role pomocí názvu zobrazení, přečtěte si téma [seznam všech rolí](role-based-access-control-manage-access-rest.md#list-all-roles).

Chcete-li získat informace o roli, musíte mít přístup k `Microsoft.Authorization/roleDefinitions/read` operaci. Mezi integrované role mají přístup k této operace. Další informace o přiřazení rolí a správu přístupu k prostředkům Azure najdete v tématu [řízení přístupu](role-based-access-control-configure.md).

### <a name="request"></a>Žádost
Použití **získat** metoda s následující identifikátor URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

V rámci identifikátoru URI proveďte následující náhradu k přizpůsobení vaší žádosti:

1. Nahraďte *{oboru}* s oborem, pro který chcete zobrazit seznam přiřazení rolí. Následující příklady ukazují, jak lze určit obor pro různé úrovně:

   * Předplatné: /subscriptions/ {id předplatného}  
   * Skupina prostředků: /subscriptions/ {id předplatného} / Skupinyprostředků/myresourcegroup1  
   * Prostředek: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Nahraďte *{id role definice}* s identifikátorem GUID definice role.
3. Nahraďte *{api-version}* s 2015-07-01.

### <a name="response"></a>Odpověď
Stavový kód: 200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## <a name="create-a-custom-role"></a>Vytvořit vlastní roli
Vytvořte vlastní roli.

Pokud chcete vytvořit vlastní roli, musíte mít přístup k `Microsoft.Authorization/roleDefinitions/write` operaci na všech `AssignableScopes`. Z předdefinovaných rolí pouze *vlastníka* a *správce přístupu uživatelů* mají udělen přístup k této operaci. Další informace o přiřazení rolí a správu přístupu k prostředkům Azure najdete v tématu [řízení přístupu](role-based-access-control-configure.md).

### <a name="request"></a>Žádost
Použití **PUT** metoda s následující identifikátor URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

V rámci identifikátoru URI proveďte následující náhradu k přizpůsobení vaší žádosti:

1. Nahraďte *{oboru}* s prvním *AssignableScope* vlastní role. Následující příklady ukazují, jak lze určit obor pro různé úrovně.

   * Předplatné: /subscriptions/ {id předplatného}  
   * Skupina prostředků: /subscriptions/ {id předplatného} / Skupinyprostředků/myresourcegroup1  
   * Prostředek: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Nahraďte *{id role definice}* s nový identifikátor GUID, který se stane identifikátor GUID novou vlastní roli.
3. Nahraďte *{api-version}* s 2015-07-01.

Tělo žádosti zadejte hodnoty v následujícím formátu:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Název elementu | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| jméno |Ano |Řetězec |Identifikátor GUID vlastní role. |
| properties.roleName |Ano |Řetězec |Zobrazovaný název vlastní role. Maximální velikost 128 znaků. |
| properties.description |Ne |Řetězec |Popis vlastní role. Maximální velikost 1024 znaků. |
| properties.type |Ano |Řetězec |Nastavte na "CustomRole." |
| properties.permissions.actions |Ano |řetězec] |Pole řetězců akce zadání operace udělit pomocí vlastní role. |
| properties.permissions.notActions |Ne |řetězec] |Pole řetězců akce zadání operace, které chcete vyloučit z operace udělit pomocí vlastní role. |
| properties.assignableScopes |Ano |řetězec] |Pole obory, kde můžete použít vlastní role. |

### <a name="response"></a>Odpověď
Stavový kód: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="update-a-custom-role"></a>Aktualizovat vlastní Role
Upravte vlastní roli.

Pokud chcete upravit vlastní roli, musíte mít přístup k `Microsoft.Authorization/roleDefinitions/write` operaci na všech `AssignableScopes`. Z předdefinovaných rolí pouze *vlastníka* a *správce přístupu uživatelů* mají udělen přístup k této operaci. Další informace o přiřazení rolí a správu přístupu k prostředkům Azure najdete v tématu [řízení přístupu](role-based-access-control-configure.md).

### <a name="request"></a>Žádost
Použití **PUT** metoda s následující identifikátor URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

V rámci identifikátoru URI proveďte následující náhradu k přizpůsobení vaší žádosti:

1. Nahraďte *{oboru}* s prvním *AssignableScope* vlastní role. Následující příklady ukazují, jak lze určit obor pro různé úrovně:

   * Předplatné: /subscriptions/ {id předplatného}  
   * Skupina prostředků: /subscriptions/ {id předplatného} / Skupinyprostředků/myresourcegroup1  
   * Prostředek: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Nahraďte *{id role definice}* s identifikátorem GUID vlastní role.
3. Nahraďte *{api-version}* s 2015-07-01.

Tělo žádosti zadejte hodnoty v následujícím formátu:

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| Název elementu | Požaduje se | Typ | Popis |
| --- | --- | --- | --- |
| jméno |Ano |Řetězec |Identifikátor GUID vlastní role. |
| properties.roleName |Ano |Řetězec |Zobrazovaný název aktualizované vlastní role. |
| properties.description |Ne |Řetězec |Popis aktualizované vlastní role. |
| properties.type |Ano |Řetězec |Nastavte na "CustomRole." |
| properties.permissions.actions |Ano |řetězec] |Pole řetězců akce zadání operací, u kterých aktualizované vlastní role uděluje přístup. |
| properties.permissions.notActions |Ne |řetězec] |Pole určující operace, které chcete vyloučit z operací, které aktualizované vlastní role uděluje řetězce akce. |
| properties.assignableScopes |Ano |řetězec] |Pole obory, ve kterých lze použít aktualizované vlastní role. |

### <a name="response"></a>Odpověď
Stavový kód: 201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## <a name="delete-a-custom-role"></a>Odstranit vlastní roli
Odstraňte vlastní roli.

Chcete-li odstranit vlastní roli, musíte mít přístup k `Microsoft.Authorization/roleDefinitions/delete` operaci na všech `AssignableScopes`. Z předdefinovaných rolí pouze *vlastníka* a *správce přístupu uživatelů* mají udělen přístup k této operaci. Další informace o přiřazení rolí a správu přístupu k prostředkům Azure najdete v tématu [řízení přístupu](role-based-access-control-configure.md).

### <a name="request"></a>Žádost
Použití **odstranit** metoda s následující identifikátor URI:

    https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}

V rámci identifikátoru URI proveďte následující náhradu k přizpůsobení vaší žádosti:

1. Nahraďte *{oboru}* s oborem, na které chcete odstranit definici role. Následující příklady ukazují, jak lze určit obor pro různé úrovně:

   * Předplatné: /subscriptions/ {id předplatného}  
   * Skupina prostředků: /subscriptions/ {id předplatného} / Skupinyprostředků/myresourcegroup1  
   * Prostředek: /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1  
2. Nahraďte *{id role definice}* s id definice role GUID vlastní role.
3. Nahraďte *{api-version}* s 2015-07-01.

### <a name="response"></a>Odpověď
Stavový kód: 200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

## <a name="next-steps"></a>Další postup

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
