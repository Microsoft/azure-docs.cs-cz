---
title: Postupné upgrady aplikací
description: Zjistěte, jak pomocí geografické replikace Azure SQL Database podporovat online upgrady vaší cloudové aplikace.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 9c627c3e597fdcd3859ce02ea208fc7a8b5d612b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822859"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Správa postupných upgradů cloudových aplikací pomocí aktivní geografické replikace databáze SQL Database

Zjistěte, jak používat [aktivní geografickou replikaci](sql-database-auto-failover-group.md) v Azure SQL Database k povolení postupných upgradů vaší cloudové aplikace. Vzhledem k tomu, že upgrady jsou rušivé operace, měly by být součástí plánování a návrhu kontinuity provozu. V tomto článku se podíváme na dvě různé metody orchestrace procesu upgradu a diskutovat o výhodách a kompromisy každé možnosti. Pro účely tohoto článku odkazujeme na aplikaci, která se skládá z webu, který je připojen k jedné databázi jako jeho datové vrstvy. Naším cílem je upgradovat verzi 1 (V1) aplikace na verzi 2 (V2) bez významného dopadu na uživatelské prostředí.

Při hodnocení možností upgradu zvažte tyto faktory:

* Dopad na dostupnost aplikací během upgradů, například jak dlouho mohou být funkce aplikace omezeny nebo sníženy.
* Možnost vrátit zpět, pokud upgrade selže.
* Chyba zabezpečení aplikace, pokud během inovace dojde k nesouvisejícímu katastrofickému selhání.
* Celkové náklady na dolar. Tento faktor zahrnuje další redundanci databáze a přírůstkové náklady na dočasné součásti používané procesem upgradu.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Upgrade aplikací, které spoléhají na zálohování databáze pro zotavení po havárii

Pokud vaše aplikace závisí na automatické zálohování databáze a používá geografické obnovení pro zotavení po havárii, je nasazená do jedné oblasti Azure. Chcete-li minimalizovat narušení uživatele, vytvořte pracovní prostředí v této oblasti se všemi součástmi aplikace zapojenými do upgradu. První diagram znázorňuje provozní prostředí před procesem upgradu. Koncový bod `contoso.azurewebsites.net` představuje produkční prostředí webové aplikace. Chcete-li vrátit zpět upgrade, musíte vytvořit pracovní prostředí s plně synchronizovanou kopii databáze. Chcete-li vytvořit pracovní prostředí pro upgrade, postupujte takto:

1. Vytvořte sekundární databázi ve stejné oblasti Azure. Sledujte sekundární, abyste zjistili, zda je proces výsevu dokončen (1).
2. Vytvořte nové prostředí pro webovou aplikaci a nazvěte ji "Staging". Bude zaregistrován v Azure DNS `contoso-staging.azurewebsites.net` s adresou URL (2).

> [!NOTE]
> Tyto kroky přípravy nebudou mít vliv na produkční prostředí, které může fungovat v režimu úplného přístupu.

![Konfigurace geografické replikace sql database pro cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Po dokončení přípravných kroků je aplikace připravena pro vlastní upgrade. Následující diagram znázorňuje kroky související s procesem upgradu:

1. Nastavte primární databázi do režimu jen pro čtení (3). Tento režim zaručuje, že produkční prostředí webové aplikace (V1) zůstane jen pro čtení během upgradu, čímž se zabrání rozdílům dat mezi instancemi databáze V1 a V2.
2. Odpojte sekundární databázi pomocí režimu plánovaného ukončení (4). Tato akce vytvoří plně synchronizované, nezávislé kopie primární databáze. Tato databáze bude inovována.
3. Přepněte sekundární databázi do režimu čtení a zápisu a spusťte skript upgradu (5).

![Konfigurace geografické replikace sql database pro cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Pokud upgrade úspěšně dokončí, jste nyní připraveni přepnout uživatele na inovované kopírování aplikace, která se stane produkčním prostředím. Přepínání zahrnuje několik dalších kroků, jak je znázorněno v následujícím diagramu:

1. Aktivujte operaci odkládacího režimu mezi produkčním a testovacím prostředím webové aplikace (6). Tato operace přepne adresy URL obou prostředí. Nyní `contoso.azurewebsites.net` odkazuje na verzi V2 webu a na databázi (produkční prostředí). 
2. Pokud již nepotřebujete verzi V1, která se stala pracovní kopií po odkládání, můžete vyřazeno z provozu pracovní prostředí (7).

![Konfigurace geografické replikace sql database pro cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Pokud je proces upgradu neúspěšný (například z důvodu chyby ve skriptu upgradu), zvažte ohrožení pracovního prostředí. Chcete-li vrátit zpět aplikaci do stavu před upgradem, vraťte aplikaci v provozním prostředí k plnému přístupu. Následující diagram znázorňuje kroky reverze:

1. Nastavte kopii databáze do režimu čtení a zápisu (8). Tato akce obnoví plnou funkci V1 produkční kopie.
2. Proveďte analýzu hlavní příčiny a vyřazte pracovní prostředí z provozu (9).

V tomto okamžiku aplikace je plně funkční a můžete opakovat kroky upgradu.

> [!NOTE]
> Vrácení zpět nevyžaduje změny DNS, protože jste ještě neprovedli operaci prohození.

![Konfigurace geografické replikace sql database pro cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Hlavní výhodou této možnosti je, že můžete upgradovat aplikaci v jedné oblasti pomocí sady jednoduchých kroků. Dolarové náklady na upgrade jsou relativně nízké. 

Hlavní kompromis je, že pokud dojde k závažné selhání během upgradu, obnovení stavu před upgradem zahrnuje opětovné nasazení aplikace v jiné oblasti a obnovení databáze ze zálohy pomocí geografické obnovení. Tento proces má za následek významné prostoje.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Upgrade aplikací, které jsou závislé na geografické replikaci databáze pro zotavení po havárii

Pokud vaše aplikace používá aktivní geografickou replikaci nebo skupiny s automatickou podporou převzetí služeb při selhání pro kontinuitu podnikání, nasadí se alespoň do dvou různých oblastí. V primární oblasti je aktivní primární databáze a sekundární databáze jen pro čtení v oblasti zálohování. Spolu s faktory uvedenými na začátku tohoto článku musí proces upgradu také zaručit, že:

* Aplikace zůstává chráněna před katastrofickými chybami po celou dobu během procesu upgradu.
* Geograficky redundantní součásti aplikace jsou upgradovány paralelně s aktivními součástmi.

K dosažení těchto cílů, kromě použití prostředí webových aplikací, budete využívat Výhod Azure Traffic Manager pomocí profilu převzetí služeb při selhání s jedním aktivním koncovým bodem a jeden koncový bod zálohování. Následující diagram znázorňuje provozní prostředí před procesem upgradu. Webové stránky `contoso-1.azurewebsites.net` `contoso-dr.azurewebsites.net` a představují produkční prostředí aplikace s úplnou geografickou redundancí. Produkční prostředí zahrnuje následující součásti:

* Produkční prostředí webové aplikace `contoso-1.azurewebsites.net` v primární oblasti (1)
* Primární databáze v primární oblasti (2)
* Pohotovostní instance webové aplikace v oblasti zálohování (3)
* Geograficky replikovaná sekundární databáze v oblasti zálohování (4)
* Profil výkonu Traffic Manageru s `contoso-1.azurewebsites.net` volaným online koncovým bodem a offline koncovým bodem nazývaným`contoso-dr.azurewebsites.net`

Chcete-li, aby bylo možné vrátit zpět upgrade, je nutné vytvořit pracovní prostředí s plně synchronizovanou kopii aplikace. Vzhledem k tomu, že je třeba zajistit, že aplikace může rychle obnovit v případě, že dojde ke katastrofickému selhání během procesu upgradu, pracovní prostředí musí být také geograficky redundantní. K vytvoření pracovního prostředí pro upgrade jsou nutné následující kroky:

1. Nasazení pracovního prostředí webové aplikace v primární oblasti (6).
2. Vytvořte sekundární databázi v primární oblasti Azure (7). Nakonfigurujte pracovní prostředí webové aplikace tak, aby se k ní připojilo. 
3. Vytvořte další geograficky redundantní sekundární databázi v oblasti zálohování replikací sekundární databáze v primární oblasti. (Tato metoda se nazývá *zřetězená geografická replikace*.) (8).
4. Nasaďte pracovní prostředí instance webové aplikace v oblasti zálohování (9) a nakonfigurujte ji tak, aby se připojila k geograficky redundantní sekundární databázi vytvořené v bodě (8).

> [!NOTE]
> Tyto kroky přípravy nebude mít vliv na aplikaci v produkčním prostředí. Zůstane plně funkční v režimu čtení a zápisu.

![Konfigurace geografické replikace sql database pro cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Po dokončení přípravných kroků je pracovní prostředí připraveno k upgradu. Následující diagram znázorňuje tyto kroky upgradu:

1. Nastavte primární databázi v provozním prostředí do režimu jen pro čtení (10). Tento režim zaručuje, že produkční databáze (V1) se během upgradu nezmění, čímž zabrání rozdílům dat mezi instancemi databáze V1 a V2.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Ukončit geografickou replikaci odpojením sekundární (11). Tato akce vytvoří nezávislou, ale plně synchronizovanou kopii produkční databáze. Tato databáze bude inovována. Následující příklad používá Transact-SQL, ale [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) je také k dispozici. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Spusťte skript `contoso-1-staging.azurewebsites.net` `contoso-dr-staging.azurewebsites.net`upgradu proti , a pracovní primární databázi (12). Změny databáze budou automaticky replikovány do pracovní sekundární.

![Konfigurace geografické replikace sql database pro cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Pokud upgrade úspěšně skončí, jste nyní připraveni přepnout uživatele na verzi V2 aplikace. Následující diagram znázorňuje související kroky:

1. Aktivujte operaci odkládacího režimu mezi produkčním a testovacím prostředím webové aplikace v primární oblasti (13) a v oblasti zálohování (14). V2 aplikace se nyní stává produkčním prostředím s redundantní kopií v oblasti zálohování.
2. Pokud už aplikaci V1 (15 a 16) nepotřebujete, můžete vyřazeno z provozu pracovní prostředí.

![Konfigurace geografické replikace sql database pro cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Pokud je proces upgradu neúspěšný (například z důvodu chyby ve skriptu upgradu), zvažte pracovní prostředí v nekonzistentním stavu. Chcete-li vrátit zpět aplikace do stavu před upgradem, vraťte se k použití V1 aplikace v provozním prostředí. Požadované kroky jsou uvedeny na následujícím diagramu:

1. Nastavte kopii primární databáze v produkčním prostředí do režimu čtení a zápisu (17). Tato akce obnoví plnou funkci V1 v provozním prostředí.
2. Proveďte analýzu hlavní příčiny a opravou nebo odeberte pracovní prostředí (18 a 19).

V tomto okamžiku aplikace je plně funkční a můžete opakovat kroky upgradu.

> [!NOTE]
> Vrácení zpět nevyžaduje změny DNS, protože jste neprovedli operaci prohození.

![Konfigurace geografické replikace sql database pro cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Hlavní výhodou této možnosti je, že můžete upgradovat aplikaci i její geograficky redundantní kopii paralelně, aniž by byla ohrožena kontinuita provozu během upgradu.

Hlavní kompromis je, že vyžaduje dvojí redundance každé součásti aplikace, a proto vzniknou vyšší náklady dolaru. Zahrnuje také složitější pracovní postup.

## <a name="summary"></a>Souhrn

Dvě metody upgradu popsané v článku se liší složitostí a náklady v dolarech, ale obě se zaměřují na minimalizaci, jak dlouho je uživatel omezen na operace jen pro čtení. Tato doba je přímo definována dobou trvání skriptu upgradu. Nezávisí na velikosti databáze, vybrané úrovni služby, konfiguraci webu nebo jiných faktorech, které nelze snadno řídit. Všechny kroky přípravy jsou odděleny od kroků upgradu a nemají vliv na produkční aplikaci. Efektivita skriptu upgradu je klíčovým faktorem, který určuje uživatelské prostředí během upgradu. Takže nejlepší způsob, jak zlepšit tuto zkušenost, je zaměřit své úsilí na to, aby byl skript upgradu co nejúčinnější.

## <a name="next-steps"></a>Další kroky

* Přehled kontinuity provozu a scénáře najdete v tématu [Přehled kontinuity provozu](sql-database-business-continuity.md).
* Další informace o aktivní geografické replikaci azure SQL Database najdete v tématu [Vytváření čitelných sekundárních databází pomocí aktivní geografické replikace](sql-database-active-geo-replication.md).
* Další informace o skupinách automatického převzetí služeb při selhání azure SQL Database najdete v [tématu Použití skupin s možností převzetí služeb při selhání pomocí možností povolit transparentní a koordinované převzetí služeb při selhání z více databází](sql-database-auto-failover-group.md).
* Další informace o pracovních prostředích ve službě Azure App Service najdete v [tématu Nastavení pracovních prostředí ve službě Azure App Service](../app-service/deploy-staging-slots.md).
* Další informace o profilech Azure Traffic Manageru najdete [v tématu Správa profilu Azure Traffic Manageru](../traffic-manager/traffic-manager-manage-profiles.md).
