---
title: Co je Azure Active Directory B2C? | Dokumenty Microsoft
description: Přečtěte si, jak pomocí Azure Active Directory B2C vytvořit a spravovat prostředí identity, jako je přihlášení k registraci, a Správa profilů ve vaší aplikaci.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ca636079439f811a887d16b627473e7d73930799
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065698"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co je Azure Active Directory B2C?

Azure Active Directory B2C (Azure AD B2C) je služba pro správu identit od firmy do spotřebitele. Tato služba umožňuje přizpůsobit a řídit, jak budou uživatelé zabezpečeně pracovat s webovými, stolními, mobilními nebo jednostránkovémi aplikacemi. Pomocí Azure AD B2C se uživatelé můžou zaregistrovat, přihlašovat, resetovat hesla a upravovat profily. Azure AD B2C implementuje formulář protokolů OpenID Connect a OAuth 2,0. Důležitým klíčem při implementaci těchto protokolů jsou tokeny zabezpečení a jejich deklarace identity, které umožňují poskytovat zabezpečený přístup k prostředkům.

*Cesta uživatele* je požadavek, který určuje zásadu, která řídí chování způsobu, jakým uživatel a aplikace budou pracovat s Azure AD B2C. K dispozici jsou dvě cesty pro definování cest uživatelů v Azure AD B2C.

Pokud jste vývojář aplikací s odborností identity nebo bez něj, můžete se rozhodnout definovat běžné toky identity uživatelů pomocí Azure Portal. Pokud jste odborník na identitu, systémový integrátor, konzultant nebo interní tým identity, je vhodné, abyste mohli používat toky OpenID Connect a lépe porozuměli poskytovatelům identity a ověřování založeném na deklaracích. můžete zvolit vlastní zásady založené na XML.

Než začnete s definováním cesty pro uživatele, musíte vytvořit klienta Azure AD B2C a zaregistrovat svoji aplikaci a rozhraní API v tenantovi. Po dokončení těchto úloh můžete začít s definicí cesty uživatelů buď pomocí uživatelských toků, nebo vlastních zásad. Volitelně můžete také přidat nebo změnit poskytovatele identity nebo přizpůsobit způsob, jakým uživatel pracuje na cestě.

## <a name="protocols-and-tokens"></a>Protokoly a tokeny

Azure AD B2C podporuje [protokoly OpenID Connect a OAuth 2,0](active-directory-b2c-reference-protocols.md) pro cesty uživatelů. V implementaci OpenID Connect v Azure AD B2C zahajuje aplikace tuto cestu uživatele zasíláním žádostí o ověření do Azure AD B2C.

Výsledkem požadavku na Azure AD B2C je token zabezpečení, jako je token [ID nebo přístupový token](active-directory-b2c-reference-tokens.md). Tento token zabezpečení definuje identitu uživatele. Tokeny jsou přijímány z Azure AD B2C koncových bodů `/token` , `/authorize` jako je například nebo koncový bod. Z těchto tokenů můžete získat přístup k deklaracím, které se dají použít k ověření identity a povolení přístupu k zabezpečeným prostředkům.

## <a name="tenants-and-applications"></a>Klienti a aplikace

V Azure AD B2C *tenant* představuje vaši organizaci a je adresářem uživatelů. Každý tenant Azure AD B2C se odlišuje a je oddělený od jiných tenantů Azure AD B2C. Je možné, že již máte klienta Azure Active Directory, Azure AD B2C tenant je jiný tenant. Tenant obsahuje informace o uživatelích, kteří se zaregistrovali k používání vaší aplikace. Například hesla, data profilu a oprávnění. Další informace najdete v tématu [kurz: Vytvořte tenanta](tutorial-create-tenant.md)Azure Active Directory B2C.

Předtím, než aplikaci nakonfigurujete tak, aby používala Azure AD B2C, je nutné ji nejprve zaregistrovat v tenantovi pomocí Azure Portal. Proces registrace shromáždí a přiřadí vaší aplikaci hodnoty. Tyto hodnoty zahrnují ID aplikace, které jedinečně identifikuje aplikaci a identifikátor URI pro přesměrování, který se používá k přímému směrování odpovědí zpět do aplikace.

Interakce každé aplikace probíhá podle podobného vzoru:

1. Aplikace nasměruje uživatele na spuštění zásady.
2. Uživatel vykoná zásadu podle její definice.
3. Aplikace obdrží token.
4. Aplikace používá token k pokusu o přístup k prostředku.
5. Server prostředků ověří token, aby ověřil, že je možné udělit přístup.
6. Aplikace pravidelně aktualizuje token.

Chcete-li zaregistrovat webovou aplikaci, proveďte kroky v [kurzu: Zaregistrujte aplikaci, abyste mohli povolit registraci a přihlašování pomocí Azure AD B2C](tutorial-register-applications.md). Můžete také [Přidat aplikaci webového rozhraní API do tenanta Azure Active Directory B2C](add-web-application.md) nebo [Přidat nativní klientskou aplikaci do svého klienta Azure Active Directory B2C](add-native-application.md).

## <a name="user-journeys"></a>Cesty uživatelů

Zásady v cestě uživatele je možné definovat jako [tok uživatele](active-directory-b2c-reference-policies.md) nebo [vlastní zásady](active-directory-b2c-overview-custom.md). Předdefinované toky uživatelů pro nejběžnější úkoly identity, jako je registrace, přihlašování a úpravy profilu, jsou k dispozici v Azure Portal.

Cesty uživatelů umožňují řídit chování konfigurací následujících nastavení:

- Účty sociálních sítí, které uživatel používá k registraci aplikace
- Data shromážděná od uživatele, jako je křestní jméno nebo poštovní směrovací číslo
- Ověřování pomocí služby Multi-Factor Authentication
- Vzhled a chování stránek
- Informace vrácené do aplikace

Vlastní zásady jsou konfigurační soubory, které definují chování [architektury identity Experience Framework](trustframeworkpolicy.md) ve vašem tenantovi Azure AD B2C. Architektura prostředí identity je základní platforma, která vytváří vztah důvěryhodnosti s více stranami a dokončuje kroky v cestě uživatele.

Vlastní zásady je možné změnit tak, aby prováděly řadu úloh. Vlastní zásada je jeden nebo více souborů ve formátu XML, které odkazují na sebe navzájem v hierarchickém řetězu. K dispozici je [Startovní sada](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) pro vlastní zásady, které umožňují běžné úkoly identity.

Vlastní zásady nebo uživatelské toky různých typů se ve vašem tenantovi Azure AD B2C používají podle potřeby a je možné je znovu použít napříč aplikacemi. Tato flexibilita umožňuje definovat a upravovat prostředí identity uživatelů s minimálními nebo žádnými změnami kódu. Zásady se používají tak, že se do žádostí o ověření pomocí protokolu HTTP přidá speciální parametr dotazu. Pokud chcete vytvořit vlastní zásadu, přečtěte si téma Začínáme [s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Zprostředkovatelé identity

V aplikacích můžete chtít povolit uživatelům, aby se přihlásili pomocí různých zprostředkovatelů identity. *Zprostředkovatel identity* vytváří, uchovává a spravuje informace o identitě při poskytování služeb ověřování aplikacím. Zprostředkovatele identity, které jsou podporovány Azure AD B2C, můžete přidat pomocí Azure Portal.

V aplikaci obvykle používáte pouze jednoho poskytovatele identity, ale máte možnost přidat další. Pokud chcete ve svém tenantovi Azure AD B2C nakonfigurovat poskytovatele identity, musíte nejdřív vytvořit aplikaci na webu pro vývojáře zprostředkovatele identity a potom zaznamenat identifikátor aplikace nebo identifikátor klienta a heslo nebo tajný klíč klienta od poskytovatele identity. aplikaci, kterou vytvoříte. Tento identifikátor a heslo potom slouží ke konfiguraci vaší aplikace.

Následující články popisují postup přidání některých běžných zprostředkovatelů identity do toků uživatelů:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Účet Microsoft](active-directory-b2c-setup-msa-app.md)

Následující články popisují postup přidání některých běžných zprostředkovatelů identity do vlastních zásad:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Účet Microsoft](active-directory-b2c-custom-setup-msa-idp.md)

Další informace najdete v tématu [kurz: Přidejte do svých aplikací zprostředkovatele identity v Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Přizpůsobení stránky

Většinu obsahu HTML a CSS, který je prezentován zákazníkům v cestě uživatele, lze řídit. Pomocí přizpůsobení stránky můžete přizpůsobit vzhled a chování všech vlastních zásad nebo toku uživatelů. Pomocí této funkce pro vlastní nastavení můžete udržovat konzistenci značky a vizuální konzistenci mezi vaší aplikací a službou Azure AD B2C.

Azure AD B2C spouští kód v prohlížeči uživatele a používá moderní přístup nazvaný sdílení prostředků mezi zdroji (CORS). Nejprve zadáte v zásadě s vlastním obsahem ve formátu HTML adresu URL. Azure AD B2C sloučí prvky uživatelského rozhraní s obsahem HTML načteným z vaší adresy URL a pak zobrazí stránku uživateli.

Do Azure AD B2C se parametry odesílají v řetězci dotazu. Předáním parametru do koncového bodu HTML se dynamicky změní obsah stránky. Například můžete změnit obrázek pozadí na přihlašovací stránce nebo na přihlašovací stránce na základě parametru, který předáte z webové nebo mobilní aplikace.

Pokud chcete přizpůsobit stránky v toku uživatelů, přečtěte si téma [kurz: Přizpůsobení rozhraní uživatelského prostředí v Azure Active Directory B2C](tutorial-customize-ui.md). Pokud chcete přizpůsobit stránky ve vlastních zásadách, přečtěte si téma [přizpůsobení uživatelského rozhraní aplikace pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) nebo [Konfigurace uživatelského rozhraní s dynamickým obsahem pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Materiály pro vývojáře

### <a name="client-applications"></a>Klientské aplikace

Máte možnost zvolit si aplikace pro [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md)a .NET, a to mimo jiné. Azure AD B2C tyto akce umožňuje současně chránit identity uživatelů.

Pokud jste vývojář webové aplikace v ASP.NET, nastavte svoji aplikaci tak, aby ověřovala účty pomocí kroků v [kurzu: Povolit webové aplikaci ověřování s účty pomocí Azure AD B2C](active-directory-b2c-tutorials-web-app.md).

Pokud jste vývojář desktopové aplikace, nastavte svoji aplikaci tak, aby ověřovala účty pomocí kroků v [tomto kurzu: Povolit aplikaci klasické pracovní plochy ověřování s účty pomocí Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md).

Pokud jste samoobslužným vývojářem aplikací pomocí Node. js, nastavte svoji aplikaci na ověřování účtů pomocí kroků v [tomto kurzu: Povolte jednostránkovou aplikaci pro ověřování s účty pomocí Azure AD B2C](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>API
Pokud klient nebo webové aplikace potřebují volat rozhraní API, můžete nastavit zabezpečený přístup k těmto prostředkům v Azure AD B2C.

Pokud jste vývojář webové aplikace v ASP.NET, nastavte svou aplikaci tak, aby volala chráněné rozhraní API pomocí kroků v [tomto kurzu: Udělte přístup k webovému rozhraní API ASP.NET pomocí](active-directory-b2c-tutorials-web-api.md)Azure Active Directory B2C.

Pokud jste vývojář desktopové aplikace, nastavte svou aplikaci tak, aby volala chráněné rozhraní API pomocí kroků v [tomto kurzu: Udělte přístup k webovému rozhraní API Node. js z desktopové aplikace pomocí](active-directory-b2c-tutorials-desktop-app-webapi.md)Azure Active Directory B2C.

Pokud jste samoobslužnou aplikaci pro vývoj aplikací pomocí Node. js, nastavte aplikaci pro ověřování účtů pomocí kroků v [tomto kurzu: Udělte přístup k ASP.NET Core webovému rozhraní API z jednostránkové aplikace pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

Do aplikací v Azure AD B2C můžete přidat vlastní kód na straně klienta JavaScriptu. Chcete-li nastavit jazyk JavaScript v aplikaci, definujte [rozložení stránky](page-layout.md) a povolte [JavaScript](javascript-samples.md) ve vašich uživatelských tocích nebo vlastních zásadách.

### <a name="user-accounts"></a>Uživatelské účty

Mnoho běžných úloh správy tenantů je potřeba provést programově. Primárním příkladem je Správa uživatelů. Je možné, že budete muset migrovat existující úložiště uživatelů na klienta Azure AD B2C. Je možné, že budete chtít hostovat registraci uživatele na vlastní stránce a vytvořit v adresáři Azure AD B2C uživatelské účty na pozadí. Tyto typy úloh vyžadují možnost vytvářet, číst, aktualizovat a odstraňovat uživatelské účty. Tyto úlohy můžete provádět pomocí [Graph API služby Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Další postup

Začněte konfigurovat svoji aplikaci pro prostředí registrace a přihlášení – pokračujte tímto kurzem.

> [!div class="nextstepaction"]
> [Kurz: Vytvoření tenanta Azure Active Directory B2C](tutorial-create-tenant.md)
