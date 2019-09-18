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
ms.openlocfilehash: 50a4ead58cc70524ec464e52ce546b36f9685df5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064539"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Přidání aplikace webového rozhraní API do tenanta Azure Active Directory B2C

 Zaregistrujte prostředky webového rozhraní API ve vašem tenantovi, aby mohli přijímat a reagovat na žádosti klientskými aplikacemi, které prezentují přístupový token. V tomto článku se dozvíte, jak zaregistrovat aplikaci v Azure Active Directory B2C (Azure AD B2C).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace**a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
7. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V produkční aplikaci můžete nastavit adresu URL odpovědi na hodnotu, například `https://localhost:44332`. Pro účely testování nastavte adresu URL odpovědi na `https://jwt.ms`.
8. Pro **identifikátor URI ID aplikace**zadejte identifikátor použitý pro vaše webové rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob, jak řídit přístup k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. V tomto kurzu pomocí oborů nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

1. Vyberte **aplikace**a pak vyberte *webapi1*.
2. Vyberte **publikované obory**.
3. Pro **Rozsah**zadejte `Read`, a pro Popis zadejte `Read access to the application`.
4. Pro **Rozsah**zadejte `Write`, a pro Popis zadejte `Write access to the application`.
5. Klikněte na **Uložit**.

Publikované obory lze použít k udělení oprávnění klientské aplikace webovému rozhraní API.

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. Například v [kurzu: Registrace aplikace v Azure Active Directory B2C](tutorial-register-applications.md)je webová aplikace vytvořena v Azure AD B2C s názvem *WebApp1*. Tuto aplikaci můžete použít k volání webového rozhraní API.

1. Vyberte **aplikace**a pak vyberte webovou aplikaci.
2. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
3. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
4. V rozevíracím seznamu **Vybrat obory** vyberte obory **pro čtení** a **zápis** , které jste předtím definovali.
5. Klikněte na **OK**.

Vaše aplikace je zaregistrovaná pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C pro použití aplikace. Aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.
