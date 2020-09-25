---
title: Postupné upgrady aplikací
description: Naučte se používat Azure SQL Database geografickou replikaci k podpoře postupného upgradu vaší cloudové aplikace.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 8645e8c1f1f371f1416a998af41104ebb6867eea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334879"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Správa postupného upgradu cloudových aplikací pomocí SQL Database aktivní geografická replikace
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Naučte se používat [aktivní geografickou replikaci](auto-failover-group-overview.md) v Azure SQL Database k zajištění postupného upgradu vaší cloudové aplikace. Vzhledem k tomu, že upgrady jsou rušivé operace, měly by být součástí plánování a návrhu kontinuity vaší firmy. V tomto článku se podíváme na dvě různé metody orchestrace procesu upgradu a podíváme se na výhody a nevýhody jednotlivých možností. Pro účely tohoto článku odkazujeme na aplikaci, která se skládá z webu, který je připojený k izolované databázi jako jeho Datová vrstva. Naším cílem je upgradovat verzi 1 (V1) aplikace na verzi 2 (v2), aniž by to mělo žádný významný dopad na činnost koncového uživatele.

Při vyhodnocování možností upgradu Vezměte v úvahu tyto faktory:

* Dopad na dostupnost aplikace během upgradů, například na to, jak dlouho můžou být funkce aplikací omezené nebo omezené.
* Možnost vrátit zpět, pokud se upgrade nezdařil.
* Ohrožení zabezpečení aplikace v případě, že během upgradu dojde k nesouvisejícímu selhání.
* Celkové náklady na dolary Tento faktor zahrnuje další redundanci databáze a přírůstkové náklady na dočasné komponenty, které používá proces upgradu.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Upgradujte aplikace, které spoléhají na zálohování databáze pro zotavení po havárii.

Pokud vaše aplikace spoléhá na automatické zálohování databáze a používá geografickou obnovu pro zotavení po havárii, nasadí se do jedné oblasti Azure. Chcete-li minimalizovat přerušení uživatele, vytvořte v této oblasti přípravné prostředí se všemi komponentami aplikace zapojenými do upgradu. První diagram znázorňuje provozní prostředí před procesem upgradu. Koncový bod `contoso.azurewebsites.net` představuje provozní prostředí webové aplikace. Aby bylo možné vrátit zpět upgrade, je nutné vytvořit přípravné prostředí s plně synchronizovanou kopií databáze. Pomocí těchto kroků vytvořte přípravné prostředí pro upgrade:

1. Vytvořte sekundární databázi ve stejné oblasti Azure. Sledujte sekundární a zjistěte, jestli je proces osazenní dokončený (1).
2. Vytvořte nové prostředí pro vaši webovou aplikaci a zavolejte ho "fázování". Bude zaregistrována v Azure DNS s adresou URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Tyto přípravné kroky nebudou mít vliv na produkční prostředí, které může fungovat v režimu úplného přístupu.

![SQL Database konfiguraci geografické replikace pro zotavení po havárii v cloudu.](./media/manage-application-rolling-upgrade/option1-1.png)

Po dokončení přípravných kroků je aplikace připravená na vlastní upgrade. Další diagram znázorňuje kroky, které jsou součástí procesu upgradu:

1. Nastavte primární databázi na režim jen pro čtení (3). Tento režim zaručuje, že produkční prostředí webové aplikace (V1) zůstane během upgradu jen pro čtení, což zabrání rozdílům v datech mezi instancemi databáze V1 a v2.
2. Odpojte sekundární databázi pomocí režimu plánovaného ukončení (4). Tato akce vytvoří plně synchronizovanou nezávislou kopii primární databáze. Tato databáze bude upgradována.
3. Převeďte sekundární databázi do režimu pro čtení i zápis a spusťte skript upgradu (5).

![SQL Database konfiguraci geografické replikace pro zotavení po havárii v cloudu.](./media/manage-application-rolling-upgrade/option1-2.png)

Pokud se upgrade úspěšně dokončí, teď jste připraveni přepnout uživatele na upgradovanou kopii aplikace, která se stane provozním prostředím. Přepínání zahrnuje několik dalších kroků, jak je znázorněno v následujícím diagramu:

1. Aktivujte operaci prohození mezi produkčním a přípravným prostředím webové aplikace (6). Tato operace přepne adresy URL obou prostředí. Nyní `contoso.azurewebsites.net` odkazuje na verzi v2 webu a databáze (produkční prostředí). 
2. Pokud již nepotřebujete verzi V1, která se po prohození stala přípravnou kopií, můžete pracovní prostředí vyřadit z provozu (7).

![SQL Database konfiguraci geografické replikace pro zotavení po havárii v cloudu.](./media/manage-application-rolling-upgrade/option1-3.png)

Pokud je proces upgradu neúspěšný (například kvůli chybě v skriptu pro upgrade), zvažte, jestli má být ohrožené pracovní prostředí. Chcete-li vrátit aplikaci do stavu před upgradem, vraťte aplikaci v provozním prostředí na úplný přístup. Následující diagram znázorňuje kroky převerze:

1. Nastavte kopii databáze do režimu čtení i zápisu (8). Tato akce obnoví plnou funkčnost verze V1 produkční kopie.
2. Proveďte analýzu původní příčiny a vyřaďte z provozu pracovní prostředí (9).

V tuto chvíli je aplikace plně funkční a můžete postup upgradu zopakovat.

> [!NOTE]
> Vrácení zpět nevyžaduje změny DNS, protože jste ještě neprováděli operaci prohození.

![SQL Database konfiguraci geografické replikace pro zotavení po havárii v cloudu.](./media/manage-application-rolling-upgrade/option1-4.png)

Klíčovou výhodou této možnosti je, že aplikaci v jedné oblasti můžete upgradovat pomocí sady jednoduchých kroků. Cena za dolar za upgrade je poměrně nízká. 

Hlavním kompromisem je to, že pokud během upgradu dojde k závažné chybě, obnovení do stavu před upgradem zahrnuje opětovné nasazení aplikace v jiné oblasti a obnovení databáze ze zálohy pomocí geografického obnovení. Výsledkem tohoto procesu je výrazné výpadky.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Upgradovat aplikace, které spoléhají na geografickou replikaci databáze pro zotavení po havárii

Pokud vaše aplikace používá aktivní geografickou replikaci nebo skupiny s automatickým převzetím služeb při selhání pro zajištění provozní kontinuity, nasadí se do aspoň dvou různých oblastí. V oblasti zálohování je aktivní primární databáze v primární oblasti a sekundární databáze jen pro čtení. Spolu s faktory uvedenými na začátku tohoto článku musí proces upgradu také zaručit, že:

* Aplikace zůstane během procesu upgradu v každé době chráněná před závažnými chybami.
* Geografické redundantní komponenty aplikace jsou upgradovány paralelně s aktivními komponentami.

K dosažení těchto cílů Kromě použití Web Apps prostředí můžete využít výhod Azure Traffic Manager pomocí profilu převzetí služeb při selhání s jedním aktivním koncovým bodem a jedním záložním koncovým bodem. Další diagram znázorňuje provozní prostředí před procesem upgradu. Webové stránky `contoso-1.azurewebsites.net` a `contoso-dr.azurewebsites.net` reprezentují produkční prostředí aplikace s plnou geografickou redundancí. Provozní prostředí obsahuje následující komponenty:

* Provozní prostředí webové aplikace `contoso-1.azurewebsites.net` v primární oblasti (1)
* Primární databáze v primární oblasti (2)
* Pohotovostní instance webové aplikace v oblasti zálohování (3)
* Geograficky replikovaná sekundární databáze v oblasti zálohování (4)
* Traffic Manager profil výkonu s názvem online koncový bod `contoso-1.azurewebsites.net` a s názvem offline koncový bod `contoso-dr.azurewebsites.net`

Aby bylo možné vrátit zpět upgrade, je nutné vytvořit přípravné prostředí s plně synchronizovanou kopií aplikace. Vzhledem k tomu, že je potřeba zajistit, aby se aplikace mohla rychle obnovit v případě, že během procesu upgradu dojde k závažným chybám, musí být pracovní prostředí také geograficky redundantní. Pro vytvoření přípravného prostředí pro upgrade je potřeba provést tyto kroky:

1. Nasaďte přípravné prostředí webové aplikace v primární oblasti (6).
2. Vytvořte sekundární databázi v primární oblasti Azure (7). Konfigurace přípravného prostředí webové aplikace pro připojení. 
3. Pomocí replikace sekundární databáze v primární oblasti vytvořte v oblasti zálohování jinou geograficky redundantní sekundární databázi. (Tato metoda se nazývá *zřetězená geografická replikace*.) (8).
4. Nasaďte přípravné prostředí instance webové aplikace v oblasti zálohování (9) a nakonfigurujte ji tak, aby se připojila geograficky redundantní sekundární databáze vytvořená v (8).

> [!NOTE]
> Tyto přípravné kroky nebudou mít vliv na aplikaci v produkčním prostředí. Zůstane plně funkční v režimu pro čtení i zápis.

![SQL Database konfiguraci geografické replikace pro zotavení po havárii v cloudu.](./media/manage-application-rolling-upgrade/option2-1.png)

Po dokončení přípravných kroků je pracovní prostředí připraveno k upgradu. Následující diagram znázorňuje tyto kroky upgradu:

1. Nastavte primární databázi v provozním prostředí na režim jen pro čtení (10). Tento režim zaručuje, že se během upgradu nedojde ke změně provozní databáze (V1), což zabrání rozdílům v datech mezi instancemi databáze V1 a v2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Dokončí geografickou replikaci odpojením sekundárního (11). Tato akce vytvoří nezávislou, plně synchronizovanou kopii provozní databáze. Tato databáze bude upgradována. Následující příklad používá jazyk Transact-SQL, ale je k dispozici také [prostředí PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) . 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Spusťte skript upgradu proti `contoso-1-staging.azurewebsites.net` , `contoso-dr-staging.azurewebsites.net` a pracovní primární databázi (12). Změny databáze budou automaticky replikovány do pracovního sekundárního prostředí.

![SQL Database konfiguraci geografické replikace pro zotavení po havárii v cloudu.](./media/manage-application-rolling-upgrade/option2-2.png)

Pokud se upgrade úspěšně dokončí, teď jste připraveni přepnout uživatele na verzi v2 aplikace. Následující diagram znázorňuje postup, který je součástí:

1. Aktivujte operaci prohození mezi produkčním a přípravným prostředím webové aplikace v primární oblasti (13) a v oblasti zálohování (14). V2 aplikace se teď staly produkčním prostředím, s redundantní kopií v oblasti zálohování.
2. Pokud už nepotřebujete aplikaci V1 (15 a 16), můžete pracovní prostředí vyřadit z provozu.

![SQL Database konfiguraci geografické replikace pro zotavení po havárii v cloudu.](./media/manage-application-rolling-upgrade/option2-3.png)

Pokud je proces upgradu neúspěšný (například kvůli chybě v skriptu pro upgrade), zvažte, jestli je pracovní prostředí v nekonzistentním stavu. Chcete-li vrátit aplikaci zpět do stavu před upgradem, vraťte se na použití v1 aplikace v provozním prostředí. Požadované kroky se zobrazí v dalším diagramu:

1. Nastavte primární databázovou kopii v produkčním prostředí do režimu pro čtení i zápis (17). Tato akce obnoví plná funkčnost v1 v produkčním prostředí.
2. Proveďte analýzu původní příčiny a opravte nebo odeberte pracovní prostředí (18 a 19).

V tuto chvíli je aplikace plně funkční a můžete postup upgradu zopakovat.

> [!NOTE]
> Vrácení zpět nevyžaduje změny DNS, protože jste neprováděli operaci swap.

![SQL Database konfiguraci geografické replikace pro zotavení po havárii v cloudu.](./media/manage-application-rolling-upgrade/option2-4.png)

Klíčovou výhodou této možnosti je, že můžete upgradovat aplikaci i její geograficky redundantní kopii paralelně bez toho, aby během upgradu došlo k narušení vaší provozní kontinuity.

Hlavním kompromisem je to, že vyžaduje dvojí redundanci každé součásti aplikace, a proto se zvýší náklady na dolar. Zahrnuje taky složitější pracovní postup.

## <a name="summary"></a>Shrnutí

Tyto dvě metody upgradu popsané v článku se liší od nákladů na složitost a dolar, ale obě se zaměřují na minimalizaci toho, jak dlouho je uživatel omezený na operace jen pro čtení. Tato doba je přímo definovaná po dobu trvání skriptu upgradu. Nezávisí na velikosti databáze, zvolené úrovni služby, konfiguraci webu nebo jiných faktorech, které nemůžete snadno ovládat. Všechny kroky přípravy jsou oddělené od kroků upgradu a neovlivňují produkční aplikaci. Efektivita skriptu upgradu je klíčovým faktorem, který určuje prostředí uživatele během upgradů. Nejlepším způsobem, jak toto prostředí zlepšit, je zaměřit se na to, co je potřeba k tomu, aby byl skript upgradu co nejefektivnější.

## <a name="next-steps"></a>Další kroky

* Přehled provozní kontinuity a scénáře najdete v tématu [Přehled provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Další informace o tom, jak Azure SQL Database aktivní geografickou replikaci, najdete v tématu [Vytvoření čitelných sekundárních databází pomocí aktivní geografické replikace](active-geo-replication-overview.md).
* Další informace o Azure SQL Database skupin automatického převzetí služeb při selhání najdete v tématu [použití skupin s automatickým převzetím služeb při selhání k zajištění transparentního a koordinovaného převzetí služeb](auto-failover-group-overview.md)
* Další informace o přípravném prostředí v Azure App Service najdete v tématu [Nastavení přípravných prostředí v Azure App Service](../../app-service/deploy-staging-slots.md).
* Další informace o profilech Azure Traffic Manager najdete v tématu [Správa profilu azure Traffic Manager](../../traffic-manager/traffic-manager-manage-profiles.md).
