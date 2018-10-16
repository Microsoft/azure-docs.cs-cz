---
title: Principy připojení k Azure digitální dvojče zařízení a ověřování | Dokumentace Microsoftu
description: Použití Azure digitální dvojče pro připojení a ověřování zařízení
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: adfb4c369ea1b324da8562a5b0b245ebdecff602
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324050"
---
# <a name="create-and-manage-role-assignments"></a>Vytvořit a spravovat přiřazení rolí

Azure digitální dvojče používá řízení přístupu na základě rolí ([RBAC](./security-role-based-access-control.md)) ke správě přístupu k prostředkům.

Každé role přiřazení zahrnují:

* **Identifikátor objektu** (ID služby Azure Active Directory, ID instančního objektu služby nebo název domény).
* **Identifikátor typu objektu**.
* A **ID definice role**.
* A **místo cesta**.
* (Ve většině případů) služby Azure Active Directory **ID tenanta**.

## <a name="role-definition-identifiers"></a>Identifikátory definice role

Následující tabulka ukazuje, co lze získat pomocí dotazu na rozhraní API systému/role:

| **Role** | **identifikátor** |
| --- | --- |
| Místo správce | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Správce uživatelů| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Správce zařízení | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Klíč správce | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Správce tokenů | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Uživatel | b1ffdb77-c635-4E7E-ad25-948237d85b30 |
| Odborné | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalační program zařízení | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| GatewayDevice | d4c69766-e9bd-4e61-BFC1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>Podporované ObjectIdTypes

Podporované `ObjectIdTypes` jsou:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Vytvořit přiřazení role

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **Název** | **Vyžaduje** | **Typ** | **Popis** |
| --- | --- | --- | --- |
| RoleId| Ano |řetězec | Identifikátor definice role. Definice rolí a jejich identifikátorů můžete vyhledat dotazem na systémové rozhraní API. |
| ID objektu | Ano |řetězec | ID objektu pro přiřazení rolí, které musí být ve formátu podle jeho přidruženého typu. Pro `DomainName` ObjectIdType, ObjectId musí začínat `“@”` znak. |
| objectIdType | Ano |řetězec | Typ přiřazení role. Musí být jedna z následujících řádků v této tabulce. |
| ID Tenanta | Různé | řetězec |Identifikátor tenanta. Zakázáno pro `DeviceId` a `TenantId` ObjectIdTypes. Vyžaduje se pro `UserId` a `ServicePrincipalId` ObjectIdTypes. Volitelné pro DomainName ObjectIdType. |
| Cesta * | Ano | řetězec |Cesta úplný přístup k `Space` objektu. Např.: `/{Guid}/{Guid}` Pokud identifikátor potřebuje přiřazení role pro celý graf, zadejte `"/"` (který určuje kořenový adresář). Použití, který je však nedoporučuje a **by měla vždy použijte Princip nejnižších oprávnění**. |

## <a name="sample-configuration"></a>Ukázky konfigurace

Uživatel potřebuje přístup správce k dolní mez místa tenanta:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

Aplikace, který spuštění testu scénáře napodobování zařízeními a senzory:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Všichni uživatelé součástí domény obdrží oprávnění ke čtení pro mezery, senzory a uživatelům, včetně jejich odpovídajících související objekty:

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Pokud chcete získat přiřazení Role:

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **Název** | **V** | **Vyžaduje** |    **Typ** |  **Popis** |
| --- | --- | --- | --- | --- |
| Cesta | Cesta | True | Řetězec | Úplná cesta k oboru |

Pokud chcete odstranit přiřazení Role:

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **Název** | **V** | **Vyžaduje** | **Typ** | **Popis** |
| --- | --- | --- | --- | --- |
| ID | Cesta | True | Řetězec |   ID přiřazení role |

## <a name="next-steps"></a>Další postup

Další informace o zabezpečení Azure digitální dvojče [ověřování API](./security-authenticating-apis.md).
