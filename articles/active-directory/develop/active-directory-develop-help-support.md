---
title: Možnosti podpory a nápovědu pro vývojáře Azure Identity | Microsoft Docs
description: Vědět, jak získat nápovědu a podporu pro vývoj související otázky a problémy při vytváření aplikace, která integrovat identit Microsoft Azure (Azure Active Directory a účet spravované služby)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: ae682830a8d0e2c5c27211a3396d04c881c41585
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="support-and-help-options-for-developers"></a>Možnosti podpory a nápovědu pro vývojáře 

Pokud právě začínáte k integraci s Azure Active Directory, Microsoft identity nebo Microsoft Graph API, nebo při implementaci nová funkce do vaší aplikace, jsou časy, které potřebujete získat pomoc od komunity nebo pochopit, bez ohledu Možnosti podpory, které mají jako vývojář. Tento článek pomáhá pochopit tyto možnosti níže Shrnutí:

> [!div class="checklist"]
> * Hledání, zkontrolujte, zda váš problém dotaz není zodpovězená komunitou, nebo pokud stávající dokumentaci pro funkci chcete implementovat již existuje
> * V některých případech jenom chcete pomocí naší nástrojů podpory vám pomůže při ladění konkrétní problém
> * Pokud nemůžete najít odpověď z co potřebujete, můžete chtít zeptejte se na *Stack Overflow*
> * Pokud zjistíte problém s jedním z našich knihovny ověřování, zvýšit *Githubu* problém
> * Nakonec pokud potřebujete, aby komunikoval s někým, můžete chtít otevřít žádost o podporu


## <a name="search"></a>Search

Pokud máte otázky související s vývojem, bude pravděpodobně možné najít odpověď na dokumentaci, potřebujete naše [githubu ukázky](https://github.com/azure-samples), nebo odpovědi na [Stack Overflow](https://www.stackoverflow.com) otázky.

### <a name="scoped-search"></a>Prohledejte
Rychlejší výsledky, určit rozsah hledání tak, aby Stack Overflow, naší dokumentace a ukázek kódu pomocí následující na vaše [oblíbený vyhledávací web](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Kde *{vašich podmínek vyhledávání}* je slova pro vyhledávání.
<br/>

## <a name="use-our-development-support-tools"></a>Použít vývojářských nástrojů podpory

|Nástroj  |Popis  |
|---------|---------|
|[jwt.MS](https://jwt.ms)| Vložte tokeny ID nebo přístup k dekódování deklarace identity názvy a hodnoty |
|[Analyzátor kódu chyby](https://apps.dev.microsoft.com/portal/tools/errors)| Vložte kód chyby přijaté při přihlášení nebo souhlas stránky zobrazíte možné příčiny a nápravy |
|[Průzkumník Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Nástroj, který vám umožní provádět požadavky a odpovědi pro Microsoft Graph API najdete v části|

<br/>

[![Přetečení zásobníku](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Odeslat dotaz k přetečení zásobníku

Přetečení zásobníku je upřednostňovaný kanál pro vývoj otázky ohledně – kde oba členové komunity služby jako Microsoft členové týmu jsou přímo ovlivněny na pomoci při problém vyřešit.

Pokud nemůžete najít odpověď na váš problém prostřednictvím vyhledávání, Odeslat novou otázku k přetečení zásobníku: použijte jednu z následujících značky při provádění otázky, které pomáhají identifikovat, komunita pak odpovídající vaší otázce v časovém limitu:

|Komponenta nebo oblasti  |Značky  |
|---------|---------|
|Knihovna ADAL |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|Knihovna MSAL     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|OWIN middleware  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure ad b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure ad b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft Graph API](https://developer.microsoft.com/graph/) |[[microsoft grafu]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|Další oblasti související s ověřování nebo autorizace témata |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> V následujících příspěvcích z Stack Overflow obsahovat tipy, jak provádět dotazy a tipy k přidání zdrojový kód – následující tyto pokyny mohou pomoci zvýšit pravděpodobnost pro členy komunity pro hodnocení a rychle reagovat na svoji otázku:  
> - [Jak mohu položit dotaz dobrý](https://stackoverflow.com/help/how-to-ask)
> - [Postup vytvoření minimální, dokončete a ověřitelný příklad](https://stackoverflow.com/help/mcve)

<br/>


[![Přetečení zásobníku](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Vytvořit problém Githubu

 Pokud zjistíte, chyby nebo problém související s naše knihovny, zvyšte problém na našem úložišť GitHub. Protože naše knihovny s otevřeným zdrojem, máte také volno odeslat žádost o přijetí změn také. V následujícím článku obsahuje seznam knihoven a jejich úložišť GitHub:

- [ADAL, MSAL a Owin middleware](active-directory-authentication-libraries.md) úložišť GitHub a knihovny

<br/>

## <a name="open-a-support-request"></a>Otevřete žádost o podporu

Pokud potřebujete, aby komunikoval s někým, můžete otevřít žádost o podporu. Pokud jste Azure zákazníků, existuje několik možností podpory k dispozici. K porovnání plány, najdete v části [tuto stránku](https://azure.microsoft.com/support/plans/). Podpora vývojáře je taky dostupné pro zákazníky, Azure. Informace o tom, jak zakoupit plánům podpory Developer, najdete v části [tuto stránku](https://azure.microsoft.com/support/plans/developer/).

- Pokud už máte Azure podporují plán, [otevřete žádost o podporu sem](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Pokud si nejste Azure zákazníků, můžete také otevřít žádost o podporu se společností Microsoft prostřednictvím [naše komerční podporu](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Můžete také zkusit [naše virtuální agenta](https://support.microsoft.com/contactus/?ws=support) získat podporu nebo klást otázky.

### <a name="free-chat-support-for-a-limited-time"></a>Podpora volné chat po omezenou dobu

Můžete také použít Naše podpora chat – což je zdarma pro Microsoft Partners po omezenou dobu. Pokud vaše společnost není partnera společnosti Microsoft, můžete ho zdarma zaregistrovat a získat další výhody přechodem [zde](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Po registraci vaší společnosti, můžete spustit požadavek chat [zde](https://aka.ms/devchat).