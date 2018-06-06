---
title: Víceklientské SaaS vzorky – Azure SQL Database | Microsoft Docs
description: Informace o požadavky a běžně data architektura vzory víceklientské softwaru jako databáze aplikace služby (SaaS), které běží v prostředí cloudu Azure.
keywords: kurz k sql database
services: sql-database
author: billgib
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.reviewer: genemi
ms.author: billgib
ms.openlocfilehash: 39be48019979ceb1337cbd3008c8cf071d403310
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737676"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Víceklientské SaaS databáze klientů vzory

Při navrhování víceklientské aplikace SaaS, je třeba pečlivě zvolit klientů model, který nejlépe vyhovuje potřebám vaší aplikace.  Model klientů Určuje, jak data každého klienta je mapována na úložiště.  Zvoleného klientů modelu ovlivňuje návrh aplikace a správu.  Přepnutí na jiný model později je někdy nákladná.

Tento článek popisuje modely alternativní klientů.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS principy a terminologií

Software jako služba (SaaS) model, v vaší společnosti není prodeje *licence* vašeho softwaru. Místo toho každého zákazníka a to díky pronajímat plateb pro vaši společnost, provedení každého zákazníka *klienta* vaší společnosti.

Po platícího pronájem, každý klient obdrží přístup k součásti vaší aplikace SaaS a má jeho dat uložených v systému SaaS.

Termín *klientů modelu* odkazuje na uspořádání uložených dat klientů:

- *Single klientů:* &nbsp; každou databázi ukládá data od klientů pouze jeden.
- *Víceklientská architektura:* &nbsp; každou databázi ukládá data z více samostatných klientů (s mechanismy k ochraně ochrany osobních údajů).
- Hybridní klientů modely jsou k dispozici.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Jak vybrat odpovídající klientů modelu

Obecně platí klientů model nemá negativní vliv na funkci aplikace, ale je pravděpodobně má dopad na další aspekty celého řešení.  Následující kritéria se používají k vyhodnocení, všechny modely:

- **Škálovatelnost:**
    - Počet klientů.
    - Úložiště za klienta.
    - Úložiště v agregace.
    - Zatížení.

- **Izolaci klientů:** &nbsp; izolaci dat a výkonu (jestli zatížení jednoho klienta má dopad na jiné).

- **Náklady na klienta:** &nbsp; databáze náklady.

- **Vývoj pro složitost:**
    - Změny schématu.
    - Změny dotazy (vyžadováno vzor).

- **Provozní složitost:**
    - Monitorování a správa výkonu.
    - Správa schématu.
    - Obnovení klienta.
    - Zotavení po havárii.

- **Možnosti přizpůsobení:** &nbsp; snadná podpora úpravy schématu, které jsou buď konkrétního klienta nebo klienta konkrétní třídy.

Diskusní klientů se zaměřuje na *data* vrstvy.  Ale zvažte na chvíli *aplikace* vrstvy.  Aplikační vrstvu, je zpracovaná jako monolitický entity.  Pokud budete provádět dělení aplikaci do mnoho malých součástí, může se měnit zvoleného klientů modelu.  Některé součásti může považovat jinak než ostatní týkající se klientů a technologie úložiště nebo platformy, které používá.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Samostatné aplikace jednoho klienta pomocí databáze jednoho klienta

#### <a name="application-level-isolation"></a>Úroveň izolace aplikací

V tomto modelu celou aplikaci se nainstaluje opakovaně, jednou pro každého klienta.  Každá instance aplikace je samostatné instance, takže nikdy komunikuje se službou jiných samostatná instance.  Každou instanci aplikace má pouze jednoho klienta a proto potřebuje pouze jednu databázi.  Klient má databáze na sebe sama.

![Návrh samostatné aplikace pomocí přesně jednu databázi jednoho klienta.][image-standalone-app-st-db-111a]

Každá instance aplikace je nainstalována ve skupině samostatné prostředků Azure.  Skupina prostředků může patřit do odběru, který je vlastněn dodavatele softwaru nebo klienta.  V obou případech mohou spravovat dodavatele softwaru klienta.  Každá instance aplikace je nakonfigurován pro připojení k příslušné databázi.

Každá databáze klienta se nasazuje jako samostatná databáze.  Tento model poskytuje největší izolace databáze.  Ale izolaci vyžaduje, že se pro každou databázi pro zpracování jeho zatížení ve špičce přidělit dostatek prostředků.  Tady důležitý, že elastické fondy nelze použít pro databáze, které jsou nasazeny v různých skupinách prostředků nebo do různých předplatných.  Toto omezení díky této aplikaci jednoho klienta samostatné modelu nejnákladnější řešení z celkové náklady na perspektivy databáze.

#### <a name="vendor-management"></a>Správa dodavatele

Dodavatele přístup všechny databáze v všechny samostatné aplikace instance, i v případě, že instance aplikace jsou nainstalovány v různých klienta předplatných.  Přístup se dosahuje prostřednictvím připojení k SQL.  Tento přístup mezi instance můžete povolit na dodavatele a centralizovat správu schématu a mezidatabázové dotazu pro účely vytváření sestav, nebo analýzy.  V případě potřeby tento druh centralizovanou správu katalog musí být nasazený mapující identifikátory klienta do databáze identifikátory URI.  Databáze SQL Azure poskytuje horizontálního dělení knihovny, která se používá spolu s SQL database k poskytování katalog.  Knihovna horizontálního dělení oficiálně jmenuje [klientské knihovny pro elastické databáze][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Víceklientské aplikace pomocí databáze za klienta

Tento vzor další používá víceklientské aplikace s mnoha databázemi, všechny se databáze jednoho klienta.  Novou databázi se zřizuje pro každého nového klienta.  Aplikační vrstvě je škálovat *až* svisle přidáním více prostředků na každém uzlu.  Nebo je škálovat aplikaci *out* vodorovně přidáním další uzly.  Měřítko je založena na pracovním vytížení a je nezávislý na čísla nebo škálování jednotlivých databází.

![Návrh víceklientské aplikace pomocí databáze za klienta.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Přizpůsobit pro klienta

Vzor samostatné aplikace, jako je použití jednoho klienta databáze poskytuje izolaci silné klientů.  V jakékoli aplikaci, jejíž modelu určuje jen jeden klienta databáze můžete přizpůsobit a optimalizované pro jeho klienta schéma pro všechny jeden danou databázi.  Toto vlastní nastavení nemá vliv na jiných klientů v aplikaci. Možná klienta může být nutné dat nad rámec základní datová pole, které je třeba všechny klienty.  Doplňující data pole navíc může být nutné indexu.

Databáze na klientovi je jednoduchá k dosažení přizpůsobení schématu pro jednoho nebo více jednotlivých klientů.  Postup pečlivě spravovat úpravy schématu ve velkém měřítku musí navrhnout dodavatele aplikace.

#### <a name="elastic-pools"></a>Elastické fondy

Když je databáze nasazená ve stejné skupině prostředků, mohou být seskupeny do fondů elastické databáze.  Fondy umožňují efektivní sdílení prostředků mezi mnoha databázemi.  Tato možnost fondu je levnější než nutnosti každou databázi, aby být dostatečně velký na to, aby dokázala pojmout vrcholů využití, které se vyskytne.  Přestože databáze ve fondu sdílet přístup k prostředkům se stále můžete dosáhnout vysoký stupeň izolaci výkonu.

![Návrh víceklientské aplikace pomocí databáze za klienta, pomocí elastického fondu.][image-mt-app-db-per-tenant-pool-153p]

Databáze SQL Azure poskytuje nástroje, které jsou nezbytné ke konfiguraci, sledování a správě sdílení.  Obě metriky výkonu fondu úroveň a databáze jsou dostupné na portálu Azure a prostřednictvím analýzy protokolů.  Metriky můžete udělit skvělý přehled o výkonu agregační i konkrétního klienta.  Jednotlivé databáze lze přesunout mezi fondy zajistit rezervované prostředky pro konkrétní klienta.  Tyto nástroje umožňují zajistit dobrý výkon nákladově efektivní způsobem.

#### <a name="operations-scale-for-database-per-tenant"></a>Operace škálování databáze za klienta

Platforma Azure SQL Database má mnoho funkcí pro správu navržená ke správě velkého počtu databází ve velkém měřítku, jako jsou třeba i více než 100 000 databáze.  Tyto funkce usnadnění vyhovující vzoru databáze za klienta.

Předpokládejme například, že systém má databáze 1000 klienta jako jeho pouze jednu databázi.  Databáze může mít 20 indexy.  Pokud systém převede na situaci, kdy 1000 jednoho klienta databáze, roste množství indexy na 20 000.  V databázi SQL v rámci [automatické ladění][docu-sql-db-automatic-tuning-771a], ve výchozím nastavení jsou povoleny automatické indexování funkce.  Automatické indexování spravuje za vás všechny 20 000 indexy a probíhající vytvořit a vyřazení optimalizace.  Tyto automatizované akce se provedou v rámci jednotlivých databáze a nejsou koordinované nebo omezené na základě podobné akce v jiných databázích.  Automatické indexování zpracovává indexy odlišně v zaneprázdněn databázi než v databázi méně zaneprázdněn.  Tento typ přizpůsobení správy indexu by nepraktické škálované databáze za klienta, pokud tato úloha obrovské správu měli provést ručně.

Další funkce správy, které škálovat zahrnují následující:

- Integrované zálohování.
- Vysoká dostupnost
- Šifrování na disku.
- Výkonu telemetrie.

#### <a name="automation"></a>Automation

Operace správy mohou být skripty a nabízeným přes [devops] [ http-visual-studio-devops-485m] modelu.  Operace můžete i automatizované a vystavený v aplikaci.

Například může automatizovat obnovení jednoho klienta do dřívějšího bodu v čase.  Obnovení stačí obnovit databázi jeden jednoho klienta, která ukládá klienta.  Toto obnovení nemá žádný vliv na ostatních klientů, který potvrdí, že operace správy jsou podrobně podrobné úrovni jednotlivých jednotlivých klientů.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Víceklientské aplikace s více klienty databáze

Jiné dostupné vzor je uložit velký počet klientů v databázi více klientů.  Instance aplikace může mít libovolný počet databází více klientů.  Schéma databáze víceklientské musí mít jeden nebo více sloupců identifikátor klienta tak, aby se nedají selektivně načíst data ze všech daného klienta.  Další schématu může vyžadovat několik tabulek nebo sloupců, které jsou používány pouze podmnožině klientů.  Statické kódu a referenčních dat však uložen pouze jednou a platí pro všechny klienty.

#### <a name="tenant-isolation-is-sacrificed"></a>Publikovaný izolaci klientů

*Data:* &nbsp; databázi víceklientské nutně upřednostňuje izolaci klientů.  Data více klientů společně uložena v jedné databáze.  Během vývoje Ujistěte se, že dotazy nikdy neměli zveřejňovat data z více než jednoho klienta.  Databáze SQL podporuje [zabezpečení na úrovni řádků][docu-sql-svr-db-row-level-security-947w], které můžete vynutit tato data vrácená z dotazu omezená na jednoho klienta.

*Zpracování:* &nbsp; databázi víceklientské sdílí výpočetní a úložnou kapacitu přes všechny jeho klienty.  K zajištění, že je její výkon přijatelně se dá sledovat databázi jako celek.  Azure systém však má předdefinovaný způsob, jak sledovat nebo spravovat pomocí těchto prostředků pomocí jednoho klienta.  Proto databázi víceklientské představuje větší riziko vzniku aktivní Sousedé BGP, kde zatížení jednoho klienta overactive ovlivňuje výkon ostatních klientů ve stejné databázi.  Další monitorování úrovni aplikace může sledovat výkon úrovni klienta.

#### <a name="lower-cost"></a>Nižší náklady

Obecně platí víceklientské databáze mají nejnižší za klienta náklady.  Prostředek náklady na databázi samostatné jsou nižší než ekvivalentně velikosti fondu elastické databáze.  Kromě toho pro scénáře, kde klienti potřebují jenom omezené úložiště, potenciálně miliony klientů, které může být uložený v jedné databáze.  Žádná elastický fond může obsahovat miliony databáze.  Řešení obsahující 1000 databází na každý fond s 1 000 fondy, však může dosáhnout rozsahu miliony jeho riziko vzniku nepraktické ke správě.

Dvě varianty modelu víceklientské databáze jsou popsané v následující, s horizontálně dělené víceklientského modelu je flexibilní a škálovatelné.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Víceklientské aplikace pomocí jedné databáze více klientů

Nejjednodušší vzor víceklientské databáze používá jeden samostatná databáze k hostování dat. pro všechny klienty.  Při přidávání více klientů, databázi škálovat s další úložiště a výpočetní prostředky.  I když vždy existuje maximální ultimate škálování všechno, co je potřeba, může být tento škálování nahoru.  Ale dlouho předtím, než je dosaženo tohoto limitu databáze se stane nepraktické ke správě.

Operace správy, které jsou zaměřené na jednotlivé klienty jsou složitější implementovat v databázi více klientů.  A škálované mohou být tyto operace příliš pomalé.  Jedním z příkladů je v okamžiku obnovení dat pro jedním klienta.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Víceklientské aplikace s horizontálně dělené víceklientské databáze

Většina aplikací SaaS přístup k datům jenom jeden klienta najednou.  Tento vzor přístupu umožňuje dat klienta být distribuován do více databází nebo horizontálních oddílů, kde všechna data pro některého klienta se nachází v jedné horizontálního oddílu.  V kombinaci s víceklientské databáze vzor, horizontálně dělené model umožňuje téměř neomezenou škálování.

![Návrh aplikace víceklientské s horizontálně dělené víceklientské databáze.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Spravovat horizontálních oddílů

Horizontálního dělení přidá složitost jak k návrhu a provozu správy.  Katalog je nutný, ve kterém se má zachovat mapování mezi klienty a databáze.  Kromě toho postupy správy vyžadovaných ke správě horizontálních oddílů a naplnění klienta.  Například postupy musí být navrženy k přidání a odebrání horizontálních oddílů a pro přesun dat klienta mezi horizontálních oddílů.  Jedním ze způsobů škálování je přidáním nové horizontálního oddílu a naplňování s nové klienty.  V jinou dobu může rozdělit početnými horizontálního oddílu na dva méně-početnými horizontálních oddílů.  Po několika klienty přesunuta případně zastaveny, vám může sloučit řídce vyplněná horizontálních oddílů.  Sloučení by způsobilo další využití cenově efektivní prostředků.  Klienti mohou také přesouvat mezi horizontálních oddílů můžete vyrovnávat zatížení.

SQL Database nabízí rozdělení či sloučení nástroj, který funguje ve spojení s knihovně horizontálního dělení a databáze katalogu.  Zadaná aplikace můžete rozdělit a horizontálních oddílů sloučení ale můžete přesouvat data klienta mezi horizontálních oddílů.  Aplikace také udržuje ovlivněn katalogu během těchto operací, označení klientů jako offline před jejich přesunutí.  Po přesunutí nastavení aplikace aktualizace katalogu znovu s novou mapování a označení klientů jako zpět do režimu online.

#### <a name="smaller-databases-more-easily-managed"></a>Další menší databáze snadno spravovat

Distribucí klientů napříč více databází, výsledkem horizontálně dělené víceklientské řešení menší databáze, které se snadněji spravují.  Například obnovení konkrétní klienta do předchozího bodu v čase nyní zahrnuje obnovení jedné menší databáze ze zálohy, nikoli větší databázi, která obsahuje všechny klienty. Velikost databáze a počtu klientů na databázi, je možné vybrat k vyrovnávání zatížení a správu úsilí.

#### <a name="tenant-identifier-in-the-schema"></a>Identifikátor klienta ve schématu.

V závislosti na horizontálního dělení přístupu, používají mohou být uloženy další omezení schématu databáze.  Databáze SQL rozdělení či sloučení aplikace vyžaduje, aby schéma obsahuje horizontálního dělení klíč, který je obvykle identifikátor klienta.  Identifikátor klienta je přední element v primárním klíči všechny horizontálně dělené tabulky.  Identifikátor klienta umožňuje aplikaci rozdělit či sloučení rychle najít a přesouvat data související s konkrétní klienta.

#### <a name="elastic-pool-for-shards"></a>Elastický fond pro horizontálních oddílů

Horizontálně dělené víceklientské databáze mohou být umístěny v elastické fondy.  Obecně platí s mnoha databázemi jednoho klienta ve fondu je jako efektivní tak, že má velký počet klientů v několika databází víceklientské náklady.  Víceklientské databáze jsou výhodné, pokud existuje velký počet klientů, které relativně neaktivní.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Model hybridní horizontálně dělené víceklientské databáze

Všechny databáze v modelu hybridního mít identifikátor klienta v jejich schématu.  Databáze jsou všechny umožňující ukládání více než jednoho klienta a databáze může být horizontálně dělené.  Proto v tom smyslu, schéma, jsou všechny databáze více klientů.  Ještě v praxi, některé z těchto databází obsahovat pouze jeden klienta.  Bez ohledu na to počtu klientů, které ukládají do dané databáze nemá žádný vliv na schéma databáze.

#### <a name="move-tenants-around"></a>Pohyb klientů

Kdykoli můžete přesunout konkrétní klienta svou vlastní databázi více klientů.  A v každém okamžiku může rozmyslíte a přesunout klienta zpět do databáze, která obsahuje několik klientů.  Můžete také přiřadit klienta k nové databázi jednoho klienta při zřizování nové databáze.

Model hybridní září, když jsou velké rozdíly mezi požadavky na prostředky osobní skupin klientů.  Předpokládejme například, že stejnou vysokou úroveň výkonu, které jsou odběru klienty se nezaručuje, klienty účastní bezplatnou zkušební verzi.  Zásady může být pro klienty ve fázi bezplatné zkušební verze se neukládají v databázi více klientů, která je sdílena mezi bezplatné zkušební klienty.  Pokud bezplatnou zkušební verzi klienta přihlásí k úroveň služby na úrovni basic, klient se dají přesunout do jiné databáze více klientů, který může mít méně klientů.  Odběratele, které platí pro úroveň služby premium může přesunout do vlastní novou databázi jednoho klienta.

#### <a name="pools"></a>Fondy

V tomto modelu hybridního databáze jednoho klienta pro klienty odběratele mohou být umístěny ve fondech zdrojů, jak snížit náklady databáze každého klienta.  Tím se taky dělá ve model databáze za klienta.

## <a name="i-tenancy-models-compared"></a>I. Porovnání modelů klientů

Následující tabulka shrnuje rozdíly mezi modely hlavní klientů.

| Měření | Samostatná aplikace | Databáze za klienta | Horizontálně dělené více klientů |
| :---------- | :------------- | :------------------ | :------------------- |
| Měřítko | Střednědobé používání<br />1-100s | Velmi vysoké<br />1-100,000s | Unlimited<br />1-1,000,000s |
| Izolaci klientů | Velmi vysoké | Vysoký | Nízká; s výjimkou žádným singleton klientem (která je samostatně do databáze. MT). |
| Náklady na databázi každého klienta | Vysoká; je dimenzované pro vrcholů. | Nízká; fondy použít. | Nejnižší pro malé klienty ve MT Operations Manager. |
| Sledování výkonu a správy | Za klienta pouze | Agregace + za klienta | Agregace; i když je každý klient jenom pro jednotlivé prvky. |
| Vývoj pro složitost | Nízká | Nízká | Střední; z důvodu horizontálního dělení. |
| Provozní složitost | Nízká vysoká. Jednotlivě jednoduchý, komplexní ve velkém měřítku. | Nízká – střední. Vzory adres složitost ve velkém měřítku. | Nízká vysoká. Správa jednoho klienta je komplexní. |
| &nbsp; ||||

## <a name="next-steps"></a>Další postup

- [Nasazení a prozkoumejte aplikace Wingtip více klientů, která používá model SaaS databáze za klienta – Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Vítá vás adresář Wingtip lístky ukázková SaaS Azure SQL Database klientů aplikace][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Návrh samostatné aplikace pomocí přesně jednu databázi jednoho klienta."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Návrh víceklientské aplikace pomocí databáze za klienta."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Návrh víceklientské aplikace pomocí databáze za klienta, pomocí elastického fondu."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Návrh aplikace víceklientské s horizontálně dělené víceklientské databáze."

