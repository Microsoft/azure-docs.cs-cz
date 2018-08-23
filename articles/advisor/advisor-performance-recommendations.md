---
title: Doporučení Azure Advisoru výkonu | Dokumentace Microsoftu
description: Použijte Advisor za účelem optimalizace výkonu všech vašich nasazení Azure.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: makohli
ms.openlocfilehash: c2d7cc21b1546c9594a2ef174927777f583a6841
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42058322"
---
# <a name="advisor-performance-recommendations"></a>Poradce doporučení k výkonu

Doporučení Azure Advisoru výkonu zvýšit rychlost a rychlost odezvy důležitých aplikací. Doporučení k výkonu služby Advisor můžete získat **výkonu** karty řídicí panel služby Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>DNS doba TTL na svůj profil Traffic Manageru k převzetí služeb při selhání v dobrém stavu koncových bodů rychleji

[Čas nastavení Live (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) ve vašem profilu Traffic Manageru umožňuje určit, jak rychle přepnout koncové body, pokud daný koncový bod reagovat na dotazy. Snížení hodnoty TTL znamená, že klienti se budou směrovat do funkční koncových bodů rychlejší.

Azure Advisor určí profily Traffic Manageru s delší TTL nakonfigurovaný a doporučuje konfigurace hodnoty TTL na 20 sekund nebo 60 sekund, podle toho, jestli profil, který je nakonfigurovaný pro [rychlé převzetí služeb při selhání](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Zvyšte výkon databáze pomocí SQL DB Advisoru.

Advisor vám poskytuje konzistentní vzhledem k aplikacím, konsolidované zobrazení doporučení pro všechny vaše prostředky Azure. Integruje se službou SQL Database Advisor a přináší vám doporučení pro vylepšení výkonu vaší databáze SQL Azure. SQL Database Advisor vyhodnocuje výkon vašich databází SQL Azure díky analýze historii využití. Potom nabízí doporučení, která jsou nejvhodnější pro spuštění typické zatížení databáze. 

> [!NOTE]
> Pokud chcete získat doporučení, musí mít databázi o týden využití a v daném týdnu musí být některé aktivity konzistentní vzhledem k aplikacím. SQL Database Advisor můžete optimalizovat snadněji pro vzory dotazů konzistentní než pro náhodné nárůstům aktivity.

Další informace o službě SQL Database Advisor najdete v tématu [služby SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-redis-cache-performance-and-reliability"></a>Zlepšení výkonu služby Redis Cache a spolehlivosti

Advisor identifikuje instance služby Redis Cache kde výkon může negativně ovlivněn vysoké využití paměti, zatížení serveru, šířky pásma sítě nebo velký počet připojení klientů. Osvědčené postupy Advisor také poskytuje doporučení, abyste se vyhnuli potenciální problémy. Další informace o doporučení Redis Cache najdete v tématu [Redis Cache Advisoru](https://azure.microsoft.com/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Zlepšení výkonu služby App Service a spolehlivosti

Azure Advisor se integruje se doporučené postupy pro zlepšení prostředí služby App Services a zajištění příslušné možnosti. Příklady doporučení App Services:
* Zjišťování instancí, ve kterém vyčerpání paměti nebo prostředků procesoru podle moduly runtime aplikace s možnostmi omezení rizik.
* Zjišťování instancí, kde tyto zdroje, jako jsou webové aplikace a databáze můžete zvýšit výkon a nižší náklady. 

Další informace o doporučeních App Services najdete v tématu [osvědčené postupy pro službu Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Odeberte na tabulku SQL data warehouse ke zvýšení výkonu dotazů Nerovnoměrná distribuce dat.

Nerovnoměrná distribuce dat může způsobit nepotřebná data pohybu nebo prostředků problémová místa při spuštění vaší úlohy. Poradce zjistí data distribuce zkosení delší než 15 % a doporučujeme znovu distribuovat data a návštěvě klíče výběr distribuce tabulky. Další informace o identifikaci a odebírání zkosení najdete v tématu [řešení potíží s zkosení](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Vytvořit nebo aktualizovat statistiky zastaralé tabulky na tabulku SQL data warehouse ke zvýšení výkonu dotazů

Advisor identifikuje tabulky, které nemají aktuální [Statistika tabulky](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) a doporučuje se vytváří nebo aktualizuje statistika tabulky. Dotaz, že Optimalizátor používá aktuální statik k odhadu kardinality nebo počet řádků ve výsledku dotazu, který umožňuje optimalizátoru dotazů k vytvoření vysoce kvalitní plán dotazu pro nejrychlejší výkon služby SQL data warehouse.

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Jak získat přístup k doporučení k výkonu v Advisoru

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a pak otevřete [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na řídicím panelu služby Advisor, klikněte na tlačítko **výkonu** kartu.

## <a name="next-steps"></a>Další postup

Další informace o doporučení Advisoru, naleznete v tématu:

* [Úvod do služby Advisor](advisor-overview.md)
* [Začínáme se službou Advisor](advisor-get-started.md)
* [Náklady na doporučení advisoru](advisor-performance-recommendations.md)
* [Poradce doporučení k vysoké dostupnosti](advisor-high-availability-recommendations.md)
* [Bezpečnostní doporučení advisoru](advisor-security-recommendations.md)

