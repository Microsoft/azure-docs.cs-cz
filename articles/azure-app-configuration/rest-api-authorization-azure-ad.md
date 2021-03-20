---
title: Konfigurace aplikace Azure REST API – autorizace Azure Active Directory
description: Použití Azure Active Directory k autorizaci s konfigurací aplikace Azure pomocí REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: c028dcf6649da9abd196997d077b58386a5e6dd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101092788"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Ověřování Azure Active Directory – REST API Reference

Při ověřování pomocí Azure Active Directory (Azure AD) se autorizace zpracovává pomocí řízení přístupu na základě role (RBAC). RBAC vyžaduje přiřazení uživatelů k rolím, aby bylo možné udělit přístup k prostředkům. Každá role obsahuje sadu akcí, které mohou uživatelé, kteří jsou přiřazeni k roli, provádět.

## <a name="roles"></a>Role

Ve výchozím nastavení jsou v předplatných Azure k dispozici následující role:

- **Vlastník dat konfigurace aplikace Azure**: Tato role poskytuje úplný přístup ke všem operacím.
- **Čtečka dat konfigurace aplikace Azure**: Tato role povoluje operace čtení.

## <a name="actions"></a>Akce

Role obsahují seznam akcí, které mohou uživatelé přiřazení k této roli provádět. Konfigurace aplikací Azure podporuje následující akce:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Tato akce povolí přístup pro čtení k prostředkům konfiguračního klíče aplikace, jako je například/KV a/labels..
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Tato akce povoluje přístup pro zápis do prostředků konfigurace aplikace klíčová hodnota.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Tato akce povoluje odstranění prostředků konfigurační klíč-hodnota aplikace. Všimněte si, že odstranění prostředku vrátí klíčovou hodnotu, která byla odstraněna.

## <a name="error"></a>Chyba

```http
HTTP/1.1 403 Forbidden
```

**Důvod:** Objekt zabezpečení, který vytváří požadavek, nemá požadovaná oprávnění k provedení požadované operace.
**Řešení:** Přiřaďte roli nutnou k provedení požadované operace objektu zabezpečení, který požadavek odeslal.

## <a name="managing-role-assignments"></a>Správa přiřazení rolí

Přiřazení rolí můžete spravovat pomocí [procedur Azure RBAC](../role-based-access-control/overview.md) , které jsou standardem napříč všemi službami Azure. Můžete to provést prostřednictvím rozhraní příkazového řádku Azure, PowerShellu a Azure Portal. Další informace najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md).