---
title: Nastavení registrace a přihlášení s OpenID Connect pomocí služby Azure Active Directory B2C | Dokumentace Microsoftu
description: Nastavení registrace a přihlášení s OpenID Connect pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9f6b65a4253b9cc7c04f397dde7ecab9a64d5ae0
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845975"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s OpenID Connect pomocí služby Azure Active Directory B2C

>[!NOTE]
> Tato funkce je ve verzi Public Preview. Nepoužívejte tuto funkci v produkčním prostředí.


[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) je ověřovací protokol, postavený na OAuth 2.0, který umožňuje bezpečně přihlásit uživatele. Většina poskytovatelů identit, které používají tento protokol vyžadoval, jako například [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), jsou podporovány v Azure AD B2C. Tento článek vysvětluje, jak můžete přidat vlastní poskytovatele OpenID Connect identity do vašich toků uživatelů.


## <a name="add-the-identity-provider"></a>Přidat zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a potom klikněte na tlačítko **přidat**.
5. Pro **typ zprostředkovatele identit**vyberte **OpenID Connect (verze Preview)**.

## <a name="configure-the-identity-provider"></a>Konfigurace zprostředkovatele identity

Každý poskytovatele OpenID Connect identity popisuje dokumentu metadat, který obsahuje většinu informací potřebných k provedení přihlášení. To zahrnuje informace, jako jsou adresy URL k použití a umístění služby veřejné podpisového klíče. Dokument metadat OpenID Connect se nachází na koncový bod, který končí v vždy `.well-known\openid-configuration`. Pro poskytovatele OpenID Connect identity Pokud chcete přidat, zadejte jeho adresu URL metadat.

Umožňuje uživatelům přihlášení zprostředkovatele identity vyžaduje, aby registraci aplikace ve své službě vývojáři. Tato aplikace má ID, které se označuje jako **ID klienta** a **tajný kód klienta**. Zkopírujte tyto hodnoty od zprostředkovatele identity a zadejte do příslušných polí.

> [!NOTE]
> Tajný kód klienta je volitelný. Ale musíte zadat tajný klíč klienta, pokud chcete použít [tok autorizačního kódu](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), který používá tajný kód k výměně kód pro daný token.

Obor určuje informace a oprávnění, které pokud chcete shromažďovat z vašeho vlastního zprostředkovatele identity. Musí obsahovat OpenID Connect požadavky `openid` hodnotu oboru pro získání ID token od zprostředkovatele identity. Bez ID tokenu, nebudou se uživatelé moct pro přihlášení k Azure AD B2C pomocí vlastního zprostředkovatele identity. Další obory lze připojit odděleny mezerou. Dokumentaci vlastního zprostředkovatele identity Pokud chcete zobrazit, co může být jiné obory k dispozici.

Typ odpovědi, který popisuje, jaký druh informací se odešle zpět v počáteční volání `authorization_endpoint` z vlastního zprostředkovatele identity. Je možné následující typy odezvy:

- `code`: Jak je uvedeno [tok autorizačního kódu](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), kód se vrátí zpět do Azure AD B2C. Azure AD B2C pokračuje k volání `token_endpoint` k výměně kód pro daný token.
- `token`: Přístupový token se vrátí zpět do Azure AD B2C z vlastního zprostředkovatele identity.
- `id_token`: ID token se vrátí zpět do Azure AD B2C z vlastního zprostředkovatele identity.

Režim odpovědi definuje metodu, který se má použít k odesílání dat zpět z vlastního zprostředkovatele identity do Azure AD B2C. Je možné použít v následujících režimech odpovědi:

- `form_post`: Tento režim odpovědi se doporučuje pro zvýšení zabezpečení. Odpověď se přenášejí prostřednictvím HTTP `POST` metoda s kódem nebo token zakódována pomocí textu `application/x-www-form-urlencoded` formátu.
- `query`: Kód nebo token se vrátí jako parametr dotazu.

Nápověda domény je možné přeskočit přímo k přihlašovací stránce poskytovatele identitu, namísto toho, aby bylo možné uživatele vytvořit výběr v seznamu zprostředkovatelů identity k dispozici. Pokud chcete povolit tento druh chování, zadejte hodnotu pro nápovědu domény. Chcete-li přejít do vlastního zprostředkovatele identity, přidejte parametr `domain_hint=<domain hint value>` na konci vaší žádosti při volání Azure AD B2C pro přihlášení.

Po vlastní identitu odešle zprostředkovatel tokenu ID zpět do Azure AD B2C, Azure AD B2C musí být schopen namapovat deklarace identity z přijatého tokenu na deklarace, které Azure AD B2C rozpozná a použije. Pro každou z následující mapování naleznete v dokumentaci vlastního zprostředkovatele identity pochopit deklarace identity, které jsou vráceny zpět v tokenech zprostředkovatele identity:

- `User ID`: Zadejte deklaraci identity, která poskytuje jedinečný identifikátor pro přihlášeného uživatele.
- `Display Name`: Zadejte deklaraci identity, která poskytuje zobrazované jméno nebo příjmení uživatele.
- `Given Name`: Zadejte deklaraci identity, která poskytuje křestní jméno uživatele.
- `Surname`: Zadejte deklaraci identity, který obsahuje příjmení uživatele.
- `Email`: Zadejte deklaraci identity, která poskytuje e-mailovou adresu uživatele.

