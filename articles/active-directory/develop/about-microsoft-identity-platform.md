---
title: O platformě Microsoft Identity Platform | Azure
description: Platforma Microsoft Identity Platform vznikla z platformy pro vývojáře a službu identit Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f3fcbda160d84e41fd2244a4d58766ae3991e52
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56197653"
---
# <a name="about-microsoft-identity-platform"></a>O platformě Microsoft Identity Platform

Platforma Microsoft Identity Platform vznikla z platformy pro vývojáře a službu identit Azure Active Directory (Azure AD). Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity od Microsoftu a získávají tokeny pro volání Microsoft Graphu, dalších rozhraní API od Microsoftu nebo rozhraní API, která vytvořili vývojáři. Je to plnohodnotná platforma, kterou tvoří ověřovací služba, open source knihovny, registrace a konfigurace aplikací (prostřednictvím portálu pro vývojáře a rozhraní API aplikací), úplná dokumentace pro vývojáře, ukázky kódu a další obsah pro vývojáře. Microsoft Identity Platform podporuje standardní oborové protokoly, jako jsou OAuth 2.0 a OpenID Connect.

Až dosud používala většina vývojářů platformu Azure AD v1.0 k ověřování identit Azure AD (pracovní a školní účty) na základě vyžádání tokenů z koncového bodu Azure AD v1.0 s použitím Azure AD Authentication Library (ADAL), webu Azure Portal pro registraci a konfiguraci aplikací a Azure AD Graph API pro konfiguraci aplikací prostřednictvím kódu programu. Azure AD v1.0 je pokročilá nabídka platformy, která bude dále fungovat pro podnikové aplikace.

Aby se možnosti platformy Microsoft Identity Platform rozšířily a dál rozvinuly, můžete teď ověřovat pestřejší škálu identit Microsoftu (identity Azure AD, účty Microsoft (třeba outlook.com nebo hotmail.com) a účty sociálních sítí a místní účty ve službě Azure AD B2C) prostřednictvím koncového bodu označovaného jako Azure AD v2.0. Tady budete používat Microsoft Authentication Library (MSAL) nebo jakoukoliv open source knihovnu OAuth2.0 nebo OpenID Connect, Azure Portal pro registraci a konfiguraci aplikací a rozhraní Microsoft Graph API pro konfiguraci aplikace s použitím programového kódu. Aktualizovaná platforma Microsoft Identity Platform (zejména knihovny MSAL a nejnovější prostředí registrace aplikací na webu Azure Portal) se za poslední rok výrazně vyvinula. Aby bylo možné tuto verzi dokončit, chtěli bychom požádat vývojáře, aby svoje aplikace vyvíjeli a testovali s použitím nejnovější platformy Microsoft Identity Platform.

Aplikace používající nejnovější knihovnu ADAL a nejnovější knihovnu MSAL budou mezi sebou používat jednotné přihlašování. Aplikace aktualizované z knihovny ADAL na MSAL si zachovají stav přihlašování uživatelů. Vývojáři se můžou rozhodnout, že budou aktualizovat svoje aplikace na verzi MSAL podle toho, jak budou považovat za vhodné, protože aplikace vytvořené s použitím knihovny ADAL budou dál fungovat a budou se dál podporovat.

## <a name="microsoft-identity-platform-experience"></a>Používání platformy Microsoft Identity Platform

Na následujícím schématu vidíte používání identit Microsoftu na vysoké úrovni včetně použití registrace aplikací, sad SDK, koncových bodů a podporovaných identit.

![Platforma Microsoft Identity Platform dnes](./media/about-microsoft-identity-platform/microsoft-identity-platform-preview.png)

Platforma Microsoft Identity Platform má dva koncové body (v1.0 a v2.0) a dvě sady klientských knihoven pro zpracování těchto koncových bodů. Při vývoji nových aplikací zvažte výhody a aktuální stav koncových bodů a knihoven ověřování. Zvažte také následující body:

* Podporované platformy

    * [ADAL](active-directory-authentication-libraries.md) podporuje .NET, JavaScript, iOS, Android, Javu a Python
    * [Knihovna MSAL ve verzi Preview](reference-v2-libraries.md) podporuje .NET, JavaScript, iOS a Android
    * Oba koncové body podporují middleware .NET a Node.js na straně serveru pro účely ochrany rozhraní API a přihlášení. 

* U koncového bodu v2.0 a MSAL provádíme řadu inovací, jako je například dynamický a přírůstkový souhlas, současně ale dál podporujeme v1.0 a ADAL.

    Na webu Azure Portal bylo dřív nutné staticky identifikovat všechny obory, které potřebuje vaše aplikace. S koncovým bodem v2.0 a portály přidruženými k tomuto koncovému bodu můžete staticky definovat obory stejně jako před tím nebo si je můžete vyžádat dynamicky, když bude vaše aplikace potřebovat oprávnění. Dynamické vyžádání obsahuje jednu další volitelnou možnost, přírůstkový souhlas. Přírůstkový souhlas umožňuje požádat o podmnožinu oborů, které vyžadujete při prvním ověření uživatele, a požádat o další obory podle potřeby. 
    
    Například při použití aplikace fotoaparátu/kamery na mobilním zařízení se uživateli zobrazí výzva, aby povolil aplikaci přístup k fotoaparátu/kameře, a až poté, co uživatel souhlasí, bude mít aplikace přístup k fotoaparátu/kameře a bude moct pořídit fotografii.  Když je aplikace připravená uložit novou fotku, může požádat o oprávnění číst/zapsat fotku. 

* Možné změny způsobující chyby

    Knihovna MSAL je vhodná pro použití v produkčním prostředí. Poskytujeme stejnou úroveň podpory produkčního prostředí pro MSAL jako pro aktuální knihovny produkčního prostředí. Ve verzi preview můžeme provádět změny rozhraní API, formátu interní mezipaměti a dalších mechanismů této knihovny, které budete muset přijmout spolu s opravami chyb nebo vylepšeními funkcí. To může mít vliv na vaši aplikaci. Třeba změna formátu mezipaměti může mít dopad na vaše uživatele, například v tom, že se budou muset znovu přihlásit. Změna rozhraní API může vyžadovat aktualizaci kódu. Poté, co poskytneme všeobecně dostupnou verzi (GA), budeme požadovat, abyste na verzi GA aktualizovali do šesti měsíců, protože aplikace napsané v preview verzi knihovny můžou přestat fungovat.

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o verzích v1.0 a v2.0.

* [Informace o verzi v1.0](v1-overview.md)
* [Informace o verzi v2.0](v2-overview.md)
