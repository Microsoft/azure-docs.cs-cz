---
title: Navrhujte globálně dostupné služby
description: Přečtěte si o návrhu aplikací pro vysoce dostupné služby pomocí Azure SQL Database.
keywords: cloud zotavení po havárii, řešení zotavení po havárii, zálohování dat aplikací, geografická replikace, plánování kontinuity podnikání
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 348bd2b92801217a5aea2ef4d1426c020085e4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269065"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Navrhování globálně dostupných služeb pomocí Azure SQL Database

Při vytváření a nasazování cloudových služeb pomocí Azure SQL Database používáte [aktivní geografickou replikaci](sql-database-active-geo-replication.md) nebo [skupiny s automatickým převzetím služeb při selhání,](sql-database-auto-failover-group.md) které poskytují odolnost vůči regionálním výpadkům a katastrofickým selháním. Stejná funkce umožňuje vytvářet globálně distribuované aplikace optimalizované pro místní přístup k datům. Tento článek popisuje běžné způsoby použití, včetně výhod a kompromisů jednotlivých možností.

> [!NOTE]
> Pokud používáte databáze Premium nebo Business Critical a elastické fondy, můžete je učinit odolnými vůči regionálním výpadkům jejich převedením na zónovou redundantní konfiguraci nasazení. Viz [Zóna redundantní databáze](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scénář 1: Použití dvou oblastí Azure pro kontinuitu podnikání s minimálními prostoji

V tomto scénáři aplikace mají následující charakteristiky:

* Aplikace je aktivní v jedné oblasti Azure
* Všechny relace databáze vyžadují přístup pro čtení a zápis (RW) k datům
* Webová vrstva a datová vrstva musí být kolokována, aby se snížila latence a náklady na provoz.
* V zásadě je prostoje pro tyto aplikace vyšším obchodním rizikem než ztráta dat

V takovém případě je topologie nasazení aplikace optimalizovaná pro zpracování regionálních havárií, když všechny součásti aplikace potřebují k převzetí služeb při selhání společně. Níže uvedený diagram znázorňuje tuto topologii. Pro geografickou redundanci jsou prostředky aplikace nasazeny do oblasti A a B. Prostředky v oblasti B však nejsou využívány, dokud se nepodaří oblast A. Skupina převzetí služeb při selhání je mezi dvěma oblastmi nakonfigurována pro správu připojení k databázi, replikace a převzetí služeb při selhání. Webová služba v obou oblastech je nakonfigurovánpro přístup k databázi prostřednictvím ** &lt;schylů&gt;** pro čtení a zápis název skupiny .database.windows.net (1). Správce provozu je nastaven tak, aby používal [metodu směrování priorit](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) se používá v celém tomto článku pouze pro ilustrační účely. Můžete použít libovolné řešení vyrovnávání zatížení, které podporuje metodu směrování priorit.

Následující diagram znázorňuje tuto konfiguraci před výpadkem:

![Scénář 1. Konfigurace před výpadkem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Po výpadku v primární oblasti služba SQL Database zjistí, že primární databáze není přístupná a aktivuje převzetí služeb při selhání do sekundární oblasti na základě parametrů zásadautomatického převzetí služeb při selhání (1). V závislosti na sla aplikace můžete nakonfigurovat období odkladu, které řídí čas mezi detekcí výpadku a samotným převzetím služeb při selhání. Je možné, že správce provozu iniciuje převzetí služeb při selhání koncového bodu dříve, než skupina převzetí služeb při selhání aktivuje převzetí služeb při selhání databáze. V takovém případě se webová aplikace nemůže okamžitě znovu připojit k databázi. Ale opětovné připojení bude automaticky úspěšné, jakmile se dokončí převzetí služeb při selhání databáze. Po obnovení neúspěšné oblasti a zpět do režimu online se staré primární automaticky znovu připojí jako nové sekundární. Následující diagram znázorňuje konfiguraci po převzetí služeb při selhání.

> [!NOTE]
> Všechny transakce potvrzené po převzetí služeb při selhání jsou ztraceny během opětovného připojení. Po dokončení převzetí služeb při selhání aplikace v oblasti B je schopen znovu připojit a restartovat zpracování požadavků uživatelů. Webová aplikace i primární databáze jsou nyní v oblasti B a zůstávají umístěny společně.

![Scénář 1. Konfigurace po převzetí služeb při selhání](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Pokud dojde k výpadku v oblasti B, proces replikace mezi primární a sekundární databáze se pozastaví, ale propojení mezi těmito dvěma zůstane beze změny (1). Spravované přenosy zjistí, že připojení k oblasti B je přerušeno a označí webovou aplikaci koncového bodu 2 jako degradovanou (2). Výkon aplikace není ovlivněna v tomto případě, ale databáze se zpřístupní a proto na vyšší riziko ztráty dat v případě, že oblast A selže po sobě.

> [!NOTE]
> Pro zotavení po havárii doporučujeme konfiguraci s nasazením aplikace omezena na dvě oblasti. Důvodem je, že většina azure geografie mají pouze dvě oblasti. Tato konfigurace nechrání aplikaci před současným katastrofickým selháním obou oblastí. V nepravděpodobném případě takového selhání můžete obnovit databáze ve třetí oblasti pomocí [operace geografického obnovení](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Jakmile je výpadek zmírněn, sekundární databáze se automaticky znovu synchronizuje s primární. Během synchronizace může být ovlivněn výkon primární. Konkrétní dopad závisí na množství dat, která nové primární získaly od převzetí služeb při selhání. Následující diagram znázorňuje výpadek v sekundární oblasti:

![Scénář 1. Konfigurace po výpadku v sekundární oblasti.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Hlavní **výhody** tohoto návrhového vzoru jsou:

* Stejná webová aplikace se nasadí do obou oblastí bez jakékoli konfigurace specifické pro oblast a nevyžaduje další logiku pro správu převzetí služeb při selhání.
* Výkon aplikace není ovlivněnpřevzetíslužeb při selhání jako webové aplikace a databáze jsou vždy umístěny společně.

Hlavní **kompromis** je, že prostředky aplikace v oblasti B jsou nedostatečně využívány většinu času.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scénář 2: Oblasti Azure pro kontinuitu podnikání s maximálním zachováním dat

Tato možnost je nejvhodnější pro aplikace s následujícími charakteristikami:

* Jakákoli ztráta dat je vysoké obchodní riziko. Převzetí služeb při selhání databáze lze použít pouze jako poslední možnost, pokud je výpadek způsoben katastrofickým selháním.
* Aplikace podporuje režimy operací jen pro čtení a čtení a zápis a může pracovat v režimu jen pro čtení po určitou dobu.

V tomto vzoru aplikace přepne do režimu jen pro čtení při připojení pro čtení a zápis spustit získání chyby časového času. Webová aplikace je nasazena do obou oblastí a obsahuje připojení ke koncovému bodu posluchače čtení a zápisu a jiné připojení ke koncovému bodu posluchače jen pro čtení (1). Profil správce provozu by měl používat [směrování priorit](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Monitorování koncového bodu](../traffic-manager/traffic-manager-monitoring.md) by mělo být povoleno pro koncový bod aplikace v každé oblasti (2).

Následující diagram znázorňuje tuto konfiguraci před výpadkem:

![Scénář 2. Konfigurace před výpadkem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Když správce provozu zjistí selhání připojení k oblasti A, automaticky přepne provoz uživatele na instanci aplikace v oblasti B. Pomocí tohoto vzoru je důležité nastavit období odkladu se ztrátou dat na dostatečně vysokou hodnotu, například 24 hodin. Zajišťuje, že ztráta dat je zabráněno, pokud výpadek je zmírněna v této době. Při aktivaci webové aplikace v oblasti B operace čtení a zápisu spustit selhání. V tomto okamžiku by se měl přepnout do režimu jen pro čtení (1). V tomto režimu jsou požadavky automaticky směrovány do sekundární databáze. Pokud je výpadek způsoben katastrofickým selháním, s největší pravděpodobností jej nelze zmírnit během období odkladu. Po vypršení platnosti skupiny převzetí služeb při selhání aktivuje převzetí služeb při selhání. Poté bude k dispozici naslouchací proces čtení a zápis a připojení k němu přestanou selhávat (2). Následující diagram znázorňuje dvě fáze procesu obnovení.

> [!NOTE]
> Pokud je výpadek v primární oblasti zmírněn během období odkladu, správce provozu zjistí obnovení připojení v primární oblasti a přepne uživatelský provoz zpět na instanci aplikace v oblasti A. Tato instance aplikace pokračuje a pracuje v režimu čtení a zápisu pomocí primární databáze v oblasti A, jak je znázorněno v předchozím diagramu.

![Scénář 2. Fáze zotavení po havárii.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Pokud dojde k výpadku v oblasti B, správce provozu zjistí selhání koncového bodu web-app-2 v oblasti B a označí ji degradován (1). Do té doby skupina převzetí služeb při selhání přepne naslouchací proces jen pro čtení do oblasti A (2). Tento výpadek nemá vliv na prostředí koncového uživatele, ale primární databáze je vystavena během výpadku. Následující diagram znázorňuje selhání v sekundární oblasti:

![Scénář 2. Výpadek sekundární oblasti.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Jakmile je výpadek zmírněn, sekundární databáze je okamžitě synchronizována s primární a naslouchací proces jen pro čtení je přepnut zpět do sekundární databáze v oblasti B. Během synchronizace výkon primární může být mírně ovlivněna v závislosti na množství dat, která je třeba synchronizovat.

Tento návrhový vzor má několik **výhod**:

* Zabraňuje ztrátě dat během dočasných výpadků.
* Prostoje závisí pouze na tom, jak rychle správce provozu zjistí selhání připojení, které lze konfigurovat.

**Kompromis** je, že aplikace musí být schopen pracovat v režimu jen pro čtení.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scénář 3: Přemístění aplikace do jiné zeměpisné polohy bez ztráty dat a téměř nulové prostoje

V tomto scénáři aplikace má následující charakteristiky:

* Koncoví uživatelé přistupují k aplikaci z různých zeměpisných oblastí
* Aplikace obsahuje úlohy jen pro čtení, které nezávisí na úplné synchronizaci s nejnovějšími aktualizacemi
* Přístup pro zápis k datům by měl být podporován ve stejné zeměpisné oblasti pro většinu uživatelů
* Latence čtení je pro koncového uživatele důležitá

Aby bylo možné splnit tyto požadavky, musíte zaručit, že uživatelské zařízení **se vždy** připojí k aplikaci nasazené ve stejné zeměpisné oblasti pro operace jen pro čtení, jako jsou data procházení, analýzy atd. Vzhledem k tomu, OLTP operace jsou zpracovávány ve stejné zeměpisné **poloze většinu času**. Například během dne jsou operace OLTP zpracovány ve stejné zeměpisné oblasti, ale během mimo pracovní doby mohou být zpracovány v jiné zeměpisné oblasti. Pokud aktivita koncového uživatele většinou probíhá během pracovní doby, můžete většinu času zaručit optimální výkon pro většinu uživatelů. Následující diagram znázorňuje tuto topologii.

Prostředky aplikace by měly být nasazeny v každé zeměpisné oblasti, kde máte značné využití poptávky. Například pokud vaše aplikace se aktivně používá ve Spojených státech, Evropské unii a jihovýchodní Asii aplikace by měla být nasazena do všech těchto zeměpisných oblastí. Primární databáze by měla být dynamicky přepnuta z jedné zeměpisné oblasti na další na konci pracovní doby. Tato metoda se nazývá "sledovat slunce". Úloha OLTP se vždy připojí k databázi ** &lt;&gt;** prostřednictvím naslouchací proces pro čtení a zápis název skupiny .database.windows.net (1). Úloha jen pro čtení se připojuje k místní databázi přímo pomocí názvu serveru koncového serveru serveru databázového serveru ** &lt;&gt;.database.windows.net** (2). Traffic Manager je konfigurován s [metodou směrování výkonu](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Zajišťuje, že zařízení koncového uživatele je připojeno k webové službě v nejbližší oblasti. Správce provozu by měl být nastaven s povoleným sledováním koncového bodu pro každý koncový bod webové služby (3).

> [!NOTE]
> Konfigurace skupiny převzetí služeb při selhání definuje, která oblast se používá pro převzetí služeb při selhání. Vzhledem k tomu, že nové primární je v jiné zeměpisné oblasti převzetí služeb při selhání má za následek delší latence pro OLTP a jen pro čtení úlohy, dokud ovlivněné oblasti je zpět do režimu online.

![Scénář 3. Konfigurace s primárnív USA východ.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Na konci dne, například ve 23 hodin místního času, by měly být aktivní databáze převedeny na další region (severní Evropa). Tuto úlohu lze plně automatizovat pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Úkol zahrnuje následující kroky:

* Přepnutí primárního serveru ve skupině převzetí služeb při selhání do severní Evropy pomocí přátelského převzetí služeb při selhání (1)
* Odebrání skupiny převzetí služeb při selhání mezi východními USA a severní Evropou
* Vytvořte novou skupinu převzetí služeb při selhání se stejným názvem, ale mezi severní Evropou a východní Asií (2).
* Do této skupiny převzetí služeb při selhání přidejte primární položky v severní Evropě a sekundární ve východní Asii (3).

Následující diagram znázorňuje novou konfiguraci po plánovaném převzetí služeb při selhání:

![Scénář 3. Přechod primárek do severní Evropy.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Pokud k výpadku dojde například v severní Evropě, automatické převzetí služeb při selhání je iniciováno skupinou převzetí služeb při selhání, což efektivně vede k přesunutí aplikace do další oblasti před plánem (1).  V takovém případě je východ USA jediným zbývajícím sekundárním regionem, dokud nebude severní Evropa zpět online. Zbývající dvě oblasti slouží zákazníkům ve všech třech zeměpisných oblastech změnou rolí. Aplikace Azure Logic Apps je třeba odpovídajícím způsobem upravit. Vzhledem k tomu, že zbývající oblasti získat další uživatelský provoz z Evropy, výkon aplikace je ovlivněna nejen další latence, ale také zvýšený počet připojení koncových uživatelů. Jakmile je výpadek zmírněn v severní Evropě, sekundární databáze je okamžitě synchronizována s aktuální primární. Následující diagram znázorňuje výpadek v severní Evropě:

![Scénář 3. Výpadek v severní Evropě.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Můžete zkrátit dobu, kdy je prostředí koncového uživatele v Evropě snížena o dlouhou latenci. Chcete-li to provést, měli byste proaktivně nasadit kopii aplikace a vytvořit sekundární databáze v jiné místní oblasti (Západní Evropa) jako náhradu instance offline aplikace v severní Evropě. Když je tato aplikace zpět online, můžete se rozhodnout, zda budete pokračovat v používání západní Evropy, nebo odstranit kopii aplikace a přepnout zpět na používání severní Evropy.

Hlavní **výhody** tohoto návrhu jsou:

* Úloha aplikace jen pro čtení přistupuje k datům v oblasti skříní za všech okolností.
* Úloha aplikace pro čtení a zápis přistupuje k datům v nejbližší oblasti během období nejvyšší aktivity v každé zeměpisné oblasti.
* Vzhledem k tomu, že aplikace je nasazena do více oblastí, může přežít ztrátu jedné z oblastí bez významných prostojů.

Ale tam jsou některé **kompromisy**:

* Regionální výpadek má za následek zeměpisné polohy, které mají být ovlivněny delší latence. Úlohy jen pro čtení a jen pro čtení obsluhuje aplikace v jiné zeměpisné oblasti.
* Úlohy jen pro čtení se musí připojit k jinému koncovému bodu v každé oblasti.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Plánování kontinuity provozu: Zvolte návrh aplikace pro cloudové zotavení po havárii

Vaše konkrétní strategie zotavení po havárii cloudu můžete kombinovat nebo rozšířit tyto návrhové vzory, aby co nejlépe vyhovovaly potřebám vaší aplikace.  Jak již bylo zmíněno dříve, strategie, kterou zvolíte, je založena na sla, kterou chcete nabídnout svým zákazníkům, a topologii nasazení aplikace. Následující tabulka porovnává volby založené na cíli bodu obnovení (RPO) a odhadované době obnovení (ERT), která vám pomůže řídit vaše rozhodnutí.

| Vzor | Cíl bodu obnovení | Ert |
|:--- |:--- |:--- |
| Aktivní pasivní nasazení pro zotavení po havárii se spoluumístěným přístupem k databázi |Přístup pro čtení a zápis < 5 sekund |Doba detekce selhání + DNS TTL |
| Aktivní nasazení pro vyrovnávání zatížení aplikací |Přístup pro čtení a zápis < 5 sekund |Doba detekce selhání + DNS TTL |
| Aktivní pasivní nasazení pro uchovávání dat |Přístup jen pro čtení < 5 sekund | Přístup jen pro čtení = 0 |
||Přístup pro čtení a zápis = nula | Přístup pro čtení a zápis = doba detekce selhání + doba odkladu se ztrátou dat |
|||

## <a name="next-steps"></a>Další kroky

* Přehled kontinuity provozu a scénáře najdete v tématu [Přehled kontinuity provozu](sql-database-business-continuity.md)
* Informace o aktivní geografické replikaci naleznete [v tématu Aktivní geografická replikace](sql-database-active-geo-replication.md).
* Další informace o skupinách s automatickým převzetím služeb při selhání naleznete [v tématu Skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).
* Informace o aktivní geografické replikaci s elastické fondy naleznete v [tématu Strategie zotavení po havárii elastického fondu](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
