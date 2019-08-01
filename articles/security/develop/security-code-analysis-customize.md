---
title: Průvodce přizpůsobením úlohy Microsoft Azure Security Code Analysis
description: Tento článek se týká přizpůsobení úloh v rozšíření analýza kódu zabezpečení.
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
ms.openlocfilehash: ab219b71eb8cd6f6172b7d02a639301c67811b49
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718350"
---
# <a name="how-to-configure--customize-the-build-tasks"></a>Jak: Konfigurace & přizpůsobení úloh sestavení

Tato stránka podrobně popisuje možnosti konfigurace, které jsou k dispozici v jednotlivých úlohách sestavení, počínaje úlohami nástrojů pro analýzu kódu zabezpečení následovanými úlohami po zpracování.

## <a name="anti-malware-scanner-task"></a>Úloha skeneru ochrany proti malwaru

> [!NOTE]
> Úloha sestavení proti malwaru vyžaduje, aby byl agent sestavení s povoleným Windows Defenderem, který má hodnotu true u "hostovaného VS2017" a novějších agentů sestavení. (Nespustí se na hostovaném agentovi starší/VS2015) Podpisy nelze na těchto agentech aktualizovat, ale signatura by měla být vždy poměrně aktuální, méně než 3 hodiny staré.

Snímek obrazovky a podrobnosti o konfiguraci níže.

![Přizpůsobení úlohy sestavení pro kontrolu proti malwaru](./media/security-tools/5-add-anti-malware-task600.png) 

- Nastavení pro typ = **základní**
- K přizpůsobení kontroly můžete zadat typ = **vlastní**, argumenty příkazového řádku.

Windows Defender používá klienta web Windows Update ke stažení a instalaci signatur. Pokud se aktualizace signatury v agentu sestavení nezdařila, kód chyby HRESULT bude nejspíš přijít z web Windows Update. Další informace o chybách web Windows Update a jejich zmírnění najdete na [této stránce](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) a na této [stránce na webu TechNet](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx) .

## <a name="binskim-task"></a>BinSkim – úloha

> [!NOTE]
> Podmínkou pro spuštění úlohy BinSkim by vaše sestavení mělo splňovat jednu z následujících podmínek.
>    - Sestavení vytváří binární artefakty ze spravovaného kódu.
>    - Máte navázané binární artefakty, které byste chtěli analyzovat pomocí BinSkim.

Snímek obrazovky a podrobnosti o konfiguraci níže. 

![Nastavení BinSkim](./media/security-tools/7-binskim-task-details.png)  
1. Nastavte konfiguraci sestavení na ladit, aby se vytvořily soubory ladění * **. pdb** . Používají se v BinSkim k mapování problémů nalezených ve výstupním binárním souboru zpátky do zdrojového kódu. 
2. Pokud se chcete vyhnout opakovanému vyhledávání a vytváření vlastního příkazového řádku, vyberte typ = **základní** & funkce = **analyzovat** . 
3. **Target** – jeden nebo více specifikátorů pro soubor, adresář nebo vzor filtru, který se překládá na jeden nebo více binárních souborů, které se mají analyzovat. 
    - Více cílů by mělo být odděleno **středníkem (;)** . 
    - Může to být jeden soubor nebo obsahovat zástupné znaky.
    - Adresáře by vždy měly končit\*
    - Příklady:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
4. Pokud vyberete typ = **příkazový řádek**, 
     - Ujistěte se, že první argument **BinSkim. exe** je příkaz **analyzovat** pomocí úplných cest nebo cest relativně ke zdrojovému adresáři.
     - V případě vstupu z **příkazového řádku** by mělo být více cílů odděleno mezerou.
     - Můžete vynechat parametr souboru **/o** nebo **/Output** ; bude přidáno pro vás nebo nahrazeno. 
     - **Konfigurace standardního příkazového řádku** 

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
          > [!NOTE]
          > V případě zadání \* adresáře nebo adresářů pro cíl je koncový čas velmi důležitý. 

Další podrobnosti o BinSkim o argumentech příkazového řádku najdete v části pravidla podle ID nebo ukončovacích kódů najdete v [uživatelské příručce k BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md) .

## <a name="credential-scanner-task"></a>Úloha pro kontrolu přihlašovacích údajů
Snímek obrazovky a podrobnosti o konfiguraci níže.
 
![Vlastní nastavení skeneru přihlašovacích údajů](./media/security-tools/3-taskdetails.png)

K dispozici jsou tyto možnosti: 
  - **Formát výstupu** – TSV/CSV/SARIF/Fast
  - **Verze nástroje** Doporučil Nejnovější
  - **Složka pro skenování** – složka v úložišti, která se má prohledat
  - **Typ souboru vyhledávacích vyhledávačů** – možnosti pro vyhledání vyhledávacích souborů používaných ke skenování.
  - **Soubor potlačení** – soubor [JSON](https://json.org/) se dá použít k potlačení problémů v protokolu výstupu (další podrobnosti najdete v části Resources). 
  - **Podrobný výstup** – vysvětlivekný 
  - **Velikost dávky** – počet souběžných vláken používaných k paralelnímu spouštění skenerů přihlašovacích údajů. Výchozí hodnota je 20 (hodnota musí být v rozsahu od 1 do 2147483647).
  - **Časový limit shody** – doba strávená pokusem o vyrovnávání výsledků hledání před opuštěním kontroly. 
  - **Velikost vyrovnávací paměti pro čtení prohledávání souborů** – velikost vyrovnávací paměti při čtení obsahu v bajtech (Výchozí hodnota je 524288) 
  - **Maximální počet čtení z kontroly souborů** – maximální počet bajtů ke čtení z daného souboru během analýzy obsahu. (Výchozí hodnota je 104857600) 
  - **Spustit tuto úlohu** (v části **možnosti ovládacího prvku**) – určuje, kdy se má úloha spustit. Pokud chcete zadat složitější podmínky, zvolte vlastní podmínky. 
  - Verze úlohy sestavení **verze** v rámci služby Azure DevOps. Nepoužívá se často. 

## <a name="microsoft-security-risk-detection-task"></a>Úloha zjišťování rizik zabezpečení společnosti Microsoft
> [!NOTE]
> Aby bylo možné tuto úlohu používat, je nutné vytvořit a nakonfigurovat účet se službou zjišťování rizik jako předpokladem. Tato služba vyžaduje samostatný proces připojování. u většiny ostatních úloh v tomto rozšíření není plug and Play. Přečtěte si prosím [zjišťování rizik Microsoftu](https://aka.ms/msrddocs) a [zjišťování rizik Microsoftu pro zabezpečení: ](https://docs.microsoft.com/security-risk-detection/how-to/) Postup pro pokyny.

Podrobnosti o konfiguraci níže.

Zadejte požadovaná data. Každá možnost má textovou podobu myši.
   - **Název koncového bodu služby Azure DevOps pro MSRD**: Pokud jste vytvořili obecný typ koncového bodu služby Azure DevOps k uložení adresy URL instance MSRD (připojili jste k) a REST API přístupového tokenu, můžete zvolit koncový bod služby. V takovém případě klikněte na odkaz Správa a vytvořte a nakonfigurujte nový koncový bod služby pro tuto úlohu MSRD. 
   - **ID účtu**: Je to identifikátor GUID, který se dá načíst z adresy URL účtu MSRD.
   - **Adresy URL pro binární soubory**: Seznam veřejně dostupných adres URL oddělený středníkem (který se použije pro přibližný počítač ke stažení binárních souborů).
   - **Adresy URL počátečních souborů**: Seznam veřejně dostupných adres URL oddělený středníkem (který se použije pro přibližný počítač ke stažení semen). Toto pole je volitelné, pokud jsou soubory počáteční hodnoty staženy společně s binárními soubory.
   - **Typ platformy operačního systému**: Typ platformy operačního systému (Windows nebo Linux) počítačů, na kterých má běžet Přibližná úloha.
   - Edice **Windows/Linux Edition**: Edice operačních systémů počítačů, na kterých má běžet fuzzy úlohu. Výchozí hodnotu můžete přepsat, pokud vaše počítače mají jinou edici operačního systému.
   - **Instalační skript balíčku**: Zadejte skript, který bude spuštěn na testovacím počítači pro instalaci cílového programu testu a jeho závislostí před odesláním fuzzy úlohy.
   - **Parametry odeslání úlohy**:
       - **Počáteční adresář**: Cesta k adresáři v přibližném počítači, který obsahuje semena.
       - **Rozšíření počáteční**hodnoty: Přípona souboru semen
       - **Spustitelný soubor testovacího ovladače**: Cesta k cílovému spustitelnému souboru v rozmazaných počítačích.
       - **Architektura spustitelných souborů testovacího ovladače**: Architektura cílového spustitelného souboru (x86 nebo amd64).
       - **Argumenty testovacího ovladače**: Argumenty příkazového řádku předané do spustitelného souboru cíle testu. Všimněte si, že symbol "% Testfile%", včetně dvojitých uvozovek, bude automaticky nahrazen úplnou cestou k cílovému souboru, a proto se očekává, že testovací ovladač má být analyzován a je požadován.
       - **Proces testovacího ovladače se ukončí při dokončení testu**: Po dokončení zkontrolujte, že se testovací ovladač ukončí. Zrušte kontrolu, zda je nutné vynutit ukončení testovacího ovladače.
       - **Maximální doba trvání (v sekundách)** : Zadejte odhad nejdelšího předpokládaného času, který vyžaduje, aby cílový program analyzoval vstupní soubor. Přesnější tento odhad, efektivnější běh.
       - **Testovací ovladač lze spustit opakovaně**: Zkontrolujte, zda lze testovací ovladač spustit opakovaně bez závislosti na trvalém/sdíleném globálním stavu.
       - **Testovací ovladač lze přejmenovat**: Zkontrolujte, zda lze přejmenovat spustitelný soubor testovacího ovladače a zda stále funguje správně.
       - **Fuzzy aplikace běží jako jeden proces operačního systému**: Zkontrolujte, zda testovací ovladač běží v rámci jednoho procesu operačního systému. Zrušte kontrolu, zda ovladač testu sekryje další procesy.


## <a name="roslyn-analyzers-task"></a>Úloha analyzátorů Roslyn
> [!NOTE]
> Aby bylo možné spustit úlohu Roslyn Analyzer, musí vaše sestavení splňovat následující podmínky.
>  - Vaše definice sestavení zahrnuje vestavěný úkol MSBuild nebo VSBuild sestavení pro kompilaci C# kódu (nebo VB). Tato úloha spoléhá na vstup a výstup této konkrétní úlohy sestavení pro opětovné spuštění kompilace MSBuild s povolenými analyzátory Roslyn.
>  - Agent sestavení spouštějící tuto úlohu sestavení má nainstalovanou verzi sady Visual Studio 2017 v 15.5 nebo novější (kompilátor verze 2.6. x).
>

Podrobnosti o konfiguraci níže.

K dispozici jsou tyto možnosti: 
- Vyžaduje se **RuleSet** -SDL, doporučuje se SDL nebo můžete použít vlastní RuleSet.
- **Verze analyzátorů** Doporučil Nejnovější
- **Upozornění kompilátoru potlačuje soubor** – textový soubor se seznamem ID upozornění, která by se měla potlačit. 
- **Spustit tuto úlohu** (v části **možnosti ovládacího prvku**) – určuje, kdy se má úloha spustit. Pokud chcete zadat složitější podmínky, zvolte**vlastní podmínky**. 

> [!NOTE]
> - Analyzátory Roslyn jsou integrované kompilátorem a dají se spustit jenom jako součást kompilace CSC. exe. Proto tato úloha vyžaduje opětovné přehrání nebo opětovné spuštění příkazu kompilátoru, který byl spuštěn dříve v sestavení. To se provádí dotazování na VSTS pro protokoly úloh sestavení MSBuild (neexistuje žádný jiný způsob, jak úlohu spolehlivě získat příkazový řádek kompilace MSBuild z definice sestavení; měli byste zvážit přidání volného textového pole, které umožní uživatelům zadat příkazové řádky. , ale je obtížné je udržet v aktuálním stavu a synchronizovat s hlavním sestavením. Vlastní sestavení vyžadují opakované přehrání celé sady příkazů, nikoli jenom příkazy kompilátoru, a není triviální ani spolehlivá, aby v těchto případech povolila analyzátory Roslyn. 
> - Analyzátory Roslyn jsou integrovány s kompilátorem a vyžadují vyvolání kompilace. Tato úloha sestavení je implementována opětovnou C# kompilací projektů, které již byly vytvořeny pomocí pouze úlohy sestavení MSBuild/VSBuild, ve stejné definici sestavení nebo sestavení, ale v tomto případě s povolenými analyzátory. Pokud je tato úloha sestavení spuštěna ve stejném agentě jako původní úloha sestavení, výstup původní úlohy sestavení MSBuild/VSBuild bude přepsán ve složce sources "Source" na základě výstupu této úlohy sestavení. Výstup sestavení bude stejný, ale doporučujeme, abyste spustili nástroj MSBuild, zkopírovali výstup do pracovního adresáře artefakty a pak spustili Roslyn.
>

Další zdroje informací pro analyzátory Roslyn pro kontrolu úloh analyzátory [Roslyn na Microsoft docs](https://docs.microsoft.com/dotnet/standard/analyzers/)

Balíček analyzátoru nainstalovaného a používaného tímto úkolem sestavení najdete [tady](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) . 

## <a name="tslint-task"></a>TSLint – úloha

Další informace o TSLint najdete v [úložišti GitHub TSLint](https://github.com/palantir/tslint) .
>[!NOTE] 
>Vzhledem k tomu, že si možná budete vědomi, TSLint se už v 2019 (zdroj: [Úložiště GitHub TSLint](https://github.com/palantir/tslint)) Tým aktuálně zkoumá [ESLint](https://github.com/eslint/eslint) jako alternativu a vytvoření nové úlohy pro ESLint je v plánu.

## <a name="publish-security-analysis-logs-task"></a>Úloha publikování protokolů analýzy zabezpečení
Snímek obrazovky a podrobnosti o konfiguraci níže.

![Přizpůsobení analýzy zabezpečení publikování](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Název artefaktu** – může to být libovolný identifikátor řetězce.
- **Typ artefaktu** – můžete publikovat protokoly na serveru Azure-DevOps nebo do sdílené složky, která je pro agenta sestavení přístupná. 
- **Nástroje** – můžete si vybrat, že zachováte protokoly pro jednotlivé nebo specifické nástroje, nebo vyberete **všechny nástroje** , abyste zachovali všechny protokoly. 

## <a name="security-report-task"></a>Úloha sestavy zabezpečení
Snímek obrazovky a podrobnosti o konfiguraci níže.  
![Nastavení po analýze](./media/security-tools/4-createsecurityanalysisreport600.png) 
- **Sestavy** – vyberte soubory sestav, které chcete vytvořit; jedna se vytvoří v **konzole**formátů, **TSV**a/nebo **HTML** . 
- **Nástroje** – vyberte nástroje v definici sestavení, pro které byste chtěli zjistit souhrn zjištěných problémů. Pro každý vybraný nástroj může být k dispozici možnost vybrat, jestli chcete zobrazit jenom chyby, nebo jak chyby, tak upozornění v sestavě. 
- **Pokročilé možnosti** – můžete si zvolit, že se má zaznamenat upozornění nebo chyba (a úloha selže) pro případ, že nejsou k dispozici žádné protokoly pro jeden z vybraných nástrojů.
Můžete přizpůsobit složku základních protokolů, kde se protokoly mají najít, ale toto není Typický scénář. 

## <a name="post-analysis-task"></a>Úkol po analýze
Snímek obrazovky a podrobnosti o konfiguraci níže.

![Přizpůsobení po analýze](./media/security-tools/a-post-analysis600.png) 
- **Nástroje** – vyberte nástroje v definici sestavení, pro které chcete vložit přerušení sestavení na základě jeho zjištění. Pro každý vybraný nástroj může být k dispozici možnost vybrat, zda chcete přerušit pouze chyby nebo chyby a upozornění. 
- **Sestava** – můžete volitelně napsat výsledky, které jsou nalezeny a které způsobují přerušení sestavení v okně konzoly Azure DevOps a souboru protokolu. 
- **Pokročilé možnosti** – můžete si zvolit, že se má zaznamenat upozornění nebo chyba (a úloha selže) pro případ, že nejsou k dispozici žádné protokoly pro jeden z vybraných nástrojů.

## <a name="next-steps"></a>Další kroky

Pokud máte další dotazy týkající se rozšíření a nabízených nástrojů, podívejte se na [stránku Nejčastější dotazy.](security-code-analysis-faq.md)


