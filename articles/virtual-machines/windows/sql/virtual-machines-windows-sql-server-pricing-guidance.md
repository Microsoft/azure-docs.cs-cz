---
title: Efektivní správě náklady pro SQL Server na virtuálních počítačích Azure | Microsoft Docs
description: Obsahuje doporučené postupy pro výběr správné virtuálního počítače systému SQL Server cenový model.
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
ms.date: 05/02/2018
ms.author: jroth
ms.openlocfilehash: 71c86af9d4dcdf1026b4f539574b9932ef1cfc89
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32767796"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Pokyny pro virtuální počítače SQL serveru Azure – ceny

Tento článek obsahuje pokyny, cenovou [virtuální počítače systému SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) v Azure. Existuje několik možností, které ovlivňují náklady a je důležité vybrat správné bitové kopie, který vyrovnává náklady obchodním požadavkům.

> [!TIP]
> Pokud potřebujete zjistit odhad náklady pro konkrétní kombinaci edici systému SQL Server a velikost virtuálního počítače, najdete na stránce s cenami pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Vyberte platformu a edici systému SQL Server z **operační systém a Software** seznamu.
>
> ![Uživatelské rozhraní na stránce ceny virtuálních počítačů](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Nebo použijte [cenové kalkulačky](https://azure.microsoft.com/pricing/#explore-cost) k přidání a konfigurace virtuálního počítače. 

## <a name="free-licensed-sql-server-editions"></a>Uvolněte licenci edicích systému SQL Server

Pokud chcete vývoj, testování nebo sestavení testování konceptu, použijte volně licencovanou **SQL Server Developer edition**. Tato edice obsahuje všechny funkce SQL Server Enterprise edition, což umožňuje sestavení a testů jakéhokoli typu aplikace. Developer edition však nelze spustit v produkčním prostředí. SQL Server Developer edition virtuálních počítačů pouze budou vám účtovány poplatky nákladů na virtuální počítač, protože nejsou k dispozici žádné přidružené k serveru SQL, náklady na licencování.

Pokud chcete spustit prosté úlohy v produkčním prostředí (< 4 jádra, < 1 GB paměti, < 10 GB/databáze), použijte volně licencovanou **edice systému SQL Server Express**. Edice systému SQL Server Express virtuální počítač také pouze budou vám účtovány poplatky nákladů na virtuální počítač.

Pro tyto vývoj/testování a lightweight produkčním prostředí můžete také šetřit peníze a vybrat menší velikost virtuálního počítače, který odpovídá těchto úloh. DS1v2 může být vhodné v některých scénářích.

Pokud chcete vytvořit virtuální počítač Azure SQL Server 2017 s jedním z těchto bitových kopií, v následujících tématech:

| Platforma | Volně licencovanou Image |
|---|---|
| Windows Server 2016 | [SQL Server 2017 vývojáře Azure virtuálních počítačů](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express virtuálních počítačů Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 vývojáře Azure virtuálních počítačů](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express virtuálních počítačů Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 vývojáře Azure virtuálních počítačů](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express virtuálních počítačů Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 vývojáře Azure virtuálních počítačů](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express virtuálních počítačů Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Placených edicích systému SQL Server

Pokud máte jiný lightweight produkční zatížení, použijte jednu z následujících edicích systému SQL Server:

| Edice systému SQL Server | Úloha |
|-----|-----|
| Web | Malé weby |
| Standard | Malé a střední zatížení |
| Enterprise | Velké soubory nebo důležité úlohy|

Máte dvě možnosti platit za licencování SQL serveru pro tyto edice: *platíte za použití* nebo *přineste si vlastní licenci (BYOL)*.

## <a name="pay-per-usage"></a>Platba za použití

**Licenci systému SQL Server za použití platícího** znamená, že za sekundu náklady na provozování virtuálního počítače Azure obsahuje náklady na licence pro SQL Server. Můžete zobrazit ceny pro různých edicích systému SQL Server (Web, Standard, Enterprise) ve virtuálním počítači Azure pro stránce s cenami [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Náklady na je stejný pro všechny verze systému SQL Server (2012 SP3 na 2017). Náklady na licencování za sekundu závisí na počtu jader virtuálního počítače, což je standard pro všechny licencování SQL serveru.

Platícího systému SQL Server licencování za použití se doporučuje pro:

- **Dočasné nebo pravidelné úlohy**. Například aplikace, která musí podporovat událost pro několik měsíců každý rok nebo obchodní analýzy v pondělí.

- **Úlohy s neznámou životnost nebo škálování**. Například aplikace, nemusí být nutné několik měsíců, nebo které může být více nebo méně výpočetního výkonu, v závislosti na vyžádání.

Pokud chcete vytvořit virtuální počítač Azure SQL Server 2017 s jedním z těchto bitových kopií platím za použití, v následujících tématech:

| Platforma | Licencovanou Image |
|---|---|
| Windows Server 2016 | [SQL Server virtuálního počítače Azure webové 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server virtuálního počítače Azure Enterprise 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server virtuálního počítače Azure webové 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server virtuálního počítače Azure Enterprise 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server virtuálního počítače Azure webové 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server virtuálního počítače Azure Enterprise 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server virtuálního počítače Azure webové 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server virtuálního počítače Azure Enterprise 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Při vytváření virtuálního počítače s SQL serverem na portálu **zvolte velikost** v okně se zobrazí odhadované náklady. Je důležité si uvědomit, že je tento odhad výpočetní náklady pro spuštění virtuálního počítače společně s žádné OS licenční náklady (Windows nebo operačních systémů Linux třetích stran).
>
> ![Vyberte virtuální počítač velikost okna](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nezahrnuje dodatečné licenční náklady pro webové, Standard a Enterprise systému SQL Server. Chcete-li získat nejpřesnější cenovou odhad, vyberte operační systém a edice systému SQL Server na na stránce s cenami pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="bring-your-own-license-byol"></a>Používání vlastní licence (BYOL)

**Přináší vlastní licenci na SQL Server prostřednictvím mobilita licencí**, také nazývaný jako **BYOL**, znamená existující Volume License serveru SQL pomocí programu Software Assurance virtuální počítač Azure. Virtuální počítač SQL Server pomocí BYOL pouze poplatky pro náklady na provozování virtuálního počítače, ne pro licencování SQL serveru, vzhledem k tomu, že jste už získali licence a programu Software Assurance prostřednictvím multilicenčního programu.

> [!NOTE]
> BYOL bitové kopie jsou momentálně dostupné jenom pro virtuální počítače s Windows. Však můžete ručně nainstalovat SQL Server na virtuálním počítači pouze Linux. Viz pokyny v [Linux SQL virtuálního počítače – nejčastější dotazy](../../linux/sql/sql-server-linux-faq.md).

Přináší vlastní SQL licencování prostřednictvím mobilita licencí se doporučuje pro:

- **Nepřetržité úlohy**. Například aplikace, která musí podporovat podnikové operace 24 x 7.

- **Úlohy s známé životnost a škálování**. Například aplikace, která je nezbytné pro celý rok a který požadavek má byla naplánované.

Používat BYOL virtuálního počítače s SQL Server, musí mít licenci pro SQL Server Standard nebo Enterprise a [programu Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), což je požadovaná možnost prostřednictvím některé multilicenčních programů a volitelný nákup s ostatními. Cenová úroveň poskytované prostřednictvím multilicenčních programů se liší, na základě typu smlouvy a množství a nebo závazku k systému SQL Server. Ale jako existuje pravidlo, přináší vlastní licenci pro produkční nepřetržité úlohy má následující výhody:

| BYOL výhody | Popis |
|-----|-----|
| **Úspora nákladů** | Přináší vlastní licenci na SQL Server je nákladově efektivnější než platícího za použití, pokud úloha běží nepřetržitě, SQL Server Standard nebo Enterprise pro *více než 10 měsíců*. |
| **Dlouhodobé úspory** | V průměru je *30 % levnější za jeden rok.* koupit nebo obnovit licenci SQL serveru pro první 3 roky. Kromě toho po 3 roky, nemusíte už obnovit licence, platit pouze pro Software Assurance. V tomto okamžiku je *200 % levnější*. |
| **Volné pasivní sekundární repliky** | Další výhodou přinesou vlastní licence je [volné licencování pro jeden pasivní sekundární repliky](https://azure.microsoft.com/pricing/licensing-faq/) na Server SQL pro vysokou dostupnost pro účely. To snižuje v poloviční licenční náklady na vysoce dostupné nasazení systému SQL Server (například použití skupin dostupnosti Always On). Práva pro spuštění pasivní sekundární jsou k dispozici prostřednictvím výhody Software Assurance servery převzetí služeb při selhání. |

Vytvoření virtuálního počítače s SQL serverem 2017 Azure s jedním z těchto přineste si vlastníte license bitových kopií naleznete v tématu předponu "{BYOL}" virtuální počítače:

- [SQL Server virtuálního počítače Azure Enterprise 2017](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 standardní virtuální počítač Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Dejte nám vědět do 10 dnů kolik licencí systému SQL Server používáte v Azure. Odkazy na předchozí bitové kopie jsou pokyny o tom, jak to udělat.

> [!NOTE]
> Není možné změnit model licencování platím za sekundu virtuální počítač SQL Server používat vlastní licenci. V takovém případě musíte vytvořit nový virtuální počítač BYOL a migrovat databáze do nového virtuálního počítače.

## <a name="reduce-costs"></a>Snížení nákladů

Abyste se vyhnuli zbytečných nákladů, zvolte velikost optimální virtuálního počítače a zvažte přerušované vypnutí systému pro nesouvislé úlohy.

### <a id="machinesize"></a> Správně velikost virtuálního počítače

Náklady na licencování SQL serveru přímo souvisí s počet jader. Zvolte velikost virtuálního počítače, který odpovídá vašim potřebám očekávané pro využití procesoru, paměti, úložiště a vstupně-výstupní šířky. Úplný seznam možnosti velikosti počítačů najdete v tématu [velikosti virtuálních počítačů Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) a [velikosti virtuálního počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Existují nové velikosti počítačů, které funkce fungují dobře u určitých typů úloh SQL serveru. Tyto počítače velikosti udržování vysoké úrovně paměť, úložiště a vstupně-výstupní šířky pásma, ale mají nižší počet virtualizované jader. Představte si třeba v následujícím příkladu:

| Velikost virtuálního počítače | vCPU | Memory (Paměť) | Maximální počet disků | Maximální propustnost vstupu/výstupu | Náklady na licencování SQL | Celkové náklady (výpočetní + licencování) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 IOPS nebo 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51 200 IOPS nebo 768 MB/s | 75 % nižší | 57 % nižší |

> [!IMPORTANT]
> Toto je příklad v daném okamžiku. Nejnovější specifikace, naleznete v článcích velikost počítače a Azure pro stránce s cenami [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

V předchozím příkladu můžete uvidíte, že specifikace pro **Standard_DS14v2** a **Standard_DS14 4v2** jsou identické s výjimkou Vcpu. Přípona **-4v2** na konci **Standard_DS14 4v2** velikost počítače udává počet active Vcpu. Vzhledem k tomu, že náklady na licencování SQL serveru, jsou svázané s počet jader, to významně snižuje náklady na virtuální počítač ve scénářích, kde nejsou potřebné navíc Vcpu. Toto je jedním z příkladů a existuje mnoho velikosti počítačů s omezené Vcpu, které jsou označeny tento vzor příponu. Další informace naleznete v příspěvku blogu [uvedení nové velikosti virtuálního počítače Azure pro další databáze nákladově efektivní práci](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Vypnout váš virtuální počítač, pokud je to možné

Pokud používáte jakékoli úlohy, které nepoužívají nepřetržitě, vezměte v úvahu vypínání virtuálního počítače během období neaktivní. Platíte jenom za to, co používáte.

Pokud zkoušíte jednoduše na SQL Server na virtuální počítač Azure, by například chtít platit poplatky omylem ponecháte systémem týdny. Jeden řešením je použití [funkce automatického ukončení](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Virtuální počítač SQL autoshutdown](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatické ukončení je součástí větší sady podobné funkce poskytované službou [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Pro jiné pracovní postupy, zvažte automaticky vypínání a restartování virtuálních počítačů Azure v rámci řešení pro skriptování, například [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Vypíná a rušení přidělení virtuálního počítače je jedinou možností, aby se zabránilo poplatky. Jednoduše zastavení nebo pomocí možnosti napájení vypnout virtuální počítač stále způsobuje poplatky za používání.

## <a name="next-steps"></a>Další postup

Obecné Azure ceny pokyny najdete v tématu [zabránit neočekávané náklady s Azure fakturace a náklady na správu](../../../billing/billing-getting-started.md). Nejnovější virtuální počítače ceny, včetně systému SQL Server, najdete v tématu Azure virtuálních počítačů Azure pro stránce s cenami [virtuálních počítačů Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a [virtuální počítače s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Přehled systému SQL Server běžící na virtuálních počítačích Azure najdete v následujících článcích:

- [Přehled systému SQL Server na virtuálních počítačích Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Přehled systému SQL Server na virtuální počítače s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md)