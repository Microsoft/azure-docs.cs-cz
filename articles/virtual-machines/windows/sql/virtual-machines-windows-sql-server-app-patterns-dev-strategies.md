---
title: Vzory aplikací serveru SQL Server na virtuálních počítačích | Dokumenty společnosti Microsoft
description: Tento článek popisuje vzory aplikací pro SQL Server na virtuálních počítačích Azure. Poskytuje architektům a vývojářům řešení základ pro dobrou architekturu a design aplikací.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: cbc2bfbb68910c3eb12352bebb575c4548885a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70124025"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Souhrn:
Určení, který vzor aplikace nebo vzory, které mají být používány pro vaše aplikace založené na serveru SQL-Server v prostředí Azure, je důležité rozhodnutí o návrhu a vyžaduje solidní pochopení toho, jak SQL Server a jednotlivé součásti infrastruktury Azure spolupracují. Pomocí SQL Serveru ve službách Infrastruktury Azure můžete snadno migrovat, udržovat a monitorovat stávající aplikace SQL Serveru integrované s Windows Serverem do virtuálních počítačů v Azure.

Cílem tohoto článku je poskytnout architektům a vývojářům řešení základ pro dobrou architekturu a návrh aplikací, které mohou sledovat při migraci stávajících aplikací do Azure a při vývoji nových aplikací v Azure.

Pro každý vzor aplikace najdete místní scénář, jeho příslušné řešení s podporou cloudu a související technická doporučení. Kromě toho článek popisuje strategie vývoje specifické pro Azure, takže můžete navrhnout aplikace správně. Vzhledem k mnoha možným vzorům aplikací se doporučuje, aby architekti a vývojáři zvolili nejvhodnější vzor pro své aplikace a uživatele.

**Techničtí přispěvatelé:** Luis Carlos Vargas Sleď, Madhan Arumugam Ramakrishnan

**Techničtí recenzenti:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Úvod
Můžete vyvinout mnoho typů n-vrstvých aplikací oddělením součástí různých aplikačních vrstev na různých počítačích i v samostatných součástech. Můžete například umístit součásti klientských aplikací a obchodních pravidel do jednoho počítače, front-endové webové vrstvy a komponenty vrstvy přístupu k datům v jiném počítači a databázovou vrstvu back-end u jiného počítače. Tento druh strukturování pomáhá izolovat každou úroveň od sebe. Pokud změníte, odkud data pocházejí, nemusíte měnit klientskou nebo webovou aplikaci, ale pouze součásti vrstvy přístupu k datům.

Typická *n-vrstvá* aplikace zahrnuje prezentační vrstvu, obchodní úroveň a datovou vrstvu:

| Úroveň | Popis |
| --- | --- |
| **Zobrazení** |*Prezentační vrstva* (webová úroveň, front-endová vrstva) je vrstva, ve které uživatelé interagují s aplikací. |
| **Firemní** |*Obchodní vrstva* (střední vrstva) je vrstva, kterou prezentační vrstva a datová vrstva používají ke vzájemné komunikaci a zahrnuje základní funkce systému. |
| **Data** |*Datová vrstva* je v podstatě server, který ukládá data aplikace (například server se systémem SQL Server). |

Vrstvy aplikací popisují logické seskupení funkcí a součástí v aplikaci; vzhledem k tomu, že vrstvy popisují fyzické rozdělení funkcí a součástí na samostatných fyzických serverech, počítačích, sítích nebo vzdálených umístěních. Vrstvy aplikace mohou být umístěny ve stejném fyzickém počítači (stejné vrstvě) nebo mohou být distribuovány v samostatných počítačích (n-vrstva) a součásti v každé vrstvě komunikují s komponentami v jiných vrstvách prostřednictvím dobře definovaných rozhraní. Můžete si myslet, že úroveň termínu odkazuje na fyzické distribuční vzory, jako jsou dvouvrstvé, třívrstvé a n-vrstvé. **Dvouvrstvý aplikační vzor** obsahuje dvě vrstvy aplikace: aplikační server a databázový server. Přímá komunikace probíhá mezi aplikačním serverem a databázovým serverem. Aplikační server obsahuje součásti webové i podnikové vrstvy. Ve **třívrstvém aplikačním vzoru**existují tři vrstvy aplikace: webový server, aplikační server, který obsahuje součásti přístupu k datům obchodní logiky nebo obchodní vrstvy, a databázový server. Komunikace mezi webovým serverem a databázovým serverem probíhá přes aplikační server. Podrobné informace o vrstvách aplikací a úrovních naleznete v [příručce Microsoft Application Architecture Guide](https://msdn.microsoft.com/library/ff650706.aspx).

Než začnete číst tento článek, měli byste mít znalosti o základní koncepty SQL Server a Azure. Další informace naleznete v [tématech SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), SQL Server ve [virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md) a [Azure.com](https://azure.microsoft.com/).

Tento článek popisuje několik vzorů aplikací, které mohou být vhodné pro jednoduché aplikace, stejně jako velmi složité podnikové aplikace. Než začnete podrobně popisovat jednotlivé vzorky, doporučujeme, abyste se seznámili s dostupnými službami úložiště dat v Azure, jako je [Azure Storage](../../../storage/common/storage-introduction.md), Azure [SQL Database](../../../sql-database/sql-database-technical-overview.md)a SQL Server [ve virtuálním počítači Azure](virtual-machines-windows-sql-server-iaas-overview.md). Chcete-li učinit nejlepší rozhodnutí o návrhu pro vaše aplikace, zjistěte, kdy jasně používat kterou službu pro ukládání dat.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Zvolte SQL Server ve virtuálním počítači Azure, když:
* Potřebujete kontrolu na SQL Server a Windows. Může se jednat například o verzi serveru SQL Server, speciální opravy hotfix, konfiguraci výkonu atd.
* Potřebujete plnou kompatibilitu s SQL Server místně a chcete přesunout existující aplikace do Azure tak, jak je.
* Chcete využít možnosti prostředí Azure, ale Azure SQL Database nepodporuje všechny funkce, které vaše aplikace vyžaduje. To by mohlo zahrnovat tyto oblasti:
  
  * **Velikost databáze**: V době, kdy byl tento článek aktualizován, SQL Database podporuje databázi až 1 TB dat. Pokud vaše aplikace vyžaduje více než 1 TB dat a nechcete implementovat vlastní řešení pro syně, doporučujeme použít SQL Server ve virtuálním počítači Azure. Nejnovější informace najdete v [tématu škálování mimo Azure SQL Database](https://msdn.microsoft.com/library/azure/dn495641.aspx), Model [nákupu na základě DTU](../../../sql-database/sql-database-service-tiers-dtu.md)a [nákupní model založený na virtuálních jádrech](../../../sql-database/sql-database-service-tiers-vcore.md)(preview).
  * **Kompatibilita s HIPAA:** Zákazníci ve zdravotnictví a nezávislí dodavatelé softwaru (ISV) si mohou vybrat [SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md) místo Azure SQL [Database,](../../../sql-database/sql-database-technical-overview.md) protože SQL Server ve virtuálním počítači Azure se vztahuje na smlouvu HIPAA Business Associate Agreement (BAA). Informace o dodržování předpisů najdete v [tématu Microsoft Azure Trust Center: Compliance](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funkce na úrovni instance**: V tuto chvíli databáze SQL nepodporuje funkce, které žijí mimo databázi (například propojené servery, úlohy agenta, FileStream, Service Broker atd.). Další informace naleznete v [tématu Pokyny a omezení databáze Azure SQL](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1vrstvý (jednoduchý): jeden virtuální počítač
V tomto vzoru aplikace nasadíte aplikaci a databázi SQL Serveru do samostatného virtuálního počítače v Azure. Stejný virtuální počítač obsahuje klientskou/webovou aplikaci, obchodní komponenty, vrstvu pro přístup k datům a databázový server. Kód pro prezentaci, firmu a přístup k datům je logicky oddělen, ale fyzicky se nachází v počítači s jedním serverem. Většina zákazníků začíná s tímto vzorem aplikace a pak se škálují přidáním dalších webových rolí nebo virtuálních počítačů do svého systému.

Tento vzor aplikace je užitečný, když:

* Chcete provést jednoduchou migraci na platformu Azure a vyhodnotit, jestli platforma odpovídá požadavkům vaší aplikace nebo ne.
* Chcete zachovat všechny aplikační vrstvy hostované ve stejném virtuálním počítači ve stejném datovém centru Azure, abyste snížili latenci mezi vrstvami.
* Chcete rychle zřídit vývojová a testovací prostředí na krátkou dobu.
* Chcete provádět zátěžové testování pro různé úrovně pracovního vytížení, ale zároveň nechcete vlastnit a udržovat mnoho fyzických počítačů po celou dobu.

Následující diagram ukazuje jednoduchý místní scénář a jak můžete nasadit jeho cloudové řešení v jednom virtuálním počítači v Azure.

![Vzor aplikace 1 vrstvy](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Nasazení obchodní vrstvy (obchodní logiky a komponent přístupu k datům) na stejné fyzické vrstvě jako prezentační vrstva může maximalizovat výkon aplikace, pokud není nutné použít samostatnou vrstvu z důvodu škálovatelnosti nebo problémů se zabezpečením.

Vzhledem k tomu, že se jedná o velmi běžný vzor, můžete najít následující článek o migraci užitečný pro přesun dat do virtuálního počítače SQL Server: [Migrace databáze na SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3vrstvé (jednoduché): více virtuálních počítačů
V tomto vzoru aplikace nasadíte třívrstvou aplikaci v Azure umístěním každé aplikační vrstvy do jiného virtuálního počítače. To poskytuje flexibilní prostředí pro scénáře snadné škálování a škálování na více. Pokud jeden virtuální počítač obsahuje klientskou/webovou aplikaci, druhý hostuje vaše obchodní součásti a druhý je hostitelem databázového serveru.

Tento vzor aplikace je užitečný, když:

* Chcete provést migraci složitých databázových aplikací do virtuálních počítačů Azure.
* Chcete, aby různé aplikační vrstvy hostované v různých oblastech. Můžete mít například sdílené databáze, které jsou nasazeny do více oblastí pro účely vytváření sestav.
* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do virtuálních počítačů Azure. Podrobné informace o podnikových aplikacích naleznete v [tématu Co is a Enterprise Application](https://msdn.microsoft.com/library/aa267045.aspx).
* Chcete rychle zřídit vývojová a testovací prostředí na krátkou dobu.
* Chcete provádět zátěžové testování pro různé úrovně pracovního vytížení, ale zároveň nechcete vlastnit a udržovat mnoho fyzických počítačů po celou dobu.

Následující diagram ukazuje, jak můžete umístit jednoduchou 3vrstvou aplikaci v Azure umístěním každé aplikační vrstvy do jiného virtuálního počítače.

![Třívrstvý aplikační vzor](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

V tomto vzoru aplikace je pouze jeden virtuální počítač (VM) v každé vrstvě. Pokud máte v Azure víc virtuálních počítačů, doporučujeme nastavit virtuální síť. [Virtuální síť Azure](../../../virtual-network/virtual-networks-overview.md) vytvoří důvěryhodnou hranici zabezpečení a také umožňuje virtuálním počítačům komunikovat mezi sebou přes privátní IP adresu. Kromě toho vždy ujistěte, že všechna připojení k Internetu přejít pouze na prezentační vrstvu. Při sledování tohoto vzoru aplikace spravujte pravidla skupiny zabezpečení sítě pro řízení přístupu. Další informace najdete [v tématu Povolení externího přístupu k virtuálnímu počítači pomocí portálu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

V diagramu mohou být internetové protokoly TCP, UDP, HTTP nebo HTTPS.

> [!NOTE]
> Nastavení virtuální sítě v Azure je zdarma. Účtují se vám však za bránu VPN, která se připojuje k místnímu. Tento poplatek je založen na době, po kterou je toto připojení zřízeno a k dispozici.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>Dvouvrstvé a třívrstvé s horizontálním navýšením kapacity prezentační úrovně
V tomto vzoru aplikace nasadíte dvouvrstvou nebo třívrstvou databázovou aplikaci do virtuálních počítačů Azure umístěním každé aplikační vrstvy do jiného virtuálního počítače. Kromě toho můžete horizontální navýšení kapacity prezentační vrstvy z důvodu zvýšení objemu příchozích požadavků klientů.

Tento vzor aplikace je užitečný, když:

* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do virtuálních počítačů Azure.
* Chcete horizontální navýšení kapacity prezentační vrstvy z důvodu zvýšeného objemu příchozích požadavků klientů.
* Chcete rychle zřídit vývojová a testovací prostředí na krátkou dobu.
* Chcete provádět zátěžové testování pro různé úrovně pracovního vytížení, ale zároveň nechcete vlastnit a udržovat mnoho fyzických počítačů po celou dobu.
* Chcete vlastnit prostředí infrastruktury, které lze vertikálně navzestupovat a snížit na vyžádání.

Následující diagram ukazuje, jak můžete umístit aplikační vrstvy ve více virtuálních počítačích v Azure horizontálním navýšením kapacity prezentační vrstvy z důvodu zvýšeného objemu příchozích požadavků klientů. Jak je vidět v diagramu, Azure Balancer je zodpovědný za distribuci provozu napříč více virtuálních počítačů a také určení, který webový server se připojit. S více instancí webových serverů za vyrovnávání zatížení zajišťuje vysokou dostupnost prezentační vrstvy.

![Aplikační vzor – horizontální navýšení kapacity prezentační vrstvy](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Doporučené postupy pro dvouvrstvé, třívrstvé nebo nvrstvé vzory, které mají více virtuálních počítačů v jedné vrstvě
Doporučujeme umístit virtuální počítače, které patří do stejné úrovně ve stejné cloudové službě a ve stejné sadě dostupnosti. Například umístěte sadu webových serverů v **CloudService1** a **AvailabilitySet1** a sadu databázových serverů v **CloudService2** a **AvailabilitySet2**. Sada dostupnosti v Azure umožňuje umístit uzly s vysokou dostupností do samostatných domén selhání a upgradovacích domén.

Chcete-li využít více instancí virtuálních virtuálních počítačů vrstvy, musíte nakonfigurovat Azure Balancer mezi aplikačními vrstvami. Chcete-li nakonfigurovat vyrovnávání zatížení v každé vrstvě, vytvořte koncový bod s vyrovnáváním zatížení na virtuálních počítačích každé vrstvy samostatně. Pro konkrétní úroveň nejprve vytvořte virtuální virtuální připojení ve stejné cloudové službě. Tím je zajištěno, že mají stejnou veřejnou virtuální IP adresu. Dále vytvořte koncový bod na jednom z virtuálních počítačů na této úrovni. Potom přiřaďte stejný koncový bod ostatním virtuálním počítačům na této vrstvě pro vyrovnávání zatížení. Vytvořením sady s vyrovnáváním zatížení distribuujete provoz mezi více virtuálními počítači a také povolíte nástrojpro vyrovnávání zatížení určit, který uzel se má připojit při selhání uzlu back-endového virtuálního počítače. Například s více instancí webových serverů za vyrovnávání zatížení zajišťuje vysokou dostupnost prezentační vrstvy.

Jako osvědčený postup vždy ujistěte se, že všechna připojení k Internetu nejprve přejít na prezentační vrstvu. Prezentační vrstva přistupuje k obchodní vrstvě a pak obchodní vrstva přistupuje k datové vrstvě. Další informace o tom, jak povolit přístup k prezentační vrstvě, najdete v [tématu Povolení externího přístupu k virtuálnímu počítači pomocí portálu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Všimněte si, že vyrovnávání zatížení v Azure funguje podobně jako vyrovnávání zatížení v místním prostředí. Další informace najdete v [tématu Vyrovnávání zatížení pro služby infrastruktury Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Kromě toho doporučujeme nastavit privátní síť pro vaše virtuální počítače pomocí virtuální sítě Azure. To jim umožňuje komunikovat mezi sebou přes privátní IP adresu. Další informace najdete v tématu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>Dvoustupňové a třístupňové s horizontálním navýšením kapacity obchodní úrovně
V tomto vzoru aplikace nasadíte dvouvrstvou nebo třívrstvou databázovou aplikaci do virtuálních počítačů Azure umístěním každé aplikační vrstvy do jiného virtuálního počítače. Kromě toho můžete chtít distribuovat součásti aplikačního serveru do více virtuálních počítačů z důvodu složitosti vaší aplikace.

Tento vzor aplikace je užitečný, když:

* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do virtuálních počítačů Azure.
* Chcete distribuovat součásti aplikačního serveru do více virtuálních počítačů z důvodu složitosti vaší aplikace.
* Chcete přesunout obchodní logiku těžké místní LOB (line-of-business) aplikace do virtuálních počítačů Azure. Lobové aplikace jsou sada důležitých počítačových aplikací, které jsou důležité pro spuštění podniku, jako je účetnictví, lidské zdroje (HR), mzdy, správa dodavatelského řetězce a aplikace pro plánování zdrojů.
* Chcete rychle zřídit vývojová a testovací prostředí na krátkou dobu.
* Chcete provádět zátěžové testování pro různé úrovně pracovního vytížení, ale zároveň nechcete vlastnit a udržovat mnoho fyzických počítačů po celou dobu.
* Chcete vlastnit prostředí infrastruktury, které lze vertikálně navzestupovat a snížit na vyžádání.

Následující diagram znázorňuje místní scénář a jeho cloudové řešení. V tomto scénáři umístíte aplikační vrstvy do více virtuálních počítačů v Azure horizontálním navýšením kapacity obchodní vrstvy, která obsahuje vrstvu obchodní logiky a komponenty pro přístup k datům. Jak je vidět v diagramu, Azure Balancer je zodpovědný za distribuci provozu napříč více virtuálních počítačů a také určení, který webový server se připojit. S více instancí aplikačních serverů za vyrovnávání zatížení zajišťuje vysokou dostupnost obchodní vrstvy. Další informace najdete [v tématu Doporučené postupy pro vzory dvouvrstvých, třívrstvých nebo nvrstvých aplikací, které mají více virtuálních počítačů v jedné vrstvě](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Aplikační vzor s horizontálním navýšením kapacity obchodní úrovně](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>Dvoustupňové a třístupňové s škálovatcí mise a škálování obchodních úrovní a hadr
V tomto vzoru aplikace nasadíte dvouvrstvou nebo třívrstvou databázovou aplikaci do virtuálních počítačů Azure distribucí prezentační vrstvy (webového serveru) a součástí podnikové vrstvy (aplikačního serveru) do více virtuálních počítačů. Kromě toho implementujete řešení s vysokou dostupností a zotavení po havárii pro vaše databáze ve virtuálních počítačích Azure.

Tento vzor aplikace je užitečný, když:

* Chcete přesunout podnikové aplikace z virtualizovaných platforem místně do Azure implementací SQL Serveru s vysokou dostupností a možnostmi zotavení po havárii.
* Chcete horizontální navýšení kapacity prezentační vrstvy a obchodní vrstvy z důvodu zvýšeného objemu příchozích požadavků klientů a složitosti vaší aplikace.
* Chcete rychle zřídit vývojová a testovací prostředí na krátkou dobu.
* Chcete provádět zátěžové testování pro různé úrovně pracovního vytížení, ale zároveň nechcete vlastnit a udržovat mnoho fyzických počítačů po celou dobu.
* Chcete vlastnit prostředí infrastruktury, které lze vertikálně navzestupovat a snížit na vyžádání.

Následující diagram znázorňuje místní scénář a jeho cloudové řešení. V tomto scénáři můžete škálovat prezentační vrstvu a součásti obchodní vrstvy ve více virtuálních počítačích v Azure. Kromě toho implementujete vysoké dostupnosti a zotavení po havárii (HADR) techniky pro SQL Server databází v Azure.

Spuštění více kopií aplikace v různých virtuálních počítačích ujistěte se, že jsou požadavky na vyrovnávání zatížení přes ně. Když máte více virtuálních počítačů, musíte se ujistit, že všechny vaše virtuální počítače jsou přístupné a spuštěné v jednom okamžiku. Pokud nakonfigurujete vyrovnávání zatížení, nástroj Azure Balancer sleduje stav virtuálních počítačích a správně směruje příchozí volání na uzly virtuálních počítačích v pořádku. Informace o tom, jak nastavit vyrovnávání zatížení virtuálních počítačů, najdete v tématu [Vyrovnávání zatížení pro služby infrastruktury Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). S více instancemi webových a aplikačních serverů za vyrovnávání zatížení zajišťuje vysokou dostupnost prezentace a obchodní vrstvy.

![Horizontální navýšení kapacity a vysoká dostupnost](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Doporučené postupy pro vzory aplikací, které vyžadují SQL HADR
Když nastavíte řešení sql serveru s vysokou dostupností a zotavení po havárii ve virtuálních počítačích Azure, nastavení virtuální sítě pro vaše virtuální počítače pomocí [virtuální sítě Azure](../../../virtual-network/virtual-networks-overview.md) je povinné.  Virtuální počítače v rámci virtuální sítě budou mít stabilní privátní IP adresu i po prostojce služby, takže se můžete vyhnout době aktualizace potřebné pro překlad názvů DNS. Kromě toho virtuální síť umožňuje rozšířit místní síť do Azure a vytvoří důvěryhodné hranice zabezpečení. Pokud má například vaše aplikace omezení podnikové domény (například ověřování systému Windows, službu Active Directory), je nutné nastavení [virtuální sítě Azure.](../../../virtual-network/virtual-networks-overview.md)

Většina zákazníků, kteří používají produkční kód v Azure, udržuje primární i sekundární repliky v Azure.

Úplné informace a kurzy o vysoké dostupnosti a technikách zotavení po havárii najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>Dvouvrstvé a třívrstvé používání virtuálních počítačů Azure a cloudových služeb
V tomto vzoru aplikace nasadíte do Azure dvouvrstvou nebo třívrstvou aplikaci pomocí [Cloudových služeb Azure](../../../cloud-services/cloud-services-choose-me.md) (webové a pracovní role – platforma jako služba (PaaS)) a [virtuálních počítačů Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Infrastruktura jako služba (IaaS)). Použití [Cloudových služeb Azure](https://azure.microsoft.com/documentation/services/cloud-services/) pro úroveň prezentace/obchodní vrstvy a SQL Serveru ve [virtuálních počítačích Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro datovou vrstvu je výhodné pro většinu aplikací spuštěných v Azure. Důvodem je, že spuštění výpočetní instance v cloudových službách poskytuje snadnou správu, nasazení, monitorování a horizontální navýšení kapacity.

Díky cloudovým službám azure udržuje infrastrukturu za vás, provádí běžnou údržbu, opravuje operační systémy a pokouší se zotavit z selhání služeb a hardwaru. Když vaše aplikace potřebuje horizontální navýšení kapacity, automatické a ruční škálování na více možností jsou k dispozici pro váš projekt cloudové služby zvýšením nebo snížením počtu instancí nebo virtuálních počítačů, které jsou používány vaší aplikací. Kromě toho můžete použít místní Visual Studio k nasazení aplikace do projektu cloudové služby v Azure.

Stručně řečeno, pokud nechcete vlastnit rozsáhlé úlohy správy pro prezentační nebo obchodní vrstvu a vaše aplikace nevyžaduje žádnou složitou konfiguraci softwaru nebo operačního systému, použijte Cloudservices Azure. Pokud Azure SQL Database nepodporuje všechny funkce, které hledáte, použijte SQL Server ve virtuálním počítači Azure pro datovou vrstvu. Spuštění aplikace ve Cloudových službách Azure a ukládání dat ve virtuálních počítačích Azure kombinuje výhody obou služeb. Podrobné porovnání najdete v části v tomto tématu [porovnání strategií vývoje v Azure](#comparing-web-development-strategies-in-azure).

V tomto vzoru aplikace obsahuje prezentační vrstva webovou roli, což je součást cloudových služeb spuštěná v prostředí spouštění Azure a je přizpůsobena pro programování webových aplikací, jak podporuje služba IIS a ASP.NET. Obchodní nebo back-endová vrstva zahrnuje roli pracovního procesu, což je součást cloudových služeb spuštěná v prostředí pro spouštění Azure a je užitečná pro generalizovaný vývoj a může provádět zpracování na pozadí pro webovou roli. Úroveň databáze je umístěna ve virtuálním počítači SQL Serveru v Azure. Komunikace mezi prezentační vrstvou a databázovou vrstvou probíhá přímo nebo přes obchodní vrstvu – součásti role pracovního procesu.

Tento vzor aplikace je užitečný, když:

* Chcete přesunout podnikové aplikace z virtualizovaných platforem místně do Azure implementací SQL Serveru s vysokou dostupností a možnostmi zotavení po havárii.
* Chcete vlastnit prostředí infrastruktury, které lze vertikálně navzestupovat a snížit na vyžádání.
* Azure SQL Database nepodporuje všechny funkce, které vaše aplikace nebo databáze potřebuje.
* Chcete provádět zátěžové testování pro různé úrovně pracovního vytížení, ale zároveň nechcete vlastnit a udržovat mnoho fyzických počítačů po celou dobu.

Následující diagram znázorňuje místní scénář a jeho cloudové řešení. V tomto scénáři umístíte prezentační vrstvu do webových rolí, obchodní vrstvy v rolích pracovních míst, ale na datové vrstvě ve virtuálních počítačích v Azure. Spuštění více kopií prezentační vrstvy v různých webových rolích zajišťuje požadavky na vyrovnávání zatížení mezi nimi. Když zkombinujete Cloudové služby Azure s virtuálními počítači Azure, doporučujeme také nastavit [virtuální síť Azure.](../../../virtual-network/virtual-networks-overview.md) S [Virtuální sítí Azure](../../../virtual-network/virtual-networks-overview.md)můžete mít stabilní a trvalé privátní IP adresy v rámci stejné cloudové služby v cloudu. Jakmile definujete virtuální síť pro vaše virtuální počítače a cloudové služby, můžou mezi sebou začít komunikovat přes privátní IP adresu. Navíc virtuální počítače a role Azure web/worker ve stejné [virtuální síti Azure](../../../virtual-network/virtual-networks-overview.md) poskytuje nízkou latenci a bezpečnější připojení. Další informace naleznete v [tématu Co je cloudová služba](../../../cloud-services/cloud-services-choose-me.md).

Jak je vidět v diagramu, Azure Balancer distribuuje provoz mezi více virtuálních počítačů a také určuje, který webový server nebo aplikační server se má připojit. S více instancemi webových a aplikačních serverů za vyrovnávání zatížení zajišťuje vysokou dostupnost prezentační vrstvy a obchodní vrstvy. Další informace naleznete v [tématu Doporučené postupy pro vzory aplikací, které vyžadují SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Vzorce aplikací s cloudovými službami](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Dalším přístupem k implementaci tohoto vzoru aplikace je použití konsolidované webové role, která obsahuje komponenty prezentační vrstvy i obchodní vrstvy, jak je znázorněno v následujícím diagramu. Tento vzor aplikace je užitečné pro aplikace, které vyžadují stavový návrh. Vzhledem k tomu, že Azure poskytuje bezstavové výpočetní uzly na webových a pracovních rolích, doporučujeme implementovat logiku pro ukládání stavu relace pomocí jedné z následujících technologií: [Azure Caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) nebo Azure SQL [Database](../../../sql-database/sql-database-technical-overview.md).

![Vzorce aplikací s cloudovými službami](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Vzor s virtuálními počítači Azure, databází Azure SQL a službou Azure App Service (Web Apps)
Primárním cílem tohoto vzoru aplikace je ukázat vám, jak kombinovat infrastrukturu Azure jako součásti služby (IaaS) s komponentami platformy Azure jako služby (PaaS) ve vašem řešení. Tento vzor se zaměřuje na Azure SQL Database pro relační úložiště dat. Nezahrnuje SQL Server ve virtuálním počítači Azure, který je součástí infrastruktury Azure jako nabídky služeb.

V tomto vzoru aplikace nasadíte databázovou aplikaci do Azure umístěním prezentační a obchodní vrstvy ve stejném virtuálním počítači a přístupem k databázi na serverech Azure SQL Database (SQL Database). Prezentační vrstvu můžete implementovat pomocí tradičních webových řešení založených na službě IIS. Nebo můžete implementovat kombinovanou prezentaci a obchodní úroveň pomocí [služby Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/).

Tento vzor aplikace je užitečný, když:

* Již máte existující sql database server nakonfigurovaný v Azure a chcete rychle otestovat vaši aplikaci.
* Chcete otestovat možnosti prostředí Azure.
* Chcete rychle zřídit vývojová a testovací prostředí na krátkou dobu.
* Vaše obchodní logika a součásti přístupu k datům mohou být samostatné ve webové aplikaci.

Následující diagram znázorňuje místní scénář a jeho cloudové řešení. V tomto scénáři umístíte aplikační vrstvy do jednoho virtuálního počítače v Azure a získáte přístup k datům v Azure SQL Database.

![Vzor smíšené aplikace](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Pokud se rozhodnete implementovat kombinovanou webovou a aplikační vrstvu pomocí Azure Web Apps, doporučujeme zachovat střední vrstvu nebo aplikační vrstvu jako knihovny dynamických odkazů (DLL) v kontextu webové aplikace.

Kromě toho zkontrolujte doporučení uvedená v [porovnání strategií vývoje webových aplikací v části Azure](#comparing-web-development-strategies-in-azure) na konci tohoto článku se dozvíte více o programovacích technikách.

## <a name="n-tier-hybrid-application-pattern"></a>N-vrstvý vzor hybridní aplikace
Ve vzoru n-vrstvé hybridní aplikace implementujete vaši aplikaci ve více vrstvách distribuovaných mezi místními a Azure. Proto vytvoříte flexibilní a opakovaně použitelný hybridní systém, který můžete upravit nebo přidat určitou úroveň bez evidenčních vrstev. Chcete-li rozšířit svou podnikovou síť do cloudu, použijte službu [Azure Virtual Network.](../../../virtual-network/virtual-networks-overview.md)

Tento vzor hybridní aplikace je užitečný, když:

* Chcete vytvářet aplikace, které běží částečně v cloudu a částečně místně.
* Chcete migrovat některé nebo všechny prvky existující místní aplikace do cloudu.
* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do Azure.
* Chcete vlastnit prostředí infrastruktury, které lze vertikálně navzestupovat a snížit na vyžádání.
* Chcete rychle zřídit vývojová a testovací prostředí na krátkou dobu.
* Chcete nákladově efektivní způsob, jak provést zálohy pro podnikové databázové aplikace.

Následující diagram znázorňuje n-vrstvý vzor hybridní aplikace, který zahrnuje v místním prostředí a Azure. Jak je znázorněno v diagramu, místní infrastruktura zahrnuje řadič domény [služby Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) pro podporu ověřování a autorizace uživatelů. Všimněte si, že diagram ukazuje scénář, kde některé části datové vrstvy žijí v místním datovém centru, zatímco některé části datové vrstvy žijí v Azure. V závislosti na potřebách vaší aplikace můžete implementovat několik dalších hybridních scénářů. Například můžete zachovat prezentační vrstvu a obchodní vrstvu v místním prostředí, ale na datové vrstvě v Azure.

![N-vrstvý aplikační vzor](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

V Azure můžete službu Active Directory použít jako samostatný cloudový adresář pro vaši organizaci nebo můžete také integrovat stávající místní službu Active Directory se [službou Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Jak je vidět v diagramu, komponenty obchodní vrstvy mohou přistupovat k více zdrojům dat, například k [SQL Serveru v Azure](virtual-machines-windows-sql-server-iaas-overview.md) prostřednictvím privátní interní IP adresy, k místnímu SQL Serveru prostřednictvím virtuální sítě [Azure](../../../virtual-network/virtual-networks-overview.md)nebo k [databázi SQL pomocí](../../../sql-database/sql-database-technical-overview.md) technologií poskytovatele dat rozhraní .NET Framework. V tomto diagramu je Azure SQL Database volitelná služba úložiště dat.

Ve vzoru n-vrstvé hybridní aplikace můžete implementovat následující pracovní postup v zadaném pořadí:

1. Identifikujte podnikové databázové aplikace, které je třeba přesunout do cloudu pomocí [sady Microsoft Assessment and Planning (MAP) Toolkit](https://microsoft.com/map). Sada nástrojů MAP shromažďuje data o inventáři a výkonu z počítačů, které zvažujete pro virtualizaci, a poskytuje doporučení ohledně plánování kapacity a hodnocení.
2. Naplánujte prostředky a konfiguraci potřebnou na platformě Azure, jako jsou účty úložiště a virtuální počítače.
3. Nastavte síťové připojení mezi místní podnikovou sítí a [virtuální sítí Azure](../../../virtual-network/virtual-networks-overview.md). Chcete-li nastavit připojení mezi místní podnikovou sítí a virtuálním počítačem v Azure, použijte jednu z následujících dvou metod:
   
   1. Navazte si připojení mezi místním a Azure přes veřejné koncové body na virtuálním počítači v Azure. Tato metoda poskytuje snadné nastavení a umožňuje používat ověřování serveru SQL Server ve vašem virtuálním počítači. Kromě toho nastavte pravidla skupiny zabezpečení sítě pro řízení veřejného provozu na virtuálním počítači. Další informace najdete [v tématu Povolení externího přístupu k virtuálnímu počítači pomocí portálu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Navazte připojení mezi místním a Azure prostřednictvím tunelového propojení virtuální privátní sítě Azure (VPN). Tato metoda umožňuje rozšířit zásady domény na virtuální počítač v Azure. Kromě toho můžete nastavit pravidla brány firewall a použít ověřování systému Windows ve virtuálním počítači. V současné době Azure podporuje zabezpečené připojení VPN mezi lokalitami a připojení VPN z bodu na pracoviště:
      
      * Díky zabezpečenému připojení mezi jednotlivými místy můžete v Azure navázat síťové připojení mezi místní sítí a virtuální sítí. Doporučujese pro připojení místního prostředí datového centra k Azure.
      * Díky zabezpečenému připojení point-to-site můžete navázat síťové připojení mezi virtuální sítí v Azure a jednotlivými počítači spuštěnými kdekoli. Většinou se doporučuje pro účely vývoje a testování.
      
      Informace o tom, jak se připojit k SQL Serveru v Azure, najdete v [tématu Připojení k virtuálnímu počítači SQL Serveru v Azure](virtual-machines-windows-sql-connect.md).
4. Nastavte naplánované úlohy a výstrahy, které zálohovat místní data na disku virtuálního počítače v Azure. Další informace najdete v tématu [ZÁLOHOVÁNÍ a obnovení serveru SQL Server pomocí služby Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx) a zálohování a obnovení pro SQL Server ve [virtuálních počítačích Azure](virtual-machines-windows-sql-backup-recovery.md).
5. V závislosti na potřebách vaší aplikace můžete implementovat jeden z následujících tří běžných scénářů:
   
   1. Svůj webový server, aplikační server a necitlivá data můžete uchovávat na databázovém serveru v Azure, zatímco citlivá data uchováváte místně.
   2. Můžete mít svůj webový server a aplikační server místně, zatímco databázový server ve virtuálním počítači v Azure.
   3. Databázový server, webový server a aplikační server můžete mít v místním prostředí, zatímco repliky databází můžete uchovávat ve virtuálních počítačích v Azure. Toto nastavení umožňuje místním webovým serverům nebo aplikacím pro vytváření sestav přístup k replikám databáze v Azure. Proto můžete dosáhnout snížit zatížení v místní databázi. Doporučujeme implementovat tento scénář pro úlohy pro velké čtení a vývojové účely. Informace o vytváření replik databáze v Azure najdete v tématu AlwaysOn Skupiny dostupnosti na [vysoké dostupnosti a zotavení po havárii pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Porovnání strategií vývoje webů v Azure
K implementaci a nasazení vícevrstvé aplikace založené na serveru SQL Server v Azure můžete použít jednu z následujících dvou metod programování:

* Nastavte tradiční webový server (IIS – Internetová informační služba) v Azure a získejte přístup k databázím v SQL Serveru ve virtuálních počítačích Azure.
* Implementujte a nasaďte cloudovou službu do Azure. Pak se ujistěte, že tato cloudová služba má přístup k databázím v SQL Serveru v azure virtuálních počítačích. Cloudová služba může obsahovat více webových a pracovních rolí.

Následující tabulka obsahuje porovnání tradičního vývoje webu s Cloudovými službami Azure a Azure Web Apps s ohledem na SQL Server ve virtuálních počítačích Azure. Tabulka obsahuje Azure Web Apps, protože je možné použít SQL Server ve virtuálním počítači Azure jako zdroj dat pro Azure Web Apps prostřednictvím své veřejné virtuální IP adresy nebo názvu DNS.

|  | Tradiční vývoj webových aplikací ve virtuálních počítačích Azure | Cloudové služby v Azure | Webhosting s Webovými aplikacemi Azure |
| --- | --- | --- | --- |
| **Migrace aplikace z místního prostředí** |Existující aplikace tak, jak jsou. |Aplikace potřebují webové a pracovní role. |Existující aplikace jako-je, ale vhodné pro samostatné webové aplikace a webové služby, které vyžadují rychlou škálovatelnost. |
| **Vývoj a nasazení** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Správce Služby IIS, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Každá cloudová služba má dvě prostředí, do kterých můžete nasadit balíček služeb a konfiguraci: pracovní a produkční. Můžete nasadit cloudovou službu do pracovního prostředí a otestovat ji před povýšením do produkčního prostředí. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell. |
| **Správa a instalace** |Jste zodpovědní za úlohy správy aplikace, dat, pravidel brány firewall, virtuální sítě a operačního systému. |Jste zodpovědní za úlohy správy v aplikaci, data, pravidla brány firewall a virtuální sítě. |Nesete odpovědnost pouze za úkoly správy v aplikaci a za data. |
| **Vysoká dostupnost a zotavení po havárii (HADR)** |Doporučujeme umístit virtuální počítače ve stejné sadě dostupnosti a ve stejné cloudové službě. Udržování virtuálních počítačů ve stejné sadě dostupnosti umožňuje Azure umístit uzly s vysokou dostupností do samostatných domén selhání a upgradu domén. Podobně udržování virtuálních počítačů ve stejné cloudové službě umožňuje vyrovnávání zatížení a virtuální počítače mohou komunikovat přímo mezi sebou prostředně přes lokální síť v rámci datového centra Azure.<br/><br/>Jste zodpovědní za implementaci řešení vysoké dostupnosti a zotavení po havárii pro SQL Server ve virtuálních počítačích Azure, abyste se vyhnuli prostojům. Podporované technologie HADR najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Jste zodpovědní za zálohování vlastních dat a aplikací.<br/><br/>Azure můžete přesunout vaše virtuální počítače, pokud hostitelský počítač v datovém centru selže kvůli problémům s hardwarem. Kromě toho může být plánované prostoje virtuálního počítače při aktualizaci hostitelského počítače pro aktualizace zabezpečení nebo softwaru. Proto doporučujeme udržovat alespoň dva virtuální zařízení v každé aplikační vrstvě, abyste zajistili nepřetržitou dostupnost. Azure neposkytuje sla pro jeden virtuální počítač. |Azure spravuje selhání vyplývající z základního hardwaru nebo softwaru operačního systému. Doporučujeme implementovat více instancí role webu nebo pracovního procesu, abyste zajistili vysokou dostupnost vaší aplikace. Další informace naleznete [v tématu Cloud Services, Virtual Machines a Virtual Network Service Level Agreement](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Jste zodpovědní za zálohování vlastních dat a aplikací.<br/><br/>Pro databáze s bydlištěm v databázi SQL Server ve virtuálním počítači Azure, jste zodpovědní za implementaci vysoké dostupnosti a řešení zotavení po havárii, aby se zabránilo prostojům. Podporované technologie HDAR najdete v tématu vysoká dostupnost a zotavení po havárii pro SQL Server ve virtuálních počítačích Azure.<br/><br/>**Zrcadlení databáze SQL Serveru:** Použití s Cloudovými službami Azure (role webu/pracovního procesu). Virtuální počítače SQL Serveru a projekt cloudové služby můžou být ve stejné virtuální síti Azure. Pokud virtuální počítač SQL Server není ve stejné virtuální síti, je třeba vytvořit alias serveru SQL Server pro směrování komunikace s instancí serveru SQL Server. Kromě toho název aliasu musí odpovídat názvu serveru SQL Server. |Vysoká dostupnost se dědí z rolí pracovních rolí Azure, úložiště objektů blob Azure a Azure SQL Database. Azure Storage například udržuje tři repliky všech dat objektů blob, tabulky a fronty. V každém okamžiku Azure SQL Database udržuje tři repliky dat spuštěné – jednu primární repliku a dvě sekundární repliky. Další informace najdete v tématu [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) a [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Při použití SQL Serveru ve virtuálním počítači Azure jako zdroje dat pro Azure Web Apps, mějte na paměti, že Azure Web Apps nepodporuje Azure Virtual Network. Jinými slovy, všechna připojení z Azure Web Apps na SQL Server virtuálních počítačů v Azure musí projít veřejné koncové body virtuálních počítačů. To může způsobit některá omezení pro vysokou dostupnost a scénáře zotavení po havárii. Například klientská aplikace v Azure Web Apps připojení k virtuálnímu počítači SQL Server s zrcadlením databáze by se nemohla připojit k novému primárnímu serveru, protože zrcadlení databáze vyžaduje, abyste nastavili virtuální síť Azure mezi hostitelskými virtuálními počítači SQL Server v Azure. Proto použití **SQL Server zabezpečení databáze** pomocí Azure Web Apps není aktuálně podporována.<br/><br/>**Skupiny dostupnosti SQL Serveru AlwaysOn**: Při použití Azure Web Apps s virtuálními počítači SQL Server v Azure můžete nastavit skupiny dostupnosti AlwaysOn. Ale je potřeba nakonfigurovat AlwaysOn Dostupnost skupiny posluchače směrovat komunikaci do primární repliky prostřednictvím koncových bodů s vyrovnáváním veřejného zatížení. |
| **propojení mezi prostory** |Virtuální síť Azure můžete použít k místnímu připojení. |Virtuální síť Azure můžete použít k místnímu připojení. |Virtuální síť Azure je podporovaná. Další informace naleznete v tématu [Integrace virtuální sítě webových aplikací](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Škálovatelnost** |Škálování je k dispozici zvětšením velikosti virtuálního počítače nebo přidáním dalších disků. Další informace o velikostech virtuálních strojů najdete [v tématu Velikosti virtuálních strojů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Pro databázový server**: Horizontální navýšení kapacity je k dispozici prostřednictvím techniky dělení databáze a SQL Server AlwaysOn Dostupnost skupiny.<br/><br/>Pro úlohy s velkým čtením můžete použít [skupiny dostupnosti AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) na více sekundárních uzlech a také replikaci serveru SQL Server.<br/><br/>Pro úlohy vysokého zápisu můžete implementovat data horizontálního dělení na více fyzických serverech a poskytnout tak horizontální navýšení kapacity aplikací.<br/><br/>Kromě toho můžete implementovat horizontální navýšení kapacity pomocí [serveru SQL Server s směrováním závislým na datech](https://technet.microsoft.com/library/cc966448.aspx). S datově závislé směrování (DDR), je třeba implementovat mechanismus dělení v klientské aplikaci, obvykle ve vrstvě obchodní vrstvy, pro směrování požadavků databáze do více uzlů SQL Server. Obchodní vrstva obsahuje mapování na to, jak jsou data rozdělena na oddíly a který uzel obsahuje data.<br/><br/>Můžete škálovat aplikace, které jsou spuštěny virtuální počítače. Další informace naleznete v [tématu Jak škálovat aplikaci](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Důležitá poznámka:** Funkce **automatického škálování** v Azure umožňuje automaticky zvýšit nebo snížit virtuální počítače, které používá vaše aplikace. Tato funkce zaručuje, že prostředí koncového uživatele není negativně ovlivněno během období špičky a virtuální chody se vypnou, když je poptávka nízká. Doporučujeme nenastavovat možnost automatického škálování pro cloudovou službu, pokud obsahuje virtuální počítače SQL Server. Důvodem je, že funkce automatického škálování umožňuje Azure zapnout virtuální počítač, když je využití procesoru v tomto virtuálním počítači vyšší než určitá prahová hodnota, a vypnout virtuální počítač, když využití procesoru klesne nižší než je. Funkce automatického škálování je užitečná pro bezstavové aplikace, jako jsou webové servery, kde jakýkoli virtuální virtuální server může spravovat úlohy bez odkazů na jakýkoli předchozí stav. Funkce automatického škálování však není užitečná pro stavové aplikace, jako je například SQL Server, kde pouze jedna instance umožňuje zápis do databáze. |Škálování je k dispozici pomocí více webových a pracovních rolí. Další informace o velikostech virtuálních strojů pro webové role a role pracovních procesů naleznete v [tématu Konfigurace velikostí pro cloudové služby](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Při používání **cloudových služeb**můžete definovat více rolí pro distribuci zpracování a také dosáhnout flexibilníškálování vaší aplikace. Každá cloudová služba obsahuje jednu nebo více webových rolí nebo rolí pracovních procesů, z nichž každá má vlastní soubory aplikace a konfiguraci. Můžete vertikálně navýšit kapacitu cloudové služby zvýšením počtu instancí rolí (virtuálních počítačů) nasazených pro roli a škálování cloudové služby snížením počtu instancí rolí. Podrobné informace najdete v tématu [Azure Execution Models](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Horizontální navýšení kapacity je k dispozici prostřednictvím integrované podpory vysoké dostupnosti Azure prostřednictvím [cloudových služeb, virtuálních počítačů a smlouvy o úrovni služeb virtuální sítě](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) a nástroje pro vyrovnávání zatížení.<br/><br/>Pro vícevrstvé aplikace doporučujeme připojit webové nebo pracovní role aplikace k virtuálním počítačům databázového serveru prostřednictvím virtuální sítě Azure. Kromě toho Azure poskytuje vyrovnávání zatížení pro virtuální počítače ve stejné cloudové službě, šíření požadavků uživatelů napříč nimi. Virtuální počítače připojené tímto způsobem můžete komunikovat přímo mezi sebou přes lokální síť v rámci datového centra Azure.<br/><br/>**Automatické škálování** můžete nastavit na webu Azure Portal i na časech plánu. Další informace naleznete v tématu [Jak nakonfigurovat automatické škálování pro cloudovou službu na portálu](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Škálování nahoru a dolů**: Můžete zvětšit nebo zmenšit velikost instance (VM) vyhrazené pro váš web.<br/><br/>Horizontální navýšení kapacity: Pro web můžete přidat další rezervované instance(VM).<br/><br/>Můžete nastavit **automatické škálování** na portálu, stejně jako časy plánu. Další informace naleznete v tématu [Scale Web Apps](../../../app-service/manage-scale-up.md). |

Další informace o výběru mezi těmito programovacími metodami najdete v [tématu Azure Web Apps, Cloud Services a VMs: Kdy použít které](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Další kroky
Další informace o spuštění SQL Serveru ve virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure Přehled](virtual-machines-windows-sql-server-iaas-overview.md).

