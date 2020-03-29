---
title: Nejčastější dotazy k dokumentaci k analýze bezpečnostního kódu společnosti Microsoft
description: Tento článek obsahuje nejčastější dotazy týkající se rozšíření Microsoft Security Code Analysis
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
ms.openlocfilehash: de76467cc741a65a851e5f3a7ec424d0326aebb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851515"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy
Máte nějaké otázky? Další informace najdete v následujících nejčastějších dotazech.

## <a name="general-faq"></a>Obecné nejčastější dotazy

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Můžu rozšíření nainstalovat do instance serveru Visual Studio Team Foundation Server místo na instanci Azure DevOps?

Ne. Rozšíření není k dispozici pro stahování a instalaci pro Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Je třeba spustit analýzu kódu zabezpečení společnosti Microsoft se svým sestavením? 

Možná. Záleží na typu analytického nástroje. Zdrojový kód může být jediná věc, která je požadována, nebo výstup sestavení může být požadováno.

Například Skener pověření (CredScan) analyzuje soubory ve struktuře složek úložiště kódu. Z důvodu této analýzy můžete spustit CredScan a publikovat protokoly analýzy zabezpečení sestavení úlohy v samostatném sestavení získat výsledky.

Pro další nástroje, jako je BinSkim, které analyzují artefakty po sestavení, sestavení je nutné jako první.

### <a name="can-i-break-my-build-when-results-are-found"></a>Mohu přerušit sestavení, když jsou nalezeny výsledky?

Ano. Přerušení sestavení můžete zavést, když libovolný nástroj hlásí problém nebo problém v jeho souboru protokolu. Stačí přidat úlohu sestavení po analýze a zaškrtněte políčko pro libovolný nástroj, pro který chcete přerušit sestavení.

V ui úlohy po analýze můžete přerušit sestavení, když libovolný nástroj hlásí pouze chyby nebo chyby a upozornění.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Jak se argumenty příkazového řádku v Azure DevOps liší od těchto argumentů v samostatných nástrojích pro stolní počítače? 

Z větší části azure devops sestavení úkoly jsou přímé obálky kolem argumenty příkazového řádku nástrojů zabezpečení. Jako argumenty můžete předat úlohě sestavení vše, co normálně předáte nástroji příkazového řádku.

Znatelné rozdíly:

- Nástroje jsou spuštěny ze zdrojové složky agenta $(Build.SourcesDirectory) nebo z %BUILD_SOURCESDIRECTORY %. Příkladem je C:\agent\_work\1\s.
- Cesty v argumentech mohou být relativní ke kořenovému adresáři dříve uvedeného zdrojového adresáře. Cesty mohou být také absolutní. Absolutní cesty získáte buď pomocí proměnných sestavení Azure DevOps, nebo spuštěním místního agenta se známými umístěními nasazení místních prostředků.
- Nástroje automaticky poskytují cestu nebo složku výstupního souboru. Pokud zadáte výstupní umístění pro úlohu sestavení, toto umístění je nahrazeno cestou k našemu dobře známému umístění protokolů na agentovi sestavení
- Některé další argumenty příkazového řádku jsou změněny pro některé nástroje. Jedním z příkladů je přidání nebo odebrání možností, které zajišťují, že není spuštěno žádné grafické uživatelské rozhraní.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Můžu spustit úlohu sestavení, jako je skener přihlašovacích údajů ve více úložištích v azure devops sestavení?

Ne. Spuštění zabezpečených vývojových nástrojů ve více repozitářích v jednom kanálu není podporováno.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Zadaný výstupní soubor není vytvářen nebo nelze najít zadaný výstupní soubor

Úlohy sestavení filtrují některé vstupy uživatele. Pro tuto otázku konkrétně aktualizovat umístění generovaného výstupního souboru být společné umístění na agenta sestavení. Další informace o tomto umístění naleznete v následujících otázkách.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Kde jsou výstupní soubory generované nástroji uloženy? 

Úlohy sestavení automaticky přidávají výstupní cesty do tohoto dobře známého umístění agenta\_sestavení: $(Agent.BuildDirectory) sdt\logs. Protože standardizujeme v tomto umístění, všechny týmy, které produkují nebo spotřebovávají protokoly analýzy kódu, mají přístup k výstupu.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Je možné zařadit sestavení do fronty pro spuštění těchto úloh v hostovaném agentovi sestavení? 

Ano. Všechny úkoly a nástroje v rozšíření lze provést na hostované sestavení agenta.

>[!NOTE]
> Úloha sestavení antimalwarového skeneru vyžaduje agenta sestavení s povoleným programem Windows Defender. Hostované Visual Studio 2017 a později poskytnout takového agenta. Úloha sestavení se nespustí v hostovaném agentovi Visual Studia 2015.
>
> Přestože podpisy nelze aktualizovat na tyto agenty, podpisy by měly být vždy kratší než tři hodiny.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Lze spustit tyto úlohy sestavení jako součást kanálu verze na rozdíl od kanálu sestavení?

Ve většině případů ano.

Azure DevOps však nepodporuje spuštěné úlohy v rámci kanálů vydání, když tyto úlohy publikují artefakty. Tento nedostatek podpory zabraňuje úspěšnému spuštění úlohy protokolů publikování analýzy zabezpečení v kanálu vydání. Úloha se místo toho nezdaří s popisnou chybovou zprávou.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Odkud úlohy sestavení stahují nástroje?

Úlohy sestavení můžete stáhnout balíčky NuGet nástroje z [kanálu Azure DevOps Package Management](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). Úlohy sestavení můžete také použít Správce balíčků uzlů, který musí být předinstalován na agenta sestavení. Příkladem takové instalace je příkaz **npm install tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Jaký vliv má instalace rozšíření na mou organizaci Azure DevOps? 

Po jejich instalaci budou úkoly sestavení zabezpečení poskytované rozšířením k dispozici všem uživatelům ve vaší organizaci. Když vytvoříte nebo upravíte kanál Azure, tyto úkoly jsou k dispozici ze seznamu kolekce úloh sestavení. V opačném případě instalace rozšíření ve vaší organizaci Azure DevOps nemá žádný vliv. Instalace nezmění žádné nastavení účtu, nastavení projektu nebo kanály.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Upravuje instalace rozšíření moje stávající kanály Azure? 

Ne. Instalace rozšíření zpřístupní úlohy sestavení zabezpečení pro přidání do vašich kanálů. Stále musíte přidávat nebo aktualizovat definice sestavení, aby nástroje mohly pracovat s procesem sestavení.

## <a name="task-specific-faq"></a>Nejčastější dotazy týkající se konkrétních úkolů

Otázky specifické pro vytváření úkolů jsou uvedeny v této části.

### <a name="credential-scanner"></a>Skener pověření

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Jaké jsou běžné scénáře potlačení a příklady?

Zde jsou podrobnosti o dvou nejběžnějších scénářích potlačení.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Potlačení všech výskytů daného tajného klíče v zadané cestě

Klíč hash tajného klíče z výstupního souboru CredScan je vyžadován, jak je znázorněno v následující ukázce.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> Klíč hash je generován částí odpovídající hodnoty nebo obsahu souboru. Jakákoli revize zdrojového kódu může změnit klíč hash a zakázat pravidlo potlačení.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Chcete-li potlačit všechny tajné klíče v zadaném souboru nebo potlačit samotný soubor tajných klíčů

Výraz souboru může být název souboru. Může to být také základní název části úplné cesty k souboru nebo název souboru. Zástupné znaky se nepodporují.

Následující příklady ukazují, jak \<potlačit soubor InputPath>\src\JS\lib\angular.js

Příklady platných pravidel potlačení:

- \<Aplikace InputPath>\src\JS\lib\angular.js - potlačí soubor v zadané cestě
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - potlačí jakýkoli soubor se stejným názvem

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

>[!WARNING] 
> Všechny budoucí tajné kódy přidané do souboru budou také automaticky potlačeny.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Jaké jsou doporučené pokyny pro správu tajných kódů?

Následující prostředky vám pomohou bezpečně spravovat tajné kódy a získat přístup k citlivým informacím z vašich aplikací:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Identita spravované služby Azure AD (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Spravované identity ve službě Azure App Service a Funkcích Azure](../../app-service/overview-managed-identity.md)
 - [Knihovna AppAuthentication](../../key-vault/service-to-service-authentication.md)


Další informace najdete v příspěvku blogu [Správa tajných kódů bezpečně v cloudu](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Mohu napsat vlastní hledače?

Skener pověření spoléhá na sadu hledání obsahu, které jsou běžně definovány v souboru buildsearchers.xml. Soubor obsahuje pole xml serializovaných objektů, které představují **ContentSearcher** objektu. Program je distribuován se sadou dobře testovaných hledačů. Ale můžete implementovat vlastní hledače taky.

Vyhledávací hledáčátek obsahu je definován takto:

- **Název**: Popisný název hledače, který má být použit ve výstupních souborech skeneru pověření. Doporučujeme použít konvenci pojmenování camel-case pro jména hledajících.
- **RuleId**: Stabilní neprůhledné ID hledače:
    - Výchozí vyhledávací soubor pověření skeneru je přiřazena hodnota **RuleId** jako CSCAN0010, CSCAN0020 nebo CSCAN0030. Poslední číslice je vyhrazena pro potenciálně slučující nebo dělící skupiny hledajících pomocí regulárních výrazů (regulární výraz).
    - Hodnota **RuleId** pro vlastní hledače by měla mít vlastní obor názvů. Příklady zahrnují\<CSCAN-\>Namespace 0010, CSCAN-\<Namespace\>0020 a CSCAN-\<Namespace\>0030.
    - Plně kvalifikovaný název hleděče je kombinací hodnoty **RuleId** a názvu hledače. Příklady zahrnují CSCAN0010. KeyStoreFiles a CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: Regex přípon souborů zkontrolovat proti hledač.
- **ContentSearchPatterns**: Pole řetězců obsahujících příkazy regulárního výrazu, které se mají shodovat. Pokud nejsou definovány žádné vzorky hledání, jsou vráceny všechny soubory odpovídající hodnotě **ResourceMatchPattern.**
- **ContentSearchFilters**: Pole řetězců obsahujících příkazy regulárních výrazů pro filtrování falešných poplachů specifických pro vyhledávání.
- **MatchDetails**: Popisná zpráva, pokyny ke zmírnění rizik nebo obojí, které mají být přidány pro každou shodu hledače.
- **Doporučení**: Obsah pole návrhů pro shodu pomocí formátu sestavy PREfast.
- **Závažnost**: Celé číslo, které odráží úroveň závažnosti problému. Nejvyšší úroveň závažnosti má hodnotu 1.

  ![XML zobrazující nastavení skeneru přihlašovacích údajů](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analyzátory Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Jaké jsou běžné chyby při použití úlohy Analyzátory Roslyn?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Projekt byl obnoven pomocí nesprávné verze microsoft.NETCore.App

Úplná chybová zpráva:

"Chyba: Projekt byl obnoven pomocí microsoft.NETCore.App verze *x.x.x*, ale s aktuálním nastavením, verze *y.y.y* by být použity místo. Chcete-li tento problém vyřešit, ujistěte se, že stejná nastavení se používají pro obnovení a pro následné operace, jako je například sestavení nebo publikování. Obvykle k tomuto problému může dojít, pokud runtimeidentifier vlastnost je nastavena během sestavení nebo publikovat, ale ne během obnovení."

Vzhledem k tomu, že úlohy analyzátorů Roslyn jsou spuštěny jako součást kompilace, zdrojový strom v počítači sestavení musí být v sestavitelném stavu.

Krok mezi hlavní sestavení a Roslyn Analyzátory kroky může mít dát zdrojový strom do stavu, který brání vytváření. Tento další krok je pravděpodobně **dotnet.exe publikovat**. Zkuste duplikovat krok, který provádí obnovení NuGet těsně před krokem Roslyn Analyzátory. Tento duplicitní krok může umístit zdrojový strom zpět do sestavitelného stavu.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe nemůže vytvořit instanci analyzátoru

Úplná chybová zpráva:

"'csc.exe' ukončens kódem chyby 1 -- Instance analyzátoru *AAAA* nelze vytvořit z C:\\*BBBB*.dll : Nelze načíst soubor nebo sestavení 'Microsoft.CodeAnalysis, Version=*X.X.X.X*, Culture=neutral, PublicKeyToken=31bf3856ad364e35' nebo jednu z jeho závislostí. Systém nemůže najít zadaný soubor."

Ujistěte se, že váš kompilátor podporuje Roslyn Analyzátory. Spuštění příkazu **csc.exe /version** by mělo hlásit hodnotu verze 2.6 nebo novější.

Někdy soubor .csproj může přepsat instalaci zařízení sestavení Visual Studio odkazem na balíček z Microsoft.Net.Compilers. Pokud nemáte v úmyslu použít konkrétní verzi kompilátoru, odeberte odkazy na Microsoft.Net.Compilers. V opačném případě se ujistěte, že verze odkazovaného balíčku je také 2.6 nebo novější.

Pokuste se získat cestu protokolu chyb, která je zadána v **cc.exe /errorlog** možnost. Možnost a cesta se zobrazí v protokolu pro úlohu sestavení analyzátory Roslyn. Mohou vypadat podobně jako **/errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>Verze kompilátoru Jazyka C# není dostatečně aktuální

Chcete-li získat nejnovější verze kompilátoru jazyka C#, přejděte na [web Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Chcete-li získat nainstalovanou verzi, spusťte **csc.exe /version** na příkazovém řádku. Ujistěte se, že odkazujete na balíček Microsoft.Net.Compilers NuGet, který je verze 2.6 nebo novější.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Protokoly MSBuild a VSBuild nebyly nalezeny.

Úloha sestavení analyzátorů Roslyn musí zadávat dotaz na Azure DevOps pro protokol MSBuild z úlohy sestavení MSBuild. Pokud úloha analyzátoru spustí ihned po úkolu MSBuild, protokol ještě nebude k dispozici. Umístěte další úkoly mezi úlohu MSBuild a úlohu Roslyn Analyzátory. Příklady dalších úkolů patří BinSkim a Anti-Malware Scanner.

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další pomoc, podpora analýzy bezpečnostního kódu společnosti Microsoft je k dispozici od pondělí do pátku od 9:00 do 17:00 tichomořského standardního času.

- Registrace: Podívejte se na naši [dokumentaci k onboardingu](security-code-analysis-onboard.md)
  
- Podpora: Pošlete e-mail našemu týmu na [podporu analýzy bezpečnostního kódu společnosti Microsoft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)