---
title: Přidání aplikace webového rozhraní API – Azure Active Directory B2C | Microsoft Docs
description: Naučte se, jak přidat aplikaci webového rozhraní API do tenanta Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0eeff0c8c338b09fbe375587db2b955a143138c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94949815"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Přidání aplikace webového rozhraní API do tenanta Azure Active Directory B2C

 Zaregistrujte prostředky webového rozhraní API ve vašem tenantovi, aby mohli přijímat a reagovat na žádosti klientskými aplikacemi, které prezentují přístupový token. V tomto článku se dozvíte, jak zaregistrovat webové rozhraní API v Azure Active Directory B2C (Azure AD B2C).

K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *webapi1*.
1. V části **identifikátor URI pro přesměrování** vyberte **Web** a potom zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V produkční aplikaci můžete nastavit identifikátor URI přesměrování jako koncový bod `https://localhost:5000` . Během vývoje nebo testování je možné jej nastavit na `https://jwt.ms` webovou aplikaci, která je ve vlastnictví společnosti Microsoft, která zobrazuje dekódování obsahu tokenu (obsah tokenu nikdy nezůstane v prohlížeči). V registrovaných aplikacích můžete kdykoli přidat a změnit identifikátory URI pro přesměrování.
1. Vyberte **Zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v kódu webového rozhraní API.

Pokud máte aplikaci, která implementuje tok implicitního udělení, například [jednoduchou aplikaci založenou na jazyce JavaScript (Spa)](tutorial-register-spa.md), můžete tok povolit pomocí následujícího postupu:

1. V části **Spravovat** vyberte **ověřování**.
1. V části **implicitní udělení** vyberte zaškrtávací políčka **přístupové tokeny** i **tokeny ID** .
1. Vyberte **Uložit**.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace (starší verze)** a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku** vyberte **Ano**.
7. V případě **adresy URL odpovědi** Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V produkční aplikaci můžete nastavit adresu URL odpovědi na hodnotu, například `https://localhost:44332` . Pro účely testování nastavte adresu URL odpovědi na `https://jwt.ms` .
8. Pro **identifikátor URI ID aplikace** zadejte identifikátor použitý pro vaše webové rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na **Vytvořit**.
10. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci webové aplikace.

* * *

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. V tomto kurzu pomocí oborů nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. Například v [kurzu: registrace aplikace v Azure Active Directory B2C](tutorial-register-applications.md), Webová aplikace s názvem *WebApp1* je zaregistrována v Azure AD B2C. Tuto aplikaci můžete použít k volání webového rozhraní API.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Vaše aplikace je zaregistrovaná pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C pro použití aplikace. Aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.