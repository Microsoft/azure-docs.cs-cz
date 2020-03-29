---
title: Přehled dokumentace analýzy bezpečnostního kódu společnosti Microsoft
description: Tento článek je přehledem rozšíření Microsoft Security Code Analysis
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851464"
---
# <a name="about-microsoft-security-code-analysis"></a>O analýze kódu zabezpečení společnosti Microsoft

Díky rozšíření Microsoft Security Code Analysis můžou týmy přidat analýzu kódu zabezpečení do svých kanálů průběžné integrace a doručování (CI/CD) azure devops. Tuto analýzu doporučují odborníci [na životní cyklus bezpečného vývoje (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) společnosti Microsoft.

Konzistentní uživatelské číslo zjednodušuje zabezpečení tím, že skryje složitost spuštěných nástrojů. S NuGet na základě dodání nástrojů, týmy již není nutné spravovat instalaci nebo aktualizaci nástrojů. S příkazovým řádkem a základní rozhraní pro úlohy sestavení, všichni uživatelé mohou mít stejnou kontrolu nad nástroji, jak chtějí.

Týmy mohou také používat výkonné funkce postprocessingu, jako jsou:

- Publikování protokolů pro uchovávání.
- Generování použitelných sestav zaměřených na vývojáře.
- Konfigurace přerušení sestavení při regresních testech.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Proč bych měl používat analýzu bezpečnostního kódu společnosti Microsoft?

### <a name="security-simplified"></a>Zjednodušené zabezpečení

Přidání nástrojů pro analýzu kódu zabezpečení Microsoftu do kanálu Azure DevOps je stejně jednoduché jako přidání nových úkolů. Přizpůsobte úkoly nebo použijte jejich výchozí chování. Úlohy se spouštějí jako součást vašeho kanálu Azure DevOps a vytvářejí protokoly, které podrobně popisují mnoho druhů výsledků.

### <a name="clean-builds"></a>Čisté sestavení

Po řešení počáteční problémy hlášené nástroji, můžete nakonfigurovat rozšíření přerušit staví na nové problémy.Nastavení průběžné integrace vychází z každé žádosti o přijetí vzato snadno.

### <a name="set-it-and-forget-it"></a>Nastavte ji a zapomeňte na to

Ve výchozím nastavení zůstávají úkoly sestavení a nástroje aktuální. Pokud existuje aktualizovaná verze nástroje, nemusíte jej stahovat a instalovat. Rozšíření se postará o aktualizaci za vás.

### <a name="under-the-hood"></a>Pod pokličkou

Úlohy sestavení rozšíření skrýt složitosti:
  - Spuštění bezpečnostních statických analytických nástrojů.
  - Zpracování výsledků ze souborů protokolu k vytvoření souhrnné sestavy nebo přerušení sestavení.

## <a name="microsoft-security-code-analysis-tool-set"></a>Sada nástrojů pro analýzu kódu zabezpečení společnosti Microsoft

Rozšíření Microsoft Security Code Analysis umožňuje nejnovější verze důležitých analytických nástrojů, které jsou vám snadno dostupné. Rozšíření zahrnuje nástroje spravované společností Microsoft i nástroje s otevřeným zdrojovým kódem.

Tyto nástroje se automaticky stáhnou do agenta hostovaného v cloudu po použití odpovídající úlohy sestavení ke konfiguraci a spuštění kanálu.

V této části je uvedena sada nástrojů, které jsou aktuálně k dispozici v rozšíření. Dávejte pozor na přidání dalších nástrojů. Také nám pošlete své návrhy na nástroje, které chcete, abychom přidali.

### <a name="anti-malware-scanner"></a>Antimalwarový skener

Úloha sestavení antimalwarového skeneru je nyní součástí rozšíření Microsoft Security Code Analysis. Tuto úlohu je nutné spustit u agenta sestavení, který má program Windows Defender již nainstalován. Další informace naleznete na [webu programu Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

BinSkim je přenosný spustitelný (PE) lehký skener, který ověřuje nastavení kompilátoru, nastavení propojovacího programu a další charakteristiky binárních souborů relevantní pro zabezpečení. Tato úloha sestavení poskytuje obálku příkazového řádku kolem aplikace konzoly binskim.exe. BinSkim je open source nástroj. Další informace najdete v [tématu BinSkim na GitHubu](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Skener pověření

Hesla a další tajné klíče uložené ve zdrojovém kódu jsou významným problémem. Credential Scanner je proprietární statický analytický nástroj, který pomáhá tento problém vyřešit. Nástroj detekuje pověření, tajné klíče, certifikáty a další citlivý obsah ve zdrojovém kódu a výstupu sestavení.

### <a name="microsoft-security-risk-detection"></a>Zjišťování bezpečnostních rizik společnosti Microsoft

Microsoft Security Risk Detection (MSRD) je cloudová služba pro testování fuzz. Identifikuje zneužitelné bezpečnostní chyby v softwaru. Tato služba vyžaduje samostatné předplatné a aktivaci. Další informace naleznete v [Centru pro vývojáře msrd](https://docs.microsoft.com/security-risk-detection/).

### <a name="roslyn-analyzers"></a>Analyzátory Roslyn

Roslyn Analyzátory je kompilátor integrovaný nástroj společnosti Microsoft pro staticky analýzu spravované ho jazyka C# a visual basic kód. Další informace naleznete v [analyzátorech založených na roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/).

### <a name="tslint"></a>TSLint

TSLint je rozšiřitelný nástroj statické analýzy, který kontroluje kód TypeScript pro čitelnost, udržovatelnost a chyby ve funkčnosti. Je široce podporován moderními editory a systémy sestavení. Můžete jej přizpůsobit pomocí vlastních pravidel, konfigurací a formatters. TSLint je open-source nástroj. Další informace najdete v [tématu TSLint na GitHubu](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Analýza a následné zpracování výsledků

Rozšíření Microsoft Security Code Analysis má také tři úlohy postprocessing. Tyto úkoly vám pomohou analyzovat výsledky nalezené úkoly nástroje zabezpečení. Při přidání do kanálu tyto úkoly obvykle sledují všechny ostatní úkoly nástroje.

### <a name="publish-security-analysis-logs"></a>Publikovat protokoly analýzy zabezpečení

Úloha sestavení publikování analýzy zabezpečení zachová soubory protokolu nástrojů zabezpečení, které jsou spuštěny během sestavení. Můžete si přečíst tyto protokoly pro vyšetřování a následné-up.

Soubory protokolu můžete publikovat do azure artefaktů jako soubor ZIP. Můžete je také zkopírovat do sdílené složky s usnadněním přístupu z vašeho soukromého agenta sestavení.

### <a name="security-report"></a>Zpráva o zabezpečení

Úloha sestavení sestavy zabezpečení analyzuje soubory protokolu. Tyto soubory jsou vytvořeny pomocí nástrojů zabezpečení, které jsou spuštěny během sestavení. Úloha sestavení pak vytvoří jeden soubor souhrnné sestavy. Tento soubor zobrazuje všechny problémy nalezené analytickými nástroji.

Tuto úlohu můžete nakonfigurovat tak, aby vykazovala výsledky pro konkrétní nástroje nebo pro všechny nástroje. Můžete také zvolit, jakou úroveň problému chcete nahlásit, například pouze chyby nebo chyby a upozornění.

### <a name="post-analysis-build-break"></a>Po analýze (přerušení sestavení)

S úlohou sestavení po analýze můžete vložit přerušení sestavení, které záměrně způsobí selhání sestavení. Vložíte přerušení sestavení, pokud jeden nebo více analytických nástrojů hlásí problémy v kódu.

Tento úkol můžete nakonfigurovat tak, aby přerušil sestavení pro problémy nalezené určitými nástroji nebo všemi nástroji. Můžete jej také nakonfigurovat na základě závažnosti zjištěných problémů, jako jsou chyby nebo upozornění.

>[!NOTE]
>Podle návrhu každý úkol sestavení úspěšné, pokud úkol úspěšně dokončena. To platí bez ohledu na to, zda nástroj najde problémy, takže sestavení lze spustit až do dokončení povolením spuštění všech nástrojů.

## <a name="next-steps"></a>Další kroky

Pokyny k připojení a instalaci analýzy kódu zabezpečení společnosti Microsoft naleznete [v příručce Onboarding and Installation Guide](security-code-analysis-onboard.md).

Další informace o konfiguraci úloh sestavení naleznete v [příručce konfigurace](security-code-analysis-customize.md) nebo [v průvodci konfigurací YAML](yaml-configuration.md).

Máte-li další dotazy týkající se rozšíření a nabízených nástrojů, podívejte se na naši [stránku faq](security-code-analysis-faq.md).
