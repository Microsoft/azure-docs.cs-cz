---
title: Návrh globálně dostupných služeb
description: Přečtěte si o návrhu aplikací pro služby s vysokou dostupností pomocí Azure SQL Database.
keywords: zotavení po havárii v cloudu, řešení zotavení po havárii, zálohování dat aplikací, geografickou replikaci, plánování kontinuity podnikových aplikací
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: be632ba06edc858e7eadcd6e57a4f7769f69f2cb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321675"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Návrh globálně dostupných služeb pomocí Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Při sestavování a nasazování cloudových služeb pomocí Azure SQL Database použijete [aktivní skupiny geografických replikace](active-geo-replication-overview.md) nebo [automatického převzetí služeb při selhání](auto-failover-group-overview.md) k zajištění odolnosti proti místním výpadkům a závažným chybám. Stejná funkce vám umožní vytvářet globálně distribuované aplikace optimalizované pro místní přístup k datům. Tento článek popisuje běžné aplikační vzory, včetně výhod a kompromisů jednotlivých možností.

> [!NOTE]
> Pokud používáte databáze úrovně Premium nebo Pro důležité obchodní informace a elastické fondy, můžete je v případě, že je převedete do konfigurace redundantního nasazení zóny, zajistit odolnost proti regionálním výpadkům. Viz [zóna – redundantní databáze](high-availability-sla.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Scénář 1: použití dvou oblastí Azure pro kontinuitu podnikových aplikací s minimálními výpadky

V tomto scénáři mají aplikace následující vlastnosti:

* Aplikace je aktivní v jedné oblasti Azure.
* Všechny relace databáze vyžadují pro data přístup pro čtení a zápis (RW).
* Aby se snížila latence a náklady na provoz, musí být společně umístěnéhoa webová vrstva a Datová vrstva.
* V podstatě je výpadkům pro tyto aplikace vyšší riziko, než je ztráta dat.

V takovém případě je topologie nasazení aplikace optimalizována pro zpracování regionálních havárií, pokud všechny součásti aplikace potřebují převzít služby při selhání společně. Následující diagram znázorňuje tuto topologii. V případě geografické redundance jsou prostředky aplikace nasazeny do oblastí A a B. Prostředky v oblasti B se ale nevyužívají, dokud nebude oblast A neúspěšná. Skupina převzetí služeb při selhání je nakonfigurovaná mezi dvěma oblastmi pro správu připojení k databázi, replikaci a převzetí služeb při selhání. Webová služba v obou oblastech je nakonfigurovaná pro přístup k databázi prostřednictvím naslouchacího procesu pro čtení i zápis ** &lt; převzetí služeb při selhání-Group-name &gt; . Database.Windows.NET** (1). Služba Azure Traffic Manager je nastavená tak, aby používala [metodu směrování priority](../../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) se v tomto článku používá jenom pro ilustraci. Můžete použít jakékoli řešení vyrovnávání zatížení, které podporuje metodu směrování priority.

Následující diagram znázorňuje tuto konfiguraci před výpadkem:

![Scénář 1. Konfigurace před výpadkem.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Po výpadku primární oblasti SQL Database zjistí, že primární databáze není dostupná, a aktivuje převzetí služeb při selhání do sekundární oblasti na základě parametrů zásady automatického převzetí služeb při selhání (1). V závislosti na vaší smlouvě SLA vaší aplikace můžete nakonfigurovat dobu odkladu, která určuje dobu mezi detekcí výpadku a samotným převzetím služeb při selhání. Je možné, že Azure Traffic Manager iniciuje převzetí služeb koncového bodu před převzetím služeb při selhání v databázi. V takovém případě se webová aplikace nemůže okamžitě znovu připojit k databázi. Ale opětovné připojení proběhne automaticky, jakmile se převzetí služeb při selhání databáze dokončí. Když se neúspěšná oblast obnoví a vrátí zpět do stavu online, stará primární primární se automaticky znovu připojí jako nová sekundární. Následující obrázek znázorňuje konfiguraci po převzetí služeb při selhání.

> [!NOTE]
> Všechny transakce potvrzené po převzetí služeb při selhání se při opětovném připojení ztratí. Po dokončení převzetí služeb při selhání se aplikace v oblasti B bude moci znovu připojit a znovu spustit zpracování uživatelských požadavků. Webová aplikace i primární databáze jsou nyní v oblasti B a zůstávají společně umístěné.

![Scénář 1. Konfigurace po převzetí služeb při selhání](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Pokud dojde k výpadku v oblasti B, proces replikace mezi primární a sekundární databází se pozastaví, ale propojení mezi dvěma zůstává beze změny (1). Traffic Manager zjistí, že připojení k oblasti B je přerušeno, a označí webovou aplikaci Endpoint 2 jako sníženou (2). V takovém případě není ovlivněn výkon aplikace, ale databáze je vystavena a je proto vyšší riziko ztráty dat v případě, že dojde k chybě v oblasti A v případě úspěchu.

> [!NOTE]
> Pro zotavení po havárii doporučujeme, aby konfigurace s nasazením aplikace byla omezená na dvě oblasti. Je to proto, že většina geografických oblastí Azure má pouze dvě oblasti. Tato konfigurace nechrání vaši aplikaci před současným závažným selháním obou oblastí. V nepravděpodobném případě takového selhání můžete databáze obnovit ve třetí oblasti pomocí [operace geografického obnovení](disaster-recovery-guidance.md#recover-using-geo-restore).
>

 Po zmírnění výpadku se sekundární databáze automaticky znovu synchronizuje s primárním. Během synchronizace může být ovlivněn výkon primární. Konkrétní dopad závisí na množství dat, které nově získal od převzetí služeb při selhání. 

> [!NOTE]
> Po omezení výpadku bude Traffic Manager začít směrovat připojení k aplikaci v oblasti A jako koncový bod s vyšší prioritou. Pokud máte v úmyslu zachovat primární oblast B v oblasti B, měli byste odpovídajícím způsobem změnit tabulku priorit v profilu přenosů Manager. 
>
 
 Následující diagram znázorňuje výpadek v sekundární oblasti:

![Scénář 1. Konfigurace po výpadku v sekundární oblasti.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

Hlavní **výhodou** tohoto vzoru návrhu jsou:

* Stejná webová aplikace se nasadí do obou oblastí bez jakékoli konfigurace specifické pro oblast a nevyžaduje další logiku pro správu převzetí služeb při selhání.
* Výkon aplikace není ovlivněn převzetím služeb při selhání, protože webová aplikace a databáze jsou vždy umístěny společně.

Hlavním **kompromisem** je, že prostředky aplikace v oblasti B jsou ve většině případů nevyužité.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Scénář 2: oblasti Azure pro kontinuitu podnikových aplikací s maximálním uchováváním dat

Tato možnost je vhodná pro aplikace s následujícími charakteristikami:

* Ztráta dat je vysokým obchodním rizikem. Převzetí služeb při selhání databáze lze použít pouze jako poslední způsob, pokud je výpadek způsoben závažnou chybou.
* Aplikace podporuje režimy operací jen pro čtení a čtení i zápis a v časovém intervalu může pracovat v režimu jen pro čtení.

V tomto modelu se aplikace přepne do režimu jen pro čtení, když se na začátku připojení pro čtení a zápis začnou získávat chyby časového limitu. Webová aplikace je nasazena v obou oblastech a zahrnuje připojení ke koncovému bodu naslouchacího procesu pro čtení i zápis a jiné připojení ke koncovému bodu naslouchacího procesu jen pro čtení (1). Profil Traffic Manager by měl používat [Směrování priority](../../traffic-manager/traffic-manager-configure-priority-routing-method.md). Pro koncový bod aplikace v každé oblasti (2) by mělo být povoleno [monitorování koncových bodů](../../traffic-manager/traffic-manager-monitoring.md) .

Následující diagram znázorňuje tuto konfiguraci před výpadkem:

![Scénář 2. Konfigurace před výpadkem.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Pokud Traffic Manager zjistí selhání připojení v oblasti A, automaticky přepne provoz uživatele do instance aplikace v oblasti B. V tomto modelu je důležité nastavit období odkladu s ztrátou dat na dostatečně vysokou hodnotu, například 24 hodin. Zajišťuje ztrátu dat, pokud dojde k omezení výpadku v této době. Pokud je webová aplikace v oblasti B aktivována, operace čtení i zápisu se nedaří spustit. V tomto okamžiku by měl přepnout do režimu jen pro čtení (1). V tomto režimu jsou požadavky automaticky směrovány do sekundární databáze. Pokud je výpadek způsoben závažnou chybou, pravděpodobně ho nelze zmírnit v období odkladu. Po vypršení platnosti dojde k selhání skupiny převzetí služeb při selhání. Po tom, co bude k dispozici naslouchací proces pro čtení i zápis a připojení k němu přestane selhat (2). Následující diagram znázorňuje dvě fáze procesu obnovení.

> [!NOTE]
> Pokud dojde k omezení výpadku v primární oblasti v rámci období odkladu, Traffic Manager zjistí obnovení připojení v primární oblasti a přepne přenos uživatelů zpátky do instance aplikace v oblasti A. Instance aplikace bude pokračovat a funguje v režimu pro čtení i zápis pomocí primární databáze v oblasti A, jak je znázorněno v předchozím diagramu.

![Scénář 2. Fáze zotavení po havárii.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Pokud dojde k výpadku v oblasti B, Traffic Manager zjistí selhání koncového bodu Web-App-2 v oblasti B a označí ho jako degradované (1). Mezitím skupina převzetí služeb při selhání přepne naslouchací proces jen pro čtení do oblasti A (2). Tento výpadek nemá vliv na činnost koncového uživatele, ale během výpadku se zveřejňuje primární databáze. Následující diagram znázorňuje selhání v sekundární oblasti:

![Scénář 2. Výpadek sekundární oblasti.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Po zmírnění výpadku je sekundární databáze hned synchronizována s primární databází a naslouchací proces jen pro čtení se přepne zpět do sekundární databáze v oblasti B. V závislosti na množství dat, které je třeba synchronizovat, může být během synchronizace na primárním počítači mírně ovlivněný výkon.

Tento vzor návrhu má několik **výhod**:

* Vyhne se ztrátě dat během dočasného výpadku.
* Výpadky závisí jenom na tom, jak rychle Traffic Manager zjistí selhání připojení, které je možné konfigurovat.

**Kompromisem** je, že aplikace musí být schopná pracovat v režimu jen pro čtení.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Scénář 3: přemístění aplikace na jinou geografickou oblast bez ztráty dat a téměř bez výpadků

V tomto scénáři má aplikace následující vlastnosti:

* Koncoví uživatelé přistupují k aplikaci z různých geografických oblastí
* Tato aplikace zahrnuje úlohy jen pro čtení, které nezávisí na úplné synchronizaci s nejnovějšími aktualizacemi.
* Přístup pro zápis dat by měl být podporován ve stejné geografické oblasti pro většinu uživatelů.
* Latence čtení je pro činnost koncového uživatele zásadní.

Aby bylo možné splnit tyto požadavky, je třeba zaručit, že se zařízení uživatele **vždy** připojí k aplikaci nasazené ve stejné geografické oblasti pro operace jen pro čtení, například na data procházení, analýzy atd. Vzhledem k tomu, že operace OLTP se zpracovávají ve stejném geografickém **čase**. Například během dne se OLTP operace zpracování ve stejné geografické oblasti, ale v době mimo hodiny, kdy je možné je zpracovat v jiném geografickém období. Pokud se aktivita koncového uživatele většinou stane během pracovní doby, můžete zajistit optimální výkon pro většinu uživatelů v většinu času. Tato topologie je znázorněna na následujícím obrázku.

Prostředky aplikace by se měly nasadit v každé geografické oblasti, kde máte významné nároky na využití. Například pokud se vaše aplikace aktivně používá v USA, Evropské unii a Jižní Východní Asie aplikace by měla být nasazená do všech těchto zeměpisných oblastí. Primární databáze by se měla dynamicky přepínat mezi jednotlivými geografickými oblastmi na konci pracovní doby. Tato metoda se nazývá "následovat na Sun". Úloha OLTP se vždy připojuje k databázi prostřednictvím naslouchacího procesu pro čtení i zápis ** &lt; převzetí služeb při selhání-Group-name &gt; . Database.Windows.NET** (1). Úloha jen pro čtení se připojuje k místní databázi přímo pomocí serveru koncového bodu serveru databáze ** &lt; -name &gt; . Database.Windows.NET** (2). Traffic Manager je nakonfigurována pomocí [metody směrování výkonu](../../traffic-manager/traffic-manager-configure-performance-routing-method.md). Zajišťuje, aby bylo zařízení koncového uživatele připojeno k webové službě v nejbližší oblasti. Traffic Manager by měl být nastaven s povoleným monitorováním koncových bodů pro každý koncový bod webové služby (3).

> [!NOTE]
> Tato oblast se používá pro převzetí služeb při selhání konfigurace skupiny převzetí služeb při selhání. Vzhledem k tomu, že nový primární objekt je v jiné geografické úrovni, jsou výsledky převzetí služeb při selhání v delší době pro OLTP i pro úlohy jen pro čtení, dokud se ovlivněná oblast znovu nevrátí do režimu online.

![Scénář 3. Konfigurace s primárním v Východní USA.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

Na konci dne, například v 11 místního času, by měly být aktivní databáze přepnuty do další oblasti (Severní Evropa). Tato úloha se dá plně automatizovat pomocí [Azure Logic Apps](../../logic-apps/logic-apps-overview.md). Úkol zahrnuje následující kroky:

* Přepnutí primárního serveru ve skupině převzetí služeb při selhání na Severní Evropa s použitím popisného převzetí služeb při selhání (1
* Odebrání skupiny převzetí služeb při selhání mezi Východní USA a Severní Evropa
* Vytvoří novou skupinu převzetí služeb při selhání se stejným názvem, ale mezi Severní Evropa a Východní Asie (2).
* Do této skupiny převzetí služeb při selhání přidejte primární Severní Evropa a sekundární Východní Asie.

Následující diagram znázorňuje novou konfiguraci po plánovaném převzetí služeb při selhání:

![Scénář 3. Převod primárního na Severní Evropa.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Pokud dojde k výpadku v Severní Evropa například automatické převzetí služeb při selhání databáze je iniciováno skupinou převzetí služeb při selhání, což efektivně vede k přesunu aplikace do další oblasti před plánem (1).  V takovém případě je USA – východ jedinou zbývající sekundární oblastí, dokud Severní Evropa znovu nepřejdete do režimu online. Zbývající dvě oblasti slouží zákazníkům ve všech třech zeměpisných oblastech přepínáním rolí. Azure Logic Apps musí být upraveny odpovídajícím způsobem. Vzhledem k tomu, že zbývající oblasti získají další uživatelský provoz z Evropy, výkon aplikace bude mít vliv nejen na další latenci, ale také na zvýšený počet připojení koncových uživatelů. Po omezení výpadku v Severní Evropa je sekundární databáze okamžitě synchronizovaná s aktuální primární databází. Následující diagram znázorňuje výpadek Severní Evropa:

![Scénář 3. Výpadek v Severní Evropa.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Můžete zkrátit čas, kdy se činnost koncového uživatele v Evropě sníží o dlouhou latenci. K tomu byste měli proaktivně nasadit kopii aplikace a vytvořit sekundární databáze v jiné místní oblasti (Západní Evropa) jako náhradu offline instance aplikace v Severní Evropa. Když je znovu online, můžete se rozhodnout, jestli chcete i nadále používat Západní Evropa nebo odebrat kopii aplikace a přepnout zpátky na použití Severní Evropa.

Klíčové **výhody** tohoto návrhu:

* Úloha aplikace jen pro čtení přistupuje k datům v oblasti skříní.
* Úloha čtení a zápisu aplikací přistupuje k datům v nejbližší oblasti během období nejvyšší aktivity v jednotlivých geografických oblastech.
* Vzhledem k tomu, že aplikace je nasazena do více oblastí, může dojít ke ztrátě jedné z oblastí bez jakéhokoli významného výpadku.

Existují však některé **kompromisy**:

* Oblastní výpadek má za následek, že by to ovlivnilo delší latenci. Aplikace v jiném geografickém programu obsluhuje úlohy pro čtení i zápis i pro čtení.
* Úlohy jen pro čtení se musí připojit k jinému koncovému bodu v každé oblasti.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Plánování kontinuity podnikových aplikací: volba návrhu aplikace pro zotavení po havárii v cloudu

Vaše konkrétní strategie cloudového zotavení po havárii může tyto vzory návrhu zkombinovat nebo roztáhnout tak, aby co nejlépe splňovala požadavky vaší aplikace.  Jak už bylo zmíněno dříve, strategie, kterou zvolíte, je založená na smlouvě SLA, kterou chcete nabídnout vašim zákazníkům a topologii nasazení aplikace. Následující tabulka porovnává tyto možnosti podle rozhodnutí bodu obnovení (RPO) a odhadované doby obnovení (ERT), které vám pomůžou s vaším rozhodnutím.

| Vzor | RPO | ERT |
|:--- |:--- |:--- |
| Aktivní – pasivní nasazení pro zotavení po havárii s společně umístěným přístupem k databázi |Přístup pro čtení i zápis < 5 sec |Čas detekce selhání + hodnota TTL systému DNS |
| Aktivní – aktivní nasazení pro vyrovnávání zatížení aplikace |Přístup pro čtení i zápis < 5 sec |Čas detekce selhání + hodnota TTL systému DNS |
| Aktivní – pasivní nasazení pro zachování dat |Přístup jen pro čtení < 5 sec | Přístup jen pro čtení = 0 |
||Přístup pro čtení i zápis = nula | Přístup pro čtení i zápis = doba detekce selhání + období odkladu s ztrátou dat |
|||

## <a name="next-steps"></a>Další kroky

* Přehled provozní kontinuity a scénářů najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md) .
* Informace o aktivní geografické replikaci najdete v tématu [Aktivní geografická replikace](active-geo-replication-overview.md).
* Další informace o skupinách automatického převzetí služeb při selhání najdete v tématu [skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md).
* Informace o aktivní geografické replikaci s elastickými fondy najdete v tématu [strategie zotavení po havárii elastického fondu](disaster-recovery-strategies-for-applications-with-elastic-pool.md).
