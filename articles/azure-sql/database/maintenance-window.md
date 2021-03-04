---
title: Časové období údržby
description: Seznamte se s tím, jak lze nakonfigurovat okno údržby Azure SQL Database a spravované instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/02/2021
ms.openlocfilehash: 9dc4d17ea95362dd915bd1dfdfd82f4cdec611b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692806"
---
# <a name="maintenance-window-preview"></a>Časové období údržby (Preview)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Funkce časového období údržby umožňuje konfiguraci předvídatelných plánů oken údržby pro [Azure SQL Database](sql-database-paas-overview.md) a [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md). 

Další informace o událostech údržby najdete v tématu [plánování událostí údržby Azure v Azure SQL Database a spravované instanci Azure SQL](planned-maintenance.md).

## <a name="overview"></a>Přehled

Azure provádí plánované aktualizace údržby pro prostředky spravované instance Azure SQL Database a SQL pravidelně, které často zahrnují aktualizace základního hardwaru, softwaru, včetně základního operačního systému (OS) a stroje SQL. Během aktualizace údržby jsou prostředky plně dostupné a přístupné, ale některé z aktualizací údržby vyžadují převzetí služeb při selhání, protože Azure při použití aktualizací údržby (osm sekund v průměru) převezme instance v režimu offline.  K plánovaným aktualizacím údržby dochází po každých 35 dní v průměru, což znamená, že zákazník může očekávat přibližně jednu plánovanou událost údržby za měsíc na Azure SQL Database nebo spravovanou instanci SQL a jenom v případě, že jsou sloty časového období údržby vybrané zákazníkem.   

Časový interval pro správu a údržbu je určený pro obchodní úlohy, které nejsou odolné proti problémům s přerušovaným připojením, které můžou vést k plánovaným událostem údržby.

Časové období údržby se dá nakonfigurovat pomocí Azure Portal, PowerShellu, rozhraní příkazového řádku nebo Azure API. Dá se nakonfigurovat při vytváření nebo pro existující databáze SQL a spravované instance SQL.

### <a name="gain-more-predictability-with-maintenance-window"></a>Získání větší předvídatelnosti s časovým obdobím údržby

Ve výchozím nastavení se všechny databáze SQL Azure a databáze spravované instance aktualizují jenom během 17:00 na místní čas, aby se předešlo výpadkům v pracovní době. Místní čas je určený [oblastí Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , která je hostitelem prostředku. Aktualizace údržby můžete dále upravit na čas, který je pro vaši databázi vhodný, a to výběrem ze dvou dalších slotů časového období údržby:

* **Výchozí** okno, 17:00 8:00 místní čas v pondělí – neděle 
* Okno pracovního dne, 10PM se na 6:00 místní čas pondělí – čtvrtek
* Víkendové okno, 10PM se na 6:00 místního času v pátek – neděle

Po provedení výběru časového období údržby budou všechny plánované aktualizace údržby provedeny pouze v okně podle vašeho výběru.   

> [!Note]
> Kromě plánovaných aktualizací údržby můžou ve výjimečných případech neplánované události údržby způsobit nedostupnost. 

### <a name="cost-and-eligibility"></a>Náklady a nárok

Výběr časového období údržby je zdarma pro následující [typy nabídek](https://azure.microsoft.com/support/legal/offer-details/)předplatného: průběžné platby, poskytovatel Cloud Solution Provider (CSP), Microsoft Enterprise nebo Microsoft Customer Agreement.

> [!Note]
> Nabídka Azure je typ předplatného Azure, které máte. Například předplatné s [tarify](https://azure.microsoft.com/offers/ms-azr-0003p/)průběžných plateb, [systém Azure v rámci licenčního programu Open](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)a [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) jsou všechny nabídky Azure. Každá nabídka nebo plán má různé výrazy a výhody. Vaše nabídka nebo plán se zobrazí v přehledu předplatného. Další informace o přepnutí předplatného na jinou nabídku najdete v tématu [Změna předplatného Azure na jinou nabídku](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Oznámení předem

Oznámení o údržbě je možné nakonfigurovat tak, aby zákazníkům upozornila na nadcházející plánované události údržby 24 hodin předem, v době údržby a po dokončení časového období údržby. Další informace najdete v tématu [oznámení o předem](advance-notifications.md).

## <a name="availability"></a>Dostupnost

### <a name="supported-service-level-objectives"></a>Podporované cíle na úrovni služby

Výběr časového období údržby, které je jiné než výchozí, je k dispozici na všech slo **s výjimkou**:
* Hyperškálování 
* Starší verze COMPUTE GEN4 – vCore
* Basic, S0 a S1 
* DC, Fsv2, M-Series

### <a name="azure-region-support"></a>Podpora oblasti Azure

Výběr jiných časových období údržby, než je výchozí, je aktuálně k dispozici v následujících oblastech:

- Austrálie – východ
- Austrálie – jihovýchod
- Brazílie – jih
- Střední Kanada
- Střední USA
- East US
- USA – východ 2
- Japonsko – východ
- NorthCentral nás
- Severní Evropa
- Střed nás
- Jihovýchodní Asie
- Spojené království – jih
- West Europe
- USA – západ
- USA – západ 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Údržba brány pro Azure SQL Database

Pokud chcete získat maximální výhody z časových období údržby, ujistěte se, že klientské aplikace používají zásady připojení přesměrování. Přesměrování je doporučená zásada připojení, kde klienti navázali připojení přímo k uzlu, který je hostitelem databáze, což vede ke snížení latence a lepší propustnosti.  

* V Azure SQL Database můžou být všechna připojení pomocí zásad připojení k proxy serveru ovlivněná zvoleným časovým obdobím údržby i oknem údržby uzlu brány. Připojení klientů pomocí doporučených zásad připojení přesměrování se ale neprojeví při převzetí služeb při selhání v rámci údržby uzlu brány. 

* Ve spravované instanci Azure SQL se uzly brány nacházejí [v rámci virtuálního clusteru](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) a mají stejné časové období údržby jako spravovaná instance, takže použití zásad připojení proxy nezveřejňuje připojení k dalšímu časovému intervalu pro správu a údržbu.

Další informace o zásadách připojení klienta v Azure SQL Database najdete v tématu [zásady připojení Azure SQL Database](../database/connectivity-architecture.md#connection-policy). 

Další informace o zásadách připojení klientů ve spravované instanci Azure SQL najdete v tématu [typy připojení spravované instance SQL Azure](../../azure-sql/managed-instance/connection-types-overview.md).


## <a name="next-steps"></a>Další kroky

* [Oznámení předem](advance-notifications.md)
* [Konfigurovat časový interval pro správu a údržbu](maintenance-window-configure.md)

## <a name="learn-more"></a>Další informace

* [Nejčastější dotazy k časovému období údržby](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [Spravovaná instance SQL](../managed-instance/sql-managed-instance-paas-overview.md)
* [Plánování událostí údržby Azure v Azure SQL Database a spravované instanci Azure SQL](planned-maintenance.md)




