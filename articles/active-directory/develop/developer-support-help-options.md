---
title: Podpora a možnosti pomoci pro vývojáře aplikací Azure AD
description: Informace o tom, jak získat nápovědu a podporu pro otázky a problémy související s vývojem při vytváření aplikace, která se integruje s identitami Microsoft (Azure Active Directory a účet Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: af363bb30d6515ce969afe146c780baa4b31cd83
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88117205"
---
# <a name="support-and-help-options-for-developers"></a>Možnosti pomoci a podpory pro vývojáře

Pokud se teprve začínáte integrovat s Azure Active Directory (Azure AD), Microsoft identity nebo Microsoft Graph API, nebo když implementujete novou funkci aplikace, nastane čas, kdy potřebujete získat pomoc od komunity nebo porozumět možnostem podpory, které máte jako vývojář. Tento článek vám pomůže pochopit tyto možnosti, včetně těchto:

> [!div class="checklist"]
> * Jak hledat, jestli na vaši otázku neodpověděla komunita, nebo jestli už existuje stávající dokumentace k funkci, kterou se pokoušíte implementovat
> * V některých případech chcete použít naše nástroje podpory, které vám pomůžou s laděním konkrétního problému.
> * Pokud nemůžete najít odpověď, kterou potřebujete, můžete klást otázky na *Stack Overflow*
> * Pokud narazíte na problém s jednou z našich knihoven pro ověřování, vyvolejte problém *GitHubu* .
> * Nakonec, pokud potřebujete mluvit s někým, možná budete chtít otevřít žádost o podporu.

## <a name="search"></a>Hledat

Pokud máte dotazy související s vývojem, možná budete moci najít odpověď v dokumentaci, [ukázkách GitHubu](https://github.com/azure-samples)nebo odpovědích na [Stack Overflow](https://www.stackoverflow.com) otázky.

### <a name="scoped-search"></a>Hledání v oboru

Pro dosažení rychlejších výsledků můžete určit rozsah hledání Stack Overflow, dokumentaci a ukázky kódu pomocí následujícího dotazu v oblíbených vyhledávacích modulech:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Kde *{vaše hledané výrazy}* odpovídají vašim klíčovým slovům pro hledání.

## <a name="use-the-development-support-tools"></a>Použití nástrojů pro podporu vývoje

| Nástroj  | Popis  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Vložte ID nebo přístupový token k dekódování názvů a hodnot deklarací identity. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Nástroj, který umožňuje vytvářet žádosti a zobrazovat odpovědi na rozhraní Microsoft Graph API. |

## <a name="post-a-question-to-stack-overflow"></a>Vystavení otázky pro Stack Overflow

Stack Overflow je preferovaným kanálem pro otázky související s vývojem. V tomto případě jsou členové komunity vývojářů a členové týmu společnosti Microsoft přímo zapojeni v rámci pomoci při řešení vašich problémů.

Pokud nemůžete najít odpověď na svůj dotaz pomocí hledání, odešlete novou otázku pro Stack Overflow. Použijte jednu z následujících značek při dotazování otázek, které komunitu pomůžou najít a odpovědět na svůj dotaz rychleji:

|Součást/oblast  | Značky |
|---------|---------|
| Knihovna ADAL | [modulu](https://stackoverflow.com/questions/tagged/adal) |
| Knihovna MSAL     | [msal](https://stackoverflow.com/questions/tagged/msal) |
| Middleware OWIN  | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](../external-identities/what-is-b2b.md)  | [[Azure-AD-B2B]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [B2C Azure](https://azure.microsoft.com/services/active-directory-b2c/)  | [[Azure-AD-B2C]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[Microsoft-Graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Jakákoli další oblast související s ověřováním nebo autorizačními tématy | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Následující příspěvky z Stack Overflow obsahují tipy, jak klást otázky a jak přidat zdrojový kód. Postupujte podle těchto pokynů a zvyšte pravděpodobnost, že členové komunity budou moci rychle posoudit a reagovat na své otázky:

* [Návody klást dobré otázky](https://stackoverflow.com/help/how-to-ask)
* [Jak vytvořit minimální, úplný a ověřitelný příklad](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Vytvoření problému GitHubu

Pokud narazíte na chybu nebo problém týkající se našich knihoven, vyvolejte problém v našich úložištích GitHubu. Vzhledem k tomu, že naše knihovny jsou open source, můžete také odeslat žádost o přijetí změn.

Seznam knihoven a jejich úložišť GitHub najdete v následujících tématech:

* Knihovny [knihovny Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) a úložiště GitHub
* Knihovny [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) a úložiště GitHub

## <a name="open-a-support-request"></a>Otevřete žádost o podporu.

Pokud potřebujete mluvit s někým, můžete otevřít žádost o podporu. Pokud jste zákazníkem Azure, máte k dispozici několik možností podpory. K porovnání plánů si Projděte [tuto stránku](https://azure.microsoft.com/support/plans/). K dispozici je také podpora pro vývojáře pro zákazníky Azure. Informace o tom, jak zakoupit plány podpory pro vývojáře, najdete na [této stránce](https://azure.microsoft.com/support/plans/developer/).

* Pokud už máte plán podpory Azure, [otevřete žádost o podporu tady](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

* Pokud nejste zákazníkem Azure, můžete také pomocí [naší komerční podpory](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)otevřít žádost o podporu od Microsoftu.

Můžete také vyzkoušet [virtuálního agenta](https://support.microsoft.com/contactus/?ws=support) a získat podporu nebo klást otázky.