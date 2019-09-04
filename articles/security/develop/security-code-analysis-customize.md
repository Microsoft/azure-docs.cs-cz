---
title: Průvodce přizpůsobením úlohy Microsoft Azure Security Code Analysis
description: Tento článek popisuje přizpůsobení úloh v rozšíření Microsoft Security Code Analysis.
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
ms.openlocfilehash: c0d49c3ce06f6fa72daf7aff466ef65e09ced09a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241797"
---
# <a name="configure-and-customize-the-build-tasks"></a>Konfigurace a přizpůsobení úloh sestavení

Tento článek podrobně popisuje možnosti konfigurace, které jsou k dispozici v jednotlivých úkolech sestavení. Článek začíná úkoly nástroje pro analýzu kódu zabezpečení. Končí úlohami po zpracování.

## <a name="anti-malware-scanner-task"></a>Úloha skeneru ochrany proti malwaru

>[!NOTE]
> Úloha sestavení pro kontrolu proti malwaru vyžaduje, aby byl v programu Windows Defender povolen agent sestavení. Hostitelem sady Visual Studio 2017 a novějších je například agent. V hostovaném agentu sady Visual Studio 2015 se úloha sestavení nespustí.
>
> I když signatury nelze na těchto agentech aktualizovat, signatury by měly být vždy méně než tři hodiny staré.

Podrobnosti o konfiguraci úlohy jsou uvedené na následujícím snímku obrazovky a textu.

![Konfigurace úlohy sestavení pro kontrolu proti malwaru](./media/security-tools/5-add-anti-malware-task600.png)

V poli se seznamem **typů** na snímku obrazovky je vybrána možnost **základní** . Vyberte možnost **vlastní** a zadejte argumenty příkazového řádku, které prověřování přizpůsobují.

Windows Defender používá klienta web Windows Update ke stažení a instalaci signatur. Pokud se aktualizace signatury v agentu sestavení nezdařila, kód chyby **HRESULT** bude nejspíš přijít z web Windows Update.

Další informace o chybách web Windows Update a jejich zmírnění najdete v tématu [web Windows Update kódy chyb podle součásti](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) a článku [web Windows Update agenta – kódy chyb](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

## <a name="binskim-task"></a>BinSkim – úloha

> [!NOTE]
> Předtím, než můžete spustit úlohu BinSkim, musí sestavení splňovat jednu z těchto podmínek:
>    - Sestavení vytváří binární artefakty ze spravovaného kódu.
>    - Máte potvrzené binární artefakty, které chcete analyzovat pomocí BinSkim.

Podrobnosti o konfiguraci úlohy jsou uvedené na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení BinSkim](./media/security-tools/7-binskim-task-details.png)

- Nastavte konfiguraci sestavení na ladit tak, aby byly vytvořeny soubory ladění. pdb. BinSkim tyto soubory používá k namapování problémů ve výstupních binárních souborech zpátky do zdrojového kódu.
- Aby nedošlo k opakovanému vyhledávání a vytváření vlastního příkazového řádku:
     - V seznamu **typ** vyberte **základní**.
     - V seznamu **funkce** vyberte **analyzovat**.
- Do vlastnosti **cíl**zadejte jeden nebo více specifikátorů pro soubor, adresář nebo vzor filtru. Tyto specifikátory jsou přeloženy na jeden nebo více binárních souborů, které mají být analyzovány:
    - Více zadaných cílů musí být oddělených středníkem (;).
    - Specifikátorem může být jeden soubor nebo obsahovat zástupné znaky.
    - Specifikace adresáře musí vždy končit \\znakem *.
    - Příklady:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Pokud v seznamu **typ** vyberete položku **příkazový řádek** , je nutné spustit binskim. exe:
     - Ujistěte se, že první argumenty pro binskim. exe jsou příkazy **analyzovat** následované jednou nebo více specifikacemi cesty. Každá cesta může být buď úplná cesta, nebo cesta relativní ke zdrojovému adresáři.
     - Více cílových cest musí být odděleny mezerou.
     - Můžete vynechat možnost **/o** nebo **/Output** . Výstupní hodnota je přidána nebo nahrazena.
     - Standardní konfigurace příkazového řádku jsou uvedené níže.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > Koncový \\znak * je důležitý, pokud zadáte adresáře pro cíl.

Další informace o argumentech příkazového řádku BinSkim, pravidlech podle ID nebo ukončovacích kódech najdete v [uživatelské příručce k BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

## <a name="credential-scanner-task"></a>Úloha pro kontrolu přihlašovacích údajů

Podrobnosti o konfiguraci úlohy jsou uvedené na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení pro kontrolu přihlašovacích údajů](./media/security-tools/3-taskdetails.png)

K dispozici jsou tyto možnosti:

  - **Výstupní formát**: K dispozici jsou tyto hodnoty: **TSV**, **CSV**, **SARIF**a **Fast**.
  - **Verze nástroje**: Doporučujeme vybrat možnost **nejnovější**.
  - **Složka pro skenování**: Složka úložiště, která se má zkontrolovat
  - **Typ souboru vyhledávacích souborů**: Možnosti pro vyhledání souboru vyhledávacích souborů, který se používá ke skenování.
  - **Soubor potlačení**: Soubor [JSON](https://json.org/) může potlačit problémy ve výstupním protokolu. Další informace o scénářích potlačení najdete v části Nejčastější dotazy v tomto článku.
  - **Podrobný výstup**: Zřejmá.
  - **Velikost dávky**: Počet souběžných vláken používaných ke spuštění skeneru přihlašovacích údajů. Výchozí hodnota je 20. Možné hodnoty jsou v rozsahu od 1 do 2 147 483 647.
  - **Časový limit shody**: Doba v sekundách, po kterou se při pokusu o přijetí změn shoduje se se zadaným vyhledávacím objektem, než zrušíte kontrolu.
  - **Velikost vyrovnávací paměti pro čtení prohledávání souborů**: Velikost vyrovnávací paměti použité při čtení obsahu v bajtech Výchozí hodnota je 524 288.  
  - **Maximální počet přečtených bajtů při kontrole souborů**: Maximální počet bajtů, které mají být čteny ze souboru při analýze obsahu. Výchozí hodnota je 104 857 600.
  - **Možnosti ovládacích prvků** **spouštějí tuto úlohu:**  >  Určuje, kdy se úloha spustí. Pokud chcete zadat složitější podmínky, vyberte **vlastní podmínky** .
  - **Verze**: Verze úlohy sestavení v rámci služby Azure DevOps. Tato možnost se často nepoužívá.

## <a name="microsoft-security-risk-detection-task"></a>Úloha zjišťování rizik zabezpečení společnosti Microsoft

> [!NOTE]
> Před použitím úlohy MSRD je nutné vytvořit a nakonfigurovat účet se službou Microsoft Security rizikovost Detection (MSRD). Tato služba vyžaduje samostatný proces připojování. Na rozdíl od většiny ostatních úloh v tomto rozšíření Tato úloha vyžaduje samostatné předplatné s MSRD.
>
> Přečtěte si prosím [zjišťování rizik Microsoftu](https://aka.ms/msrddocs) a [zjišťování rizik Microsoftu pro zabezpečení: ](https://docs.microsoft.com/security-risk-detection/how-to/) Postup pro pokyny.

Podrobnosti o konfiguraci této úlohy jsou uvedené v následujícím seznamu. Pro libovolný prvek uživatelského rozhraní můžete na tento prvek umístit ukazatel myši a získat tak nápovědu.

   - **Název koncového bodu služby Azure DevOps pro MSRD**: Obecný typ koncového bodu služby Azure DevOps ukládá adresu URL instance připojeného MSRD a váš přístupový token REST API. Pokud jste takový koncový bod vytvořili, můžete ho zadat tady. V opačném případě vyberte odkaz **Spravovat** pro vytvoření a konfiguraci nového koncového bodu služby pro tuto úlohu MSRD.
   - **ID účtu**: Identifikátor GUID, který se dá načíst z adresy URL účtu MSRD.
   - **Adresy URL pro binární soubory**: Seznam veřejně dostupných adres URL oddělený středníkem. Rozmazaný počítač používá tyto adresy URL ke stažení binárních souborů.
   - **Adresy URL počátečních souborů**: Seznam veřejně dostupných adres URL oddělený středníkem. Rozmazaný počítač používá tyto adresy URL ke stažení semen. Zadání této hodnoty je volitelné, pokud jsou soubory počáteční hodnoty staženy společně s binárními soubory.
   - **Typ platformy operačního systému**: Platforma operačního systému (OS) počítačů, které spouštějí úlohu s fuzzy logikou. Dostupné hodnoty jsou **Windows** a **Linux**.
   - Edice **Windows/Linux Edition**: Edice operačních systémů počítačů, které spouštějí fuzzy úlohu. Výchozí hodnotu můžete přepsat, pokud vaše počítače mají jinou edici operačního systému.
   - **Instalační skript balíčku**: Skript, který má být spuštěn na testovacím počítači. Tento skript nainstaluje cílový program testu a jeho závislosti před odesláním přibližné úlohy.
   - **Parametry odeslání úlohy**:
       - **Počáteční adresář**: Cesta k adresáři v přibližném počítači, který obsahuje semena.
       - **Rozšíření počáteční**hodnoty: Přípona názvu souboru semen.
       - **Spustitelný soubor testovacího ovladače**: Cesta k cílovému spustitelnému souboru v rozmazaných počítačích.
       - **Architektura spustitelných souborů testovacího ovladače**: Architektura cílového spustitelného souboru. Dostupné hodnoty jsou **x86** a **amd64**.
       - **Argumenty testovacího ovladače**: Argumenty příkazového řádku předané do spustitelného souboru testu. Argument "% Testfile%", včetně uvozovek, je automaticky nahrazen úplnou cestou k cílovému souboru. Tento soubor se analyzuje pomocí ovladače testu a je povinný.
       - **Proces testovacího ovladače se ukončí při dokončení testu**: Toto políčko zaškrtněte, pokud má být test ovladače po dokončení ukončen. Vymažte je, pokud je třeba test ovladače vynuceně zavřít.
       - **Maximální doba trvání (v sekundách)** : Odhad nejdelší rozumně očekávané doby, kterou cílový program vyžaduje k analýze vstupního souboru. Přesnější odhad, efektivnější spuštění aplikace.
       - **Testovací ovladač lze spustit opakovaně**: Toto políčko zaškrtněte, pokud testovací ovladač může běžet opakovaně bez závislosti na trvalém nebo sdíleném globálním stavu.
       - **Testovací ovladač lze přejmenovat**: Toto políčko zaškrtněte, pokud se spustitelný soubor testovacího ovladače dá přejmenovat a pořád funguje správně.
       - **Fuzzy aplikace běží jako jeden proces operačního systému**: Toto políčko zaškrtněte, pokud testovací ovladač běží v rámci jednoho procesu operačního systému. Pokud testovací ovladač setrvá další procesy, vymažte ho.

## <a name="roslyn-analyzers-task"></a>Úloha analyzátorů Roslyn

> [!NOTE]
> Předtím, než budete moci spustit úlohu analyzátorů Roslyn, musí sestavení splňovat tyto podmínky:
> - Vaše definice sestavení zahrnuje vestavěný úkol MSBuild nebo VSBuild sestavení pro zkompilování C# nebo Visual Basic kódu. Úloha analyzátory spoléhá na vstup a výstup vestavěné úlohy ke spuštění kompilace MSBuild s povolenými analyzátory Roslyn.
> - Agent sestavení spouštějící tuto úlohu sestavení má nainstalovanou aplikaci Visual Studio 2017 verze 15,5 nebo novější, aby používala kompilátor verze 2,6 nebo novější.

Podrobnosti o konfiguraci úlohy jsou uvedené v následujícím seznamu a Poznámka.

K dispozici jsou tyto možnosti:

- **RuleSet**: Hodnoty jsou **vyžadovány v SDL**, doporučuje se pro **SDL**nebo vlastní sada pravidel.
- **Verze analyzátorů**: Doporučujeme vybrat možnost **nejnovější**.
- **Soubor potlačení upozornění kompilátoru**: Textový soubor se seznamem ID upozornění, které se potlačí.
- **Možnosti ovládacích prvků** **spouštějí tuto úlohu:**  >  Určuje, kdy se úloha spustí. Zvolte **vlastní podmínky** a určete složitější podmínky.

> [!NOTE]
> - Analyzátory Roslyn jsou integrovány s kompilátorem a lze je spustit pouze jako součást kompilace CSc. exe. Proto tato úloha vyžaduje, aby byl příkaz kompilátoru, který byl dříve v sestavení spuštěn, znovu přehrán nebo znovu spuštěn. Toto přehrajte nebo spusťte dotazování v Visual Studio Team Services (VSTS) pro protokoly úloh sestavení MSBuild.
>
>   Neexistuje žádný jiný způsob, jak úlohu spolehlivě získat příkazový řádek kompilace MSBuild z definice sestavení. Doporučujeme přidat textové pole volného tvaru, které uživatelům umožní zadat jejich příkazové řádky. Ale pak by bylo obtížné tyto příkazové řádky udržovat v aktuálním stavu a synchronizovat s hlavním sestavením.
>
>   Vlastní sestavení vyžadují opakované přehrání celé sady příkazů, nikoli jenom příkazů kompilátoru. V těchto případech není povolení analyzátorů Roslyn triviální ani spolehlivější.
>
> - Analyzátory Roslyn jsou integrovány s kompilátorem. Aby se vyvolaly, analyzátory Roslyn vyžadují kompilaci.
>
>   Tato nová úloha sestavení je implementována opětovnou C# kompilací projektů, které již byly vytvořeny. Nový úkol používá jako původní úlohu pouze úlohy sestavení MSBuild a VSBuild ve stejném sestavení nebo definici sestavení. V tomto případě se ale nové úlohy používají s povolenými analyzátory Roslyn.
>
>   Pokud je nová úloha spuštěna na stejném agentě jako původní úloha, bude výstup nové úlohy přepsat výstup původní úlohy ve složce zdroje *s* . I když je výstup sestavení stejný, doporučujeme, abyste spustili nástroj MSBuild, zkopírovali výstup do pracovního adresáře artefakty a pak spustili analyzátory Roslyn.

Další zdroje pro úlohu analyzátorů Roslyn najdete v [části analyzátory založené na Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/) na Microsoft docs.

Balíček analyzátoru nainstalovaný a používaný touto úlohou sestavení můžete najít na stránce NuGet [Microsoft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

## <a name="tslint-task"></a>TSLint – úloha

Další informace o TSLint najdete v [úložišti GitHub TSLint](https://github.com/palantir/tslint).

>[!NOTE] 
>Jak si můžete být vědomi, Domovská stránka [úložiště GitHub TSLint](https://github.com/palantir/tslint) říká, že TSLint bude v průběhu 2019 zastaralá. Microsoft zkoumá [ESLint](https://github.com/eslint/eslint) jako alternativní úkol.

## <a name="publish-security-analysis-logs-task"></a>Úloha publikování protokolů analýzy zabezpečení

Podrobnosti o konfiguraci úlohy jsou uvedené na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení pro publikování protokolů analýzy zabezpečení](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Název artefaktu**: Libovolný identifikátor řetězce.
- **Typ artefaktu**: V závislosti na výběru můžete publikovat protokoly na server Azure DevOps nebo do sdíleného souboru, ke kterému má agent sestavení přístup.
- **Nástroje**: Můžete si vybrat, že zachováte protokoly pro konkrétní nástroje, nebo můžete vybrat **všechny nástroje** pro zachování všech protokolů.

## <a name="security-report-task"></a>Úloha sestavy zabezpečení

Podrobnosti o konfiguraci sestavy zabezpečení jsou uvedeny na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení sestavy zabezpečení](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Sestavy**: Vyberte libovolnou **konzolu kanálu**, **soubor TSV**a formáty **souborů HTML** . Pro každý vybraný formát se vytvoří jeden soubor sestavy.
- **Nástroje**: Vyberte nástroje v definici sestavení, pro které chcete souhrn zjištěných problémů. Pro každý vybraný nástroj může být k dispozici možnost vybrat, zda se zobrazí pouze chyby, nebo zobrazit chyby a upozornění v sestavě Shrnutí.
- **Rozšířené možnosti**: Pokud pro jeden z vybraných nástrojů neexistují žádné protokoly, můžete si vybrat, jestli se má zaznamenat upozornění nebo chyba. Pokud dojde k chybě, úloha se nezdařila.
- **Základní složka protokolů**: Můžete přizpůsobit složku základních protokolů, kde budou nalezeny protokoly. Tato možnost se obvykle nepoužívá.

## <a name="post-analysis-task"></a>Úkol po analýze

Podrobnosti o konfiguraci úlohy jsou uvedené na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení po analýze](./media/security-tools/a-post-analysis600.png)

- **Nástroje**: Vyberte nástroje v definici sestavení, pro které chcete podmíněně vložit přerušení sestavení. Pro každý vybraný nástroj může být k dispozici možnost vybrat, zda chcete přerušit pouze chyby nebo jak na chyby, tak i na upozornění.
- **Sestava**: Volitelně můžete napsat výsledky, které způsobují přerušení sestavení. Výsledky se zapisují do okna konzoly Azure DevOps a do souboru protokolu.
- **Rozšířené možnosti**: Pokud pro jeden z vybraných nástrojů neexistují žádné protokoly, můžete si vybrat, jestli se má zaznamenat upozornění nebo chyba. Pokud dojde k chybě, úloha se nezdařila.

## <a name="next-steps"></a>Další kroky

Pokud máte další dotazy týkající se rozšíření analýzy kódu zabezpečení a nabízených nástrojů, podívejte se na [stránku Nejčastější dotazy](security-code-analysis-faq.md).
