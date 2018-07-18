---
title: Návrh řešení pro zotavení po havárii – Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak navrhnout vaše cloudové řešení pro zotavení po havárii výběrem vzor správné převzetí služeb při selhání.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 6952e26898e4ff27dd7c2f6780dcb9b8b224460b
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092541"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Strategie zotavení po havárii pro aplikace využívající elastické fondy SQL Database
V průběhu let jsme zjistili, že cloud services nejsou spolehlivá a stát katastrofickými incidenty. SQL Database nabízí několik možnosti, které zajišťuje nepřetržitý chod podniků vaší aplikace zadejte po těchto incidentech. [Elastické fondy](sql-database-elastic-pool.md) a izolované databáze podporují stejný druh možnosti zotavení po havárii. Tento článek popisuje několik strategií zotavení po Havárii pro elastické fondy, které využívají tyto funkce provozní kontinuity SQL Database.

Tento článek používá následující vzor kanonické aplikace SaaS nezávislých výrobců softwaru:

<i>Moderní cloudové webové aplikace zřídí jednu databázi SQL pro každý koncový uživatel. ISV má mnoho zákazníků a proto používá mnoho databází, označované jako databáze tenantů. Protože databází tenantů mají obvykle nepředvídatelné aktivity vzorců, ISV pomocí elastického fondu databáze nákladů velmi předvídatelné přes dlouhou dobu. Elastický fond také usnadňuje správu výkonu, když špičky aktivity uživatelů. Kromě databází tenantů aplikace také používá několik databází můžete spravovat profily uživatelů, zabezpečení nebo shromažďovat vzorce používání atd. Dostupnost jednotlivých tenantů nemá žádný vliv na dostupnost svojí aplikace jako celek. Ale dostupnost a výkon databáze správy je velmi důležité pro funkce aplikace a pokud jsou databáze správy offline celá aplikace je offline.</i>  

Tento článek popisuje pokrývají celou řadu scénářů od nákladů citlivé spouštění aplikací které požadavky na dostupnost přísné strategie zotavení po Havárii.

> [!NOTE]
> Pokud používáte Premium nebo pro důležité obchodní informace databáze a elastické fondy, můžete provádět je odolné regionální výpadky převedením na konfiguraci nasazení redundantního zóny. Zobrazit [zónově redundantní databáze](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scénář 1. Náklady citlivé spuštění
<i>Jsem Start-up a jsem nákladů velmi citlivé.  Chcete zjednodušit nasazení a správu aplikace a mám omezené smlouvu SLA pro jednotlivé zákazníky. Ale můžu muset zajistit, aby aplikaci jako celek se nikdy offline.</i>

Tím se uspokojí požadavek na jednoduchost, nasazení všech databázích tenantů do jednoho elastického fondu v oblasti Azure podle vašeho výběru a nasazení databází správy jako geograficky replikovaného izolované databáze. Pro zotavení po havárii tenantů použijte geografické obnovení, který se dodává se nevztahují žádné další poplatky. K zajištění dostupnosti správy databází, geografická replikace je do jiné oblasti pomocí automaticky při selhání skupiny (krok 1). Průběžné výdaje konfigurace zotavení po havárii v tomto scénáři je roven celkové náklady na sekundární databází. Tato konfigurace je znázorněno na následující diagram.

![Obrázek 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Pokud dojde k výpadku v primární oblasti, se popisují kroky obnovení pro používání vaší aplikace online pomocí následující diagram.

* Skupiny převzetí služeb při selhání iniciuje automatické převzetí služeb při selhání databáze správy do oblasti zotavení po Havárii. Aplikace automaticky znovu nepřipojí k nové primární a všechny nové účty a vytvoří se v oblasti zotavení po Havárii databází tenantů. Stávající zákazníci zobrazit svá data, není dočasně k dispozici.
* Vytvoření elastického fondu pomocí stejné konfigurace jako původní fond (2).
* Geografické obnovení použijte k vytvoření kopie tenanta (3) databáze. Můžete zvážit spouštění jednotlivých obnovení připojení koncových uživatelů nebo použít některé jiné schéma priority specifické pro aplikaci.


V tuto chvíli je aplikace zpátky do online režimu v oblasti zotavení po Havárii, ale zákazníci, kteří zaznamenat zpoždění při přístupu k jejich datům.

![Obrázek 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Pokud jste byli dočasný výpadek, je možné, že je Azure obnovit primární oblast předtím, než všechny databáze obnoví se dokončí v oblasti zotavení po Havárii. V takovém případě Orchestrace, přesun aplikace zpět do primární oblasti. Tento proces trvá popsané postupy na následující diagram.

* Zrušte všechny zbývající geografické obnovení žádosti.   
* Převzetí služeb při selhání správy databáze do primární oblasti (5). Po obnovení oblast stará primárek staly automaticky sekundárních replik. Nyní jsou role znovu přejděte. 
* Změňte připojovací řetězec vaší aplikace tak, aby odkazovala zpátky do primární oblasti. V primární oblasti se nyní vytvoří všechny nové účty a databází tenantů. Některé stávající zákazníci zobrazit svá data, není dočasně k dispozici.   
* Nastavte všechny databáze ve fondu zotavení po Havárii jen pro čtení k zajištění, že nemůže být upraven v oblasti zotavení po Havárii (6). 
* Pro každou databázi ve fondu zotavení po Havárii, které se změnily od obnovení přejmenování nebo odstranění příslušných databází ve fondu primární (7). 
* Kopírovat aktualizované databáze z fondu zotavení po Havárii do primární fondu (8). 
* Odstranit fond zotavení po Havárii (9)

Aplikace je v tuto chvíli online v primární oblasti se všech databázích tenantů k dispozici ve primárního fondu.

![Obrázek 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Klíč **výhody** této strategie je nízké náklady na průběžnou kvůli redundanci dat vrstvy. Zálohy jsou prováděny automaticky službou SQL Database s žádné revize aplikace a bez dalších poplatků.  Náklady se účtují pouze v případě, že se obnoví elastických databází. **Kompromis** je, že dokončení obnovení všech databázích tenantů trvá déle. Dlouhá doba závisí na celkový počet obnovení spustíte v oblasti zotavení po Havárii a celkovou velikost databází tenantů. I v případě, že nad ostatními určit prioritu obnoví některé klienty, jsou konkurují všechny další obnovení, které jsou spouštěné ve stejné oblasti jako služba řeší a omezuje minimalizovat celkový dopad na stávající zákazníky databází. Obnovení databází tenantů kromě toho nelze spustit, až do vytvoření nového elastického fondu v oblasti zotavení po Havárii.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scénář 2. Až po zralé aplikace se vrstvený službou
<i>Jmenuji se až po zralé aplikaci SaaS s vrstvené služeb a různými smlouvami SLA pro zákazníky, zkušební verze a platíte zákazníků. Pro zákazníky, zkušební verze budu muset snížit náklady na co největší míře. Zkušební zákazníkům může trvat výpadek, ale chcete snížit pravděpodobnost, že jeho. Žádné výpadky pro platící zákazníky představuje riziko letu. Chci Ujistěte se, že platíte jsou vždy mít pro přístup k datům zákazníků.</i> 

Pro podporu tohoto scénáře, oddělte zkušební tenanti z placených tenantů umístit je do samostatné elastických fondů. Zkušební verze zákazníci mají nižší eDTU nebo virtuálních jader na tenanta a nižší SLA s delší dobu obnovení. Platby zákazníků jsou ve fondu s vyšší eDTU nebo virtuálních jader na tenanta a vyšší úroveň smlouvy SLA. Zajistit nejnižší čas obnovení databází tenantů platby zákazníků jsou geograficky replikovaný. Tato konfigurace je znázorněno na následující diagram. 

![Obrázek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Stejně jako v prvního scénáře jsou databáze správy poměrně aktivní, proto pro něj (1) použijete jeden geograficky replikované databáze. To zajišťuje předvídatelný výkon nových zákaznických předplatných, profil aktualizace a jiné operace správy. Oblast, ve kterém jsou umístěny primárek databáze správy je primární oblasti a oblasti, ve kterém jsou umístěny sekundárních replik databáze pro správu je oblasti zotavení po Havárii.

Databáze tenanta platby zákazníků mají aktivní databáze ve fondu "placené" zřízené v primární oblasti. Zřízení sekundární fondu se stejným názvem v oblasti zotavení po Havárii. Každý tenant je geograficky replikované sekundární fondu (2). To umožňuje rychlé obnovení všech databázích tenantů pomocí převzetí služeb při selhání. 

Pokud dojde k výpadku v primární oblasti, postup používání vaší aplikace online obnovení jsou uvedeny v následující diagram:

![Obrázek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Okamžitě převzetí služeb při selhání správy databází do oblasti pro zotavení po Havárii (3).
* Změňte připojovací řetězec vaší aplikace tak, aby odkazoval na oblasti zotavení po Havárii. Nyní všechny nové účty a databází tenantů se vytvoří v oblasti zotavení po Havárii. Stávající zákazníci zkušební zobrazit svá data, není dočasně k dispozici.
* Převzetí služeb při selhání placené tenanta databáze ve fondu v oblasti zotavení po Havárii okamžitě obnovit jejich dostupnost (4). Převzetí služeb při selhání je změna úrovně rychlé metadata, zvažte optimalizaci, ve kterém jsou jednotlivé převzetí služeb při selhání spustit na vyžádání připojení koncových uživatelů. 
* Pokud hodnota vaší sekundární fondu eDTU velikost nebo vCore byla nižší než primární, protože sekundární databáze pouze požadované kapacity pro zpracování protokolů změn, když byly sekundární databáze, okamžitě navýšení kapacity fondu tak, aby vyhovovaly úplné úlohy nyní ze všech tenantů (5). 
* Vytvoření nového elastického fondu se stejným názvem a stejnou konfiguraci v oblasti zotavení po Havárii pro databáze zkušební zákazníků (6). 
* Po vytvoření fondu zkušební zákazníkům geografické obnovení použijte k obnovení databází jednotlivých zkušební verzi tenanta do nového fondu (7). Vezměte v úvahu aktivací jednotlivé obnovení koncového uživatele připojení nebo používají některé jiné schéma priority specifické pro aplikaci.

Aplikace je v tuto chvíli zpátky do online režimu v oblasti zotavení po Havárii. Všechny platby zákazníků mají přístup ke svým datům zkušební zákazníkům zaznamenat zpoždění při přístupu k jejich datům.

Kdy obnovit primární oblast Azure *po* obnovení aplikace v oblasti zotavení po Havárii můžete pokračovat v používání aplikace v dané oblasti nebo je možné provést obnovení do primární oblasti. Je-li obnovit primární oblast *před* dokončení procesu převzetí služeb při selhání, vezměte v úvahu navrácením služeb po obnovení okamžitě. Navrácení služeb po obnovení trvá popsané postupy v následující diagram: 

![Obrázek 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Zrušte všechny zbývající geografické obnovení žádosti.   
* Převzetí služeb při selhání databáze správy (8). Po obnovení oblasti původního primárního, automaticky se stanou sekundární. Teď bude primárním znovu.  
* Převzetí služeb při selhání databáze tenantů placené (9). Podobně po obnovení oblast, stará primárek, automaticky se stanou sekundárních replik. Nyní budou primárek znovu. 
* Nastavte obnovené databáze zkušební verze, které se změnily v oblasti zotavení po Havárii na jen pro čtení (10).
* Pro každou databázi ve fondu zkušební zákazníkům zotavení po Havárii, která se změnila od obnovení přejmenování nebo odstranění odpovídající databáze ve fondu primární zkušební zákazníků (11). 
* Kopírovat aktualizované databáze z fondu zotavení po Havárii do primární fondu (12). 
* Odstranit fond zotavení po Havárii (13) 

> [!NOTE]
> Operace převzetí služeb při selhání je asynchronní. Chcete-li minimalizovat čas obnovení je důležité, spusťte příkaz převzetí služeb při selhání tenantských databázích v dávkách po nejméně 20 databází. 
> 
> 

Klíč **výhody** této strategie je, že poskytuje nejvyšší smlouvě SLA pro platící zákazníky. Také zaručuje, že nové zkušební verze blokovaná, jakmile se vytvoří zkušební verzi fondu zotavení po Havárii. **Kompromis** platí, že tento instalační program zvyšuje náklady na sekundární fond zotavení po Havárii pro celkové náklady na databázích tenantů zákazníků. Kromě toho Pokud sekundární fond má jinou velikost, platby zákazníků výkon nižší po převzetí služeb při selhání až do dokončení fondu inovace v oblasti zotavení po Havárii. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scénář 3. Geograficky distribuované aplikace se vrstvený službou
<i>Mám k dispozici až po zralé aplikaci SaaS s vrstvené služeb. Chci nabízet velmi agresivní smlouvu SLA pro placené zákazníky a minimalizovat riziko dopad na chod firmy, když dojde k výpadku, protože i stejné přerušení může způsobit, že jejich nespokojenost zákazníka. Je velmi důležité, aby platby zákazníků můžete vždy přístup k datům. Hodnocení je zdarma a nenabízí smlouvu SLA během zkušebního období. </i> 

Pro podporu tohoto scénáře, použijte tři samostatné elastických fondů. Zřízení dva stejné velikosti fondy s vysokou Edtu nebo virtuálních jader na databázi ve dvou různých oblastech tak, aby obsahovala placené zákazníkům databází tenantů. Třetí fondu, který obsahuje zkušební tenanti můžou mít nižší Edtu nebo virtuálních jader na databázi a zřídit v jednom ze dvou oblastech.

Zajistit nejnižší čas obnovení během výpadků databází tenantů platby zákazníků jsou geograficky replikované s 50 % primární databází v každé dvě oblasti. Podobně každá oblast má 50 % sekundární databází. Tímto způsobem, pokud je v režimu offline, oblast pouze 50 % placené zákazníkům databází se to týká a mají převzít služby při selhání. Ostatní databáze nijak nezmění. Tato konfigurace je znázorněn v následujícím diagramu:

![Obrázek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Jako v předchozích případech jsou poměrně aktivní správu databází tak je nakonfigurujte jako izolované geograficky replikované databáze (1). To zajišťuje předvídatelný výkon nového zákazníka, předplatná, aktualizace profilu a jiné operace správy. Oblasti A je primární oblast pro správu databází a oblast B se použije pro obnovení databází správy.

Databáze tenanta platby zákazníků jsou také geograficky replikované, ale s primárek a sekundární databáze rozdělit mezi oblast A a B (2) oblasti. Tímto způsobem primární databází tenantů vliv výpadek můžete převzetí služeb při selhání do jiné oblasti a budou k dispozici. Ostatní polovinu databází tenantů se projeví na všech. 

Následující diagram znázorňuje postup obnovení provádělo, pokud dojde k výpadku v oblasti A.

![Obrázek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Okamžitě převzetí služeb při selhání databáze správy do oblasti B (3).
* Změňte připojovací řetězec vaší aplikace tak, aby odkazoval na správu databáze v oblasti B. upravit Ujistěte se, že v oblasti B se vytvoří nové účty a databázích tenantů a existující databáze tenantů jsou v ní existuje také správu databáze. Stávající zákazníci zkušební zobrazit svá data, není dočasně k dispozici.
* Převzetí služeb při selhání databáze placené tenantů do fondu 2 v oblasti B okamžitě obnovit jejich dostupnost (4). Převzetí služeb při selhání je změna úrovně rychlé metadata, zvažte optimalizaci, ve kterém jsou jednotlivé převzetí služeb při selhání spustit na vyžádání připojení koncových uživatelů. 
* Od teď fond 2 obsahuje pouze primární databáze, celkový počet úloh ve fondu zvýší a okamžitě zvýšit jeho velikosti v jednotkách eDTU (5) nebo počet virtuálních jader. 
* Vytvoření nového elastického fondu se stejným názvem a stejnou konfiguraci v oblasti B pro zkušební zákazníkům databáze (6). 
* Po vytvoření fondu geografické obnovení použijte k obnovení databáze jednotlivé zkušební verzi klienta do fondu (7). Můžete zvážit spouštění jednotlivých obnovení připojení koncových uživatelů nebo použít některé jiné schéma priority specifické pro aplikaci.

> [!NOTE]
> Operace převzetí služeb při selhání je asynchronní. Chcete-li minimalizovat čas obnovení, je důležité, spusťte příkaz převzetí služeb při selhání tenantských databázích v dávkách po nejméně 20 databází. 
> 

V tuto chvíli je aplikace zpátky do online režimu v oblasti B. Všechny platby zákazníků mají přístup ke svým datům zkušební zákazníkům zaznamenat zpoždění při přístupu k jejich datům.

Při obnovení oblast A musíte se rozhodnout, pokud budete chtít použít oblast B pro zákazníky zkušební verze nebo navrácení služeb po obnovení pomocí fondu zkušební zákazníkům v oblasti A. Jedno kritérium může být % upravit, protože obnovení databází zkušební verzi klienta. Bez ohledu na to, že rozhodnutí budete muset znovu vyvážit placené tenantů mezi dvěma fondy. Následující obrázek znázorňuje proces, když dojde k selhání databází zkušební verzi klienta zpět do oblasti A.  

![Obrázek 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Zrušte všechny zbývající geografické obnovení žádosti o zkušební verzi fondu zotavení po Havárii.   
* Převzetí služeb při selhání databáze pro správu (8). Po obnovení oblasti původního primárního automaticky stal sekundárním. Teď bude primárním znovu.  
* Vyberte kterého placené tenanta databáze se vrátí zpátky fond 1 a zahájit převzetí služeb při selhání jejich sekundární databáze (9). Po obnovení oblasti všechny databáze ve fondu 1 automaticky stal sekundárních replik. Nyní 50 % z nich stanou primárek znovu. 
* Snížení velikosti fondu 2 původní eDTU (10) nebo počet virtuálních jader.
* Všechny sady obnovili zkušební verze databáze v oblasti B jen pro čtení (11).
* Pro každou databázi ve fondu zkušební zotavení po Havárii, které se změnily od obnovení přejmenování nebo odstranění odpovídající databáze ve fondu zkušební verze primární (12). 
* Kopírovat aktualizované databáze z fondu zotavení po Havárii do primární fondu (13). 
* Odstranit fond zotavení po Havárii (14) 

Klíč **výhody** této strategie jsou:

* Podporuje nejvíce agresivní smlouvu SLA pro platící zákazníky, protože zajistí, že kvůli výpadku nemůže mít vliv na více než 50 % databází tenantů. 
* Zaručuje, že nové zkušební verze blokovaná, jakmile je záznam pro zotavení po Havárii fondu se vytvoří během obnovení. 
* To umožňuje efektivnější využití kapacity fondu jako 50 % druhotných databází na fond 1 a fond 2 je zaručeno, že jako aktivní méně než primární databáze.

Hlavní **kompromisy** jsou:

* Operace CRUD s databázemi management mají nižší latenci pro koncové uživatele, který je připojený k oblasti A než pro koncové uživatele, který je připojený k oblasti B, jako jsou vykonávány na primární databáze správy.
* Vyžaduje složitější návrh databáze pro správu. Například každý záznam tenanta má značku umístění, které je potřeba změnit během převzetí služeb při selhání a navrácení služeb po obnovení.  
* Platby zákazníků může výkon nižší než obvykle. až do dokončení fondu inovace v oblasti B. 

## <a name="summary"></a>Souhrn
Tento článek se zaměřuje na strategie zotavení po havárii pro databázovou vrstvu používá víceklientských aplikací SaaS ISV. Strategie, kterou zvolíte je podle potřeby aplikace, jako je například obchodní model SLA chcete nabídnout zákazníkům, rozpočet omezení atd. Každý popsané strategie popisuje výhody a kompromis tak může provést informované rozhodnutí. Aplikace konkrétní pravděpodobné také dalšími komponentami Azure. Přečtěte si pokyny k jejich obchodní kontinuity podnikových procesů a orchestraci obnovení na úrovni databáze s nimi. Další informace o správě obnovení databáze aplikace v Azure, najdete v tématu [návrhu cloudových řešení pro zotavení po havárii](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Další postup
* Další informace o Azure SQL Database, automatické zálohování, naleznete v tématu [automatické zálohování SQL Database](sql-database-automated-backups.md).
* Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
* Další informace o obnovení pomocí automatizovaného zálohování, naleznete v tématu [obnovení databáze ze záloh spouštěných službou](sql-database-recovery-using-backups.md).
* Další informace o možnosti rychlejší obnovení najdete v tématu [aktivní geografickou replikaci](sql-database-geo-replication-overview.md).
* Další informace o použití automatizované zálohování pro archivaci, naleznete v tématu [kopírování databáze](sql-database-copy.md).

