---
title: Víceklientské vzory SaaS – Azure SQL Database | Dokumentace Microsoftu
description: Další informace o požadavky a data služby common vzorech architektury víceklientský software jako služba (SaaS) aplikací databáze, které běží v prostředí cloudu Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 2775ceb3cf27b6feedfd73cd43855204490ebc31
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471194"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Vzory víceklientské SaaS databáze tenantů

Při navrhování aplikace SaaS s více tenanty, musíte pečlivě zvolit tenantů model, který nejlépe vyhovuje potřebám vaší aplikace.  Tenantů model Určuje, jak data každého klienta je mapována do úložiště.  Podle vašeho výběru tenantů modelu má vliv na návrh aplikace a správu.  Přepnutí na jiný model pozdější někdy je nákladné.

Tento článek popisuje alternativní tenantů modely.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS koncepty a terminologie

V tomto softwaru jako služby (SaaS) model není prodávat vaše společnost *licence* váš software. Místo toho každý zákazník odešle poskytovat do nájmu platby vaší společnosti, aby každý zákazník *tenanta* vaší společnosti.

Po platit nadřazené, každý tenant získá přístup k komponenty aplikace SaaS a má jeho dat uložených v systému SaaS.

Termín *tenantů modelu* odkazuje na uspořádání uložených dat klientů:

- *Single – architektura:*&nbsp; Každá databáze uchovává data z pouze jednoho tenanta.
- *Víceklientská architektura:*&nbsp; Každá databáze uchovává data z více tenantů v samostatných (s mechanismy pro ochranu dat o ochraně osobních údajů).
- Hybridní tenantů modely jsou také k dispozici.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Jak zvolit odpovídající tenantů modelu

Obecně platí tenantů model nemá vliv na funkce aplikace, ale pravděpodobně ovlivní další aspekty celkového řešení.  Tato kritéria se používají k vyhodnocení všech modelů:

- **Škálovatelnost:**
    - Počet klientů.
    - Úložiště na tenanta.
    - Úložiště v agregaci.
    - Úlohy.

- **Izolaci klientů:**&nbsp; Izolace dat a výkonu (Určuje, zda pracovní zatížení jednoho tenanta má vliv na ostatní).

- **Náklady na tenanta:**&nbsp; Náklady na databázi.

- **Vývoj složitost:**
    - Změny schématu.
    - Změny dotazů (vyžadují vzor).

- **Provozní složitost:**
    - Monitorování a správa výkonu.
    - Správa schématu.
    - Obnovení klienta.
    - Zotavení po havárii.

- **Přizpůsobitelnost:**&nbsp; Snadné podpora schématu přizpůsobení, které jsou buď specifickým pro tenanta nebo tenanta specifický pro třídu.

Diskuze tenantů se zaměřuje na *data* vrstvy.  Ale zamysleme *aplikace* vrstvy.  Aplikační vrstvu je považován za monolitické entity.  Pokud budete provádět dělení aplikaci do mnoha malých součástí, podle vašeho výběru modelu tenantů může změnit.  Některé součásti může zpracovávat jinak než ostatní tenantů a technologie úložiště nebo platformu používá.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Samostatná jednoho tenanta aplikace s jedním tenantem databáze

#### <a name="application-level-isolation"></a>Úrovně izolace aplikací

V tomto modelu celé aplikace se nainstaluje opakovaně, jednou pro každého tenanta.  Každá instance aplikace je samostatná, proto nikdy komunikuje s jakoukoli samostatnou instanci.  Každá instance aplikace má jenom k jednomu tenantovi a proto potřebuje pouze jednu databázi.  Tenant je databáze na sebe sama.

![Návrh samostatné aplikace s využitím přesně jeden databáze s jedním tenantem.][image-standalone-app-st-db-111a]

Každá instance aplikace se instaluje do samostatných skupinu prostředků Azure.  Skupina prostředků může patřit do předplatného, který je vlastněn dodavatele softwaru nebo tenanta.  V obou případech můžete spravovat na dodavatele softwaru pro příslušného tenanta.  Každá instance aplikace nakonfigurovaná pro připojení k příslušné databázi.

Každá databáze tenanta se nasadí jako izolovanou databázi.  Tento model poskytuje největší izolace databáze.  Ale izolaci vyžaduje, že pro každou databázi pro zpracování jeho špičkách přidělit dostatek prostředků.  Tady je to nejdůležitější, elastické fondy nelze použít pro databáze, které jsou nasazené v různých skupinách prostředků nebo do různých předplatných.  Toto omezení je tato aplikace jednoho tenanta samostatný model nejdražší řešení z hlediska celkové náklady na databázi.

#### <a name="vendor-management"></a>Správa dodavatele

Dodavatele můžete přístup ke všem databázím na všechny samostatné aplikace instance, i v případě, že instance aplikace jsou nainstalované v jiném tenantovi předplatných.  Přístup se dosahuje prostřednictvím připojení SQL.  Tento přístup různé instance můžete povolit na dodavatele a centralizovaná správa schématu a mezi databázemi dotazu pro účely nahlášení nebo analýzy.  Pokud je tento druh centralizovanou správu, katalog musí být nasazený, který mapuje identifikátory tenanta na databázi identifikátory URI.  Azure SQL Database poskytuje knihovnu horizontálního dělení, která se používá spolu s SQL database k poskytování katalog.  Knihovna horizontálního dělení je formálně s názvem [Klientská knihovna Elastic Database][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Aplikace s více tenanty s databází na tenanta

Tento další model používá aplikaci s více tenanty s mnoha databázemi, všechny právě jednoho tenanta databází.  Nová databáze je zřídit pro každého nového klienta.  Škálovat na úrovni aplikace *nahoru* svisle tak, že přidáte další prostředky na jeden uzel.  Nebo škálování aplikace *si* horizontálně přidáním dalších uzlů.  Škálování podle úloh a nezávisí čísla nebo škálování jednotlivých databází.

![Návrh aplikace s více tenanty s databází na tenanta.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Vlastní nastavení pro klienta

Jako vzor pro samostatné aplikace použijte jednoho tenanta databáze poskytuje izolaci silné klientů.  V jakékoli aplikaci, jejíž model určuje pouze jednoho tenanta databází je možné přizpůsobit a optimalizovaná pro jeho tenanta schématu jeden dané databáze.  Toto přizpůsobení neovlivní jiných tenantů v aplikaci. Možná tenanta může být nutné data nad rámec základní datová pole, které je třeba všechny tenanty.  Doplňující data pole může být nutné dále indexu.

S databází na tenanta je jednoduché pro dosažení přizpůsobení schéma pro jeden nebo více jednotlivých tenantů.  Na dodavatele aplikace musí navrhovat postupy při správě pečlivě úpravy schématu ve velkém měřítku.

#### <a name="elastic-pools"></a>Elastické fondy

Při nasazení databáze ve stejné skupině prostředků, mohou být seskupeny do elastických fondů.  Fondy poskytuje nákladově efektivní způsob sdílení prostředků mezi mnoha databázemi.  Tato možnost fondu je levnější než vyžadující každou databázi bude dostatečně velký, aby odpovídala špičky využití, které dochází.  I v případě, že databáze ve fondu sdílejí přístup k prostředkům se můžete stále dosáhnout vysokou míru izolace výkonu.

![Návrh aplikace s více tenanty s databází na tenanta, použití elastického fondu.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database poskytuje nástroje potřebné k konfigurovat, monitorovat a spravovat sdílení.  Obě metriky na úrovni fondu a databáze úrovně výkonu jsou k dispozici na webu Azure Portal a pomocí Log Analytics.  Metriky můžete poskytovat skvělé informací o výkonu agregují a specifickým pro tenanta.  Jednotlivé databáze můžete přesouvat mezi fondy poskytují vyhrazené prostředky ke konkrétnímu tenantu.  Tyto nástroje umožňují zajistili dobrý výkon nákladově efektivní způsobem.

#### <a name="operations-scale-for-database-per-tenant"></a>Operace škálování pro databáze na tenanta

Platforma Azure SQL Database má řadu funkcí správy, které jsou navržené tak, aby správu velkého počtu databází ve velkém měřítku, jako je například mnohem více než 100 000 databází.  Tyto funkce usnadnění přesvědčivého model databáze na tenanta.

Například předpokládejme, že systém má databáze tenanta 1000 jako jeho pouze jednu databázi.  Databáze může mít 20 indexy.  Pokud systém převede s tím, že databáze 1000 jednoho tenanta, roste počet indexů na 20 000.  Ve službě SQL Database jako součást [automatické ladění][docu-sql-db-automatic-tuning-771a], automatické indexování funkcí jsou ve výchozím nastavení povolené.  Automatické indexování spravuje za vás všechny 20 000 indexy a průběžné vytváření a drop optimalizace.  Tyto automatizované akce, ke kterým došlo v jednotlivých databází a nejsou koordinovaný nebo omezené na základě akce podobně jako v jiných databázích.  Automatické indexování zpracovává indexy odlišně v zaneprázdněné databáze než méně zaneprázdněná databáze.  Tento typ indexu přizpůsobení správy by nepraktické ve velkém měřítku databáze na tenanta, pokud tuto úlohu správy obrovské museli provést ručně.

Jiné funkce správy, které se velikostí dobře patří:

- Integrované zálohování.
- Vysoká dostupnost
- Šifrování na disku.
- Telemetrické informace o výkonu.

#### <a name="automation"></a>Automation

Operace správy mohou být skripty a nabízených přes [devops] [ http-visual-studio-devops-485m] modelu.  Operace můžete dokonce automatizovaná a vystavený v aplikaci.

Například může automatizovat obnovení jednoho tenanta k dřívějšímu bodu v čase.  Obnovení pouze musí obnovit jednu databázi jednoho tenanta, který ukládá tenanta.  Toto obnovení nemá žádný vliv na ostatní tenanty, které potvrdí, že operace správy jsou jemně detailní úrovni jednotlivých jednotlivých klientů.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Víceklientské aplikace s víceklientskou databází

Další dostupné vzor je uložit velký počet klientů v databázi s více tenanty.  Instance aplikace může mít libovolný počet databází pro více tenantů.  Schéma víceklientskou databází musí mít jeden nebo více sloupců identifikátor tenanta, aby můžete selektivně načíst data z žádného daného tenanta.  Schéma může vyžadovat další, několik tabulek nebo sloupců, které jsou používány pouze podmnožinu tenantů.  Však statické kódu a referenčních dat jsou uloženy pouze jednou a je sdílen všech tenantů.

#### <a name="tenant-isolation-is-sacrificed"></a>Publikovaný izolaci klientů

*Data:*&nbsp; Databázi s více tenanty nutně upřednostňuje izolaci klientů.  Data z více tenantů se ukládají společně v jedné databázi.  Během vývoje Ujistěte se, že dotazy nikdy vystavit data z více než jednoho tenanta.  SQL Database podporuje [zabezpečení na úrovní řádků][docu-sql-svr-db-row-level-security-947w], které můžete vynutit tato data vrácená z dotazu obor do jednoho tenanta.

*Zpracování:*&nbsp; Databázi s více tenanty sdílí výpočetních a úložných kapacit ve všech jeho tenantech.  K zajištění, že funguje přijatelně, je možné monitorovat databáze jako celek.  Systému Azure ale nemá možnost nijak integrované sledovat nebo spravovat pomocí těchto prostředků, jednotlivým tenantem.  Proto databázi s více tenanty přenáší je zvýšené riziko vzniku "hlučným sousedům," kde úloh jednoho tenanta overactive ovlivňuje výkon z jiných tenantů ve stejné databázi.  Další monitorování na úrovni aplikace může monitorovat výkon na úrovni tenanta.

#### <a name="lower-cost"></a>Nižší náklady

Obecně platí databáze s více tenanty máte nejnižší na tenanta nákladů.  Náklady na prostředky pro izolované databáze jsou nižší než ekvivalentně velikosti fondu elastické databáze.  Kromě toho pro scénáře, které klienti vyžadují pouze omezené úložiště, potenciálně miliony tenantů může být uloženy v izolované databáze.  Žádný elastický fond může obsahovat miliony databází.  Řešení obsahující 1000 databází na fond, s 1 000 fondy, však může dosáhnout škálování milionů nese stávají nepraktické.

Dvě varianty databázi s více tenanty modelu jsou popsány v následující, s horizontálně dělené víceklientského modelu, flexibilní a škálovatelná.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Aplikace s více tenanty pomocí jedné databázi s více tenanty

Nejjednodušším vzorem databázi s více tenanty izolované databáze k hostování dat používá pro všechny tenanty.  Při přidání více tenanty, databáze škálovat s více prostředky úložiště a výpočetního výkonu.  Toto vertikálního navýšení může být vše, co je potřeba, i když je vždy omezený ultimate škálování.  Dlouho před dosažení tohoto limitu databáze však nepraktický ke správě.

Operace správy, které jsou zaměřené na jednotlivých tenantů jsou mnohem složitější a implementovat v databázi s více tenanty.  A ve velkém měřítku může být příliš pomalé těchto operací.  Jedním z příkladů je obnovení bodu v čase data pro právě jednoho tenanta.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Aplikace s více tenanty s horizontálně dělené databáze s více tenanty

Většina aplikací SaaS přístup k datům jenom k jednomu tenantovi v čase.  Tento vzor přístupu umožňuje tenanta dat distribuovat napříč více databází nebo horizontální oddíly, ve kterém všechna data pro každý tenant nachází v jednom horizontálním oddílu.  V kombinaci se vzorem databázi s více tenanty, horizontálně dělené model umožňuje téměř neomezeným Škálováním.

![Návrh aplikace s více tenanty s horizontálně dělené databáze s více tenanty.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Správa horizontálních oddílů

Horizontální dělení zvyšuje složitost návrhu i provozní správu.  Katalog je potřeba ve kterém chcete udržovat mapování mezi tenanty a databází.  Kromě toho postupy správy vyžadovaných ke správě horizontální oddíly a počtu obyvatel tenanta.  Například postupy musí být navržen tak, přidávat a odebírat horizontální oddíly a přesunout tenanta dat mezi horizontálními oddíly.  Jedním ze způsobů škálování je přidáním nových horizontálních oddílů a naplnění nových tenantů.  V jinou dobu může být početnými horizontálních oddílů rozdělit na dvě menší-početnými horizontálních oddílů.  Po několik tenantů byly přesunuty nebo ukončena, vám může sloučit řídce naplněných horizontálních oddílů.  Výsledkem sloučení, budou další využití prostředků nákladově efektivní.  Tenanti můžou také přesunout mezi horizontálními oddíly můžete vyrovnávat zatížení.

SQL Database poskytuje dělení a slučování nástroj, který funguje ve spojení s knihovnou horizontálního dělení a databáze katalogu.  Zadaná aplikace můžete rozdělit a sloučení horizontálních oddílech a můžete přesunout data tenanta mezi horizontálními oddíly.  Aplikace také udržuje pro postižené katalogu během těchto operací označení klientů jako offline před jejich přesunutí.  Po přesunutí aplikace aktualizuje katalog znovu nové mapování a označení klientů jako zpátky do online režimu.

#### <a name="smaller-databases-more-easily-managed"></a>Další menších databází snadno spravovat

Díky distribuci ve více databázích tenantů, výsledkem horizontálně dělené víceklientské řešení menších databází, které se snadněji spravují.  Například obnovení konkrétního tenanta do předchozího bodu v čase nyní zahrnuje obnovení jeden menší databáze ze zálohy, nikoli větší databáze, která obsahuje všechny tenanty. Velikost databáze a počet klientů na databázi, je možné vybrat k vyrovnávání zatížení a činnosti správy.

#### <a name="tenant-identifier-in-the-schema"></a>Identifikátor tenanta ve schématu

V závislosti na použitý přístup horizontálního dělení je potřeba zavést další omezení na schéma databáze.  Aplikace dělení a slučování SQL Database vyžaduje, že schéma obsahuje klíč horizontálního dělení, což je obvykle identifikátor tenanta.  Identifikátor tenanta je přední prvku v primárním klíči všechny horizontálně dělené tabulky.  Identifikátor tenanta umožní aplikaci dělení a slučování rychle najít a přesunout data přidružená k konkrétního tenanta.

#### <a name="elastic-pool-for-shards"></a>Elastický fond pro horizontálních oddílů

Horizontálně dělené databáze pro více tenantů mohou být umístěny v elastických fondech.  Obecně platí s mnoha jednoho tenanta databází ve fondu je jako nákladově efektivnější tak, že má velký počet klientů v několika databázích více tenantů.  Víceklientská databáze jsou výhodné, pokud existuje velký počet relativně neaktivních klientů.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hybridního modelu horizontálně dělené databázi s více tenanty

Všechny databáze v modelu hybridního mít identifikátor tenanta v jejich schématu.  Databáze podporují všechny ukládání více než jednoho tenanta, a může být horizontálně dělené databáze.  Takže ve smyslu schématu jsou všechny databáze s více tenanty.  Ještě v praxi některé z těchto databází obsahují jenom k jednomu tenantovi.  Bez ohledu na to množství uložených v dané databázi tenantů nemá žádný vliv na schéma databáze.

#### <a name="move-tenants-around"></a>Pohyb tenantů

V okamžiku můžete přesunout konkrétního tenanta pro svou vlastní databázi s více tenanty.  A kdykoli, můžete si to rozmyslet a tenanta přejděte zpět do databáze, která obsahuje více tenantů.  Můžete také přiřadit tenanta do nové databáze s jedním tenantem, když si zřídíte nová databáze.

Hybridního modelu vynikne, když existují velké rozdíly mezi potřebné prostředky údaje skupin klientů.  Předpokládejme například, že tenantů účasti v bezplatné zkušební verze není zaručeno stejnou vysokou úroveň výkonu, které jsou odběratelská tenantů.  Zásada může být pro klienty v bezplatné zkušební fáze má být uložen v databázi více tenantů, jež jsou sdílena mezi bezplatnou zkušební tenanti.  Pokud bezplatné zkušební verzi klienta přihlásí na úrovni služby basic, tenanta lze přesunout do jiné databázi s více tenanty, který může mít méně tenantů.  Odběratel, který platí pro úrovně služeb premium nich nedají přesunout na své vlastní nové databáze s jedním tenantem.

#### <a name="pools"></a>Fondy

V tomto modelu hybridního databáze jednoho tenanta pro předplatitele tenanti mohou být umístěny ve fondech zdrojů ke snížení nákladů na databáze na tenanta.  Tím se taky dělá v modelu databáze na tenanta.

## <a name="i-tenancy-models-compared"></a>I. Porovnání modelů tenantů

Následující tabulka shrnuje rozdíly mezi modely hlavních tenantů.

| Měření | Samostatná aplikace | Databáze na tenanta | Horizontálně dělené více tenantů |
| :---------- | :------------- | :------------------ | :------------------- |
| Měřítko | Střednědobé používání<br />1-100s | Velmi vysoké<br />1-100,000s | Unlimited<br />1-1,000,000s |
| Izolaci klientů | Velmi vysoké | Vysoký | Nízká; s výjimkou (který je pouze ve službě db MT) jednoho tenanta. |
| Poplatky za databázi za tenanta | Vysoká; je velikost špičky. | Nízká; fondy použít. | Nejnižší pro malé tenanty v MT databází. |
| Monitorování a správa výkonu | Na tenanta pouze | Agregace a na tenanta | Agregovat; i když je na tenanta jenom pro určené. |
| Vývoj složitost | Nízká | Nízká | Střední; z důvodu horizontálního dělení. |
| Provozní složitost | Low-High. Jednotlivě jednoduché a komplexní ve velkém měřítku. | S nízkou až střední. Vzory adres složitost ve velkém měřítku. | Low-High. Správa jednoho klienta je složité. |
| &nbsp; ||||

## <a name="next-steps"></a>Další postup

- [Nasazení a zkoumání aplikace Wingtip s více tenanty, který používá model SaaS databáze na tenanta – Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Vítá vás aplikace Wingtip Tickets ukázkové SaaS Azure SQL Database tenantů][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Návrh samostatné aplikace s využitím přesně jeden databáze s jedním tenantem."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Návrh aplikace s více tenanty s databází na tenanta."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Návrh aplikace s více tenanty s databází na tenanta, použití elastického fondu."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Návrh aplikace s více tenanty s horizontálně dělené databáze s více tenanty."

