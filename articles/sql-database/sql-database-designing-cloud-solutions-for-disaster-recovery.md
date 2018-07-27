---
title: Návrh globálně dostupné služby využívající Azure SQL Database | Dokumentace Microsoftu
description: Další informace o návrhu aplikace služby s vysokou dostupností pomocí Azure SQL Database.
keywords: cloudové zotavení po havárii, řešení pro zotavení po havárii, zálohování dat aplikace, geografickou replikaci, plánování obchodní kontinuity podnikových procesů
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 88a6e1a66390b2b317e1e30a71455ad693e6d7df
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264650"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Navrhování globálně dostupné služby využívající Azure SQL Database

Při vytváření a nasazování cloudových služeb s Azure SQL Database, můžete použít [převzetí služeb při selhání skupiny a aktivní geografickou replikaci](sql-database-geo-replication-overview.md) zajistit odolnost místních výpadků a katastrofických selhání. Stejné funkce můžete vytvářet globálně distribuované aplikace optimalizované pro místní přístup k datům. Tento článek popisuje běžné vzory aplikací, včetně výhody a nevýhody jednotlivých možností. 

> [!NOTE]
> Pokud používáte Premium nebo pro důležité obchodní informace databáze a elastické fondy, můžete provádět je odolné regionální výpadky převedením na konfiguraci nasazení redundantního zóny. Zobrazit [zónově redundantní databáze](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scénář 1: Použití dvou oblastech Azure pro obchodní kontinuity podnikových procesů s minimálními výpadky
V tomto scénáři aplikace mají následující vlastnosti: 
*   Aplikace je aktivní v jedné oblasti Azure
*   Vyžadovat všechny relace databáze pro čtení a zápis (RW) k datům
*   Webová vrstva a datová vrstva musí být společně umístěná, abyste snížili náklady na latenci a provoz 
*   V podstatě výpadku je vyšší riziko obchodní pro tyto aplikace než ztráty dat

V takovém případě topologie nasazení aplikace je optimalizovaná pro zpracování regionální jiného problému ovlivňujícího při převzetí služeb při selhání společně potřebovat všechny součásti aplikace. Následující diagram znázorňuje tuto topologii. Geografická redundance jsou nasazené prostředky vaší aplikace do oblasti A a B. Prostředky v oblasti B nejsou ale využít, dokud neselže oblast odpověď. Skupiny převzetí služeb při selhání je nakonfigurovaná mezi dvěma oblastmi ke správě připojení k databázi, replikace a převzetí služeb při selhání. Webová služba v obou oblastech je nakonfigurovaná pro přístup k databázi pomocí naslouchacího procesu pro čtení i zápis  **&lt;název skupiny převzetí služeb při selhání&gt;. database.windows.net** (1). Traffic manager nastaven na použití [metody prioritního směrování](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Azure traffic Manageru](../traffic-manager/traffic-manager-overview.md) slouží jen jako ukázka v tomto článku. Můžete použít řešení vyrovnávání zatížení, který podporuje metody prioritního směrování.    
>

Následující diagram znázorňuje tuto konfiguraci před výpadku:

![Scénář 1. Konfigurace před výpadek.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Po výpadku v primární oblasti služba SQL Database zjistí, že primární databáze není dostupný a aktivuje převzetí služeb při selhání sekundární oblastí na základě parametrů zásad automatické převzetí služeb při selhání (1). V závislosti na vaší smlouvě SLA aplikace můžete nakonfigurovat období odkladu, které řídí dobu mezi detekce výpadek a převzetí služeb při selhání, samotného. Je možné, že Traffic manager inicializuje převzetí služeb při selhání koncového bodu předtím, než se aktivuje převzetí služeb při selhání skupiny převzetí služeb při selhání databáze. V takovém případě webová aplikace nemůže se připojit znovu okamžitě do databáze. Ale opětovná připojení automaticky úspěšné co nejdříve po dokončení převzetí služeb při selhání databáze. Při selhání oblasti je obnovena a znovu online, původního primárního automaticky znovu připojí jako nové sekundární. Následující diagram znázorňuje konfiguraci po převzetí služeb při selhání.
 
> [!NOTE]
> Všechny transakce potvrzena po převzetí služeb se ztratí během obnovení připojení. Po dokončení převzetí služeb aplikace v oblasti B je možné znovu připojit a znovu spusťte zpracování požadavků uživatele. Webová aplikace i primární databáze jsou teď v oblasti B a zůstat společně umístěné. n>

![Scénář 1. Konfigurace po převzetí služeb při selhání](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Pokud dojde k výpadku v oblasti B, pozastaven proces replikace mezi primární a sekundární databáze, ale propojení mezi těmito dvěma zůstává beze změny (1). Přenos spravovaných zjistí, že připojení k oblasti B je porušený a označí koncový bod webové aplikace 2 jako snížený (2). Není v tomto případě dopad na výkon vaší aplikace, ale databáze bude vystavena a proto vystavený zvýšenému riziku ztráty dat v případě oblast A vyvolá chybu v daný okamžik.

> [!NOTE]
> Pro zotavení po havárii doporučujeme, abyste konfiguraci s omezenou nasazení aplikace do dvou oblastí. Je, že většina zeměpisných oblastech Azure pouze dvě oblasti. Tato konfigurace nechrání vaší aplikace z katastrofických selhání z obou oblastí. V nepravděpodobném případě takové selhání, můžete obnovit databáze v třetí oblasti pomocí [geografické obnovení operace](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Jakmile se výpadek je zmírnit, sekundární databáze automaticky znovu synchronizují s aplikací primární. Při synchronizaci může mít dopad na výkon primární. Konkrétní dopad závisí na množství dat nový primární získaných od převzetí služeb při selhání. Následující diagram ukazuje výpadek v sekundární oblasti:

![Scénář 1. Konfigurace po výpadku v sekundární oblasti.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Klíč **výhody** tohoto vzoru návrhu jsou:

* Stejnou webovou aplikaci nasadíte pro obě oblasti bez konfigurace specifické pro oblast a nevyžaduje další logiku ke správě převzetí služeb při selhání. 
* Výkon aplikace není ovlivněn převzetí služeb při selhání jako webová aplikace a databáze jsou vždy umístěny společně.

Hlavní **kompromis** je, že aplikace prostředků v oblasti B by byla málo využitá ve většině případů.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scénář 2: Oblastech Azure pro zajištění obchodní kontinuity maximální množství dat a zachovávání s rozlišením
Tato možnost je nejvhodnější pro aplikace s následujícími charakteristikami:

* Jakoukoli ztrátu dat, je vysoký obchodní rizika. Převzetí služeb při selhání databázi jde použít jenom jako poslední možnost je-li výpadek závažnému selhání.
* Aplikace podporuje jen pro čtení a pro čtení i zápis režimy operace a mohou pracovat v "režimu jen pro čtení" pro určitou dobu.

V tomto vzoru aplikace se přepne do režimu jen pro čtení při připojení ke čtení a zápis začít využívat chyby časového limitu. Webová aplikace je nasazená do obou oblastí a zahrnují připojení ke koncovému bodu naslouchacího procesu pro čtení i zápis a jiné připojení na koncový bod naslouchacího zápisu jen pro čtení (1). Profil služby Traffic manager používejte [priority směrování](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Koncový bod monitorování](../traffic-manager/traffic-manager-monitoring.md) byste měli povolit pro koncový bod aplikace v jednotlivých oblastech (2).

Následující diagram znázorňuje tuto konfiguraci před výpadku:

![Scénář 2. Konfigurace před výpadek.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Když traffic manager zjistí selhání připojení k oblasti A, automaticky se přepne provoz uživatelů na instanci aplikace v oblasti B. Tento model je důležité nastavit období odkladu se ztrátou dat na hodnotu dostatečně vysokou, například 24 hodin. Zajišťuje, že je zabráněno ztráty dat, pokud výpadek vyřešení za tu dobu. Když aktivujete webovou aplikaci v oblasti B začít operací čtení a zápis, služeb při selhání. V tomto okamžiku by měla přepnutí do režimu jen pro čtení (1). V tomto režimu se automaticky směrují požadavky na sekundární databázi. Je-li výpadek závažnému selhání, pravděpodobně ji není možné řešit v období odkladu. Jeho vypršení platnosti aktivace skupiny převzetí služeb při selhání převzetí služeb při selhání. Za naslouchací proces pro čtení a zápis k dispozici a připojení k němu zastavení služeb při selhání (2). Následující diagram znázorňuje dvě fáze procesu obnovení.

> [!NOTE]
> Pokud výpadku v primární oblasti je v období odkladu zmírnit, traffic manager zjistí obnovení připojení v primární oblasti a uživatelský provoz přepne zpět do instance aplikace v oblasti A. Tuto instanci aplikace bude pokračovat a funguje v režimu čtení a zápis v oblasti A jak je znázorněno v předchozím diagramu primární databáze.
>

![Scénář 2. Fáze pro zotavení po havárii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Pokud dojde k výpadku v oblasti B, traffic manager zjistí selhání koncového bodu web-app-2 v oblasti B a značky degradovaný (1). Do té doby se skupiny převzetí služeb při selhání přepne jen pro čtení naslouchací proces na oblast A (2). Tento výpadek nemá vliv na prostředí koncového uživatele, ale primární databáze je přístupný během výpadku. Následující diagram znázorňuje selhání v sekundární oblasti:

![Scénář 2. Výpadek sekundární oblasti.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Jakmile se výpadek je zmírnit, sekundární databáze se okamžitě synchronizovány s primární a naslouchacího zápisu jen pro čtení přepne zpět do sekundární databáze v oblasti B. Během synchronizace primární může být mírně dopad na výkon v závislosti na množství dat, která musí být synchronizovány.

Tento vzor návrhu víme o několika **výhody**:

* Předchází ztrátě dat během dočasných výpadků.
* Výpadek závisí jenom na rychlost traffic manager zjistí selhání připojení, která je konfigurovatelná.

**Kompromis** je, že aplikace musí mít možnost pracovat v režimu jen pro čtení.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scénář 3: Aplikace přemístění do jiné zeměpisné oblasti bez ztráty dat a téměř nulovými výpadky 
V tomto scénáři aplikace má následující vlastnosti: 
* Koncoví uživatelé přístup k aplikaci z různých zeměpisných oblastech
* Aplikace obsahuje úlohy jen pro čtení, které nezávisí na úplnou synchronizaci s nejnovějšími aktualizacemi
* Ve stejné zeměpisné oblasti pro většinu uživatelů by měla podporovat zápis přístup k datům 
* Latence pro čtení je velmi důležité pro činnost koncového uživatele 


Chcete-li splnit tyto požadavky je nutné zaručit, že uživatel zařízení **vždy** připojí k aplikaci nasazenou ve stejné zeměpisné oblasti jen pro čtení operací, jako je procházení dat analytics atd. Vzhledem k tomu OLTP operací zpracování ve stejné zeměpisné oblasti **většinu času**. Například v době den OLTP operací zpracování ve stejné zeměpisné oblasti, ale během hodin mimo jejich nebylo možné zpracovat v jiném datovém typu geography. Pokud činnost koncového uživatele se většinou dojde během pracovní doby, můžete zajistit optimální výkon pro většinu uživatelů většinu času. Následující diagram znázorňuje tuto topologii. 
 
Prostředky aplikace by měly být nasazeny v každé zeměpisné oblasti, kde mají podstatné využití nároky. Například pokud vaše aplikace se aktivně používá ve Spojených státech amerických, Evropské unie a jihovýchodní Asie aplikace by měly být nasazeny všechny tyto geografické oblasti. Primární databáze by možné dynamicky přepnout z jedné zeměpisné oblasti na další na konec pracovní doby. Tato metoda je volána "sledovat slunce". Úlohy OLTP vždy připojí k databázi pomocí naslouchacího procesu pro čtení i zápis  **&lt;název skupiny převzetí služeb při selhání&gt;. database.windows.net** (1). Úlohy jen pro čtení připojí k místní databázi přímo pomocí koncového bodu serveru databáze  **&lt;název serveru&gt;. database.windows.net** (2). Má nakonfigurovanou Traffic Manageru [metody směrování podle výkonu](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Zajišťuje, že zařízení koncového uživatele je připojena k webové službě v nejbližší oblasti. Traffic Manageru byste nastavit s koncový bod monitorování povoleno pro každou webovou službu koncový bod (3).

> [!NOTE]
> Konfigurace skupiny převzetí služeb při selhání definuje, jakou oblast se používá pro převzetí služeb při selhání. Vzhledem k tomu, že nový primární je v jiném datovém typu geography převzetí služeb při selhání má za následek delší latence pro OLTP a úlohy jen pro čtení, dokud je ovlivněné oblasti zpátky do online režimu.
>

![Scénář 3. Konfigurace s primárním v oblasti východní USA.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Na konci dne (například ve 23: 00 místního času) by měl přepnout aktivní databáze do další oblast (Severní Evropa). Tuto úlohu lze plně automatizovat využitím [Azure službu plánování](../scheduler/scheduler-intro.md).  Úloha zahrnuje následující kroky:
* Přepnout primární server ve skupině převzetí služeb při selhání na Severní Evropa pomocí popisný převzetí služeb při selhání (1)
* Odebrat skupinu převzetí služeb při selhání mezi USA – východ a Severní Evropa
* Vytvořte novou skupinu převzetí služeb při selhání se stejným názvem, ale mezi Severní Evropa a východní Asie (2). 
* Přidejte primární do této skupiny převzetí služeb při selhání (3) v oblasti Severní Evropa a sekundární ve východní Asie.


Následující diagram znázorňuje novou konfiguraci po plánované převzetí služeb při selhání:

![Scénář 3. Přechod od primární severní Evropě.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Pokud kvůli výpadku dojde například v oblasti Severní Evropa, převzetí služeb při selhání automatické databáze inicializuje skupiny převzetí služeb při selhání, což účinně vede k přesunutí aplikace k další oblasti plánem (1).  V takovém případě USA – východ je jediný zbývající sekundární oblasti, dokud je zpátky do online režimu Severní Evropa. Zbývající dvě oblasti poskytovat zákazníkům ve všech zeměpisných oblastech tři přepnutím role. Azure scheduler musí být upraven. Protože zbývající oblasti získáte další uživatelský provoz z Evropy výkon vaší aplikace to má vliv nejen službou další latence, ale také navýšení počtu připojení koncového uživatele. Po vyřešení výpadek v oblasti Severní Evropa, sekundární databáze se okamžitě synchronizuje s aktuální primární. Následující diagram znázorňuje výpadku v oblasti Severní Evropa:

![Scénář 3. Výpadek v oblasti Severní Evropa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Můžete zkrátit dobu, kdy má snížený výkon činnost koncového uživatele v Evropě tak dlouho latence. K tomu je by měl proaktivně nasadit jako kopie aplikace a vytvořte sekundární databáze v jiné místní oblasti (západní Evropa) jako náhrada za instanci offline aplikace v oblasti Severní Evropa. Při návrhu je zpátky do online režimu, můžete se rozhodnout, zda chcete pokračovat v používání západní Evropa nebo odebrat kopii tam a přejít zpět na Severní Evropa
>

Klíč **výhody** tohoto návrhu jsou:
* Úlohy jen pro čtení aplikace přistupuje k datům v oblasti skříně za všech okolností. 
* Úlohy čtení a zápis aplikace přistupuje k datům v nejbližší oblasti během období nejvyšší aktivity v každé zeměpisné oblasti
* Protože aplikace je nasazená do více oblastí, přežijí ke ztrátě oblastech bez žádné významné výpadky. 

Zde jsou některé, ale **kompromisy**:
* K oblastnímu výpadku výsledků v zeměpisné oblasti ovlivní delší latence. Obsluhuje úlohy čtení a zápis i čtení aplikací v jiném datovém typu geography. 
* Úlohy jen pro čtení musí připojit k jiné koncový bod v jednotlivých oblastech. 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Plánování obchodní kontinuity podnikových procesů: Zvolte návrh aplikace pro zotavení po havárii cloudu
Strategie zotavení po havárii konkrétní cloud můžete kombinovat nebo rozšířit tyto způsoby návrhu, které nejvíc vyhovují potřebám vaší aplikace.  Jak už bylo zmíněno dříve, strategie, kterou zvolíte je založená na smlouvě SLA chcete nabízet vaše zákazníky a topologie nasazení aplikace. Mohou pomoci rozhodnout, následující tabulka porovnává možnosti na základě plánovaného bodu obnovení (RPO) a odhadovaný čas obnovení (ERT).

| Vzor | CÍL BODU OBNOVENÍ | ERT |
|:--- |:--- |:--- |
| Aktivní pasivní nasazení pro zotavení po havárii pomocí společně umístěné databáze access |Přístup pro čtení a zápis < 5 s |Čas detekce selhání a hodnotu TTL pro DNS |
| Aktivní aktivní nasazení pro vyrovnávání zatížení aplikací |Přístup pro čtení a zápis < 5 s |Čas detekce selhání a hodnotu TTL pro DNS |
| Aktivní pasivní nasazení pro uchovávání dat |Přístup jen pro čtení < 5 s | Přístup jen pro čtení = 0 |
||Přístup pro čtení a zápis = nula | Přístup pro čtení a zápis = čas detekce selhání a období odkladu se ztrátou dat |
|||

## <a name="next-steps"></a>Další postup
* Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md)
* Další informace o skupinách geografickou replikaci a převzetí služeb při selhání najdete v tématu [aktivní geografické replikace](sql-database-geo-replication-overview.md)  
* Informace o aktivní geografickou replikaci s Elastickými fondy najdete v tématu [strategie zotavení po havárii Elastických fondů](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
