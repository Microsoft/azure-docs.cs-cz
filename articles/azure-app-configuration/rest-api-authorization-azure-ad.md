---
title: Konfigurace aplikace Azure REST API – autorizace Azure Active Directory
description: Použití Azure Active Directory k autorizaci s konfigurací aplikace Azure pomocí REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: bebab7c06062726f7b5c7868f984cadda3b4c98e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424012"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Ověřování Azure Active Directory – REST API Reference

Při použití ověřování Azure Active Directory (Azure AD) se autorizace zpracovává pomocí Access Control na základě rolí Azure (RBAC). Azure RBAC vyžaduje, aby se uživatelé přiřadí k rolím, aby mohli udělit přístup k prostředkům. Každá role obsahuje sadu akcí, které budou moci provádět uživatelé přiřazení k této roli.

## <a name="roles"></a>Role

Následující role jsou předdefinované role, které jsou ve výchozím nastavení k dispozici ve službě Azure Subscriptions:

- **Vlastník dat konfigurace aplikace Azure** : Tato role poskytuje úplný přístup ke všem operacím.
- **Čtečka dat konfigurace aplikace Azure** : Tato role povoluje operace čtení.

## <a name="actions"></a>Akce

Role obsahují seznam akcí, které mohou uživatelé přiřazení k této roli provádět. Konfigurace aplikací Azure podporuje následující akce:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Tato akce povolí přístup pro čtení k prostředkům konfiguračního klíče aplikace, jako je například/KV a/labels..
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Tato akce povoluje přístup pro zápis do prostředků konfigurace aplikace klíčová hodnota.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Tato akce povoluje odstranění prostředků konfigurační klíč-hodnota aplikace. Poznámka: odstranění prostředku vrátí klíčovou hodnotu, která se odstranila.

## <a name="errors"></a>Chyby

```http
HTTP/1.1 403 Forbidden
```

**Důvod:** Objekt zabezpečení vytvářející požadavek nemá požadovaná oprávnění k provedení požadované operace.
**Řešení:** Přiřaďte roli nutnou k provedení požadované operace objektu zabezpečení, který požadavek odeslal.

## <a name="managing-role-assignments"></a>Správa přiřazení rolí

Správa přiřazení rolí se provádí pomocí procedur [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) , které jsou standardem napříč všemi službami Azure. To lze provést prostřednictvím Azure CLI, PowerShellu, Azure Portal a dalších. Oficiální dokumentaci, jak vytvořit přiřazení rolí, najdete [tady](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
