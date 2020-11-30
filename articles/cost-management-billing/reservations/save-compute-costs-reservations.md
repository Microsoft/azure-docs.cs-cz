---
title: Co jsou rezervace Azure?
description: Informace o rezervacích Azure a úsporách nákladů na rezervované instance pro virtuální počítače, databáze SQL, službu Azure Cosmos DB a další prostředky
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: overview
ms.date: 11/18/2020
ms.author: banders
ms.openlocfilehash: eef19a6c421a64c3951d35eea78ea84f7e7d97b2
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887208"
---
# <a name="what-are-azure-reservations"></a>Co jsou rezervace Azure?

Rezervace Azure umožňují šetřit peníze tím, že potvrzují závazek využívání několika produktů v rámci plánů na jeden nebo tři roky. Díky potvrzení závazku můžete získat slevu na prostředky, které využíváte. Rezervace můžou výrazně snížit náklady na prostředky, a to až o 72 % oproti průběžným platbám. Rezervace poskytují slevu z faktury a neovlivňují běhový stav prostředků. Po zakoupení rezervace se sleva automaticky uplatní na vyhovující prostředek.

Za rezervaci můžete zaplatit předem nebo prostřednictvím měsíčních plateb. Celkové náklady na rezervaci placenou předem a rezervaci s měsíčními platbami jsou stejné. Pokud tedy zvolíte měsíční platby, neplatíte žádné další poplatky. Měsíční platba je k dispozici pro rezervace Azure, ne pro produkty třetích stran.

Rezervaci můžete zakoupit na webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Proč zakoupit rezervaci?

Pokud máte konzistentní využití prostředků, které podporuje rezervace, nákup rezervace vám dává možnost snížit vaše náklady. Pokud například nepřetržitě provozujete instance služby bez rezervace, účtují se vám sazby pro průběžné platby. Když si koupíte rezervaci, okamžitě získáte slevu za rezervaci. Za tyto prostředky se už nebudou účtovat sazby pro průběžné platby.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Po zakoupení se sleva za rezervaci automaticky uplatňuje na využití prostředků odpovídající atributům, které vyberete při zakoupení rezervace. Mezi atributy patří SKU, oblasti (kde je to možné) a rozsah. Rozsah rezervace určuje, kde se úspory získané rezervací uplatní.

Další informace o uplatnění slevy najdete v tématu věnovaném [využití slevy za rezervované instance](reservation-discount-application.md).

Další informace o tom, jak funguje rozsah rezervacíe, najdete v tématu [Vymezení rozsahů rezervací](prepare-buy-reservation.md#scope-reservations).

## <a name="determine-what-to-purchase"></a>Určení, co koupit 

Všechny rezervace s výjimkou Azure Databricks se využívají na hodinovém základu. Při nákupu rezervací vezměte v úvahu vaše konzistentní základní využití. S určením, jakou rezervaci byste měli koupit, vám může pomoct analýza vašich dat o využití nebo doporučení pro rezervace. Doporučení jsou dostupná na těchto místech:

- Azure Advisor (pouze pro virtuální počítače)
- Prostředí pro nákup rezervací na webu Azure Portal
- Aplikace Power BI Cost Management
- Rozhraní API 

Další informace najdete v tématu  [Určení rezervace k zakoupení](determine-reservation-purchase.md). 

## <a name="buying-a-reservation"></a>Nákup rezervace 

Rezervace si můžete koupit prostřednictvím webu Azure Portal, rozhraní API, PowerShellu nebo rozhraní příkazového řádku. 

Pokud chcete provést nákup, přejděte na [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs).

Další informace najdete v tématu  [Nákup rezervace](prepare-buy-reservation.md).

## <a name="how-is-a-reservation-billed"></a>Jak se rezervace fakturuje? 

Rezervace se fakturuje za použití způsobu platby spojeného s předplatným. Náklady na rezervaci se odečtou ze zůstatku peněžního závazku, pokud je k dispozici. Pokud zůstatek peněžního závazku nepokryje náklady na rezervaci, bude se vám účtovat nadlimitní využití. Pokud máte předplatné z individuálního plánu s průběžnými platbami, z platební karty přidružené k vašemu účtu se ihned strhne částka za nákup placený předem. Měsíční platby se objeví na vaší faktuře a částka se bude každý měsíc strhávat z vaší platební karty. Při fakturaci na základě faktury uvidíte poplatky na vaší další faktuře. 

## <a name="permissions-to-view-and-manage-reservations"></a>Oprávnění k zobrazení a správě rezervací 

Uživatel, který rezervaci zakoupí, a správce účtu předplatného sloužícího k fakturaci rezervace získají roli vlastníka objednávky rezervace a samotné rezervace.

Správu rezervace můžete delegovat přidáním lidí do rolí rezervace nebo objednávky rezervace. Role se přiřazují na webu Azure Portal nebo pomocí rozhraní API a PowerShellu. 

Další informace najdete v tématu  [Přidání nebo změna uživatelů, kteří můžou spravovat rezervaci](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation). 

## <a name="get-reservation-details-and-utilization-after-purchase"></a>Získání podrobností o rezervaci a informací o využití po nákupu

Pokud máte oprávnění k zobrazení rezervace, můžete ji a její využití zobrazit na webu Azure Portal. Tato data můžete získat také pomocí rozhraní API. 

Další informace o zobrazení rezervací na webu Azure Portal najdete v tématu  [Zobrazení rezervací na webu Azure Portal](view-reservations.md). 

## <a name="manage-reservations-after-purchase"></a>Správa rezervací po nákupu 

Po nákupu rezervace Azure můžete aktualizovat její rozsah, abyste ji mohli použít pro jiné předplatné, změnit osobu, která ji může spravovat, rozdělit ji na menší části nebo změnit její flexibilní velikost. 

Další informace najdete v tématu  [Správa rezervací prostředků Azure](manage-reserved-vm-instance.md). 

## <a name="flexibility-with-azure-reservations"></a>Flexibilita s využitím rezervací Azure

Rezervace Azure vám dávají flexibilitu pro zvládání stále se vyvíjejících potřeb. Rezervaci si můžete vyměnit za jinou rezervaci stejného typu. Za rezervaci si také můžete nechat vrátit peníze, pokud už ji nepotřebujete, a to až do 50 000 USD za průběžné období 12 měsíců. Maximální limit vrácení peněz se vztahuje na všechny rezervace v rozsahu vaší smlouvy s Microsoftem.

Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](exchange-and-refund-azure-reservations.md).


## <a name="charges-covered-by-reservation"></a>Poplatky, na které se rezervace vztahuje

- **Rezervovaná instance virtuálního počítače** – Rezervace se vztahuje jenom na náklady na výpočetní funkce virtuálních počítačů a cloudových služeb. Nevztahuje se na další poplatky za software, Windows, sítě nebo úložiště.
- **Rezervovaná kapacita služby Azure Storage** – Rezervace pokrývá kapacitu úložiště pro účty úložiště úrovně Standard ve službách Blob Storage nebo Azure Data Lake Storage Gen2. Rezervace se nevztahuje na šířku pásma ani rychlost transakcí.
- **Rezervovaná kapacita služby Azure Cosmos DB** – Rezervace se vztahuje na propustnost zřízenou pro vaše prostředky. Nevztahuje se na poplatky za úložiště a sítě.
- **Rezervované virtuální jádro služby SQL Database** – Rezervace se vztahuje na SQL Managed Instance i SQL Database ve variantě Elastický fond / Jednoúčelová databáze. Součástí rezervace jsou jenom náklady na výpočetní funkce. Licence SQL se účtuje samostatně. 
- **Azure Synapse Analytics** – Rezervace se vztahuje na využití cDWU. Nevztahuje se na poplatky za úložiště nebo sítě související s využitím služby Azure Synapse Analytics.
- **Azure Databricks** – Rezervace se vztahuje pouze na využití DBU. Další poplatky, například za výpočetní prostředky, úložiště a sítě, se účtují zvlášť.
- **Poplatek za kolek služby App Service** – Rezervace se vztahuje na využití kolku. Nevztahuje se na pracovní procesy, takže všechny další prostředky přidružené ke kolku se účtují samostatně.
- **Azure Database for MySQL** – Rezervace se vztahuje pouze na náklady na výpočetní prostředky. Rezervace se nevztahuje na poplatky za software, sítě ani úložiště související s databázovým serverem MySQL.
- **Azure Database for PostgreSQL** – Rezervace se vztahuje pouze na náklady na výpočetní prostředky. Rezervace se nevztahuje na poplatky za software, sítě ani úložiště související s databázovými servery PostgreSQL.
- **Azure Database for MariaDB** – Rezervace se vztahuje pouze na náklady na výpočetní prostředky. Rezervace se nevztahuje na poplatky za software, sítě ani úložiště související s databázovým serverem MariaDB.
- **Azure Data Explorer** – Rezervace se vztahuje na přirážku. Rezervace se nevztahuje na poplatky za výpočetní prostředky, sítě ani úložiště související s clustery.
- **Azure Cache for Redis** – Rezervace se vztahuje pouze na náklady na výpočetní prostředky. Rezervace se nevztahuje na poplatky za sítě ani úložiště související s instancemi mezipaměti Redis.
- **Azure Dedicated Host** – Součástí vyhrazeného hostitele jsou jenom náklady na výpočetní funkce.
- **Rezervace služby Azure Disk Storage** – Rezervace pokrývá jenom disky SSD P30 Premium nebo větší. Nevztahuje se na žádné jiné typy disků ani velikosti menší než P30.

Softwarové plány:

- **SUSE Linux** – Rezervace se vztahuje na náklady na plán softwaru. Slevy se vztahují pouze na měřiče SUSE, a ne na využití virtuálního počítače.
- **Plány Red Hat** – Rezervace se vztahuje na náklady na plán softwaru. Slevy se vztahují pouze na měřiče Red Hat, a ne na využití virtuálního počítače.
- **Azure VMware Solution by CloudSimple** – Rezervace se vztahuje na uzly VMware CloudSimple. Stále se však účtují další náklady na software.
- **Azure Red Hat OpenShift** – Rezervace se vztahuje na náklady na OpenShift, ne na infrastrukturu Azure.

U virtuálních počítačů s Windows a SQL Database se sleva za rezervaci nevztahuje na náklady na software. Licenční náklady můžete pokrýt pomocí programu [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích Azure najdete v následujících článcích:
    - [Správa rezervací Azure](manage-reserved-vm-instance.md)
    - [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
    - [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
    - [Náklady na software pro Windows nezahrnuté v rezervacích](reserved-instance-windows-software-costs.md)
    - [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

- Další informace o rezervacích pro plány služby:
    - [Virtuální počítače se službou Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Prostředky Azure Cosmos DB s rezervovanou kapacitou služby Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Výpočetní prostředky SQL Database s rezervovanou kapacitou služby Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
    - [Prostředky Azure Cache for Redis s rezervovanou kapacitou služby Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md) Další informace o rezervacích pro softwarové plány:
    - [Plány softwaru Red Hat z rezervací Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
    - [Plány softwaru SUSE z rezervací Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)