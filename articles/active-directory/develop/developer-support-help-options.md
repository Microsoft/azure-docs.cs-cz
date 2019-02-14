---
title: Možnosti podporu a nápovědu pro vývojáře aplikací Azure AD | Dokumentace Microsoftu
description: Už víte, jak získat nápovědu a podporu pro dotazy související s vývojem a problémů při vytváření aplikace, která lze integrovat s Microsoft identity (účet služby Azure Active Directory a Microsoft)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2019
ms.author: celested
ms.reviewer: jmprieur, dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f3f041fa3b594b4b680d794c1c013e55b97da4
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235185"
---
# <a name="support-and-help-options-for-developers"></a>Možnosti podporu a nápovědu pro vývojáře

Pokud začínáte, můžete integrovat s Azure Active Directory (Azure AD), Microsoft identity nebo Microsoft Graph API, nebo když implementujete novou funkci pro vaši aplikaci, existují situace, kdy je potřeba získat pomoc od komunity nebo pochopit, Možnosti podpory, které máte jako vývojář. Tento článek vám pomůže porozumět těchto možností, včetně:

> [!div class="checklist"]
> * Jak hledat, jestli váš dotaz není zodpovězená komunitou, nebo pokud stávající dokumentaci pro funkci, na který zkoušíte, implementovat již existuje
> * V některých případech chcete používání našich nástrojů podpory si můžete usnadnit ladění určitý problém
> * Pokud nenajdete odpovědi, které potřebujete, můžete položit dotaz na *Stack Overflow*
> * Pokud narazíte na problém s jedním z našich knihoven ověřování, zvýšit *Githubu* problém
> * Nakonec pokud potřebujete mluvit někomu, můžete chtít otevřít žádost o podporu

## <a name="search"></a>Search

Pokud máte dotazy související s vývojem, může být schopna najít odpovědi v dokumentaci, [Githubu ukázky](https://github.com/azure-samples), nebo odpovědi na [Stack Overflow](https://www.stackoverflow.com) dotazy.

### <a name="scoped-search"></a>Hledání s vymezeným oborem

Rychlejší výsledky omezit rozsah hledání na webu Stack Overflow, dokumentaci a ukázky kódu s použitím následujícího dotazu v váš oblíbený vyhledávací web:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Kde *{Your hledané termíny}* odpovídají klíčová slova pro vyhledávání.

## <a name="use-the-development-support-tools"></a>Použití vývojářských nástrojů, podpory

| Nástroj  | Popis  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Vložte ID a přístupový token k dekódování deklarace identity názvy a hodnoty. |
| [Analyzátor kódu chyby](https://apps.dev.microsoft.com/portal/tools/errors)| Vložte kód chyby přijaté při přihlašování nebo souhlas stránky, které najdete v článku možné příčiny a řešení. |
| [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer)| Nástroj, který vám umožní vytvářet požadavky a odpovědi na rozhraní Microsoft Graph API najdete v článku. |

## <a name="post-a-question-to-stack-overflow"></a>Odeslat dotaz na Stack Overflow

Přetečení zásobníku je upřednostňovaný kanál pro dotazy související s vývojem. Členové komunity vývojářů a členové týmu Microsoft se tady přímo podílí na využít pomoci při řešení problémů.

Pokud nemůžete najít odpověď na svou otázku prostřednictvím vyhledávání, odešlete nový dotaz na Stack Overflow. Při kladení otázek pomůže komunity identifikovat a odpověď na vaši otázku rychleji, použijte jednu z následujících značek:

|Komponenta nebo oblast  | Značky |
|---------|---------|
| Knihovna ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Knihovna MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN middleware  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Další oblasti související s ověřením nebo autorizací témata | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Následující příspěvky z přetečení zásobníku obsahují tipy, jak klást otázky a přidání zdrojového kódu. Postupujte podle těchto pokynů zvýšit šanci na pro členy komunity k vyhodnocení a rychle reagovat na vaši otázku:

* [Jak můžu pokládat správné otázky](https://stackoverflow.com/help/how-to-ask)
* [Jak vytvořit příklad minimální, úplný a ověřit](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Vytvořit problém na Githubu

Pokud najdete chybu nebo problém související s naše knihovny, vyvolat chybu v našich úložištích GitHub. Protože jsou našich knihoven open source, můžete také odeslat žádost o přijetí změn.

Pokud chcete zobrazit seznam knihoven a jejich úložiště GitHub, naleznete v následujících článcích:

* [ADAL](active-directory-authentication-libraries.md) knihovny a úložištích GitHub.
* [Knihovna MSAL](reference-v2-libraries.md) knihovny a úložištích GitHub.

## <a name="open-a-support-request"></a>Žádost o podporu

Pokud potřebujete mluvit někomu, můžete otevřít žádost o podporu. Pokud jste zákazníkem Azure, existuje několik možností podpory k dispozici. Porovnání plánů, najdete v článku [na této stránce](https://azure.microsoft.com/support/plans/). Plán Developer support je také dostupná pro zákazníky Azure. Informace o tom, jak zakoupit plány podpory Developer, naleznete v tématu [na této stránce](https://azure.microsoft.com/support/plans/developer/).

* Pokud už máte Azure podporují plánování, [žádost o podporu tady](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Pokud si nejste zákazník Azure, můžete také otevřít žádost o podporu u Microsoftu přes [naši obchodní podporu](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Můžete také zkusit [virtuální agenta](https://support.microsoft.com/contactus/?ws=support) k získání podpory nebo položit dotazy.

### <a name="free-chat-support-for-a-limited-time"></a>Podpora formou chatu zdarma po omezenou dobu

Můžete také použít Naše podpora formou chatu, který je zdarma pro Microsoft Partners po omezenou dobu. Pokud vaše společnost není partnera společnosti Microsoft, můžete ho zdarma zaregistrovat a získat další výhody tak, že přejdete [tady](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Po registraci vaší společnosti, můžete spustit chat požadavek [tady](https://aka.ms/devchat).
