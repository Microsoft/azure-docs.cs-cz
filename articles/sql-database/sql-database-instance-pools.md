---
title: Fondy instancí Azure SQL Database (Preview)
description: Tento článek popisuje Azure SQL Database fondy instancí (Preview).
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
ms.openlocfilehash: 7d8c316d5c78cfe09bcf134b5a5c513e1c007d74
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689767"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Co jsou fondy SQL Database instancí (Preview)?

Fondy instancí jsou novým prostředkem v Azure SQL Database, který poskytuje pohodlný a cenově výhodný způsob migrace menších instancí SQL do cloudu ve velkém měřítku.

Fondy instancí umožňují předem zřídit výpočetní prostředky podle vašich celkových požadavků na migraci. Následně můžete nasadit několik jednotlivých spravovaných instancí až do předem zřízené výpočetní úrovně. Pokud například předzřídíte 8 virtuální jádra, můžete nasadit dvě 2 vCore a jednu 4 instance vCore a následně migrovat databáze do těchto instancí. Před tím, než jsou fondy instancí k dispozici, je při migraci do cloudu často nutné konsolidovat menší a méně úlohy náročné na výpočetní výkon. Nutnost migrace skupin databází na velkou instanci obvykle vyžaduje pečlivé plánování kapacity a zásady správného řízení prostředků, další požadavky na zabezpečení a další práci při konsolidaci dat na úrovni instance.

Fondy instancí navíc podporují nativní integraci virtuální sítě, takže můžete ve stejné podsíti nasadit více fondů instancí a více instancí s několika samostatnými instancemi.


## <a name="key-capabilities-of-instance-pools"></a>Klíčové funkce fondů instancí

Fondy instancí poskytují následující výhody:

1. Možnost hostovat 2 instance vCore *\*jenom pro instance ve fondech instancí*.
2. Předvídatelný a rychlý čas nasazení instance (až 5 minut).
3. Minimální přidělení IP adresy.

Následující diagram znázorňuje fond instancí s několika instancemi nasazenými v podsíti virtuální sítě.

![fond instancí s více instancemi](./media/sql-database-instance-pools/instance-pools1.png)

Fondy instancí umožňují nasazení více instancí na stejném virtuálním počítači, kde je velikost výpočetní služby virtuálního počítače založená na celkovém počtu virtuální jádra přidělených pro daný fond. Tato architektura umožňuje *rozdělit* virtuální počítač na více instancí, což může být libovolná podporovaná velikost, včetně 2 virtuální jádra (2 instance Vcore je k dispozici pouze pro instance ve fondech).

Operace správy u instancí ve fondu jsou po prvním nasazení fondu mnohem rychlejší. Tyto operace jsou rychlejší, protože nasazení nebo rozšíření [virtuálního clusteru](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (vyhrazené sady virtuálních počítačů) není součástí zřizování spravované instance.

Vzhledem k tomu, že všechny instance ve fondu sdílejí stejný virtuální počítač, celkové přidělování IP adres nezávisí na počtu nasazených instancí, což je pohodlné pro nasazení v podsítích s úzkým rozsahem IP adres.

Každý fond má pevnou IP alokaci jenom devět IP adres (nezahrnuje pět IP adres v podsíti, které jsou vyhrazené pro vlastní potřeby). Podrobnosti najdete v tématu [požadavky na velikost podsítě pro jednotlivé instance](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Scénáře aplikací pro fondy instancí

Následující seznam poskytuje hlavní případy použití, ve kterých by se měly brát v úvahu fondy instancí:

- Migrace *skupiny instancí SQL* ve stejnou dobu, kde většina má menší velikost (například 2 nebo 4 virtuální jádra).
- Scénáře, ve kterých je důležité *předvídatelné a krátké vytváření nebo škálování instancí* . Například nasazení nového tenanta ve víceklientském prostředí aplikace SaaS, které vyžaduje možnosti na úrovni instance.
- Jsou důležité scénáře s *pevnými náklady* nebo *omezením útraty* . Můžete například spouštět sdílené vývojové a testovací prostředí nebo ukázková prostředí s pevnou (nebo často měnící) velikostí, kde je potřeba pravidelně nasazovat spravované instance.
- Scénáře, kdy je důležité *přidělování minimálních IP adres* v podsíti virtuální sítě. Všechny instance ve fondu sdílejí virtuální počítač, takže počet přidělených IP adres je nižší, než v případě jedné instance.


## <a name="architecture-of-instance-pools"></a>Architektura fondů instancí

Fondy instancí mají podobnou architekturu s běžnými spravovanými instancemi (*jedné instance*). Pro podporu [nasazení v rámci virtuálních sítí Azure (virtuální sítě)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) a pro zajištění izolace a zabezpečení pro zákazníky využívají fondy instancí také [virtuální clustery](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture). Virtuální clustery reprezentují vyhrazenou sadu izolovaných virtuálních počítačů nasazených v podsíti virtuální sítě zákazníka.

Hlavním rozdílem mezi těmito dvěma modely nasazení je, že fondy instancí povolují nasazení více SQL Server procesů ve stejném uzlu virtuálního počítače, který je prostředkem, který se řídí pomocí [objektů úloh systému Windows](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), zatímco jednotlivé instance jsou vždy samostatné. uzel virtuálního počítače.

Následující diagram znázorňuje fond instancí a dvě jednotlivé instance nasazené ve stejné podsíti a ilustrují hlavní informace o architektuře pro oba modely nasazení:

![fond instancí a dvě jednotlivé instance](./media/sql-database-instance-pools/instance-pools2.png)

Každý fond instancí vytvoří pod sebou samostatný virtuální cluster. Instance v rámci fondu a jedné instance nasazené ve stejné podsíti nesdílejí výpočetní prostředky přidělené pro SQL Server procesy a součásti brány, což zajišťuje předvídatelnost výkonu.

## <a name="instance-pools-resource-limitations"></a>Omezení prostředků fondů instancí

Na fondy instancí a instance v rámci fondů se vztahuje několik omezení prostředků:

- Fondy instancí jsou k dispozici pouze na Gen5 hardwaru.
- Instance v rámci fondu mají vyhrazené PROCESORy a paměť RAM, takže agregovaný počet virtuální jádra napříč všemi instancemi musí být menší nebo roven počtu virtuální jádra přidělených fondu.
- Všechna [omezení na úrovni instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) se vztahují na instance vytvořené v rámci fondu.
- Kromě omezení na úrovni instance existují také dvě omezení, která jsou uložena *na úrovni fondu instancí*:
  - Celková velikost úložiště na fond (8 TB).
  - Celkový počet databází na fond (100).

Celkové přidělení úložiště a počet databází napříč všemi instancemi musí být nižší nebo roven omezením vystaveným fondy instancí.

- Fondy instancí podporují 8, 16, 24, 32, 40, 64 a 80 virtuální jádra.
- Spravované instance v rámci fondů podporují 2, 4, 8, 16, 24, 32, 40, 64 a 80 virtuální jádra.
- Spravované instance v rámci fondů podporují velikosti úložiště mezi 32 GB a 8 TB, s výjimkou:
  - 2 instance vCore podporují velikosti mezi 32 GB a 640 GB
  - 4 instance vCore podporují velikosti mezi 32 GB a 2 TB.

[Vlastnost vrstvy služeb](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) je přidružená k prostředku fondu instancí, takže všechny instance ve fondu musí být stejné jako úroveň služby fondu. V tuto chvíli je dostupná jenom úroveň služby Pro obecné účely Service (v části omezení v aktuální verzi Preview viz následující část).

### <a name="public-preview-limitations"></a>Omezení veřejné verze Preview

Verze Public Preview má následující omezení:

- V současné době je k dispozici pouze úroveň služby Pro obecné účely.
- Fondy instancí se nedají škálovat během veřejné verze Preview, takže je potřeba důkladné plánování kapacity před tím, než je nasazení důležité.
- Azure Portal podporu pro vytváření a konfiguraci fondu instancí ještě není k dispozici. Všechny operace s fondy instancí se podporují jenom přes PowerShell. Nasazení počáteční instance v předem vytvořeném fondu je podporované i přes PowerShell. Po nasazení do fondu lze spravované instance aktualizovat pomocí Azure Portal.
- Spravované instance vytvořené mimo fond nejde přesunout do existujícího fondu a instance, které se vytvořily v rámci fondu, nejde přesunout mimo jednu instanci nebo do jiného fondu.
- Ceny za rezervované instance (včetně licencí nebo Zvýhodněné hybridní využití Azure) nejsou k dispozici.

## <a name="sql-features-supported"></a>Podporované funkce SQL

Instance vytvořené v fondech podporují stejné [úrovně kompatibility a funkce podporované v rámci jedné spravované instance](sql-database-managed-instance.md#sql-features-supported).

Každá spravovaná instance nasazená ve fondu má samostatnou instanci SQL agenta.

Volitelné funkce nebo funkce, které vyžadují, abyste zvolili konkrétní hodnoty (například kolace na úrovni instance, časové pásmo, veřejný koncový bod pro přenos dat, skupiny převzetí služeb při selhání), se konfigurují na úrovni instance a můžou se lišit pro každou instanci ve fondu.

## <a name="performance-considerations"></a>Otázky výkonu

I když spravované instance v rámci fondů mají vyhrazené vCore a RAM, sdílí místní disk (pro použití databáze tempdb) a síťové prostředky. Není pravděpodobné, ale je možné vyzkoušet *sousední* efekt v případě vysokého počtu instancí, pokud má více instancí ve fondu současně stejnou spotřebu prostředků. Pokud toto chování provedete, zvažte nasazení těchto instancí do většího fondu nebo jako jedné instance.

## <a name="security-considerations"></a>Aspekty zabezpečení

Vzhledem k tomu, že instance nasazené ve fondu sdílejí stejný virtuální počítač, možná budete chtít vzít v úvahu, že jsou zakázané funkce, které vedou k vyšším bezpečnostním rizikům, nebo abyste k těmto funkcím měli oprávnění Například integrace modulu CLR, nativní zálohování a obnovení, databázový e-mail atd.

## <a name="instance-pool-support-requests"></a>Žádosti o podporu fondu instancí

Vytváření a správa žádostí o podporu pro fondy instancí v [Azure Portal](https://portal.azure.com).

Pokud máte problémy související s nasazením fondu instancí (vytvoření nebo odstranění), ujistěte se, že jste v poli **podtyp problému** zadali **fondy instancí** .

![žádost o podporu fondů instancí](./media/sql-database-instance-pools/support-request.png)

Pokud máte problémy související s jednou instancí nebo databázemi v rámci fondu, měli byste vytvořit pravidelný lístek podpory pro Azure SQL Database spravované instance.

Pokud chcete vytvořit větší nasazení spravovaných instancí (s fondy instancí nebo bez nich), budete možná muset získat větší regionální kvótu. Použijte [proceduru standardního spravované instance pro vyžádání větší kvóty](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance), ale Všimněte si, že pokud používáte fondy instancí, logika nasazení porovnává celkovou spotřebu Vcore *na úrovni fondu* s vaší kvótou a určí, zda jste povoluje se vytvářet nové prostředky bez dalšího zvýšení kvóty.

## <a name="instance-pool-billing"></a>Fakturace fondu instancí

Fondy instancí umožňují nezávislé škálování výpočetní kapacity a úložiště. Zákazníci platí za výpočetní prostředky spojené s prostředkem fondu měřeným v virtuální jádra a úložiště přidružené ke každé instanci měřené v gigabajtech (prvních 32 GB je zdarma za každou instanci).

vCore cena za fond se účtuje bez ohledu na to, kolik instancí se v daném fondu nasadí.

Pro cenu za výpočetní výkon (měřenou v virtuální jádra) jsou k dispozici dvě cenové možnosti:

  1. *Zahrnutá licence*: použijte existující licence SQL Server se Software Assurance.
  2. *Zvýhodněné hybridní využití Azure*: snížená cena, která zahrnuje zvýhodněné hybridní využití Azure SQL Server. Zákazníci se můžou k této ceně přihlédnout pomocí svých stávajících licencí SQL Server se Software Assurance. Informace o způsobilosti a dalších podrobnostech najdete v tématu [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Nastavení různých cenových možností není možné pro jednotlivé instance ve fondu. Všechny instance v nadřazeném fondu musí být buď v ceně zahrnuté v licenci, nebo v Zvýhodněné hybridní využití Azure ceně. Licenční model fondu se dá po vytvoření fondu změnit.

> [!IMPORTANT]
> Pokud zadáte licenční model pro instanci, která se liší od fondu, bude použita cena fondu a hodnota úrovně instance je ignorována.

Pokud vytváříte fondy instancí u [předplatných, která mají nárok na zvýhodnění pro vývoj a testování](https://azure.microsoft.com/pricing/dev-test/), automaticky získáte zlevněné sazby až 55 procent na spravované instanci Azure SQL.

Úplné podrobnosti o cenách fondu instancí najdete v části *fondy instancí* na [stránce s cenami spravované instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Další kroky

- Chcete-li začít s fondy instancí, přečtěte si téma [Průvodce vytvořením fondů instancí SQL Database](sql-database-instance-pools-how-to.md).
- Další informace o tom, jak vytvořit první spravovanou instanci, najdete v tématu [Průvodce rychlým startem](sql-database-managed-instance-get-started.md).
- Seznam funkcí a porovnání najdete v tématu věnovaném [běžným funkcím SQL](sql-database-features.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě VNet spravované instance](sql-database-managed-instance-connectivity-architecture.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Kurz pro migraci pomocí Azure Database Migration Service (DMS) najdete v tématu [migrace spravované instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Pro pokročilé monitorování výkonu databáze spravované instance s integrovanými funkcemi pro odstraňování potíží najdete informace v tématu [monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Informace o cenách najdete v tématu [SQL Database ceny za Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).