---
title: Microsoft Security Code Analysis – vydání
description: Tento článek popisuje nadcházející vydání rozšíření služby Microsoft Security Code Analysis.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 7596df66dbcbe1b7cdefab4811da7174bc83ac65
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801175"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft Security Code Analysis – vydání a plán

> [!Note]
> Od 1. března 2022 bude rozšíření Microsoft Security Code Analysis (MSCA) vyřazeno. Stávající zákazníci MSCA si budou mít přístup k MSCA do 1. března 2022. Alternativní možnosti v Azure DevOps najdete v [nástrojích pro analýzu zdrojového kódu OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) . Zákazníci, kteří plánují migraci na GitHub, si můžete zaregistrovat v [rozšířeném zabezpečení GitHubu](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Tým Microsoft Security Code Analysis je v partnerství s Developer Support hrdým k oznámení nejnovějších a nadcházejících vylepšení našeho rozšíření MSCA.


## <a name="credential-scanner-v20-released-in-april-2020"></a>Credential Scanner v 2.0: vydaný v dubnu 2020

### <a name="innovations--improvements"></a>Inovace & vylepšení

- **Základní modul**

   - Průměrná aktualizace výkonu 25% s téměř lineárním časem běhu
   - Vyhledávání a hodnocení na základě kontextu nebo legitimace na vyšší přesnost
   - Vylepšení obecného zjišťování hesel a srovnávací logiky pro zjevné zástupné symboly (například fakePassword)

- **Pokrytí** – podpora pro 25 a více tajných typů, včetně následujícího shora vyžádaného:

   - Přístupové heslo k certifikátu účtu Fabric
   - Tajný kód klienta/klíč rozhraní API
   - Autorizační hlavička protokolu HTTP
   - Přístupový klíč ke tajnému kódu klienta Amazon S3
   - Azure Active Directory tokenu klientského přístupu
   - Azure Function Master/klíč rozhraní API
   - Power BI přístupový klíč
   - Vzor hesla pro šablonu Azure Resource Manager

- **Výstupy**

   - Podpora pro formáty souborů výstupu SARIF 2,1 a CSV

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0: vydáno v dubnu 2020

### <a name="improvements"></a>Vylepšen

- FUNKCE: aktualizujte na finální SARIF v2 (verze 2.1.16). Tato aktualizace umožňuje ukládání výsledků do mezipaměti při předávání--hash na příkazovém řádku, což vede k výraznému zlepšení výkonu při rekurzivní analýze adresářů s více kopiemi cílů skenování.
- Oprava chyby: Opravte překlep v BA2021. DoNotMarkWritableSectionsAsExecutable výstup.
- VÝKON: Eliminujte načítání PDB pro všechny mimo smíšený režim pro spravovaná sestavení, včetně knihovny IL (předem kompilovaných) binárních souborů.
- Nepravdivá ZÁPORNÁ Oprava: Ověřte, že soubor PDB umístěný společně s binárním souborem se ve skutečnosti shoduje s binárním souborem v analýze.
- FUNKCE: zadáním argumentu--Local-symbol-directorys zadejte další (místní, nesymbolové) umístění pro hledání PDB.
- FALEŠNě pozitivní Oprava: přeskočit analýzu založenou na PDB pro vygenerovaný spustitelný soubor .NET Core Native Bootstrap (který není uživatelsky ovladatelného kódu).

## <a name="whats-next-in-q3-cy20"></a>Co dál v kroku Q3 CY20?

- Nástroj Java Security Analysis
- Nástroj pro analýzu zabezpečení Pythonu
- ES Lint nahrazení TS Lint pro TypeScript a JavaScript
- Nástroj pro analýzu šablon Správce prostředků

## <a name="tool-deprecation-notification"></a>Oznámení o zastaralosti nástroje

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>Zjišťování rizik společnosti Microsoft (MSRD) je zastaralá od června 26 2020.

Vyřazená služba MSRD, která se už nepoužívá, bude nahrazena Open Source neočekávanou platformou vývojáře pro Azure. Tato platforma se v současnosti vyvíjí a testuje v partnerství s mnoha jádry produktových týmů Microsoftu. Tato přibližná platforma bude integrovat úpravy a umožní adaptivní, výukové testy, které jsou integrovány do kanálů CI/CD, které se v průběhu času v softwarových projektech rozrůstá v čase. Verze Open Source této platformy je naplánovaná na druhou polovinu 2020.

## <a name="next-steps"></a>Další kroky

Pokyny, jak připojit a nainstalovat Microsoft Security Code Analysis, najdete v naší [příručce k registraci a instalaci](security-code-analysis-onboard.md).

Pokud máte další dotazy týkající se rozšíření a nabízených nástrojů, podívejte se na naše [Časté stránky Nejčastější dotazy](security-code-analysis-faq.md).
