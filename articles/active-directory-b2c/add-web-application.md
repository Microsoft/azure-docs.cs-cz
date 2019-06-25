---
title: Přidat webové aplikace – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak přidat webové aplikace do svého tenanta Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a1d098550c6fb733e088f8ad211d29f48f55d2d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511706"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Přidání webového rozhraní API aplikace do svého tenanta Azure Active Directory B2C

 Registrace webového rozhraní API prostředky ve vašem tenantovi tak, aby pak můžou přijímat a reagovat na požadavky u klientských aplikací, které se přístupový token. Tento článek ukazuje, jak zaregistrovat aplikaci v Azure Active Directory (Azure AD) B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C. Vyberte **filtr adresářů a předplatných** v horní nabídce a vyberte adresář, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. Pro **zahrnout webovou aplikaci / webové rozhraní API** a **povolit implicitní tok**vyberte **Ano**.
7. Pro **adresy URL odpovědi**, zadejte koncový bod kam by měl Azure AD B2C vrátí všechny tokeny, které vaše aplikace požaduje. V aplikace v produkčním prostředí, může být nastavíte příslušnou odpovědní adresu URL na hodnotu jako `https://localhost:44332`. Pro účely testování nastavena adresa URL odpovědi na `https://jwt.ms`.
8. Pro **identifikátor ID URI aplikace**, zadejte identifikátor použitý pro vaše webového rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti zaznamenejte ID aplikace, které budete používat při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Například uživatelé webového rozhraní API můžou mít přístup ke čtení i zápisu nebo přístup pouze ke čtení. V tomto kurzu pomocí oborů nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

1. Vyberte **aplikací**a pak vyberte *webapi1*.
2. Vyberte **publikované obory**.
3. Pro **oboru**, zadejte `Read`a popis, zadejte `Read access to the application`.
4. Pro **oboru**, zadejte `Write`a popis, zadejte `Write access to the application`.
5. Klikněte na **Uložit**.

Publikované obory je možné klientovi udělit oprávnění k aplikaci pro webové rozhraní API.

## <a name="grant-permissions"></a>Udělení oprávnění

Pokud chcete volat chráněné webové rozhraní API z aplikace, budete muset udělit oprávnění aplikace rozhraní API. Například v [kurzu: Registrace aplikace v Azure Active Directory B2C](tutorial-register-applications.md), webové aplikace se vytvoří v Azure AD B2C s názvem *webapp1*. Tuto aplikaci můžete použít k volání webového rozhraní API.

1. Vyberte **aplikace**a pak vyberte webovou aplikaci.
2. Vyberte **přístup přes rozhraní API**a pak vyberte **přidat**.
3. V **vybrat rozhraní API** rozevíracím seznamu vyberte *webapi1*.
4. V **vyberte obory** rozevírací seznam, vyberte **čtení** a **zápisu** obory, které jste dříve definovali.
5. Klikněte na **OK**.

Vaše aplikace je zaregistrovaná a může volat chráněné webové rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C k používání aplikace. Aplikace obdrží udělení autorizace z Azure AD B2C pro přístup k chráněné webové rozhraní API.
