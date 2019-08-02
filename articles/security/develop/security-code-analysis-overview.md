---
title: Přehled dokumentace k analýze kódů zabezpečení Microsoft Azure
description: Tento článek představuje přehled rozšíření analýzy kódu zabezpečení.
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
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718285"
---
# <a name="about-microsoft-security-code-analysis"></a>Informace o analýze kódu zabezpečení společnosti Microsoft

**Rozšíření pro analýzu kódu Microsoft Security** umožňuje týmům plynule integrovat analýzu kódu zabezpečení do jejich kanálů CI/CD DevOps Azure, jak doporučuje odborníci na rozhraní [SDL (Secure Development Lifecycle)](https://www.microsoft.com/securityengineering/sdl/practices) v Microsoftu. Zabezpečení je zjednodušené díky konzistentnímu UŽIVATELSKÉmu prostředí, které vyabstrakcí složité spouštění různých nástrojů. Díky doručování nástrojů na základě NuGet už týmy nepotřebují spravovat ani instalovat nástroje. Díky rozhraním příkazového řádku a základnímu sestavování úloh pro všechny uživatele, od Gurus nástrojů až po každodenní vývojáře, může mít méně nebo tolik toho, co chtějí nástroje podle svých přání. Týmy mohou také využít výkonné funkce pro následné zpracování, jako je publikování protokolů pro uchovávání, generování sestav zaměřených na vývojáře & konfiguraci konců sestavení na regresích.

## <a name="why-microsoft-security-code-analysis"></a>Důvody analýzy kódu zabezpečení společnosti Microsoft

### <a name="security-simplified"></a>Zjednodušené zabezpečení

Přidávání nástrojů pro analýzu kódu zabezpečení do kanálu Azure DevOps je jednoduché jako přidávání nových úloh. Přizpůsobte si je nebo použijte výchozí nastavení. Úlohy se spouštějí jako součást kanálu DevOps a vytváří protokoly podrobně o všech druzích zjištění.

### <a name="clean-builds"></a>Vyčistit sestavení

Po vyřešení počátečních problémů hlášených nástroji můžete nakonfigurovat rozšíření pro přerušení sestavení při nových problémech. Nastavení průběžné integrace na každé žádosti o přijetí změn nebylo nikdy jednoduché.

### <a name="set-it-and-forget-it"></a>Nastavit a zapomenout

Úlohy a nástroje sestavení lze nastavit tak, aby byly stále aktuální (a jsou ve výchozím nastavení). Pokud je k dispozici aktualizovaná verze nástroje, není nutné ji stahovat a instalovat. Toto rozšíření se za vás zajímá. 

>>>
### <a name="under-the-hood"></a>Pod kapotou

Úlohy sestavení rozšíření Microsoft Security Code Analysis jsou složité:
  - Spouští se nástroje pro statickou analýzu zabezpečení a
  - Zpracování výsledků ze souborů protokolu pro vytvoření souhrnné sestavy nebo přerušení sestavení.
>>>

## <a name="security-code-analysis-toolset"></a>Sada nástrojů pro analýzu kódu zabezpečení

Rozšíření pro analýzu kódu Microsoft Security vám nabízí snadno dostupné verze důležitých analytických nástrojů. Rozšíření zahrnuje interní i open source nástroje Microsoftu. Po nakonfigurování & spuštění kanálu pomocí odpovídající úlohy sestavení se nástroje automaticky stáhnou do agenta hostovaného v cloudu. Níže je uvedená sada nástrojů, které jsou dnes k dispozici v rozšíření. Udržujte si lepší přehled a pošlete nám svoje návrhy na nástroje, které by se daly přidat.

### <a name="anti-malware-scanner"></a>Skener proti malwaru

Úloha sestavení pro kontrolu proti malwaru je teď součástí rozšíření Microsoft Security Code Analysis Extension. Musí být spuštěn na agentu sestavení, který má již nainstalovaný program Windows Defender. Další informace najdete na [webu Defenderu](https://aka.ms/defender) . 

### <a name="binskim"></a>BinSkim

BinSkim je přenosný spustitelný soubor (PE) na nejvyšší úrovni, který ověřuje nastavení kompilátoru/linkeru a další binární charakteristiky související se zabezpečením. Úloha sestavení poskytuje obálku příkazového řádku kolem aplikace BinSkim. exe. BinSkim je open source nástroj a další informace najdete [v BinSkim na GitHubu](https://github.com/Microsoft/binskim) .

### <a name="credential-scanner"></a>Skener přihlašovacích údajů

Hesla a další tajné kódy uložené ve zdrojovém kódu jsou momentálně významným problémem. Skener přihlašovacích údajů je proprietární statický Nástroj pro analýzu, který detekuje přihlašovací údaje, tajné kódy, certifikáty a další citlivý obsah ve zdrojovém kódu a ve výstupu sestavení.

### <a name="microsoft-security-risk-detection"></a>Zjišťování rizik zabezpečení společnosti Microsoft

Detekce rizik zabezpečení je jedinečná cloudová služba pro přibližné testování od Microsoftu, která identifikuje zneužití chyb zabezpečení v softwaru. Tato služba vyžaduje samostatný proces připojování. Další informace najdete [na webu MSRD na docs.Microsoft.com](https://docs.microsoft.com/security-risk-detection/) .

### <a name="roslyn-analyzers"></a>Analyzátory Roslyn

Statická analýza integrovaná s kompilátorem Microsoftu pro analýzu spravovaného kódu (C# a VB). Další informace najdete [v analyzátorech Roslyn na docs.Microsoft.com](https://docs.microsoft.com/dotnet/standard/analyzers/) .

### <a name="tslint"></a>TSLint

TSLint je rozšiřitelný Nástroj pro statickou analýzu, který kontroluje kód TypeScriptu pro čtení, udržovatelnost a chyby funkcí. Je široce podporovaná napříč moderními editory a systémy sestavení a je možné ji přizpůsobit vlastními pravidly Lint, konfiguracemi a formátovacími moduly. TSLint je open source nástroj a další informace najdete [v TSLint na GitHubu](https://github.com/palantir/tslint) .

## <a name="analysis-and-post-processing-of-results"></a>Analýza a následné zpracování výsledků

Rozšíření Microsoft Security Code Analysis má také tři užitečné úlohy následného zpracování, které vám pomůžou zpracovat a analyzovat výsledky zjištěné úlohami nástrojů zabezpečení. Obvykle se přidávají do kanálu po všech ostatních úkolech nástroje.

### <a name="publish-security-analysis-logs"></a>Publikování protokolů analýzy zabezpečení
Úloha sestavení protokoly analýzy zabezpečení zachovává soubory protokolů, které jsou spuštěny během sestavení pro účely šetření a následné zpracování.

Mohou být publikovány do artefaktů serveru Azure (jako soubor ZIP) nebo zkopírovány do přístupné sdílené složky z vašeho privátního agenta sestavení.

### <a name="security-report"></a>Sestava zabezpečení
Úloha sestavení sestavy zabezpečení analyzuje soubory protokolů vytvořené pomocí nástrojů zabezpečení spouštěných během sestavení a vytvoří jeden soubor souhrnných sestav se všemi problémy, které nalezly analytické nástroje.

Úkol lze nakonfigurovat tak, aby nahlásil nálezy pro konkrétní nástroje nebo pro všechny nástroje, a můžete také zvolit, jakou úroveň problémů mají být hlášeny (chyby nebo chyby a upozornění).

### <a name="post-analysis-build-break"></a>Po analýze (přerušení sestavení)
Úloha sestavení po analýze umožňuje zákazníkovi vložit přerušení sestavení a selže sestavení v případě, že jeden z dalších analytických nástrojů ohlásí výsledky nebo problémy v kódu.

Úkol lze nakonfigurovat tak, aby přerušil sestavení pro problémy nalezené konkrétními nástroji nebo pro všechny nástroje a také na základě závažnosti problémů zjištěných (chyby nebo varování).

>[!NOTE]
>Jednotlivé úlohy sestavení budou úspěšné, a to tak dlouho, dokud se nástroj úspěšně dokončí, ať už se jedná o zjištění, nebo ne, aby bylo sestavení možné spustit, aby se mohly spouštět všechny nástroje.

## <a name="next-steps"></a>Další postup

Pokyny k registraci a instalaci analýzy kódu zabezpečení najdete v naší [příručce k registraci a instalaci](security-code-analysis-onboard.md) .

Další informace o konfiguraci úloh sestavení najdete v našem [Průvodci konfigurací](security-code-analysis-customize.md) .

Pokud máte další dotazy týkající se rozšíření a nabízených nástrojů, podívejte se na [stránku Nejčastější dotazy.](security-code-analysis-faq.md)