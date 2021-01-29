---
title: Omezení a omezení služby Azure AD B2C
titleSuffix: Azure AD B2C
description: Referenční informace o limitech a omezeních služby pro službu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 866af8b992374492286f47357f108a01f35e560b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051037"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Omezení a omezení služby Azure Active Directory B2C

Tento článek obsahuje omezení využití a další omezení služby pro službu Azure Active Directory B2C (Azure AD B2C).

## <a name="end-userconsumption-related-limits"></a>Omezení související s koncovým uživatelem a spotřebou

Následující omezení služby související s koncovým uživatelem se vztahují na všechna ověřování a autorizační protokoly podporované Azure AD B2C, včetně SAML, Open ID Connect, OAuth2 a ROPC.

|Kategorie |Omezení    |
|---------|---------|
|Počet požadavků na IP adresu na klienta Azure AD B2C       |6000/5 minut          |
|Celkový počet požadavků na tenanta Azure AD B2C     |12000/min          |

Počet požadavků se může lišit v závislosti na počtu čtení a zápisu adresářů, ke kterým dochází během Azure AD B2C cesty uživatele. Například jednoduchá cesta pro přihlášení, která se čte z adresáře, se skládá z 1 žádosti. Pokud musí cesta pro přihlášení také aktualizovat adresář, tato operace se počítá jako další požadavek.

## <a name="azure-ad-b2c-configuration-limits"></a>Azure AD B2C omezení konfigurace

Následující tabulka uvádí omezení konfigurace správy ve službě Azure AD B2C.

|Kategorie  |Omezení  |
|---------|---------|
|Počet aplikací na tenanta Azure AD B2C   |250           |
|Počet oborů na aplikaci        |1000          |
|Počet [vlastních atributů](user-profile-attributes.md#extension-attributes)   na uživatele <sup>1</sup>       |100         |
|Počet adres URL pro přesměrování na aplikaci       |100         |
|Počet odhlašovacích adres URL na aplikaci        |1          |
|Omezení počtu řetězců na atribut      |250 znaků          |
|Počet tenantů B2C na předplatné      |20         |
|Úrovně [dědičnosti](custom-policy-overview.md#inheritance-model) ve vlastních zásadách     |10         |
|Počet zásad na tenanta Azure AD B2C      |200          |
|Maximální velikost souboru zásad      |400 KB          |

<sup>1</sup> viz také omezení [a omezení služby Azure AD](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Další kroky

- Další informace o [pokynech k omezení Microsoft Graph](/graph/throttling.md) 
- Další informace o [rozdílech v ověřování pro aplikace Azure AD B2C](../active-directory/develop/supported-accounts-validation.md)













