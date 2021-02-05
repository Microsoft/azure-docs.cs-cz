---
title: Způsob uplatňování slevy za rezervaci Azure
description: Tento článek vám pomůže pochopit, jak se obecně využívají slevy za rezervované instance.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1dec8253d785973aa900434c10f85ec0a63e0599
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574868"
---
# <a name="how-a-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Tento článek vám pomůže pochopit, jak se obecně využívají slevy za rezervované instance. Sleva za rezervaci se uplatňuje na využití prostředků odpovídající atributům, které vyberete při zakoupení rezervace. Mezi atributy patří rozsah, ve kterém se odpovídající virtuální počítače, databáze SQL, služba Azure Cosmos DB nebo jiné prostředky používají. Pokud například chcete slevu za rezervaci čtyř virtuálních počítačů Standard D2 v oblasti USA – západ, vyberte předplatné, ve kterém se tyto virtuální počítače používají.

Nevyužitá sleva za rezervaci se *nenahrazuje*. Pokud nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny nelze převést na další období.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

Můžete například později vytvořit prostředek a mít odpovídající rezervaci, která je nevyužitá. Sleva za rezervaci se automaticky uplatní na nový vyhovující prostředek.

Pokud se virtuální počítače používají v různých předplatných v rámci prováděcí smlouvy nebo účtu, nastavte rozsah jako sdílený. Sdílený rozsah umožňuje uplatnění slevy za rezervaci napříč předplatnými. Po zakoupení rezervace můžete rozsah změnit. Další informace najdete v tématu [Správa rezervací Azure](manage-reserved-vm-instance.md).

Sleva za rezervaci se vztahuje jenom na prostředky přidružené k předplatným Enterprise, CSP, předplatným se smlouvou se zákazníkem Microsoftu nebo předplatným s průběžnými platbami. Prostředky, které se spouští v rámci předplatného s jinými typy nabídek, neobdrží slevu za rezervace.

## <a name="when-the-reservation-term-expires"></a>Po skončení období rezervace

Na konci rezervačního období vyprší platnost fakturační slevy a prostředky se účtují podle sazeb za průběžné platby. Ve výchozím nastavení není pro rezervace nastavené automatické obnovování. Automatické obnovení rezervace můžete povolit výběrem příslušné možnosti v nastaveních prodloužení. Pokud je nastavené automatické prodloužení, při vypršení platnosti stávající rezervace se koupí náhradní. Náhradní rezervace má ve výchozím nastavení stejné atributy jako rezervace s vypršenou platností. Volitelně můžete v nastaveních prodloužení změnit četnost fakturace, období nebo množství. Prodloužení může nastavit libovolný uživatel, který má přístup vlastníka k dané rezervaci a k předplatnému použitému pro fakturaci.  

## <a name="discount-applies-to-different-sizes"></a>Sleva se vztahuje na různé velikosti

Když zakoupíte rezervaci, může se sleva uplatnit na jiné instance s atributy, které se nacházejí ve stejné skupině velikostí. Tato funkce se označuje jako flexibilní velikost instance. Flexibilita pokrytí slevou závisí na typu rezervace a atributech vybraných při zakoupení rezervace.

Plány služby:

- Rezervované instance virtuálních počítačů: Pokud zakoupíte rezervaci a vyberete možnost **optimalizace pro flexibilní velikost instance**, bude pokrytí slevou záviset na tom, jakou velikost virtuálního počítače zvolíte. Rezervace se může vztahovat na velikosti virtuálních počítačů ve stejné skupině velikostí. Další informace najdete v tématu [Flexibilita velikosti virtuálních počítačů s rezervovanými instancemi virtuálních počítačů](../../virtual-machines/reserved-vm-instance-size-flexibility.md).
- Rezervovaná kapacita služby Azure Storage: Rezervovanou kapacitu pro účty služby Azure Storage úrovně Standard si můžete zakoupit v jednotkách 100 TiB nebo 1 PiB za měsíc. Informace o tom, které oblasti podporují rezervovanou kapacitu služby Azure Storage, najdete v tématu věnovaném [cenám objektů blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/). Rezervovaná kapacita služby Azure Storage je dostupná pro všechny úrovně přístupu (horkou, studenou i archivní) a pro libovolnou konfiguraci replikace (LRS, GRS nebo ZRS).
- Rezervovaná kapacita služby SQL Database: Pokrytí slevou závisí na úrovni výkonu, kterou zvolíte. Další informace najdete ve [vysvětlení způsobu uplatnění slevy za rezervaci Azure](understand-reservation-charges.md).
- Rezervovaná kapacita služby Azure Cosmos DB: Pokrytí slevou závisí na zřízené propustnosti. Další informace najdete ve [vysvětlení způsobu uplatnění slevy za rezervaci služby Azure Cosmos DB](understand-cosmosdb-reservation-charges.md).

## <a name="how-discounts-apply-to-specific-azure-services"></a>Jak se slevy uplatňují u jednotlivých služeb Azure

Přečtěte si následující články, které se na vás vztahují. Dozvíte se, jak se slevy uplatňují na konkrétní služby Azure:

- [App Service](reservation-discount-app-service.md)
- [Azure Cache for Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [Databáze Cosmos](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Database for MySQL](understand-reservation-charges-mysql.md)
- [Database for PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Dedicated Hosts](billing-understand-dedicated-hosts-reservation-charges.md)
- [Disk Storage](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [Softwarové plány](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [SQL Database](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [Virtual Machines](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>Další kroky

- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Náklady na software pro Windows nezahrnuté v rezervacích](reserved-instance-windows-software-costs.md)