---
title: Jednostránkové aplikace ve službě Azure Active Directory
description: Popisuje, co jsou jednostránkové aplikace (SP) a základy toku protokolu, registrace a vypršení platnosti tokenu pro tento typ aplikace.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154742"
---
# <a name="single-page-applications"></a>Jednostránkové aplikace

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Jednostránkové aplikace (SP) jsou obvykle strukturovány jako prezentační vrstva JavaScriptu (front-end), která běží v prohlížeči, a back-end webového rozhraní API, který běží na serveru a implementuje obchodní logiku aplikace. Další informace o implicitním udělení autorizace a pomoc s rozhodováním o tom, jestli je to správné pro váš scénář aplikace, najdete [v tématu Principy implicitního grantového toku OAuth2 ve službě Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

V tomto scénáři při přihlášení uživatele používá front-end JavaScript [u JavaScriptu pro JavaScript (ADAL). JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) a implicitní udělení autorizace získat token ID (id_token) z Azure AD. Token je uložen do mezipaměti a klient jej připojí k požadavku jako token nosiče při volání back-endu webového rozhraní API, který je zabezpečen pomocí middlewaru OWIN.

## <a name="diagram"></a>Diagram

![Jednostránkový aplikační diagram](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Tok protokolu

1. Uživatel přejde do webové aplikace.
1. Aplikace vrátí javascriptový front-end (prezentační vrstvu) do prohlížeče.
1. Uživatel zahájí přihlášení, například kliknutím na přihlašovací odkaz. Prohlížeč odešle GET do koncového bodu autorizace Azure AD požádat o token ID. Tento požadavek obsahuje ID aplikace a adresu URL odpovědi v parametrech dotazu.
1. Azure AD ověří adresu URL odpovědi proti registrované adrese URL odpovědi, která byla nakonfigurovaná na webu Azure Portal.
1. Uživatel se přihlásí na přihlašovací stránce.
1. Pokud je ověření úspěšné, Azure AD vytvoří token ID a vrátí jej jako fragment adresy URL na adresu URL aplikace. Pro produkční aplikaci by tato adresa URL odpovědi měla být HTTPS. Vrácený token obsahuje deklarace identity o uživateli a azure ad, které jsou vyžadovány aplikací k ověření tokenu.
1. Kód klienta JavaScript usměrňovaný v prohlížeči extrahuje token z odpovědi, která se použije při zabezpečení volání back-endu webového rozhraní API aplikace.
1. Prohlížeč volá back-end webového rozhraní API aplikace s tokenem ID v hlavičce autorizace. Služba ověřování Azure AD vydává token ID, který lze použít jako token nosiče, pokud je prostředek stejný jako ID klienta (v tomto případě to platí, protože webové rozhraní API je vlastní back-end aplikace).

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na [ukázky kódu pro scénáře jednostránkové aplikace](sample-v1-code.md#single-page-applications). Ujistěte se, že kontrolovat zpět často jako nové vzorky jsou přidávány často.

## <a name="app-registration"></a>Registrace aplikací

* Jeden tenant – pokud vytváříte aplikaci pouze pro vaši organizaci, musí být zaregistrovaná v adresáři vaší společnosti pomocí portálu Azure.
* Víceklient – pokud vytváříte aplikaci, kterou mohou používat uživatelé mimo vaši organizaci, musí být zaregistrována v adresáři vaší společnosti, ale musí být také registrována v adresáři každé organizace, který bude aplikaci používat. Chcete-li, aby vaše aplikace byla k dispozici ve svém adresáři, můžete zahrnout proces registrace pro zákazníky, který jim umožní souhlas s vaší aplikací. Když se zaregistrují pro vaši aplikaci, zobrazí se dialogové okno, které zobrazuje oprávnění, která aplikace vyžaduje, a pak možnost souhlasu. V závislosti na požadovaných oprávněních může být správce v jiné organizaci požádán o udělení souhlasu. Když uživatel nebo správce souhlasí, aplikace je registrována v jejich adresáři.

Po registraci aplikace musí být nakonfigurován a používat implicitní grantový protokol OAuth 2.0. Ve výchozím nastavení je tento protokol pro aplikace zakázán. Chcete-li povolit implicitní grantový protokol OAuth2 pro vaši aplikaci, upravte jeho manifest aplikace z portálu Azure a nastavte hodnotu "oauth2AllowImplicitFlow" na hodnotu true. Další informace naleznete v tématu [Manifest aplikace](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Použití ADAL.js pomáhá s:

* Aktualizace tokenu s prošlou platností
* Vyžádání přístupového tokenu pro volání prostředku webového rozhraní API

Po úspěšném ověření Azure AD zapíše soubor cookie v prohlížeči uživatele k vytvoření relace. Všimněte si, že relace existuje mezi uživatelem a Azure AD (nikoli mezi uživatelem a webovou aplikací). Když vyprší platnost tokenu, ADAL.js používá tuto relaci tiše získat jiný token. Soubor ADAL.js používá skrytý iFrame k odeslání a přijetí požadavku pomocí implicitního grantového protokolu OAuth. Soubor ADAL.js může také použít stejný mechanismus k tichému získání přístupových tokenů pro jiné prostředky webového rozhraní API, které aplikace volá, pokud tyto prostředky podporují sdílení prostředků mezi zdroji (CORS), jsou registrovány v adresáři uživatele a byl vyžadován souhlas uživatelem během přihlašování.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [typech aplikací a scénářích](app-types.md)
* Informace o [základech ověřování](v1-authentication-scenarios.md) Azure AD
