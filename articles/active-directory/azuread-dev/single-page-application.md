---
title: Jednostránkové aplikace v Azure Active Directory
description: Popisuje, co jsou jednostránkové aplikace (jednostránkové) a základní informace o toku, registraci a vypršení platnosti tokenu pro tento typ aplikace.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154742"
---
# <a name="single-page-applications"></a>Jednostránkové aplikace

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Jednostránkové aplikace (jednostránkové) jsou obvykle strukturované jako prezentační vrstva JavaScriptu (front-end), která běží v prohlížeči, a back-end webového rozhraní API, který běží na serveru a implementuje obchodní logiku aplikace. Pokud chcete získat další informace o implicitním udělení autorizace a pomůžete se rozhodnout, jestli je to pro váš scénář aplikace správné, přečtěte si téma [Principy OAuth2 implicitního udělení grantu v Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

V tomto scénáři, když se uživatel přihlásí, používá front-end JavaScript [Active Directory Authentication Library pro JavaScript (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) a implicitní autorizační udělení k získání tokenu ID (id_token) ze služby Azure AD. Token se ukládá do mezipaměti a klient ho při volání do back-endu webového rozhraní API připojí k žádosti jako nosný token, který je zabezpečený pomocí middlewaru OWIN.

## <a name="diagram"></a>Diagram

![Diagram jednostránkové aplikace](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Tok protokolu

1. Uživatel přejde na webovou aplikaci.
1. Aplikace vrátí front-end JavaScriptu (prezentační vrstvu) do prohlížeče.
1. Uživatel zahájí přihlášení, například kliknutím na odkaz pro přihlášení. Prohlížeč pošle koncovému bodu autorizace Azure AD požadavek na token ID. Tato žádost obsahuje ID aplikace a adresu URL odpovědi v parametrech dotazu.
1. Azure AD ověří adresu URL odpovědi před registrovanou adresou URL odpovědi, která byla nakonfigurovaná v Azure Portal.
1. Uživatel se přihlásí na přihlašovací stránce.
1. Pokud je ověření úspěšné, Azure AD vytvoří token ID a vrátí ho jako fragment adresy URL (#) k adrese URL pro odpověď aplikace. V případě produkční aplikace by tato adresa URL odpovědi měla být HTTPS. Vrácený token obsahuje deklarace identity uživatele a služby Azure AD, které vyžaduje aplikace k ověření tokenu.
1. Kód klienta JavaScriptu běžící v prohlížeči extrahuje token z odpovědi pro použití při zabezpečení volání back-endu webového rozhraní API aplikace.
1. Prohlížeč volá back-end webového rozhraní API aplikace v autorizační hlavičce s tokenem ID. Služba ověřování Azure AD vydá token ID, který se dá použít jako nosný token, pokud je prostředek stejný jako ID klienta (v tomto případě je to pravda jako webové rozhraní API vlastní back-end aplikace).

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na [ukázky kódu pro scénáře aplikací s jednou stránkou](sample-v1-code.md#single-page-applications). Nezapomeňte pravidelně kontrolovat, jak se často přidávají nové ukázky.

## <a name="app-registration"></a>Registrace aplikace

* Jeden tenant – Pokud vytváříte aplikaci jenom pro vaši organizaci, musí být zaregistrovaná v adresáři vaší společnosti pomocí Azure Portal.
* Vícenásobný tenant – Pokud vytváříte aplikaci, kterou můžou používat uživatelé mimo vaši organizaci, musí být zaregistrované v adresáři vaší společnosti, ale musí být zaregistrované v adresáři každé organizace, který bude aplikaci používat. Aby vaše aplikace byla ve svém adresáři k dispozici, můžete pro zákazníky zahrnout proces registrace, který jim umožní vyjádřit souhlas s vaší aplikací. Při registraci do aplikace se zobrazí dialogové okno, které zobrazuje oprávnění, která aplikace vyžaduje, a pak možnost souhlasu. V závislosti na požadovaných oprávněních může být vyžadováno poskytnutí souhlasu správce v jiné organizaci. Když uživatel nebo správce souhlasí, aplikace se zaregistruje ve svém adresáři.

Po registraci aplikace musí být nakonfigurovaná tak, aby používala protokol OAuth 2,0 implicitní udělení. Ve výchozím nastavení je tento protokol pro aplikace zakázaný. Pokud chcete pro vaši aplikaci povolit protokol implicitního udělení OAuth2, upravte jeho manifest aplikace z Azure Portal a nastavte hodnotu "oauth2AllowImplicitFlow" na hodnotu true. Další informace naleznete v tématu [manifest aplikace](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Použití ADAL.js pomáhá:

* Aktualizace tokenu, jehož platnost vypršela
* Vyžádání přístupového tokenu pro volání prostředku webového rozhraní API

Po úspěšném ověření služba Azure AD zapíše soubor cookie v prohlížeči uživatele, aby navázal relaci. Všimněte si, že relace existuje mezi uživatelem a službou Azure AD (ne mezi uživatelem a webovou aplikací). Po vypršení platnosti tokenu ADAL.js používá tuto relaci k tichému získání jiného tokenu. ADAL.js používá skrytý prvek iFrame k odeslání a přijetí žádosti pomocí protokolu OAuth implicitního udělení. ADAL.js může použít stejný mechanismus i k tichému získání přístupových tokenů pro jiné prostředky webového rozhraní API, které aplikace volá, pokud tyto prostředky podporují sdílení prostředků mezi zdroji (CORS), jsou zaregistrované v adresáři uživatele a při přihlášení se uživateli zadal jakýkoli požadovaný souhlas.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [typech a scénářích aplikací](app-types.md)
* Seznamte se se [základy ověřování](v1-authentication-scenarios.md) Azure AD
