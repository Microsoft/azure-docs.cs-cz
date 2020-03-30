---
title: Cenové poradenství & náklady na řízení
description: Poskytuje osvědčené postupy pro výběr správného modelu stanovení cen virtuálních strojů sql serveru.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b7a8f7136d0899ab3afe04bce2d25bc2e7f8d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981714"
---
# <a name="pricing-guidance-for-azure-sql-server-vms"></a>Doprovodné materiály k cenám pro virtuální počítače Azure s SQL Serverem

Tento článek obsahuje pokyny k cenám pro [virtuální počítače SQL Serveru](virtual-machines-windows-sql-server-iaas-overview.md) v Azure. Existuje několik možností, které ovlivňují náklady, a je důležité vybrat správný obrázek, který vyvažuje náklady s obchodními požadavky.

> [!TIP]
> Pokud potřebujete zjistit pouze odhad nákladů pro určitou kombinaci edice SQL Serveru a velikosti virtuálního počítače, podívejte se na stránku s cenami pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Vyberte svou platformu a edici SQL Serveru ze seznamu **operačních sů/softwaru.**
>
> ![UI na stránce Ceny virtuálního počítače](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Nebo použijte [cenovou kalkulačku](https://azure.microsoft.com/pricing/#explore-cost) k přidání a konfiguraci virtuálního počítače. 

## <a name="free-licensed-sql-server-editions"></a>Edice SQL Serveru s licencí s licencí

Pokud chcete vyvíjet, testovat nebo vytvářet doklad o konceptu, použijte volně licencovanou **edici SQL Server Developer Edition**. Tato edice obsahuje všechny funkce edice SQL Server Enterprise, což umožňuje vytvářet a testovat jakýkoli typ aplikace. V produkčním prostředí však nelze spustit edici vývojáře. Sql Server Developer edition virtuální hod uvolní poplatky za náklady na virtuální ms, protože neexistují žádné související náklady na licencování SQL Server.

Pokud chcete spustit zjednodušené zatížení v produkčním prostředí (<4 jádra, <paměti 1 GB, <10 GB/databáze), použijte volně licencovanou **edici SQL Server Express**. Sql Server Express vydání virtuálního účtu také účtuje poplatky za náklady na virtuální ms.

Pro tyto vývojové a testovací úlohy a zjednodušené produkční úlohy můžete také ušetřit peníze výběrem menší velikosti virtuálního počítače, která odpovídá těmto úlohám. DS1v2 může být dobrou volbou v některých scénářích.

Pokud chcete vytvořit virtuální počítač Azure sql serveru 2017 s jednou z těchto bitových kopií, podívejte se na následující odkazy:

| Platforma | Volně licencované obrázky |
|---|---|
| Windows Server 2016 | [Virtuální počítač Azure pro vývojáře SQL Serveru 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Virtuální počítač SQL Server 2017 Express Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [Virtuální počítač Azure pro vývojáře SQL Serveru 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Virtuální počítač SQL Server 2017 Express Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Virtuální počítač Azure pro vývojáře SQL Serveru 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Virtuální počítač SQL Server 2017 Express Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Virtuální počítač Azure pro vývojáře SQL Serveru 2017](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Virtuální počítač SQL Server 2017 Express Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Placené edice serveru SQL Server

Pokud máte nezjednodušené produkční zatížení, použijte jednu z následujících edic SQL Serveru:

| Edice SERVERU SQL Server | Úloha |
|-----|-----|
| Web | Malé webové stránky |
| Standard | Malé až střední úlohy |
| Enterprise | Velké nebo kritické pracovní zatížení|

Máte dvě možnosti platby za licencování serveru SQL Server pro tyto edice: *platba za použití* nebo *vlastní licence (BYOL)*.

## <a name="pay-per-usage"></a>Platba za použití

**Placení licence SQL Serveru za použití** (označované také jako **průběžně platit**) znamená, že náklady na spuštění virtuálního počítače Azure za sekundu zahrnují náklady na licenci SQL Serveru. Ceny pro různé edice SQL Serveru (Web, Standard, Enterprise) najdete na stránce s cenami virtuálních zařízení Azure pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Náklady jsou stejné pro všechny verze SQL Serveru (2012 SP3 až 2017). Náklady na licencování za sekundu závisí na počtu virtuálních procesorů virtuálních zařízení virtuálních zařízení.

Platba licencí serveru SQL Server na využití se doporučuje pro:

- **Dočasné nebo periodické úlohy**. Například aplikace, která potřebuje podporovat událost na několik měsíců každý rok, nebo obchodní analýzy v pondělí.

- **Úlohy s neznámou životností nebo škálování**. Například aplikace, která nemusí být vyžadována během několika měsíců nebo která může vyžadovat více nebo méně výpočetního výkonu, v závislosti na poptávce.

Pokud chcete vytvořit virtuální počítač Azure sql serveru 2017 s jednou z těchto bitových kopií s průběžnými platbami, podívejte se na následující odkazy:

| Platforma | Licencované obrázky |
|---|---|
| Windows Server 2016 | [Virtuální počítač SQL Server 2017 Pro web Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Virtuální počítač Azure se standardním SQL Serverem 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Virtuální počítač SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [Virtuální počítač SQL Server 2017 Pro web Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Virtuální počítač Azure se standardním SQL Serverem 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Virtuální počítač SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Virtuální počítač SQL Server 2017 Pro web Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Virtuální počítač Azure se standardním SQL Serverem 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Virtuální počítač SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Virtuální počítač SQL Server 2017 Pro web Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Virtuální počítač Azure se standardním SQL Serverem 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Virtuální počítač SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Při vytváření virtuálního počítače SQL Server na portálu, **vyberte velikost** okno zobrazuje odhadované náklady. Je důležité si uvědomit, že tento odhad je pouze výpočetní náklady pro spuštění virtuálního počítače spolu s náklady na licencování operačního systému (Windows nebo operační systémy Linux třetích stran).
>
> ![Zvolit okno velikosti virtuálního počítače](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Nezahrnuje další licenční náklady serveru SQL Server pro webové, standardní a podnikové edice. Chcete-li získat co nejpřesnější odhad cen, vyberte operační systém a edici SQL Server na stránce s cenami pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) nebo [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> Nyní je možné změnit licenční model z pay-per-usage přinést vlastní licenci (BYOL) a zpět. Další informace naleznete [v tématu Jak změnit model licencování pro virtuální virtuální modul SQL](virtual-machines-windows-sql-ahb.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a>Přineste si vlastní licenci (BYOL)

**Přivedení vlastní licence sql serveru prostřednictvím mobility licencí**, označované také jako **BYOL**, znamená použití existující multilicence SQL Serveru s programem Software Assurance ve virtuálním počítači Azure. Virtuální modul SQL Server, který používá BYOL, se účtuje pouze za náklady na spuštění virtuálního počítače, nikoli za licencování serveru SQL Server, vzhledem k tomu, že jste již získali licence a program Software Assurance prostřednictvím multilicenčního programu.

> [!IMPORTANT]
> Bitové kopie BYOL vyžadují smlouvu Enterprise Agreement with Software Assurance. V současné době nejsou k dispozici jako součást partnera cloudových řešení Azure (CSP). Zákazníci CSP si můžou přinést vlastní licenci nasazením bitové kopie s průběžnými platbami a povolením [hybridní výhody Azure](virtual-machines-windows-sql-ahb.md).

> [!NOTE]
> Bitové kopie BYOL jsou v současné době k dispozici pouze pro virtuální počítače s Windows. Sql Server však můžete ručně nainstalovat na virtuální počítač pouze s Linuxem. Přečtěte si pokyny v [nejčastějších dotazech na linuxové SQL VM](../../linux/sql/sql-server-linux-faq.md).

Uvedení vlastních licencí SQL prostřednictvím mobility licencí se doporučuje pro:

- **Průběžné úlohy**. Například aplikace, která potřebuje podporovat obchodní operace 24 hodin denně, 7 dní v pořádku.

- **Úlohy se známou životností a škálování**. Například aplikace, která je vyžadována pro celý rok a která poptávka byla předpovězena.

Chcete-li používat BYOL s virtuálním počítačem SQL Server, musíte mít licenci pro SQL Server Standard nebo Enterprise a [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), což je požadovaná možnost prostřednictvím některých multilicenčních programů a volitelného nákupu s ostatními. Cenová úroveň poskytovaná prostřednictvím multilicenčních programů se liší v závislosti na typu smlouvy a množství a nebo závazku k serveru SQL Server. Ale jako pravidlo, přináší vlastní licenci pro kontinuální produkční úlohy má následující výhody:

| BYOL prospěch | Popis |
|-----|-----|
| **Úspora nákladů** | [Hybridní výhoda Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) nabízí až 55% úsporu. Další informace naleznete v tématu [Switch licensing model](virtual-machines-windows-sql-ahb.md) |
| **Bezplatná pasivní sekundární replika** | Další výhodou přináší vlastní licenci je [bezplatné licencování pro jednu pasivní sekundární repliku](https://azure.microsoft.com/pricing/licensing-faq/) na SQL Server pro účely vysoké dostupnosti. To snižuje v polovině licenční náklady na vysoce dostupné nasazení SERVERU SQL Server (například pomocí vždy na dostupnost skupiny). Práva ke spuštění pasivní sekundární jsou poskytovány prostřednictvím fail-over servers software assurance výhodu. |

Pokud chcete vytvořit virtuální počítač Azure sql serveru 2017 s jednou z těchto ibi přineste vlastní licenci, přečtěte si další virtuální počítače s předponou "{BYOL}":

- [Virtuální počítač SQL Server 2017 Enterprise Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [Virtuální počítač Azure se standardním SQL Serverem 2017](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Dejte nám vědět do 10 dnů, kolik licencí SQL Serveru používáte v Azure. Odkazy na předchozí obrázky mají pokyny, jak to udělat.

> [!NOTE]
> Nyní je možné změnit licenční model z pay-per-usage přinést vlastní licenci (BYOL) a zpět. Další informace naleznete [v tématu Jak změnit model licencování pro virtuální virtuální modul SQL](virtual-machines-windows-sql-ahb.md). 



## <a name="reduce-costs"></a>Snížení nákladů

Chcete-li se vyhnout zbytečným nákladům, zvolte optimální velikost virtuálního počítače a zvažte přerušované vypnutí pro nesouvislé úlohy.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a>Správná velikost virtuálního počítače

Náklady na licencování sql serveru přímo souvisí s počtem virtuálních procesorů. Zvolte velikost virtuálního počítače, která odpovídá vašim očekávaným potřebám pro procesor, paměť, úložiště a šířku pásma vstupně-up. Úplný seznam možností velikosti počítače najdete v tématu [velikosti virtuálních zařízení windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) a [velikosti virtuálních zařízení v Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Existují nové velikosti počítačů, které dobře fungují s určitými typy úloh serveru SQL Server. Tyto velikosti počítačů udržují vysokou úroveň paměti, úložiště a šířky pásma vstupně-v., ale mají nižší počet virtualizovaných jader. Zvažte například následující příklad:

| Velikost virtuálního počítače | Virtuální procesory | Memory (Paměť) | Maximální počet disků | Maximální vstupně-tonás | Licenční náklady SQL | Celkové náklady (výpočetní výkon + licencování) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51 200 vstupně-výstupních operací nebo 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51 200 vstupně-výstupních operací nebo 768 MB/s | O 75 % nižší | O 57 % nižší |

> [!IMPORTANT]
> Toto je příklad bodu v čase. Nejnovější specifikace najdete v článcích velikosti počítače a na stránce s cenami Azure pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

V předchozím příkladu uvidíte, že specifikace pro **Standard_DS14v2** a **Standard_DS14-4v2** jsou identické s výjimkou virtuálních procesorů. Přípona **-4v2** na konci velikosti počítače **Standard_DS14-4v2** označuje počet aktivních virtuálních procesorů. Vzhledem k tomu, že náklady na licencování serveru SQL Server jsou vázány na počet virtuálních procesorů, výrazně to snižuje náklady na virtuální ms ve scénářích, kde nejsou potřeba další virtuální procesory. Toto je jeden příklad a existuje mnoho velikostí počítače s omezenými virtuálními procesory, které jsou identifikovány s tímto vzorem přípony. Další informace najdete v příspěvku blogu [Oznámení nových velikostí virtuálních virtuálních počítačů Azure pro nákladově efektivnější databázovou práci](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Vypnutí virtuálního počítače, pokud je to možné

Pokud používáte všechny úlohy, které neběží nepřetržitě, zvažte vypnutí virtuálního počítače během neaktivních období. Platíte jenom za to, co používáte.

Například pokud jste jednoduše vyzkoušet SQL Server na virtuálním počítači Azure, nebudete chtít vynaložit poplatky tím, že omylem ponechat spuštěné týdny. Jedním z řešení je použití [funkce automatického vypnutí](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Automatické vypnutí virtuálního počítače SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Automatické vypnutí je součástí větší sady podobných funkcí poskytovaných [laboratoří Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

U jiných pracovních postupů zvažte automatické vypnutí a restartování virtuálních počítačích Azure pomocí skriptovacího řešení, jako je [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Vypnutí a zrušení přidělení virtuálního počítače je jediný způsob, jak se vyhnout poplatkům. Jednoduše zastavení nebo použití možností napájení k vypnutí virtuálního počítače stále účtuje poplatky za využití.

## <a name="next-steps"></a>Další kroky

Obecné pokyny k cenám Azure najdete v [tématu Prevence neočekávaných nákladů pomocí fakturace azure a správy nákladů](../../../cost-management-billing/manage/getting-started.md). Nejnovější ceny virtuálních počítačů, včetně SQL Serveru, najdete na stránce s cenami virtuálních počítačů Azure azure pro [virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) a virtuální počítače s [Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Přehled SQL Serveru spuštěného na virtuálních počítačích Azure najdete v následujících článcích:

- [Přehled SQL Serveru na virtuálních počítačích se systémem Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Přehled SQL Serveru na virtuálních počítačích s Linuxem](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
