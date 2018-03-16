---
title: "Vrácení upgradu aplikace – Azure SQL Database | Microsoft Docs"
description: "Další informace o použití geografická replikace databáze SQL Azure pro podporu online upgrady cloudových aplikací."
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 07/16/2016
ms.author: sashan
ms.openlocfilehash: 6efc14e5f8fab5baafff50e8eb6844f8decca1c9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Správa postupné upgrady cloudových aplikací pomocí SQL Database aktivní geografickou replikaci
> [!NOTE]
> [Aktivní geografickou replikací](sql-database-geo-replication-overview.md) je nyní k dispozici pro všechny databáze na všech úrovních.
> 

Další informace o použití [geografická replikace](sql-database-geo-replication-overview.md) v databázi SQL za účelem povolení postupné upgrady cloudových aplikací. Protože upgrade je rušivý operace, je nutné součást vaší firmy kontinuity plánování a návrhu. V tomto článku jsme podívejte se na dvou různých metod Orchestrace procesu upgradu a popisují výhody a kompromis jednotlivých možností. Pro účely tohoto článku použijeme jednoduchou aplikaci, která se skládá z webu připojené k jedné databáze jako jeho datové vrstvy. Naším cílem je pro upgrade verze 1 aplikace a verze 2 bez významného dopadu na činnost koncového uživatele. 

Při vyhodnocování možnosti upgradu byste měli zvážit následující faktory:

* Dopad na dostupnost aplikace během upgradu. Jak dlouho může být funkce aplikací omezené nebo snížený výkon.
* Možnost vrácení v případě selhání upgradu.
* Chyba zabezpečení aplikace během upgradu dojde k nesouvisejícími závažné chybě.
* Celkový počet dolaru náklady.  To zahrnuje další redundanci a přírůstkové náklady dočasné komponenty používané stránkami proces upgradu. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Upgrade aplikací, které jsou závislé na zálohování databází můžete pro zotavení po havárii
Pokud vaše aplikace spoléhá na automatické zálohování databází a používá geografické obnovení pro zotavení po havárii, je obvykle nasadit do jedné oblasti Azure. V takovém případě proces upgradu zahrnuje, vytvoření zálohy nasazení všech součástí aplikace zahrnutých v upgradu. Chcete-li minimalizovat narušení koncového uživatele využíváte Azure provoz Manager (WATM) s profilem převzetí služeb při selhání.  Následující diagram znázorňuje před upgradem v provozním prostředí. Koncový bod <i>contoso-1.azurewebsites.net</i> představuje produkční slot aplikace, která musí být upgradován. Povolit možnost vrácení upgradu potřebovat vytvoříte slot fáze s plně synchronizované kopie aplikace. Následující kroky jsou nezbytné pro Příprava aplikací pro upgrade:

1. Vytvořte slot fáze upgradu. Chcete provést, vytvořte sekundární databázi (1) a nasadit identické webu ve stejné oblasti Azure. Monitorování sekundární chcete zobrazit, pokud proces synchronizace replik indexů je dokončený.
2. Vytvoření profilu převzetí služeb při selhání v WATM s <i>contoso-1.azurewebsites.net</i> jako koncový bod online a <i>contoso-2.azurewebsites.net</i> v režimu offline. 

> [!NOTE]
> Všimněte si, přípravné kroky nebude mít vliv na aplikaci v produkčním slotu a může fungovat v režimu úplný přístup.
>  

![Konfigurace replikace přejít databáze SQL. Cloud zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Po dokončení přípravné kroky aplikace je připravená na skutečném upgradu. Následující diagram znázorňuje kroky v procesu upgradu. 

1. Nastavte primární databázi v produkčním slotu do režimu jen pro čtení (3). Tím se zaručí, že instance produkční aplikace (V1) zůstane jen pro čtení během upgradu, takže si odchylkami dat mezi instancemi databáze V1 a V2.  
2. Odpojte sekundární databáze v režimu plánované ukončení (4). Vytvoří kopii plně synchronizované nezávislé primární databáze. Tato databáze budou upgradovány.
3. Zapnout primární databázi do režimu pro čtení a zápis a spusťte skript pro upgrade ve fázi slotu (5).     

![Konfigurace geografická replikace databáze SQL. Cloud zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Pokud se upgrade úspěšně dokončit nyní jste připraveni přepnutí koncoví uživatelé na dvoufázové instalace kopie aplikace. Nyní bude aplikace produkční slot.  To zahrnuje několik další kroky, jak je znázorněno na následujícím diagramu.

1. Přepnout online koncový bod v profilu WATM k <i>contoso-2.azurewebsites.net</i>, která odkazuje na verzi V2 webového serveru (6). Nyní bude produkční slot s aplikací V2 a provoz koncový uživatel se přesměruje k němu.  
2. Pokud již nepotřebujete součásti aplikace V1, abyste mohli bezpečně odeberte je (7).   

![Konfigurace geografická replikace databáze SQL. Cloud zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Pokud neproběhne proces upgradu, například z důvodu chyby ve skriptu pro upgrade, slotu fáze považovat za narušení. Vrácení aplikace do stavu před upgradem jednoduše obnovit aplikaci v produkčním slotu úplný přístup. Kroky jsou uvedeny další diagram.    

1. Nastavte kopie databáze do režimu pro čtení a zápis (8). Tato akce obnoví úplné V1 funkčně v produkčním slotu.
2. Provedení analýzy příčina kořenové a odebrat ohrožené součásti ve fázi slotu (9). 

V tomto okamžiku je plně funkční aplikaci a lze je opakovat jednotlivé kroky upgradu.

> [!NOTE]
> Vrácení zpět nevyžaduje změny v profilu WATM jako již odkazuje na <i>contoso-1.azurewebsites.net</i> jako aktivní koncový bod.
> 
> 

![Konfigurace geografická replikace databáze SQL. Cloud zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

Klíč **využít** této možnosti je, že můžete upgradovat aplikaci v jedné oblasti používáte sadu jednoduchých kroků. Náklady na dolaru upgradu je relativně nízký. Hlavní **kompromis** je, že pokud při upgradu dojde k závažné chybě, bude zahrnovat obnovení do stavu před upgradem opakované nasazení aplikace v jiné oblasti a obnovit databázi ze zálohy pomocí geografické obnovení. Tento proces bude způsobit významné výpadky.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Upgrade aplikací, které jsou závislé na geografické replikace databáze pro zotavení po havárii
Pokud vaše aplikace využívá geografická replikace pro kontinuitu podnikových procesů, se nasadí do alespoň dva různé oblasti s aktivní nasazení v primární oblasti a pohotovostní nasazení v oblasti zálohování. Kromě faktory, již bylo zmíněno dříve proces upgradu musí zaručit, že:

* Aplikace i nadále chráněn před závažné selhání za všech okolností během procesu upgradu
* Geograficky redundantní komponenty aplikace jsou upgradovat paralelně s active součásti

K dosažení těchto cílů využije profil převzetí služeb při selhání pomocí jednu aktivní a tři zálohy koncové body Azure provoz Manager (WATM).  Následující diagram znázorňuje před upgradem v provozním prostředí. Na webech <i>contoso-1.azurewebsites.net</i> a <i>contoso-dr.azurewebsites.net</i> představují produkční slot aplikace s úplné geografická redundance. Povolit možnost vrácení upgradu potřebovat vytvoříte slot fáze s plně synchronizované kopie aplikace. Vzhledem k tomu, že je potřeba zajistit, že aplikace může rychle obnovit v případě, že během procesu upgradu dojde k závažné chybě, musí být geograficky redundantní také fáze slot. Následující kroky jsou nezbytné pro Příprava aplikací pro upgrade:

1. Vytvořte slot fáze upgradu. Chcete provést, vytvořte sekundární databázi (1) a nasadit identické kopii webu ve stejné oblasti Azure. Monitorování sekundární chcete zobrazit, pokud proces synchronizace replik indexů je dokončený.
2. Vytvoření geograficky redundantní sekundární databáze ve fázi slotu geografickou replikací sekundární databáze pro zálohování oblast (to se označuje jako "zřetězené geografická replikace"). Monitorování sekundární zálohy zjistit, jestli proces synchronizace replik indexů dokončené (3).
3. Vytvořit kopii webu pohotovostní v oblasti zálohování a tu propojit na geograficky redundantní sekundární (4).  
4. Přidat další koncové body <i>contoso-2.azurewebsites.net</i> a <i>contoso-3.azurewebsites.net</i> profilu převzetí služeb při selhání v WATM jako offline koncové body (5). 

> [!NOTE]
> Všimněte si, přípravné kroky nebude mít vliv na aplikaci v produkčním slotu a může fungovat v režimu úplný přístup.
> 
> 

![Konfigurace geografická replikace databáze SQL. Cloud zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Po dokončení kroků přípravy slot fáze je připravený na upgrade. Následující diagram znázorňuje jednotlivé kroky upgradu.

1. Nastavte primární databázi v produkčním slotu do režimu jen pro čtení (6). Tím se zaručí, že instance produkční aplikace (V1) zůstane jen pro čtení během upgradu, takže si odchylkami dat mezi instancemi databáze V1 a V2.  
2. Odpojte sekundární databázi ve stejné oblasti pomocí plánované ukončení režimu (7). Vytvoří kopii plně synchronizované nezávislé primární databázi, která se automaticky stane primární po ukončení. Tato databáze budou upgradovány.
3. Zapnout primární databáze ve fázi slotu do režimu pro čtení a zápis a spusťte upgrade skriptu (8).    

![Konfigurace geografická replikace databáze SQL. Cloud zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Pokud aktualizace byla úspěšně dokončena nyní jste připraveni k přepínači koncoví uživatelé na V2 verzi aplikace. Následující diagram znázorňuje kroky.

1. Přepínač aktivní koncový bod v WATM profil, který se <i>contoso-2.azurewebsites.net</i>, které nyní odkazuje na verze V2 webového serveru (9). Nyní bude produkční slot s aplikací V2 a provoz koncový uživatel se přesměruje k němu. 
2. Pokud již nepotřebujete V1 aplikace, abyste mohli bezpečně odeberte ji (10 a 11).  

![Konfigurace geografická replikace databáze SQL. Cloud zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Pokud neproběhne proces upgradu, například z důvodu chyby ve skriptu pro upgrade, slotu fáze považovat za narušení. Vrácení aplikace do stavu před upgradem jednoduše obnovit pomocí aplikace v produkčním slotu s úplným přístupem. Kroky jsou uvedeny další diagram.    

1. Nastavte kopie primární databáze v produkčním slotu do režimu pro čtení a zápis (12). Tato akce obnoví úplné V1 funkčně v produkčním slotu.
2. Provedení analýzy příčina kořenové a odebrat ohrožené součásti ve fázi slotu (13 a 14). 

V tomto okamžiku je plně funkční aplikaci a lze je opakovat jednotlivé kroky upgradu.

> [!NOTE]
> Vrácení zpět nevyžaduje změny v profilu WATM jako již odkazuje na <i>contoso-1.azurewebsites.net</i> jako aktivní koncový bod.
> 
> 

![Konfigurace geografická replikace databáze SQL. Cloud zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

Klíč **využít** této možnosti je, aniž by to ohrozilo vaší kontinuity podnikových procesů během upgradu můžete upgradovat aplikaci a její geograficky redundantní kopie paralelně. Hlavní **kompromis** je, že vyžaduje dvojité redundance jednotlivých součástí aplikace a proto způsobuje vyšší dolaru náklady. Zahrnuje také složitější pracovního postupu. 

## <a name="summary"></a>Souhrn
Tyto dvě metody upgradu, které jsou popsané v článku se liší v složitost a dolaru náklady ale oba soustředit na minimalizovat čas, kdy je omezený na jen pro čtení operace koncového uživatele. Tento čas je přímo definované doba trvání upgradu skriptu. Nezávisí na velikost databáze, úroveň služby, které jste zvolili, konfiguraci webu a dalších faktorů, které nelze snadno řídit. Je to proto, že všechny přípravné kroky jsou odpojené od jednotlivé kroky upgradu a lze provést bez dopadu na produkční aplikaci. Efektivitu skriptu pro upgrade je klíčovým faktorem, který určuje činnost koncového uživatele při upgradech. Proto je nejlepší způsob je možné zvýšit činnosti spojené se zaměříte na provedení upgradu skriptu co nejúčinnější.  

## <a name="next-steps"></a>Další kroky
* Přehled kontinuity obchodních a scénářů najdete v tématu [obchodní kontinuity přehled](sql-database-business-continuity.md).
* Další informace o Azure SQL Database automatizované zálohování najdete v tématu [automatizované zálohování SQL Database](sql-database-automated-backups.md).
* Další informace o použití automatizované zálohování pro obnovení, najdete v části [obnovit databázi ze zálohy pro automatické](sql-database-recovery-using-backups.md).
* Další informace o možnosti rychlejší obnovení najdete v tématu [aktivní geografickou replikací](sql-database-geo-replication-overview.md).


