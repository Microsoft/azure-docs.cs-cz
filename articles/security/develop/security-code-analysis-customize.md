---
title: Přizpůsobení úloh analýzy kódu zabezpečení společnosti Microsoft
titleSuffix: Azure
description: Tento článek popisuje přizpůsobení úloh v rozšíření Microsoft Security Code Analysis.
author: sukhans
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
ms.openlocfilehash: e36485b54f209b10f9a4e6b4e6a26248955b2275
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031394"
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

Informace o konfiguraci YAML pro tuto úlohu najdete v našich [možnostech ochrany proti malwaru YAML](yaml-configuration.md#anti-malware-scanner-task) .

## <a name="binskim-task"></a>BinSkim – úloha

> [!NOTE]
> Předtím, než můžete spustit úlohu BinSkim, musí sestavení splňovat jednu z těchto podmínek:
>  - Sestavení vytváří binární artefakty ze spravovaného kódu.
>  - Máte potvrzené binární artefakty, které chcete analyzovat pomocí BinSkim.

Podrobnosti o konfiguraci úlohy jsou uvedené na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení BinSkim](./media/security-tools/7-binskim-task-details.png)

- Nastavte konfiguraci sestavení na ladit tak, aby byly vytvořeny soubory ladění. pdb. BinSkim tyto soubory používá k namapování problémů ve výstupních binárních souborech zpátky do zdrojového kódu.
- Aby nedošlo k opakovanému vyhledávání a vytváření vlastního příkazového řádku:
     - V seznamu **typ** vyberte **základní**.
     - V seznamu **funkce** vyberte **analyzovat**.
- Do vlastnosti **cíl**zadejte jeden nebo více specifikátorů pro soubor, adresář nebo vzor filtru. Tyto specifikátory jsou přeloženy na jeden nebo více binárních souborů, které mají být analyzovány:
    - Více zadaných cílů musí být oddělených středníkem (;).
    - Specifikátorem může být jeden soubor nebo obsahovat zástupné znaky.
    - Specifikace adresáře musí vždy končit znakem \\ *.
    - Příklady:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- Pokud v seznamu **typ** vyberete položku **příkazový řádek** , je nutné spustit binskim.exe:
     - Ujistěte se, že první argumenty binskim.exe jsou příkazy **analyze** a jedna nebo více specifikací cesty. Každá cesta může být buď úplná cesta, nebo cesta relativní ke zdrojovému adresáři.
     - Více cílových cest musí být odděleny mezerou.
     - Můžete vynechat možnost **/o** nebo **/Output** . Výstupní hodnota je přidána nebo nahrazena.
     - Standardní konfigurace příkazového řádku jsou uvedené níže.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> Koncový \\ znak * je důležitý, pokud zadáte adresáře pro cíl.

Další informace o argumentech příkazového řádku BinSkim, pravidlech podle ID nebo ukončovacích kódech najdete v [uživatelské příručce k BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Informace o konfiguraci YAML pro tuto úlohu najdete v našich [možnostech BINSKIM YAML](yaml-configuration.md#binskim-task) .

## <a name="credential-scanner-task"></a>Úloha pro kontrolu přihlašovacích údajů

Podrobnosti o konfiguraci úlohy jsou uvedené na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení pro kontrolu přihlašovacích údajů](./media/security-tools/3-taskdetails.png)

Mezi dostupné možnosti patří:
  - **Zobrazovaný název**: název úlohy Azure DevOps. Výchozí hodnota je spustit skener přihlašovacích údajů
  - **Hlavní verze nástroje**: dostupné hodnoty zahrnují **CredScan v2**, **CredScan v1**. Doporučujeme, aby zákazníci používali verzi **CredScan v2** .
  - **Výstupní formát**: dostupné hodnoty zahrnují **TSV**, **CSV**, **SARIF**a **Fast**.
  - **Verze nástroje**: Doporučujeme vybrat možnost **nejnovější**.
  - **Složka pro skenování**: složka úložiště, která se má zkontrolovat.
  - **Typ souboru vyhledávačů**: možnosti pro vyhledání souboru vyhledávacích souborů, který se používá ke skenování.
  - **Soubor potlačení**: soubor [JSON](https://json.org/) může potlačit problémy ve výstupním protokolu. Další informace o scénářích potlačení najdete v části Nejčastější dotazy v tomto článku.
  - **Podrobný výstup**: samozřejmý
  - **Velikost dávky**: počet souběžných vláken používaných ke spuštění skeneru přihlašovacích údajů. Výchozí hodnota je 20. Možné hodnoty jsou v rozsahu od 1 do 2 147 483 647.
  - **Časový limit shody**: doba v sekundách, po kterou se při pokusu o vynechání kontroly shoduje se zadaným vyhledávacím objektem.
  - **Velikost vyrovnávací paměti pro čtení prohledávání souborů**: velikost vyrovnávací paměti použité při čtení obsahu v bajtech. Výchozí hodnota je 524 288.  
  - **Maximální počet přečtených bajtů při kontrole souborů**: maximální počet bajtů, které se mají načíst ze souboru při analýze obsahu. Výchozí hodnota je 104 857 600.
  - **Možnosti**  >  ovládacích prvků **Spustit tuto úlohu**: Určuje, kdy se úloha spustí. Pokud chcete zadat složitější podmínky, vyberte **vlastní podmínky** .
  - **Version (verze**): verze úlohy buildu v rámci služby Azure DevOps. Tato možnost se často nepoužívá.

Informace o konfiguraci YAML pro tuto úlohu najdete v našich [přihlašovacích údajích k YAML možností skeneru přihlašovacích údajů](yaml-configuration.md#credential-scanner-task) .

## <a name="roslyn-analyzers-task"></a>Úloha analyzátorů Roslyn

> [!NOTE]
> Předtím, než budete moci spustit úlohu analyzátorů Roslyn, musí sestavení splňovat tyto podmínky:
>
> - Vaše definice sestavení zahrnuje integrovanou úlohu MSBuild nebo VSBuild Build pro zkompilování kódu C# nebo Visual Basic. Úloha analyzátory spoléhá na vstup a výstup vestavěné úlohy ke spuštění kompilace MSBuild s povolenými analyzátory Roslyn.
> - Agent sestavení spouštějící tuto úlohu sestavení má nainstalovanou aplikaci Visual Studio 2017 verze 15,5 nebo novější, aby používala kompilátor verze 2,6 nebo novější.

Podrobnosti o konfiguraci úlohy jsou uvedené v následujícím seznamu a Poznámka.

Mezi dostupné možnosti patří:

- **RuleSet**: hodnoty jsou **vyžadovány v SDL**, **doporučuje se SDL**nebo vaše vlastní sada pravidel.
- **Verze analyzátorů**: Doporučujeme vybrat možnost **nejnovější**.
- **Soubor potlačení upozornění kompilátoru**: textový soubor se seznamem ID upozornění, které se potlačí.
- **Možnosti**  >  ovládacích prvků **Spustit tuto úlohu**: Určuje, kdy se úloha spustí. Zvolte **vlastní podmínky** a určete složitější podmínky.

> [!NOTE]
>
> - Analyzátory Roslyn jsou integrovány s kompilátorem a lze je spustit pouze jako součást kompilace csc.exe. Proto tato úloha vyžaduje, aby byl příkaz kompilátoru, který byl dříve v sestavení spuštěn, znovu přehrán nebo znovu spuštěn. Toto přehrajte nebo spusťte dotazování v Visual Studio Team Services (VSTS) pro protokoly úloh sestavení MSBuild.
>
>   Neexistuje žádný jiný způsob, jak úlohu spolehlivě získat příkazový řádek kompilace MSBuild z definice sestavení. Doporučujeme přidat textové pole volného tvaru, které uživatelům umožní zadat jejich příkazové řádky. Ale pak by bylo obtížné tyto příkazové řádky udržovat v aktuálním stavu a synchronizovat s hlavním sestavením.
>
>   Vlastní sestavení vyžadují opakované přehrání celé sady příkazů, nikoli jenom příkazů kompilátoru. V těchto případech není povolení analyzátorů Roslyn triviální ani spolehlivější.
>
> - Analyzátory Roslyn jsou integrovány s kompilátorem. Aby se vyvolaly, analyzátory Roslyn vyžadují kompilaci.
>
>   Tato nová úloha sestavení je implementována opětovným kompilováním projektů jazyka C#, které již byly vytvořeny. Nový úkol používá jako původní úlohu pouze úlohy sestavení MSBuild a VSBuild ve stejném sestavení nebo definici sestavení. V tomto případě se ale nové úlohy používají s povolenými analyzátory Roslyn.
>
>   Pokud je nová úloha spuštěna na stejném agentě jako původní úloha, bude výstup nové úlohy přepsat výstup původní úlohy ve složce zdroje *s* . I když je výstup sestavení stejný, doporučujeme, abyste spustili nástroj MSBuild, zkopírovali výstup do pracovního adresáře artefakty a pak spustili analyzátory Roslyn.

Další zdroje pro úlohu analyzátorů Roslyn najdete v [části analyzátory založené na Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/api-analyzer) na Microsoft docs.

Balíček analyzátoru nainstalovaný a používaný touto úlohou sestavení můžete najít na stránce NuGet [Microsoft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Informace o konfiguraci YAML pro tuto úlohu najdete v našich [možnostech Roslyn Analyzer YAML](yaml-configuration.md#roslyn-analyzers-task) .

## <a name="tslint-task"></a>TSLint – úloha

Další informace o TSLint najdete v [úložišti GitHub TSLint](https://github.com/palantir/tslint).

>[!NOTE] 
>Jak si můžete být vědomi, Domovská stránka [úložiště GitHub TSLint](https://github.com/palantir/tslint) říká, že TSLint bude v průběhu 2019 zastaralá. Microsoft zkoumá [ESLint](https://github.com/eslint/eslint) jako alternativní úkol.

Informace o konfiguraci YAML pro tuto úlohu najdete v našich [možnostech TSLINT YAML](yaml-configuration.md#tslint-task) .

## <a name="publish-security-analysis-logs-task"></a>Úloha publikování protokolů analýzy zabezpečení

Podrobnosti o konfiguraci úlohy jsou uvedené na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení pro publikování protokolů analýzy zabezpečení](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Název artefaktu**: libovolný identifikátor řetězce.
- **Typ artefaktu**: v závislosti na vašem výběru můžete protokoly publikovat do Azure DevOps Server nebo do sdíleného souboru, který je přístupný agentovi sestavení.
- **Nástroje**: můžete si vybrat, že zachováte protokoly pro konkrétní nástroje, nebo můžete vybrat **všechny nástroje** pro zachování všech protokolů.

Informace o konfiguraci YAML pro tuto úlohu najdete v našich [možnostech publikování protokolů zabezpečení YAML](yaml-configuration.md#publish-security-analysis-logs-task) .

## <a name="security-report-task"></a>Úloha sestavy zabezpečení

Podrobnosti o konfiguraci sestavy zabezpečení jsou uvedeny na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení sestavy zabezpečení](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Sestavy**: vyberte libovolnou **konzolu kanálu**, **soubor TSV**a formáty **souborů HTML** . Pro každý vybraný formát se vytvoří jeden soubor sestavy.
- **Nástroje**: Vyberte nástroje v definici sestavení, pro které chcete souhrn zjištěných problémů. Pro každý vybraný nástroj může být k dispozici možnost vybrat, zda se zobrazí pouze chyby, nebo zobrazit chyby a upozornění v sestavě Shrnutí.
- **Rozšířené možnosti**: Pokud pro jeden z vybraných nástrojů neexistují žádné protokoly, můžete si vybrat, jestli se má zaznamenat upozornění nebo chyba. Pokud dojde k chybě, úloha se nezdařila.
- **Základní složka protokolů**: můžete přizpůsobit složku základních protokolů, kde budou nalezeny protokoly. Tato možnost se obvykle nepoužívá.

Informace o konfiguraci YAML pro tuto úlohu najdete v naší [YAML možnosti sestavy zabezpečení](yaml-configuration.md#security-report-task) .

## <a name="post-analysis-task"></a>Úkol po analýze

Podrobnosti o konfiguraci úlohy jsou uvedené na následujícím snímku obrazovky a seznamu.

![Konfigurace úlohy sestavení po analýze](./media/security-tools/a-post-analysis600.png)

- **Nástroje**: Vyberte nástroje v definici sestavení, pro které chcete podmíněně vložit přerušení sestavení. Pro každý vybraný nástroj může být k dispozici možnost vybrat, zda chcete přerušit pouze chyby nebo jak na chyby, tak i na upozornění.
- **Sestava**: Volitelně můžete napsat výsledky, které způsobují přerušení sestavení. Výsledky se zapisují do okna konzoly Azure DevOps a do souboru protokolu.
- **Rozšířené možnosti**: Pokud pro jeden z vybraných nástrojů neexistují žádné protokoly, můžete si vybrat, jestli se má zaznamenat upozornění nebo chyba. Pokud dojde k chybě, úloha se nezdařila.

Informace o konfiguraci YAML pro tuto úlohu najdete v našich [možnostech YAML po analýze](yaml-configuration.md#post-analysis-task) .

## <a name="next-steps"></a>Další kroky

Informace o konfiguraci založené na YAML najdete v našem [Průvodci konfigurací YAML](yaml-configuration.md).

Pokud máte další dotazy týkající se rozšíření analýzy kódu zabezpečení a nabízených nástrojů, podívejte se na [stránku Nejčastější dotazy](security-code-analysis-faq.md).