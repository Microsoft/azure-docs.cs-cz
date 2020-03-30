---
title: Přizpůsobení úloh analýzy kódu zabezpečení společnosti Microsoft
titleSuffix: Azure
description: Tento článek popisuje přizpůsobení úkolů v rozšíření Microsoft Security Code Analysis
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
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499987"
---
# <a name="configure-and-customize-the-build-tasks"></a>Konfigurace a přizpůsobení úloh sestavení

Tento článek podrobně popisuje možnosti konfigurace, které jsou k dispozici v každé z úloh sestavení. Článek začíná úkoly pro nástroje pro analýzu kódu zabezpečení. Končí úkoly po zpracování.

## <a name="anti-malware-scanner-task"></a>Úloha antimalwarového skeneru

>[!NOTE]
> Úloha sestavení antimalwarového skeneru vyžaduje agenta sestavení s povoleným programem Windows Defender. Hostované Visual Studio 2017 a později poskytnout takového agenta. Úloha sestavení se nespustí v hostovaném agentovi Visual Studia 2015.
>
> Přestože podpisy nelze aktualizovat na tyto agenty, podpisy by měly být vždy kratší než tři hodiny.

Podrobnosti o konfiguraci úloh y jsou uvedeny na následujícím snímku obrazovky a textu.

![Konfigurace úlohy sestavení antimalwarového skeneru](./media/security-tools/5-add-anti-malware-task600.png)

V seznamu **Typ** snímku obrazovky je vybrána možnost **Základní.** Výběrem **možnosti Vlastní** zadejte argumenty příkazového řádku, které přizpůsobují prohledávku.

Program Windows Defender používá ke stažení a instalaci podpisů klienta služby Windows Update. Pokud se u agenta sestavení nezdaří aktualizace podpisu, kód chyby **HRESULT** pravděpodobně pochází ze služby Windows Update.

Další informace o chybách služby Windows Update a jejich zmírnění naleznete [v tématu Kódy chyb služby Windows Update podle komponent](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) a v článku webu Windows Update Agent – [Kódy chyb](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Informace o konfiguraci YAML pro tuto úlohu naleznete v našich [možnostech Anti-Malware YAML](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Úloha BinSkim

> [!NOTE]
> Před spuštěním úlohy BinSkim musí sestavení splňovat jednu z těchto podmínek:
>  - Sestavení vytváří binární artefakty ze spravovaného kódu.
>  - Máte binární artefakty potvrzena, které chcete analyzovat s BinSkim.

Podrobnosti o konfiguraci úloh y jsou uvedeny na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení BinSkim](./media/security-tools/7-binskim-task-details.png)

- Nastavte konfiguraci sestavení na ladění tak, aby byly vytvořeny ladicí soubory .pdb. BinSkim používá tyto soubory k mapování problémů ve výstupních binárních souborech zpět do zdrojového kódu.
- Chcete-li se vyhnout výzkumu a vytváření vlastního příkazového řádku:
     - V seznamu **Typ** vyberte **Základní**.
     - V seznamu **Funkce** vyberte **Analyzovat**.
- Do **pole Cíl**zadejte jeden nebo více specifikátorů pro soubor, adresář nebo vzorek filtru. Tyto specifikátory se přeřazují do jedné nebo více binárních souborů, které mají být analyzovány:
    - Více zadaných cílů musí být odděleno středníkem (;).
    - Specifikátor může být jeden soubor nebo obsahovat zástupné znaky.
    - Specifikace adresáře musí \\vždy končit *.
    - Příklady:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Pokud v seznamu **Typ** vyberete **příkazový řádek,** budete muset spustit soubor binskim.exe:
     - Ujistěte se, že první argumenty binskim.exe jsou **analýzou** slovesa následovanou jednou nebo více specifikacemi cesty. Každá cesta může být úplná cesta nebo cesta vzhledem ke zdrojovému adresáři.
     - Více cílových cest musí být odděleno mezerou.
     - Můžete vynechat **/o** nebo **/output** možnost. Výstupní hodnota je přidána pro vás nebo nahrazena.
     - Standardní konfigurace příkazového řádku jsou zobrazeny následujícím způsobem.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > Koncové \\* je důležité, pokud zadáte adresáře pro cíl.

Další informace o argumentech příkazového řádku BinSkim, pravidlech podle ID nebo ukončovacích kódech naleznete v [uživatelské příručce binSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Informace o konfiguraci YAML pro tuto úlohu naleznete v našich [možnostech BinSkim YAML](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Úloha skeneru pověření

Podrobnosti o konfiguraci úloh y jsou uvedeny na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení skeneru přihlašovacích údajů](./media/security-tools/3-taskdetails.png)

Mezi dostupné možnosti patří:

  - **Výstupní formát**: Dostupné hodnoty zahrnují **TSV**, **CSV**, **SARIF**a **PREfast**.
  - **Verze nástroje**: Doporučujeme vybrat **možnost Nejnovější**.
  - **Složky skenování**: Složka úložiště, která má být prohledána.
  - **Typ souboru hledačů**: Možnosti pro vyhledání souboru hledačů, který se používá pro skenování.
  - **Soubor potlačení**: Soubor [JSON](https://json.org/) může potlačit problémy ve výstupním protokolu. Další informace o scénářích potlačení naleznete v části Nejčastější dotazy v tomto článku.
  - **Podrobný výstup**: Samovysvětlující.
  - **Velikost dávky**: Počet souběžných podprocesů používaných ke spuštění skeneru pověření. Výchozí hodnota je 20. Možné hodnoty se pohybují od 1 do 2 147 483 647.
  - **Časový čas zápasu**: Doba v sekundách, která má strávit pokus emitování shody před opuštěním šeku.
  - **Velikost vyrovnávací paměti pro čtení souboru**: Velikost v bajtech vyrovnávací paměti použité při čtení obsahu. Výchozí hodnota je 524 288.  
  - **Maximální počet bajtů čtení prohledávaní souborů**: Maximální počet bajtů, které lze číst ze souboru během analýzy obsahu. Výchozí hodnota je 104 857 600.
  - **Možnosti ovládacího prvku** > **Spuštění této úlohy**: Určuje, kdy bude úloha spuštěna. Chcete-li určit složitější podmínky, vyberte **možnost Vlastní podmínky.**
  - **Verze**: Verze úlohy sestavení v rámci Azure DevOps. Tato možnost se často nepoužívá.

Informace o konfiguraci YAML pro tuto úlohu naleznete v našich [možnostech skeneru přihlašovacích údajů YAML](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>Úloha zjišťování bezpečnostních rizik společnosti Microsoft

> [!NOTE]
> Před použitím úlohy MSRD je nutné vytvořit a nakonfigurovat účet pomocí služby MSRD (Microsoft Security Risk Detection). Tato služba vyžaduje samostatný proces registrace. Na rozdíl od většiny ostatních úkolů v tomto rozšíření tento úkol vyžaduje samostatné předplatné s MSRD.
>
> Pokyny naleznete v [dokumentech zjišťování bezpečnostních rizik](https://aka.ms/msrddocs) společnosti Microsoft a zjišťování bezpečnostních rizik společnosti [Microsoft.](https://docs.microsoft.com/security-risk-detection/how-to/)

Podrobnosti pro konfiguraci tohoto úkolu jsou uvedeny v následujícím seznamu. Pro libovolný prvek ui můžete najet na tento prvek a získat nápovědu.

   - **Název koncového bodu služby Azure DevOps pro službu MSRD**: Obecný typ koncového bodu služby Azure DevOps ukládá vaši adresu URL instance MSRD a přístupový token rozhraní REST API. Pokud jste takový koncový bod vytvořili, můžete jej zadat zde. V opačném případě vyberte odkaz **Spravovat** a vytvořte a nakonfigurujte nový koncový bod služby pro tuto úlohu služby MSRD.
   - **ID účtu**: Identifikátor GUID, který lze načíst z adresy URL účtu MSRD.
   - **Adresy URL binárních souborů**: Seznam veřejně dostupných adres URL oddělených středníkem. Fuzzing stroj používá tyto adresy URL ke stažení binární soubory.
   - Adresy URL souborů **sezavislých souborů**: Seznam veřejně dostupných adres URL oddělených středníkem. Fuzzing stroj používá tyto adresy URL ke stažení semen. Zadání této hodnoty je volitelné, pokud jsou soubory sezamy staženy společně s binárními soubory.
   - **Typ platformy operačního**systému: Platforma operačního systému (OS) počítačů, které spouštějí úlohu fuzzing. Dostupné hodnoty jsou **Windows** a **Linux**.
   - **Windows Edition / Linux Edition:** OS vydání počítačů, které běží fuzzing úlohy. Výchozí hodnotu můžete přepsat, pokud vaše počítače mají jinou edici operačního systému.
   - **Instalační skript balíčku**: Skript, který má být spuštěn v testovacím počítači. Tento skript nainstaluje testovací cílový program a jeho závislosti před odesláním úlohy fuzzing.
   - **Parametry odeslání projektu**:
       - **Seed Directory**: Cesta k adresáři na fuzzing stroj, který obsahuje semena.
       - **Rozšíření osiva**: Přípona názvu souboru semen.
       - **Spustitelný soubor testovacího ovladače**: Cesta k cílovému spustitelnému souboru v zařízení fuzzing.
       - **Spustitelná architektura testovacího ovladače**: Architektura cílového spustitelného souboru. Dostupné hodnoty jsou **x86** a **amd64**.
       - **Argumenty testovacího ovladače**: Argumenty příkazového řádku byly předány spustitelnému souboru testu. Argument %testfile%, včetně uvozovek, je automaticky nahrazen úplnou cestou k cílovému souboru. Tento soubor je analyzován testovacím ovladačem a je vyžadován.
       - **Ukončení procesu testovacího procesu po dokončení testu**: Toto políčko zaškrtněte, pokud má být testovací ovladač po dokončení ukončen. Pokud je třeba zkušební ovladač násilně uzavřít, zrušte jej.
       - **Maximální doba trvání (v sekundách):** Odhad nejdelšího přiměřeně očekávaného času, který cílový program potřebuje k analýzě vstupního souboru. Čím přesnější je odhad, tím efektivněji se spustí fuzzing ováaplikace.
       - **Testovací ovladač lze spustit opakovaně**: Toto políčko zaškrtněte, pokud může testovací ovladač běžet opakovaně bez závislosti na trvalém nebo sdíleném globálním stavu.
       - **Testovací ovladač lze přejmenovat**: Toto políčko zaškrtněte, pokud lze spustitelný soubor testovacího ovladače přejmenovat a stále pracovat správně.
       - **Fuzzing aplikace běží jako jeden proces operačního systému**: Toto políčko zaškrtněte, pokud testovací ovladač běží v rámci jednoho procesu operačního systému. Zrušte zaškrtnutí, pokud testovací ovladač spouští další procesy.

Informace o konfiguraci YAML pro tuto úlohu naleznete v našich [možnostech yaml pro zjišťování bezpečnostních rizik](yaml-configuration.md#microsoft-security-risk-detection-task) společnosti Microsoft.

## <a name="roslyn-analyzers-task"></a>Úloha analyzátorů Roslyn

> [!NOTE]
> Před spuštěním úlohy Roslyn Analyzátory, vaše sestavení musí splňovat tyto podmínky:
>
> - Definice sestavení zahrnuje předdefinovaný úkol sestavení MSBuild nebo VSBuild pro kompilaci kódu jazyka C# nebo Visual Basic. Úloha analyzátorů závisí na vstupu a výstupu předdefinované úlohy ke spuštění kompilace MSBuild s povolenými analyzátory Roslyn.
> - Agent sestavení s pouštějící tuto úlohu sestavení má nainstalovanou visual studio 2017 verze 15.5 nebo novější, takže používá kompilátor verze 2.6 nebo novější.

Podrobnosti o konfiguraci úloh jsou uvedeny v následujícím seznamu a poznámce.

Mezi dostupné možnosti patří:

- **Sada pravidel**: Hodnoty jsou **požadovány SDL**, **sdl doporučeno**nebo vlastní sadu pravidel.
- **Verze analyzátorů**: Doporučujeme vybrat **možnost Nejnovější**.
- **Soubor potlačení upozornění kompilátoru**: Textový soubor se seznamem ID upozornění, která jsou potlačena.
- **Možnosti ovládacího prvku** > **Spuštění této úlohy**: Určuje, kdy bude úloha spuštěna. Zvolte **Vlastní podmínky,** chcete-li určit složitější podmínky.

> [!NOTE]
>
> - Analyzátory Roslyn jsou integrovány s kompilátorem a lze je spustit pouze jako součást kompilace csc.exe. Proto tato úloha vyžaduje příkaz kompilátoru, který byl spuštěn dříve v sestavení, aby byl přehrán nebo znovu spuštěn. Toto přehrání nebo spuštění se provádí dotazem Visual Studio Team Services (VSTS) pro protokoly úloh sestavení MSBuild.
>
>   Neexistuje žádný jiný způsob, jak úloha spolehlivě získat příkazový řádek kompilace MSBuild z definice sestavení. Zvažovali jsme přidání volného textového pole, které uživatelům umožní zadávat příkazové řádky. Ale pak by bylo těžké udržet tyto příkazové řádky aktuální a synchronizované s hlavním sestavením.
>
>   Vlastní sestavení vyžadují přehrání celé sady příkazů, nikoli pouze příkazů kompilátoru. V těchto případech povolení Roslyn Analyzátory není triviální nebo spolehlivé.
>
> - Analyzátory Roslyn jsou integrovány s kompilátorem. Chcete-li být vyvolána, Roslyn Analyzátory vyžadují kompilaci.
>
>   Tento nový úkol sestavení je implementována rekompilace c# projekty, které již byly vytvořeny. Nový úkol používá pouze MSBuild a VSBuild sestavení úlohy ve stejném sestavení nebo sestavení definice jako původní úkol. V tomto případě je však nový úkol používá s povolenými analyzátory Roslyn.
>
>   Pokud je nová úloha spuštěna na stejném agentovi jako původní úloha, výstup nové úlohy přepíše výstup původní úlohy ve složce *zdrojů.* Přestože výstup sestavení je stejný, doporučujeme spustit MSBuild, zkopírujte výstup do adresáře artefakty pracovní a pak spustit Roslyn Analyzátory.

Další zdroje informací pro úlohu analyzátory Roslyn naleznete [v analyzátorech založených na programu Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/) v dokumentech společnosti Microsoft.

Balíček analyzátoru nainstalovaný a používaný touto úlohou sestavení najdete na stránce NuGet [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Informace o konfiguraci YAML pro tuto úlohu naleznete v našich [možnostech Analyzátory YAML Roslyn](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>Úkol TSLint

Další informace o TSLint najdete v [úložišti TSLint GitHub](https://github.com/palantir/tslint).

>[!NOTE] 
>Jak si možná uvědomujete, domovská stránka [úložiště TSLint GitHub](https://github.com/palantir/tslint) říká, že TSLint bude někdy v roce 2019 zastaralá. Společnost Microsoft vyšetřuje [ESLint](https://github.com/eslint/eslint) jako alternativní úkol.

Informace o konfiguraci YAML pro tuto úlohu naleznete v našich [možnostech TSLint YAML](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Úloha Publikovat protokoly analýzy zabezpečení

Podrobnosti o konfiguraci úloh y jsou uvedeny na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení protokolů analýzy zabezpečení publikování](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Název artefaktu**: Libovolný identifikátor řetězce.
- **Typ artefaktu**: V závislosti na výběru můžete publikovat protokoly na serveru Azure DevOps nebo do sdíleného souboru, který je přístupný agentovi sestavení.
- **Nástroje**: Můžete se rozhodnout zachovat protokoly pro určité nástroje nebo můžete vybrat **všechny nástroje,** chcete-li zachovat všechny protokoly.

Informace o konfiguraci YAML pro tuto úlohu naleznete v našich [možnostech publikování protokolů zabezpečení YAML](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Úloha zprávy zabezpečení

Podrobnosti o konfiguraci sestavy zabezpečení jsou uvedeny na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení sestavy zabezpečení](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Sestavy**: Vyberte některý z formátů **potrubní konzoly**, **Soubor TSV**a **Soubor HTML.** Pro každý vybraný formát je vytvořen jeden soubor sestavy.
- **Nástroje**: Vyberte nástroje v definici sestavení, pro které chcete souhrn zjištěných problémů. U každého vybraného nástroje může být možnost vybrat, zda se zobrazí pouze chyby nebo upozornění v souhrnné sestavě.
- **Upřesnit možnosti**: Pokud pro jeden z vybraných nástrojů nejsou žádné protokoly, můžete se rozhodnout přihlásit upozornění nebo chybu. Pokud zaznamenáte chybu, úloha se nezdaří.
- **Složka základních protokolů**: Můžete přizpůsobit složku základní protokoly, kde se protokoly mají najít. Ale tato možnost se obvykle nepoužívá.

Informace o konfiguraci YAML pro tuto úlohu naleznete v naší [zprávě o zabezpečení YAML možnosti](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Úkol po analýze

Podrobnosti o konfiguraci úloh y jsou uvedeny na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení po analýze](./media/security-tools/a-post-analysis600.png)

- **Nástroje**: Vyberte nástroje v definici sestavení, pro které chcete podmíněně vložit konec sestavení. Pro každý vybraný nástroj může být možnost vybrat, zda chcete přerušit pouze chyby nebo na chyby a upozornění.
- **Sestava**: Volitelně můžete zapsat výsledky, které jsou příčinou přerušení sestavení. Výsledky se zapisují do okna konzoly Azure DevOps a do souboru protokolu.
- **Upřesnit možnosti**: Pokud pro jeden z vybraných nástrojů nejsou žádné protokoly, můžete se rozhodnout přihlásit upozornění nebo chybu. Pokud zaznamenáte chybu, úloha se nezdaří.

Informace o konfiguraci YAML pro tuto úlohu naleznete v našich [možnostech Analýzy příspěvku YAML](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Další kroky

Informace o konfiguraci založené na yaml naleznete v našem [průvodci konfigurací YAML](yaml-configuration.md).

Máte-li další dotazy týkající se rozšíření Analýza bezpečnostního kódu a nabízených nástrojů, podívejte se na [naši stránku nejčastějších dotazů](security-code-analysis-faq.md).
