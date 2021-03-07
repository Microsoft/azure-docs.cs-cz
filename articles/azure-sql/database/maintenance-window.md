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
ms.date: 03/05/2021
ms.openlocfilehash: b658fa9f2df6e8a88df89f9e8ccc1cf6b68cec39
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426055"
---
# <a name="maintenance-window-preview"></a>Časové období údržby (Preview)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Funkce časový interval pro správu a údržbu umožňuje nakonfigurovat plán údržby pro [Azure SQL Database](sql-database-paas-overview.md) a prostředky [spravované instance Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) , které mají vliv na předvídatelné a méně rušivé události údržby pro vaše zatížení. 

> [!Note]
> Funkce časový interval pro správu a údržbu nechrání před neplánovanými událostmi, jako je selhání hardwaru, což může způsobit krátké přerušení připojení.

## <a name="overview"></a>Přehled

Azure pravidelně provádí [plánovanou údržbu](planned-maintenance.md) prostředků spravované instance SQL SQL Database a SQL. Během události údržby Azure SQL jsou databáze plně dostupné, ale můžou být v rámci příslušného SLA dostupnosti pro [SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database) a [SQL spravované instance](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance)podléhat krátkým převzetím služeb při selhání, protože v některých případech je potřeba rekonfigurace prostředků.

Časový interval pro správu a údržbu je určený pro produkční úlohy, které nejsou odolné vůči převzetí služeb při selhání databáze nebo instance a nemůžou absorbovat krátké přerušení připojení způsobené plánovanými událostmi údržby. Volbou okna pro správu, které dáváte přednost, můžete minimalizovat dopad plánované údržby, protože k němu dojde mimo špičku pracovní doby. Odolné úlohy a neprodukční úlohy se můžou spoléhat na výchozí zásady údržby Azure SQL.

Časové období údržby se dá nakonfigurovat při vytváření nebo pro stávající prostředky SQL Azure. Dá se nakonfigurovat pomocí rozhraní API Azure Portal, PowerShellu, CLI nebo Azure.

> [!Important]
> Konfigurace časového období údržby je dlouhodobě spuštěná asynchronní operace, podobně jako změna úrovně služby prostředku SQL Azure. Prostředek je k dispozici během operace s výjimkou krátkého převzetí služeb při selhání, které se na konci operace stane, a obvykle trvá až 8 sekund i v případě přerušených dlouhotrvajících transakcí. Abyste minimalizovali dopad převzetí služeb při selhání, měli byste tuto operaci provést mimo špičku hodin.

### <a name="gain-more-predictability-with-maintenance-window"></a>Získání větší předvídatelnosti s časovým obdobím údržby

Ve výchozím nastavení zablokují zásady údržby služby Azure SQL v průběhu období **, kdy se 8:00 místní čas na 17:00 místního času** , aby se předešlo jakémukoli výpadkům během typických pracovních hodin. Místní čas určuje umístění [oblasti Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , která je hostitelem prostředku, a může sledovat letní čas v souladu s definicí místního časového pásma. 

Aktualizace údržby můžete dále upravit na čas, který je vhodný pro vaše prostředky Azure SQL, a to tak, že vyberete ze dvou dalších slotů časových období údržby:
 
* Okno pracovního dne, 10PM se na 6:00 místního času v pondělí – čtvrtek
* Víkendové okno, 10PM se na 6:00 místního času v pátek – neděle

Po provedení výběru časového období údržby a dokončení konfigurace služby dojde k plánované údržbě pouze během zvoleného okna.   

> [!Important]
> Ve velmi vzácných situacích, kdy jakékoli odložení akce může způsobit vážný dopad, jako je použití kritické opravy zabezpečení, se může dočasně přepsat nakonfigurované časové období údržby. 

### <a name="cost-and-eligibility"></a>Náklady a nárok

Konfigurace a používání okna údržby je zdarma pro všechny opravňující [typy nabídek](https://azure.microsoft.com/support/legal/offer-details/): průběžné platby, zprostředkovatel Cloud Solution Provider (CSP), Microsoft smlouva Enterprise nebo smlouva Microsoft Customer Agreement.

> [!Note]
> Nabídka Azure je typ předplatného Azure, které máte. Například předplatné s [tarify](https://azure.microsoft.com/offers/ms-azr-0003p/)průběžných plateb, [systém Azure v rámci licenčního programu Open](https://azure.microsoft.com/offers/ms-azr-0111p/)a [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) jsou všechny nabídky Azure. Každá nabídka nebo plán má různé výrazy a výhody. Vaše nabídka nebo plán se zobrazí v přehledu předplatného. Další informace o přepnutí předplatného na jinou nabídku najdete v tématu [Změna předplatného Azure na jinou nabídku](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Oznámení předem

Oznámení o údržbě je možné nakonfigurovat tak, aby vás upozornila na nadcházející plánované události údržby pro Azure SQL Database 24 hodin předem v době údržby a po dokončení údržby. Další informace najdete v tématu [oznámení o předem](advance-notifications.md).

## <a name="availability"></a>Dostupnost

### <a name="supported-service-level-objectives"></a>Podporované cíle na úrovni služby

Výběr časového období údržby, které je jiné než výchozí, je k dispozici na všech slo **s výjimkou**:
* Hyperškálování 
* Fondy instancí
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

* Ve spravované instanci Azure SQL se uzly brány hostují [ve virtuálním clusteru](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) a mají stejné časové období údržby jako spravovaná instance, ale při použití zásady přesměrování připojení se pořád doporučuje minimalizovat počet přerušení během události údržby.

Další informace o zásadách připojení klienta v Azure SQL Database najdete v tématu [zásady připojení Azure SQL Database](../database/connectivity-architecture.md#connection-policy). 

Další informace o zásadách připojení klientů ve spravované instanci Azure SQL najdete v tématu [typy připojení spravované instance SQL Azure](../../azure-sql/managed-instance/connection-types-overview.md).

## <a name="considerations-for-azure-sql-managed-instance"></a>Předpoklady pro spravovanou instanci Azure SQL

Spravovaná instance Azure SQL se skládá z komponent služby hostovaných na vyhrazené sadě izolovaných virtuálních počítačů, které běží v podsíti virtuální sítě zákazníka. Tyto virtuální počítače tvoří [virtuální clustery](/azure/azure-sql/managed-instance/connectivity-architecture-overview#high-level-connectivity-architecture) , které mohou hostovat více spravovaných instancí. Časové období údržby nakonfigurované na instancích jedné podsítě může mít vliv na počet virtuálních clusterů v rámci podsítě a na distribuci instancí mezi virtuálními clustery. To může vyžadovat zvážení několika efektů.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>Konfigurace časového období údržby je dlouhodobá operace. 
Všechny instance hostované ve virtuálním clusteru sdílejí časový interval pro správu a údržbu. Ve výchozím nastavení se všechny spravované instance hostují ve virtuálním clusteru s výchozím časovým obdobím údržby. Zadání dalšího časového období údržby pro spravovanou instanci během jeho vytvoření nebo později znamená, že musí být umístěn ve virtuálním clusteru s odpovídajícím časovým obdobím údržby. Pokud v podsíti žádný takový virtuální cluster neexistuje, je třeba nejprve vytvořit nové, aby se pokryla instance. Další instance ve stávajícím virtuálním clusteru může vyžadovat změnu velikosti clusteru. Obě operace přispívají k době trvání konfigurace časového období údržby pro spravovanou instanci.
Očekávaná doba trvání konfigurace časového období údržby na spravované instanci se dá vypočítat pomocí [Odhadované doby trvání operací správy instancí](/azure/azure-sql/managed-instance/management-operations-overview#duration).

> [!Important]
> Krátká operace převzetí služeb při selhání probíhá na konci operace údržby a obvykle trvá až 8 sekund i v případě přerušených dlouhotrvajících transakcí. Abyste minimalizovali dopad převzetí služeb při selhání, měli byste naplánovat operaci mimo špičku v době špičky.

### <a name="ip-address-space-requirements"></a>Požadavky na adresní prostor IP adres
Každý nový virtuální cluster v podsíti vyžaduje další IP adresy v závislosti na [přidělování IP adres virtuálních clusterů](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#determine-subnet-size). Změna časového období údržby pro existující spravovanou instanci také vyžaduje [dočasnou další kapacitu IP adres](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#address-requirements-for-update-scenarios) jako v rámci škálování scénáře virtuální jádra pro odpovídající úroveň služby.

### <a name="ip-address-change"></a>Změna IP adresy
Konfigurace a změna okna údržby způsobuje změnu IP adresy instance v rámci rozsahu IP adres podsítě.

> [!Important]
>  Ujistěte se, že pravidla NSG a firewallu neblokují přenos dat po změně IP adresy. 

## <a name="next-steps"></a>Další kroky

* [Oznámení předem](advance-notifications.md)
* [Konfigurovat časový interval pro správu a údržbu](maintenance-window-configure.md)

## <a name="learn-more"></a>Další informace

* [Nejčastější dotazy k časovému období údržby](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [Spravovaná instance SQL](../managed-instance/sql-managed-instance-paas-overview.md)
* [Plánování událostí údržby Azure v Azure SQL Database a spravované instanci Azure SQL](planned-maintenance.md)





