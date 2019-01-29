---
title: Možnosti podporu a nápovědu pro vývojáře Azure Identity | Dokumentace Microsoftu
description: Už víte, jak získat nápovědu a podporu pro dotazy související s vývojem a problémů při vytváření aplikace, která lze integrovat s Microsoft Azure identity (Azure Active Directory a MSA)
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
ms.date: 10/27/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: f8c5e5f598ab8566eacb594ff66b63ce3793f57f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093186"
---
# <a name="support-and-help-options-for-developers"></a>Možnosti podporu a nápovědu pro vývojáře

Pokud se právě začínají integrovat s Azure Active Directory, Microsoft identity nebo Microsoft Graph API, nebo když implementujete nové funkce do vaší aplikace, jsou časy, které potřebujete získat pomoc od komunity nebo pochopit, bez ohledu Možnosti podpory, které máte jako vývojář. Tento článek vám pomůže porozumět následujících souhrn těchto možností:

> [!div class="checklist"]
> * Hledání zkontrolujte, jestli váš dotaz problém není zodpovězená komunitou, nebo pokud stávající dokumentaci pro funkci, která se pokoušíte k implementaci již existuje
> * V některých případech chcete pomocí našich nástrojů podpory systému si můžete usnadnit ladění konkrétní problém
> * Pokud nemůžete najít odpovědi z co potřebujete, můžete položit dotaz na *Stack Overflow*
> * Pokud narazíte na problém s jedním z našich knihoven ověřování, zvýšit *Githubu* problém
> * Nakonec pokud potřebujete mluvit někomu, můžete chtít otevřít žádost o podporu


## <a name="search"></a>Search

Pokud máte dotazy související s vývojem, bude pravděpodobně možné najít odpověď, musíte na dokumentaci pro naše [Githubu ukázky](https://github.com/azure-samples), nebo odpovědi na [Stack Overflow](https://www.stackoverflow.com) dotazy.

### <a name="scoped-search"></a>Hledání s vymezeným oborem
Rychlejší výsledky, omezit rozsah hledání na webu Stack Overflow, dokumentaci a ukázky kódu pomocí následujících na vaše [oblíbený vyhledávací web](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Kde *{Your hledané termíny}* je klíčová slova pro vyhledávání.
<br/>

## <a name="use-our-development-support-tools"></a>Použití vývojářských nástrojů podpory

|Nástroj  |Popis  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| Vložte ID nebo přístup tokenů pro dekódování deklarace identity názvy a hodnoty |
|[Analyzátor kódu chyby](https://apps.dev.microsoft.com/portal/tools/errors)| Vložte kód chyby přijaté při přihlašování nebo souhlas stránky, které najdete v článku možné příčiny a nápravy |
|[Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer)| Nástroj, který vám umožní vytvářet požadavky a odpovědi na rozhraní Microsoft Graph API najdete v článku|

<br/>

[![Přetečení zásobníku](./media/developer-support-help-options/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Odeslat dotaz na Stack Overflow

Přetečení zásobníku je upřednostňovaný kanál pro otázky související s vývojem – kde členové týmu i členové komunity jako Microsoft se přímo podílí na dokážete problém vyřešit.

Pokud nemůžete najít odpověď na váš problém prostřednictvím vyhledávání, odešlete nový dotaz na Stack Overflow: použijte jednu z následujících značek při provádění dotazy, které pomůžou komunity identifikovat, pak odpověď na vaši otázku na včas neukončily:

|Komponenta nebo oblast  |Značky  |
|---------|---------|
|Knihovna ADAL |[[adal]](https://stackoverflow.com/questions/tagged/adal)|
|Knihovna MSAL     |[[msal]](https://stackoverflow.com/questions/tagged/msal)|
|OWIN middleware  |[[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft Graph API](https://developer.microsoft.com/graph/) |[[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph)
|Další oblasti související s ověřením nebo autorizací témata |[[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Následující příspěvky z přetečení zásobníku obsahují tipy, jak provádět dotazy a tipy k přidání zdrojového kódu – držet těchto pokynů může přispět k vyšší pravděpodobnost pro členy komunity k vyhodnocení a rychle reagovat na vaši otázku:
> - [Jak můžu pokládat správné otázky](https://stackoverflow.com/help/how-to-ask)
> - [Vytvoření minimální, dokončeno a ověřitelné příklad](https://stackoverflow.com/help/mcve)

<br/>


[![Přetečení zásobníku](./media/developer-support-help-options/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Vytvořit problém na Githubu

 Pokud najdete chybu nebo problém související s naše knihovny, vyvolat chybu na našich úložištích GitHub. Protože jsou našich knihoven open source, můžete je taky zadarmo odeslat žádost o přijetí změn. V následujícím článku obsahuje seznam knihoven a jejich úložiště GitHub:

- [Middleware Owin, ADAL a MSAL](active-directory-authentication-libraries.md) knihovny a úložištích GitHub.

<br/>

## <a name="open-a-support-request"></a>Žádost o podporu

Pokud potřebujete mluvit někomu, můžete otevřít žádost o podporu. Pokud jste zákazníkem Azure, existuje několik možností podpory k dispozici. Porovnání plánů, najdete v článku [na této stránce](https://azure.microsoft.com/support/plans/). Plán Developer support je také dostupná pro zákazníky Azure. Informace o tom, jak zakoupit plány podpory Developer, naleznete v tématu [na této stránce](https://azure.microsoft.com/support/plans/developer/).

- Pokud už máte Azure podporují plánování, [žádost o podporu tady](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Pokud si nejste zákazník Azure, můžete také otevřít žádost o podporu u Microsoftu přes [naši obchodní podporu](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Můžete také zkusit [naše virtuální agenta](https://support.microsoft.com/contactus/?ws=support) k získání podpory nebo položit dotazy.

### <a name="free-chat-support-for-a-limited-time"></a>Podpora formou chatu zdarma po omezenou dobu

Můžete také použít Naše podpora formou chatu – což je zdarma pro Microsoft Partners po omezenou dobu. Pokud vaše společnost není partnera společnosti Microsoft, můžete ho zdarma zaregistrovat a získat další výhody tak, že přejdete [tady](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Po registraci vaší společnosti, můžete spustit chat požadavek [tady](https://aka.ms/devchat).
