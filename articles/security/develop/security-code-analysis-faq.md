---
title: Nejčastější dotazy ke službě Microsoft Azure Security Code Analysis
description: Tento článek obsahuje nejčastější dotazy týkající se rozšíření analýzy bezpečnostního kódu.
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
ms.openlocfilehash: 1fc5e83c2c46a7da2a4b56879a2d596405fd8bee
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718311"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy
Máte otázky? Další informace najdete v nejčastějších dotazech dole.

## <a name="general-faqs"></a>Obecné Nejčastější dotazy

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>Můžu nainstalovat rozšíření na mém serveru TFS (ne na Azure DevOps)? 

Ne, rozšíření není k dispozici ke stažení a instalaci pro TFS.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Musím v mém buildu spustit analýzu kódu zabezpečení od Microsoftu? 

Ano a ne. V závislosti na typu analytického nástroje může být samotný zdrojový kód pouze to, co je požadováno, nebo může být požadováno výstup sestavení. Například vzhledem k tomu, že aplikace pro kontrolu přihlašovacích údajů analyzuje soubory v rámci struktury složek v úložišti kódu, můžete spustit úlohy skeneru přihlašovacích údajů a publikovat protokoly analýzy zabezpečení v samostatném sestavení a načíst výsledky.
Pro jiné nástroje, které analyzují artefakty po sestavení, jako je BinSkim, se sestavení požaduje jako první.

### <a name="can-i-break-my-build-when-results-are-found"></a>Je možné ukončit sestavení při nalezení výsledků? 
Ano, můžete začlenit přerušení sestavení, když kterýkoli nástroj ohlásí problém, najde v jeho souboru protokolu. Stačí přidat úlohu sestavení po analýze a zaškrtnout políčko u všech nástrojů, pro které chcete sestavení přerušit. Můžete zvolit, že se má sestavení přerušit, když jakýkoli nástroj ohlásí chyby nebo upozornění a chyby v uživatelském rozhraní úlohy po analýze.

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>Jak se liší argumenty příkazového řádku v Azure DevOps, než jsou v samostatných desktopových nástrojích? 

Ve většině případů jsou úkoly sestavení Azure DevOps přímými obálkami kolem argumentů příkazového řádku nástrojů zabezpečení. Cokoli, co byste normálně předávali nástroji na příkazovém řádku z plochy, můžete předat zadání argumentů pro úlohu sestavení.
Tady je seznam znatelnéch rozdílů:
 - Nástroj bude spuštěn ze zdrojové složky agenta $ (Build. SourcesDirectory) nebo% BUILD_SOURCESDIRECTORY%. Příklad: C:\Agent\_work\1\s 
 - Cesty v argumentech můžou být relativní ke kořenu zdrojového adresáře uvedeného výše nebo jako absolutní, a to spuštěním agenta on-Prem se známými umístěními nasazení místních prostředků nebo pomocí proměnných Azure DevOps buildu.
 - Nástroje budou automaticky poskytovat cestu k výstupnímu souboru nebo složce, pokud je zadána výstupní cesta, bude odebrána a nahrazena cestou k našemu dobře známému umístění protokolů v agentovi sestavení.
 - Některé další parametry příkazového řádku se upraví a odeberou na některých nástrojích, jako je přidání nebo odebrání možností pro zajištění, že se nespustí žádné grafické rozhraní (GUI).

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Můžu spustit úlohu sestavení (například skener přihlašovacích údajů) ve více úložištích ve službě Azure DevOps Build? 

Ne. v současné době není podporováno používání nástrojů pro bezpečné vývojové prostředí proti více úložištím v jednom kanálu.

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>Zadaný výstupní soubor se nevytváří nebo nejde najít zadaný výstupní soubor. 

Úlohy sestavení aktuálně upravují vstup uživatele a aktualizují umístění výstupního souboru vygenerovaného na společné umístění agenta sestavení. Další informace o tomto umístění najdete na následujících dotazech.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Kde jsou výstupní soubory generované nástroji uložené? 

Úkoly sestavení automaticky přidávají výstupní cesty k následujícímu známému umístění v agentovi sestavení $ (agent. BuildDirectory)\_sdt\logs. Díky standardizaci v tomto umístění můžeme zaručit, že budou mít přístup i další týmy, které vytvářejí protokoly analýzy kódu nebo jejich využívání.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Můžu sestavení zařadit do fronty a spustit tyto úlohy v hostovaném agentu sestavení? 

Ano, všechny úlohy a nástroje v rozšíření lze spustit u hostovaného agenta sestavení.

>[!NOTE]
> Úloha sestavení proti malwaru vyžaduje, aby byl agent sestavení s povoleným Windows Defenderem, který platí pro "hostované VS2017" nebo novější agenty sestavení. (Nespustí se na hostovaném agentovi starší/VS2015.) Podpisy nelze na těchto agentech aktualizovat, ale signatura by měla být vždy poměrně aktuální, méně než 3 hodiny staré.
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Je možné spustit tyto úlohy sestavení jako součást kanálu pro vydávání verzí (na rozdíl od kanálu sestavení)? 
Ve většině případů Ano. Nicméně úlohy, které publikují artefakty, nejsou službou Azure DevOps spuštěné v rámci kanálů vydávání: "Jediná kategorie úloh, které se neočekávají pro práci s vydáním, jsou ty, které publikují artefakty. Důvodem je, že od této chvíle neposkytujeme podporu pro publikování artefaktů v rámci vydání.
Tím se zabrání úspěšnému spuštění úlohy publikovat protokoly analýzy zabezpečení z kanálu vydání; dojde k selhání s popisnou chybovou zprávou.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Odkud úlohy sestavení stáhnou nástroje? 
Úkoly sestavení a) stahují balíčky NuGet pro nástroje z následujícího informačního [kanálu služby Azure DevOps Správa balíčků](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) nebo pomocí uzlu Správce balíčků, které musí být předem nainstalovány v agentu sestavení (například: "npm Install tslint").

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>Jaký vliv bude rozšíření instalovat na moji organizaci Azure DevOps? 

Po instalaci budou úkoly sestavení zabezpečení poskytované rozšířením k dispozici pro použití všemi uživateli ve vaší organizaci. Při vytváření nebo úpravách kanálu Azure budou tyto úkoly k dispozici pro přidání ze seznamu kolekce úloh sestavení. V opačném případě není instalace rozšíření v organizaci Azure DevOps nijak ovlivněna. Neupravuje žádný účet nebo nastavení projektu ani kanály.

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>Bude instalace rozšíření upravit existující Azure Pipelines? 

Ne. Při instalaci rozšíření budou úkoly sestavení zabezpečení k dispozici pro přidání do vašeho Azure Pipelines. Uživatelé jsou stále vyžadováni k přidání nebo aktualizaci definic sestavení pro integraci nástrojů do procesu sestavení.

## <a name="task-specific-faqs"></a>Nejčastější dotazy ke konkrétním úlohám

V této části jsou uvedené Nejčastější dotazy týkající se úloh sestavení.

### <a name="credential-scanner-faqs"></a>Nejčastější dotazy ke skeneru přihlašovacích údajů

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>Co jsou běžné scénáře a příklady potlačení? 
Dva z nejběžnějších scénářů potlačení jsou popsané níže:
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Potlačí všechny výskyty daného tajného kódu v zadané cestě. 
Je nutné zadat klíč hash tajného kódu z výstupního souboru pro kontrolu přihlašovacích údajů, jak je znázorněno v následující ukázce.
   
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
> Klíč hash je vygenerován částí odpovídající hodnoty nebo obsahu souboru. Jakákoli revize zdrojového kódu by mohla změnit klíč hash a zakázat pravidlo potlačení. 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Pro potlačení všech tajných kódů v zadaném souboru (nebo pro potlačení samotného souboru tajných klíčů) 
Výraz souboru může být název souboru nebo libovolná přípona v úplné cestě k souboru nebo názvu. Zástupné znaky se nepodporují. 

**Příklad** 

Soubor, který se má potlačit: [InputPath] \src\JS\lib\angular.js 

Platná pravidla potlačení: 
- [InputPath] \src\JS\lib\angular.js – potlačit soubor v zadané cestě
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- úhlov. js – potlačení všech souborů se stejným názvem

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
> Všechny budoucí tajné klíče přidané do souboru se taky automaticky potlačí. 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>Jaké jsou doporučené pokyny pro správu tajných kódů? 
Při odhalování pevně kódovaných tajných klíčů včas a jejich zmírnění je užitečné, je ještě lepší, pokud by jedna mohla zabránit tomu, aby se tajná klíča úplně kontrolovala. V tomto ohledu společnost Microsoft vydala analyzátor kódu CredScan jako součást [rozšíření Microsoft DevLabs Extension](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) pro sadu Visual Studio. V předběžnou verzi Preview poskytuje vývojářům vložené prostředí pro zjišťování možných tajných kódů v kódu, takže jim dává možnost tyto problémy vyřešit v reálném čase. Další informace najdete v [tomto](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/) blogu o bezpečné správě tajných kódů v cloudu. Níže najdete pár dalších prostředků, které vám pomůžou spravovat tajné klíče a přistupovat k citlivým informacím z vašich aplikací zabezpečeným způsobem: 
 - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
 - [Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
 - [Identita spravované služby Azure AD](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Identita spravované služby pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
 - [Identita spravované služby Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity)
 - [Knihovna AppAuthentication](https://docs.microsoft.com/azure/key-vault/service-to-service-authentication)

#### <a name="can-i-write-my-own-custom-searchers"></a>Můžu psát vlastní vyhledávací vlastníky?

Kontrola přihlašovacích údajů spoléhá na sadu vyhledávačů obsahu, která je běžně definovaná v souboru **buildsearchers. XML** . Soubor obsahuje pole serializovaných objektů XML, které reprezentují objekt ContentSearcher. Program se distribuuje se sadou vyhledávacích programů, které byly dobře testovány, ale umožňují vám také implementovat vlastní vyhledávací služby. 

Hledání obsahu je definováno následujícím způsobem: 

- **Název** – popisný název vyhledávacího programu, který se použije ve výstupním souboru pro kontrolu přihlašovacích údajů. Doporučuje se používat ve stylu CamelCase konvence pro pojmenování názvů vyhledávacích objektů. 
- **RuleId** – stabilní neprůhledné ID vyhledávacího programu. 
    - Výchozí vyhledávací nástroje pro kontrolu přihlašovacích údajů jsou přiřazené k RuleIds, jako je CSCAN0010, CSCAN0020, CSCAN0030 atd. Poslední číslice je vyhrazena pro případ slučování nebo dělení skupinového regulárního výrazu vyhledávacího výrazu.
    - RuleId pro přizpůsobené vyhledávací služby by měly mít vlastní obor názvů ve formátu: CSCAN-{Namespace} 0010, CSCAN-{Namespace} 0020, CSCAN-{Namespace} 0030 atd.
    - Plně kvalifikovaný název hledání je kombinací názvu RuleId a vyhledávacího pole. Příklad CSCAN0010. KeyStoreFiles, CSCAN0020. Base64EncodedCertificate atd.
- **ResourceMatchPattern** – regulární výraz přípon souborů, které se mají kontrolovat u hledání
- **ContentSearchPatterns** – pole řetězců obsahující příkazy Regex, které mají být shodné. Pokud nejsou definovány žádné vzory hledání, vrátí se všechny soubory odpovídající vzoru shody prostředků.
- **ContentSearchFilters** – pole řetězců obsahující příkazy regulárního výrazu pro filtrování falešně pozitivních hodnot specifických pro hledání.
- **Matchdetails** – Popisná zpráva a pokyny pro zmírnění rizik, které je třeba přidat pro každou shodu hledání.
- **Doporučení** – poskytuje obsah pole návrhy pro porovnávání pomocí formátu předrychlých sestav.
- **Závažnost** – celé číslo, které odráží závažnost problému (nejvyšší = 1).
![Nastavení skeneru přihlašovacích údajů](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Nejčastější dotazy k analyzátorům Roslyn

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>Jaké jsou nejběžnější chyby při použití úlohy analyzátory Roslyn?

**Chyba: Projekt se obnovil pomocí Microsoft. NETCore. app verze x. x. x, ale s aktuálním nastavením se místo toho použije verze y. y. y. Chcete-li tento problém vyřešit, zajistěte, aby se pro obnovení používalo stejné nastavení a pro následné operace, jako je například sestavení nebo publikování. K tomuto problému obvykle dochází, pokud je vlastnost RuntimeIdentifier nastavena během sestavování nebo publikování, ale není během obnovování:**

Analyzátory Roslyn se spouštějí jako součást kompilace, takže zdrojový strom na sestavovacím počítači musí být ve stavu, který lze sestavit. Krok (pravděpodobně "dotnet. exe Publish") mezi hlavní sestavami buildu a analyzátorem Roslyn může obsahovat zdrojový strom v nesestaveném stavu. Možná duplikujete krok, který provede obnovení NuGet těsně před krokem analyzátory Roslyn, umístí zdrojový strom zpátky do sestaveného stavu.

**CSc. exe byl ukončen s kódem chyby 1 – instanci služby Analyzer AAAA nelze vytvořit z C:\BBBB.dll: Nepovedlo se načíst soubor nebo sestavení Microsoft. CodeAnalysis, Version = X. X. X. X, Culture = neutral, PublicKeyToken = 31bf3856ad364e35 nebo jedna z jejích závislostí. Systém nemůže najít zadaný soubor.**

Ujistěte se, že kompilátor podporuje analyzátory Roslyn. CSc. exe/Version by měl hlásit aspoň v 2.6. x. V některých případech mohou jednotlivé soubory. csproj obejít instalaci sady Visual Studio sestavení počítače pomocí odkazu na balíček z Microsoft.Net. compilers. Pokud použití konkrétní verze kompilátoru nebylo zamýšlené, odeberte odkazy na Microsoft.Net. compilers. V opačném případě se ujistěte, že je příslušný balíček také minimálně v 2.6. x. Zkuste získat protokol chyb, který najdete v/errorlog: parametr z příkazového řádku csc. exe (najdete ho v protokolu úlohy sestavení Roslyn). Může to vypadat nějak takto:/errorlog: f:\ts-Services-123\_work\456\s\Some\Project\Code\Code.csproj.Sarif

**C# Kompilátor není dost nedávné (musí být > = 2,6)**

Nejnovější verze C# kompilátoru jsou vydány zde: https://www.nuget.org/packages/Microsoft.Net.Compilers. Nainstalovanou verzi získáte spuštěním příkazu Spustit `C:\>csc.exe /version` z příkazového řádku. Ujistěte se, že nemáte žádné odkazy na balíček NuGet. compilers nástroje Microsoft.Net, který je < v 2.6.

**Protokoly MSBuild/VSBuild se nenašly.**

Vzhledem k tomu, jak úkol funguje, musí tato úloha zadat dotaz na Azure DevOps pro protokol MSBuild z úlohy sestavení MSBuild. Pokud se tato úloha spustí hned po úloze sestavení MSBuild, protokol ještě nebude k dispozici. Mezi úlohu sestavení MSBuild a úlohu sestavení Roslyn Analyzer umístěte další úkoly sestavení, včetně úloh sestavení SecDevTools, jako je Binskim, antimalwarová kontrola a další). 

## <a name="next-steps"></a>Další postup

Pokud potřebujete další pomoc, podpora analýzy kódu zabezpečení společnosti Microsoft je k dispozici od pondělí do pátku od 9:00 dop. 5:00 odp. tichomořského (běžný čas)

  - Připojování – kontaktujte správce technických účtů a začněte. 
  >[!NOTE] 
  >Pokud ještě nemáte placenou relaci podpory Microsoftu nebo pokud máte nabídku podpory, která vám neumožňuje kupovat služby z katalogu v Phoenixu, navštivte prosím naši [domovskou stránku služeb podpory](https://www.microsoft.com/enterprise/services/support) , kde najdete další informace.

  - Podpora – E-mail pro náš tým na [webu Microsoft Security Code Analysis support](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)