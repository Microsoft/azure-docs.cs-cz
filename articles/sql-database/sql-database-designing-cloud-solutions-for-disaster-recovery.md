---
title: Návrh službu s vysokou dostupností pomocí Azure SQL Database | Microsoft Docs
description: Další informace o návrhu aplikace pro používání Azure SQL Database služeb s vysokou dostupností.
keywords: cloud zotavení po havárii, řešení zotavení po havárii, zálohování dat aplikace, geografická replikace, obchodní kontinuity plánování
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: d19087743740799ec9972bed7a602073afea9f26
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Navrhování služeb s vysokou dostupností pomocí Azure SQL Database

Při vytváření a nasazování služeb s vysokou dostupností v databázi SQL Azure, je použít [převzetí služeb při selhání skupiny a aktivní geografickou replikací](sql-database-geo-replication-overview.md) zajistit odolnost proti místní výpadky a závažné selhání. Taky umožňuje rychlé obnovení do sekundární databáze. Tento článek se zaměřuje na běžných vzorů aplikace a popisuje výhody a kompromis jednotlivých možností. Informace o aktivní geografickou replikaci s elastické fondy najdete v tématu [strategie zotavení po havárii elastický fond](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

> [!NOTE]
> Pokud používáte Premium nebo kritické obchodní (preview) databáze i elastické fondy, můžete je provést odolné vůči regionální výpadků převede konfigurace redundantní nasazení zóny (momentálně ve verzi preview). V tématu [Zónově redundantní databáze](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scénář 1: Použití dvou oblastí Azure pro kontinuitu podnikových procesů s minimálními výpadky
V tomto scénáři aplikací mít následující vlastnosti: 
*   Aplikace je aktivní v jedné oblasti Azure
*   Všechny relace databáze vyžaduje oprávnění ke čtení a zápisu (RW) na data
*   Webová vrstva a datové vrstvy musí být společně umístěná, na snížení nákladů na latenci a provoz 
*   Zásadně výpadku je vyšší riziko obchodní pro tyto aplikace než ztrátě dat.

Topologie nasazení aplikace v tomto případě je optimalizovaná pro zpracování místní havárie, pokud se všechny součásti aplikace musí převzetí služeb při selhání společně. Následující diagram znázorňuje Tato topologie. Geografická redundance jsou prostředky aplikace nasazené do oblasti A a B. Prostředky v oblasti B nejsou však použít, dokud neselže oblasti A. Skupinu převzetí služeb při selhání je nakonfigurován mezi dvěma oblastmi ke správě připojení k databázi, replikace a převzetí služeb při selhání. Webová služba v obou oblastech je nakonfigurovaná pro přístup k databázi přes naslouchací proces pro čtení a zápis  **&lt;název skupiny pro převzetí služeb při selhání&gt;. database.windows.net** (1). Správce provozu je nastavit tak, aby použít [metody směrování s prioritou](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Azure traffic Manageru](../traffic-manager/traffic-manager-overview.md) se používá v tomto článku obrázek pouze pro účely. Můžete použít jakéhokoli řešení vyrovnávání zatížení, které podporuje metody směrování s prioritou.    
>

Následující diagram znázorňuje tuto konfiguraci před výpadek:

![Scénář 1. Konfigurace před se výpadek.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Po výpadku v primární oblasti služba SQL Database zjistí, že není dostupný primární databázi a aktivuje převzetí služeb při selhání pro sekundární oblast na základě parametrů zásady automatické převzetí služeb při selhání (1). V závislosti na vaší smlouvě SLA aplikace můžete nakonfigurovat období odkladu, které řídí čas mezi detekce se výpadek a převzetí služeb při selhání, sám sebe. Je možné, že traffic Manageru zahájí převzetí služeb při selhání koncový bod před převzetí služeb při selhání skupina aktivuje převzetí služeb při selhání databáze. V takovém případě webová aplikace nemůže znovu okamžitě databáze. Ale opětovná připojení bude úspěšné automaticky po dokončení převzetí služeb při selhání databázi. Při selhání oblast je obnovena a zpět do režimu online, z původního primárního automaticky znovu připojí jako nové sekundární. Následující obrázek znázorňuje konfiguraci po převzetí služeb při selhání.
 
> [!NOTE]
> Během obnovení připojení se ke ztrátě všech transakcí potvrzených po převzetí. Po dokončení převzetí aplikace v oblasti B je moci znovu připojit a znovu spusťte zpracování žádostí uživatele. Webové aplikace a primární databáze jsou nyní v oblasti B a zůstanou společně umístěné. n>

![Scénář 1. Konfigurace po převzetí služeb při selhání](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

V případě výpadku v oblasti B získá procesu replikace mezi primární a sekundární databázi pozastaveno ale propojení mezi těmito dvěma zůstává beze změn (1). Provoz spravované zjistí, že připojení k oblasti B je porušený a označí koncový bod webové aplikace 2 jako snížený (2). Není v tomto případě ovlivněn výkon aplikace, ale databáze se stane zveřejněné a proto vyšší riziko ztráty dat v případě oblasti A selže po sobě.

> [!NOTE]
> Pro zotavení po havárii doporučujeme konfigurace nasazení aplikace na omezenou dvou oblastí. Je to proto, že většina Azure zeměpisných má jenom dvě oblasti. Tato konfigurace nechrání aplikace souběžných závažné selhání obou oblastí. V nepravděpodobnému takové selhání, můžete obnovit vaše databáze v třetí oblast pomocí [geografické obnovení operace](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Jakmile se výpadek zmírnit, sekundární databázi automaticky znovu synchronizují s aplikací primární. Během synchronizace může mít dopad na výkon primární. Dopad na konkrétní závisí na množství dat, nový primární získali od převzetí služeb při selhání. Následující diagram znázorňuje výpadku v sekundární oblasti:

![Scénář 1. Konfigurace po výpadku v sekundární oblasti.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Klíč **výhody** tohoto vzoru návrhu jsou:

* Stejné webové aplikace se nasadí do obou oblastí bez jakékoli specifické konfigurace a nevyžaduje další logiku navíc ke správě převzetí služeb při selhání. 
* Výkon aplikace nebude ovlivněné převzetí služeb při selhání jako webové aplikace a databáze jsou vždy společně umístěné.

Hlavní **kompromis** je, že jsou nedostatečně využité prostředky aplikace v oblasti B ve většině případů.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scénář 2: Oblastí Azure pro kontinuitu podnikových procesů s písmen a zachovávání s maximum dat.
Tato možnost je nejvhodnější pro aplikace s následujícími charakteristikami:

* Jakoukoli ztrátu dat, je vysoký obchodní riziko. Převzetí služeb při selhání databázi můžete použít jako poslední možnost jen v případě, že se výpadek je způsobena závažné chybě.
* Aplikace podporuje jen pro čtení a čtení a zápis režimy operací a můžou fungovat v "režimu jen pro čtení" pro určitou dobu.

V tomto vzoru přepínačů aplikace do režimu jen pro čtení, když dochází k chybám časového limitu připojení pro čtení a zápis. Webová aplikace je nasazen na obou oblastí a jsou připojení ke koncovému bodu naslouchací proces pro čtení a zápis a jiné připojení ke koncovému bodu naslouchací proces jen pro čtení (1). Profil správce provozu měli používat [směrování s prioritou](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Koncový bod monitorování](../traffic-manager/traffic-manager-monitoring.md) by měla být povolená pro koncový bod aplikace v každé oblasti (2).

Následující obrázek znázorňuje tuto konfiguraci před výpadek:

![Scénář 2. Konfigurace před se výpadek.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Když správce provozu zjistí selhání připojení oblasti A, automaticky přepíná provozu generovaného uživateli na instanci aplikace v oblasti B. Pomocí tohoto vzoru je důležité nastavit období odkladu ztráty dat. dostatečně vysokou hodnotu, například 24 hodin. Zajišťuje, aby ztrátě dat se vyloučilo, pokud se výpadek zmírnit v této době. Když je aktivován webové aplikace v oblasti B operace čtení a zápis začnou selhávat. V tomto okamžiku by měl přepnout do režimu jen pro čtení (1). V tomto režimu jsou směrovány do sekundární databáze automaticky žádosti. Pokud se výpadek je způsobeno závažné chybě, pravděpodobně ho nemůže být omezeny během poskytnuté lhůty. Při jeho vypršení platnosti aktivace skupiny převzetí služeb při selhání převzetí služeb při selhání. Po které bude k dispozici naslouchací proces pro čtení a zápis a připojení k němu zastavit selhání (2). Následující diagram znázorňuje dvě fáze procesu obnovení.

> [!NOTE]
> Pokud se výpadek v primární oblasti zmírnit během poskytnuté lhůty, traffic Manageru zjistí obnovení připojení v primární oblasti a přepíná provozu generovaného uživateli zpět do instance aplikace v oblasti A. Tuto instanci aplikace obnoví a funguje v režimu pro čtení a zápis v oblasti A které jsou popsány v předchozí diagram primární databáze.
>

![Scénář 2. Fáze obnovení po havárii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Pokud v oblasti B se stane výpadku, správce provozu zjistí selhání koncový bod webové app-2 v oblasti B a označí je degradovaný (1). Do té doby převzetí služeb při selhání skupiny přepínačů jen pro čtení naslouchací proces pro oblast A (2). Tímto výpadkem nemá negativní vliv na prostředí koncového uživatele, ale primární databáze je vystaven během se výpadek. Následující diagram znázorňuje selhání v sekundární oblasti:

![Scénář 2. Výpadek služby sekundární oblast.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Jakmile se výpadek zmírnit, sekundární databáze je okamžitě synchronizovány s primárním serverem a je jen pro čtení naslouchací proces přepnout zpět do sekundární databáze v oblasti B. Během synchronizace z hlavních může mít mírně dopad na výkon v závislosti na množství dat, který je potřeba synchronizovat.

Tento vzor návrhu má několik **výhody**:

* Ní nedochází ke ztrátě dat při dočasných výpadků.
* Výpadek závisí pouze na tom, jak rychle traffic Manageru zjistí chybu připojení, které je možné konfigurovat.

**Kompromis** je, že aplikace musí být schopen fungují v režimu jen pro čtení.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scénář 3: Aplikace přemístění do jiné geography bez ztráty dat a téměř nepřeruší 
V tomto scénáři aplikace má následující vlastnosti: 
* Koncovým uživatelům přístup k aplikacím z různých zeměpisných regionech
* Aplikace zahrnuje jen pro čtení úlohy, které nezávisí na úplnou synchronizaci s nejnovějšími aktualizacemi
* Oprávnění k zápisu do datového by mělo podporovat ve stejné geography pro většinu uživatelů 
* Latence pro čtení je velmi důležitá pro činnost koncového uživatele 


Chcete-li splňovat tyto požadavky je potřeba zaručit, že zařízení uživatele **vždy** připojuje k aplikaci nasadit ve stejné geography pro operace jen pro čtení, například procházení dat, analýzy atd. Zatímco OLTP operace, které jsou zpracovány v stejné geography **většinu času**. Například během doby den OLTP operace jsou zpracovány v stejné geography, ale při vypnutém hodin se nepodařilo zpracovat v jiném datovém typu geography. Pokud aktivita koncový uživatel se stane většinou během pracovní doby, můžete zajistit optimální výkon pro většinu uživatelů většinu času. Následující diagram znázorňuje Tato topologie. 
 
Prostředky aplikace musí být nasazené v každé geography, kdy máte vyžádání významné využití. Například pokud se vaše aplikace používá aktivně ve Spojených státech amerických, Evropské unie a jihovýchodní Asie aplikace musí být nasazené do všech těchto zeměpisných oblastí. Primární databáze by měla dynamicky přepnout z jednoho Geografie na další na konci pracovní dobu. Tato metoda je volána "následovat sun". Zatížení OLTP vždy připojí k databázi prostřednictvím naslouchací proces pro čtení a zápis  **&lt;název skupiny pro převzetí služeb při selhání&gt;. database.windows.net** (1). Zatížení jen pro čtení připojí k místní databázi přímo pomocí koncového bodu serveru databáze  **&lt;název serveru&gt;. database.windows.net** (2). Správce provozu je nakonfigurované [metody směrování podle výkonu](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Zajišťuje, koncový uživatel zařízení je připojené k webové službě v nejbližší oblast. Správce provozu měli nastavit s koncového bodu monitorování povoleno pro každou webovou službu koncového bodu (3).

> [!NOTE]
> Konfigurace převzetí služeb při selhání skupiny definuje, které oblasti se používá pro převzetí služeb při selhání. Vzhledem k tomu, že nový primární je v jiném datovém typu geography výsledky převzetí služeb při selhání v delší latence pro OLTP a úlohy jen pro čtení, až ovlivněné oblast je zpět do režimu online.
>

![Scénář 3. Konfigurace s primární ve východní USA.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Na konci dne (například ve 23: 00 místního času) by měl aktivní databáze přepnout do oblasti Další (Severní Evropa). Tato úloha je možné plně automatizovat pomocí [Azure, služby plánování](../scheduler/scheduler-intro.md).  Úloha zahrnuje následující kroky:
* Přepnout primární server ve skupině pro převzetí služeb při selhání na Severní Evropa popisný převzetí služeb při selhání (1)
* Odeberte skupinu převzetí služeb při selhání mezi Východ USA a Severní Evropa
* Vytvořte novou skupinu převzetí služeb při selhání se stejným názvem, ale až Severní Evropa, východní Asie (2). 
* Přidejte primární do této skupiny převzetí služeb při selhání (3) v oblasti Severní Evropa a sekundární ve východní Asii.


Následující diagram znázorňuje novou konfiguraci po plánované převzetí služeb při selhání:

![Scénář 3. Přechod primární na Severní Evropa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Pokud k výpadku dojde například v Severní Evropa, převzetí služeb při selhání automatické databáze inicializuje skupině převzetí služeb při selhání, což vede k efektivně přesunutí aplikace do oblasti Další plánem (1).  USA – východ v takovém případě je jenom zbývající sekundární oblast, dokud Severní Evropa je zpět do režimu online. Zbývající dvě oblasti sloužit zákazníkům ve všech tří geografických přepnutím role. Azure scheduler musí být upraven. Protože zbývající oblasti získat další uživatelské provoz z Evropa, je dopad na výkon aplikace jenom s další latencí, ale taky pomocí zvýšením počtu připojení koncového uživatele. Jakmile se výpadek zmírnit v Severní Evropa, sekundární databázi je okamžitě synchronizován s aktuální primární. Následující diagram znázorňuje výpadku v severní Evropě:

![Scénář 3. Výpadek v severní Evropě.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Můžete zkrátit dobu, kdy je degradovaný činnost koncového uživatele v Evropě s dlouhou latencí. Uděláte, které by měl proaktivně nasadit jako kopie aplikace a vytvoření sekundární databáze v jiné oblasti místní (západní Evropa) jako náhrada instance offline aplikace v severní Evropě. Pokud k tomu je zpět do režimu online můžete se rozhodnout, zda chcete pokračovat v používání západní Evropa nebo chcete odebrat kopii aplikace existuje a přejít zpět k používání Severní Evropa,
>

Klíč **výhody** tohoto návrhu jsou:
* Jen pro čtení aplikace zatížení přistupuje k datům v oblasti skříně za všech okolností. 
* Aplikace pro čtení a zápis zatížení získá přístup k datům v nejbližší oblast během období nejvyšší aktivity v každé geography
* Protože aplikace je nasazená do několika oblastí, přežijí ke ztrátě některé z oblastí bez odstávky významné. 

Ale některé **kompromisy**:
* Místní výpadku výsledkem Geografie na ovlivňovat delší latence. Úlohy pro čtení i zápis i jen pro čtení je obsluhuje aplikace v jiném datovém typu geography. 
* Úlohy jen pro čtení musí připojit k jiné koncového bodu v každé oblasti. 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Plánování kontinuity obchodních: Zvolte návrh aplikace pro zotavení po havárii cloudu
Strategie zotavení po havárii konkrétní cloudové můžete kombinovat nebo rozšířit tyto vzory návrhu na nejlépe vyhovovat potřebám vaší aplikace.  Jak už bylo zmíněno dříve, strategie, kterou zvolíte je založena na smlouvě SLA můžete chtít nabízet zákazníkům a topologii nasazení aplikace. Chcete-li Průvodce vaše rozhodnutí, následující tabulka porovnává voleb na základě plánovaného bodu obnovení (RPO) a obnovení odhadovanou dobu (Vložit).

| Vzor | RPO | ERT |
|:--- |:--- |:--- |
| Aktivní – pasivní nasazení pro zotavení po havárii s přístupem společně umístěné databáze |Přístup pro čtení a zápis < 5 s |Čas detekce selhání + TTL pro DNS |
| Aktivní aktivní nasazení pro aplikaci služby Vyrovnávání zatížení |Přístup pro čtení a zápis < 5 s |Čas detekce selhání + TTL pro DNS |
| Aktivní – pasivní nasazení pro uchovávání dat |Jen pro čtení. < 5 s | Jen pro čtení = 0 |
||Přístup pro čtení a zápis = nula. | Přístup pro čtení a zápis = čas detekce selhání + období odkladu ztráty dat. |
|||

## <a name="next-steps"></a>Další postup
* Přehled kontinuity obchodních a scénářů najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md)
* Další informace o skupinách geografická replikace a převzetí služeb při selhání najdete v tématu [aktivní geografickou replikaci](sql-database-geo-replication-overview.md)  
* Informace o aktivní geografickou replikaci s elastické fondy najdete v tématu [strategie zotavení po havárii elastický fond](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
