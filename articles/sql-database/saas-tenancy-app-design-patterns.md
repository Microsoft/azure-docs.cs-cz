---
title: Víceklientské vzory SaaS
description: Seznamte se s požadavky a běžnými vzory datové architektury databázových aplikací s více klienty jako služby (SaaS), které běží v cloudovém prostředí Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 01/25/2019
ms.custom: seoapril2019
ms.openlocfilehash: 956d74467c69d9924d26f9cae8d902a6ddd84496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067488"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Víceklientské vzory pronájmu databáze SaaS

Tento článek popisuje různé modely nájmu, které jsou k dispozici pro víceklientské aplikace SaaS.

Při navrhování víceklientské aplikace SaaS je nutné pečlivě zvolit model nájmu, který nejlépe vyhovuje potřebám vaší aplikace.  Model klienta určuje, jak jsou data každého klienta mapována do úložiště.  Váš výběr nájemního modelu má vliv na návrh a správu aplikací.  Přechod na jiný model později je někdy nákladné.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS pojmy a terminologie

V modelu Software as a Service (SaaS) vaše společnost neprodává *licence* k vašemu softwaru. Místo toho každý zákazník provádí platby nájemného vaší společnosti, takže každý zákazník je *nájemcem* vaší společnosti.

Výměnou za placení nájemného získá každý klient přístup k vašim součástem aplikace SaaS a má svá data uložená v systému SaaS.

Termín *nájemní model* odkazuje na způsob uspořádání uložených dat klientů:

- *Jednoklient:* &nbsp; Každá databáze ukládá data pouze z jednoho klienta.
- *Víceklientské:* &nbsp; Každá databáze ukládá data z více samostatných klientů (s mechanismy pro ochranu osobních údajů).
- K dispozici jsou také modely hybridního pronájmu.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Jak si vybrat vhodný model nájmu

Obecně platí, že model nájmu nemá vliv na funkci aplikace, ale pravděpodobně ovlivňuje další aspekty celkového řešení.  Pro posouzení každého z modelů se používají tato kritéria:

- **Škálovatelnost:**
    - Počet nájemníků.
    - Úložiště na tenanta.
    - Skladování v souhrnu.
    - Pracovního vytížení.

- **Izolace tenanta:** &nbsp; Izolace dat a výkon (ať už úloha jednoho klienta ovlivňuje ostatní).

- **Náklady na klienta:** &nbsp; Náklady na databázi.

- **Složitost vývoje:**
    - Změny schématu.
    - Změny dotazů (vyžadované vzorem).

- **Provozní složitost:**
    - Monitorování a správa výkonu.
    - Správa schématu.
    - Obnovení klienta.
    - Zotavení po havárii.

- **Přizpůsobitelnost:** &nbsp; Usnadnění podpory přizpůsobení schématu, které jsou specifické pro klienta nebo specifické pro třídu klienta.

Diskuse o nájmu je zaměřena na *datovou* vrstvu.  Ale zvažte na chvíli *aplikační* vrstvu.  Aplikační vrstva je považována za monolitickou entitu.  Pokud aplikaci rozdělíte na mnoho malých součástí, může se změnit váš výběr modelu nájmu.  S některými součástmi byste mohli zacházet jinak než s jinými, pokud jde o nájem i použitou technologii úložiště nebo platformu.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Samostatná aplikace s jedním klientem s databází s jedním klientem

#### <a name="application-level-isolation"></a>Izolace na úrovni aplikace

V tomto modelu je opakovaně nainstalována celá aplikace, jednou pro každého klienta.  Každá instance aplikace je samostatná instance, takže nikdy nespolupracuje s žádnou jinou samostatnou instancí.  Každá instance aplikace má pouze jednoho klienta, a proto potřebuje pouze jednu databázi.  Tenant má databázi pro sebe.

![Návrh samostatné aplikace s přesně jednou databází jednoho klienta.][image-standalone-app-st-db-111a]

Každá instance aplikace se nainstaluje do samostatné skupiny prostředků Azure.  Skupina prostředků může patřit do předplatného, které je vlastněno dodavatelem softwaru nebo tenantem.  V obou případech může dodavatel spravovat software pro klienta.  Každá instance aplikace je nakonfigurována pro připojení k odpovídající databázi.

Každá databáze klienta se nasadí jako jedna databáze.  Tento model poskytuje největší izolaci databáze.  Ale izolace vyžaduje, aby byly přiděleny dostatečné prostředky pro každou databázi pro zpracování jeho zatížení ve špičce.  Zde záleží na tom, že elastické fondy nelze použít pro databáze nasazené v různých skupinách prostředků nebo do různých předplatných.  Toto omezení umožňuje tento samostatný model aplikace s jedním tenantem nejdražší řešení z hlediska celkových nákladů databáze.

#### <a name="vendor-management"></a>Správa dodavatelů

Dodavatel může přistupovat ke všem databázím ve všech instancích samostatné aplikace, i když jsou instance aplikace nainstalované v různých předplatných tenanta.  Přístup je dosaženo prostřednictvím připojení SQL.  Tento přístup mezi instancemi může umožnit dodavateli centralizovat správu schématu a dotaz mezi databázemi pro účely vytváření sestav nebo analýzy.  Pokud je tento druh centralizované správy žádoucí, musí být nasazen katalog, který mapuje identifikátory klienta na identifikátory databáze URI.  Azure SQL Database poskytuje knihovnu s ráždí, která se používá společně s databází SQL k poskytnutí katalogu.  Knihovna srážlivosti je formálně pojmenována [Klientská knihovna elastické databáze][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Víceklientské aplikace s databází na klienta

Tento další vzor používá víceklientské aplikace s mnoha databázemi, všechny jsou databáze jednoho klienta.  Pro každého nového klienta je zřízena nová databáze.  Aplikační vrstva je vertikálně *škálovat* přidáním dalších prostředků na uzel.  Nebo se aplikace *škáluje* vodorovně přidáním dalších uzlů.  Škálování je založena na zatížení a je nezávislá na počtu nebo škálování jednotlivých databází.

![Návrh víceklientské aplikace s databází na tenanta.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Přizpůsobení pro tenanta

Stejně jako vzor samostatné aplikace, použití databází jednoho tenanta poskytuje silné izolace tenanta.  V libovolné aplikaci, jejíž model určuje pouze databáze jednoho klienta, lze schéma pro libovolnou databázi přizpůsobit a optimalizovat pro svého klienta.  Toto přizpůsobení nemá vliv na ostatní klienty v aplikaci. Možná, že klient může potřebovat data nad rámec základních datových polí, které potřebují všichni klienti.  Další pole extra může potřebovat index.

S databáze na tenanta, přizpůsobení schématu pro jednoho nebo více jednotlivých klientů je snadné dosáhnout.  Dodavatel aplikace musí navrhnout postupy pro pečlivě spravovat vlastní nastavení schématu ve velkém měřítku.

#### <a name="elastic-pools"></a>Elastické fondy

Když jsou databáze nasazeny ve stejné skupině prostředků, mohou být seskupeny do elastických fondů.  Fondy poskytují nákladově efektivní způsob sdílení prostředků v mnoha databázích.  Tato možnost fondu je levnější než vyžadování, aby každá databáze byla dostatečně velká, aby vyhovovala špičkám využití, které zažívá.  I v případě, že sdružené databáze sdílejí přístup k prostředkům, které mohou stále dosáhnout vysokého stupně izolace výkonu.

![Návrh víceklientské aplikace s databází na tenanta pomocí elastického fondu.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database poskytuje nástroje potřebné ke konfiguraci, monitorování a správě sdílení.  Metriky výkonu na úrovni fondu i databáze jsou dostupné na webu Azure Portal a prostřednictvím protokolů Azure Monitoru.  Metriky mohou poskytnout skvělé přehledy o agregovaném výkonu i výkonu specifickém pro klienta.  Jednotlivé databáze lze přesouvat mezi fondy a poskytovat rezervované prostředky konkrétnímu tenantovi.  Tyto nástroje umožňují zajistit dobrý výkon nákladově efektivním způsobem.

#### <a name="operations-scale-for-database-per-tenant"></a>Provozní škálování pro databázi na klienta

Platforma Azure SQL Database má mnoho funkcí pro správu navržených pro správu velkého počtu databází ve velkém měřítku, například více než 100 000 databází.  Tyto funkce, aby databáze na tenanta vzor věrohodné.

Předpokládejme například, že systém má databázi 1000 tenantů jako jedinou databázi.  Databáze může mít 20 indexů.  Pokud systém převede na 1000 databází jednoho klienta, množství indexů se zvýší na 20 000.  V databázi SQL jako součást [automatického ladění][docu-sql-db-automatic-tuning-771a]jsou funkce automatického indexování ve výchozím nastavení povoleny.  Automatické indexování spravuje pro vás všech 20 000 indexů a jejich průběžné optimalizace vytváření a přetažení.  Tyto automatizované akce se vyskytují v rámci jednotlivé databáze a nejsou koordinovány nebo omezeny podobnými akcemi v jiných databázích.  Automatické indexování zachází indexy odlišně v obsazené databázi než v méně zaneprázdněné databázi.  Tento typ přizpůsobení správy indexu by bylo nepraktické v databázi na tenanta škálování, pokud tento obrovský úkol správy musel být provedeno ručně.

Mezi další funkce správy, které dobře škálují, patří následující:

- Vestavěné zálohy.
- Vysoká dostupnost
- Šifrování na disku.
- Telemetrie výkonu.

#### <a name="automation"></a>Automatizace

Operace správy lze skriptovat a nabídnout prostřednictvím modelu [devops.][http-visual-studio-devops-485m]  Operace mohou být dokonce automatizovány a vystaveny v aplikaci.

Můžete například automatizovat obnovení jednoho klienta do dřívějšího bodu v čase.  Obnovení pouze potřebuje k obnovení databáze jednoho klienta, který ukládá klienta.  Toto obnovení nemá žádný vliv na ostatní klienty, což potvrzuje, že operace správy jsou na jemně podrobné úrovni každého jednotlivého klienta.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Aplikace pro více klientů s víceklientské databáze

Dalším dostupným vzorem je uložení mnoha klientů v databázi s více tenanty.  Instance aplikace může mít libovolný počet databází s více klienty.  Schéma databáze s více klienty musí mít jeden nebo více sloupců identifikátoru klienta, aby bylo možné selektivně načíst data z libovolného klienta.  Schéma může dále vyžadovat několik tabulek nebo sloupců, které používají pouze podmnožina klientů.  Statický kód a referenční data jsou však uloženy pouze jednou a jsou sdíleny všemi klienty.

#### <a name="tenant-isolation-is-sacrificed"></a>Izolace nájemníka je obětována

*Data:* &nbsp; Víceklientská databáze nutně obětuje izolaci klienta.  Data více klientů je uložena společně v jedné databázi.  Během vývoje zajistěte, aby dotazy nikdy nezpřístupní data z více než jednoho klienta.  SQL Database podporuje [zabezpečení na úrovni řádků][docu-sql-svr-db-row-level-security-947w], které může vynutit, že data vrácená z dotazu být vymezeny na jednoho klienta.

*Zpracování:* &nbsp; Víceklientská databáze sdílí výpočetní prostředky a prostředky úložiště napříč všemi jeho tenanty.  Databázi jako celek lze sledovat, aby bylo zajištěno, že funguje přijatelně.  Systém Azure však nemá žádný předdefinovaný způsob, jak monitorovat nebo spravovat použití těchto prostředků jednotlivými tenanty.  Proto víceklientské databáze nese zvýšené riziko výskytu hlučné sousedy, kde zatížení jednoho hyperaktivního klienta ovlivňuje zkušenosti s výkonem jiných klientů ve stejné databázi.  Další monitorování na úrovni aplikace může sledovat výkon na úrovni klienta.

#### <a name="lower-cost"></a>Nižší náklady

Obecně platí, že databáze s více tenanty mají nejnižší náklady na klienta.  Náklady na prostředky pro jednu databázi jsou nižší než pro ekvivalentní velikosti elastického fondu.  Kromě toho pro scénáře, kde klienti potřebují pouze omezené úložiště, potenciálně miliony klientů mohou být uloženy v jedné databázi.  Žádný elastický fond může obsahovat miliony databází.  Řešení obsahující 1000 databází na fond s 1000 fondy by však mohlo dosáhnout rozsahu milionů na riziko, že se stane těžkopádným.

Dvě varianty víceklientského databázového modelu jsou popsány v následujícím, přičemž horizontální ho víceklientský model je nejflexibilnější a škálovatelný.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Aplikace pro více klientů s jednou databází s více klienty

Nejjednodušší víceklientský databázový vzor používá jednu databázi k hostování dat pro všechny klienty.  Jako další klienty jsou přidány, databáze je navertteničně navertovat s více prostředků úložiště a výpočetní prostředky.  Toto škálování může být vše, co je potřeba, i když je vždy konečný limit měřítka.  Však dlouho před dosažením tohoto limitu databáze stane těžkopádný spravovat.

Operace správy, které jsou zaměřeny na jednotlivé klienty jsou složitější implementovat v databázi s více klienty.  A ve velkém měřítku by se tyto operace mohly stát nepřijatelně pomalými.  Jedním z příkladů je obnovení dat v čase pro jednoho klienta.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Aplikace s více klienty s databázemi s více klienty s oddíly

Většina aplikací SaaS přístup k datům pouze jednoho klienta najednou.  Tento přístup vzor umožňuje data klienta distribuovat mezi více databází nebo střepů, kde všechna data pro jednoho klienta je obsažena v jednom střepu.  V kombinaci se vzorem databáze s více tenanty umožňuje horizontálně dělený model téměř neomezené škálování.

![Návrh víceklientské aplikace s vrstvenými databázemi s více klienty.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Správa úlomků

Sharding zvyšuje složitost návrhu i provozní správy.  Je vyžadován katalog, ve kterém chcete udržovat mapování mezi klienty a databázemi.  Kromě toho jsou nutné postupy správy pro správu střepů a populace klienta.  Například postupy musí být navrženy pro přidání a odebrání štrůdků a přesunout data klienta mezi oddíly štrůdků.  Jedním ze způsobů škálování je přidáním nového horizontálního oddílu a jeho vyplněním novými tenanty.  Jindy můžete rozdělit hustě obydlený oddíl oddílu na dva méně hustě osídlené oddíly.  Po několika tenantů byly přesunuty nebo ukončeny, můžete sloučit řídce osídlené oddíly společně.  Sloučení by mělo za následek nákladově efektivnější využití prostředků.  Klienti mohou být také přesunuty mezi úlomky k vyrovnání úloh.

SQL Database poskytuje nástroj rozdělení/sloučení, který pracuje ve spojení s knihovnou srážlivých prvků a databázi katalogu.  Poskytnutá aplikace můžete rozdělit a sloučit úlomky a můžete přesunout data klienta mezi oddíly.  Aplikace také udržuje katalog během těchto operací, označení postižených klientů jako offline před jejich přesunutím.  Po přesunutí aplikace aktualizuje katalog znovu s novým mapováním a označení klienta jako zpět online.

#### <a name="smaller-databases-more-easily-managed"></a>Menší databáze snadněji spravovatelné

Distribucí klientů napříč více databázemi, rozdělení řešení s více klienty má za následek menší databáze, které jsou snadněji spravovatelné.  Například obnovení konkrétního klienta do předchozího bodu v čase nyní zahrnuje obnovení jedné menší databáze ze zálohy, nikoli větší databáze, která obsahuje všechny klienty. Velikost databáze a počet klientů na databázi, lze zvolit k vyrovnání pracovního vytížení a úsilí o správu.

#### <a name="tenant-identifier-in-the-schema"></a>Identifikátor klienta ve schématu

V závislosti na přístupu k řícení použít další omezení mohou být uloženy na schéma databáze.  Aplikace sql database rozdělení a sloučení vyžaduje, aby schéma obsahuje klíč rozdělení, který je obvykle identifikátor klienta.  Identifikátor klienta je úvodní prvek v primárním klíči všech oddílových tabulek.  Identifikátor klienta umožňuje aplikaci rozdělení/sloučení rychle vyhledat a přesunout data přidružená k určitému klientovi.

#### <a name="elastic-pool-for-shards"></a>Elastický bazén pro úlomky

Vrstvené víceklientské databáze lze umístit do elastických fondů.  Obecně platí, že mít mnoho databází s jedním tenantem ve fondu je stejně efektivní jako mít mnoho klientů v několika databázích s více klienty.  Víceklientské databáze jsou výhodné, pokud existuje velký počet relativně neaktivních klientů.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hybridní oborový model databáze s více klienty

V hybridním modelu mají všechny databáze identifikátor klienta ve schématu.  Databáze jsou všechny schopné ukládání více než jednoho klienta a databáze mohou být oddíly.  Takže ve smyslu schématu jsou všechny víceklientské databáze.  Přesto v praxi některé z těchto databází obsahují pouze jednoho klienta.  Bez ohledu na to, množství klientů uložených v dané databázi nemá žádný vliv na schéma databáze.

#### <a name="move-tenants-around"></a>Přesun klientů

Kdykoli můžete přesunout konkrétního klienta do jeho vlastní databáze s více tenanty.  A kdykoli můžete změnit názor a přesunout klienta zpět do databáze, která obsahuje více klientů.  Můžete také přiřadit klienta k nové databázi jednoho klienta při zřizování nové databáze.

Hybridní model svítí, když existují velké rozdíly mezi potřebami prostředků identifikovatelných skupin klientů.  Předpokládejme například, že klienti, kteří se účastní bezplatné zkušební verze, nemají zaručenou stejně vysokou úroveň výkonu jako přihlášení k odběru klientů.  Zásada může být pro klienty ve fázi bezplatné zkušební verze, které mají být uloženy v databázi více klientů, která je sdílena mezi všemi klienty zkušební verze.  Když se bezplatný zkušební klient přihlásí k odběru základní úrovně služeb, klienta můžete přesunout do jiné databáze více klientů, která může mít méně klientů.  Odběratel, který platí za úroveň služby premium, může být přesunut do vlastní nové databáze s jedním tenantem.

#### <a name="pools"></a>Fondy

V tomto hybridním modelu mohou být databáze s jedním klientem pro klienty odběratelů umístěny do fondů prostředků, aby se snížily náklady na databázi na klienta.  To se také provádí v modelu databáze na tenanta.

## <a name="i-tenancy-models-compared"></a>I. Nájemní modely ve srovnání

Následující tabulka shrnuje rozdíly mezi hlavními modely nájmu.

| Měření | Samostatná aplikace | Databáze na klienta | Víceklient s pevnými vrstvami |
| :---------- | :------------- | :------------------ | :------------------- |
| Škálování | Střednědobé používání<br />1-100s | Velmi vysoká<br />1-100 000s | Unlimited<br />1-1 000 000 |
| Izolace tenanta | Velmi vysoká | Vysoká | Nízká; s výjimkou jednoho klienta (který je sám v MT db). |
| Náklady na databázi na klienta | Vysoká; je dimenzována pro špičky. | Nízká; použitých bazénů. | Nejnižší, pro malé nájemníky v MT DBs. |
| Sledování a řízení výkonu | Pouze pro klienta | Agregovat + na klienta | Agregace; i když je na nájemce pouze pro jednotlivce. |
| Složitost vývoje | Nízká | Nízká | Střední; v důsledku střepů. |
| Provozní složitost | Nízko-vysoko. Individuálně jednoduché, složité ve velkém měřítku. | Nízká-Střední. Vzory řeší složitost ve velkém měřítku. | Nízko-vysoko. Správa jednotlivých klientů je složitá. |
| &nbsp; ||||

## <a name="next-steps"></a>Další kroky

- [Nasazení a prozkoumání víceklientské aplikace Wingtip, která používá model SaaS pro jednotlivé databáze – Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Vítá vás ukázková aplikace pro pronájem sazure azure databáze SaaS Azure][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Návrh samostatné aplikace s přesně jednou databází jednoho klienta."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Návrh víceklientské aplikace s databází na tenanta."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Návrh víceklientské aplikace s databází na tenanta pomocí elastického fondu."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Návrh víceklientské aplikace s vrstvenými databázemi s více klienty."

