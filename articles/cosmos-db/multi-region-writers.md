---
title: Více hlavních serverů v globálním měřítku s Azure Cosmos DB | Microsoft Docs
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: 12306b7868fa7fb2321f26657aab81beabb9db35
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Více hlavní v globálním měřítku s Azure Cosmos DB 
 
Vývoj globálně distribuované aplikace, které reagují s místní latence při zachování konzistentní zobrazení dat po celém světě je náročné problém. Zákazníci použít globálně distribuované databáze, protože potřebují k zlepšení latence přístupu dat, dosáhnout dat vysoké dostupnosti a ujistěte se zaručenou zotavení po havárii a, (4) ke splnění svých podnikových požadavků. Více hlavního serveru v Azure Cosmos DB poskytuje vysokou úrovní dostupnosti (99.999 %), latence jednociferné milisekundu k zápisu dat a škálovatelnost integrovanou konfliktní komplexní a flexibilní řešení podpory. Tyto funkce výrazně usnadňuje vývoj globálně distribuované aplikace. Pro globálně distribuované aplikace je zásadní podpora více hlavních serverů. 

![Více hlavních architektura](./media/multi-region-writers/multi-master-architecture.png)

S podporou více hlavních databázi Cosmos Azure můžete provést zápisy na kontejnery data (například kolekcí, grafy, tabulky) distribuované kdekoliv na světě. Můžete aktualizovat data v libovolné oblasti, která souvisí s vaším účtem databáze. Tyto aktualizace dat můžete rozšířit asynchronně. Kromě rychlý přístup a latence zápisu u ke svým datům, více hlavní taky nabízí praktické řešení pro převzetí služeb při selhání a vyrovnávání zatížení problémy. Souhrnně s Azure Cosmos DB získáte latence zápisu < 10 ms v 99th percentilu kdekoli v world, 99.999 % zápisu a čtení dostupnost kdekoli v celém světě a schopnost škálování obě zápisu a čtení propustnost kdekoli po celém světě.   

> [!IMPORTANT]
> Podpora více hlavních serverů je v privátní Preview verzi, používat verzi preview [zaregistrovat](#sign-up-for-multi-master-support) nyní.

## <a name="sign-up-for-multi-master-support"></a>Registrace pro podporu více hlavních serverů

Pokud již máte předplatné Azure, musíte se zaregistrovat chcete zapojit do programu preview více hlavních na portálu Azure. Pokud jste Azure ještě nepoužívali, zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/free) kde získat dobu 12 měsíců volného přístupu k databázi Azure Cosmos. Pomocí následujících kroků si vyžádáte přístup k programu více hlavních preview.

1. V [portál Azure](https://portal.azure.com), klikněte na tlačítko **vytvořit prostředek** > **databáze** > **Azure Cosmos DB**.  

2. Na stránce nový účet zadejte název pro váš účet Azure Cosmos DB, vyberte rozhraní API, předplatné, skupinu prostředků a umístění.  

3. Dále vyberte **zaregistrujte si verzi Preview ještě dnes** v rámci pole více předlohy Preview.  

   ![Zaregistrujte si více hlavních preview](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. V **zaregistrujte si verzi Preview ještě dnes** podokně klikněte na tlačítko **OK**. Po odeslání žádosti se stav změní na **čekající na schválení** v okně vytváření účtu.  

Po odeslání žádosti, obdržíte e-mail s oznámením, že vaše žádost byla schválena. Kvůli velkému počtu požadavků měli byste obdržet oznámení do jednoho týdne. Není nutné vytvořit lístek podpory pro dokončení požadavku. V pořadí, ve kterém bylo přijato projde kontrolou požadavků.

## <a name="a-simple-multi-master-example--content-publishing"></a>Publikování jednoduchý více hlavních příklad – obsahu  

Podívejme se na reálného scénáře, který popisuje, jak používat více hlavních podporu s Azure Cosmos DB. Vezměte v úvahu vytvořené v Azure Cosmos DB platforma pro publikování obsahu. Tady jsou některé požadavky, které tuto platformu musí splňovat pro vysoký výkon uživatele pro vydavatele a spotřebitelé. 

* Autoři a Odběratelé, kteří se šíří po celém světě.  

* Autoři musíte publikovat články (zápisu) na jejich místní oblast (nejbližší).  

* Autoři mají čtečky nebo odběratele, jejich článků kteří distribuují po celém světě.  

* Odběratelé, kteří měli obdržet oznámení, při publikování nových článků.  

* Odběratelé, kteří musí být možné číst články ze své místní oblast. Musí být také možné přidat recenze na tyto články.  

* Každý, kdo včetně autora články musí být možné zobrazit všechny recenze připojená k články z místní oblast.  

Za předpokladu, že miliony s až miliardy článků, vydavatelům a spotřebitelům brzy máme boji s problémy měřítka společně s zaručující polohu přístupu. Případ použití je ideální kandidátem pro Azure Cosmos DB více hlavní server. 

## <a name="benefits-of-having-multi-master-support"></a>Výhody použití více hlavních podpory 

Podpora více hlavních je základem pro globálně distribuované aplikace. Se skládá z několika hlavní [více hlavní oblasti](distribute-data-globally.md) který rovnoměrně účastnit model zápisu odkudkoli (aktivní – aktivní vzor) a slouží k zajištění, že dat je k dispozici kdykoli potřebujete kde. Aktualizace provedené oblast jednotlivých rozšířeny asynchronně ke všem oblastem (které jsou naopak hlavní oblasti v vlastní). Oblastí Azure Cosmos DB jako hlavní oblasti v konfiguraci s více hlavních operační automaticky fungovat sloučit data všech replik a zajistit [globální konzistence a integrita dat](consistency-levels.md). Následující obrázek znázorňuje replikace pro čtení a zápis pro jeden hlavní a mult hlavní.

![Hlavní jeden a více hlavních serverů](./media/multi-region-writers/single-vs-multi-master.png)

Implementuje více hlavní sami přidá zatížení na vývojáři. Ve velkém měřítku zákazníci, kteří zkuste implementovat více hlavní na své vlastní může trávit stovky hodin konfiguraci a testování konfiguraci více hlavních serverů po celém světě a mnoho má vyhrazený sadu engineers, jehož jediným úlohy je monitorovat a spravovat více hlavní data replikace. Vytváření a správu více hlavních serverů instalační program na vlastní trvá čas prostředky mimo innovating ve vlastní aplikace a výsledkem mnohem vyšší náklady. Azure Cosmos DB poskytuje více hlavních podporu "out-of-the-box" a odstraní tato dodatečná režie vývojáři.  

Souhrnně více hlavní poskytuje následující výhody:

* **Lepší zotavení po havárii, zapisovat dostupnosti a převzetí služeb při selhání**-více hlavní lze zachovat vysokou dostupnost důležitých databáze ve větší míře. Například více hlavních databázi můžete replikovat data z jedné oblasti v oblasti převzetí služeb při selhání při primární oblasti stane nedostupným kvůli výpadku nebo regionální po havárii. Oblast převzetí služeb při selhání bude sloužit jako hlavní plně funkční oblast pro podporu aplikace. Více hlavní poskytuje větší "funkční schopnost" ochrany s ohledem na přírodní katastrofy, výpadky napájení, nebo napadení nebo obojí, protože zbývající oblasti může být v geograficky různých více hlavních serverů s > 99.999 % dostupnosti zaručenou zápisu. 

* **Vylepšené latence zápisu pro koncové uživatele** – Čím bližší data (který obsluhujete) je pro koncového uživatele, tím lepší bude zkušenosti. Například pokud máte uživatele v Evropě, ale databáze se nachází v USA nebo Austrálie, přidané latence je přibližně 140 ms a 300 ms pro příslušné oblasti. Zpoždění nepřijatelné spustit s mnoha oblíbených hry, bankovnictví požadavky nebo interaktivní aplikací (web nebo mobilní). Latence hraje obrovské součástí v dojem zákazníka prostředí vysoce kvalitní a ukázalo se jako ovlivnit chování uživatelů do určité míry znatelné. Zlepšuje technologie a zejména s nástupem AR, VR a MR nutnosti i další prostředí dokonalé a živoucí vývojáři teď potřeba vytvořit systémy softwaru s latencí přísné požadavky. Místně dostupné aplikace a data (obsah pro aplikace) je proto důležité. S více hlavní v Azure Cosmos DB výkonu je jako rychlostí, jakou regulární místní čte a zapisuje a rozšířené globálně pomocí geo rozdělení.  

* **Vylepšené škálovatelnosti zápisu a zápisu propustnost** – více hlavní získáte vyšší propustnost a vyšší využití při nabízí více modelů konzistence správnost zaručuje a zálohovaná SLA. 

  ![Škálování propustnost zápisu s více hlavní](./media/multi-region-writers/scale-write-throughput.png)

* **Lepší podpory pro odpojené prostředí (například hraniční zařízení)** -více hlavní umožňuje uživatelům replikovat všechny nebo podmnožinu dat z hraniční zařízení na nejbližší oblast v odpojeném prostředí. Tento scénář je typické pro prodej vynutit automatizace systémy, kde k jednotlivcům přenosný počítač (odpojené zařízení) ukládá část dat souvisejících s jednotlivé prodejce. Hlavní oblasti v cloudu, které jsou umístěny kdekoliv na světě můžou fungovat jako cíl kopírování ze vzdáleného hraniční zařízení.  

* **Vyrovnávání zatížení** -s více hlavní zatížení napříč aplikace může být znovu vyrovnána přesunutím uživatele nebo úlohy ze velkém zatížení oblasti do oblasti, kde je zatížení rovnoměrně rozdělené. Zapsat kapacity lze snadno rozšířit přidáním nové oblasti a pak přepínání některé zápisy do nové oblasti. 

* **Lepší využití zřízená kapacita** – s více hlavní pro úlohy náročné na zápis a smíšený, může vyčerpat zřízená kapacita nad několika oblastmi...  V některých případech je možné znovu distribuovat čtení a zápisy více stejně, takže vyžaduje méně propustnost zřízení a za následek více finanční úspoře pro zákazníky.  

* **Jednodušší a pružnější aplikace architektury** -aplikace Přesun do více hlavních konfigurace získat zaručit odolnost data.  S Azure DB Cosmos skrytí všech složitost se může podstatně zjednodušit návrh aplikace a architektura. 

* **Bez rizika, testování převzetí služeb při selhání** – testování převzetí služeb při selhání nebude mít žádné snížení na propustnost zápisu. S více hlavní server jsou jiných oblastí úplné hlavních serverů, takže převzetí služeb při selhání nebude mít mnoho dopad na propustnost zápisu.  

* **Snížit celkové náklady na Ownership(TCO) a DevOps** -splňuje škálovatelnost, výkon, globální distribuční cíli doby obnovení jsou obvykle nákladné z důvodu nákladné doplňky nebo údržbu infrastruktury zálohování, která je umístěná až po havárii narazilo. S více master Azure Cosmos DB zálohovaná špičkový SLA vývojáři už vyžadují vytváření a údržbu "spojovací logiku back-end" sami a získat jistotu spuštěné jejich kritické úlohy. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Případy použití, kde je potřeba více hlavních podpory

Existují množství případy použití pro více hlavní server v Azure Cosmos DB: 

* **IoT** -více master Azure Cosmos DB umožňuje pro zjednodušenou implementaci distribuované zpracování dat IoT. Geograficky distribuovaná edge nasazení, která používají CRDT konflikt bez replikovaných dat typy často potřebují sledovat časových řad dat z více umístění. Každé zařízení může adresami na jednu z nejbližšího oblasti a zařízení můžete cestování (například automobilu) a můžete dynamicky přesunutí k zápisu do jiné oblasti.  

* **E-Commerce** -zajistit vysoký výkon uživatele ve scénářích elektronické obchodování musí mít vysokou dostupnost a odolnost proti selhání scénáře. V případě, že oblast selže, chcete uživatelské relace, nákupních košíků, aktivní, že seznamy muset být bezproblémově zachyceny pomocí jiné oblasti bez ztráty stavu. Do té doby je správně zvládnout aktualizace provedené uživatelem (například přidá a odebere z nákupního košíku musí přenos přes). S více hlavní server může řádně, zpracovávat takových scénářů s plynulý přechod mezi aktivní oblasti při zachování konzistentní zobrazení z hlediska uživatele Azure Cosmos DB. 

* **Zjišťování podvodu nebo anomálií** -často aplikace, které monitorování aktivity uživatelů nebo aktivitu na účtu jsou globálně distribuované a musí udržovat přehled o několik událostí současně. Při vytváření a údržbu skóre pro uživatele, musíte aktualizovat akce z různých zeměpisných oblastech současně zachovat vložený riziko metriky skóre. Azure Cosmos DB můžete zajistit, že vývojáři nemusí zpracovávat scénáře konfliktů na úrovni aplikace. 

* **Spolupráce** – pro aplikace, které pořadí založeny na době Oblíbené články například zboží na prodej nebo na médiu, který se má používat atd. Sledování oblíbenosti přes geografické oblasti můžete získat složitá, zvláště pokud licenční poplatky musí být čas placené nebo skutečné inzerování rozhodnutí, která má být provedeno. Řazení, řazení a vytváření sestav v mnoha oblastech po celém světě, v reálném čase s Azure Cosmos DB umožňuje vývojářům poskytovat funkce s malým množstvím úsilí a to bez kompromisů v latenci. 

* **Měření** – počítání a regulační využití (například volání rozhraní API používá minut transakcí za sekundu) můžete provedeny globálně s jednoduchost pomocí více master Azure Cosmos DB. Řešení konfliktů předdefinované zaručuje i přesnost počtů a nařízení v reálném čase. 

* **Přizpůsobení** – jestli jste zachování geograficky rozptýlené čítače, které aktivují akcí, například věrného body nálezů nebo implementace přizpůsobené uživatelské relace zobrazení, vysokou dostupnost a zjednodušená zeměpisné polohy počítání poskytované Azure Cosmos DB, umožňuje aplikacím doručit vysoký výkon s jednoduchost. 

## <a name="conflict-resolution-with-multi-master"></a>Řešení konfliktů s více hlavní 

S více hlavní server na výzvu je často, dva (nebo více) repliky stejného záznamu může být aktualizován současně jiné uživatelé vytvářející obsah ve dvou nebo více různých oblastech. Souběžných zápisy může vést k dvě různé verze stejného záznamu a bez řešení konfliktů předdefinované a vlastní aplikace musí provádět řešení konfliktů. Chcete-li vyřešit tento nekonzistence.  

**Příklad** -Předpokládejme, že používáte Azure Cosmos DB jako úložiště trvalosti pro aplikaci nákupního košíku a tato aplikace je nasazená ve dvou oblastí: Východ USA a západní USA.  Pokud přibližně ve stejnou dobu, uživateli v síti San Franciscu přidá položku do jeho nákupní košík (například kniha) při procesu správy inventáře ve východní USA by způsobila neplatnost na jinou položku nákupní košík (například nové telefonní číslo) pro tomuto uživateli v reakci s upplier oznámení, že datum vydání měl byla zpožděna. V čase T1 nákupní košík záznamy v dvou oblastí se liší. Databáze bude používat jeho replikace a mechanismus řešení konfliktu k odstranění této nekonzistence a nakonec se být vybrána jedna z dvou verzích nákupní košík. Pomocí heuristické metody řešení konfliktu nejčastěji použít více databází (například poslední zápis wins), není možné pro uživatele nebo aplikace k předvídání, která verze bude vybrána. V obou případech dojde ke ztrátě dat nebo může dojít k neočekávanému chování. Pokud je vybraná verze oblast – východ, dojde ke ztrátě pak výběr uživatele nové položky nákupu (to znamená, kniha) a pokud je vybrána této oblasti, pak dříve vybrané položky (telefon) je stále v košíku. V obou případech informace budou ztraceny. Nakonec jiný proces kontroly nákupního košíku mezi časy T1 a T2 bude také Nedeterministický chování. Například byste mohli proces na pozadí, který vybere sklad plnění a aktualizuje košíku přesouvání náklady vytvořit výsledky, které je v konfliktu s případný obsah košíku. Pokud je proces spuštěný v této oblasti a alternativní 1 se změní na skutečnost, ho by výpočetní přesouvání náklady na dvě položky, i když košíku brzy může mít pouze pro jednu položku seznamu. 

Azure Cosmos DB implementuje logiku pro zpracování konfliktní zápisy v databázovém stroji sám sebe. Nabízí Azure Cosmos DB **konflikt komplexní a flexibilní řešení podpory** tím, že nabízí několik konflikt překladu modely, včetně automatického (bez konfliktů CRDT replikované datové typy), poslední zápis Wins (LWW) a (vlastní Uložené procedury) pro automatické rozpoznávání konfliktů. Modely řešení konfliktu poskytovat záruku správnosti a konzistence a odebrání zatížení vývojáři k vezměte v úvahu konzistence, dostupnosti, výkonu, latenci replikace a komplexní kombinace události geo-převzetí služeb při selhání a je v konfliktu mezi oblastmi zápisu.  

  ![Řešení konfliktů mult-master](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Budete mít 3 typy modelů konflikt řešení, které nabízí Azure Cosmos DB. Sémantika modely řešení konfliktu jsou následující: 

**Automatické** -Toto je výchozí zásady řešení konfliktů. Výběrem této zásady způsobí, že Azure DB Cosmos automaticky vyřešit konfliktní aktualizace na straně serveru a zadejte silné konzistence typu případné záruky. Interně Azure Cosmos DB implementuje řešení konfliktů automatické ve využívání konflikt-bez-replikovat – datové typy (CRDTs) v databázovém stroji.  

**Poslední-Write-Wins (LWW)** – výběr těchto zásad vám umožní při řešení konfliktů na základě některé definovaná systémem synchronizována vlastnost časového razítka, nebo vlastní vlastnosti definované na verzi konfliktní záznamy. Řešení konfliktů se odehrává na straně serveru a verzí s nejnovější časové razítko je vybrán jako vítěz.  

**Vlastní** -zaregistrujete logiku aplikace definovaná konflikt řešení tak, že zaregistrujete uloženou proceduru. Uložená – procedura se získat volána po zjištění konfliktu aktualizace pod dohledem databázových transakcí na straně serveru. Pokud vyberete možnost ale nezdaří jejich registrace uložené procedury (nebo pokud uložená procedura vyvolá výjimku za běhu), můžete používat všechny konfliktní verze prostřednictvím kanálu konflikty a jejich řešení jednotlivě.  

## <a name="next-steps"></a>Další postup  

V tomto článku dozvědí použití globálně distribuované více hlavní s Azure Cosmos DB. Další podívejte se na následující prostředky: 

* [Další informace o tom, jak Azure Cosmos DB podporuje globální distribuční](distribute-data-globally.md)  

* [Další informace o automatické převzetí služeb při selhání v Azure Cosmos DB](regional-failover.md)  

* [Další informace o globální konzistence s Azure Cosmos DB](consistency-levels.md)  

* Vývoj s několika oblastí pomocí Azure Cosmos DB - [rozhraní SQL API](tutorial-global-distribution-sql-api.md), [MongoDB rozhraní API](tutorial-global-distribution-mongodb.md), nebo [tabulky rozhraní API](tutorial-global-distribution-table.md)  