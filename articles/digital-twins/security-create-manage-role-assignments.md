---
title: Vytváření a správa přiřazení rolí – digitální vlákna Azure | Microsoft Docs
description: Seznamte se s vytvářením a správou přiřazení rolí v rámci digitálních vláken Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1c83ca0abfd17db873bec62f0a0d052703862a45
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77110401"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Vytváření a správa přiřazení rolí v digitálních prozdvojeních Azure

Digitální vlákna Azure používá řízení přístupu na základě role ([RBAC](./security-role-based-access-control.md)) ke správě přístupu k prostředkům.

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

| Atribut | Název | Požaduje se | Typ | Popis |
| --- | --- | --- | --- | --- |
| roleId | Identifikátor definice role | Ano | Řetězec | Jedinečné ID požadovaného přiřazení role Vyhledejte definice rolí a jejich identifikátor pomocí dotazu na následující tabulku rozhraní API systému nebo revize. |
| Objektu | Identifikátor objektu | Ano | Řetězec | ID Azure Active Directory, ID objektu zabezpečení služby nebo název domény. K čemu přiřazení role je přiřazeno. Přiřazení role musí být formátováno podle jeho přidruženého typu. Pro `DomainName` ObjectIdType musí objectID začínat `“@”` znakem. |
| objectIdType | Typ identifikátoru objektu | Ano | Řetězec | Typ použitého identifikátoru objektu. Viz článek **podporované ObjectIdTypes** níže. |
| cesta | Cesta k prostoru | Ano | Řetězec | Úplná cesta pro přístup k `Space` objektu. Příklad: `/{Guid}/{Guid}`. Pokud identifikátor potřebuje přiřazení role pro celý graf, zadejte `"/"`. Tento znak určuje kořenový adresář, ale jeho použití se nedoporučuje. Vždy postupujte podle principu nejnižší úrovně oprávnění. |
| tenantId | Identifikátor tenanta | Různé | Řetězec | Ve většině případů Azure Active Directory ID tenanta. Zakázáno pro `DeviceId` a `TenantId` ObjectIdTypes. Vyžaduje se `UserId` pro `ServicePrincipalId` a ObjectIdTypes. Volitelné pro domainname ObjectIdType. |

### <a name="supported-role-definition-identifiers"></a>Podporované identifikátory definice rolí

Každé přiřazení role přidruží definici role k entitě ve vašem prostředí digitálních vláken Azure.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Podporované typy identifikátorů objektů

Dřív byl zavedený atribut **objectIdType** .

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Operace přiřazení role

Funkce digitálních vláken Azure podporuje úplné operace *Vytvoření*, *čtení*a *odstranění* pro přiřazení rolí. Operace *aktualizace* jsou zpracovávány přidáním přiřazení rolí, odebráním přiřazení rolí nebo úpravou uzlů [grafu prostorové Intelligence](./concepts-objectmodel-spatialgraph.md) , ke kterým mají přiřazení role přístup.

[![Koncové body přiřazení role](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

Dodaná Referenční dokumentace k Swagger obsahuje další informace o všech dostupných koncových bodech rozhraní API, operacích požadavků a definicích.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Udělení oprávnění objektu služby

Udělení oprávnění k instančnímu objektu je často jedním z prvních kroků, které budete provádět při práci s digitálními vlákna Azure. Zahrnuje:

1. Přihlaste se k instanci Azure pomocí rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) nebo [PowerShellu](https://docs.microsoft.com/powershell/azure/).
1. Získávají se informace o instančním objektu.
1. Přiřazení požadované role k instančnímu objektu.

Vaše ID aplikace vám bylo dodáno v Azure Active Directory. Další informace o konfiguraci a zřizování digitálních vláken Azure ve službě Active Directory si můžete přečíst v [rychlém](./quickstart-view-occupancy-dotnet.md)startu.

Jakmile budete mít ID aplikace, spusťte jeden z následujících příkazů. V Azure CLI:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

V PowerShellu:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

Uživatel **s rolí správce může přiřadit roli správce** prostoru k uživateli tím, že na adresu URL přiřadí ověřený požadavek HTTP post:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

S následujícím textem JSON:

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

Pokud chcete zobrazit seznam všech dostupných rolí (definice rolí), proveďte ověřený požadavek HTTP GET na:

```URL
YOUR_MANAGEMENT_API_URL/system/roles
```

Úspěšná žádost vrátí pole JSON s položkami pro každou roli, která může být přiřazena:

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

### <a name="check-a-specific-role-assignment"></a>Kontrolovat přiřazení konkrétní role

Pokud chcete zkontrolovat přiřazení konkrétní role, proveďte ověřený požadavek HTTP GET na:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Hodnota parametru** | **Požadováno** |  **Typ** |  **Popis** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | Řetězec |   Identifikátor objectId pro identifikátor UserId objectIdType |
| YOUR_PATH | True | Řetězec |   Vybraná cesta pro kontrolu přístupu. |
| YOUR_ACCESS_TYPE |  True | Řetězec |   *Číst*, *vytvořit*, *aktualizovat*nebo *Odstranit* |
| YOUR_RESOURCE_TYPE | True | Řetězec |  *Zařízení*, *DeviceBlobMetadata*, *DeviceExtendedProperty*, *ExtendedPropertyKey*, *ExtendedType*, *koncový bod*, *úložiště klíčů* *, porovnávací služba,* *Ontology*, *Sestava*, *rutiny roledefinition*, *senzor*, *SensorExtendedProperty*, *prostor*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *systém*, *UerDefinedFunction*, *uživatel* *, UserBlobMetadata*nebo *UserExtendedProperty* |

Úspěšná žádost vrátí logickou hodnotu `true` nebo `false` k označení, zda byl k dané cestě a prostředku přiřazen daný typ přístupu uživateli.

### <a name="get-role-assignments-by-path"></a>Získat přiřazení rolí podle cesty

Pokud chcete pro cestu získat všechna přiřazení rolí, proveďte ověřený požadavek HTTP GET na:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_PATH | Úplná cesta k prostoru |

Úspěšná žádost vrátí pole JSON s každým přiřazením role přidruženým k vybranému parametru **cesty** :

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

Pokud chcete odvolat oprávnění od příjemce, odstraňte přiřazení role tím, že provedete ověřený požadavek HTTP DELETE:

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | **ID** přiřazení role, které se má odebrat |

Úspěšná žádost o odstranění vrátí stav odpovědi 204. Ověřte odebrání přiřazení role tím, že [zkontrolujete](#check-a-specific-role-assignment) , jestli přiřazení role pořád obsahuje.

### <a name="create-a-role-assignment"></a>Vytvoření přiřazení role

Chcete-li vytvořit přiřazení role, proveďte ověřený požadavek HTTP POST na adresu URL:

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

Úspěšná žádost vrátí stav odpovědi 201 spolu s **ID** nově vytvořeného přiřazení role:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Příklady konfigurace

Následující příklady ukazují, jak nakonfigurovat tělo JSON v několika běžně používaných scénářích přiřazení rolí.

* **Příklad**: uživatel potřebuje přístup správce k podlaze prostoru tenanta.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Příklad**: aplikace spouští testovací scénáře pro zařízení a senzory.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Příklad**: všichni uživatelé, kteří jsou součástí domény, obdrží přístup pro čtení pro prostory, senzory a uživatele. Tento přístup zahrnuje odpovídající související objekty.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Další kroky

- Pokud si chcete projít službu Azure Digital revlákens-Control-Control, přečtěte si [role-Base-Access-Control](./security-authenticating-apis.md).

- Pokud se chcete dozvědět víc o ověřování API u digitálních vláken Azure, přečtěte si téma [ověřování rozhraní API](./security-authenticating-apis.md).
