---
title: Více hlavních serverů v globálním měřítku s využitím Azure Cosmos DB | Dokumentace Microsoftu
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: faca70ccc1b96a8cefd65a941abde579ce54a616
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050217"
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Více hlavních databází v globálním měřítku s využitím Azure Cosmos DB 
 
Vývoj globálně distribuované aplikace, které odpoví místní latenci při zachování konzistentní zobrazení dat po celém světě je náročný problém. Zákazníci používat globálně distribuované databáze, protože potřebují zlepšit latenci přístupu k data, dosáhnout vysokou dostupnost, zajištění zaručené zotavení po havárii a pro splnění svých obchodních požadavků. Více hlavních databází ve službě Azure Cosmos DB poskytuje vysokou úroveň dostupnosti (99,999 %), latence v řádu milisekund k zápisu dat a škálovatelnost s integrovanou konfliktní komplexní a flexibilní podporu rozlišení. Tyto funkce výrazně zjednodušuje vývoj globálně distribuovaných aplikací. Pro globálně distribuované aplikace je zásadní podpora několika hlavními databázemi. 

![Architektura s několika hlavními databázemi](./media/multi-region-writers/multi-master-architecture.png)

S několika hlavními databázemi podpory služby Azure Cosmos DB můžete provádět zápisy na kontejnery dat (například kolekce, grafy, tabulky), distribuované kdekoli na světě. Můžete aktualizovat data v libovolné oblasti, která souvisí se svým databázovým účtem. Tyto aktualizace dat může rozšířit asynchronně. Kromě poskytování rychlého přístupu a latence zápisu ke svým datům, více hlavních databází také poskytuje praktické řešení pro převzetí služeb při selhání a vyrovnávání zatížení problémy. Stručně řečeno, pomocí služby Azure Cosmos DB můžete získat latence zápisu < 10 ms na 99. percentilu kdekoli ve světě, 99,999 % zápisu a čtení dostupnost kdekoli v celém světě a schopnost škálování i zápis a čtení propustnost kdekoli na světě.   

> [!IMPORTANT]
> Podpora několika hlavními databázemi je ve verzi private preview pro použití verze preview [zaregistrovat](#sign-up-for-multi-master-support) nyní.

## <a name="sign-up-for-multi-master-support"></a>Zaregistrujte si podporu několika hlavními databázemi

Pokud už máte předplatné Azure, můžete zaregistrovat do programu několika hlavními databázemi ve verzi preview na webu Azure Portal. Pokud ještě Azure nepoužíváte, zaregistrujte si [bezplatnou zkušební verzi](https://azure.microsoft.com/free), kde získáte 12 měsíců volného přístupu k Azure Cosmos DB. Proveďte následující kroky požádáte o přístup k programu několika hlavními databázemi ve verzi preview.

1. Na [portálu Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**.  

2. Na stránce nový účet zadejte název účtu služby Azure Cosmos DB, zvolte rozhraní API, předplatné, skupinu prostředků a umístění.  

3. Dále vyberte **zaregistrujte si verzi preview ještě dnes** pod pole s více hlavní ve verzi Preview.  

   ![Zaregistrovat verzi preview několika hlavními databázemi](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. V **zaregistrujte si verzi preview ještě dnes** podokně klikněte na tlačítko **OK**. Po odeslání žádosti se stav změní na **čekající na schválení** v okně vytvoření účtu.  

Po odeslání žádosti obdržíte oznámení e-mailem, že vaše žádost se schválila. Protože je požadavků hodně, měli byste oznámení obdržet do týdne. K dokončení žádosti není nutné vytvořit lístek podpory. Žádosti budeme vyřizovat v pořadí, v jakém je obdržíme.

## <a name="a-simple-multi-master-example--content-publishing"></a>Publikování několika hlavními databázemi jednoduše – obsahu  

Podívejme se na reálný scénář, který popisuje způsob použití několika hlavními databázemi podpora pomocí služby Azure Cosmos DB. Zvažte možnost obsahu publikování platforma založená na službě Azure Cosmos DB. Tady jsou některé požadavky, které tuto platformu, musí splňovat pro skvělé uživatelské prostředí pro vydavatele i spotřebitele. 

* Autoři a odběratele se šíří po celém světě.  

* Autoři musí Publikovat články (zápis) místní oblasti (nejbližší).  

* Autoři mají čtenáři/předplatitelé jejich článků, kteří jsou distribuovaných po celém světě.  

* Předplatitelé měli oznámení, když se publikují nové články.  

* Předplatitelé musí být schopni číst články z místní oblasti. Musí být také možnost přidávat recenze na tyto články.  

* Každý, včetně autora článků by měl být moct zobrazit všechny revize připojená k články z místní oblasti.  

Miliony zákazníků a vydavatelé miliardy články, za předpokladu, že nejdříve musíme boji s problémy škálování – zaručující umístění přístup. Případ použití je ideální Release candidate pro více hlavních databází Azure Cosmos DB. 

## <a name="benefits-of-having-multi-master-support"></a>Výhody s několika hlavními databázemi podpory 

Podpora několika hlavními databázemi je nezbytné pro globálně distribuované aplikace. Se skládá z více hlavních databází [několika hlavních oblastech](distribute-data-globally.md) , který rovnoměrně součástí modelu zápisu na libovolné platformě (aktivní – aktivní vzor) a používá se k zajištění, že data jsou k dispozici v každém okamžiku kde ji potřebujete. Aktualizace provedené v jednotlivých oblastech se asynchronně rozšíří do všech oblastí (které jsou zase hlavní oblasti ve vlastním). Oblastí Azure Cosmos DB automaticky provoz jako hlavní oblasti v konfiguraci s několika hlavními databázemi práce je, aby sloučit data všech replik a zajištění [globální konzistence a integrita dat](consistency-levels.md). Následující obrázek ukazuje replikace pro čtení a zápisu pro jedinou předlohou a více hlavních databází.

![Jedinou předlohou a několika hlavními databázemi](./media/multi-region-writers/single-vs-multi-master.png)

Implementující více hlavních databází sami přidá zatížení na vývojáře. Ve velkém měřítku zákazníci, kteří se pokusí implementovat více hlavních databází na své vlastní může trávit stovky hodin konfigurace a testování po celém světě multimasterovou konfigurace a řada má vyhrazenou sadu technici, jehož jediným úkolem je monitorovat a spravovat více hlavních databází replikace. Vytváření a správa několika hlavními databázemi instalační program sami zabere určitý čas, prostředky mimo inovace ve vlastní aplikace a má za následek výrazně vyšší náklady. Azure Cosmos DB podporuje multimasterovou "out-of-the-box" a odebere toto zatížení od vývojářů.  

Stručně řečeno, více hlavních databází poskytuje následující výhody:

* **Lepší zotavení po havárii, zápisu, dostupnost a převzetí služeb při selhání**-více hlavních databází je možné zachovat dostupnost vysokou důležitostí pro chod databáze ve větší míře. Například více hlavních databází můžete replikovat data z jedné oblasti do oblasti převzetí služeb při selhání při primární oblast přestane být k dispozici kvůli výpadku nebo regionálního. Převzetí služeb při selhání oblast bude sloužit jako plně funkční hlavní oblasti pro podporu aplikace. Více hlavních databází poskytuje větší ochranu "přežití" s ohledem na přírodní pohromy, výpadků napájení nebo napadení zařízení, protože zbývající oblastech můžou využívat geograficky různých více hlavních serverů s > 99,999 % dostupnost zaručenou zápisu. 

* **Vylepšené latence zápisu pro koncové uživatele** – blíže (kterým obsluhujete) je koncovým uživatelům, tím lepší bude prostředí. Například, pokud mají uživatelé v Evropě, ale vaše databáze je v USA nebo Austrálie, přidání latence je přibližně 140 ms a 300 ms pro příslušné oblasti. Zpoždění nepřijatelné začínat pro mnoho oblíbených hry, bankovnictví požadavky nebo interaktivní aplikace (webové i mobilní zařízení). Latence hraje velkou část v jeho vnímání vysoce kvalitní prostředí a se prokázalo, že chování uživatelů do určité míry znatelný vliv. Zlepšuje technologií a především s nástupem AR, virtuální Realitu a MR ještě více živoucí a atraktivní prostředí, které vyžadují vývojáři teď potřebujete k tvorbě softwarových systémů přísné latenci. S místně dostupné aplikace a data (obsahu pro aplikace) je proto důležité. Výkon s několika hlavními uzly ve službě Azure Cosmos DB, je tak rychle, jak místní pravidelně čte a zapisuje a vylepšené globálně pomocí geografická distribuce.  

* **Vylepšené škálovatelnosti zápisu a zápisu propustnost** – více hlavních databází vám poskytne větší propustnost a vyšší využití a nabízí různé modely konzistence s správnosti zaručuje a zajištěná smlouvy o úrovni služeb. 

  ![Škálování propustnosti zápis s několika hlavními uzly](./media/multi-region-writers/scale-write-throughput.png)

* **Lepší podpora pro odpojené prostředí (například hraniční zařízení)** – umožňuje uživatelům replikovat všechny nebo podmnožinu dat z hraničního zařízení do nejbližší oblasti v odpojeném prostředí více hlavních databází. Tento scénář je typický pro prodejců automatizační systémy, kde přenosných počítačů jednotlivých zákazníků (odpojené zařízení) ukládá část dat souvisejících s jednotlivé prodejce. Hlavní oblasti v cloudu, které jsou umístěny kdekoli na světě můžou fungovat jako cíl kopírování z Vzdálená hraniční zařízení.  

* **Vyrovnávání zatížení** – s několika hlavními uzly, zatížení napříč aplikací může být znovu vyrovnána přesunutím uživatelů a úloh z velkém zatížení oblasti do oblastí, kde se rovnoměrně distribuuje zatížení. Zapsat kapacitu je možné snadno rozšířit přidáním nové oblasti a potom přepněte některé zápisy do nové oblasti. 

* **Lepší využití zřízená kapacita** – s několika hlavními uzly pro úlohy náročné na zápis a smíšené, jste saturate zřízená kapacita napříč několika oblastmi...  V některých případech je možné znovu distribuovat přečtených a zapsaných více stejně, takže vyžaduje méně propustnost, které se mají zřídit a vede k větší úspory pro zákazníky.  

* **Jednodušší a odolnost aplikace architektury** – Přesun několika hlavními databázemi konfiguraci aplikací získat zaručeno, že odolnost dat.  Pomocí služby Azure Cosmos DB skrytí všem složitosti ho podstatně může zjednodušit návrh aplikace a architektury. 

* **Bez rizika, testování převzetí služeb při selhání** – testování převzetí služeb při selhání nebude mít žádné snížení na zapisování. S několika hlavními uzly všechny ostatní oblasti jsou plně tedy hlavních uzlů, tak převzetí služeb při selhání nebude mít mnoho dopad na propustnost zápisu.  

* **Snižte celkové náklady na Ownership(TCO) a DevOps** -splnění škálovatelnost, výkon, globální distribuci, cíle plánované doby obnovení jsou často drahé kvůli drahé doplňky nebo údržbu infrastruktury zálohování, který je v klidovém stavu až po havárii náhlé. S Azure Cosmos DB více hlavních databází zajištěná špičkové smlouvy SLA vývojáři už vyžadují vytváření a udržování "spojovací logiku back-end" sami a získat klid s jejich nejdůležitější úlohy. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Případy použití, které vyžadují podporu několika hlavními databázemi

Existují mnoho případy použití pro více hlavních databází ve službě Azure Cosmos DB: 

* **IoT** -více hlavních databází Azure Cosmos DB umožňuje jednodušší implementace distribuované zpracování dat IoT. Edge geograficky distribuované nasazení, využívající bez konfliktů CRDT replikovaná data. typy se často potřebují ke sledování dat časových řad z více míst. Každé zařízení může být adresami k jednomu z nejbližší oblasti a zařízení mohou projít (například auta) a mohou být přesunuty dynamicky k zápisu do jiné oblasti.  

* **E-Commerce** -skvělé uživatelské prostředí ve scénářích elektronického obchodování je ujištěním, potřebuje vysokou dostupnost a odolnost vůči selhání scénáře. V případě selhání oblasti chtěli uživatelské relace, nákupní košíky, aktivní seznamy musí být bezproblémově vyzvednutí jiné oblasti bez ztráty stavu. Prozatím musí správně zpracovat aktualizace provedené uživatelem (například přidá a odebere z nákupního košíku musí přenos přes). S několika hlavními uzly můžete, elegantně zpracovat takové scénáře s hladký přechod mezi oblastmi aktivní při zachování konzistentní zobrazení z pohledu uživatele služby Azure Cosmos DB. 

* **Zjišťování možných podvodů a anomálií** -často aplikací, které monitoruje aktivity uživatele nebo aktivity účtu jsou globálně distribuované a musí udržovat přehled o několik událostí současně. Při vytváření a správa skóre, které se pro uživatele, musíte aktualizovat akce z různých geografických oblastech současně skóre zachovat vložené metriky rizika. Azure Cosmos DB můžete zajistit, že vývojáři nemuseli kvůli zpracování scénářů konfliktů na úrovni aplikace. 

* **Spolupráce** – pro aplikace, které podle oblíbenosti článků, jako je například zboží na prodej nebo média, který se má používat atd. Sledování oblíbenosti napříč geografickými oblastmi můžete získat složité, zejména pokud musí být čas placené nebo skutečné reklamy rozhodnutí o licenčních poplatků. Pořadí, řazení a vytváření sestav napříč mnoha oblastmi po celém světě v reálném čase pomocí služby Azure Cosmos DB umožňuje vývojářům dodávat funkce s malou úsilí a bez negativního vlivu na latenci. 

* **Monitorování míry využívání** – počítání a řízení využití (jako je například volání rozhraní API používá minut transakcí za sekundu) je možné implementovat globálně pomocí jednoduchosti použití více hlavních databází Azure Cosmos DB. Řešení konfliktů integrované zaručuje obou nemusí být počty a nařízení v reálném čase. 

* **Přizpůsobení** – Určuje, zda uchováváte geograficky distribuované čítače, které aktivují akce, třeba věrnostní odkazuje ocenění za příspěvky nebo implementace přizpůsobené uživatelské relace zobrazení, vysokou dostupnost a zjednodušená geograficky distribuované počítání poskytované služby Azure Cosmos DB umožňuje vysoký výkon doručování aplikací s jednoduchost. 

## <a name="conflict-resolution-with-multi-master"></a>Řešení konfliktů s několika hlavními uzly 

S několika hlavními uzly je před obrovskou výzvou – často, že dva (nebo více) repliky stejný záznam může aktualizovat současně různých zapisovače ve dvou nebo více různých oblastech. Souběžných zápisů může vést k dvě různé verze stejného záznamu a bez konfliktů integrované a vlastní aplikace musí provést řešení konfliktů, chcete-li vyřešit Tato nekonzistence.  

**Příklad** -Předpokládejme, že používáte služby Azure Cosmos DB, protože trvalého úložiště pro aplikaci nákupního košíku a tato aplikace je nasazená ve dvou oblastech: USA – východ a USA – západ.  Pokud přibližně ve stejnou dobu, uživatel v kalifornském San Franciscu přidá položku do svého nákupního košíku (například kniha) při procesu správy inventáře v oblasti východní USA zruší platnost různých nákupního košíku položku (například nový telefon) pro tento stejný uživatel v reakci s upplier oznámení, který měl byla zpožděna datum vydání. V čase T1 nákupního košíku záznamy ve dvou oblastech se liší. Databáze bude používat jeho replikace a mechanismus řešení konfliktu vyřešit Tato nekonzistence a nakonec jednu z dvou verzí nákupního košíku bude vybrána. Pomocí heuristiky řešení konfliktu nejčastěji používané více hlavních databází (například poslední zápis wins), je možné pro uživatele nebo aplikace k předvídání, která verze bude vybraná. V obou případech dojde ke ztrátě dat nebo může dojít k neočekávanému chování. Pokud je vybrána oblast verze – východ, pak výběr nové položky nákupu (to znamená adresáře), který uživatel ztratí a pokud je vybrána oblast – Západ, pak dříve vybrané položky (telefon) je stále v košíku. V obou případech dojde ke ztrátě informací. A konečně jiný proces kontroly nákupního košíku mezi časem T1 a T2 bude také nedeterministické chování najdete v článku. Proces na pozadí, který vybere sklad plnění a aktualizuje košíků, přepravní by například vráceny výsledky, které jsou v konfliktu s konečnou obsah košíku. Pokud je proces spuštěn v oblasti Západ a alternativní 1 stane realitou, to by přesouvání náklady na výpočetní výkon pro dvě položky, i když košíku brzy může mít pouze jednu položku, knihy. 

Azure Cosmos DB implementuje logiku pro zpracování konfliktní zápisy v databázovém stroji samotný. Azure Cosmos DB nabízí **podpory řešení konfliktů komplexní a flexibilní** tím, že nabízí několik konflikt překladu modelů, včetně automatického (bez konfliktů CRDT replikované datové typy), poslední zápis Wins (LWW) a vlastní () Uložená procedura) pro automatické odstraňování konfliktů. Modely řešení konfliktu představují záruku správnosti a konzistence a odebrat zatížení od vývojářů myslet konzistencí, dostupností, výkonem, latence replikace a komplexní kombinací události v rámci geo-převzetí služeb při selhání a konflikty mezi různými oblastmi zápisu.  

  ![Řešení konfliktů mult-master](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Budete mít 3 typy modelů řešení konfliktu nabízené službou Azure Cosmos DB. Sémantika modely řešení konfliktů je následujícím způsobem: 

**Automatické** – Toto je výchozí zásada řešení konfliktů. Výběrem této zásady způsobí, že Azure Cosmos DB automaticky vyřešit konfliktní aktualizace na straně serveru a zadejte silné--konzistencí záruky. Azure Cosmos DB interně, implementuje automatické odstraňování konfliktů ve využívání konflikt-bez-replikovat – datové typy (CRDTs) v databázovém stroji.  

**Služby Wins posledního zápisu (LWW)** – výběrem této zásady vám umožní vyřešit konflikty na základě některé definovaná systémem synchronizovaných vlastnost časového razítka nebo vlastní vlastnosti definované ve verzi konfliktní záznamy. Řešení konfliktů dochází na straně serveru a verze se nejnovější časovým razítkem je vybraná jako vítěze.  

**Vlastní** – můžete zaregistrovat logiky řešení konfliktu definovanými v aplikaci tak, že zaregistrujete uloženou proceduru. Spouštění uložená procedura se získat vyvolána při zjišťování konfliktů při aktualizacích pod záštitou databázové transakce na straně serveru. Pokud vyberte možnost, ale nepovedlo se zaregistrovat uložené procedury (nebo uložená procedura dojde k výjimce za běhu), můžete přístup ke všem konfliktní verze prostřednictvím kanálu je v konfliktu a jejich řešení jednotlivě.  

## <a name="next-steps"></a>Další postup  

V tomto článku učení použití globálně distribuované více hlavních databází pomocí služby Azure Cosmos DB. V dalším kroku se podívejte na následující zdroje: 

* [Další informace o tom, jak službu Azure Cosmos DB podporuje globální distribuce](distribute-data-globally.md)  

* [Další informace o automatické převzetí služeb při selhání ve službě Azure Cosmos DB](regional-failover.md)  

* [Další informace o globální soudržnosti pomocí služby Azure Cosmos DB](consistency-levels.md)  

* Vývoj s využitím více oblastí pomocí služby Azure Cosmos DB – [rozhraní SQL API](tutorial-global-distribution-sql-api.md), [rozhraní MongoDB API](tutorial-global-distribution-mongodb.md), nebo [rozhraní Table API](tutorial-global-distribution-table.md)  
