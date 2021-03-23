---
title: Přehled dokumentace analýzy kódu zabezpečení společnosti Microsoft
description: Přečtěte si o rozšíření Microsoft Security Code Analysis Extension. Pomocí tohoto rozšíření můžete přidat analýzu kódu zabezpečení do kanálů Azure DevOps CI/ID.
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
ms.openlocfilehash: 4443bdbc134f222256c6af8bb1f52c684cdb4624
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801192"
---
# <a name="about-microsoft-security-code-analysis"></a>Informace o analýze kódu zabezpečení společnosti Microsoft

> [!Note]
> Od 1. března 2022 bude rozšíření Microsoft Security Code Analysis (MSCA) vyřazeno. Stávající zákazníci MSCA si budou mít přístup k MSCA do 1. března 2022. Alternativní možnosti v Azure DevOps najdete v [nástrojích pro analýzu zdrojového kódu OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) . Zákazníci, kteří plánují migraci na GitHub, si můžete zaregistrovat v [rozšířeném zabezpečení GitHubu](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Díky rozšíření Microsoft Security Code Analysis může týmy přidat analýzu kódu zabezpečení do svých kanálů Azure DevOps průběžné integrace a doručování (CI/CD). Tuto analýzu doporučuje odborníci na [zabezpečení SDL (Secure Development Lifecycle)](https://www.microsoft.com/securityengineering/sdl/practices) v Microsoftu.

Konzistentní uživatelské prostředí zjednodušuje zabezpečení skrytím složitosti spuštěných nástrojů. Díky doručování nástrojů na základě NuGet už týmy nepotřebují spravovat instalaci ani aktualizaci nástrojů. Pomocí obou rozhraní příkazového řádku a základního rozhraní pro úlohy sestavení může mít všichni uživatelé co nejvíc ovládat nástroje, jak chtějí.

Týmy můžou také využívat výkonné postprocessing možnosti, jako například:

- Publikování protokolů pro uchování
- Generují se akce zaměřené na vývojáře.
- Konfigurace konců sestavení u regresních testů.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Proč mám použít analýzu kódu zabezpečení Microsoftu?

### <a name="security-simplified"></a>Zjednodušené zabezpečení

Přidávání nástrojů pro analýzu kódu Microsoft Security do vašeho kanálu Azure DevOps je jednoduché jako přidávání nových úloh. Přizpůsobte úkoly nebo použijte své výchozí chování. Úlohy se spouštějí jako součást kanálu Azure DevOps a vytváří protokoly, které podrobně popisují mnoho druhů výsledků.

### <a name="clean-builds"></a>Vyčistit sestavení

Po vyřešení počátečních problémů hlášených nástroji můžete nakonfigurovat rozšíření pro přerušení sestavení při nových problémech. Nastavení průběžné integrace na každé žádosti o přijetí změn je snadné.

### <a name="set-it-and-forget-it"></a>Nastavit a zapomenout

Ve výchozím nastavení jsou úlohy a nástroje sestavení stále aktuální. Pokud je k dispozici aktualizovaná verze nástroje, nemusíte ji stahovat a instalovat. Rozšíření se za vás postará o aktualizaci.

### <a name="under-the-hood"></a>Pod pokličkou

Úlohy sestavení rozšíření skryjí složitosti:
  - Spouští se nástroje pro statickou analýzu zabezpečení.
  - Zpracování výsledků ze souborů protokolu pro vytvoření souhrnné sestavy nebo přerušení sestavení.

## <a name="microsoft-security-code-analysis-tool-set"></a>Sada nástrojů pro analýzu kódu zabezpečení společnosti Microsoft

Rozšíření Microsoft Security Code Analysis Extension zpřístupňuje nejnovější verze důležitých analytických nástrojů, které jsou vám k dispozici. Toto rozšíření zahrnuje nástroje spravované od Microsoftu i open source nástroje.

Tyto nástroje se automaticky stáhnou do agenta hostovaného v cloudu po použití odpovídající úlohy sestavení ke konfiguraci a spuštění kanálu.

V této části jsou uvedeny sady nástrojů, které jsou aktuálně k dispozici v rozšíření. Sledujte přidání dalších nástrojů. Pošlete nám také svoje návrhy na nástroje, které chceme přidat.

### <a name="anti-malware-scanner"></a>Skener proti malwaru

Úloha sestavení pro kontrolu proti malwaru je teď součástí rozšíření Microsoft Security Code Analysis Extension. Tato úloha musí být spuštěna na agentu sestavení, který je již nainstalován v programu Windows Defender. Další informace najdete na [webu Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

BinSkim je přenosný spustitelný soubor (PE), který ověřuje nastavení kompilátoru, nastavení linkeru a další charakteristiky binárních souborů souvisejících se zabezpečením. Tato úloha sestavení poskytuje obálku příkazového řádku kolem konzolové aplikace binskim.exe. BinSkim je open source nástroj. Další informace najdete v tématu [BinSkim na GitHubu](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Skener přihlašovacích údajů

Hesla a další tajné kódy uložené ve zdrojovém kódu jsou významné problémy. Skener přihlašovacích údajů je proprietární Nástroj pro statickou analýzu, který pomáhá tento problém vyřešit. Nástroj detekuje přihlašovací údaje, tajné kódy, certifikáty a další citlivý obsah ve zdrojovém kódu a ve výstupu sestavení.

### <a name="roslyn-analyzers"></a>Analyzátory Roslyn

Analyzátory Roslyn jsou nástrojem integrovaným kompilátorem Microsoftu pro statickou analýzu spravovaného kódu C# a Visual Basic. Další informace najdete v tématu [analyzátory založené na Roslyn](/dotnet/fundamentals/code-analysis/quality-rules/security-warnings).

### <a name="tslint"></a>TSLint

TSLint je rozšiřitelný Nástroj pro statickou analýzu, který kontroluje čitelnost, udržovatelnost a chyby ve funkcích v kódu TypeScript. Je široce podporován moderními editory a systémy pro sestavování. Můžete ji přizpůsobit vlastními pravidly Lint, konfiguracemi a formátovacími moduly. TSLint je open source nástroj. Další informace najdete v tématu [TSLint na GitHubu](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Analýza a následné zpracování výsledků

Rozšíření Microsoft Security Code Analysis má také tři úlohy postprocessing. Tyto úlohy vám pomůžou analyzovat výsledky zjištěné úkoly nástroje Security. Při přidání do kanálu se tyto úlohy obvykle řídí všemi ostatními úlohami nástroje.

### <a name="publish-security-analysis-logs"></a>Publikování protokolů analýzy zabezpečení

Úloha sestavení protokoly analýzy zabezpečení zachovává soubory protokolů nástrojů zabezpečení, které jsou spuštěny během sestavení. Tyto protokoly si můžete přečíst pro účely šetření a následného zpracování.

Soubory protokolu můžete publikovat, aby Azure Artifacts jako soubor. zip. Můžete je také zkopírovat do přístupné sdílené složky z vašeho privátního agenta sestavení.

### <a name="security-report"></a>Sestava zabezpečení

Úloha sestavení sestavy zabezpečení analyzuje soubory protokolů. Tyto soubory jsou vytvořeny pomocí nástrojů zabezpečení, které jsou spuštěny během sestavení. Úloha sestavení potom vytvoří jeden souhrnný soubor sestavy. Tento soubor zobrazuje všechny problémy zjištěné nástroji pro analýzu.

Tuto úlohu můžete nakonfigurovat tak, aby nahlásila výsledky pro konkrétní nástroje nebo pro všechny nástroje. Můžete také zvolit, jakou úroveň problému chcete ohlásit, jako pouze chyby nebo chyby a upozornění.

### <a name="post-analysis-build-break"></a>Po analýze (přerušení sestavení)

Pomocí úlohy sestavení po analýze můžete vložit přerušení sestavení, které záměrně způsobí selhání sestavení. Zadáte zalomení sestavení, pokud jeden nebo více sestav nástroje pro analýzu ohlásí problémy v kódu.

Tuto úlohu můžete nakonfigurovat pro přerušení sestavení pro problémy nalezené konkrétními nástroji nebo všemi nástroji. Můžete ji také nakonfigurovat na základě závažnosti zjištěných problémů, jako jsou chyby nebo upozornění.

>[!NOTE]
>Podle návrhu je každá úloha sestavení úspěšná, pokud se úloha úspěšně dokončí. To je pravdivé bez ohledu na to, zda nástroj najde problémy, aby bylo možné spustit sestavení pomocí povolení všech nástrojů.

## <a name="next-steps"></a>Další kroky

Pokyny, jak připojit a nainstalovat Microsoft Security Code Analysis, najdete v naší [příručce k registraci a instalaci](security-code-analysis-onboard.md).

Další informace o konfiguraci úloh sestavení najdete v našem průvodci [konfigurací](security-code-analysis-customize.md) nebo v [příručce konfigurace YAML](yaml-configuration.md).

Pokud máte další dotazy týkající se rozšíření a nástrojů, které jsou k dispozici, podívejte se na [stránku Nejčastější dotazy](security-code-analysis-faq.md).