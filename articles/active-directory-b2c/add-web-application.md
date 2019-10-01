---
title: Přidání webové aplikace – Azure Active Directory B2C | Microsoft Docs
description: Naučte se, jak přidat webovou aplikaci do tenanta Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3114f082a402fd005797b270cfdcd7527784dbe1
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679228"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Přidání aplikace webového rozhraní API do tenanta Azure Active Directory B2C

 Zaregistrujte prostředky webového rozhraní API ve vašem tenantovi, aby mohli přijímat a reagovat na žádosti klientskými aplikacemi, které prezentují přístupový token. V tomto článku se dozvíte, jak zaregistrovat aplikaci v Azure Active Directory B2C (Azure AD B2C).

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace**a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
7. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V produkční aplikaci můžete nastavit adresu URL odpovědi na hodnotu, například `https://localhost:44332`. Pro účely testování nastavte adresu URL odpovědi na `https://jwt.ms`.
8. Pro **identifikátor URI ID aplikace**zadejte identifikátor použitý pro vaše webové rozhraní API. Pro vás se vygeneruje úplný identifikátor URI, včetně domény. Například `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na **vytvořit**.
10. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob, jak řídit přístup k chráněným prostředkům. K implementaci řízení přístupu na základě oboru používá webové rozhraní API obory. Například uživatelé webového rozhraní API můžou mít přístup pro čtení i zápis, nebo můžou mít uživatelé webového rozhraní API jenom přístup pro čtení. V tomto kurzu použijete obory k definování oprávnění ke čtení a zápisu pro webové rozhraní API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Udělit oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. Například v [kurzu: registrace aplikace v Azure Active Directory B2C](tutorial-register-applications.md)je webová aplikace vytvořena v Azure AD B2C s názvem *WebApp1*. Tuto aplikaci můžete použít k volání webového rozhraní API.

1. Vyberte **aplikace**a pak vyberte webovou aplikaci.
1. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
1. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
1. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **OK**.

Vaše aplikace je zaregistrovaná pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C pro použití aplikace. Aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.
