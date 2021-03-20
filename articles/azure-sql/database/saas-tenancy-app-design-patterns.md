---
title: SaaS vzory pro více tenantů
description: Přečtěte si o požadavcích a vzorech architektury Common data Database pro víceklientské aplikace SaaS (software jako služba), které běží v cloudovém prostředí Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 8a13c641d50a68d9661b4aa6caf8effb82d53dd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793224"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Modely tenantů SaaS Database pro více tenantů
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek popisuje různé modely tenantů dostupné pro více tenantů aplikací SaaS.

Při navrhování aplikace SaaS pro více tenantů musíte pečlivě vybrat model architektury, který nejlépe vyhovuje potřebám vaší aplikace.  Model tenantů určuje, jak jsou data každého klienta namapována na úložiště.  Váš výběr modelu architektury ovlivňuje návrh a správu aplikací.  Pozdější přepnutí na jiný model je někdy nákladné.

## <a name="a-saas-concepts-and-terminology"></a>A. Koncepty a terminologie SaaS

V modelu software jako služba (SaaS) vaše společnost neprodává *licence* k vašemu softwaru. Místo toho jednotliví zákazníci propůjčují platby do vaší společnosti, takže každý z nich bude mít *tenanta* vaší společnosti.

Při návratu k placenému pronajmutí každý tenant získá přístup k komponentám vaší aplikace SaaS a jeho data jsou uložená v SaaS systému.

Pojem *model tenantů* odkazuje na uspořádání uložených dat v klientech:

- *Jediná architektura:* &nbsp; Každá databáze uchovává data pouze z jednoho tenanta.
- Víceklientská architektura *:* &nbsp; Každá databáze uchovává data z několika samostatných klientů (s mechanismy ochrany osobních údajů).
- K dispozici jsou také modely hybridních tenantů.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Jak zvolit vhodný model architektury

Obecně platí, že model architektury nemá vliv na funkci aplikace, ale může to mít vliv na jiné aspekty celkového řešení.  Následující kritéria slouží k vyhodnocení každého modelu:

- **Škálovatelnost**
    - Počet tenantů.
    - Úložiště pro každého tenanta.
    - Úložiště je agregované.
    - Úlohy.

- **Izolace tenanta:** &nbsp; Izolace a výkon dat (bez ohledu na to, jestli zatížení jednoho tenanta má vliv na ostatní).

- **Náklady na tenanta:** &nbsp; Náklady na databázi.

- **Složitost vývoje:**
    - Změny schématu.
    - Změny dotazů (vyžadované vzorem).

- **Provozní složitost:**
    - Monitorování a Správa výkonu.
    - Správa schématu.
    - Obnovuje se tenant.
    - Zotavení po havárii.

- **Úpravou:** &nbsp; Snadná Podpora přizpůsobení schématu, která jsou specifická pro konkrétního tenanta nebo pro třídu tenanta.

Diskuze tenantů se zaměřuje na *datovou* vrstvu.  Ale vezměte v úvahu okamžik *aplikační* vrstvy.  Aplikační vrstva je považována za entitu monolitické.  Pokud rozdělíte aplikaci na mnoho malých komponent, může se změnit zvolený model architektury.  Můžete nakládat s některými komponentami, které se týkají i architektury i technologie úložiště nebo používané platformy.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Samostatná aplikace pro jednoho tenanta s databází s jedním tenanta

#### <a name="application-level-isolation"></a>Izolace na úrovni aplikace

V tomto modelu je celá aplikace nainstalována opakovaně, jednou pro každého tenanta.  Každá instance aplikace je samostatnou instancí, takže nikdy nekomunikuje s jinou samostatnou instancí.  Každá instance aplikace má pouze jednoho tenanta, a proto potřebuje pouze jednu databázi.  Tenant má tuto databázi na sebe samu.

![Návrh samostatné aplikace s právě jednou databází s jedním klientem.][image-standalone-app-st-db-111a]

Každá instance aplikace se nainstaluje do samostatné skupiny prostředků Azure.  Skupina prostředků může patřit k předplatnému, které vlastní dodavatel softwaru nebo tenant.  V obou případech může dodavatel spravovat software pro tenanta.  Každá instance aplikace je nakonfigurovaná tak, aby se připojovala k příslušné databázi.

Každá databáze tenanta je nasazena jako jediná databáze.  Tento model poskytuje největší izolaci databáze.  Tato izolace ale vyžaduje, aby se pro každou databázi přidělily dostatečné prostředky pro zpracování zátěže ve špičce.  Tady je důležité, aby elastické fondy nemohly být používány pro databáze nasazené v různých skupinách prostředků nebo v různých předplatných.  Toto omezení usnadňuje, aby tato samostatná aplikace pro jednoho tenanta byla nejdražším řešením z celkové perspektivy nákladů na databázi.

#### <a name="vendor-management"></a>Správa dodavatelů

Dodavatel má přístup ke všem databázím ve všech samostatných instancích aplikace, a to i v případě, že instance aplikace jsou nainstalované v různých předplatných tenantů.  Přístup se dosahuje prostřednictvím připojení SQL.  Tento přístup mezi instancemi může dodavatel umožnit centralizaci správy schématu a databázového dotazu pro účely vytváření sestav a analýzy.  Pokud je žádoucí tento druh centralizované správy, je nutné nasadit katalog, který mapuje identifikátory klientů na identifikátory URI databáze.  Azure SQL Database poskytuje knihovnu horizontálního dělení, která se společně používá k poskytnutí katalogu.  Knihovna horizontálního dělení je formálně pojmenována jako [Klientská knihovna elastic Database][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Víceklientská aplikace s databází – na tenanta

Tento další vzor používá aplikaci s více klienty s mnoha databázemi, přičemž všechny jsou databáze jediného tenanta.  Pro každého nového tenanta se zřídí nová databáze.  Aplikační vrstva *se vertikálně* škáluje přidáním dalších prostředků na jeden uzel.  Nebo se *aplikace vodorovně* škáluje přidáním dalších uzlů.  Škálování je založené na zatížení a je nezávislé na počtu nebo rozsahu jednotlivých databází.

![Návrh aplikace s více klienty s databází na tenanta.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Přizpůsobení pro tenanta

Podobně jako u samostatného vzoru aplikace poskytuje použití jednoho tenanta databáze silnou izolaci klientů.  V libovolné aplikaci, jejíž model určuje jenom databáze s jedním klientem, se dá schéma pro každou databázi přizpůsobit a optimalizovat pro svého tenanta.  Toto vlastní nastavení nemá vliv na ostatní klienty v aplikaci. Možná může klient potřebovat data nad rámec základních datových polí, která potřebují všichni klienti.  Kromě toho je možné, že pole další data bude potřebovat index.

S databází pro každého tenanta je přizpůsobení schématu pro jedno nebo více jednotlivých tenantů jednoduché.  Dodavatel aplikace musí navrhovat postupy pro pečlivou správu přizpůsobení schématu ve velkém měřítku.

#### <a name="elastic-pools"></a>Elastické fondy

Když jsou databáze nasazené ve stejné skupině prostředků, dají se seskupit do elastických fondů.  Fondy poskytují nákladově efektivní způsob sdílení prostředků napříč mnoha databázemi.  Možnost fondu je levnější, než vyžaduje, aby byla každá databáze dostatečně velká, aby se vešla do špičky využití, ke kterým dojde.  I když databáze ve fondu sdílejí přístup k prostředkům, můžou stále dosáhnout vysoké úrovně izolace výkonu.

![Návrh aplikace s více klienty pomocí databáze-na tenanta pomocí elastického fondu.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database poskytuje nástroje potřebné ke konfiguraci, monitorování a správě sdílení.  Metriky výkonu na úrovni fondu i databáze jsou k dispozici v Azure Portal a prostřednictvím protokolů Azure Monitor.  Metriky můžou poskytovat skvělé přehledy jak pro agregovanou, tak pro konkrétního tenanta.  Jednotlivé databáze lze přesunout mezi fondy a poskytnout tak rezervované prostředky konkrétnímu klientovi.  Tyto nástroje vám umožňují zajistit dobrý výkon za cenu.

#### <a name="operations-scale-for-database-per-tenant"></a>Škálování operací pro databáze na tenanta

Azure SQL Database má mnoho funkcí pro správu, které jsou navržené pro správu velkého počtu databází ve velkém měřítku, jako je třeba i více než 100 000 databází.  Tyto funkce vytvářejí vzor databáze pro plausible.

Předpokládejme například, že systém má jako jedinou databázi 1000 databázi tenanta.  Databáze může mít 20 indexů.  Pokud systém převede na 1000 databází s jedním tenantům, množství indexů se zvýší na 20 000.  V Azure SQL Database jako součást [automatického ladění][docu-sql-db-automatic-tuning-771a]jsou funkce automatického indexování standardně povolené.  Automatické indexování je spravováno pro všechny indexy 20 000 a jejich probíhající optimalizace vytváření a vypuštění.  K těmto automatickým akcím dochází v rámci jednotlivé databáze a nejsou koordinovány nebo omezeny podobnými akcemi v jiných databázích.  Automatické indexování zpracovává indexy odlišně v zaneprázdněné databázi než v méně vytížené databázi.  Tento typ přizpůsobení správy indexů by byl nepraktický v škálování databáze na tenanta, pokud by bylo potřeba tuto velkou úlohu správy provést ručně.

Mezi další funkce pro správu, které je vhodné škálovat, patří následující:

- Předdefinované zálohy.
- Vysoká dostupnost
- Šifrování na disku.
- Telemetrie výkonu.

#### <a name="automation"></a>Automation

Operace správy se dají skriptovat a nabízet prostřednictvím modelu [DevOps][http-visual-studio-devops-485m] .  Operace může být dokonce i automatizovaná a zveřejněná v aplikaci.

Například můžete automatizovat obnovení jednoho tenanta do dřívějšího bodu v čase.  Obnovení stačí pouze obnovit jednu databázi s jedním klientem, která ukládá klienta.  Tato operace obnovení nemá žádný vliv na ostatní klienty, což potvrzuje, že operace správy jsou na jemně detailní úrovni každého tenanta.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Víceklientská aplikace s více tenantů databází

Dalším dostupným vzorem je uložení mnoha tenantů do víceklientské databáze.  Instance aplikace může mít libovolný počet databází s více klienty.  Schéma víceklientské databáze musí mít jeden nebo více sloupců identifikátorů tenanta, aby bylo možné selektivně načíst data z libovolného daného tenanta.  Dále schéma může vyžadovat několik tabulek nebo sloupců, které jsou používány pouze podmnožinou klientů.  Statický kód a referenční data jsou však uloženy pouze jednou a jsou sdíleny všemi klienty.

#### <a name="tenant-isolation-is-sacrificed"></a>Izolace tenanta je usmrcena.

*Data:* &nbsp; Víceklientské databáze nutně zabere izolaci klientů.  Data více tenantů jsou uložena společně v jedné databázi.  Během vývoje zajistěte, aby dotazy nikdy nezveřejnily data z více než jednoho tenanta.  SQL Database podporuje [zabezpečení na úrovni řádků][docu-sql-svr-db-row-level-security-947w], které může vyhovět tomu, že data vrácená z dotazu mají rozsah pro jednoho tenanta.

*Zpracovává se:* &nbsp; Víceklientské databáze sdílí výpočetní prostředky a prostředky úložiště ve všech svých klientech.  Tuto databázi je možné monitorovat, aby se zajistilo jejich přijetí.  Systém Azure ale nemá žádný vestavěný způsob, jak tyto prostředky monitorovat nebo spravovat pomocí individuálního tenanta.  Proto má víceklientské databáze zvýšené riziko, že dojde k dosažení sousedů s vysokou zátěží, přičemž zatížení jednoho přečinného tenanta má vliv na výkon jiných tenantů ve stejné databázi.  Další monitorování na úrovni aplikace by mohlo monitorovat výkon na úrovni tenanta.

#### <a name="lower-cost"></a>Nižší náklady

Obecně platí, že databáze s více klienty mají nejnižší náklady na tenanta.  Náklady na prostředky pro izolovanou databázi jsou nižší než u srovnatelného elastického fondu.  Kromě toho pro scénáře, kdy klienti potřebují jenom omezené úložiště, můžou být potenciálně miliony klientů uložené v jediné databázi.  Žádný elastický fond nemůže obsahovat miliony databází.  Řešení, které obsahuje 1000 databází pro každý fond, s 1000 fondy, ale může dosáhnout škály milionů s riziky, která by se stala nepraktickýou správou.

V následující části jsou popsány dvě varianty modelu databáze s více klienty, přičemž model horizontálně dělené multi-tenant je nejpružnější a škálovatelný.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Aplikace pro více tenantů s jedinou databází s více klienty

Nejjednodušší model víceklientské databáze používá jedinou databázi pro hostování dat pro všechny klienty.  Po přidání dalších tenantů se databáze škáluje s větším úložištěm a výpočetními prostředky.  Toto horizontální navýšení kapacity může být nezbytné, i když je vždy limit maximálního měřítka.  Nicméně dlouho před dosažením tohoto limitu se databáze bude nepraktický spravovat.

Operace správy, které jsou zaměřené na jednotlivé klienty, jsou složitější pro implementaci ve více tenantů databázích.  A ve velkém měřítku tyto operace se mohou stát nepřijatelně pomalu.  Jedním z příkladů je obnovení dat k určitému bodu v čase pouze pro jednoho tenanta.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Víceklientská aplikace s horizontálně dělené databázemi s více klienty

Většina aplikací SaaS přistupuje pouze k datům v jednom klientovi.  Tento vzor přístupu umožňuje distribuci dat tenanta napříč více databázemi nebo horizontálních oddílů, kde všechna data pro každého tenanta jsou obsažená v jednom horizontálních oddílů.  V kombinaci se vzorem víceklientské databáze umožňuje model horizontálně dělené skoro neomezené škálování.

![Návrh aplikace s více klienty s horizontálně dělené databázemi s více klienty.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Správa horizontálních oddílů

Horizontálního dělení zvyšuje složitost jak pro návrh, tak pro provozní správu.  Pro udržování mapování mezi klienty a databázemi je vyžadován katalog.  Kromě toho jsou pro správu horizontálních oddílů a naplnění tenanta vyžadovány postupy správy.  Například procedury musí být navrženy pro přidávání a odebírání horizontálních oddílů a přesouvání dat klientů mezi horizontálních oddílů.  Jedním ze způsobů, jak škálovat, je přidání nového horizontálních oddílů a jeho naplnění novými klienty.  V jinou dobu můžete rozdělit hustě naplněný horizontálních oddílů na dvě méně husté horizontálních oddílůně naplněné.  Po přesunutí nebo ukončení několika tenantů můžete sloučit zhuštěně naplněné horizontálních oddílů dohromady.  Sloučení by vedlo k efektivnějšímu využití prostředků.  Klienti můžou být taky přesunuti mezi horizontálních oddílů a vyrovnávat zatížení.

SQL Database poskytuje nástroj pro dělení a slučování, který funguje ve spojení s knihovnou horizontálního dělení a databází katalogu.  Zadaná aplikace může rozdělit a sloučit horizontálních oddílů a může přesouvat data klienta mezi horizontálních oddílů.  Aplikace také udržuje katalog během těchto operací a před jejich přesunutím označuje ovlivněné klienty jako offline.  Po přesunu aplikace znovu aktualizuje katalog s novým mapováním a označí klienta jako zpět online.

#### <a name="smaller-databases-more-easily-managed"></a>Jednodušší správa menších databází

Díky distribuci klientů napříč více databázemi horizontálně dělené řešení pro více tenantů vede k menšímu počtu databází, které jsou snadněji spravované.  Například obnovení konkrétního klienta na předchozí bod v čase nyní zahrnuje obnovení jedné menší databáze ze zálohy, nikoli větší databáze, která obsahuje všechny klienty. Velikost databáze a počet klientů na databázi lze zvolit pro vyrovnávání zatížení a úsilí v rámci správy.

#### <a name="tenant-identifier-in-the-schema"></a>Identifikátor tenanta ve schématu

V závislosti na použitém přístupu k horizontálního dělení můžou být do schématu databáze zavedena další omezení.  Aplikace SQL Database rozdělit/sloučit vyžaduje, aby schéma zahrnovalo klíč horizontálního dělení, který obvykle je identifikátorem tenanta.  Identifikátor tenanta je předním prvkem v primárním klíči všech tabulek horizontálně dělené.  Identifikátor tenanta umožňuje aplikaci rozdělené/sloučení rychle vyhledat a přesunout data přidružená k určitému klientovi.

#### <a name="elastic-pool-for-shards"></a>Elastický fond pro horizontálních oddílů

Databáze horizontálně dělené s více klienty lze umístit do elastických fondů.  Obecně platí, že je velký počet databází s jednou tenantů ve fondu, což je cenově výhodné, protože má mnoho tenantů v několika databázích s více klienty.  Víceklientské databáze jsou výhodné, pokud existuje velký počet relativně neaktivních klientů.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>Y. Model hybridního víceklientské databáze horizontálně dělené

V hybridním modelu mají všechny databáze ve svém schématu identifikátor tenanta.  Databáze jsou schopné uložit více než jednoho tenanta a databáze mohou být horizontálně dělené.  Takže ve smyslu schématu jsou to všechny víceklientské databáze.  V praxi některé z těchto databází ještě obsahují jenom jednoho tenanta.  Bez ohledu na to, že počet klientů uložených v dané databázi nemá žádný vliv na schéma databáze.

#### <a name="move-tenants-around"></a>Přesunout klienty kolem

Konkrétního tenanta můžete kdykoli přesunout do své vlastní databáze s více klienty.  A kdykoli můžete změnit svoje rozhodnutí a přesunout klienta zpět do databáze, která obsahuje více tenantů.  Při zřizování nové databáze můžete také klienta přiřadit k novému jedinému tenantovi databáze.

Hybridní model se vydává v případě rozsáhlých rozdílů mezi potřebou prostředků zjistitelných skupin klientů.  Předpokládejme například, že klienti účastnící se bezplatné zkušební verze nemají zaručenou stejnou vysokou úroveň výkonu, který je předplatitelem klienta.  Zásady můžou být pro klienty v bezplatné zkušební fázi, aby se uložily do víceklientské databáze, která je sdílená mezi všemi klienty bezplatné zkušební verze.  Když se tenant bezplatné zkušební verze přihlásí ke službě Basic úrovně služby, může se klient přesunout do jiné databáze s více klienty, která může mít méně tenantů.  Předplatitel, který platí pro úroveň Premium Service, se může přesunout do své vlastní nové databáze s jedním tenanta.

#### <a name="pools"></a>Fondy

V tomto hybridním modelu je možné umístit databáze jednoho tenanta pro klienty předplatitele do fondů zdrojů, aby se snížily náklady databáze na tenanta.  To se provádí taky v modelu databáze pro jednotlivé klienty.

## <a name="i-tenancy-models-compared"></a>I. Porovnání modelů architektury

Následující tabulka shrnuje rozdíly mezi hlavními modely tenantů.

| Měření | Samostatná aplikace | Databáze – na tenanta | Horizontálně dělené více tenantů |
| :---------- | :------------- | :------------------ | :------------------- |
| Měřítko | Střední<br />1 – 100 | Velmi vysoké<br />1 – 100, tisících | Unlimited<br />1 – 1, 000, tisících |
| Izolace tenanta | Velmi vysoké | Vysoká | Slab s výjimkou jednoho tenanta (který je samostatně v MT DB). |
| Náklady na databázi na tenanta | Maximální má velikost pro špičky. | Slab používané fondy. | Nejnižší pro malé klienty v MT databáze. |
| Sledování a Správa výkonu | Jenom pro tenanta | Agregovaná + pro každého tenanta | Souhrnné i když je jeden tenant jenom pro jednoduchou. |
| Složitost vývoje | Nízká | Nízká | Úrovně kvůli horizontálního dělení. |
| Provozní složitost | Nízká-vysoká. Samostatně jednoduché, složité ve velkém měřítku. | Nízká – střední. Vzory řeší složitost v rozsahu. | Nízká-vysoká. Správa jednotlivých klientů je složitá. |
| &nbsp; ||||

## <a name="next-steps"></a>Další kroky

- [Nasazení a zkoumání víceklientské aplikace Wingtip, která používá model SaaS pro jednotlivé klienty – Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Vítá vás ukázková lístky Wingtip SaaS Azure SQL Database aplikace tenantů][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: /sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]:elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: saas-dbpertenant-get-started-deploy.md
[docu-sql-db-automatic-tuning-771a]:automatic-tuning-overview.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Návrh samostatné aplikace s právě jednou databází s jedním klientem."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Návrh aplikace s více klienty s databází na tenanta."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Návrh aplikace s více klienty pomocí databáze-na tenanta pomocí elastického fondu."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Návrh aplikace s více klienty s horizontálně dělené databázemi s více klienty."