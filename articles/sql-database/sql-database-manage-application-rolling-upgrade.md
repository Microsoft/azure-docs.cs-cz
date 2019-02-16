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
ms.openlocfilehash: ad971ae3157dd17ecd4af662626c986584a27fe2
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329162"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Správa postupné upgrady cloudových aplikací s využitím SQL Database aktivní geografické replikace

Další informace o použití [aktivní geografickou replikaci](sql-database-auto-failover-group.md) ve službě Azure SQL Database povolit postupné upgrady cloudových aplikací. Vzhledem k tomu upgrady rušivé operace, měly by být součástí vaší obchodní kontinuity plánování a návrhu. V tomto článku se můžeme podívat na dva různé způsoby Orchestrace procesu upgradu a popisují výhody a nevýhody jednotlivých možností. Pro účely tohoto článku se označují aplikace, která se skládá z webu, který je připojen k izolované databáze jako jeho Datová vrstva. Naším cílem je upgrade verze 1 (V1) aplikace a verze 2 (V2) bez významného dopadu na činnost koncového uživatele.

Při vyhodnocování možnosti upgradu, vezměte v úvahu tyto faktory:

* Dopad na dostupnost aplikace během upgradu, třeba jak dlouho může být funkce aplikací omezené nebo snížený výkon.
* Možnost vrácení zpět, pokud upgrade selže.
* Ohrožení zabezpečení aplikací, případě nesouvisející, katastrofických selhání během upgradu.
* Celkový počet dolar nákladů. To zahrnuje redundance další databáze a přírůstkové náklady na dočasné komponent používá proces upgradu.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Upgrade aplikací, které jsou závislé na zálohování databází můžete pro zotavení po havárii

Pokud vaše aplikace využívá automatické zálohování databází a používá geografické obnovení pro zotavení po havárii, bude nasazena do jedné oblasti Azure. Aby se minimalizovalo přerušení uživatele, vytvořte testovací prostředí v této oblasti se všemi součástmi aplikací při upgradu. První diagram znázorňuje v provozním prostředí před upgradem. Koncový bod `contoso.azurewebsites.net` představuje produkčním prostředí webové aplikace. Aby bylo možné vrátit zpět, musíte vytvořit testovací prostředí s plně synchronizované kopie databáze. Postupujte podle těchto kroků můžete vytvořit testovací prostředí pro upgrade:

1. Vytvoření sekundární databáze ve stejné oblasti Azure. Monitorování sekundární jestli osazení procesu je kompletní (1).
2. Vytvořit nové prostředí pro vaši webovou aplikaci a říkat "Pracovní". Budou zaregistrovány ve službě Azure DNS s adresou URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Postup přípravy nebude mít vliv na produkční prostředí, které můžou fungovat v režimu úplný přístup.

![Konfigurace geografické replikace SQL Database pro zotavení po havárii cloudu.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Po dokončení postupu přípravy aplikace je připravena pro skutečném upgradu. Následující diagram znázorňuje kroky v procesu upgradu:

1. Primární databázi nastavte do režimu jen pro čtení (3). Tento režim zaručuje, že produkční prostředí, které webové aplikace (V1) zůstane jen pro čtení během upgradu zamezuje tak odchylkami dat mezi instancemi databáze V1 a V2.
2. Odpojte sekundární databázi pomocí plánované ukončení režimu (4). Tato akce vytvoří plně synchronizovaná nezávislé kopie primární databáze. Tato databáze se upgraduje.
3. Zapnout sekundární databáze do režimu pro čtení i zápis a spuštění skriptu aktualizace (5).

![Konfigurace geografické replikace SQL Database pro zotavení po havárii cloudu.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Pokud se upgrade dokončí úspěšně, jste připraveni teď přepněte uživatele k upgradované kopie aplikace, která se stane produkčním prostředí. Přepínání zahrnuje několik kroků, jak je znázorněno v následující diagram:

1. Aktivujte operaci prohození mezi produkční a pracovní prostředí webové aplikace (6). Tato operace se přepne adresy URL dvě prostředí. Nyní `contoso.azurewebsites.net` odkazuje na verzi V2 na webu a databáze (produkční prostředí). 
2. Pokud už nepotřebujete verze V1, kde byl program pracovní kopie po prohození, umožňuje vyřadit z provozu pracovní prostředí (7).

![Konfigurace geografické replikace SQL Database pro zotavení po havárii cloudu.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Pokud proces upgradu neproběhne úspěšně (například z důvodu chyby ve skriptu pro upgrade), vezměte v úvahu přípravné prostředí, které u něho ohrožena bezpečnost. Chcete-li vrátit zpět před upgradem stavu aplikace, vrátit aplikaci v produkčním prostředí na úplný přístup. Následující diagram znázorňuje postup obnovení:

1. Nastavte kopie databáze do režimu pro čtení i zápis (8). Tato akce obnoví všechny funkce V1 kopii produkčního prostředí.
2. Provádět analýzy původních příčin a vyřadit z provozu pracovní prostředí (9).

V tomto okamžiku je plně funkční aplikace a zopakujete kroky upgradu.

> [!NOTE]
> Vrácení změn nevyžaduje změny DNS, protože jste dosud neprovedli operace prohození.

![Konfigurace geografické replikace SQL Database pro zotavení po havárii cloudu.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Hlavní výhodou této možnosti je, že aplikace v jedné oblasti můžete upgradovat pomocí následujícího sadu jednoduchých krocích. Náklady na dolar upgradu jsou relativně nízký. 

Hlavní kompromis je, že pokud při upgradu dojde k závažné chybě, obnovení do stavu před upgradem zahrnuje opětovného nasazení aplikace v jiné oblasti a obnovení databáze ze zálohy s využitím geografického obnovení. Výsledkem tohoto procesu významnějších výpadků.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Upgrade aplikací, které závisí na databázi geografickou replikaci pro zotavení po havárii

Pokud vaše aplikace používá aktivní geografickou replikaci nebo skupiny automatické převzetí služeb při selhání zajišťuje nepřetržitý chod podniků, je nasazený na alespoň dvěma různými oblastmi. Je databáze aktivní, primární v primární oblasti a jen pro čtení, sekundární databáze v oblasti zálohování. Spolu s faktory, které se už zmínili na začátku tohoto článku proces upgradu musí také zaručit, že:

* Aplikace i nadále chráněn z katastrofických selhání po celou dobu, během procesu upgradu.
* Geograficky redundantní komponenty aplikace jsou upgradovány zároveň s aktivní komponenty.

K dosažení těchto cílů, kromě použití prostředí webové aplikace, budete využíváte Azure Traffic Manageru s použitím profilu převzetí služeb při selhání s jeden aktivní koncový bod a jednoho záložního koncového bodu. Následující diagram znázorňuje v provozním prostředí před upgradem. Na webech `contoso-1.azurewebsites.net` a `contoso-dr.azurewebsites.net` představují produkčním prostředí aplikace pomocí úplného geografickou redundanci. V provozním prostředí obsahuje následující součásti:

* Produkčním prostředí webové aplikace `contoso-1.azurewebsites.net` v primární oblasti (1)
* Primární databázi v primární oblasti (2)
* Pohotovostní instance webové aplikace v oblasti zálohování (3)
* Geograficky replikované sekundární databáze v oblasti zálohování (4)
* Volá se profil služby Traffic Manager výkonu pomocí online koncového bodu `contoso-1.azurewebsites.net` offline koncový bod s názvem `contoso-dr.azurewebsites.net`

Aby bylo možné vrátit zpět, musíte vytvořit testovací prostředí s plně synchronizované kopie aplikace. Protože je potřeba zajistit, že aplikace může rychle obnovit v případě, že během procesu upgradu dojde k závažné chybě, přípravné prostředí musí být geograficky redundantní také. Následující kroky je potřeba vytvořit testovací prostředí pro upgrade:

1. Nasazení přípravného prostředí webové aplikace v primární oblasti (6).
2. Vytvořte sekundární databáze v primární oblasti Azure (7). Nakonfigurujte pracovní prostředí webové aplikace k němu připojit. 
3. Vytvořte jiné geograficky redundantní, sekundární databáze v oblasti zálohování replikuje sekundární databáze v primární oblasti. (Tato metoda je volána *zřetězené geografickou replikaci*.) (8).
4. Nasazení přípravného prostředí instance webové aplikace v oblasti zálohování (9) a nakonfigurujte jej pro připojení geograficky redundantní sekundární databáze vytvořené na (8).

> [!NOTE]
> Postup přípravy nebude mít vliv na aplikaci v produkčním prostředí. Bude se dál plně funkční i v režimu čtení i zápis.

![Konfigurace geografické replikace SQL Database pro zotavení po havárii cloudu.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Po dokončení postupu přípravy je pracovní prostředí připraveno k upgradu. Následující diagram znázorňuje tyto kroky upgradu:

1. Nastavte primární databází v produkčním prostředí do režimu jen pro čtení (10). Tento režim zaručuje, že je provozní databáze (V1) nezmění při upgradu, zamezuje tak odchylkami dat mezi instancemi databáze V1 a V2.
2. Odpojte sekundární databázi ve stejné oblasti pomocí plánované ukončení režimu (11). Tato akce vytvoří nezávislé, ale plně synchronizované kopie produkční databázi. Tato databáze se upgraduje.
3. Spuštění upgradu skriptu proti `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net`a pracovní primární databázi (12). Změny databáze se automaticky replikovat do sekundárního přípravy.

![Konfigurace geografické replikace SQL Database pro zotavení po havárii cloudu.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Pokud se upgrade dokončí úspěšně, jste připraveni teď přepněte uživatele na V2 verze aplikace. Následující diagram znázorňuje kroky:

1. Aktivujte operaci prohození mezi produkční a pracovní prostředí webové aplikace v primární oblasti (13) a v oblasti zálohování (14). V2 aplikace teď bude produkčním prostředí, kopii redundantní v oblasti zálohování.
2. Pokud už nepotřebujete aplikace V1 (15 a 16), umožňuje vyřadit z provozu pracovní prostředí.

![Konfigurace geografické replikace SQL Database pro zotavení po havárii cloudu.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Pokud proces upgradu neproběhne úspěšně (například z důvodu chyby ve skriptu pro upgrade), vezměte v úvahu testovací prostředí v nekonzistentním stavu. Chcete-li vrátit zpět před upgradem stavu aplikace vrátí k použití V1 aplikace v produkčním prostředí. Požadované kroky jsou uvedeny v následující diagram:

1. Nastavte primární databáze kopírování v provozním prostředí do režimu pro čtení i zápis (17). Tato akce obnoví všechny funkce verze 1 v provozním prostředí.
2. Provést analýzu hlavní příčiny a opravit nebo odebrat pracovní prostředí (18 a 19).

V tomto okamžiku je plně funkční aplikace a zopakujete kroky upgradu.

> [!NOTE]
> Vrácení změn nevyžaduje změny DNS, protože se nepovedlo provést operaci přepnutí.

![Konfigurace geografické replikace SQL Database pro zotavení po havárii cloudu.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Hlavní výhodou této možnosti je, že můžete upgradovat aplikaci a její geograficky redundantní kopie paralelně bez negativního vlivu kontinuity během upgradu.

Hlavní kompromis je, že vyžaduje dvojitá redundance jednotlivých součástí aplikace a proto má za následek vyšší náklady na dolar. Zahrnuje také složitější pracovního postupu.

## <a name="summary"></a>Souhrn

Složitost a náklady na dolar se liší dvě upgradu metod popsaných v článku, ale oba zaměřit na minimalizovat, jak dlouho uživateli je omezená na operacím jen pro čtení. Tento čas je přímo určené dobu trvání upgradu skriptu. Nezávisí na velikost databáze, úroveň služby, kterou jste zvolili, konfigurace webové stránky nebo jinými faktory, které nelze snadno řídit. Všechny přípravné kroky jsou oddělené od kroky upgradu a nebudou mít vliv na produkční aplikace. Efektivitu upgradovací skript je klíčovým faktorem, který určuje uživatelské prostředí během upgradu. Ano nejlepší způsob, jak vylepšovat se Zaměřte své úsilí na to, jak co nejúčinnější upgradu skriptu.

## <a name="next-steps"></a>Další postup

* Přehled zajištění provozní kontinuity podnikání a scénáře, naleznete v tématu [přehled zajištění provozní kontinuity firmy](sql-database-business-continuity.md).
* Další informace o Azure SQL Database aktivní geografickou replikaci, najdete v článku [vytvářet čitelné sekundární databáze pomocí aktivní geografické replikace](sql-database-active-geo-replication.md).
* Další informace o skupinách Azure SQL Database – automatické převzetí služeb při selhání najdete v tématu [pomocí skupin – automatické převzetí služeb při selhání můžete povolit transparentní a koordinovaný převzetí služeb při selhání několika databází](sql-database-auto-failover-group.md).
* Další informace o přípravných prostředí ve službě Azure App Service najdete v tématu [nastavení přípravných prostředí ve službě Azure App Service](../app-service/deploy-staging-slots.md).
* Další informace o profilů Azure Traffic Manageru, najdete v článku [Správa profilu Azure Traffic Manager](../traffic-manager/traffic-manager-manage-profiles.md).
