---
title: Postupné upgrady aplikací – Azure SQL Database | Dokumentace Microsoftu
description: Další informace o použití geografické replikace databáze SQL Azure pro podporu online upgrady cloudových aplikací.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 748a9f4d7c2ec47a2ed9470789a4443bffdc0eba
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301736"
---
# <a name="managing-rolling-upgrades-of-cloud-applications-using-sql-database-active-geo-replication"></a>Správa postupné upgrady cloudových aplikací s využitím SQL Database aktivní geografické replikace

Další informace o použití [aktivní geografickou replikaci](sql-database-auto-failover-group.md) ve službě SQL Database povolit postupné upgrady cloudových aplikací. Protože upgrade je rušivé operace, měla by být součástí vaší obchodní kontinuity podnikových procesů plánování a návrhu. V tomto článku jsme podívejte se na dva různé způsoby Orchestrace procesu upgradu a popisují výhody a nevýhody jednotlivých možností. Pro účely tohoto článku budeme používat aplikaci, která se skládá z webového serveru připojený k izolované databáze jako jeho Datová vrstva. Naším cílem je upgrade verze 1 aplikace na verzi 2 bez významného dopadu na činnost koncového uživatele.

Při vyhodnocování možnosti upgradu, je třeba zvážit následující faktory:

* Dopad na dostupnost aplikace během upgradu. Jak dlouho funkce aplikace může omezené nebo snížený výkon.
* Možnost vrácení zpět, pokud upgrade selže.
* Ohrožení zabezpečení aplikace, pokud dojde k nesouvisejícím závažnému selhání během upgradu.
* Celkový počet dolar nákladů.  To zahrnuje další redundanci a dílčí náklady dočasné komponent používá proces upgradu.

## <a name="upgrading-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Upgradování aplikací, které jsou závislé na zálohování databází můžete pro zotavení po havárii

Pokud vaše aplikace využívá automatické zálohování databází a používá geografické obnovení pro zotavení po havárii, bude nasazena do jedné oblasti Azure. Pokud chcete přerušení minimalizovat, koncových uživatelů, vytvoříte testovací prostředí v této oblasti se všemi součástmi aplikací při upgradu. Následující diagram znázorňuje v provozním prostředí před upgradem. Koncový bod `contoso.azurewebsites.net` představuje produkční slot webové aplikace. Pokud chcete povolit možnost vrácení zpět upgrade, musí vytvořit přípravný slot s plně synchronizované kopie databáze. Následující postup vytvoří přípravné prostředí pro upgrade:

1. Vytvoření sekundární databáze ve stejné oblasti Azure. Monitorování sekundární jestli osazení procesu je dokončené (1).
2. Vytvořte nový slot nasazení pro vaši webovou aplikaci se nazývá "Pracovní". Budou zaregistrovány ve službě DNS s adresou URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Mějte na paměti, přípravné kroky nebude mít vliv na produkční slot a může fungovat v režimu plný přístup.
>  

![Konfigurace replikace se službou SQL Database Go. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Po dokončení kroků přípravy, aplikace je připravena pro skutečném upgradu. Následující diagram znázorňuje kroky v procesu upgradu.

1. Primární databázi nastavte do režimu jen pro čtení (3). Tento režim se zaručí, že produkční slot webové aplikace (V1) zůstane jen pro čtení během upgradu zamezuje tak odchylkami dat mezi instancemi databáze V1 a V2.  
2. Odpojte sekundární databázi pomocí plánované ukončení režimu (4). Vytvoří kopii plně synchronizovaná nezávislé primární databáze. Tato databáze se upgraduje.
3. Zapnout sekundární databáze do režimu pro čtení i zápis a spuštění skriptu aktualizace (5).

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Pokud se upgrade úspěšně dokončen, nyní jste připraveni přepnout koncoví uživatelé k upgradované kopie aplikace. Nyní bude produkčního slotu.  Přepnutí zahrnuje několik další kroky, jak je znázorněno na následujícím diagramu.

1. Aktivujte operaci prohození mezi produkční a přípravné sloty webové aplikace (6). Přepne adresy URL dvěma sloty. Nyní `contoso.azurewebsites.net` bude odkazovat na verzi V2 na webu a databáze (produkční prostředí).  
2. Pokud už nepotřebujete verze V1, kde byl program pracovní kopie po prohození, umožňuje vyřadit z provozu pracovní prostředí (7).

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Pokud neproběhne úspěšně procesu upgradu, například z důvodu chyby ve skriptu pro upgrade, slot pro fázi považovat za dojde k ohrožení bezpečnosti. K vrácení zpět před upgradem stavu aplikace můžete se vrátit aplikaci v produkčním slotu plný přístup. Potřebný postup se zobrazí na následující diagram.

1. Nastavte kopie databáze do režimu pro čtení i zápis (8). Obnoví úplné V1 funkčně kopii produkčního prostředí.
2. Proveďte analýzu původní příčiny a vyřadit z provozu pracovní prostředí (9).

V tomto okamžiku je plně funkční aplikace a jednotlivé kroky upgradu můžete opakovat.

> [!NOTE]
> Vrácení změn nevyžaduje změny DNS, jako jste dosud neprovedli operace prohození.

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Klíč **využívat** této možnosti je, že můžete upgradovat aplikace v jedné oblasti, používáte sadu jednoduchých krocích. Náklady na dolar upgradu jsou relativně nízký. Hlavní **kompromis** je, že pokud dojde k závažné chybě během upgradu bude zahrnovat obnovení do stavu před upgradem, opětovné nasazení aplikace v jiné oblasti a obnovení databáze ze zálohy pomocí geografické obnovení. Tento proces bude mít za následek významnějších výpadků.

## <a name="upgrading-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Upgrade aplikace, které využívají databáze geografickou replikaci pro zotavení po havárii

Pokud vaše aplikace využívá aktivní geografickou replikaci nebo skupin převzetí služeb při selhání zajišťuje nepřetržitý chod podniků, je nasazený na alespoň dvěma různými oblastmi s aktivní primární databáze v primární oblasti a sekundární databáze jen pro čtení v oblasti zálohování. Kromě faktory, které již bylo zmíněno dříve proces upgradu musí zaručit, že:

* Aplikace i nadále chráněn z katastrofických selhání po celou dobu, během procesu upgradu
* Geograficky redundantní komponenty aplikace jsou upgradovány zároveň s aktivní komponenty

K dosažení těchto cílů, kromě používání slotů nasazení webové aplikace, bude využívat pomocí profilu převzetí služeb při selhání s jedním aktivním a jeden záložní koncových bodů Azure Traffic Manager (ATM).  Následující diagram znázorňuje v provozním prostředí před upgradem. Na webech `contoso-1.azurewebsites.net` a `contoso-dr.azurewebsites.net` představují produkčním prostředí aplikace pomocí úplného geografickou redundanci. V provozním prostředí obsahuje následující součásti:

1. Produkční slot webové aplikace `contoso-1.azurewebsites.net` v primární oblasti (1)
2. Primární databázi v primární oblasti (2) 
3. Instance pohotovostní webové aplikace v oblasti zálohování (3)
4. Geograficky replikované sekundární databáze v oblasti zálohování (4)
5. Profil výkonu Azure traffic Manageru s koncovým bodem online `contoso-1.azurewebsites.net` a koncový bod offline `contoso-dr.azurewebsites.net`

Povolit možnost vrácení zpět upgrade, musí vytvořit testovací prostředí s plně synchronizované kopie aplikace. Protože je potřeba zajistit, že aplikace může rychle obnovit v případě, že během procesu upgradu dojde k závažné chybě, přípravné prostředí musí být také geograficky redundantní. Následující kroky je potřeba vytvořit testovací prostředí pro upgrade:

1. Nasazení přípravného slotu webové aplikace v primární oblasti (6)
2. Vytvořte sekundární databáze v primární oblasti Azure (7). Nakonfigurujte přípravný slot webové aplikace k němu připojit. 
3. Vytvořit jiné geograficky redundantní sekundární databáze v oblasti zálohování replikuje sekundární databáze v primární oblasti (tomu se říká "zřetězené geografickou replikaci") (8).
3. Nasazení přípravného slotu instance webové aplikace v oblasti zálohování (9) a nakonfigurujte jej pro připojení geo-secondary vytvořili v kroku (9).


> [!NOTE]
> Mějte na paměti, přípravné kroky nebude mít vliv na aplikaci v produkčním slotu a bude i nadále plně funkční i v režimu čtení i zápis.

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Po dokončení kroků přípravy, je pracovní prostředí připraveno k upgradu. Následující diagram znázorňuje jednotlivé kroky upgradu.

1. Nastavte primární databází v produkčním slotu do režimu jen pro čtení (10). Tento režim se zaručí, že je provozní databáze (V1) nezmění při upgradu zamezuje tak odchylkami dat mezi instancemi databáze V1 a V2.  
2. Odpojte sekundární databáze ve stejné oblasti pomocí plánované ukončení režimu (11). Vytvoří kopii databáze produkční nezávislé, ale plně synchronizovaná. Tato databáze se upgraduje.
3. Spuštění upgradu skriptu proti `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net` a pracovní primární databázi (12). Změny databáze budou automaticky replikovat do sekundárního pracovní 

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Pokud se upgrade úspěšně dokončen, nyní jste připraveni přepnout koncovým uživatelům na V2 verze aplikace. Následující diagram znázorňuje potřebnými kroky.

1. Aktivujte operaci prohození mezi produkční a přípravné sloty webové aplikace v primární oblasti (13) a v oblasti zálohování (14). V2 aplikace teď bude produkčního slotu redundantních kopií v oblasti zálohování.
2. Pokud už nepotřebujete aplikace V1 (15 a 16) umožňuje vyřadit z provozu pracovní prostředí.  

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Pokud neproběhne úspěšně procesu upgradu, například z důvodu chyby ve skriptu pro upgrade, přípravné prostředí považovat v nekonzistentním stavu. K vrácení zpět před upgradem stavu aplikace můžete vrátit zpět k používání V1 aplikace v produkčním prostředí. Požadované kroky jsou uvedeny v následující diagram.

1. V produkčním slotu do režimu pro čtení i zápis (17) nastavte kopie primární databáze. Obnoví úplné V1 funkčně v produkčním slotu.
2. Provádět analýzy hlavních příčin a opravit nebo odebrat pracovní prostředí (18 a 19).

V tomto okamžiku je plně funkční aplikace a jednotlivé kroky upgradu můžete opakovat.

> [!NOTE]
> Vrácení změn nevyžaduje změny DNS, protože jste neprovedli operace prohození.

![Konfigurace geografické replikace databáze SQL. Cloudové zotavení po havárii.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Klíč **využívat** této možnosti je, že můžete upgradovat aplikaci a její geograficky redundantní kopie paralelně bez negativního vlivu kontinuity během upgradu. Hlavní **kompromis** je, že vyžaduje dvojitá redundance jednotlivých součástí aplikace a proto má za následek vyšší náklady na dolar. Zahrnuje také složitější pracovního postupu.

## <a name="summary"></a>Souhrn

Dvě metody upgradu je popsáno v následujícím článku se liší v složitost a dolar nákladů ale oba soustředit na minimalizovat čas, kdy je omezená na jen pro čtení operace koncového uživatele. Tento čas je přímo určené dobu trvání upgradu skriptu. Není závislý na velikosti databáze, úroveň služby, kterou jste zvolili, konfiguraci webového serveru a dalších faktorů, které nelze snadno řídit. Přípravné kroky jsou oddělené od kroky upgradu a nemají vliv produkční aplikace. Efektivitu upgradovací skript je klíčovým faktorem, který určuje prostředí pro koncové uživatele během upgradu. Takže nejlepším způsobem ji můžete vylepšit je zaměření úsilí na to, jak co nejúčinnější upgradovací skript pro.  

## <a name="next-steps"></a>Další postup

* Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
* Další informace o Azure SQL Database aktivní geografickou replikaci, najdete v článku [vytvářet čitelné sekundární databáze pomocí aktivní geografické replikace](sql-database-active-geo-replication.md).
* Další informace o skupinách Azure SQL Database převzetí služeb při selhání najdete v tématu [pomocí skupin – automatické převzetí služeb při selhání můžete povolit transparentní a koordinovaný převzetí služeb při selhání několika databází](sql-database-auto-failover-group.md).
* Další informace o sloty nasazení a přípravné prostředí ve službě Azure App Service najdete v tématu [nastavení přípravných prostředí ve službě Azure App Service](../app-service/deploy-staging-slots.md).  
* Další profilů Azure traffic Manageru najdete v tématu [Správa profilu Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md).  


