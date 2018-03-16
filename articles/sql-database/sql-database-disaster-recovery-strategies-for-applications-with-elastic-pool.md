---
title: "Návrh řešení zotavení po havárii – Azure SQL Database | Microsoft Docs"
description: "Informace o návrhu cloudové řešení pro zotavení po havárii výběrem vzoru správné převzetí služeb při selhání."
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 03/05/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 6ec202237a0b3fb1b7f0b7158c0aa454b4d65770
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Strategie zotavení po havárii pro aplikace pomocí fondů elastické databáze SQL
V průběhu let, které jsme se dozvěděli cloudové služby nejsou spolehlivá a závažné incidenty dojít. Databáze SQL obsahuje několik funkcí, které poskytují pro kontinuitu podnikových procesů vaší aplikace, když dojde k tyto incidenty. [Elastické fondy](sql-database-elastic-pool.md) a jedné databáze podporují stejný druh možnosti obnovení po havárii. Tento článek popisuje několik strategie zotavení po Havárii pro elastických fondů, které využívají tyto funkce kontinuity obchodních databáze SQL.

Tento článek používá následující kanonický vzor aplikací SaaS ISV:

<i>Moderní cloudové webovou aplikaci zřídí jedna databáze SQL pro každý koncový uživatel. ISV má mnoho zákazníků a proto používá mnoho databází, označuje jako databáze klienta. Vzhledem k tomu, že databáze klienta obvykle mají vzorce nepředvídatelné aktivity, ISV pomocí fondu elastické databáze po dlouhou dobu učinit velmi předvídatelný databázi náklady. Elastický fond také zjednodušuje správu výkonu, pokud aktivita uživatele špičky. Kromě databáze klienta aplikace také používá několik databází k spravovat profily uživatelů, zabezpečení, shromažďování vzorce používání atd. Dostupnost jednotlivé klienty, které nemá negativní vliv na dostupnost aplikace jako celek. Ale dostupnost a výkon databáze správy je velmi důležitá pro funkce aplikace a pokud jsou databáze správy offline bude celá aplikace je offline.</i>  

Tento článek popisuje zahrnující celou řadu scénářů z aplikací citlivé spuštění náklady na těch, které jsou s požadavky na přísné dostupnosti strategie zotavení po Havárii.

> [!NOTE]
> Pokud používáte Premium databáze a fondy, můžete provést je odolný regionální výpadkům převede konfigurace redundantní nasazení zóny (momentálně ve verzi preview). V tématu [Zónově redundantní databáze](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scénář 1. Náklady citlivé spuštění
<i>Jsem obchodní spuštění a mě náklady velmi citlivé.  Chcete zjednodušit nasazení a správu aplikace a mám omezené SLA pro jednotlivé zákazníky. Ale chceme se ujistit aplikaci jako celek se nikdy offline.</i>

Vyhovět požadavek na jednoduchost, nasaďte všechny databáze klienta do jednoho elastického fondu v oblasti Azure podle svého výběru a nasazení správy databáze jako geograficky replikované izolované databáze. Pro zotavení po havárii klientům používejte geografické obnovení, který se dodává bez dalších poplatků. K zajištění dostupnosti databází správy, geografické replikace je do jiné oblasti auto-převzetí služeb při selhání skupiny (v preview) (krok 1). Probíhající náklady na konfiguraci obnovení po havárii v tomto scénáři se rovná celkové náklady na sekundárních databází. Tato konfigurace je znázorněna v další diagramu.

![Obrázek 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Pokud dojde k výpadku v primární oblasti, postup obnovení, aby vaše aplikace online jsou zobrazené ve další diagramu.

* Převzetí služeb při selhání skupiny zahájí automatické převzetí služeb při selhání databázi pro správu pro danou oblast zotavení po Havárii. Aplikace automaticky znovu připojí k nové primární a všechny nové účty a vytvoří se v oblasti zotavení po Havárii databází klienta. Stávající zákazníky zobrazit tato data není dočasně k dispozici.
* Vytvořte elastický fond se stejnou konfigurací jako původní fond (2).
* Použijte geografické obnovení k vytvoření kopie klienta databází (3). Můžete zvážit spouštění jednotlivých obnovení koncového uživatele připojeními nebo použít některé další schéma s prioritou specifické pro aplikaci.


V tomto okamžiku aplikace je zpět do online režimu v oblasti zotavení po Havárii, ale někteří zákazníci zaznamenat zpoždění při přístupu k jejich datům.

![Obrázek 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Pokud se výpadek byl dočasné, je možné, že primární oblasti budou obnovena Azure předtím, než všechny databáze obnovení jsou dokončeny v oblasti zotavení po Havárii. V takovém případě orchestraci přesouvání aplikace zpět na primární oblasti. Tento proces trvat zobrazené v diagramu další kroky.

* Zrušte všechny zbývající geografické obnovení žádosti.   
* Převzít správu databáze primární oblasti (5). Po obnovení oblasti původní základní barvy automaticky se sekundární databáze. Nyní se přepínat role znovu. 
* Změňte řetězec připojení aplikace přejděte zpět na primární oblasti. Nyní všechny nové účty a databáze klienta se vytvoří v primární oblasti. Některé stávající zákazníky služby zobrazit tato data není dočasně k dispozici.   
* Nastavte všechny databáze ve fondu zotavení po Havárii jen pro čtení k zajištění, že je nelze změnit v oblasti zotavení po Havárii (6). 
* Pro každou databázi ve fondu zotavení po Havárii, který se změnil od obnovení přejmenovat nebo odstranit příslušné databáze ve fondu primární (7). 
* Zkopírujte aktualizované databází z fondu zotavení po Havárii primární fondu (8). 
* Odstranění fondu zotavení po Havárii (9)

Aplikace je nyní online v primární oblasti s všechny databáze klienta k dispozici ve fondu primární.

![Obrázek 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Klíč **těžit** tuto strategii je nízké náklady probíhající pro redundanci dat vrstvy. Ve službě SQL Database s bez přepsání aplikace a bez dalších poplatků jsou automaticky provedeny zálohy.  Náklady nabíhají jenom v případě, že jsou obnoveny elastické databáze. **Kompromis** je, že dokončení obnovení všech databází klienta trvá déle. Doba, závisí na celkový počet obnovení spustíte v oblasti zotavení po Havárii a celková velikost databází klienta. I když některé klienty obnovení určit prioritu před jinými, vzájemně neslučitelných s všechny ostatní obnovení, které jsou spouštěné ve stejné oblasti jako služba řeší a omezí generovaný minimalizovat dopad na celkový pro stávající zákazníky databáze. Kromě toho obnovení databází klienta nelze spustit, dokud se vytvoří nový elastický fond v oblasti zotavení po Havárii.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scénář 2. Vyspělá aplikace vrstvené službou
<i>Jsem vyspělá aplikace SaaS s nabídek vrstvené služby a jiné smlouvy SLA pro zkušební zákazníků a platícího zákazníků. Zkušební verze zákazníků nutné snížit náklady na co nejvíc. Zkušební verze zákazníkům může trvat výpadek, ale chcete snížit jeho pravděpodobnost. Pro platící zákazníky žádné výpadky je riziko letu. Tak chcete Ujistěte se, že platícího zákazníci mohou vždy přístup k datům.</i> 

Pro podporu tohoto scénáře, oddělte zkušební klienty z placené klienty vložit do samostatné elastické fondy. Zkušební verze zákazníci mají nižší eDTU na klienta a nižší smlouvy SLA s delší dobu obnovení. Platící zákazníky jsou ve fondu s vyšší eDTU na klienta a vyšší SLA. Pokud chcete zajistit nejnižší čas obnovení, jsou platící zákazníky klienta databáze, geograficky replikované. Tato konfigurace je znázorněna v další diagramu. 

![Obrázek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Jako prvního scénáře jsou velmi aktivní databáze správy, takže použití jedné geograficky replikované databáze pro něj (1). Tím se zajistí předvídatelný výkon pro nové předplatné zákazníka, profil aktualizace a jiné operace správy. Oblast, ve kterém jsou umístěny základní barvy databází správy je primární oblasti a oblast, ve kterém jsou umístěny sekundárních databází správy je oblast zotavení po Havárii.

Databáze klienta platící zákazníky mít aktivní databáze ve fondu "placené" zřízené v primární oblasti. Zřídit sekundární fond se stejným názvem v oblasti zotavení po Havárii. Každý klient je geograficky replikované sekundární fondu (2). To umožňuje rychlé obnovení všech databází klienta převzetí služeb při selhání. 

Pokud dojde k výpadku v primární oblasti, postup obnovení, aby vaše aplikace online jsou zobrazené v další diagramu:

![Obrázek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Okamžitě převzetí služeb při selhání databáze správy oblasti zotavení po Havárii (3).
* Změňte připojovací řetězec aplikace tak, aby odkazoval na oblasti zotavení po Havárii. Nyní všechny nové účty a databáze klienta se vytvoří v oblasti zotavení po Havárii. Stávající zkušební zákazníky zobrazit tato data není dočasně k dispozici.
* Selhání klienta placené databáze ve fondu v oblasti zotavení po Havárii okamžitě obnovit jejich dostupnost (4). Vzhledem k tomu, že převzetí služeb při selhání je rychlý Změna úrovně, zvažte optimalizaci kde jednotlivé převzetí služeb při selhání jsou aktivovány na vyžádání připojení koncových uživatelů. 
* Pokud velikost vašeho sekundární fondu eDTU byl nižší než primární, protože sekundární databáze pouze požadované kapacitu pro zpracovat protokol změn během byly sekundární repliky, okamžitě zvýšení kapacity fondu teď zohlednit úplné zatížení všech klientů (5). 
* Vytvořte nový elastický fond se stejným názvem a stejnou konfiguraci v oblasti zotavení po Havárii pro databáze zkušební zákazníků (6). 
* Jakmile je vytvořen fond zkušební zákazníků, použijte k obnovení databáze jednotlivých zkušební verzi klienta do nového fondu (7) geografické obnovení. Zvažte spouštění jednotlivých obnovení koncového uživatele připojeními nebo používat některé další schéma s prioritou specifické pro aplikaci.

Aplikace je v tomto okamžiku zpět do online režimu v oblasti zotavení po Havárii. Všechny platící zákazníky mít přístup k jejich datům během zkušební zákazníků zaznamenat zpoždění při přístupu k jejich datům.

Když je obnovit primární oblasti Azure *po* jste obnovili aplikaci v oblasti zotavení po Havárii můžete pokračovat v používání aplikace v této oblasti nebo můžete rozhodnout pro navrácení služeb po obnovení primární oblasti. Pokud je obnovit primární oblast *před* po dokončení převzetí služeb při selhání, zvažte selhání zpět hned. Navrácení služeb po obnovení učiní znázorněné na obrázku další kroky: 

![Obrázek 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Zrušte všechny zbývající geografické obnovení žádosti.   
* Převzít správu databází (8). Po obnovení oblasti z původního primárního automaticky stane sekundární. Nyní bude primární znovu.  
* Selhání databáze placené klienta (9). Po obnovení oblasti, je podobně původní základní barvy automaticky stane sekundárních databází. Nyní budou primárních barev znovu. 
* Nastavte obnovené zkušební databáze, které se změnily v oblasti zotavení po Havárii na jen pro čtení (10).
* Pro každou databázi ve fondu zkušební zákazníkům zotavení po Havárii, který změnil od obnovení přejmenovat nebo odstranit příslušné databáze ve fondu primární zkušební zákazníků (11). 
* Zkopírujte aktualizované databází z fondu zotavení po Havárii primární fondu (12). 
* Odstranění fondu zotavení po Havárii (13) 

> [!NOTE]
> Operace převzetí služeb při selhání je asynchronní. Chcete-li minimalizovat čas obnovení je důležité, možné provést příkaz převzetí služeb při selhání se databáze klienta, a to v dávkách aspoň 20 databází. 
> 
> 

Klíč **těžit** této strategie je, že poskytuje nejvyšší smlouvě SLA pro platící zákazníky. Také zaručuje, že nový zkušební verze jsou odblokuje, jakmile je vytvořen fond zkušební zotavení po Havárii. **Kompromis** je, že tato instalace zvyšuje celkové náklady na klienta databází náklady na sekundární fond zotavení po Havárii pro placené zákazníků. Kromě toho Pokud sekundární fondu má různou velikost, platící zákazníky zaznamenat snížení výkonu po převzetí služeb při selhání až do dokončení upgradu fondu v oblasti zotavení po Havárii. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scénář 3. Geograficky distribuovaná aplikace s vrstvami služby
<i>Je nutné vyspělá aplikace SaaS s nabídkami vrstvené služby. Chcete nabízet velmi agresivní SLA Moje placené zákazníkům a minimalizovat riziko dopad, když dojde k výpadku, protože i stručný přerušení může způsobit nespokojenosti zákazníka. Je důležité, aby platící zákazníky můžete vždy přístup k datům. Zkušební verze jsou zdarma a SLA není nabídnuta během zkušebního období. </i> 

Pro podporu tohoto scénáře, použijte tři samostatné elastické fondy. Zřídit dvě stejná velikost fondy s vysokou Edtu na databázi ve dvou různých oblastech tak, aby obsahovala databáze klienta placené zákazníků. Třetí fondu obsahujícího zkušební klienty může mít nižší počet jednotek Edtu na databázi a zřídit v jednom ze dvou oblastí.

Pokud chcete zajistit nejnižší obnovení době výpadky, jsou platící zákazníky klienta databáze, geograficky replikované 50 % primární databází v těchto dvou oblastech. Podobně každou oblast má 50 % sekundární databáze. Tímto způsobem, pokud oblast je v režimu offline, jen 50 % placené zákazníkům databáze dopad a mít k převzetí služeb při selhání. Ostatní databáze zůstanou beze změn. Tato konfigurace je znázorněno v následujícím diagramu:

![Obrázek 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Jako v předchozích případech jsou velmi aktivní databáze správy, nakonfigurujte je jako jeden geograficky replikované databáze (1). To zajišťuje předvídatelný výkon nového zákazníka odběry, profil aktualizace a jiné operace správy. Primární oblasti pro správu databáze, je oblast A a B oblast se používá pro obnovení databází správy.

Databáze platící zákazníky klienta jsou taky geograficky replikované, ale s základní barvy a sekundárních rozdělit mezi oblast A a B (2) oblast. Tímto způsobem primární databáze klienta se výpadek vliv můžete převzetí služeb při selhání jiné oblasti a k dispozici. Ostatní polovinu databáze klienta nejsou vůbec dopad. 

Další diagram ukazuje postup obnovení v případě, že dojde k výpadku v oblasti A.

![Obrázek 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Okamžitě převzetí služeb při selhání databáze správy oblasti B (3).
* Změňte připojovací řetězec aplikace přejděte na správu databází v oblasti B. upravit zajistěte, aby nové účty a databáze klienta se vytvoří v oblasti B a stávající databáze klienta jsou v ní existuje také správu databáze. Stávající zkušební zákazníky zobrazit tato data není dočasně k dispozici.
* Selhání placené klienta databází do fondu 2 v oblasti B okamžitě obnovit jejich dostupnost (4). Vzhledem k tomu, že převzetí služeb při selhání je rychlý Změna úrovně, zvažte optimalizaci kde jednotlivé převzetí služeb při selhání jsou aktivovány na vyžádání připojení koncových uživatelů. 
* Od nyní fond 2 obsahuje pouze primární databáze, celkový počet úloh ve fondu zvyšuje a můžete okamžitě zvětšete jeho velikost eDTU (5). 
* Vytvořte nový elastický fond se stejným názvem a stejnou konfiguraci v oblasti B pro zkušební zákazníkům databáze (6). 
* Po vytvoření fondu pomocí geografické obnovení obnovte databázi jednotlivé zkušební verzi klienta do fondu (7). Můžete zvážit spouštění jednotlivých obnovení koncového uživatele připojeními nebo použít některé další schéma s prioritou specifické pro aplikaci.

> [!NOTE]
> Operace převzetí služeb při selhání je asynchronní. Chcete-li minimalizovat dobu obnovení, je důležité, možné provést příkaz převzetí služeb při selhání se databáze klienta, a to v dávkách aspoň 20 databází. 
> 

V tomto okamžiku aplikace je zpět do online režimu v oblasti B. Všechny platící zákazníky mít přístup k jejich datům během zkušební zákazníků zaznamenat zpoždění při přístupu k jejich datům.

Když je obnovena oblast A musíte se rozhodnout, pokud budete chtít použít oblast B pro zkušební zákazníky nebo navrácení služeb po obnovení pomocí fondu zkušební zákazníků v oblasti A. Jedno kritérium může být % změnil od obnovení databází zkušební verzi klienta. Bez ohledu na to, že rozhodnutí budete muset znovu vyvážit placené klienty mezi dvěma fondy. Následující obrázek znázorňuje proces při navrácení databáze zkušební verzi klienta k oblasti A.  

![Obrázek 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Zrušte všechny zbývající geografické obnovení žádosti o zkušební verzi fondu zotavení po Havárii.   
* Selhání databázi pro správu (8). Po obnovení oblasti z původního primárního se automaticky sekundární. Nyní bude primární znovu.  
* Vyberte, které placené klienta databází navrácení služeb po obnovení fond 1 a inicializovat převzetí služeb při selhání jejich sekundární repliky (9). Po obnovení oblasti jsou všechny databáze ve fondu 1 automaticky sekundární repliky. Nyní 50 % z nich stanou základní barvy znovu. 
* Snižte velikost fondu 2 na původní eDTU (10).
* Všechny sady obnovit zkušební databází v oblasti B do jen pro čtení (11).
* Pro každou databázi ve fondu zkušební zotavení po Havárii, který se změnil od obnovení přejmenovat nebo odstranit příslušné databáze ve fondu zkušební primární (12). 
* Zkopírujte aktualizované databází z fondu zotavení po Havárii primární fondu (13). 
* Odstranění fondu zotavení po Havárii (14) 

Klíč **výhody** této strategie jsou:

* Podporuje nejvíce agresivní smlouvě SLA pro platící zákazníky, protože zajišťuje, že výpadek nemůže mít vliv na více než 50 % databáze klienta. 
* Zaručuje, že nový zkušební verze jsou odblokuje, jakmile záznam zotavení po Havárii fondu se vytvoří během obnovení. 
* Umožňuje efektivnější využití kapacity fondu jako 50 % sekundární databází ve fondu 1 a fond 2 je zaručena active méně než primární databáze.

Hlavní **kompromis** jsou:

* Operace CRUD s databázemi správu mají nižší latenci pro koncové uživatele, který je připojen k oblasti A než pro koncové uživatele, který je připojen k oblasti B, jako jsou prováděna na primární databází správy.
* To vyžaduje složitější návrhu databázi pro správu. Každý záznam klienta má například značky umístění, která je potřeba změnit během převzetí služeb při selhání a navrácení služeb po obnovení.  
* Platící zákazníků může zaznamenat snížení výkonu než obvykle, dokud se nedokončí fondu inovace v oblasti B. 

## <a name="summary"></a>Souhrn
Tento článek se zaměřuje na strategie zotavení po havárii pro databázové vrstvy používá víceklientské aplikace SaaS ISV. Strategie, kterou zvolíte podle potřeby aplikaci, například obchodní model SLA, kterou chcete nabídnout zákazníkům, rozpočet omezení atd. Každý popisuje strategii popisuje výhody a kompromis tak může provést informované rozhodnutí. Konkrétní aplikace pravděpodobně také dalšími součástmi Azure. Proto zkontrolujte své firmy kontinuity pokyny a orchestraci obnovení databázové vrstvy s nimi. Další informace o správě obnovení databáze aplikace v Azure, najdete v tématu [návrhu cloudové řešení pro zotavení po havárii](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Další postup
* Další informace o Azure SQL Database automatizované zálohování najdete v tématu [automatizované zálohování SQL Database](sql-database-automated-backups.md).
* Přehled kontinuity obchodních a scénářů najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md).
* Další informace o použití automatizované zálohování pro obnovení, najdete v části [obnovit databázi ze zálohy spouštěná služba](sql-database-recovery-using-backups.md).
* Další informace o možnosti rychlejší obnovení najdete v tématu [aktivní geografickou replikací](sql-database-geo-replication-overview.md).
* Další informace o použití automatizované zálohování pro archivaci, najdete v části [kopírování databáze](sql-database-copy.md).

