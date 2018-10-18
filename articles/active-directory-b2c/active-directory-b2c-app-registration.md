---
title: Registrace aplikace v Azure Active Directory B2C | Microsoft Docs
description: Postup registrace aplikace pomocí Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 6/13/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4f8a5b1ceda77ef254ad0c2afb7d2316581d778e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376392"
---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Registrace vaší aplikace

Tento rychlý start vám pomůže zaregistrovat aplikaci v tenantovi Microsoft Azure Active Directory (Azure AD) B2C během několika minut. Jakmile budete hotovi, vaše aplikace bude zaregistrovaná pro použití v tenantovi Azure AD B2C.

## <a name="prerequisites"></a>Požadavky

Chcete-li sestavit aplikaci, která podporuje registrace a přihlašování uživatelů, musíte aplikaci nejprve zaregistrovat pomocí klienta Azure Active Directory B2C. Vlastního klienta získáte pomocí návodu v tématu [Vytvoření klienta Azure AD B2C](active-directory-b2c-get-started.md).

Aplikace vytvořené na webu Azure Portal se musí spravovat ze stejného místa. Pokud upravíte aplikace Azure AD B2C pomocí PowerShellu nebo jiného portálu, stanou se nepodporované a nebudou s Azure AD B2C pracovat. Podrobnosti najdete v části věnující se [chybným aplikacím](#faulted-apps). 

V tomto článku se používají příklady, které vám pomůžou začít s našimi ukázkami. Více informací o těchto ukázkách najdete v dalších článcích.

## <a name="navigate-to-b2c-settings"></a>Přechod do nastavení B2C

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce tenanta B2C. 

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](../../includes/active-directory-b2c-portal-navigate-b2c-service.md)]

## <a name="choose-next-steps-based-on-your-application-type"></a>Výběr dalších kroků podle typu aplikace

* [Registrace webové aplikace](#register-a-web-app)
* [Registrace webového rozhraní API](#register-a-web-api)
* [Registrace mobilní nebo nativní aplikace](#register-a-mobile-or-native-app)
 
### <a name="register-a-web-app"></a>Registrace webové aplikace

[!INCLUDE [active-directory-b2c-register-web-app](../../includes/active-directory-b2c-register-web-app.md)]

### <a name="create-a-web-app-client-secret"></a>Vytvoření tajného klíče klienta webové aplikace

Pokud vaše webová aplikace volá webové rozhraní API zabezpečené pomocí Azure AD B2C, proveďte tyto kroky:
   1. Vytvořte Tajný klíč aplikace – přejděte do okna **Klíče** a klikněte na tlačítko **Vygenerovat klíč**. Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.
   2. Klikněte na **Přístup přes rozhraní API**, pak na **Přidat** a vyberte vaše webové rozhraní API a obory (oprávnění).

> [!NOTE]
> **Tajný klíč aplikace** je důležitý údaj zabezpečení a musí být řádně zabezpečen.
> 

[Přejít na **další kroky**](#next-steps)

### <a name="register-a-web-api"></a>Registrace webové rozhraní API

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Klikněte na **Publikované obory** a podle potřeby přidejte další obory. Ve výchozím nastavení je definovaný obor user_impersonation. Obor user_impersonation umožňuje jiným aplikacím přístup k tomuto rozhraní API jménem přihlášeného uživatele. Pokud chcete, můžete obor user_impersonation odebrat.

[Přejít na **další kroky**](#next-steps)

### <a name="register-a-mobile-or-native-app"></a>Registrace mobilní nebo nativní aplikace

[!INCLUDE [active-directory-b2c-register-mobile-native-app](../../includes/active-directory-b2c-register-mobile-native-app.md)]

[Přejít na **další kroky**](#next-steps)

## <a name="limitations"></a>Omezení

### <a name="choosing-a-web-app-or-api-reply-url"></a>Výběr adresy URL odpovědi webové aplikace nebo rozhraní API

Aktuálně je u aplikací zaregistrovaných pomocí Azure AD B2C omezená sada hodnot adresy URL odpovědi. Adresa URL odpovědi pro webové aplikace a služby musí začínat schématem `https` a všechny adresy URL odpovědi musí sdílet jednu doménu DNS. Například nemůžete zaregistrovat webovou aplikaci s některou z těchto adres URL odpovědi:

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Registrační systém porovnává celý název DNS stávající adresy URL odpovědi s názvem DNS adresy URL odpovědi, kterou přidáváte. Požadavek na přidání názvu DNS selže, pokud platí některá z následujících podmínek:

* Celý název DNS nové adresy URL odpovědi neodpovídá názvu DNS stávající adresy URL odpovědi.
* Celý název DNS nové adresy URL odpovědi není subdoménou stávající adresy URL odpovědi.

Pokud má aplikace například tuto adresu URL odpovědi:

`https://login.contoso.com`

Můžete ji přidat tímto způsobem:

`https://login.contoso.com/new`

V tomto případě se název DNS přesně shoduje. Nebo můžete provést toto:

`https://new.login.contoso.com`

V tomto případě odkazujete na subdoménu DNS login.contoso.com. Pokud chcete mít aplikaci s adresami URL odpovědi login-east.contoso.com a login-west.contoso.com, musíte tyto adresy URL odpovědi přidat v následujícím pořadí:

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

Druhé dvě adresy URL odpovědi můžete přidat, protože jsou subdoménami první adresy URL odpovědi contoso.com.

### <a name="choosing-a-native-app-redirect-uri"></a>Výběr identifikátoru URI přesměrování nativní aplikace

Existují dva důležité aspekty při výběru identifikátoru URI přesměrování pro mobilní/nativní aplikace:

* **Jedinečnost:** Schéma identifikátoru URI přesměrování by mělo být pro každou aplikaci jedinečné. V našem příkladu (com.onmicrosoft.contoso.appname://redirect/path) použijeme jako schéma com.onmicrosoft.contoso.appname. Doporučujeme používat tento vzor. Pokud dvě aplikace sdílejí stejné schéma, uživateli se zobrazí dialogové okno pro výběr aplikace. Pokud uživatel použije nesprávnou volbu, přihlášení se nezdaří.
* **Úplnost:** Identifikátor URI přesměrování musí mít schéma a cestu. Cesta musí obsahovat za doménou alespoň jedno lomítko (například //contoso/ funguje a //contoso selže).

Ujistěte se, že identifikátor URI přesměrování neobsahuje žádné speciální znaky jako podtržítka.

### <a name="faulted-apps"></a>Chybné aplikace

Aplikace B2C se NESMÍ upravovat:

* Na jiných portálech pro správu aplikací, jako je [Portál pro registraci aplikací](https://apps.dev.microsoft.com/).
* Pomocí rozhraní Graph API nebo PowerShellu.

Pokud aplikaci Azure AD B2C upravíte popsaným způsobem a pokusíte se ji znovu upravit v okně funkcí Azure AD B2C na webu Azure Portal, stane se chybnou aplikací a už ji nebude možné použít s Azure AD B2C. Je nutné aplikaci odstranit a znovu ji vytvořit.

Pokud chcete aplikaci odstranit, přejděte na [Portál pro registraci aplikací](https://apps.dev.microsoft.com/) a tam ji odstraňte. Aby byla aplikace viditelná, musíte být vlastníkem aplikace (nestačí být pouze správcem tenanta).

## <a name="next-steps"></a>Další postup

Jakmile budete mít aplikaci registrovanou pomocí Azure AD B2C, můžete absolvovat jeden z [našich kurzů pro rychlý start](active-directory-b2c-overview.md), který vám pomůže uvést ji do provozu.

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace ASP.NET s registrací, přihlášením a resetováním hesla](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
