---
title: Fondy instancí (náhled)
description: Tento článek popisuje fondy instancí Azure SQL Database (náhled).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c1e740fbfa4bf1e8a77a2d9d6060ab39dba7ae7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256208"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Co jsou fondy instancí databáze SQL (náhled)?

Fondy instancí jsou nový prostředek v Azure SQL Database, který poskytuje pohodlný a nákladově efektivní způsob migrace menších instancí SQL do cloudu ve velkém měřítku.

Fondy instancí umožňují předem zřídit výpočetní prostředky podle vašich celkových požadavků na migraci. Následně můžete nasadit několik jednotlivých spravovaných instancí až do předem zřízené výpočetní úrovně. Například pokud jste pre-provision 8 virtuálníjádra můžete nasadit dvě 2 virtuální jádra a jeden 4 virtuální jádra instance a pak migrovat databáze do těchto instancí. Před fondy instancí jsou k dispozici, menší a méně náročné výpočetní úlohy by často muset být sloučeny do větší spravované instance při migraci do cloudu. Potřeba migrovat skupiny databází do velké instance obvykle vyžaduje pečlivé plánování kapacity a zásadsprávné řízení zdrojů, další aspekty zabezpečení a některé další konsolidace dat práce na úrovni instance.

Fondy instancí navíc podporují nativní integraci virtuální sítě, takže můžete nasadit více fondů instancí a více jednotlivých instancí ve stejné podsíti.


## <a name="key-capabilities-of-instance-pools"></a>Klíčové možnosti fondů instancí

Fondy instancí poskytují následující výhody:

1. Možnost hostovat 2 instance virtuálních jader. *Pouze pro instance v instancích fondů . \**
2. Předvídatelná a rychlá doba nasazení instance (až 5 minut).
3. Minimální přidělení IP adresy.

Následující diagram znázorňuje fond instancí s více instancemi nasazenými v podsíti virtuální sítě.

![fond instancí s více instancemi](./media/sql-database-instance-pools/instance-pools1.png)

Fondy instancí umožňují nasazení více instancí na stejném virtuálním počítači, kde je výpočetní velikost virtuálního počítače založená na celkovém počtu virtuálních jader přidělených pro fond. Tato architektura umožňuje *rozdělení* virtuálního počítače do více instancí, což může být libovolná podporovaná velikost, včetně 2 virtuálních jader (2 instance virtuálních jader jsou k dispozici pouze pro instance ve fondech).

Operace správy na instance ve fondu jsou mnohem rychlejší, jakmile je fond původně nasazen. Tyto operace jsou rychlejší, protože nasazení nebo rozšíření [virtuálního clusteru](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (vyhrazená sada virtuálních počítačů) není součástí zřizování spravované instance.

Vzhledem k tomu, že všechny instance ve fondu sdílejí stejný virtuální počítač, celkové přidělení IP nezávisí na počtu nasazených instancí, což je vhodné pro nasazení v podsítích s úzkým rozsahem IP adres.

Každý fond má pevné přidělení IP adres pouze devět IP adres (bez pěti IP adres v podsíti, které jsou vyhrazeny pro jeho vlastní potřeby). Podrobnosti naleznete v tématu [požadavky na velikost podsítě pro jednotlivé instance](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Scénáře aplikací pro fondy instancí

Následující seznam obsahuje hlavní případy použití, kdy by měly být považovány za fondy instancí:

- Migrace *skupiny instancí SQL* současně, kde většina je menší velikost (například 2 nebo 4 virtuální jádra).
- Scénáře, kde *předvídatelné a krátké vytvoření instance nebo škálování* je důležité. Například nasazení nového klienta v aplikačním prostředí SaaS s více tenanty, které vyžaduje možnosti na úrovni instance.
- Scénáře, kde je důležité mít *pevné náklady* nebo *limit útraty.* Například spuštění sdílených dev-test nebo ukázkových prostředí s pevnou (nebo zřídka se měnící) velikostí, kde pravidelně nasazujete spravované instance v případě potřeby.
- Scénáře, kde je důležité *minimální přidělení IP adres* v podsíti virtuální sítě. Všechny instance ve fondu sdílejí virtuální počítač, takže počet přidělených IP adres je nižší než v případě jednotlivých instancí.


## <a name="architecture-of-instance-pools"></a>Architektura fondů instancí

Fondy instancí mají podobnou architekturu jako běžné spravované instance *(jednotlivé instance*). Pro podporu [nasazení v rámci virtuálních sítí (Virtuální sítě)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) a poskytování izolace a zabezpečení pro zákazníky, fondy instancí také spoléhají na [virtuální clustery](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture). Virtuální clustery představují vyhrazenou sadu izolovaných virtuálních počítačů nasazených uvnitř podsítě virtuální sítě zákazníka.

Hlavní rozdíl mezi dvěma modely nasazení spočívá v tom, že fondy instancí umožňují nasazení procesů více serverů SQL Server na stejném uzlu virtuálního počítače, což jsou prostředky řízené pomocí [objektů úloh systému Windows](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), zatímco jednotlivé instance jsou vždy samy na uzlu virtuálního počítače.

Následující diagram znázorňuje fond instancí a dvě jednotlivé instance nasazené ve stejné podsíti a ilustruje hlavní architektonické podrobnosti pro oba modely nasazení:

![fond instancí a dvě jednotlivé instance](./media/sql-database-instance-pools/instance-pools2.png)

Každý fond instancí vytvoří pod ním samostatný virtuální cluster. Instance v rámci fondu a jednotlivé instance nasazené ve stejné podsíti nesdílejí výpočetní prostředky přidělené procesům sql serveru a součástem brány, což zajišťuje předvídatelnost výkonu.

## <a name="instance-pools-resource-limitations"></a>Omezení prostředků fondů instancí

Na fondy instancí a instance v rámci fondů se vztahuje několik omezení prostředků:

- Fondy instancí jsou k dispozici pouze na hardwaru Gen5.
- Instance ve fondu mají vyhrazený procesor a paměť RAM, takže agregovaný počet virtuálních jader ve všech instancích musí být menší nebo roven počtu virtuálních jader přidělených do fondu.
- Všechna [omezení úrovně instancí](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) platí pro instance vytvořené v rámci fondu.
- Kromě limitů na úrovni instance existují také dvě omezení uložená *na úrovni fondu instancí*:
  - Celková velikost úložiště na fond (8 TB).
  - Celkový počet databází na fond (100).

Celkové přidělení úložiště a počet databází ve všech instancích musí být nižší nebo rovna limitům vystaveným fondy instancí.

- Fondy instancí podporují 8, 16, 24, 32, 40, 64 a 80 virtuálních jader.
- Spravované instance uvnitř fondů podporují 2, 4, 8, 16, 24, 32, 40, 64 a 80 virtuálních jader.
- Spravované instance ve fondech podporují velikosti úložiště mezi 32 GB a 8 TB, s výjimkou:
  - 2 instance virtuálních jader podporují velikosti mezi 32 GB a 640 GB
  - 4 instance virtuálních jader podporují velikosti mezi 32 GB a 2 TB

[Vlastnost úrovně služby](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) je přidružena k prostředku fondu instancí, takže všechny instance ve fondu musí být stejné úrovně služeb jako úroveň služeb fondu. V současné době je k dispozici pouze úroveň služby obecného použití (viz následující část o omezeních v aktuálním náhledu).

### <a name="public-preview-limitations"></a>Omezení veřejné verze Preview

Veřejná verze Preview má následující omezení:

- V současné době je k dispozici pouze úroveň služby pro všeobecné účely.
- Fondy instancí nelze škálovat během veřejné verze Preview, takže pečlivé plánování kapacity před nasazením je důležité.
- Podpora azure portálu pro vytvoření a konfiguraci fondu instancí ještě není k dispozici. Všechny operace ve fondech instancí jsou podporované pouze prostřednictvím Prostředí PowerShell. Počáteční instance nasazení v předem vytvořeném fondu je také podporována prostřednictvím powershellu pouze. Po nasazení do fondu, spravované instance lze aktualizovat pomocí portálu Azure.
- Spravované instance vytvořené mimo fond nelze přesunout do existujícího fondu a instance vytvořené uvnitř fondu nelze přesunout mimo jako jednu instanci nebo do jiného fondu.
- Ceny rezervovaných instancí (včetně licence nebo s hybridní výhodou Azure) nejsou dostupné.

## <a name="sql-features-supported"></a>Podporované funkce SQL

Instance vytvořené ve fondech podporují stejné [úrovně kompatibility a funkce podporované v jednotlivých spravovaných instancích](sql-database-managed-instance.md#sql-features-supported).

Každá spravovaná instance nasazená ve fondu má samostatnou instanci agenta SQL.

Volitelné funkce nebo funkce, které vyžadují výběr konkrétních hodnot (například řazení na úrovni instance, časové pásmo, veřejný koncový bod pro datový provoz, skupiny převzetí služeb při selhání) jsou konfigurovány na úrovni instance a mohou se lišit pro každou instanci ve fondu.

## <a name="performance-considerations"></a>Otázky výkonu

Přestože spravované instance v rámci fondů mají vyhrazené virtuální jádro a paměť RAM, sdílejí místní disk (pro využití tempdb) a síťové prostředky. Není to pravděpodobné, ale je možné zažít *efekt hlučný soused,* pokud více instancí ve fondu má vysokou spotřebu prostředků současně. Pokud budete dodržovat toto chování, zvažte nasazení těchto instancí do většího fondu nebo jako jednotlivé instance.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Vzhledem k tomu, že instance nasazené ve fondu sdílejí stejný virtuální počítač, můžete zvážit zakázání funkcí, které zavádějí vyšší bezpečnostní rizika, nebo pevně řídit přístupová oprávnění k těmto funkcím. Například integrace CLR, nativní zálohování a obnovení, e-mail databáze atd.

## <a name="instance-pool-support-requests"></a>Žádosti o podporu fondu instancí

Vytvořte a spravujte žádosti o podporu pro fondy instancí na [webu Azure Portal](https://portal.azure.com).

Pokud dochází k problémům souvisejícím s nasazením fondu instancí (vytvoření nebo odstranění), ujistěte se, že jste v poli **podtyp Problém** zadali **fondy instancí.**

![žádost o podporu fondů instancí](./media/sql-database-instance-pools/support-request.png)

Pokud dochází k problémům souvisejícím s jednotlivými instancemi nebo databázemi v rámci fondu, měli byste vytvořit lístek pravidelné podpory pro instance spravované službou Azure SQL Database.

Chcete-li vytvořit větší nasazení spravovaných instancí (s fondy instancí nebo bez něj), budete muset získat větší místní kvótu. Další informace naleznete v [tématu Požadavek na zvýšení kvóty pro Azure SQL Database](quota-increase-request.md). Všimněte si, že pokud používáte fondy instancí, logika nasazení porovnává celkovou spotřebu virtuálních jader *na úrovni fondu* s vaší kvótou a určuje, zda máte povoleno vytvářet nové prostředky bez dalšího zvyšování kvóty.

## <a name="instance-pool-billing"></a>Fakturace fondu instancí

Fondy instancí umožňují škálování výpočetních prostředků a úložiště nezávisle. Zákazníci platí za výpočetní prostředky spojené s fondem, měřeno ve virtuálních jádrech, a úložiště přidružené ke každé instanci měřené v gigabajtech (prvních 32 GB je zdarma pro každou instanci).

Cena virtuálního jádra pro fond se účtuje bez ohledu na to, kolik instancí se v tomto fondu nasadí.

Pro výpočetní cenu (měřeno ve virtuálních jádrech) jsou k dispozici dvě cenové možnosti:

  1. *Včetně licence*: Cena SQL licencí je zahrnuta. To je pro zákazníky, kteří se rozhodnou nepoužívat existující licence SQL Server s Program emitování.
  2. *Hybridní výhoda Azure:* Snížená cena, která zahrnuje hybridní výhody Azure pro SQL Server. Zákazníci se mohou do této ceny přihlásit pomocí svých stávajících licencí SQL Server s programem Software Assurance. Nárok a další podrobnosti najdete v tématu [Hybridní výhoda Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Nastavení různých cenových možností není možné pro jednotlivé instance ve fondu. Všechny instance v nadřazeném fondu musí být buď za cenu zahrnutou v licenci, nebo za cenu hybridní výhody Azure. Licenční model fondu lze změnit po vytvoření fondu.

> [!IMPORTANT]
> Pokud zadáte licenční model pro instanci, která se liší od ve fondu, použije se cena fondu a hodnota úrovně instance bude ignorována.

Pokud vytvoříte fondy instancí na předplatné nárok na [výhodu dev-test](https://azure.microsoft.com/pricing/dev-test/), automaticky obdržíte slevy až 55 procent na Azure SQL spravované instance.

Podrobné informace o cenách fondu instancí najdete v části *fondy instancí* na [stránce spravovaných instancí](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Další kroky

- Chcete-li začít s fondy instancí, najdete v [tématu sql database instance fondy návody](sql-database-instance-pools-how-to.md).
- Informace o tom, jak vytvořit první spravovanou instanci, najdete [v tématu Úvodní příručka](sql-database-managed-instance-get-started.md).
- Funkce a seznam porovnání naleznete v tématu [běžné funkce SQL](sql-database-features.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [konfigurace virtuální sítě spravované instance](sql-database-managed-instance-connectivity-architecture.md).
- Rychlý start, který vytvoří spravovanou instanci a obnoví databázi ze záložního souboru, najdete [v tématu vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Kurz využívající službu Migrace databáze Azure (DMS) pro migraci najdete v tématu [migrace spravovaných instancí pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Pokročilé monitorování výkonu databáze spravovaných instancí s integrovanou inteligencí řešení potíží najdete v tématu [Monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Informace o cenách naleznete v [tématu SQL Database managed instance pricing](https://azure.microsoft.com/pricing/details/sql-database/managed/).
