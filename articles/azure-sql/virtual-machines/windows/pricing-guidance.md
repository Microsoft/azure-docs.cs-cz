---
title: Doprovodné materiály k cenám & správy nákladů
description: Poskytuje osvědčené postupy pro výběr správného SQL Server cenového modelu virtuálního počítače.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 57e9c82e5685171cff994aca7985f6a4211f00e7
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327283"
---
# <a name="pricing-guidance-for-sql-server-on-azure-vms"></a>Doporučení pro ceny SQL Server na virtuálních počítačích Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek poskytuje cenové poradenství pro [SQL Server v Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md). Existuje několik možností, které mají vliv na náklady, a je důležité vybrat správnou image, která bude vyrovnávat náklady s obchodními požadavky.

> [!TIP]
> Pokud potřebujete zjistit odhad nákladů na určitou kombinaci SQL Server Edition a velikosti virtuálního počítače, přečtěte si stránku s cenami pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Vyberte platformu a edici SQL Server ze seznamu **OS/software** .
>
> ![Stránka s cenami na základě uživatelského rozhraní na virtuálním počítači](./media/pricing-guidance/virtual-machines-pricing-ui.png)
>
> Případně můžete k přidání a konfiguraci virtuálního počítače použít [cenovou kalkulačku](https://azure.microsoft.com/pricing/#explore-cost) . 

## <a name="free-licensed-sql-server-editions"></a>Bezplatné licencované SQL Server edice

Pokud chcete vyvíjet, testovat nebo sestavovat testování konceptu, pak použijte **edici** s licencí zdarma pro SQL Server Developer. Tato edice obsahuje všechny funkce edice SQL Server Enterprise, což vám umožňuje vytvářet a testovat jakýkoli typ aplikace. V produkčním prostředí však nemůžete edici Developer Edition spustit. Virtuální počítač s SQL Server Developer Edition se účtuje jenom za náklady na virtuální počítač, protože neexistují žádné přidružené licenční náklady SQL Server.

Pokud chcete provozovat odlehčené úlohy v produkčním prostředí (<4 jádra, <1 GB paměti, <10 GB/databáze), použijte zdarma licencovanou **edici SQL Server Express**. Virtuální počítač s SQL Server Express Edition se taky účtuje jenom za náklady na virtuální počítač.

U těchto vývojových/testovacích a lehkých produkčních úloh můžete také ušetřit peníze tím, že vyberete menší velikost virtuálního počítače, která odpovídá těmto úlohám. DS1v2 může být v některých scénářích dobrou volbou.

Pokud chcete vytvořit virtuální počítač Azure s SQL Server 2017 s jedním z těchto imagí, přečtěte si následující odkazy:

| Platforma | Volně licencované image |
|---|---|
| Windows Server 2016 | [Virtuální počítač Azure pro vývojáře SQL Server 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Virtuální počítač Azure s SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [Virtuální počítač Azure pro vývojáře SQL Server 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Virtuální počítač Azure s SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Virtuální počítač Azure pro vývojáře SQL Server 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Virtuální počítač Azure s SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Virtuální počítač Azure pro vývojáře SQL Server 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Virtuální počítač Azure s SQL Server 2017 Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Placené edice SQL Server

Pokud máte neodlehčené provozní úlohy, použijte jednu z následujících edic SQL Server:

| Edice SQL Serveru | Úloha |
|-----|-----|
| Web | Malé weby |
| Standard | Malá a středně velká zatížení |
| Enterprise | Velké nebo důležité úlohy|

Máte dvě možnosti, jak platíte za SQL Server licencování pro tyto edice: *Plaťte za použití* nebo *využijte vlastní licenci (BYOL)*.

## <a name="pay-per-usage"></a>Platit podle využití

**Platba SQL Server licence na použití** (označované také jako **průběžné platby**) znamená, že cena za sekundu po spuštění virtuálního počítače Azure zahrnuje náklady na SQL Server licenci. Ceny pro různé SQL Server edice (web, Standard, Enterprise) si můžete prohlédnout na stránce s cenami Azure Virtual Machines pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Náklady jsou stejné pro všechny verze SQL Server (2012 SP3 až 2019). Náklady na licencování za sekundu závisí na počtu vCPU virtuálních počítačů.

Platba SQL Server licencování na použití se doporučuje pro:

- **Dočasné nebo pravidelné úlohy**. Například aplikace, která potřebuje podporovat událost po dobu několika měsíců každý rok nebo obchodní analýzu v pondělí.

- **Úlohy s neznámou životností nebo škálováním**. Například aplikace, které nemusí být potřeba během několika měsíců nebo které můžou vyžadovat více nebo méně výpočetní výkon, v závislosti na poptávce.

Pokud chcete vytvořit virtuální počítač Azure s SQL Server 2017 s jedním z těchto imagí s průběžnými platbami, přečtěte si následující odkazy:

| Platforma | Licencované image |
|---|---|
| Windows Server 2016 | [Virtuální počítač s SQL Server 2017 web Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Virtuální počítač Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Virtuální počítač s SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [Virtuální počítač s SQL Server 2017 web Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Virtuální počítač Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Virtuální počítač s SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Virtuální počítač s SQL Server 2017 web Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Virtuální počítač Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Virtuální počítač s SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Virtuální počítač s SQL Server 2017 web Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Virtuální počítač Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Virtuální počítač s SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Když vytvoříte virtuální počítač s SQL Server v Azure Portal, zobrazí se v okně **zvolit velikost** odhadované náklady. Je důležité si uvědomit, že tento odhad je jenom výpočetní náklady na provozování virtuálního počítače spolu s náklady na licencování operačního systému (Windows nebo operační systémy Linux třetích stran).
>
> ![Okno zvolit velikost virtuálního počítače](./media/pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nezahrnuje dodatečné licenční náklady na SQL Server pro edice web, Standard a Enterprise. Pokud chcete získat nejpřesnější odhad cen, vyberte operační systém a SQL Server edici na stránce s cenami pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Nyní je možné změnit licenční model z plateb na využití a využít vlastní licenci (BYOL) a zpět. Další informace najdete v tématu [Změna licenčního modelu pro SQL Server virtuální počítač](licensing-model-azure-hybrid-benefit-ahb-change.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a> Přineste si vlastní licenci (BYOL)

**Uvedení vlastní licence SQL Server prostřednictvím License mobility**, která se také označuje jako **BYOL**, znamená použití stávající SQL Server multilicenčního programu se Software Assurance ve virtuálním počítači Azure. Virtuální počítač s SQL Server pomocí BYOL se účtuje jenom za náklady na provozování virtuálního počítače, ne pro SQL Server licencování, protože jste už získali licence a Software Assurance prostřednictvím multilicenčního programu.

> [!IMPORTANT]
> Image BYOL vyžadují smlouva Enterprise se Software Assurance. V tuto chvíli nejsou k dispozici jako součást partnera Azure Cloud Solution partner (CSP). Zákazníci CSP můžou využít vlastní licenci nasazením image s průběžnými platbami a následným povolením [zvýhodněné hybridní využití Azure](licensing-model-azure-hybrid-benefit-ahb-change.md).

> [!NOTE]
> Image BYOL jsou teď dostupné jenom pro virtuální počítače s Windows. SQL Server ale můžete nainstalovat ručně jenom na virtuální počítač jenom se systémem Linux. Pokyny najdete v tématu [SQL Server na virtuálním počítači Linux – Nejčastější dotazy](../linux/frequently-asked-questions-faq.md).

Pro poskytování vlastních SQL Server licencování prostřednictvím License Mobility doporučujeme:

- **Nepřetržité úlohy**. Například aplikace, která potřebuje zajistit nepřetržitý provoz obchodních operací.

- **Úlohy se známou životností a škálováním**. Například aplikace, která je nutná pro celý rok a na které poptávku vychází.

Pokud chcete používat BYOL s virtuálním počítačem s SQL Server, musíte mít licenci na SQL Server Standard nebo Enterprise a [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), což je povinná možnost prostřednictvím některých multilicenčních programů a volitelného nákupu s ostatními. Úroveň cen poskytovaná prostřednictvím multilicenčních programů se liší v závislosti na typu smlouvy a množství nebo závazku SQL Server. Ale jako pravidlo, které přináší vlastní licenci pro nepřetržité provozní úlohy, má následující výhody:

| Zvýhodnění BYOL | Popis |
|-----|-----|
| **Úspora nákladů** | [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) nabízí úspory až 55%. Další informace najdete v tématu [Přepnutí modelu licencování](licensing-model-azure-hybrid-benefit-ahb-change.md) . |
| **Bezplatná pasivní sekundární replika** | Další výhodou uvedení vlastní licence je [bezplatné licence pro jednu pasivní sekundární repliku](https://azure.microsoft.com/pricing/licensing-faq/) na SQL Server pro účely vysoké dostupnosti. To snižuje náklady na licence na vysoce dostupné SQL Server nasazení (například pomocí skupin dostupnosti Always On). Práva na spuštění pasivního sekundárního serveru jsou k dispozici prostřednictvím výhod Fail-Overch serverů programu Software Assurance. |

Pokud chcete vytvořit virtuální počítač Azure s SQL Server 2017 s jedním z těchto imagí s vlastní licencí, podívejte se na virtuální počítače s předponou {BYOL}:

- [Virtuální počítač s SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [Virtuální počítač Azure Standard SQL Server 2017](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Dejte nám do 10 dnů, kolik licencí SQL Server používáte v Azure. Odkazy na předchozí image obsahují pokyny k tomu, jak to provést.

> [!NOTE]
> Nyní je možné změnit licenční model z plateb na využití a využít vlastní licenci (BYOL) a zpět. Další informace najdete v tématu [Změna licenčního modelu pro SQL Server virtuální počítač](licensing-model-azure-hybrid-benefit-ahb-change.md). 



## <a name="reduce-costs"></a>Snížení nákladů

Aby nedocházelo k zbytečným nákladům, vyberte optimální velikost virtuálního počítače a zvažte občasné vypnutí pro nesouvislé úlohy.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a> Správná velikost virtuálního počítače

Náklady na licencování SQL Server přímo souvisí s počtem vCPU. Vyberte velikost virtuálního počítače, která odpovídá očekávaným potřebám pro procesor, paměť, úložiště a I/O šířku pásma. Úplný seznam možností velikosti počítače najdete v tématu [velikosti virtuálních počítačů s Windows](../../../virtual-machines/sizes.md) a [velikosti virtuálních počítačů](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)se systémem Linux.

Existují nové velikosti počítačů, které dobře fungují s určitými typy SQL Server úloh. Tyto počítače mají na paměti udržování vysoké úrovně paměti, úložiště a vstupně-výstupních propustností, ale mají nižší virtualizovaný počet jader. Zvažte například následující příklad:

| Velikost virtuálního počítače | Virtuální procesory | Memory (Paměť) | Maximální počet disků | Maximální propustnost vstupně-výstupních operací | SQL Server náklady na licencování | Celkové náklady (COMPUTE a licencování) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 vstupně-výstupních operací nebo 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51 200 vstupně-výstupních operací nebo 768 MB/s | O 75 % nižší | O 57 % nižší |

> [!IMPORTANT]
> Toto je příklad v daném časovém okamžiku. Nejnovější specifikace najdete v článcích o velikostech počítačů a na stránce s cenami Azure pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

V předchozím příkladu vidíte, že specifikace pro **Standard_DS14v2** a **Standard_DS14-4v2** jsou stejné s výjimkou vCPU. Přípona **4v2** na konci velikosti počítače **Standard_DS14-4v2** označuje počet aktivních vCPU. Vzhledem k tomu, že SQL Server náklady na licencování jsou vázané na počet vCPU, významně snižuje náklady na virtuální počítač ve scénářích, kde nejsou potřeba další vCPU. Jedná se o jeden příklad a existuje mnoho velikostí počítačů s omezenými vCPU, které jsou identifikované pomocí tohoto vzoru přípon. Další informace najdete v blogovém příspěvku s [oznámením nových velikostí virtuálních počítačů Azure](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/), které vám poúčtují cenově výhodnější databázovou práci.

### <a name="shut-down-your-vm-when-possible"></a>Pokud je to možné, vypněte virtuální počítač.

Pokud používáte nějaké úlohy, které neběží nepřetržitě, zvažte vypnutí virtuálního počítače během neaktivních období. Platíte jenom za to, co používáte.

Pokud například jednoduše vyzkoušíte SQL Server na virtuálním počítači Azure, nebudete se vám účtovat poplatky, protože by to mělo běžet po dobu týdnů. Jedním z řešení je použití [funkce automatického vypínání](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Automatické vypnutí SQL Server virtuálního počítače](./media/pricing-guidance/sql-vm-auto-shutdown.png)

Automatické vypnutí je součástí větší sady podobných funkcí poskytovaných [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Pro jiné pracovní postupy zvažte automatické vypínání a restartování virtuálních počítačů Azure pomocí skriptovacího řešení, jako je [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Jediným způsobem, jak se vyhnout poplatkům, je vypnutí a zrušení přidělení virtuálního počítače. Pouhým zastavením nebo použitím možností napájení pro vypnutí virtuálního počítače se stále účtují poplatky za využití.

## <a name="next-steps"></a>Další kroky

Obecné doprovodné materiály pro Azure najdete v tématu [Ochrana před neočekávanými náklady pomocí fakturace a správy nákladů Azure](../../../cost-management-billing/cost-management-billing-overview.md). Nejnovější ceny za Azure Virtual Machines včetně SQL Server najdete na stránce s cenami Azure Virtual Machines pro virtuální počítače s Windows a [virtuální počítače](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)se [systémem](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Linux.

Přehled SQL Server v Azure Virtual Machines najdete v následujících článcích:

- [Přehled SQL Server na virtuálních počítačích s Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Přehled SQL Server on Linux virtuálních počítačů](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)