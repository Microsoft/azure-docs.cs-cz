---
title: Přidání webové aplikace rozhraní API – Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přidat aplikaci webového rozhraní API do klienta Služby Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e6dbf3d6fd5a43ab2d075c193c5bc589dc3566a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190173"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Přidání aplikace webového rozhraní API do klienta Azure Active Directory B2C

 Zaregistrujte prostředky webového rozhraní API ve vašem tenantovi, aby mohly přijímat požadavky klientských aplikací, které představují přístupový token, a reagovat na ně. Tento článek ukazuje, jak zaregistrovat webové rozhraní API ve službě Azure Active Directory B2C (Azure AD B2C).

Chcete-li zaregistrovat aplikaci ve vašem tenantovi Azure AD B2C, můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + odběr** a zvolte adresář, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **Aplikace**a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. V **možnostech Zahrnout webové aplikace/ webové rozhraní API** a **Povolit implicitní tok**vyberte **Ano**.
7. Pro **adresu URL odpovědi**zadejte koncový bod, kde azure AD B2C by měl vrátit všechny tokeny, které vaše aplikace požaduje. V produkční aplikaci můžete nastavit adresu URL odpovědi `https://localhost:44332`na hodnotu, například . Pro účely testování nastavte adresu `https://jwt.ms`URL odpovědi na .
8. Do **identifikátoru URI ID aplikace**zadejte identifikátor použitý pro webové rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na **Vytvořit**.
10. Na stránce vlastností zaznamenejte ID aplikace, které budete používat při konfiguraci webové aplikace.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *webapi1*.
1. V části **Přesměrování identifikátoru URI**vyberte **web**a zadejte koncový bod, ve kterém by měl Azure AD B2C vrátit všechny tokeny, které vaše aplikace požaduje. V produkční aplikaci můžete nastavit přesměrování URI koncový `https://localhost:5000`bod, jako je . Během vývoje nebo testování ji `https://jwt.ms`můžete nastavit na webovou aplikaci vlastněnou společností Microsoft, která zobrazuje dekódovaný obsah tokenu (obsah tokenu nikdy neopustí prohlížeč). Identifikátory URI přesměrování adres URI můžete v registrovaných aplikacích kdykoli přidat a upravit.
1. Vyberte **Zaregistrovat**.
1. Zaznamenejte **ID aplikace (klienta)** pro použití v kódu webového rozhraní API.

Pokud máte aplikaci, která implementuje implicitní tok grantů, například jednostránkovou aplikaci založenou na jazyce JavaScript, můžete tok povolit následujícími kroky:

1. V části **Manage**vyberte **Authentication**.
1. Vyberte **Vyzkoušet nové prostředí** (pokud je zobrazeno).
1. V **části Implicitní udělení**zaškrtněte políčka **Tokeny aplikace Access** i **Tokeny ID.**
1. Vyberte **Uložit**.

* * *

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. V tomto kurzu pomocí oborů nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte udělit oprávnění aplikace k rozhraní API. Například v [kurzu: Registrace aplikace ve službě Azure Active Directory B2C](tutorial-register-applications.md), webová aplikace s názvem *webapp1* je registrovaná v Azure AD B2C. Tuto aplikaci můžete použít k volání webového rozhraní API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Vaše aplikace je registrována pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C k použití aplikace. Aplikace získá udělení autorizace od Azure AD B2C pro přístup k chráněné webové rozhraní API.
