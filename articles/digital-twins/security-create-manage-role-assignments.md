---
title: Vytvořit a spravovat přiřazení rolí v Azure digitální dvojče | Dokumentace Microsoftu
description: Vytvořit a spravovat přiřazení rolí v digitální dvojče Azure.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyrana
ms.custom: seodec18
ms.openlocfilehash: 72a42e273029bd42d77531953ff5cbfc0fe5c295
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53810895"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Vytvořit a spravovat přiřazení rolí v Azure digitální dvojče

Azure digitální dvojče používá řízení přístupu na základě rolí ([RBAC](./security-role-based-access-control.md)) ke správě přístupu k prostředkům.

## <a name="role-assignments-overview"></a>Přehled přiřazení role

Každé přiřazení role odpovídá následující definici:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

Následující tabulka popisuje každý atribut:

| Atribut | Název | Požaduje se | Typ | Popis |
| --- | --- | --- | --- | --- |
| RoleId | Identifikátor definice role | Ano | Řetězec | Jedinečné ID přiřazení požadované role. Najdete definice rolí a jejich identifikátoru dotazování rozhraní API systému nebo kontrola následující tabulka. |
| ID objektu | Identifikátor objektu | Ano | Řetězec | ID služby Azure Active Directory, ID instančního objektu služby nebo název domény. Co nebo které je přiřazeno přiřazení role. Přiřazení role musí být naformátovaná podle jeho přidruženého typu. Pro `DomainName` objectIdType, objectId musí začínat `“@”` znak. |
| objectIdType | Typ identifikátoru objektu | Ano | Řetězec | Druh použít identifikátor objektu. Zobrazit **podporované ObjectIdTypes** níže. |
| path | Místo cesty | Ano | Řetězec | Cesta úplný přístup k `Space` objektu. Příklad: `/{Guid}/{Guid}`. Pokud identifikátor potřebuje přiřazení role pro celý graf, zadejte `"/"`. Tento znak určuje kořenový adresář, ale jeho použití se nedoporučuje. Vždy použijte Princip nejnižších oprávnění. |
| ID Tenanta | Identifikátor tenanta | Různé | Řetězec | Ve většině případů ID tenanta služby Azure Active Directory. Zakázáno pro `DeviceId` a `TenantId` ObjectIdTypes. Vyžaduje se pro `UserId` a `ServicePrincipalId` ObjectIdTypes. Volitelné pro DomainName ObjectIdType. |

### <a name="supported-role-definition-identifiers"></a>Identifikátory definice role podporovaná

Každé přiřazení role přidruží entity ve vašem prostředí Azure digitální dvojče definice role.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Identifikátor typy podporovaných objektů

Dříve **objectIdType** atribut byl zaveden.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operace přiřazení role

Podporuje Azure digitální dvojče úplné *vytvořit*, *čtení*, a *odstranit* operace pro přiřazení rolí. *AKTUALIZACE* operace jsou zpracovány přiřazení rolí přidání, odebrání přiřazení role nebo úpravou [prostorový graf Intelligence](./concepts-objectmodel-spatialgraph.md) uzly, které přiřazení rolí udělit přístup k.

![Koncové body přiřazení role][1]

Zadaný referenční dokumentaci Swagger obsahuje další informace o všech dostupných rozhraní API koncové body, operace se žádostmi o a definice.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>Udělení oprávnění instančního objektu služby

Udělení oprávnění pro váš objekt služby je často jeden z prvních kroků, které využijete při práci s Azure digitální dvojče. To zahrnuje:

1. Přihlásit se k instanci Azure pomocí Powershellu.
1. Získání informací o instančním objektu vaší služby.
1. Přiřazuje se požadované role k hlavní službě.

Vaše ID aplikace je součástí v Azure Active Directory. Další informace o konfiguraci a zřizování digitální dvojče Azure ve službě Active Directory, přečtěte si [rychlý Start](./quickstart-view-occupancy-dotnet.md).

Jakmile máte ID aplikace, spusťte následující příkazy Powershellu:

```shell
Login-AzureRmAccount
Get-AzureRmADServicePrincipal -ApplicationId  <ApplicationId>
```

Uživatel s **správce** rolí potom můžete přiřadit role správce místa uživatele tím, že ověřeného požadavku HTTP POST na adresu URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Spolu s následujícím textem JSON:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>Načíst všechny role

![Role systému][2]

Seznam všech dostupných rolí (role definice), ujistěte se, ověřeného požadavku HTTP GET na:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Vrátí pole JSON se položky pro každou roli, která může být přiřazen úspěšného požadavku:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>Zkontrolujte přiřazení konkrétní roli

Pokud chcete zkontrolovat konkrétní roli přiřazení, ujistěte se, ověřeného požadavku HTTP GET na:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Hodnota parametru** | **Požadováno** |  **Typ** |  **Popis** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | Řetězec |   ObjectId pro objectIdType ID uživatele. |
| YOUR_PATH | True | Řetězec |   Vybrané cesty ke kontrole přístupu. |
| YOUR_ACCESS_TYPE |  True | Řetězec |   Typ přístupu ke kontrole. |
| YOUR_RESOURCE_TYPE | True | Řetězec |  Prostředek, který chcete zkontrolovat. |

Vrátí logickou hodnotu úspěšného požadavku `true` nebo `false` označující, zda typ přístupu se přiřadila uživateli pro danou cestu a prostředků.

### <a name="get-role-assignments-by-path"></a>Získat přiřazení role podle cesty

Pokud chcete získat všechna přiřazení rolí pro cestu, ujistěte se, ověřeného požadavku HTTP GET na:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_PATH | Úplná cesta k oboru |

Úspěšné žádosti vrátí pole JSON se každý přiřazení role, které jsou spojené s vybranou **cesta** parametr:

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Odvolat oprávnění

Odvolat oprávnění z příjemce odstraňte přiřazení role tím, že ověřeného požadavku HTTP DELETE:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **Id** odebrat přiřazení role |

Úspěšná žádost o odstranění vrátí stav 204 odpovědi. Ověří odebrání přiřazení role pomocí [kontrola](#check) Určuje, zda stále udržuje přiřazení role.

### <a name="create-a-role-assignment"></a>Vytvořit přiřazení role

Chcete-li vytvořit přiřazení role, ujistěte se, ověřeného požadavku HTTP POST na adresu URL:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Ověřte, že datové části JSON odpovídá následující schéma:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Vrátí stav odezvě 201 spolu s úspěšného požadavku **id** přiřazení nově vytvořené role:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Příklady konfigurace

Následující příklady ukazují, jak nakonfigurovat vlastní obsah JSON v několika situacích běžně došlo k přiřazení role.

* **Příklad**: Uživatel potřebuje přístup správce k dolní mez prostoru tenanta.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Příklad**: Je aplikace spuštěna testovacích scénářů napodobování zařízení a senzorů.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Příklad**: Všichni uživatelé, kteří jsou součástí domény získají přístup pro čtení pro mezery, senzory a uživatelů. Tento přístup vyžaduje odpovídající souvisejících objektů.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Další postup

Zkontrolujte digitální dvojče Azure role-– řízení přístupu na základě-, přečtěte si téma [Role-base-access-control](./security-authenticating-apis.md).

Další informace o ověřování Azure digitální dvojče API najdete v článku [ověřování API](./security-authenticating-apis.md).

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
