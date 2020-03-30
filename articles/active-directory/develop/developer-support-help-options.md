---
title: Možnosti podpory a nápovědy pro vývojáře aplikací Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak získat nápovědu a podporu pro otázky a problémy související s vývojem při vytváření aplikací, které se integrují s identitami Microsoftu (Azure Active Directory a účet Microsoft).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 89bf49fb44d8575b251a0b33698bc4ce8425cc2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160963"
---
# <a name="support-and-help-options-for-developers"></a>Možnosti pomoci a podpory pro vývojáře

Pokud teprve začínáte integrovat s Azure Active Directory (Azure AD), microsoftovými identitami nebo rozhraním Microsoft Graph API nebo když implementujete novou funkci do vaší aplikace, jsou chvíle, kdy potřebujete získat pomoc od komunity nebo pochopit možnosti podpory, které máte jako vývojář. Tento článek vám pomůže pochopit tyto možnosti, včetně:

> [!div class="checklist"]
> * Jak vyhledat, zda vaše otázka nebyla zodpovězena komunitou, nebo pokud již existuje existující dokumentace pro funkci, kterou se pokoušíte implementovat
> * V některých případech chcete použít naše nástroje podpory, které vám pomohou ladit konkrétní problém
> * Pokud nemůžete najít odpověď, kterou potřebujete, možná budete chtít položit otázku na *Stack Overflow*
> * Pokud zjistíte problém s některou z našich ověřovacích knihoven, zvyšte problém *s GitHubem*
> * A konečně, pokud potřebujete mluvit s někým, možná budete chtít otevřít žádost o podporu

## <a name="search"></a>Search

Pokud máte otázku týkající se vývoje, můžete najít odpověď v dokumentaci, [ukázky GitHub](https://github.com/azure-samples)nebo odpovědi na otázky [přetečení zásobníku.](https://www.stackoverflow.com)

### <a name="scoped-search"></a>Hledání s vymezením oborů

Pro rychlejší výsledky, rozsah hledání přetečení zásobníku, dokumentace a ukázky kódu pomocí následujícího dotazu ve vašem oblíbeném vyhledávači:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Kde *{Vaše hledané termíny}* odpovídají vašim vyhledávacím klíčovým slovům.

## <a name="use-the-development-support-tools"></a>Použití nástrojů podpory vývoje

| Nástroj  | Popis  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Vložte ID nebo přístupový token pro dekódování názvů deklarací identity a hodnot. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Nástroj, který umožňuje provádět požadavky a zobrazit odpovědi proti rozhraní Microsoft Graph API. |

## <a name="post-a-question-to-stack-overflow"></a>Odeslání otázky do přetečení zásobníku

Přetečení zásobníku je upřednostňovaný kanál pro otázky související s vývojem. Zde se členové komunity vývojářů a členové týmu Microsoft přímo podílejí na řešení vašich problémů.

Pokud nemůžete najít odpověď na svou otázku prostřednictvím vyhledávání, odešlete novou otázku do zásobníku Přetečení. Při kladení otázek použijte jednu z následujících značek, která komunitě pomůže rychleji identifikovat a odpovědět na vaši otázku:

|Součást/oblast  | Značky |
|---------|---------|
| Knihovna ADAL | [To je v pořádku.](https://stackoverflow.com/questions/tagged/adal) |
| Knihovna MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN middleware  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azurová-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azurová-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[Microsoft-Graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Jakákoli jiná oblast související s tématy ověřování nebo autorizace | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Následující příspěvky od Stack Overflow obsahují tipy, jak klást otázky a jak přidat zdrojový kód. Postupujte podle těchto pokynů, abyste zvýšili šance členů komunity rychle posoudit vaši otázku a odpovědět na ni:

* [Jak mohu položit dobrou otázku](https://stackoverflow.com/help/how-to-ask)
* [Jak vytvořit minimální, úplný a ověřitelný příklad](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Vytvoření problému githubu

Pokud najdete chybu nebo problém související s našimi knihovnami, vzbuzte problém v našich úložištích GitHub. Vzhledem k tomu, že naše knihovny jsou open source, můžete také odeslat žádost o přijetí vyhovení.

Seznam knihoven a jejich úložišť GitHub najdete v následujících tématech:

* [Knihovny Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) a úložiště GitHub
* [Knihovny Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) a úložiště GitHub

## <a name="open-a-support-request"></a>Otevření žádosti o podporu

Pokud potřebujete s někým mluvit, můžete otevřít žádost o podporu. Pokud jste zákazník azure, existuje několik možností podpory k dispozici. Chcete-li porovnat plány, podívejte se na [tuto stránku](https://azure.microsoft.com/support/plans/). Podpora vývojářů je k dispozici také pro zákazníky Azure. Informace o nákupu plánů podpory pro vývojáře naleznete na [této stránce](https://azure.microsoft.com/support/plans/developer/).

* Pokud už máte plán podpory Azure, [otevřete žádost o podporu tady](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Pokud nejste zákazníkem Azure, můžete také otevřít žádost o podporu se společností Microsoft prostřednictvím [naší komerční podpory](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Můžete také zkusit [virtuálního agenta](https://support.microsoft.com/contactus/?ws=support) získat podporu nebo klást otázky.
