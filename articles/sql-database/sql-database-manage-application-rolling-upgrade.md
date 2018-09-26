---
title: Postupné upgrady aplikací – Azure SQL Database | Dokumentace Microsoftu
description: Další informace o použití geografické replikace databáze SQL Azure pro podporu online upgrady cloudových aplikací.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 08/23/2018
ms.openlocfilehash: 4d518c959943184c38ed78cc8eb5449b306c8b04
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162300"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Správa postupné upgrady cloudových aplikací s využitím SQL Database aktivní geografické replikace
> [!NOTE]
> [Aktivní geografická replikace](sql-database-geo-replication-overview.md) je nyní k dispozici pro všechny databáze ve všech úrovních.
> 

Další informace o použití [geografickou replikaci](sql-database-geo-replication-overview.md) ve službě SQL Database povolit postupné upgrady cloudových aplikací. Protože upgrade je rušivé operace, měla by být součástí vaší obchodní kontinuity podnikových procesů plánování a návrhu. V tomto článku jsme podívejte se na dva různé způsoby Orchestrace procesu upgradu a popisují výhody a nevýhody jednotlivých možností. Pro účely tohoto článku použijeme jednoduchou aplikaci, která obsahuje webový server připojen k izolované databáze jako jeho Datová vrstva. Naším cílem je upgrade verze 1 aplikace na verzi 2 bez významného dopadu na činnost koncového uživatele. 

Při vyhodnocování možnosti upgradu byste měli zvážit následující faktory:

* Dopad na dostupnost aplikace během upgradu. Jak dlouho funkce aplikace může omezené nebo snížený výkon.
* Možnost vrácení zpět v případě upgrade nezdaří.
* Ohrožení zabezpečení aplikace, pokud dojde k nesouvisejícím závažnému selhání během upgradu.
* Celkový počet dolar nákladů.  To zahrnuje další redundanci a dílčí náklady dočasné komponent používá proces upgradu. 

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Upgradování aplikací, které jsou závislé na zálohování databází můžete pro zotavení po havárii
Pokud vaše aplikace využívá automatické zálohování databází a používá geografické obnovení pro zotavení po havárii, je obvykle nasadit do jedné oblasti Azure. V tomto případě procesu upgradu zahrnuje vytváření záloh nasazení všech součástí aplikace při upgradu. Aby se minimalizovalo přerušení koncového uživatele bude využívat Azure Traffic Manager (ATM) s profilem převzetí služeb při selhání.  Následující diagram znázorňuje v provozním prostředí před upgradem. Koncový bod <i>contoso-1.azurewebsites.net</i> představuje produkčního slotu aplikace, kterou je potřeba upgradovat. Povolit možnost vrácení zpět upgradu můžete potřebovat vytvořit fázi slot s plně synchronizované kopie aplikace. Následující kroky je potřeba připravit aplikaci na upgrade:

1. Vytvořte slot fáze upgradu. Chcete provést, vytvořte sekundární databázi (1) a nasadit stejné webové stránky ve stejné oblasti Azure. Monitorování sekundární zobrazíte, pokud je osazení proces dokončen.
2. Vytvoření profilu převzetí služeb při selhání v ATM s <i>contoso-1.azurewebsites.net</i> jako online koncový bod a <i>contoso-2.azurewebsites.net</i> jako offline. 

> [!NOTE]
> Mějte na paměti, přípravné kroky nebude mít vliv na aplikaci v produkčním slotu a může fungovat v režimu plný přístup.
>  

![Konfigurace replikace se službou SQL Database Go. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option1-1.png)

Po dokončení kroků přípravy aplikace je připravena pro skutečném upgradu. Následující diagram znázorňuje kroky v procesu upgradu. 

1. Nastavte primární databází v produkčním slotu do režimu jen pro čtení (3). Tím se zaručí, že produkční instance aplikace (V1) zůstane jen pro čtení během upgradu zamezuje tak odchylkami dat mezi instancemi databáze V1 a V2.  
2. Odpojte sekundární databázi pomocí plánované ukončení režimu (4). Vytvoří kopii plně synchronizovaná nezávislé primární databáze. Tato databáze se upgraduje.
3. Vypnout primární databáze do režimu pro čtení i zápis a spusťte upgrade skript ve fázi slotu (5).     

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option1-2.png)

Pokud se upgrade úspěšně dokončen nyní jste připraveni přepnout koncovým uživatelům na dvoufázové instalace kopie aplikace. Nyní bude produkčního slotu aplikace.  To zahrnuje několik kroků jak je znázorněno na následujícím diagramu.

1. Přepnout online koncový bod v profilu ATM <i>contoso-2.azurewebsites.net</i>, který odkazuje na verze V2 na webu (6). Teď bude produkční slot s V2 aplikace a koncových uživatelů provoz se směřuje do něj.  
2. Pokud potřebujete už součásti aplikace V1, což vám umožní bezpečně odeberte je (7).   

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option1-3.png)

Pokud neproběhne úspěšně procesu upgradu, například z důvodu chyby ve skriptu pro upgrade, slot pro fázi považovat za dojde k ohrožení bezpečnosti. Chcete-li vrátit zpět před upgradem stavu aplikace jednoduše vrátit aplikaci v produkčním slotu plný přístup. Potřebný postup se zobrazí na následující diagram.    

1. Nastavte kopie databáze do režimu pro čtení i zápis (8). Tato akce obnoví úplné V1 funkčně v produkčním slotu.
2. Proveďte analýzu původní příčiny a odebrání ohrožených komponenty ve fázi slotu (9). 

V tomto okamžiku je plně funkční aplikace a jednotlivé kroky upgradu můžete opakovat.

> [!NOTE]
> Vrácení změn nevyžaduje změny v profilu ATM už odkazuje na <i>contoso-1.azurewebsites.net</i> jako aktivní koncový bod.
> 
> 

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option1-4.png)

Klíč **využívat** této možnosti je, že můžete upgradovat aplikace v jedné oblasti, používáte sadu jednoduchých krocích. Náklady na dolar upgradu jsou relativně nízký. Hlavní **kompromis** je, že pokud dojde k závažné chybě během upgradu bude zahrnovat obnovení do stavu před upgradem, opětovné nasazení aplikace v jiné oblasti a obnovení databáze ze zálohy pomocí geografické obnovení. Tento proces bude mít za následek významnějších výpadků.   

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Upgrade aplikace, které využívají databáze geografickou replikaci pro zotavení po havárii
Pokud vaše aplikace využívá georeplikace zajišťuje nepřetržitý chod podniků, je nasazený na alespoň dvěma různými oblastmi s aktivní nasazení v primární oblasti a pohotovostní nasazení v oblasti zálohování. Kromě faktory, které již bylo zmíněno dříve proces upgradu musí zaručit, že:

* Aplikace i nadále chráněn z katastrofických selhání po celou dobu, během procesu upgradu
* Geograficky redundantní komponenty aplikace jsou upgradovány zároveň s aktivní komponenty

K dosažení těchto cílů využije pomocí profilu převzetí služeb při selhání s jedním aktivním a tři zálohy koncových bodů Azure Traffic Manager (ATM).  Následující diagram znázorňuje v provozním prostředí před upgradem. Na webech <i>contoso-1.azurewebsites.net</i> a <i>contoso-dr.azurewebsites.net</i> představují produkčního slotu aplikace s úplnou geografickou redundanci. Povolit možnost vrácení zpět upgradu můžete potřebovat vytvořit fázi slot s plně synchronizované kopie aplikace. Protože je potřeba zajistit, že aplikace může rychle obnovit v případě, že během procesu upgradu dojde k závažné chybě, musí být také geograficky redundantní slot pro fázi. Následující kroky je potřeba připravit aplikaci na upgrade:

1. Vytvořte slot fáze upgradu. Chcete provést, sekundární databázi (1) vytvářet a nasazovat totožnou kopii webové stránky ve stejné oblasti Azure. Monitorování sekundární zobrazíte, pokud je osazení proces dokončen.
2. Vytvoření geograficky redundantní sekundární databáze v slot pro fázi díky geografické replikaci sekundární databáze pro zálohování oblasti (tomu se říká "zřetězené geografickou replikaci"). Monitorování záloh sekundární jestli osazení procesu je dokončené (3).
3. Vytvořit kopii pohotovostní webové stránky v oblasti zálohování a propojit jej na geograficky redundantní sekundární (4).  
4. Přidat další koncové body <i>contoso-2.azurewebsites.net</i> a <i>contoso-3.azurewebsites.net</i> profilu převzetí služeb při selhání v ATM jako koncové body v režimu offline (5). 

> [!NOTE]
> Mějte na paměti, přípravné kroky nebude mít vliv na aplikaci v produkčním slotu a může fungovat v režimu plný přístup.
> 
> 

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option2-1.png)

Po dokončení kroků přípravy, slot pro fázi připravený k upgradu. Následující diagram znázorňuje jednotlivé kroky upgradu.

1. Nastavte primární databází v produkčním slotu do režimu jen pro čtení (6). Tím se zaručí, že produkční instance aplikace (V1) zůstane jen pro čtení během upgradu zamezuje tak odchylkami dat mezi instancemi databáze V1 a V2.  
2. Odpojte sekundární databáze ve stejné oblasti pomocí plánované ukončení režimu (7). Vytvoří kopii plně synchronizovaná nezávislé primární databázi, která se automaticky stane primární po ukončení. Tato databáze se upgraduje.
3. Zapnout primární databází ve fázi slotu do režimu pro čtení i zápis a spuštění skriptu aktualizace (8).    

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option2-2.png)

Pokud jste upgrade byl úspěšně dokončen teď jste připravení začít přepnout koncovým uživatelům na V2 verze aplikace. Následující diagram znázorňuje potřebnými kroky.

1. Přepnout aktivní koncový bod v profilu ATM <i>contoso-2.azurewebsites.net</i>, které je teď odkazuje na verze V2 na webu (9). Teď bude produkční slot s V2 aplikace a koncových uživatelů provoz se směřuje do něj. 
2. Pokud potřebujete už aplikace V1, což vám umožní bezpečně odeberte (10 a 11).  

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option2-3.png)

Pokud neproběhne úspěšně procesu upgradu, například z důvodu chyby ve skriptu pro upgrade, slot pro fázi považovat za dojde k ohrožení bezpečnosti. Chcete-li vrátit zpět před upgradem stavu aplikace jednoduše obnovit pomocí aplikace v produkčním slotu s úplným přístupem. Potřebný postup se zobrazí na následující diagram.    

1. V produkčním slotu do režimu pro čtení i zápis (12) nastavte kopie primární databáze. Tato akce obnoví úplné V1 funkčně v produkčním slotu.
2. Proveďte analýzu původní příčiny a odebrání ohrožených komponenty ve fázi slotu (13 a 14). 

V tomto okamžiku je plně funkční aplikace a jednotlivé kroky upgradu můžete opakovat.

> [!NOTE]
> Vrácení změn nevyžaduje změny v profilu ATM už odkazuje na <i>contoso-1.azurewebsites.net</i> jako aktivní koncový bod.
> 
> 

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/Option2-4.png)

Klíč **využívat** této možnosti je, že můžete upgradovat aplikaci a její geograficky redundantní kopie paralelně bez negativního vlivu kontinuity během upgradu. Hlavní **kompromis** je, že vyžaduje dvojitá redundance jednotlivých součástí aplikace a proto má za následek vyšší náklady na dolar. Zahrnuje také složitější pracovního postupu. 

## <a name="summary"></a>Souhrn
Dvě metody upgradu je popsáno v následujícím článku se liší v složitost a dolar nákladů ale oba soustředit na minimalizovat čas, kdy je omezená na jen pro čtení operace koncového uživatele. Tento čas je přímo určené dobu trvání upgradu skriptu. Není závislý na velikosti databáze, úroveň služby, kterou jste zvolili, konfiguraci webového serveru a dalších faktorů, které nelze snadno řídit. Je to proto, že všechny přípravné kroky jsou oddělené od kroky upgradu a jde provést bez dopadu na produkční aplikace. Efektivita upgradovací skript je klíčovým faktorem, který určuje prostředí pro koncové uživatele během upgradu. Takže nejlepším způsobem ji můžete vylepšit je zaměření úsilí na to, jak co nejúčinnější upgradovací skript pro.  

## <a name="next-steps"></a>Další postup
* Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
* Další informace o Azure SQL Database, automatické zálohování, naleznete v tématu [automatické zálohování SQL Database](sql-database-automated-backups.md).
* Další informace o obnovení pomocí automatizovaného zálohování, naleznete v tématu [obnovit databázi z automatizovaných záloh](sql-database-recovery-using-backups.md).
* Další informace o možnosti rychlejší obnovení najdete v tématu [aktivní geografickou replikaci](sql-database-geo-replication-overview.md).


