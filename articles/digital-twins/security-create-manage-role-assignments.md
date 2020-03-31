---
title: Vytváření a správa přiřazení rolí – Azure Digital Twins | Dokumenty společnosti Microsoft
description: Přečtěte si o vytváření a správě přiřazení rolí v rámci Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1c83ca0abfd17db873bec62f0a0d052703862a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110401"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Vytváření a správa přiřazení rolí v Azure Digital Twins

Azure Digital Twins používá řízení přístupu na základě rolí[(RBAC)](./security-role-based-access-control.md)ke správě přístupu k prostředkům.

## <a name="role-assignments-overview"></a>Přehled přiřazení rolí

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

Následující tabulka popisuje jednotlivé atributy:

| Atribut | Name (Název) | Požaduje se | Typ | Popis |
| --- | --- | --- | --- | --- |
| roleId | Identifikátor definice role | Ano | Řetězec | Jedinečné ID požadovaného přiřazení role. Definice rolí a jejich identifikátor najdete dotazem na rozhraní API systému nebo na níže uvedenou tabulku kontroly. |
| Objectid | Identifikátor objektu | Ano | Řetězec | ID služby Azure Active Directory, ID objektu hlavního adresáře služby nebo název domény. K čemu nebo komu je přiřazení role přiřazeno. Přiřazení role musí být formátováno podle přidruženého typu. Pro `DomainName` objectIdType, objectId musí `“@”` začínat znakem. |
| objectIdType | Typ identifikátoru objektu | Ano | Řetězec | Druh identifikátoru objektu. Viz **Podporované ObjectIdTypes** níže. |
| cesta | Vesmírná cesta | Ano | Řetězec | Úplná přístupová `Space` cesta k objektu. Příklad: `/{Guid}/{Guid}`. Pokud identifikátor potřebuje přiřazení role pro celý `"/"`graf, zadejte . Tento znak označuje kořen, ale jeho použití se nedoporučuje. Vždy dodržujte zásadu nejmenšího privilegia. |
| tenantId | Identifikátor klienta | Různé | Řetězec | Ve většině případů ID klienta Služby Azure Active Directory. Zakázáno pro `DeviceId` `TenantId` a ObjectIdTypes. Požadováno `UserId` `ServicePrincipalId` pro a ObjectIdTypes. Volitelné pro DomainName ObjectIdType. |

### <a name="supported-role-definition-identifiers"></a>Podporované identifikátory definic rolí

Každé přiřazení role přidruží definici role k entitě v prostředí Azure Digital Twins.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Podporované typy identifikátorů objektů

Dříve byl zaveden atribut **objectIdType.**

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operace přiřazení rolí

Azure Digital Twins podporuje úplné *operace CREATE*, *READ*a *DELETE* pro přiřazení rolí. *Operace UPDATE* jsou zpracovány přidáním přiřazení rolí, odebráním přiřazení rolí nebo úpravou uzlů [grafu prostorové inteligence,](./concepts-objectmodel-spatialgraph.md) ke kterým přiřazení rolí poskytují přístup.

[![Koncové body přiřazení role](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

Zadaná referenční dokumentace Swagger obsahuje další informace o všech dostupných koncových bodech rozhraní API, operacích požadavků a definicích.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Udělit oprávnění instančnímu objektu

Udělení oprávnění vašemu instančnímu objektu je často jedním z prvních kroků, které provedete při práci s Azure Digital Twins. Zahrnuje:

1. Přihlášení k instanci Azure prostřednictvím [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) nebo [PowerShellu](https://docs.microsoft.com/powershell/azure/).
1. Získání informací o instančním objektu.
1. Přiřazení požadované role instančnímu objektu.

ID aplikace se vám dodá ve službě Azure Active Directory. Další informace o konfiguraci a zřizování digitálních dvojčat Azure ve službě Active Directory najdete v [úvodním panelu](./quickstart-view-occupancy-dotnet.md).

Jakmile budete mít ID aplikace, spusťte jeden z následujících příkazů. V Azure CLI:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

V powershellu:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Uživatel s rolí **Správce** pak může přiřadit roli Správce prostoru uživateli tak, že na adresu URL vytvoří ověřený požadavek HTTP POST:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

S následujícím tělem JSON:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Načíst všechny role

[![Systémové role](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Chcete-li vypsat všechny dostupné role (definice rolí), vytvořte ověřený požadavek HTTP GET na:

```URL
YOUR_MANAGEMENT_API_URL/system/roles
```

Úspěšný požadavek vrátí pole JSON s položkami pro každou roli, která může být přiřazena:

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

### <a name="check-a-specific-role-assignment"></a>Kontrola konkrétního přiřazení role

Chcete-li zkontrolovat konkrétní přiřazení role, vytvořte ověřený požadavek HTTP GET na:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Hodnota parametru** | **Požadováno** |  **Typ** |  **Popis** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | Řetězec |   ObjectId pro UserId objectIdType. |
| YOUR_PATH | True | Řetězec |   Zvolená cesta ke kontrole přístupu. |
| YOUR_ACCESS_TYPE |  True | Řetězec |   *Čtení,* *Vytvoření*, *Aktualizace*nebo *Odstranění* |
| YOUR_RESOURCE_TYPE | True | Řetězec |  *Device*, *DeviceBlobMetadata*, *DeviceExtendedProperty , DeviceExtendedProperty*, *ExtendedPropertyKey*, *ExtendedType*, *Endpoint*, *KeyStore*, *Matcher*, *Ontology*, *RoleDefinition*, *Sensor*, *SensorExtendedProperty*, *Space*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *RoleDefinition* *System*, * UerDefinedFunction*, *Uživatel*, *UserBlobMetadata*nebo *UserExtendedProperty* |

Úspěšný požadavek vrátí logickou hodnotu `true` nebo `false` označuje, zda byl uživateli přiřazen typ přístupu pro danou cestu a prostředek.

### <a name="get-role-assignments-by-path"></a>Získání přiřazení rolí podle cesty

Chcete-li získat všechna přiřazení rolí pro cestu, vytvořte ověřený požadavek HTTP GET na:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_PATH | Úplná cesta k prostoru |

Úspěšný požadavek vrátí pole JSON s každým přiřazením role přidruženým k vybranému parametru **cesty:**

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

### <a name="revoke-a-permission"></a>Odvolání oprávnění

Chcete-li odvolat oprávnění od příjemce, odstraňte přiřazení role vytvořením ověřeného požadavku HTTP DELETE:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **Id** přiřazení role odebrat |

Úspěšný požadavek DELETE vrátí stav odpovědi 204. Ověřte odebrání přiřazení role [kontrolou,](#check-a-specific-role-assignment) zda přiřazení role stále platí.

### <a name="create-a-role-assignment"></a>Vytvoření přiřazení role

Chcete-li vytvořit přiřazení role, vytvořte ověřený požadavek HTTP POST na adresu URL:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

Ověřte, zda tělo JSON odpovídá následujícímu schématu:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Úspěšný požadavek vrátí stav odpovědi 201 spolu s **id** nově vytvořenépřiřazení role:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Příklady konfigurace

Následující příklady ukazují, jak nakonfigurovat tělo JSON v několika běžně se vyskytujících scénářích přiřazení rolí.

* **Příklad**: Uživatel potřebuje přístup správce k podlaze prostoru klienta.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Příklad:** Aplikace spustí testovací scénáře zesměšňovat zařízení a senzory.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Příklad:** Všichni uživatelé, kteří jsou součástí domény, získají přístup pro čtení pro mezery, senzory a uživatele. Tento přístup zahrnuje jejich odpovídající související objekty.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Další kroky

- Chcete-li zkontrolovat Azure Digital Twins řízení přístupu založené na rolích, přečtěte [si role base-access-control](./security-authenticating-apis.md).

- Informace o ověřování rozhraní API azure digital twins api, přečtěte si [ověřování rozhraní API](./security-authenticating-apis.md).
