---
title: Co je fond spravovaných instancí Azure SQL?
titleSuffix: Azure SQL Managed Instance
description: Přečtěte si o fondech spravovaných instancí Azure SQL (Preview), což je funkce, která poskytuje pohodlný a cenově výhodný způsob migrace menších SQL Server databází do cloudu ve velkém měřítku a správě více spravovaných instancí.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: ab77c8cf563c315768ad1c16089d8d939c085322
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782650"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>Co je fond spravovaných instancí Azure SQL (Preview)?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Fondy instancí ve spravované instanci Azure SQL poskytují pohodlný a cenově výhodný způsob migrace menších instancí SQL Server do cloudu ve velkém měřítku.

Fondy instancí umožňují předem zřídit výpočetní prostředky podle vašich celkových požadavků na migraci. Následně můžete nasadit několik jednotlivých spravovaných instancí až do předem zřízené výpočetní úrovně. Pokud například předzřídíte 8 virtuální jádra, můžete nasadit instanci 2 2-vCore a 1 4-vCore a následně migrovat databáze do těchto instancí. Před tím, než jsou fondy instancí k dispozici, je při migraci do cloudu často nutné konsolidovat menší a méně úlohy náročné na výpočetní výkon. Nutnost migrace skupin databází na velkou instanci obvykle vyžaduje pečlivé plánování kapacity a zásady správného řízení prostředků, další požadavky na zabezpečení a další práci při konsolidaci dat na úrovni instance.

Fondy instancí navíc podporují nativní integraci virtuální sítě, takže můžete ve stejné podsíti nasadit více fondů instancí a více instancí s několika samostatnými instancemi.

## <a name="key-capabilities"></a>Klíčové funkce

Fondy instancí poskytují následující výhody:

1. Možnost hostování 2-vCore instancí. *\* Pouze pro instance ve fondech instancí* .
2. Předvídatelný a rychlý čas nasazení instance (až 5 minut).
3. Minimální přidělení IP adresy.

Následující diagram znázorňuje fond instancí s více spravovanými instancemi nasazenými v podsíti virtuální sítě.

![fond instancí s více instancemi](./media/instance-pools-overview/instance-pools1.png)

Fondy instancí umožňují nasazení více instancí na stejném virtuálním počítači, kde je velikost výpočetního virtuálního počítače založená na celkovém počtu virtuální jádra přidělených pro daný fond. Tato architektura umožňuje *rozdělit* virtuální počítač na více instancí, což může být libovolná podporovaná velikost, včetně 2 virtuální jádra (2 – Vcore Instances jsou k dispozici pouze pro instance ve fondech).

Po počátečním nasazení budou operace správy u instancí ve fondu mnohem rychlejší. Důvodem je to, že nasazení nebo rozšíření [virtuálního clusteru](connectivity-architecture-overview.md#high-level-connectivity-architecture) (vyhrazená sada virtuálních počítačů) není součástí zřizování spravované instance.

Vzhledem k tomu, že všechny instance ve fondu sdílejí stejný virtuální počítač, celkové přidělování IP adres nezávisí na počtu nasazených instancí, což je pohodlné pro nasazení v podsítích s úzkým rozsahem IP adres.

Každý fond má pevnou IP alokaci jenom devět IP adres (nezahrnuje pět IP adres v podsíti, které jsou vyhrazené pro vlastní potřeby). Podrobnosti najdete v tématu [požadavky na velikost podsítě pro jednotlivé instance](vnet-subnet-determine-size.md).

## <a name="application-scenarios"></a>Scénáře aplikací

Následující seznam poskytuje hlavní případy použití, ve kterých by se měly brát v úvahu fondy instancí:

- Migrace *skupiny instancí SQL Server* v současné době, kde je většina menší velikost (například 2 nebo 4 virtuální jádra).
- Scénáře, ve kterých je důležité *předvídatelné a krátké vytváření nebo škálování instancí* . Například nasazení nového tenanta ve víceklientském prostředí aplikace SaaS, které vyžaduje možnosti na úrovni instance.
- Jsou důležité scénáře s *pevnými náklady* nebo *omezením útraty* . Můžete například spouštět sdílené vývojové a testovací prostředí nebo ukázková prostředí s pevnou (nebo často měnící) velikostí, kde je potřeba pravidelně nasazovat spravované instance.
- Scénáře, kdy je důležité *přidělování minimálních IP adres* v podsíti virtuální sítě. Všechny instance ve fondu sdílejí virtuální počítač, takže počet přidělených IP adres je nižší, než v případě jedné instance.

## <a name="architecture"></a>Architektura

Fondy instancí mají podobnou architekturu pro běžné ( *samostatné* ) spravované instance. Aby bylo možné podporovat [nasazení v rámci virtuálních sítí Azure](../../virtual-network/virtual-network-for-azure-services.md) a poskytovat izolaci a zabezpečení pro zákazníky, využívají fondy instancí také [virtuální clustery](connectivity-architecture-overview.md#high-level-connectivity-architecture). Virtuální clustery reprezentují vyhrazenou sadu izolovaných virtuálních počítačů nasazených v podsíti virtuální sítě zákazníka.

Hlavním rozdílem mezi těmito dvěma modely nasazení je, že fondy instancí povolují nasazení více SQL Server procesů ve stejném uzlu virtuálního počítače, který je prostředkem, který se řídí pomocí [objektů úloh systému Windows](/windows/desktop/ProcThread/job-objects), zatímco jednotlivé instance jsou vždy pouze v uzlu virtuálního počítače.

Následující diagram znázorňuje fond instancí a dvě jednotlivé instance nasazené ve stejné podsíti a ilustrují hlavní informace o architektuře pro oba modely nasazení:

![Fond instancí a dvě jednotlivé instance](./media/instance-pools-overview/instance-pools2.png)

Každý fond instancí vytvoří pod sebou samostatný virtuální cluster. Instance v rámci fondu a jedné instance nasazené ve stejné podsíti nesdílejí výpočetní prostředky přidělené pro SQL Server procesy a součásti brány, které zajistí předvídatelnost výkonu.

## <a name="resource-limitations"></a>Omezení prostředků

Na fondy instancí a instance v rámci fondů se vztahuje několik omezení prostředků:

- Fondy instancí jsou k dispozici pouze na Gen5 hardwaru.
- Spravované instance v rámci fondu mají vyhrazený procesor a paměť RAM, takže agregovaný počet virtuální jádra napříč všemi instancemi musí být menší nebo roven počtu virtuální jádra přidělených fondu.
- Všechny [limity na úrovni instance](resource-limits.md#service-tier-characteristics) se vztahují na instance vytvořené v rámci fondu.
- Kromě omezení na úrovni instance platí také dvě omezení *na úrovni fondu instancí* :
  - Celková velikost úložiště na fond (8 TB).
  - Celkový počet databází na fond (100).
- Správce AAD nelze nastavit pro instance nasazené uvnitř fondu instancí, nelze proto použít ověřování AAD.

Celkové přidělení úložiště a počet databází napříč všemi instancemi musí být nižší než nebo rovno omezením vystaveným fondy instancí.

- Fondy instancí podporují 8, 16, 24, 32, 40, 64 a 80 virtuální jádra.
- Spravované instance v rámci fondů podporují 2, 4, 8, 16, 24, 32, 40, 64 a 80 virtuální jádra.
- Spravované instance v rámci fondů podporují velikosti úložiště mezi 32 GB a 8 TB, s výjimkou:
  - 2 instance vCore podporují velikosti mezi 32 GB a 640 GB
  - 4 instance vCore podporují velikosti mezi 32 GB a 2 TB.

[Vlastnost vrstvy služeb](resource-limits.md#service-tier-characteristics) je přidružená k prostředku fondu instancí, takže všechny instance ve fondu musí být stejné jako úroveň služby fondu. V tuto chvíli je dostupná jenom úroveň služby Pro obecné účely Service (v části omezení v aktuální verzi Preview viz následující část).

### <a name="public-preview-limitations"></a>Omezení veřejné verze Preview

Verze Public Preview má následující omezení:

- V současné době je k dispozici pouze úroveň služby Pro obecné účely.
- Fondy instancí se nedají škálovat během veřejné verze Preview, takže je potřeba důkladné plánování kapacity před tím, než je nasazení důležité.
- Azure Portal podporu pro vytváření a konfiguraci fondu instancí ještě není k dispozici. Všechny operace s fondy instancí se podporují jenom přes PowerShell. Nasazení počáteční instance v předem vytvořeném fondu je podporované i přes PowerShell. Po nasazení do fondu lze spravované instance aktualizovat pomocí Azure Portal.
- Spravované instance vytvořené mimo fond nelze přesunout do existujícího fondu a instance vytvořené v rámci fondu nelze přesunout mimo jednu instanci nebo do jiného fondu.
- Ceny za [rezervované instance kapacity](../database/reserved-capacity-overview.md) nejsou k dispozici.

## <a name="sql-features-supported"></a>Podporované funkce SQL

Spravované instance vytvořené ve fondech podporují stejné [úrovně kompatibility a funkce podporované v rámci jedné spravované instance](sql-managed-instance-paas-overview.md#sql-features-supported).

Každá spravovaná instance nasazená ve fondu má samostatnou instanci SQL agenta.

Volitelné funkce nebo funkce, které vyžadují, abyste vybrali konkrétní hodnoty (například kolace na úrovni instance, časové pásmo, veřejný koncový bod pro přenos dat, skupiny převzetí služeb při selhání), se konfigurují na úrovni instance a můžou se lišit pro každou instanci ve fondu.

## <a name="performance-considerations"></a>Otázky výkonu

I když spravované instance v rámci fondů mají vyhrazené vCore a RAM, sdílí místní disk (pro použití databáze tempdb) a síťové prostředky. Není pravděpodobné, ale je možné vyzkoušet *sousední* efekt v případě vysokého počtu instancí, pokud má více instancí ve fondu současně stejnou spotřebu prostředků. Pokud toto chování provedete, zvažte nasazení těchto instancí do většího fondu nebo jako jedné instance.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Vzhledem k tomu, že instance nasazené ve fondu sdílejí stejný virtuální počítač, možná budete chtít vzít v úvahu, že jsou zakázané funkce, které vedou k vyšším bezpečnostním rizikům, nebo abyste k těmto funkcím měli oprávnění Například integrace modulu CLR, nativní zálohování a obnovení, databázový e-mail atd.

## <a name="instance-pool-support-requests"></a>Žádosti o podporu fondu instancí

Vytváření a správa žádostí o podporu pro fondy instancí v [Azure Portal](https://portal.azure.com).

Pokud máte problémy související s nasazením fondu instancí (vytvoření nebo odstranění), ujistěte se, že jste v poli **podtyp problému** zadali **fondy instancí** .

![Žádost o podporu fondů instancí](./media/instance-pools-overview/support-request.png)

Pokud máte problémy související s jednou spravovanou instancí nebo databází v rámci fondu, měli byste vytvořit pravidelný lístek podpory pro spravovanou instanci Azure SQL.

Chcete-li vytvořit větší nasazení spravované instance SQL (s fondy instancí nebo bez nich), budete možná muset získat větší regionální kvótu. Další informace najdete v tématu [zvýšení kvóty žádostí o Azure SQL Database](../database/quota-increase-request.md). Logika nasazení pro fondy instancí porovnává celkovou spotřebu vCore *na úrovni fondu* s vaší kvótou, abyste zjistili, jestli máte povoleno vytvářet nové prostředky bez dalšího zvýšení kvóty.

## <a name="instance-pool-billing"></a>Fakturace fondu instancí

Fondy instancí umožňují nezávislé škálování výpočetní kapacity a úložiště. Zákazníci platí za výpočetní prostředky spojené s prostředkem fondu měřeným v virtuální jádra a úložiště přidružené ke každé instanci měřené v gigabajtech (prvních 32 GB je zdarma za každou instanci).

vCore cena za fond se účtuje bez ohledu na to, kolik instancí se v daném fondu nasadí.

Pro cenu za výpočetní výkon (měřenou v virtuální jádra) jsou k dispozici dvě cenové možnosti:

  1. *Licence zahrnuje* : cena za licence SQL Server. Pro zákazníky, kteří se rozhodnou používat stávající licence SQL Server se Software Assurance.
  2. *Zvýhodněné hybridní využití Azure* : snížená cena, která zahrnuje zvýhodněné hybridní využití Azure SQL Server. Zákazníci se můžou k této ceně přihlédnout pomocí svých stávajících licencí SQL Server se Software Assurance. Informace o způsobilosti a dalších podrobnostech najdete v tématu [zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Nastavení různých cenových možností není možné pro jednotlivé instance ve fondu. Všechny instance v nadřazeném fondu musí být buď v ceně zahrnuté v licenci, nebo v Zvýhodněné hybridní využití Azure ceně. Licenční model fondu se dá po vytvoření fondu změnit.

> [!IMPORTANT]
> Pokud zadáte licenční model pro instanci, která se liší od fondu, bude použita cena fondu a hodnota úrovně instance je ignorována.

Pokud vytváříte fondy instancí u [předplatných, která mají nárok na zvýhodnění pro vývoj a testování](https://azure.microsoft.com/pricing/dev-test/), automaticky získáte zlevněné sazby až 55 procent na spravované instanci Azure SQL.

Úplné podrobnosti o cenách fondu instancí najdete v části *fondy instancí* na [stránce s cenami spravované instance SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít s fondy instancí, najdete v tématu [Průvodce fondy spravované instance SQL](instance-pools-configure.md).
- Další informace o tom, jak vytvořit první spravovanou instanci, najdete v tématu [Průvodce rychlým startem](instance-create-quickstart.md).
- Seznam funkcí a porovnání najdete v tématu [běžné funkce Azure SQL](../database/features-comparison.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě spravované instance SQL](connectivity-architecture-overview.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Kurz týkající se použití Azure Database Migration Service k migraci najdete v tématu [migrace spravované instance SQL pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Informace o pokročilém monitorování výkonu databáze spravované instance SQL s integrovanými funkcemi pro řešení potíží najdete v tématu [monitorování spravované instance Azure SQL pomocí Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Informace o cenách najdete v tématu [ceny za spravované instance SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).