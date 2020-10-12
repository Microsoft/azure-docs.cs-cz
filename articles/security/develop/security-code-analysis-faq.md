---
title: Dokumentace k analýze kódu zabezpečení společnosti Microsoft – nejčastější dotazy
description: Přečtěte si nejčastější dotazy a zjistěte informace o rozšíření Microsoft Security Code Analysis Extension.
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
ms.openlocfilehash: f872159e538dc85121a7a6d4d6503fd18a263628
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543040"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy
Máte nějaké otázky? Další informace najdete v následujících nejčastějších dotazech.

## <a name="general-faq"></a>Obecné nejčastější dotazy

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Můžu nainstalovat rozšíření na mou instanci sady Visual Studio Team Foundation Server místo v instanci Azure DevOps?

Ne. Rozšíření není k dispozici pro stažení a instalaci sady Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Musím v mém buildu spustit analýzu kódu zabezpečení od Microsoftu? 

Možná. Závisí na typu analytického nástroje. Zdrojový kód může být jen to, co je potřeba, nebo se může vyžadovat výstup sestavení.

Například kontrola přihlašovacích údajů (CredScan) analyzuje soubory v rámci struktury složek v úložišti kódu. Z důvodu této analýzy můžete k získání výsledků spustit úlohy CredScan a publikovat protokoly analýzy zabezpečení v samostatném sestavení.

Pro jiné nástroje, jako je BinSkim, které analyzují artefakty po sestavení, je sestavení vyžadováno jako první.

### <a name="can-i-break-my-build-when-results-are-found"></a>Je možné ukončit sestavení při nalezení výsledků?

Ano. Můžete zavést přerušení sestavení, když kterýkoli nástroj ohlásí problém nebo problém v souboru protokolu. Stačí přidat úlohu sestavení po analýze a zaškrtnout políčko pro libovolný nástroj, pro který chcete sestavení přerušit.

V uživatelském rozhraní úlohy po analýze můžete zvolit, aby se sestavení přerušit, když jakýkoli nástroj hlásí pouze chyby nebo chyby a upozornění.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Jak se argumenty příkazového řádku v Azure DevOps liší od argumentů v nástrojích samostatné plochy? 

Ve většině případů jsou úkoly sestavení Azure DevOps přímými obálkami kolem argumentů příkazového řádku nástrojů zabezpečení. Můžete předat jako argumenty do úlohy sestavení cokoli, co normálně předáte do nástroje příkazového řádku.

Patrné rozdíly:

- Nástroje jsou spouštěny ze zdrojové složky agenta $ (Build. SourcesDirectory) nebo z% BUILD_SOURCESDIRECTORY%. Příkladem je C:\Agent \_ work\1\s.
- Cesty v argumentech mohou být relativní ke kořenu výše uvedeného zdrojového adresáře. Cesty mohou být také absolutní. Absolutní cesty můžete získat buď pomocí proměnných sestavení Azure DevOps, nebo spuštěním místního agenta se známými umístěními nasazení místních prostředků.
- Nástroje automaticky poskytují cestu k výstupnímu souboru nebo složku. Pokud zadáte umístění výstupu pro úlohu sestavení, bude toto umístění nahrazeno cestou k našemu dobře známému umístění protokolů agenta sestavení.
- Některé další argumenty příkazového řádku se u některých nástrojů změnily. Jedním z příkladů je přidání nebo odebrání možností, které zajistí, že se nespustí žádné grafické rozhraní (GUI).

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Můžu spustit úlohu sestavení, jako je třeba skener přihlašovacích údajů v rámci několika úložišť v sestavení Azure DevOps?

Ne. Používání nástrojů pro bezpečné vývoj v různých úložištích v jednom kanálu se nepodporuje.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Zadaný výstupní soubor se nevytváří nebo nejde najít zadaný výstupní soubor.

Úkoly sestavení filtrují určitý uživatelský vstup. Pro tuto otázku konkrétně aktualizuje umístění generovaného výstupního souboru na společné umístění agenta sestavení. Další informace o tomto umístění najdete na následujících dotazech.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Kde jsou výstupní soubory generované nástroji uložené? 

Úlohy sestavení automaticky přidávají výstupní cesty k tomuto známému umístění v agentovi sestavení: $ (agent. BuildDirectory) \_ sdt\logs. Vzhledem k tomu, že jsme v tomto umístění standardizaci, všechny týmy, které vytváří nebo využívají protokoly analýzy kódu, mají přístup k výstupu.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Můžu sestavení zařadit do fronty a spustit tyto úlohy v hostovaném agentu sestavení? 

Ano. Všechny úlohy a nástroje v rozšíření lze spustit u hostovaného agenta sestavení.

>[!NOTE]
> Úloha sestavení pro kontrolu proti malwaru vyžaduje, aby byl v programu Windows Defender povolen agent sestavení. Hostitelem sady Visual Studio 2017 a novějších je například agent. V hostovaném agentu sady Visual Studio 2015 se úloha sestavení nespustí.
>
> I když signatury nelze na těchto agentech aktualizovat, signatury by měly být vždy méně než tři hodiny staré.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Můžu spustit tyto úlohy sestavení jako součást kanálu pro vydávání verzí, a to na rozdíl od kanálu sestavení?

Ve většině případů Ano.

Azure DevOps ale nepodporuje spouštění úloh v rámci kanálů vydávání verzí, pokud tyto úlohy publikují artefakty. Tato nedostatečná podpora zabrání úspěšnému spuštění úlohy publikování protokolů analýzy zabezpečení v kanálu vydání. Úloha se místo toho nezdařila s popisnou chybovou zprávou.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Odkud úlohy sestavení stáhnou nástroje?

Úlohy sestavení můžou stáhnout balíčky NuGet nástrojů z [DevOps kanálu služby Azure Správa balíčků](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). Úlohy sestavení mohou také používat Správce balíčků uzlů, které musí být předem nainstalovány v agentovi sestavení. Příkladem takové instalace je příkaz **npm Install tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Jaký vliv má rozšíření instalovat na moji organizaci Azure DevOps? 

Po jejich instalaci budou úkoly sestavení zabezpečení poskytované rozšířením k dispozici všem uživatelům ve vaší organizaci. Když vytváříte nebo upravujete kanál Azure, tyto úlohy jsou k dispozici v seznamu kolekce sestavení – úkoly. V opačném případě se instalace rozšíření v organizaci Azure DevOps nijak neprojeví. Instalace neupravuje žádná nastavení účtu, nastavení projektu ani kanály.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Mění se v instalaci rozšíření moje existující Azure Pipelines? 

Ne. Instalace rozšíření zpřístupňuje úlohy sestavení zabezpečení pro přidání do vašich kanálů. Stále je nutné přidat nebo aktualizovat definice sestavení, aby nástroje mohly pracovat s procesem sestavení.

## <a name="task-specific-faq"></a>Nejčastější dotazy ke konkrétním úlohám

Dotazy, které jsou specifické pro úlohy sestavení, jsou uvedené v této části.

### <a name="credential-scanner"></a>Skener přihlašovacích údajů

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Co jsou běžné scénáře potlačení a příklady?

Tady jsou podrobnosti dvou nejběžnějších scénářů potlačení.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Chcete-li potlačit všechny výskyty daného tajného kódu v zadané cestě

Klíč hash tajného kódu z výstupního souboru CredScan je vyžadován, jak je znázorněno v následující ukázce.

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
        "_justification": "Secret used by MSDN sample, it is fake."
    }
  ]
}
```

>[!WARNING]
> Klíč hash je vygenerován částí odpovídající hodnoty nebo obsahu souboru. Jakákoli revize zdrojového kódu může změnit klíč hash a zakázat pravidlo potlačení.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Potlačení všech tajných kódů v zadaném souboru nebo potlačení samotného souboru tajných klíčů

Výrazem souboru může být název souboru. Může to být i část úplné cesty k souboru nebo názvu souboru. Zástupné znaky se nepodporují.

Následující příklady ukazují, jak potlačit soubor \<InputPath>\src\JS\lib\angular.js

Příklady platných pravidel potlačení:

- \<InputPath>\src\JS\lib\angular.js – potlačí soubor v zadané cestě.
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js – potlačí všechny soubory se stejným názvem.

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "file": "\\files\\AdditonalSearcher.xml", 
        "_justification": "Additional CredScan searcher specific to my team"
    },
    {
        "file": "\\files\\unittest.pfx", 
        "_justification": "Legitimate UT certificate file with private key"
    }
  ]
}
```

>[!WARNING] 
> Všechna budoucí tajná klíče přidaná do souboru se také automaticky potlačí.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Jaké jsou doporučené pokyny pro správu tajných kódů?

Následující zdroje vám pomůžou bezpečně spravovat tajné klíče a přistupovat k citlivým informacím v rámci svých aplikací:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../azure-sql/database/authentication-aad-overview.md)
 - [Služba Azure AD Identita spravované služby (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Spravované identity v Azure App Service a Azure Functions](../../app-service/overview-managed-identity.md)
 - [Knihovna AppAuthentication](../../key-vault/general/service-to-service-authentication.md)


Další informace najdete v blogovém příspěvku, který je [bezpečný pro správu tajných kódů v cloudu](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Můžu psát vlastní vyhledávací vlastníky?

Kontrola přihlašovacích údajů spoléhá na sadu vyhledávačů obsahu, které jsou běžně definované v souboru buildsearchers.xml. Soubor obsahuje pole serializovaných objektů XML, které reprezentují objekt **ContentSearcher** . Program se distribuuje se sadou dobře testovaných vyhledávacích programů. Můžete ale taky implementovat vlastní vyhledávací služby.

Hledání obsahu je definováno následujícím způsobem:

- **Název**: popisný název vyhledávacího programu, který se má použít ve výstupních souborech pro kontrolu přihlašovacích údajů. Doporučujeme používat ve stylu CamelCase konvence pojmenovávání pro názvy vyhledávacích jmen.
- **RuleId**: stabilní neprůhledné ID vyhledávacího pole:
    - Výchozímu vyhledávacímu skeneru přihlašovacích údajů se přiřadí hodnota **RuleId** , jako je CSCAN0010, CSCAN0020 nebo CSCAN0030. Poslední číslice je vyhrazena pro potenciálně slučování nebo rozdělování skupin hledání prostřednictvím regulárních výrazů (Regex).
    - Hodnota **RuleId** přizpůsobeného vyhledávacího modulu by měla mít vlastní obor názvů. Příklady zahrnují CSCAN- \<Namespace\> 0010, CSCAN- \<Namespace\> 0020 a CSCAN- \<Namespace\> 0030.
    - Plně kvalifikovaný název hledání je kombinací hodnoty **RuleId** a názvu vyhledávacího pole. Příklady zahrnují CSCAN0010. KeyStoreFiles a CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: Regex pro přípony souborů, které se mají kontrolovat u hledání.
- **ContentSearchPatterns**: pole řetězců obsahující příkazy Regex, které mají být shodné. Pokud nejsou definovány žádné vzory hledání, vrátí se všechny soubory, které odpovídají hodnotě **ResourceMatchPattern** .
- **ContentSearchFilters**: pole řetězců obsahující příkazy Regex pro filtrování falešně specifických hodnot specifických pro hledání.
- **MatchDetails**: Popisná zpráva, pokyny pro zmírnění nebo obojí, které se mají přidat pro každou shodu hledání.
- **Doporučení**: návrhy – obsah pole pro porovnávání pomocí formátu předrychlých sestav.
- **Závažnost**: celé číslo, které odráží úroveň závažnosti problému. Nejvyšší úroveň závažnosti má hodnotu 1.

  ![XML znázorňující instalaci skeneru přihlašovacích údajů](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analyzátory Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Co jsou běžné chyby při použití úlohy analyzátorů Roslyn?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Projekt se obnovil s použitím nesprávné verze Microsoft. NETCore. app.

Úplná chybová zpráva:

"Chyba: projekt se obnovil pomocí Microsoft. NETCore. app verze *x. x. x*, ale s aktuálním nastavením se místo toho použije verze *y. y. y* . Chcete-li tento problém vyřešit, zajistěte, aby se pro obnovení používalo stejné nastavení a pro následné operace, jako je například sestavení nebo publikování. K tomuto problému obvykle dochází, pokud je vlastnost RuntimeIdentifier nastavena během sestavování nebo publikování, ale nikoli během obnovení. "

Vzhledem k tomu, že se úlohy analyzátorů Roslyn spouštějí jako součást kompilace, musí být zdrojový strom na sestavovacím počítači ve stavu pro sestavení.

Krok do hlavního postupu sestavení a analyzátoru Roslyn může vést k tomu, že se zdrojový strom vloží do stavu, který brání jeho sestavení. Tento další krok je pravděpodobně **dotnet.exe publikování**. Zkuste duplikovat krok, který provede obnovení NuGet těsně před krokem analyzátory Roslyn. Tento duplicitní krok může umístit zdrojový strom zpátky do sestaveného stavu.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe nemůže vytvořit instanci analyzátoru.

Úplná chybová zpráva:

"' csc.exe ' byl ukončen s kódem chyby 1--instanci služby Analyzer *AAAA* nelze vytvořit z jazyka C: \\ *bbbb*. dll: nelze načíst soubor nebo sestavení ' Microsoft. CodeAnalysis, Version = X.*x. x. x*, Culture = neutral, PublicKeyToken = 31bf3856ad364e35 ' nebo jednu z jeho závislostí. Systém nemůže najít zadaný soubor.

Ujistěte se, že kompilátor podporuje analyzátory Roslyn. Spuštění příkazu **csc.exe/Version** by mělo hlásit hodnotu verze 2,6 nebo novější.

V některých případech může soubor. csproj přepsat instalaci sady Visual Studio sestavení počítače tím, že odkazuje na balíček z Microsoft.Net. compilers. Pokud nechcete použít konkrétní verzi kompilátoru, odeberte odkazy na Microsoft.Net. compilers. V opačném případě se ujistěte, že verze odkazovaného balíčku je také 2,6 nebo novější.

Pokuste se získat cestu k protokolu chyb, která je zadána v možnosti **csc.exe/Errorlog** . Možnost a cesta se zobrazí v protokolu pro úlohu sestavení analyzátorů Roslyn. Můžou vypadat nějak takto **:/errorlog: f:\ts-services-123 \_ work\456\s\Some\Project\Code\Code.csproj.Sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>Verze kompilátoru C# není dost nedávné.

Chcete-li získat nejnovější verze kompilátoru jazyka C#, přejít na [Microsoft.NET. compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Pokud chcete získat nainstalovanou verzi, spusťte **csc.exe/Version** na příkazovém řádku. Ujistěte se, že odkazujete na balíček NuGet Microsoft.Net. compilers verze 2,6 nebo novější.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Protokoly MSBuild a VSBuild se nenašly.

Úloha sestavení analyzátorů Roslyn musí zadat dotaz na Azure DevOps pro protokol MSBuild z úlohy sestavení MSBuild. Pokud se úloha analyzátoru spustí hned po úloze MSBuild, protokol ještě nebude k dispozici. Umístěte další úkoly mezi úlohu MSBuild a úlohu analyzátorů Roslyn. Mezi příklady dalších úloh patří BinSkim a skener proti malwaru.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další pomoc, podpora analýzy kódu zabezpečení společnosti Microsoft je k dispozici od pondělí do pátku od 9:00 do 5:00 odp. tichomořského (běžný čas).

- Připojování: Přečtěte si naši [dokumentaci k registraci](security-code-analysis-onboard.md) .
  
- Podpora: odeslání týmu e-mailem na [podporu analýzy kódu zabezpečení společnosti Microsoft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)