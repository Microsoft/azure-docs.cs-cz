---
title: Co je Azure Active Directory B2C? | Dokumenty Microsoft
description: Další informace o tom, jak vytvořit a spravovat prostředí pro identity, jako je registrace přihlášení a Správa profilů ve vaší aplikaci pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9e01ba8ae53dbcca686a9844600a5df416a685ae
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455496"
---
# <a name="what-is-azure-active-directory-b2c"></a>Co je Azure Active Directory B2C?

Azure Active Directory (Azure AD) B2C je služba pro správu identit. Tato služba umožňuje přizpůsobení a řízení, jak uživatelé bezpečně komunikovat s webových, desktopových, mobilních nebo jednostránkové aplikace. Pomocí služby Azure AD B2C, uživatelé zaregistrovat, přihlaste se, resetování hesel a upravit profily. Azure AD B2C implementuje formu protokoly OpenID Connect a OAuth 2.0. Je důležité klíč v implementaci těchto protokolů tokeny zabezpečení a jejich deklarací identity, které vám umožní poskytnout zabezpečený přístup k prostředkům.

A *cesty uživatele* se žádosti, která určuje zásadu, která řídí chování uživatele a aplikaci interakci s Azure AD B2C. Dvě cesty jsou k dispozici pro definování cesty uživatele v Azure AD B2C. 

Pokud jste vývojář aplikací s nebo bez znalosti identity, můžete definovat běžných toků identity uživatele pomocí webu Azure portal. Pokud se identitu professional, systémový integrátor, konzultant, nebo týmu interní identity, obeznámeni s OpenID Connect toky a pochopit poskytovatelů identit a ověřování nezaloženého na deklaracích, můžete zvolit vlastní zásady založené na XML.

Než začnete definicí cesty uživatele, budete muset vytvořit tenanta Azure AD B2C a Zaregistrujte svoji aplikaci a rozhraní API v tenantovi. Po dokončení těchto úloh, můžete začít definovat cesty uživatele se toky uživatelů nebo vlastními zásadami. Můžete také v případě potřeby, přidat nebo změnit zprostředkovatele identity nebo určit, jak se uživatelské prostředí cesty.

## <a name="protocols-and-tokens"></a>Protokoly a tokeny

Azure AD B2C podporuje [protokoly OpenID Connect a OAuth 2.0](active-directory-b2c-reference-protocols.md) pro cesty, uživatele. V implementaci OpenID Connect v Azure AD B2C zahajuje aplikace tuto cestu uživatele zasíláním žádostí o ověření do Azure AD B2C. 

Výsledek požadavku na Azure AD B2C je token zabezpečení, jako například [ID token nebo přístupového tokenu](active-directory-b2c-reference-tokens.md). Tento token zabezpečení definuje identitu uživatele. Tokeny byly přijaty z koncových bodů Azure AD B2C, například `/token` nebo `/authorize` koncového bodu. Z těchto tokenů můžete použít deklarace identity, které slouží k ověření identity a povolit přístup k zabezpečeným prostředkům.

## <a name="tenants-and-applications"></a>Klientů a aplikací

V Azure AD B2C *tenanta* reprezentuje vaši organizaci a jedná se o adresář uživatele. Každý tenant Azure AD B2C se odlišuje a je oddělený od jiných tenantů Azure AD B2C. Už můžete mít tenanta Azure Active Directory, je tenant Azure AD B2C na jiného tenanta. Tenant obsahuje informace o uživatele, kteří si zaregistrovali službu, aby používali vaši aplikaci. Například hesla, data profilu a oprávnění. Další informace najdete v tématu [kurzu: Vytvoření tenanta Azure Active Directory B2C](tutorial-create-tenant.md).

Před konfigurací aplikace pomocí Azure AD B2C, musíte nejprve zaregistrovat v tenantovi pomocí webu Azure portal. Proces registrace shromáždí a přiřadí vaší aplikaci hodnoty. Tyto hodnoty zahrnují ID aplikace, které jednoznačně identifikuje aplikaci a přesměrovat identifikátor URI, který se používá ke směrování odpovědí zpět do aplikace.

Interakce každé aplikace probíhá podle podobného vzoru:

1. Aplikace uživatele přesměruje na spuštění zásad.
2. Uživatel vykoná zásadu podle její definice.
3. Aplikace obdrží token.
4. Aplikace používá token, který se pokouší o přístup k prostředku.
5. Server prostředků ověří token k ověření, že můžete udělit přístup.
6. Aplikace pravidelně aktualizuje token.

Pokud chcete zaregistrovat webovou aplikaci, proveďte kroky v [kurzu: Registrace aplikace k povolení registrace a přihlášení pomocí služby Azure AD B2C](tutorial-register-applications.md). Můžete také [přidat webovou aplikaci s rozhraním API pro vašeho tenanta Azure Active Directory B2C](add-web-application.md) nebo [přidat nativní klientské aplikace do svého tenanta Azure Active Directory B2C](add-native-application.md).

## <a name="user-journeys"></a>Cesty uživatele

Zásady v cestě uživatele může být definován jako [tok uživatele](active-directory-b2c-reference-policies.md) nebo [vlastní zásady](active-directory-b2c-overview-custom.md). Předdefinované toky uživatelů pro nejběžnější úkoly identity, jako například registrace, přihlašování a úpravy profilu, jsou k dispozici na webu Azure Portal.

Cesty uživatele umožňují řídit chování konfigurace následujících nastavení:

- Účtů na sociálních sítích, které uživatel použije k registraci pro aplikaci
- Data shromážděná z uživatele, například křestní jméno nebo PSČ
- Ověřování pomocí služby Multi-Factor Authentication
- Vzhled a chování stránek
- Informace vrácené do aplikace

Vlastní zásady jsou konfigurační soubory, které definují chování [architekturu rozhraní identit](trustframeworkpolicy.md) ve vašem tenantovi Azure AD B2C. Architekturu rozhraní identit je základní platformy, která vytváří více stran vztah důvěryhodnosti a dokončení kroků v cestě uživatele. 

Vlastní zásady je možné změnit tak, aby prováděly řadu úloh. Vlastní zásada je jeden nebo více souborů ve formátu XML, které odkazují na sebe navzájem v hierarchickém řetězu. A [starter pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) je k dispozici pro a umožnit tak běžné úkoly identity. 

Vlastní zásady nebo toky uživatelů pro různé typy se používají ve vašem tenantovi Azure AD B2C, podle potřeby a lze opětovně použít napříč aplikacemi. Díky této flexibilitě umožňuje definovat a upravovat uživatelské prostředí identit s minimálními nebo žádnými změnami kódu. Zásady se používají tak, že se do žádostí o ověření pomocí protokolu HTTP přidá speciální parametr dotazu. Vytvoření vlastní zásady, najdete v tématu [začít pracovat s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Zprostředkovatelé identit 

Ve svých aplikacích můžete umožnit uživatelům přihlášení pomocí poskytovatelů jiná identita. *Zprostředkovatele identity* vytváří, udržuje a spravuje informace o identitě současně ověřovací služby do aplikací. Můžete přidat zprostředkovatelů identity, které jsou podporovány službou Azure AD B2C na webu Azure portal. 

Obvykle používají pouze jeden poskytovatel identity ve vaší aplikaci, ale máte možnost pro přidání dalších. Konfigurace zprostředkovatele identity ve vašem tenantovi Azure AD B2C, nejprve vytvoříte aplikaci na webu vývojáře poskytovatele identit, a potom jste si poznamenejte identifikátor aplikace nebo identifikátor klienta a heslo nebo klienta, tajného kódu od zprostředkovatele identity aplikaci, kterou vytvoříte. Tento identifikátor a heslo potom slouží ke konfiguraci vaší aplikace. 

Následující články popisují postup pro přidání některé běžné poskytovatele identity toky uživatelů:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Účet Microsoft](active-directory-b2c-setup-msa-app.md)

Následující články popisují postup pro přidání některé běžné poskytovatele identity do vlastní zásady:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Účet Microsoft](active-directory-b2c-custom-setup-msa-idp.md)

Další informace najdete v tématu [kurzu: Přidat zprostředkovatele identity pro vaše aplikace v Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Přizpůsobení stránky

Většina obsahu HTML a CSS, která se zobrazí na zákazníky v cestě uživatele se dát řídit. Pomocí vlastního nastavení této stránky můžete přizpůsobit vzhled a chování vlastní tok zásad nebo uživatele. Pomocí této funkce pro vlastní nastavení můžete udržovat konzistenci značky a vizuální konzistenci mezi vaší aplikací a službou Azure AD B2C. 

Azure AD B2C spustí kód v prohlížeči uživatele a využívá moderní přístup a volá sdílení prostředků mezi zdroji (CORS). Nejprve zadáte v zásadě s vlastním obsahem ve formátu HTML adresu URL. Azure AD B2C sloučí prvky uživatelského rozhraní s obsahem HTML, který je načten z vaší adresy URL a pak zobrazí na stránce pro uživatele.

Do Azure AD B2C se parametry odesílají v řetězci dotazu. Předáním parametru do koncového bodu HTML se dynamicky změní obsah stránky. Například můžete změnit obrázek pozadí na stránce registrace nebo přihlašování založené na parametr předat z vašich webových nebo mobilních aplikací.

Přizpůsobit stránky v toku uživatele, přečtěte si článek [kurzu: Přizpůsobení rozhraní uživatelského prostředí v Azure Active Directory B2C](tutorial-customize-ui.md). Přizpůsobit stránky ve vlastních zásadách, přečtěte si článek [přizpůsobit uživatelské rozhraní vaší aplikace pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) nebo [konfigurace uživatelského rozhraní s dynamickým obsahem pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Materiály pro vývojáře

### <a name="client-applications"></a>Klientské aplikace

Máte taky možnost výběru žádostí o [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md)a .NET, mimo jiné. Azure AD B2C umožňuje tyto akce současně k ochraně identity vašich uživatelů ve stejnou dobu.

Pokud jste vývojáři ASP.NET webové aplikace, nastavení aplikace k ověřování účtů pomocí postupu v [kurzu: Povolení ověřování účtů pomocí Azure AD B2C webovou aplikaci](active-directory-b2c-tutorials-web-app.md).

Pokud jste vývojář, aplikace klasické pracovní plochy, nastavení aplikace k ověřování účtů pomocí postupu v [kurzu: Povolení ověřování účtů pomocí Azure AD B2C aplikace klasické pracovní plochy](active-directory-b2c-tutorials-desktop-app.md).

Pokud jste vývojář jednostránkové aplikace využívající Node.js, nastavení aplikace k ověřování účtů pomocí postupu v [kurzu: Povolit jednostránkovou aplikaci k ověřování účtů pomocí Azure AD B2C](active-directory-b2c-tutorials-spa.md).

### <a name="apis"></a>Rozhraní API
Pokud klient nebo webových aplikací, vyžadují volání rozhraní API, můžete nastavit zabezpečený přístup k těmto prostředkům v Azure AD B2C.

Pokud jste vývojáři ASP.NET webové aplikace, nastavení aplikace volat chráněné rozhraní API pomocí postupu v [kurzu: Udělení přístupu k webovému rozhraní ASP.NET API pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md).

Pokud jste vývojář, aplikace klasické pracovní plochy, nastavení aplikace volat chráněné rozhraní API pomocí postupu v [kurzu: Udělení přístupu k webovému rozhraní API Node.js z desktopové aplikace pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md).

Pokud jste vývojář jednostránkové aplikace využívající Node.js, nastavení aplikace k ověřování účtů pomocí postupu v [kurzu: Udělení přístupu k webovému rozhraní API ASP.NET Core z jednostránkové aplikace pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md).

### <a name="javascript"></a>JavaScript

Můžete přidat vlastní kód jazyka JavaScript na straně klienta pro vaše aplikace v Azure AD B2C. Nastavení jazyka JavaScript v aplikaci, můžete definovat [stránku smlouvy](page-contract.md) a povolit [JavaScript](javascript-samples.md) v toky uživatelů nebo vlastními zásadami.

### <a name="user-accounts"></a>Uživatelské účty

Mnoho běžných úloh správy klientů je třeba provést prostřednictvím kódu programu. Primární příklad je Správa uživatelů. Potřebujete migrovat existující úložiště uživatele do tenanta služby Azure AD B2C. Můžete hostovat registrace uživatele na vlastní stránce a vytvořte uživatelské účty ve vašem adresáři Azure AD B2C na pozadí. Tyto druhy úkonů vyžadují možnost vytvářet, číst, aktualizovat a odstraňovat uživatelské účty. Tyto úkoly můžete provést pomocí [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

## <a name="next-steps"></a>Další postup

Začněte konfigurovat svoji aplikaci pro prostředí registrace a přihlášení – pokračujte tímto kurzem.

> [!div class="nextstepaction"]
> [Kurz: Vytvoření tenanta Azure Active Directory B2C](tutorial-create-tenant.md)
