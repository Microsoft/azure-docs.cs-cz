---
title: Registrace aplikace v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak zaregistrovat aplikaci pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fcebada4ef10c3e0bb298e9308d66ecb37247832
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999254"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>Registrace aplikace v Azure Active Directory B2C

K sestavení [aplikace](active-directory-b2c-apps.md) , který přijímá registrace a přihlašování uživatelů, musíte nejprve registrovat aplikaci s tenantem Azure AD B2C. Tento článek pomůže zaregistrovat aplikaci v tenantovi Azure Active Directory (Azure AD) B2C během několika minut. Jakmile budete hotovi, vaše aplikace bude zaregistrovaná pro použití v tenantovi Azure AD B2C.

## <a name="prerequisites"></a>Požadavky

Vlastního klienta získáte pomocí postupu v [vytvoření tenanta Azure Active Directory B2C](tutorial-create-tenant.md).

Výběr dalších kroků podle typu aplikace:

- [Registrace webové aplikace](#register-a-web-application)
- [Registrace webového rozhraní API](#register-a-web-api)
- [Registrace mobilní nebo nativní aplikace](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>Registrace webové aplikace

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **aplikací**a pak vyberte **přidat**.
4. Zadejte název aplikace. Například *testapp1*.
5. Pro **zahrnout webovou aplikaci / webové rozhraní API** a **povolit implicitní tok**vyberte **Ano**.
6. Pro **adresy URL odpovědi**, zadejte koncový bod kam by měl Azure AD B2C vrátí všechny tokeny, které vaše aplikace požaduje. Například můžete nastavit ji pro naslouchání lokálně na `https://localhost:44316`. Pokud ještě neznáte číslo portu, můžete zadat hodnotu zástupného symbolu a později změnit.
7. Klikněte na možnost **Vytvořit**.

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Pokud vaše aplikace volá webové rozhraní API zabezpečené pomocí Azure AD B2C, budete muset vytvořit tajný klíč aplikace.

1. Vyberte **klíče** a potom klikněte na tlačítko **vygenerovat klíč**. 
2. Vyberte **Uložit** zobrazíte klíče. Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.
3. Vyberte **přístup přes rozhraní API**, klikněte na tlačítko **přidat**a vyberte vaše webové rozhraní API a obory (oprávnění).

## <a name="register-a-web-api"></a>Registrace webové rozhraní API

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **aplikací**a pak vyberte **přidat**.
4. Zadejte název aplikace. Například *testapp2*.
5. Pro **zahrnout webovou aplikaci / webové rozhraní API** a **povolit implicitní tok**vyberte **Ano**.
6. Pro **adresy URL odpovědi**, zadejte koncový bod kam by měl Azure AD B2C vrátí všechny tokeny, které vaše aplikace požaduje. Například můžete nastavit ji pro naslouchání lokálně na `https://localhost:44316`. Pokud ještě neznáte číslo portu, můžete zadat hodnotu zástupného symbolu a později změnit.
7. Pro **identifikátor ID URI aplikace**, zadejte identifikátor použitý pro vaše webového rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
8. Klikněte na možnost **Vytvořit**.
9. Vyberte **publikované obory** na podle potřeby přidejte další obory. Ve výchozím nastavení `user_impersonation` rozsah je definován. `user_impersonation` Oboru umožňuje aplikacím přístup k tomuto rozhraní API jménem přihlášeného uživatele. Pokud chcete, `user_impersonation` obor je možné odebrat.

## <a name="register-a-mobile-or-native-application"></a>Registrace mobilní nebo nativní aplikace

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **aplikací**a pak vyberte **přidat**.
4. Zadejte název aplikace. Například *testapp3*.
5. Pro **zahrnout webovou aplikaci / webové rozhraní API**vyberte **ne**.
6. Pro **zahrnout nativního klienta**vyberte **Ano**.
7. Pro **identifikátor URI pro přesměrování**, zadejte [identifikátor URI přesměrování s vlastním schématem](active-directory-b2c-apps.md). Ujistěte se, zvolte na dobrý identifikátor URI přesměrování a nemusí obsahovat speciální znaky jako podtržítka.
8. Klikněte na možnost **Vytvořit**.

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Pokud vaše aplikace volá webové rozhraní API zabezpečené pomocí Azure AD B2C, budete muset vytvořit tajný klíč aplikace.

1. Vyberte **klíče** a potom klikněte na tlačítko **vygenerovat klíč**. 
2. Vyberte **Uložit** zobrazíte klíče. Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.
3. Vyberte **přístup přes rozhraní API**, klikněte na tlačítko **přidat**a vyberte vaše webové rozhraní API a obory (oprávnění).

## <a name="next-steps"></a>Další postup

Další informace o použití přístupových tokenů aplikace udělit oprávnění k rozhraním API v [dotazuje přístupové tokeny](active-directory-b2c-access-tokens.md)
