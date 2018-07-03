---
title: Efektivně spravovat náklady pro SQL Server na virtuálních počítačích Azure | Dokumentace Microsoftu
description: Poskytuje osvědčené postupy pro výběr správné virtuální počítač systému SQL Server cenový model.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/02/2018
ms.author: jroth
ms.openlocfilehash: e9033724f62b383ce70488b98a3a8919e3cb198a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345273"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Ceny pokyny pro virtuální počítače Azure s SQL serverem

Tento článek obsahuje pokyny, ocenění [virtuální počítače s SQL serverem](virtual-machines-windows-sql-server-iaas-overview.md) v Azure. Existuje několik možností, které ovlivňují náklady a je důležité vybrat správnou image, který vyrovnává náklady pomocí obchodní požadavky.

> [!TIP]
> Pokud potřebujete zjistit odhad nákladů pro konkrétní kombinaci edici systému SQL Server a velikost virtuálního počítače, přečtěte si téma stránce s cenami [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Výběr platformy a z edicí systému SQL Server **operační systém nebo Software** seznamu.
>
> ![Uživatelské rozhraní na stránce s cenami virtuálních počítačů](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Nebo použijte [cenové kalkulačky](https://azure.microsoft.com/pricing/#explore-cost) přidání a konfigurace virtuálního počítače. 

## <a name="free-licensed-sql-server-editions"></a>Licencované bezplatně edicích systému SQL Server

Pokud chcete pro vývoj, testování nebo sestavení testování konceptu, použijte volně licencovaný **SQL Server Developer edition**. Tato verze obsahuje všechny funkce edice SQL serveru Enterprise, díky tomu můžete sestavit a otestovat jakýkoli typ aplikace. U verze Developer edition však nelze spustit v produkčním prostředí. SQL Server Developer edition virtuálního počítače pouze neúčtují poplatky za náklady na virtuální počítač, protože nejsou k dispozici žádný přidružený Server SQL, náklady na licencování.

Pokud budete chtít spuštění jednoduché úlohy v produkčním prostředí (< 4 jádra, < 1 GB paměti, < 10 GB/databáze), použijte volně licencovaný **edice systému SQL Server Express**. Edice systému SQL Server Express virtuálního počítače se také pouze neúčtují poplatky za náklady na virtuální počítač.

Pro tyto vývoj/testování a nenáročné produkční úlohy můžete také výběrem menší velikost virtuálního počítače, který odpovídá na tyto úlohy ušetřit peníze. DS1v2 může být dobrou volbou v některých scénářích.

Vytvoření virtuálního počítače Azure s SQL serverem 2017 s jedním z těchto imagí najdete v tématu na následujících odkazech:

| Platforma | Volně licencované Image |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express virtuálním počítači Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express virtuálním počítači Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express virtuálním počítači Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express virtuálním počítači Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Placené edice SQL serveru

Pokud máte jiné lightweight produkční úlohy, použijte jednu z následujících edic systému SQL Server:

| Edice SQL serveru | Úloha |
|-----|-----|
| Web | Malé webové stránky |
| Standard | Malé až střední úlohy |
| Enterprise | Velká, nebo klíčové úlohy|

Máte dvě možnosti k platbám za licencování SQL serveru v těchto edicích: *platit za použití* nebo *používání vlastní licence (BYOL)*.

## <a name="pay-per-usage"></a>Platba za použití

**Licenci systému SQL Server za použití platební** znamená, že za sekundu náklady na provozování virtuálního počítače Azure zahrnují cenu licence SQL serveru. Můžete zobrazit ceny pro jiné edice systému SQL Server (Web, Standard, Enterprise) ve virtuálním počítači Azure stránce s cenami za [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Náklady jsou stejné pro všechny verze systému SQL Server (2012 SP3-2017). Náklady na licencování za sekundu závisí na počtu virtuálních procesorů virtuálního počítače.

SQL serveru platíte licencování za využití se doporučuje pro:

- **Dočasné nebo pravidelné úlohy**. Například aplikace, který musí podporovat událost pár měsíců každý rok nebo obchodní analýza v pondělí.

- **Úlohy se neznámý životnosti nebo určený počet číslic**. Například aplikace, které nemusí být vyžadované během pár měsíců, nebo které může vyžadovat více nebo méně výpočetní výkon, v závislosti na vyžádání.

Vytvoření virtuálního počítače Azure s SQL serverem 2017 s jedním z těchto imagí platby za využití najdete v tématu na následujících odkazech:

| Platforma | Licencované Image |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Při vytváření virtuálního počítače s SQL serverem na portálu **zvolte velikost** okně zobrazí odhadované náklady. Je důležité si uvědomit, že tento odhad pouze náklady na výpočty pro spuštění virtuálního počítače společně s náklady (Windows nebo operačních systémů Linux třetích stran) licencí k operačnímu systému.
>
> ![Zvolte velikost okna virtuálního počítače](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nezahrnuje dodatečné licenční náklady na pro edice Web, Standard a Enterprise systému SQL Server. Pokud chcete získat co nejvíce zpřesnili cenové odhad, vyberte svůj operační systém a edice systému SQL Server na stránce s cenami [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="bring-your-own-license-byol"></a>Používání vlastní licence (BYOL)

**Přináší díky mobilitě licencí používat vlastní licenci na SQL Server**, která se také označují jako **BYOL**, znamená, že pomocí stávající licence SQL Server svazek s programem Software Assurance na Virtuálním počítači Azure. SQL Server virtuálního počítače s pomocí BYOL jenom poplatky za náklady na provozování virtuálního počítače, nikoli pro licencování SQL serveru, vzhledem k tomu, že jste už získali licencí se Software Assurance prostřednictvím multilicenčního programu.

> [!NOTE]
> BYOL Image jsou aktuálně dostupné jenom pro virtuální počítače s Windows. Můžete však ručně nainstalujte SQL Server na virtuálním počítači pouze pro Linux. Viz pokyny v [nejčastější dotazy týkající se virtuálních počítačů v systému Linux SQL](../../linux/sql/sql-server-linux-faq.md).

Přenesení vlastní SQL licencování díky mobilitě licencí používat se doporučuje pro:

- **Nepřetržité úlohy**. Například aplikace, které potřebuje pro podporu obchodních operací 24 x 7.

- **Úlohy se známými životnost a škálování**. Například aplikace, která je požadována pro celý rok a byla naplánované který požadavek.

BYOL pomocí virtuálního počítače s SQL serverem, musí mít licenci pro SQL Server Standard nebo Enterprise a [programu Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), což je požadované možnost přes několik multilicenčních programů a volitelné nákupu s ostatními. Cenová úroveň poskytuje prostřednictvím multilicenčních programů se liší, na základě typu smlouvy a množství a nebo závazku k systému SQL Server. Ale jako říci, používáním vlastní licence za nepřetržité produkční úlohy má následující výhody:

| Výhoda BYOL | Popis |
|-----|-----|
| **Úspora nákladů** | Používáním vlastní licence SQL serveru je nákladově efektivnější než platit podle využití, pokud úloha běží nepřetržitě, SQL Server Standard nebo Enterprise pro *více než 10 měsíců*. |
| **Dlouhodobé úspory** | V průměru je *30 % levnější za rok* k nákupu nebo obnovování licenci systému SQL Server na první 3 roky. Kromě toho po 3 roky, nemusíte už obnovení licence, stačí zaplatit programu Software Assurance. V tomto okamžiku je *200 % levnější*. |
| **Bezplatné pasivní sekundární repliky** | Další výhodou používáním vlastní licence je [zdarma licencování pro jednu pasivní sekundární repliku](https://azure.microsoft.com/pricing/licensing-faq/) za SQL Server potřebujete zajistit vysokou dostupnost. To snižuje v polovině licenční náklady na nasazení vysoce dostupného SQL serveru (například použití skupin dostupnosti Always On). Oprávnění ke spuštění pasivní sekundární jsou k dispozici prostřednictvím převzetí služeb při selhání serverů programu Software Assurance. |

Vytvoření virtuálního počítače Azure s SQL serverem 2017 s jedním z těchto imagí přineste svůj – používání vlastní licence, najdete v tématu virtuálních počítačů s předponou "{BYOL}":

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Dejte nám vědět do 10 dnů kolik licencí SQL serveru, který používáte v Azure. Odkazy na předchozí obrázky jsou pokyny o tom, jak to provést.

> [!NOTE]
> Není možné změnit licenční model platbami za sekundu virtuálního počítače s SQL serverem používat vlastní licenci. V takovém případě musíte vytvořit nový virtuální počítač BYOL a migrovat databáze do nového virtuálního počítače.

## <a name="reduce-costs"></a>Snížení nákladů

Abyste zabránili zbytečné náklady, zvolte pro velikost virtuálního počítače optimální a vezměte v úvahu přerušované vypínání pro nesouvislé úlohy.

### <a id="machinesize"></a> Správně velikost virtuálního počítače

Licenční náklady na SQL Server přímo souvisí s počet virtuálních procesorů. Zvolte velikost virtuálního počítače, který odpovídá vašim potřebám očekávané využití procesoru, paměti, úložiště a šířku pásma vstupně-výstupních operací. Úplný seznam možnosti velikosti počítačů najdete v tématu [velikosti virtuálních počítačů s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) a [velikosti virtuálního počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Existují nové velikosti počítačů, které fungují dobře u určitých typů úloh SQL serveru. Tyto velikosti počítačů udržovat velký objem paměti, úložiště a šířku pásma vstupně-výstupních operací, ale mají nižší počet virtualizované jader. Představte si třeba v následujícím příkladu:

| Velikost virtuálního počítače | vCPU | Memory (Paměť) | Maximální počet disků | Maximální propustnost vstupně-výstupních operací | Náklady na licencování SQL | Celkové náklady (výpočetní + licencování) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 vstupně-výstupních operací nebo 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51 200 vstupně-výstupních operací nebo 768 MB/s | 75 % nižšími | 57 % nižší |

> [!IMPORTANT]
> Toto je příklad bodu v čase. Nejnovější specifikace, najdete v článcích velikostí počítačů a stránce s cenami za Azure [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

V předchozím příkladu vidíte, že specifikace pro **Standard_DS14v2** a **Standard_DS14 4v2** jsou stejné s výjimkou virtuálních procesorů. Přípona **-4v2** na konci **Standard_DS14 4v2** velikost počítače označuje počet aktivních virtuálních procesorů. Protože náklady na licencování SQL serveru jsou svázány se počet virtuálních procesorů, to významně snižuje náklady na virtuální počítač ve scénářích, které nevyžadují další virtuální procesory. Toto je jeden příklad a existuje mnoho velikostí počítačů pomocí virtuální procesory s omezením, které jsou označené v tomto modelu příponu. Další informace naleznete v příspěvku blogu [oznamujeme nové velikosti virtuálních počítačů Azure pro další databáze nákladově efektivní práci](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Vypnutí vašeho virtuálního počítače, pokud je to možné

Pokud používáte jakékoli úlohy, která nepoužívají nepřetržitě, vezměte v úvahu vypnutí virtuálních počítačů během neaktivní období. Platíte jenom za to, co používáte.

Například pokud jsou jednoduše vyzkoušet SQL Server na Virtuálním počítači Azure, nebude chcete účtovat poplatky podle omylem se v něm spuštěná týdny. Jedním řešením je použít [funkci automatického vypínání](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Autoshutdown virtuálního počítače SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatické vypnutí je součástí větší sady podobné funkce poskytované službou [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Další pracovní postupy, zvažte možnost Automatické vypnutí a restartování virtuálních počítačů Azure pomocí skriptování řešení, jako například [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Vypíná a rušení přidělení virtuálního počítače je jediný způsob, jak se vyhnout poplatkům. Jednoduše zastavení nebo pomocí možnosti vypnutí virtuálního počítače stále neúčtují poplatky za využití.

## <a name="next-steps"></a>Další kroky

Obecné Azure ceny pokyny najdete v tématu [jak zabránit neočekávaným nákladům se správou nákladů a fakturací Azure](../../../billing/billing-getting-started.md). Nejnovější virtuální počítače cen, včetně SQL serveru, najdete v článku stránce s cenami za Azure virtuální počítač Azure [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a [virtuální počítače s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Přehled systému SQL Server provozovaný v Azure Virtual Machines najdete v následujících článcích:

- [Přehled SQL serveru na virtuálních počítačích s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Přehled SQL serveru na virtuálních počítačích s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md)