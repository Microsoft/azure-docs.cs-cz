---
title: Azure SQL databáze Azure Případová studie - Snelstart | Microsoft Docs
description: Další informace o tom, jak SnelStart používá SQL Database k rychle rozšířit své obchodní služby ve výši 1 000 nové databáze SQL Azure, za měsíc
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 55734dc0889ae6d1b03fab073586fa2fab32ec3c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>S Azure se rozšířila SnelStart rychle jeho firemní služby ve výši 1 000 nové databáze SQL Azure, za měsíc
![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart vytváří v Nizozemsko oblíbených finanční a obchodní-software pro správu pro malé a střední firmy (SMB). Jeho 55,000 zákazníci jsou obsluhovány pomocí pracovníci 110 zaměstnanců, včetně vlastní IT oddělení 35. Přesunutím z desktopového softwaru do nabídku softwaru jako služba (SaaS), který je založený na Azure SnelStart provedené nejvíce z vestavěné služby automatizaci správy pomocí známém prostředí v jazyce C# a optimalizace výkonu a škálovatelnosti podle ani přes - nebo v části zřizování firmy používající elastické fondy. Použití Azure poskytuje SnelStart 10násobnou přesunutí zákazníků mezi místními a cloudem.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-SnelStart/player]
> 
> 

## <a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Proč SnelStart rozšířené služby z plochy do cloudu
> "Práce s Azure rozumí jsme může poskytovat rychlejší softwaru, rychle reagovat na požadavky zákazníků a škálování řešení při zvýšit nároky."
> 
> – Henry byla architekti softwaru
> 
> 

SnelStart spustili úspěšné softwaru obchodní let, pomocí modelu tradiční vývoj: kód, balíčků, odeslání a opakujte. V průběhu času na rychlost změn vzrostla rychlejší a rychleji. Předpisy často mění a zákazníci potřebné jednodušší způsoby, jak zpracovat finanční záznamy a spolupracovat s jejich účetní a government udržovat tempo s těmito změnami.

"Přesouvání softwaru zákazníkům je nákladné a omezení," podle Henry byla architekti softwaru. "Výroby, balení a nákladů přesouvání omezené jsme jak často zveřejnit software. Jsme museli balíček aktualizací pro pravidelné dodávky, ale který provedené těžko podle měnících se potřeb našich zákazníků. Jsme může nikdy být jisti, že naše zákazníky upgradovat na nejnovější verzi produktu. Proto jsme měli pro podporu více verzí, provedení úlohy podpora velmi obtížné i."

Byla přidá, "jsme chtěli způsob, jak program a verze aktualizace v Zrychlený rychle, proto jsme může inovacemi. Zajistěte rychlejší a vytvoření nové služby pro naše zákazníky. Můžeme také chtěli způsob, jak automatizovat další procesy za účelem zjednodušení potřebám firmy správy našich zákazníků."

Pro SnelStart bylo rozšířit jeho služby stal SaaS poskytovatele cloudové řešení. Platformou Azure SQL Database pomohl SnelStart tam dostat, aniž by docházelo k hlavní režii IT oddělení, které by vyžadovaly do řešení pro infrastruktury jako služba (IaaS).

Model cloudu taky umožňuje SnelStart opravit chyby, a zadejte nové funkce rychle, bez zákazníci museli stáhnout a aktualizace softwaru. Podle byla "pomocí cloudových služeb Azure umožňuje nám rychle fungovat po změně požadavků od jiných výrobců. Místo nutnosti dodávat novou verzi k tisícům zákazníků, jsme můžete přizpůsobit informace odesílané ze naše desktopová aplikace do nové formátů vyžadují třetí strany."

## <a name="a-modern-company-with-traditional-roots"></a>Moderní společnost s tradiční kořenových certifikačních autorit
SnelStart je moderní, agilní, špičkovými obchodní s humble kořeny počítáno na 1964, při spuštění umístit společnosti jako výrobce MIDI částí. Vysílat firmy softwaru SnelStart skutečně spuštěna signálu v 1980s, během rozšiřování osobní počítače. Společnost potřebné lepší alternativa k monitorování účtů software, který je k dispozici v době, takže trvalo věci do vlastní rukou. V 1982 společnost vytvoří základ pro co by přestat SnelStart monitorování účtů softwaru. Od začátku, se pro jednoduchost a rychlost admired software, takže většina tak, aby společnost nakonec změnit fokus a reinvented samotné do softwarové společnosti.

V roce 1995 vydala SnelStart její první účetnictví aplikací pro Windows. Aplikace založená na 1.0 Microsoft Visual Basic a Microsoft Access databáze pomohl růst zákazník základní uživatelům více než 5 000.

V současné době SnelStart je hlavní zprostředkovatel-obchodní (LOB) a správa obchodních aplikací zaměřený na Holandská SMB a nezávislém vedoucími podniků. Jak uvádí Carlo Kuip, IT Architekti, "Naším cílem je zajistit 100 procent automatizace obchodní správy služeb pro naše zákazníky."

## <a name="optimizing-performance-and-cost-with-elastic-pools"></a>Optimalizace výkonu a nákladů s elastické fondy
SnelStart byl ve velkém měřítku včasným elastické fondy. Elastické fondy pomáhají společnosti omezit náklady a efektivněji spravovat požadavky na výkon. Podle byla "pomocí elastické fondy jsme můžete optimalizovat výkon podle potřeb našich zákazníků bez předimenzování. Pokud jsme měl zřídit podle zatížení ve špičce, by bylo velmi nákladné. Místo toho možnost sdílení prostředků mezi několika databází nízkého umožňuje vytvořit řešení, které provede dobře a je nákladově efektivní. "

## <a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Databáze Azure SQL pomoci containerize data pro izolace a zabezpečení
Databáze SQL Azure umožňuje SnelStart snadno a transparentně přesunout zákazníků místní Správa obchodních dat do Azure. Databáze SQL Azure je vhodné kontejner, který poskytuje izolaci, hranice pro ověřování, autorizaci a snadno funkce zálohování a obnovení. Databáze poskytují vhodným konceptuálního modelu pro správu firmy. Podle Carlo Kuip, IT Architekti, "položky v rámci této hranice kontejneru obsahují citlivá data, která je zásadní obchodní nebo a ukládání těchto položek do izolované databáze zajišťuje jejich dobře chráněný. Můžeme spravovat ověřování na úrovni databáze a i bez nutnosti správce databáze (DBAs) na zaměstnanci automatizace správy a horizontální těchto databází."

Azure SQL Data Warehouse také hrají roli při SnelStart zabezpečení a správu scénář tím, že pomáhá společnosti shromažďuje telemetrická data, jako je například zjišťování neoprávněných vniknutí, protokolování aktivit uživatelů a připojení.

## <a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure odebere režie tak, aby vývojáři mohou věnovat více času doručování hodnota
Model platformy Azure odebrat režijní náklady na infrastrukturu a povolené SnelStart k automatizaci nasazení pomocí správy knihovny jazyka C#. Jako Kuip stavy, "jsme byli schopni růst naše aktuální operace s velmi málo zaměstnanců při současně a zvýšit tak možnosti obnovení po havárii, škálovatelnost a rychlost pro naše klienty. Posunutí pro vývoj služby uvolněna, prostředky a zaměřit se na nové služby a funkce, místo právě aktualizovat existující kód tak, aby se nové předpisy nebo kódy daň." Přidá, "tak, že automatizaci správy a pomocí SaaS nabídky, jsme se můžou poskytovat další hodnoty pro naše klienty bez nutnosti investovat velké v provozní pracovníci." Například pomocí Azure a elastické fondy SnelStart bylo možné přidat celou řadu nových funkcí, včetně integrace robustnější data zákazníků s bankami, nové fakturace služby, kontroly malé firmy a e-mailové služby.

> "V pouze prvních několik měsíců 2016, jsme rozšířit naše nasazení Azure SQL Database z o 5,500 do více než 12 000 a aktuálně je právě přidávána asi 1000 databáze za měsíc."
> 
> – Henry byla architekti softwaru
> 
> 

Správa databáze je další automatizované pomocí funkce elastické úlohy. Jako Kuip stavy, "vysoce Děkujeme automatické zjišťování databází [server] instance databáze SQL." To umožňuje SnelStart k provedení operace správy mezi jejich dynamicky rostoucí zákaznická základna bez dalších zásahů.

SnelStart také vyvíjí rozhraní API, který funguje jako zprostředkovatel mezi zákaznická data a aplikace vytvořené pomocí softwaru třetí strany partnery. Jako Kuip stavy "Toto rozhraní API vám umožní jiných dodavatelů k přidání funkcí do našich softwarem, např. odstranění datová položka pro faktury a jiné dokumenty." Zákazníci budou už muset ručně zadejte faktury do své malé firmy monitorování účtů softwaru; SnelStart software vymění nezbytné informace přímo. To umožňuje zákazníkům připojení úkoly správy obchodní s ekosystému informace, které je vycházejících z digitální transformace v odvětví.  

## <a name="how-azure-services-enable-saas-for-snelstart"></a>Jak povolit službám Azure SaaS pro SnelStart
Pomocí služby Azure, může sloužit SnelStart svým zákazníkům a jejich účetní více bezproblémově v cloudu. Například zákazníků a účetní umožňuje sdílení informací pomocí přímý přístup k rozhraní API klienta na SnelStart, hostovanou v Azure. Stavy Kuip "tyto opakovaně použitelné služby jsou dostupné na naší straně zákazníka webové aplikace a poskytují společnou infrastrukturu a funkce povolit přístup k obchodní správy pro zákazníky a k softwaru třetích stran používané našich zákazníků. Mezi příklady patří poskytování konfiguraci produktu, Správa pravidel brány firewall a správa dlouho běžící procesy, například záloh."

> Naším cílem je zajistit 100 procent automatizace obchodní správy služeb pro naše zákazníky." 
> 
> – Carlo Kuip, IT architekti
> 
> 

Kromě toho povolit SnelStart webových služeb zákazníkům a účetní snadný přístup k datům v Azure SQL Database elastické fondy. Tento model SaaS kombinaci s databáze pružnost a Azure Resource Manager poskytuje SnelStart škálovatelnost funkce, které doplňují každé nasazení Azure. Implementace je plně automatizovat pomocí správy knihovny jazyka C#.

![Architektura SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Obrázek 1. Od června 2016 SnelStart má více než 11 000 databází a více než 50 elastické fondy

## <a name="simplicity-from-the-cloud"></a>Jednoduchost z cloudu
Od přesunutím do Azure cloudové řešení, byl SnelStart schopné podporovat rychlé zákazníka růstu zároveň nabízí inovativní funkce a služby. Podle byl, "s Azure, jsme doručovat prakticky nepřetržité aktualizace pro naše zákazníky bez rozbalení naše provozní personál. A všech dalších skvělé Azure funkcí se nám získat – jako škálovatelnost a zotavení po havárii – seskupeny ve. "

> "Pokud se ve skutečnosti přes v Redmondu... Zobrazila volání od vývojáře zpět v Nizozemsko, telefonují mě o konkrétním problému. Nebylo možné získat Microsoft k poskytování změnu do 48 hodin naše problém můžete vyřešit ve svém provozním prostředí."
> 
> – Henry byla architekti softwaru
> 
> 

SnelStart také oceňuje silné partnerství, které jste vyvinuté s týmem Microsoft Azure SQL DB. Jako Kuip stavy, "máme diskusí na funkce, a jak používat technologii, která se vezme v úvahu na obou stranách."
Okamžitou cílem pro SnelStart je zachovat ročně zvýší jeho splněna zákazníka základní. Jak se stavy, "Bez omezení prostředků a technické které jsme měli jako ISV neexistuje žádné omezení, jak daleko jsme můžou růst."

## <a name="more-information"></a>Další informace
* Další informace o Azure elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).
* Další informace o Azure SQL Data Warehouse, najdete v části [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)
* Další informace o SnelStart najdete v tématu [SnelStart](http://www.snelstart.nl).

