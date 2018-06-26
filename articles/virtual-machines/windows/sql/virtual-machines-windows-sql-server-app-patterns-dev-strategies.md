---
title: Vzory aplikace SQL Server na virtuálních počítačích | Microsoft Docs
description: Tento článek se zabývá vzory aplikace pro systém SQL Server na virtuálních počítačích Azure. Poskytuje řešení architekty a vývojáře základ pro dobrý aplikace architektury a návrhu.
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: craigg
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: ninarn
ms.openlocfilehash: 6bfb3092d6b4776499f957183613c951174b1a50
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754450"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Souhrn:
Určení, které vzor aplikací nebo vzory použít pro vaše aplikace na serveru SQL v Azure prostředí je rozhodnutí o návrhu důležité a vyžaduje důkladná znalost jak spolupracují systému SQL Server a jednotlivé komponenty infrastruktury Azure. Se systémem SQL Server ve službách infrastruktury Azure můžete snadno migrovat, údržbu a monitorování existující aplikace SQL Server vytvořené v systému Windows Server na virtuální počítače v Azure.

Cílem tohoto článku je k zajištění řešení architekty a vývojáře základ pro funkční aplikaci architektury a návrhu, které může kliknout, při migraci stávajících aplikací Azure a také vývoji nových aplikací v Azure.

Pro každou aplikaci vzor zjistíte scénářem místní, jeho odpovídající řešení využívajících cloud a související technických doporučení. Kromě toho článek popisuje specifické pro Azure vývoj strategie tak, aby vaše aplikace můžete navrhnout správně. Z důvodu mnoha schémat možné aplikace se doporučuje, že architekty a vývojáře by měl vybrat nejvhodnější vzor pro své aplikace a uživatelé.

**Přispěvatele do technického:** Leoš Carlos Vargas sleďů, Madhan Arumugam Ramakrishnan

**Odborní recenzenti:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Úvod
Mnoho typů vícevrstvé aplikace můžete vyvíjet oddělením součástí vrstvy jiné aplikace na různé počítače stejně jako samostatné součásti. Například můžete umístit klientská aplikace a obchodní pravidla součásti v jeden počítač, front-end webové vrstvy a součásti úroveň přístupu dat v jiném počítači a vrstvu databázi back-end v jiném počítači. Tento druh strukturování pomáhá izolovat jednotlivých úrovní od sebe navzájem. Pokud změníte, kde se data pocházejí z, nemusíte měnit klienta nebo webovou aplikaci, ale pouze komponenty vrstvy data access.

Typické *n vrstvá* aplikace obsahuje prezentační vrstvy, obchodní vrstvou a datovou vrstvou:

| Vrstva | Popis |
| --- | --- |
| **Prezentace** |*Prezentační vrstvy* (webová vrstva z vrstvy front-end) je vrstvy, ve kterém uživatelé pracují s aplikací. |
| **Firmy** |*Obchodní vrstvy* (střední úroveň) je vrstva, která prezentační vrstvy a datovou vrstvu používají ke komunikaci mezi sebou a obsahuje základní funkce systému. |
| **Data** |*Datové vrstvy* je v podstatě serveru, která ukládá data aplikace (například serveru se systémem SQL Server). |

Aplikačními vrstvami popisují logická seskupení funkce a součásti v aplikaci; zatímco vrstev popisují fyzické distribuci funkce a součásti na samostatné fyzické servery, počítače, sítě nebo vzdálené umístění. Vrstvy, které aplikace může nacházet na stejném fyzickém počítači (stejnou úroveň) nebo mohou být distribuovány v samostatných počítačích (n vrstvá), a komponenty v každé vrstvě komunikovat s součásti v jiných vrstev prostřednictvím dobře definovaných rozhraní. Si můžete představit jako fyzické distribuční vzorů například dvouvrstvá a třívrstvá, n vrstvá vrstvy termín. A **2vrstvý vzor aplikací** obsahuje dvě úrovně aplikace: aplikační server a databázový server. Dochází k přímé komunikaci mezi aplikační server a databázový server. Aplikační server obsahuje součásti webovou vrstvu a obchodní vrstvy. V **3vrstvý vzor aplikací**, existují tři aplikačními vrstvami: webového serveru, aplikačního serveru, který obsahuje vrstvu obchodní logiky a/nebo součástí obchodní vrstvy data access components a databázový server. Komunikace mezi webovým serverem a serverem databáze se stane přes aplikačního serveru. Podrobné informace o aplikaci vrstvy a vrstvy, najdete v části [Průvodce architekturou aplikace Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Než začnete přečtení tohoto článku, měli byste mít znalosti na základní koncepty systému SQL Server a Azure. Informace najdete v tématu [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [systému SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) a [Azure.com](https://azure.microsoft.com/).

Tento článek popisuje několik vzory aplikace, které může být vhodný pro jednoduché aplikace a také vysoce komplexní podnikové aplikace. Před s podrobnostmi o jednotlivých vzor, doporučujeme, aby byste si měli přečíst s dostupnými daty služby úložiště v Azure, jako například [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md), a [SQL Server ve virtuálním počítači Azure](virtual-machines-windows-sql-server-iaas-overview.md). Chcete-li nejlepší rozhodnutí o návrhu pro vaše aplikace, Pochopte, kdy používat služby úložiště dat, která jasně.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Zvolte systému SQL Server ve virtuálním počítači Azure, když:
* Je nutné ovládací prvek v systému SQL Server a Windows. Například to může zahrnovat verze systému SQL Server, speciální opravy hotfix, konfigurace výkonu atd.
* Potřebujete úplnou kompatibilitu s místním SQL serverem a chcete přesunout existujících aplikací do Azure jako-je.
* Chcete využít možnosti prostředí Azure, ale Azure SQL Database nepodporuje všechny funkce, které vaše aplikace vyžaduje. To může zahrnovat tyto oblasti:
  
  * **Velikost databáze**: během Tento článek byl aktualizován, databáze SQL podporuje až 1 TB dat v databázi. Pokud vaše aplikace vyžaduje více než 1 TB dat a nechcete, aby k implementaci vlastních horizontálního dělení řešení, doporučuje se použití systému SQL Server v virtuálního počítače Azure. Nejnovější informace najdete v tématu [škálování se databáze SQL Azure](https://msdn.microsoft.com/library/azure/dn495641.aspx), [DTU-Based zakoupení modelu](../../../sql-database/sql-database-service-tiers-dtu.md), a [modelu na základě vCore nákup](../../../sql-database/sql-database-service-tiers-vcore.md)(preview).
  * **Dodržování předpisů HIPAA**: zdravotní péče zákazníků a nezávislí výrobci softwaru (ISV) mohou vybrat [systému SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) místo [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) protože SQL Server v Virtuální počítač Azure je zahrnutý ve HIPAA obchodní přidružit smlouvy (BÁ). Informace o dodržování předpisů najdete v tématu [Microsoft Azure Trust Center: dodržování předpisů](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funkce na úrovni instance**: V tomto okamžiku SQL Database nepodporuje funkce, které za provozu mimo databázi (například odkazované servery agenta úlohy, FileStream, služby Service Broker, atd.). Další informace najdete v tématu [pokyny databáze SQL Azure a omezení](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>vrstvě 1 (jednoduchý): jeden virtuální počítač
V tomto vzoru aplikace můžete nasadit aplikaci systému SQL Server a databáze pro samostatný virtuální počítač v Azure. Na stejný virtuální počítač obsahuje klienta nebo webové aplikace, komponenty business, vrstva přístupu k datům a databázový server. Prezentační, obchodní a datové přístupového kódu jsou logicky oddělené ale se fyzicky nacházejí v počítači s jedním serverem. Většina zákazníků začínat tento vzor aplikací a pak škálovat tak, že přidáte další webové role nebo virtuální počítače do svého systému.

Tento vzor aplikací je užitečné, když:

* Chcete provádět jednoduché migrace pro platformu Azure k vyhodnocení, zda platformou přijme požadavky na aplikace, nebo ne.
* Chcete zachovat všechny úrovně aplikace hostované ve stejné virtuální počítač stejném datovém centru Azure k snížit latenci mezi vrstvami.
* Chcete rychle zřizovat vývoj a testování prostředí na krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně zatížení, ale současně nechcete vlastníte a udržovat vždy velký počet fyzických počítačů.

Následující diagram ukazuje jednoduchý místní scénář a nasazení jeho řešení cloud povolené na jednom virtuálním počítači v Azure.

![1vrstvý vzor aplikací](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Nasazení vrstvě podnikání (obchodní logiku a data přístup k součástem) ve stejné fyzické vrstvě jako prezentační vrstvy, můžete zvýšit výkon aplikace, pokud je nutné použít samostatné vrstvy kvůli škálovatelnosti ani bezpečnostní otázky.

Protože se jedná o velmi běžný vzor začínat, mohou být užitečné v následujícím článku na migrace pro přesun dat do virtuálního počítače SQL serveru: [migrace databáze do systému SQL Server na virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3vrstvá (jednoduchý): více virtuálních počítačů
V tomto vzoru aplikace nasadit 3vrstvé aplikace v Azure tak, že každý aplikační vrstvě jiný virtuální počítač. To poskytuje flexibilní prostředí pro snadné škálování zatížení a škálování scénáře. Pokud jeden virtuální počítač obsahuje klienta nebo webové aplikace, jiného hostitelem součásti vaší firmy a jiného hostitelem databázového serveru.

Tento vzor aplikací je užitečné, když:

* Chcete provést migraci složité databázové aplikace na virtuálních počítačích Azure.
* Chcete vrstev jinou aplikaci pro hostování v různých oblastech. Může mít například sdílené databáze, které jsou nasazeny do několika oblastí pro účely vytváření sestav.
* Chcete přesunout podnikové aplikace z virtualizované platformami na virtuálních počítačích Azure. Podrobné informace o podnikové aplikace, najdete v části [co je podniková aplikace](https://msdn.microsoft.com/library/aa267045.aspx).
* Chcete rychle zřizovat vývoj a testování prostředí na krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně zatížení, ale současně nechcete vlastníte a udržovat vždy velký počet fyzických počítačů.

Následující diagram ukazuje, jak můžete umístit jednoduché 3vrstvé aplikace v Azure tak, že každý aplikační vrstvě jiný virtuální počítač.

![vzor 3vrstvé aplikace](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

V tomto vzoru aplikace není v jednotlivých vrstvách jenom jeden virtuální počítač (VM). Pokud máte víc virtuálních počítačů v Azure, doporučujeme, abyste nastavili virtuální sítě. [Virtuální síť Azure](../../../virtual-network/virtual-networks-overview.md) vytvoří hranici zabezpečení pro důvěryhodné a také umožňuje virtuálním počítačům pro komunikaci mezi sebou pomocí privátní IP adresu. Kromě toho vždy ujistěte se, že všechna připojení k Internetu jenom přejít na prezentační vrstvy. Podle tohoto vzoru aplikace, spravovat pravidla skupiny zabezpečení sítě pro řízení přístupu. Další informace najdete v tématu [povolit externí přístup k virtuálnímu počítači pomocí portálu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

V diagramu může být Internetové protokoly TCP, UDP, HTTP nebo HTTPS.

> [!NOTE]
> Nastavení virtuální sítě v Azure je zdarma. Však budou účtovat bránu VPN, který se připojuje k místnímu. Tento poplatek podle množství času, který je zřízený a dostupné připojení.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>úroveň 2 a 3 úrovně s prezentační vrstvy Škálováním na více systémů
V tomto vzoru aplikace nasadit aplikaci vrstvy 2 nebo 3 úrovně databáze na virtuálních počítačích Azure tak, že každý aplikační vrstvě jiný virtuální počítač. Kromě toho můžete škálovat prezentační vrstvu kvůli zvýšenému objemu příchozí žádosti klientů.

Tento vzor aplikací je užitečné, když:

* Chcete přesunout podnikové aplikace z virtualizované platformami na virtuálních počítačích Azure.
* Chcete-li škálovat vrstvu prezentace kvůli zvýšenému objemu příchozí žádosti klientů.
* Chcete rychle zřizovat vývoj a testování prostředí na krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně zatížení, ale současně nechcete vlastníte a udržovat vždy velký počet fyzických počítačů.
* Chcete vlastní prostředí infrastruktury, které můžete škálovat nahoru a dolů na vyžádání.

Následující diagram ukazuje, jak můžete umístit aplikačními vrstvami ve více virtuálních počítačů v Azure pomocí škálování prezentační vrstvou kvůli zvýšenému objemu příchozích požadavků klienta. Jak je vidět v diagramu, je zodpovědný za distribuci přenosů mezi několik virtuálních počítačů a také určit, které webový server pro připojení k vyrovnávání zatížení Azure. S více instancí webové servery za službou Vyrovnávání zatížení zajistí vysokou dostupnost prezentační vrstvy.

![Vzor aplikací – prezentační vrstvy s více instancemi](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Osvědčené postupy pro vrstvy 2, 3 úrovně nebo n vrstvá vzorů, které mají víc virtuálních počítačů v jedné vrstvy
Doporučujeme umístit virtuální počítače, které patří do stejné vrstvy v rámci stejné cloudové služby a ve stejné dostupnost nastavit. Například, umístěte sadu webové servery v **CloudService1** a **AvailabilitySet1** a sada databázových serverů v **CloudService2** a  **AvailabilitySet2**. V Azure sadu dostupnosti umožňuje umístěte do domén samostatné selhání uzlů vysokou dostupnost a upgradovacích domén.

Využívat více instancí virtuálního počítače vrstvy, musíte nakonfigurovat nástroj pro vyrovnávání zatížení Azure mezi aplikačními vrstvami. Ke konfiguraci Vyrovnávání zatížení na jednotlivých úrovních, vytvořte koncový bod Vyrovnávání zatížení na jednotlivých úrovních virtuálních počítačích samostatně. Pro konkrétní úroveň nejprve vytvořte virtuální počítače v rámci stejné cloudové služby. To zajistí, že mají stejný veřejná virtuální IP adresa. Dále vytvořte koncový bod na jednom z virtuálních počítačů v této vrstvě. Potom přiřadíte stejný koncový bod jinými virtuálními počítači v této vrstvě služby Vyrovnávání zatížení. Vytvořením sady Vyrovnávání zatížení sítě provoz distribuovat mezi více virtuálních počítačů a taky umožnit nástroje pro vyrovnávání zatížení určit, který uzel k připojení při selhání uzlu virtuálního počítače back-end. Například s více instancí webové servery za službou Vyrovnávání zatížení zajistí vysokou dostupnost prezentační vrstvy.

Jako osvědčený postup vždy ujistěte, že všechna připojení k Internetu nejdřív přejít na prezentační vrstvy. Prezentační vrstva přistupuje k obchodní vrstvou a následně obchodní vrstvou přistupuje k datové vrstvy. Další informace o tom, jak povolit přístup k prezentační vrstvy najdete v tématu [povolit externí přístup k virtuálnímu počítači pomocí portálu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Všimněte si, že nástroj pro vyrovnávání zatížení v Azure funguje podobně jako v místním prostředí vyrovnávání zatížení. Další informace najdete v tématu [pro infrastrukturu Azure služby Vyrovnávání zatížení](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Kromě toho doporučujeme, abyste nastavili privátní sítě pro virtuální počítače pomocí Azure Virtual Network. To umožňuje, aby mohla mezi sebou komunikovat přes privátní IP adresu. Další informace najdete v tématu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>úroveň 2 a 3 úrovně s obchodní vrstvy Škálováním na více systémů
V tomto vzoru aplikace nasadit aplikaci vrstvy 2 nebo 3 úrovně databáze na virtuálních počítačích Azure tak, že každý aplikační vrstvě jiný virtuální počítač. Kromě toho můžete distribuovat serveru součásti aplikace ke více virtuálních počítačů z důvodu složitosti vaší aplikace.

Tento vzor aplikací je užitečné, když:

* Chcete přesunout podnikové aplikace z virtualizované platformami na virtuálních počítačích Azure.
* Chcete distribuovat serveru součásti aplikace ke více virtuálních počítačů z důvodu složitosti vaší aplikace.
* Chcete přesunout obchodní logiky těžký místní aplikace LOB (-obchodní) na virtuálních počítačích Azure. Aplikace LOB jsou sady důležitá počítačová aplikace, které jsou fungování organizace nezbytná podniku, jako jsou monitorování účtů, lidských zdrojů, mzdy, řízení a prostředek plánování aplikace.
* Chcete rychle zřizovat vývoj a testování prostředí na krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně zatížení, ale současně nechcete vlastníte a udržovat vždy velký počet fyzických počítačů.
* Chcete vlastní prostředí infrastruktury, které můžete škálovat nahoru a dolů na vyžádání.

Následující diagram ukazuje scénářem místní a jeho řešení pro cloud povolené. V tomto scénáři umístit aplikačními vrstvami ve více virtuálních počítačů v Azure pomocí škálování obchodní vrstvy, který obsahuje vrstvu obchodní logiky a součásti pro přístup k datům. Jak je vidět v diagramu, je zodpovědný za distribuci přenosů mezi několik virtuálních počítačů a také určit, které webový server pro připojení k vyrovnávání zatížení Azure. S více instancí aplikačních serverů za službou Vyrovnávání zatížení zajistí vysokou dostupnost obchodní vrstvy. Další informace najdete v tématu [osvědčené postupy pro vzory vrstvy 2, 3 úrovně nebo vícevrstvé aplikace, které mají více virtuálních počítačů v jedné vrstvy](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Vzor aplikací s obchodní vrstvy s více instancemi](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>úroveň 2 a 3 úrovně s prezentační a obchodní vrstvy Škálováním na více systémů a funkci hadr současně
V tomto vzoru aplikace nasadit aplikaci vrstvy 2 nebo 3 úrovně databáze na virtuálních počítačích Azure distribucí prezentační vrstvy (webový server) a součástí obchodní vrstvy (Aplikační server) do více virtuálních počítačů. Kromě toho implementovat řešení pro zotavení vysokou dostupnost a po havárii pro vaše databáze ve virtuálních počítačích Azure.

Tento vzor aplikací je užitečné, když:

* Chcete přesunout podnikové aplikace z virtualizované platformy místně do Azure implementací vysoká dostupnost SQL serveru a funkcím pro obnovení po havárii.
* Chcete škálovat prezentační vrstvou a obchodní vrstvou kvůli zvýšenému objemu příchozí žádosti klientů a složitost vaší aplikace.
* Chcete rychle zřizovat vývoj a testování prostředí na krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně zatížení, ale současně nechcete vlastníte a udržovat vždy velký počet fyzických počítačů.
* Chcete vlastní prostředí infrastruktury, které můžete škálovat nahoru a dolů na vyžádání.

Následující diagram ukazuje scénářem místní a jeho řešení pro cloud povolené. V tomto scénáři můžete škálovat prezentační vrstvou a obchodní vrstvy komponenty více virtuálních počítačů v Azure. Kromě toho implementovat vysokou dostupnost a po havárii obnovení (HADR) techniky pro databáze serveru SQL v Azure.

Více kopií aplikace spuštěné v různých virtuálních počítačů Ujistěte se, že jste Vyrovnávání zatížení požadavky napříč je. Pokud máte více virtuálních počítačů, musíte zajistit, že všechny virtuální počítače jsou přístupná a spuštěné v jednom bodě v čase. Při konfiguraci služby Vyrovnávání zatížení, Vyrovnávání zatížení Azure sleduje stav virtuálních počítačů a směruje příchozí volání do pořádku funkční uzlů virtuálního počítače správně. Informace o tom, jak nastavit vyrovnávání zatížení virtuálních počítačů najdete v tématu [pro infrastrukturu Azure služby Vyrovnávání zatížení](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). S více instancí webové a aplikační servery za službou Vyrovnávání zatížení zajistí vysokou dostupnost prezentační a obchodní vrstvy.

![Škálováním na více systémů a vysoké dostupnosti](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Osvědčené postupy pro vzory aplikace vyžadující SQL HADR
Při nastavování vysoká dostupnost SQL serveru a řešení zotavení po havárii v Azure Virtual Machines, nastavení virtuální sítě pro virtuální počítače pomocí [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) je povinný.  Virtuální počítače v rámci virtuální sítě i po výpadku služby bude mít stabilní privátní IP adresy, proto se můžete vyhnout aktualizace čas potřebný pro překlad názvu DNS. Kromě toho virtuální sítě vám umožní rozšiřovat do místní sítě do Azure a vytvoří hranici zabezpečení důvěryhodné. Pokud aplikace obsahuje omezení podnikové doméně (například ověřování systému Windows, služby Active Directory), například nastavení [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) je nezbytné.

Většina zákazníků, kteří běží produkčním kódu v Azure, jsou udržuje primární a sekundární repliky v Azure.

Kurzy o vysoké dostupnosti a techniky pro zotavení po havárii a komplexní informace najdete v tématu [vysokou dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>úroveň 2 a 3 vrstvě pomocí cloudových služeb a virtuálních počítačích Azure
V tomto vzoru aplikaci nasadíte vrstvy 2 nebo 3 úrovně aplikaci do Azure pomocí obou [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (webových a pracovních rolí – platforma jako služba (PaaS)) a [virtuální počítače Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) () Infrastruktura jako služba (IaaS)). Pomocí [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) pro prezentační vrstvy nebo obchodní vrstvou a SQL Server v [virtuální počítače Azure](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro data úroveň je výhodné pro většinu aplikací běžících v Azure. Důvodem je, že má do výpočetní instance spuštěné v cloudové služby poskytuje snadnou správu, nasazení, monitorování a Škálováním na více systémů.

S cloudovými službami Azure udržuje infrastruktury pro vás, provádí se pravidelná údržba, opravy operační systémy a pokusí se zotavit po selhání služby a hardwaru. Pokud aplikace potřebuje Škálováním na více systémů, automatickou a ruční škálování možnosti jsou dostupné pro projekt cloudové služby zvýšením nebo snížením počtu instancí nebo virtuálních počítačů, které jsou používány vaší aplikace. Kromě toho můžete použít místní Visual Studio k nasazení vaší aplikace do projektu cloudové služby v Azure.

V souhrnu, pokud nechcete, aby udělil rozsáhlé úlohy správy pro prezentaci nebo obchodní vrstvy, a aplikace není nutná žádná komplexní konfigurace softwaru nebo operačního systému, použijte Azure Cloud Services. Pokud Azure SQL Database nepodporuje všechny funkce, které hledáte, použití systému SQL Server v Azure virtuálního počítače pro datovou vrstvu. Spuštění aplikace na Azure Cloud Services a ukládání dat v Azure Virtual Machines kombinuje výhody obou služeb. Podrobné porovnání najdete v části v tomto tématu na [porovnávání vývoj strategie v Azure](#comparing-web-development-strategies-in-azure).

V tomto vzoru aplikace obsahuje prezentační vrstvy webové role, která v prostředí Azure provádění běží komponentu cloudové služby a je přizpůsobené pro programování webové aplikace podporuje službu IIS a ASP.NET. Vrstvu obchodní nebo back-end zahrnuje roli pracovního procesu, který komponentu cloudové služby běží v prostředí Azure provádění a je vhodný pro vývoj zobecněný a může provádět zpracování na pozadí pro webovou roli. Úroveň databáze se nachází ve virtuální počítače s SQL serverem v Azure. Komunikace mezi prezentační vrstvou a databázové vrstvy se stane, přímo nebo přes obchodní vrstvou – součásti role pracovního procesu.

Tento vzor aplikací je užitečné, když:

* Chcete přesunout podnikové aplikace z virtualizované platformy místně do Azure implementací vysoká dostupnost SQL serveru a funkcím pro obnovení po havárii.
* Chcete vlastní prostředí infrastruktury, které můžete škálovat nahoru a dolů na vyžádání.
* Azure SQL Database nepodporuje všechny funkce, které musí vaše aplikace nebo databáze.
* Chcete provést zátěžové testování pro různé úrovně zatížení, ale současně nechcete vlastníte a udržovat vždy velký počet fyzických počítačů.

Následující diagram ukazuje scénářem místní a jeho řešení pro cloud povolené. V tomto scénáři můžete umístit prezentační vrstvy v webové role, obchodní vrstvy v rolí pracovního procesu, ale datové vrstvy ve virtuálních počítačích v Azure. Spuštění více kopií prezentační vrstvou v jiných webových rolí zajistí načtení vyrovnávat požadavky mezi nimi. Když zkombinujete Azure Cloud Services pomocí Azure Virtual Machines, doporučujeme, abyste nastavili [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) také. S [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), budete mít ustájení a trvalé privátní IP adresy v rámci stejné cloudové služby v cloudu. Jakmile definovat virtuální sítě pro virtuální počítače a cloudové služby, můžou spustit komunikaci mezi sebou přes privátní IP adresu. Kromě toho s virtuální počítače a Azure web/role pracovního procesu ve stejné [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) zajišťuje nízkou latencí a bezpečnější připojení. Další informace najdete v tématu [co je Cloudová služba](../../../cloud-services/cloud-services-choose-me.md).

Jak je vidět v diagramu, nástroj pro vyrovnávání zatížení Azure rozděluje zatížení mezi více virtuálních počítačů a také určuje, které webový server nebo aplikačního serveru pro připojení k. S více instancí webové a aplikační servery za službou Vyrovnávání zatížení zajistí vysokou dostupnost prezentační vrstvou a obchodní vrstvou. Další informace najdete v tématu [osvědčených postupů pro aplikace vzory vyžadují SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Vzory aplikace s cloudovými službami](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Další postup pro implementaci tohoto vzoru aplikace je použití konsolidované webovou roli, která obsahuje prezentační vrstvou a obchodní vrstvy součásti, jak je znázorněno v následujícím diagramu. Tento vzor aplikací je užitečná pro aplikace, které vyžadují stavová návrhu. Vzhledem k tomu, že Azure poskytuje bezstavové výpočetní uzly na webových a pracovních rolí, doporučujeme implementovat logiku pro ukládání stavu relace pomocí jedné z následujících technologií: [ukládání do mezipaměti Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) nebo [databáze Azure SQL](../../../sql-database/sql-database-technical-overview.md).

![Vzory aplikace s cloudovými službami](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Vzor s virtuální počítače Azure, Azure SQL Database a Azure App Service (webové aplikace)
Hlavním cílem tohoto vzoru aplikace je ukazují, jak můžete kombinovat infrastrukturu Azure jako součásti služby (IaaS) s Azure součásti platformy jako služby (PaaS) ve vašem řešení. Tento vzor se zaměřuje na Azure SQL Database pro relační datové úložiště. Neobsahuje systému SQL Server ve virtuálním počítači Azure, který je součástí infrastruktury Azure jako nabídky služeb.

V tomto vzoru aplikace nasadit databázovou aplikaci do Azure tak, že umístění prezentační a obchodní vrstvy do stejného virtuálního počítače a přístup k databázi v servery Azure SQL Database (databáze SQL). Prezentační vrstva můžete implementovat pomocí tradičních webovou službu IIS řešení. Nebo můžete implementovat kombinované prezentační a obchodní vrstvou pomocí [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Tento vzor aplikací je užitečné, když:

* Už máte existující databázi SQL server nakonfigurovaný v Azure a chcete rychle testování vaší aplikace.
* Chcete testovat funkce prostředí Azure.
* Chcete rychle zřizovat vývoj a testování prostředí na krátkou dobu.
* Vaše obchodní logiku a data komponenty přístup může být obsaženy v rámci webové aplikace.

Následující diagram ukazuje scénářem místní a jeho řešení pro cloud povolené. V tomto scénáři můžete umístit aplikačními vrstvami na jednom virtuálním počítači v Azure a přístup dat ve službě Azure SQL Database.

![Vzor smíšený aplikací](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Pokud zvolíte možnost implementovat kombinované webové a aplikační vrstvě pomocí Azure Web Apps, doporučujeme ponechat vrstvě střední vrstvy nebo aplikace jako dynamické knihovny (DLL) v kontextu webové aplikace.

Kromě toho zkontrolujte doporučení v [porovnávání webové vývoj strategie v Azure](#comparing-web-development-strategies-in-azure) na konci tohoto článku Další informace o programování techniky.

## <a name="n-tier-hybrid-application-pattern"></a>Vzor hybridní vícevrstvé aplikace
V hybridní vícevrstvé aplikace vzoru implementovat aplikaci na více úrovních distribuovány mezi místními a Azure. Proto vytvořit hybridní flexibilní a opakovaně použitelný systém, kterou můžete změnit nebo přidat konkrétní úroveň beze změny jiných úrovních. K rozšíření vaší podnikové síti do cloudu, můžete použít [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) služby.

Tento vzor hybridní aplikace je užitečné, když:

* Chcete vytvářet aplikace, které běží částečně v cloudu a částečně místně.
* Chcete migrovat některé nebo všechny elementy stávající místní aplikace do cloudu.
* Chcete přesunout podnikové aplikace z místní virtualizované platformy Azure.
* Chcete vlastní prostředí infrastruktury, které můžete škálovat nahoru a dolů na vyžádání.
* Chcete rychle zřizovat vývoj a testování prostředí na krátkou dobu.
* Chcete nákladově efektivní způsob, jak provést zálohování pro podnikové aplikace databáze.

Následující diagram ukazuje vzor aplikací hybridní n vrstvá, které platí pro místní a Azure. Jak je vidět v diagramu, místní infrastruktury patří [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) řadič domény pro podporu uživatelů ověřování a autorizace. Všimněte si, že diagram ukazuje scénář, kde některé části datové vrstvy za provozu v datovém centru místní zatímco některé části datové vrstvy za provozu v Azure. V závislosti na potřebách vaší aplikace můžete implementovat několik dalších hybridní scénáře. Například může uchovávat prezentační vrstvou a obchodní vrstvou v místním prostředí, ale datové vrstvě v Azure.

![N-vrstvý vzor aplikací](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

V Azure, můžete použít služby Active Directory jako samostatné cloudu adresář pro vaši organizaci, nebo můžete také integrovat existující místní služby Active Directory s [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Jak je vidět v diagramu, součástí obchodní vrstvy přístup k více zdrojů dat, například k [systému SQL Server v Azure](virtual-machines-windows-sql-server-iaas-overview.md) prostřednictvím privátní interní IP adresu, na místní SQL Server prostřednictvím [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), nebo [SQL Database](../../../sql-database/sql-database-technical-overview.md) pomocí technologie zprostředkovatele dat .NET Framework. V tomto diagramu je Azure SQL Database služby storage volitelnými daty.

Ve vzoru hybridní vícevrstvé aplikace můžete implementovat následující pracovní postup v uvedeném pořadí:

1. Identifikovat enterprise databázové aplikace, které je třeba jej přesunout nahoru do cloudu pomocí [Microsoft Assessment and Planning (MAP) Toolkit](http://microsoft.com/map). MAP Toolkit shromáždí data inventáře a výkonu z počítačů, které je třeba zajistit pro virtualizaci a obsahuje doporučení týkající se kapacity a plánování assessment.
2. Plánování prostředků a konfigurace je potřeba v platformy Azure, jako je například účty úložiště a virtuálních počítačů.
3. Nastavit připojení k síti mezi podnikové sítě na místě a [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Pokud chcete nastavit připojení mezi podnikové sítě na místní a virtuální počítač v Azure, použijte jednu z následujících dvou metod:
   
   1. Navázat připojení mezi místními a Azure prostřednictvím veřejné koncové body na virtuálním počítači v Azure. Tato metoda poskytuje snadno instalace a umožňuje používat ověřování serveru SQL Server ve virtuálním počítači. Kromě toho nastavení pravidel skupiny zabezpečení vaší sítě k řízení veřejné provoz na virtuální počítač. Další informace najdete v tématu [povolit externí přístup k virtuálnímu počítači pomocí portálu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Navázat připojení mezi místními a Azure přes tunelové propojení Azure virtuální privátní sítě (VPN). Tato metoda umožňuje rozšířit zásady domény k virtuálnímu počítači v Azure. Kromě toho můžete nastavit pravidla brány firewall a použít ověřování systému Windows ve virtuálním počítači. Azure v současné době podporuje bezpečné site-to-site VPN a připojení point-to-site VPN:
      
      * S zabezpečené připojení site-to-site můžete vytvořit připojení k síti mezi vaší místní sítí a virtuální sítí v Azure. Doporučuje se pro připojení vaší místní prostředí datového centra Azure.
      * S zabezpečené připojení point-to-site můžete vytvořit připojení k síti mezi vaší virtuální sítě v Azure a vaše jednotlivé počítače se systémem kdekoli. Většinou doporučujeme pro vývojová a testovací účely.
      
      Informace o tom, jak se připojit k serveru SQL v Azure najdete v tématu [připojení SQL serveru virtuálnímu počítači na platformě Azure](virtual-machines-windows-sql-connect.md).
4. Nastavte naplánované úlohy a výstrahy, které zálohovat data místně v disku virtuálního počítače v Azure. Další informace najdete v tématu [zálohování systému SQL Server a obnovení služby úložiště objektů Blob Azure](https://msdn.microsoft.com/library/jj919148.aspx) a [zálohování a obnovení pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).
5. V závislosti na potřebách vaší aplikace můžete implementovat jednu z následujících tří běžných scénářů:
   
   1. Na databázovém serveru v Azure můžete chránit váš webový server, aplikační server a malých a velkých písmen data, zatímco zachovat citlivá data v místě.
   2. Abyste zajistili, že váš webový server a aplikační server místní vzhledem k tomu databázový server ve virtuálním počítači v Azure.
   3. Můžete ponechat databázového serveru, webového serveru a aplikačního serveru místní zatímco zachovat repliky databáze na virtuálních počítačích v Azure. Toto nastavení umožňuje místní webové servery nebo vytváření sestav aplikace pro přístup k repliky databáze v Azure. Proto můžete dosáhnout snížení zatížení v místní databázi. Doporučujeme vám, že můžete implementovat tento scénář pro těžký číst úlohy a pro účely vývoje. Informace o vytvoření repliky databáze v Azure najdete v tématu skupiny dostupnosti AlwaysOn v [vysokou dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Porovnání webových vývoj strategie v Azure
K implementaci a nasazovat vícevrstvé aplikace založené na systému SQL Server v Azure, můžete použít jednu z těchto způsobů programování:

* V databázích Azure a přístup v systému SQL Server v Azure Virtual Machines nastavte tradiční webový server (IIS - Internetová informační služba).
* Implementace a nasazení cloudové služby Azure. Pak se ujistěte, že tuto cloudovou službu můžete přístup k databází v systému SQL Server v Azure virtuální počítače. Cloudové služby může obsahovat několik webových a pracovních rolí.

Následující tabulka obsahuje porovnání tradiční webové vývoj s Azure Cloud Services a Azure Web Apps s ohledem na SQL Server v Azure Virtual Machines. Tabulka obsahuje Azure Web Apps, protože to je možné použít k systému SQL Server ve virtuálním počítači Azure jako zdroj dat pro webové aplikace Azure přes jeho veřejné virtuální IP adresa nebo název DNS.

|  | Vývoj tradiční webu v Azure Virtual Machines | Cloudové služby v Azure | Webového hostingu s webové aplikace Azure |
| --- | --- | --- | --- |
| **Migrace aplikací z místní** |Existující aplikace jako-je. |Aplikace potřebují webových a pracovních rolí. |Existující aplikace jako-ale hodí se pro samostatný webové aplikace a webové služby, které vyžadují rychlou škálovatelnost. |
| **Vývoj a nasazení** |Visual Studio, služba WebMatrix, Visual Web Developer, WebDeploy, FTP, sady TFS, Správce služby IIS, prostředí PowerShell. |Visual Studio, Azure SDK a sady TFS, prostředí PowerShell. Jednotlivých cloudových služeb má dvě prostředí, do kterých můžete nasadit balíček služby a konfigurace: pracovní a provozní. Cloudové služby můžete nasadit do fázovacího prostředí otestovat ho, než ho přesunout do výroby. |Visual Studio, služba WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, Githubu, Mercurial, sady TFS, webové nasadit, prostředí PowerShell. |
| **Správa a instalace** |Jste zodpovědní za správu úlohy na aplikace, data, pravidla brány firewall, virtuální sítě a operačního systému. |Jste zodpovědní za správu úlohy na aplikace, data, pravidla brány firewall a virtuální sítě. |Jste zodpovědní za úlohy správy v aplikaci a pouze data. |
| **Vysoká dostupnost a zotavení po havárii (HADR)** |Doporučujeme umísťovat virtuální počítače ve stejné sadě dostupnosti a v rámci stejné cloudové služby. Zachovat virtuální počítače ve stejné skupině dostupnosti umožňuje Azure a umístěte do domén samostatné selhání uzlů vysokou dostupnost a upgradu domén. Podobně zachovat virtuální počítače v rámci stejné cloudové služby umožňuje Vyrovnávání zatížení a virtuální počítače může komunikovat přímo mezi sebou v místní síti v rámci datové centrum Azure.<br/><br/>Jste zodpovědní za implementaci vysoké dostupnosti a řešení zotavení po havárii pro SQL Server v Azure Virtual Machines žádné výpadky. Podporované HADR technologií, najdete v části [vysokou dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Jste zodpovědní za zálohování dat a aplikací.<br/><br/>Azure můžete přesunout virtuální počítače, pokud se hostitelský počítač v datovém centru nezdaří kvůli problémům s hardwarem. Kromě toho je možné, plánované výpadky vašeho virtuálního počítače po aktualizaci hostitelský počítač pro zabezpečení nebo softwarové aktualizace. Proto doporučujeme udržovat aspoň dva virtuální počítače v jednotlivých vrstvách aplikace k zajištění nepřetržité dostupnosti. Azure neposkytuje SLA pro jeden virtuální počítač. |Spravuje Azure chyby způsobené základní softwaru hardwaru nebo operačního systému. Doporučujeme vám, že můžete implementovat několik instancí role web nebo pracovní proces pro zajištění vysoké dostupnosti vaší aplikace. Informace najdete v tématu [cloudové služby, virtuální počítače a virtuální sítě smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Jste zodpovědní za zálohování dat a aplikací.<br/><br/>Pro databáze, které se nacházejí v databázi systému SQL Server ve virtuálním počítači Azure jste zodpovědní za implementaci vysoké dostupnosti a po havárii řešení obnovení žádné výpadky. Podporované HDAR technologií najdete v části vysokou dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines.<br/><br/>**Zrcadlení databáze serveru SQL**: použití se službou Azure Cloud Services (webové/role pracovního procesu). Virtuálním počítačům systému SQL Server a projekt cloudové služby může být ve stejné virtuální síti Azure. Pokud virtuální počítač SQL Server není ve stejné virtuální síti, budete muset vytvořit Alias SQL Server pro směrování komunikace na instanci systému SQL Server. Název aliasu kromě toho musí odpovídat názvu systému SQL Server. |Zajištění vysoké dostupnosti je zděděn z role pracovního procesu systému Azure, Azure blob storage a Azure SQL Database. Například Azure úložiště udržuje tři repliky všech objektů blob, tabulky a fronty data. Současně, Azure SQL Database udržuje tři repliky dat systémem – jedna primární replika a dvě sekundární repliky. Další informace najdete v tématu [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) a [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Při použití SQL serveru ve virtuálním počítači Azure jako zdroj dat pro Azure Web Apps, mějte na paměti, že Azure Web Apps nepodporuje virtuální sítě Azure. Jinými slovy všechna připojení z Azure Web Apps k virtuálním počítačům systému SQL Server v Azure musí projít veřejné koncové body virtuálních počítačů. To může způsobit určitá omezení pro vysokou dostupnost a scénářů zotavení po havárii. Klientská aplikace na Azure Web Apps připojení k virtuální počítač s SQL serverem s zrcadlení databáze například nebude moci připojit k novým primárním serverem, protože zrcadlení databáze vyžaduje, abyste nastavili Azure Virtual Network mezi virtuálními počítači hostitele systému SQL Server v Azure. Proto pomocí **zrcadlení databáze serveru SQL** s webovými aplikacemi Azure v současné době nepodporují.<br/><br/>**Skupiny dostupnosti AlwaysOn serveru SQL**: skupiny dostupnosti AlwaysOn můžete nastavit při používání Azure Web Apps s virtuálním počítačům systému SQL Server v Azure. Ale budete muset nakonfigurovat naslouchací proces skupiny dostupnosti AlwaysOn směrovat komunikaci na primární repliku prostřednictvím veřejných koncových bodů s vyrovnáváním zatížení. |
| **Připojení mezi různými místy** |Můžete se připojit k místní virtuální síť Azure. |Můžete se připojit k místní virtuální síť Azure. |Virtuální síť Azure je podporováno. Další informace najdete v tématu [integrace virtuální sítě webové aplikace](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Škálovatelnost** |Škálování je k dispozici zvýšením velikosti virtuálních počítačů nebo přidání více disků. Další informace o velikosti virtuálních počítačů najdete v tématu [velikostí virtuálních počítačů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Pro Server databáze**: Škálováním na více systémů je k dispozici prostřednictvím rozdělení techniky databáze a skupiny dostupnosti AlwaysOn serveru SQL Server.<br/><br/>Pro náročných úloh pro čtení, můžete použít [skupiny dostupnosti AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) na více sekundární uzly také replikace SQL serveru.<br/><br/>Pro úlohy velkou zápisu můžete implementovat vodorovné rozdělení dat napříč více fyzických serverů zajistit aplikace Škálováním na více systémů.<br/><br/>Kromě toho můžete implementovat Škálováním na více systémů pomocí [systému SQL Server se směrováním závislé Data](https://technet.microsoft.com/library/cc966448.aspx). S dat závislé směrování (DDR), potřebujete implementovat rozdělení mechanismus klientské aplikace, obvykle ve vrstvě obchodní vrstvy pro směrování požadavků databáze do více uzlů serveru SQL Server. Obchodní vrstva obsahuje mapování data jak je rozdělena na oddíly a který uzel obsahuje data.<br/><br/>Je možné škálovat aplikace, které jsou spuštěné virtuální počítače. Další informace najdete v tématu [postup škálování aplikace](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Důležitá poznámka**: **škálování** funkce v Azure umožňuje automaticky zvětšit nebo zmenšit virtuální počítače, které jsou používány vaší aplikace. Tato funkce zaručuje, že činnost koncového uživatele není ovlivněná negativně špičky a jsou virtuální počítače vypnout při nízkém zatížení. Doporučujeme, nenastavujte možnost automatického škálování pro cloudové služby, pokud obsahuje virtuálním počítačům systému SQL Server. Důvodem je, že umožňuje, aby funkce automatického škálování Azure zapnout na virtuálním počítači, když je vyšší než některé prahová hodnota využití procesoru v tohoto virtuálního počítače a chcete-li vypnout virtuální počítač, kdy přestane využití procesoru nižší než. Funkce automatického škálování je užitečná pro bezstavové aplikace, jako jsou třeba webové servery, kde žádné virtuální počítače můžete spravovat úlohy bez všechny odkazy na všechny předchozího stavu. Funkce škálování však není užitečné stavových aplikací, jako je SQL Server, kde pouze jedna instance umožňuje zápis do databáze. |Škálování je k dispozici v několika webových a pracovních rolí. Další informace o velikosti virtuálních počítačů pro webových rolí a rolí pracovního procesu naleznete v tématu [konfigurace velikosti pro cloudové služby](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Při použití **cloudové služby**, můžete definovat víc rolí distribuce zpracování a také dosáhnout flexibilní škálování aplikace. Jednotlivých cloudových služeb obsahuje jeden nebo více webové role nebo rolí pracovního procesu, každou s vlastní soubory aplikace a konfigurace. Můžete škálování cloudové služby zvýšením počtu instancí role (virtuálních počítačů), nasazení pro roli a vertikální snížení kapacity Cloudová služba, a tím snižují počet instancí role. Podrobné informace najdete v tématu [Azure provádění modely](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Škálováním na více systémů je k dispozici prostřednictvím podpory Azure integrovanou vysokou dostupnost prostřednictvím [cloudové služby, virtuální počítače a virtuální sítě smlouvy o úrovni služeb](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) a nástroj pro vyrovnávání zatížení.<br/><br/>Vícevrstvé aplikace doporučujeme připojení aplikace role web nebo worker do databáze serveru virtuálních počítačů prostřednictvím Azure Virtual Network. Kromě toho Azure poskytuje Vyrovnávání zatížení pro virtuální počítače ve stejné cloudové služby, šíření požadavky uživatelů mezi nimi. Virtuální počítače připojené tímto způsobem může komunikovat přímo s navzájem v místní síti v rámci datové centrum Azure.<br/><br/>Můžete nastavit **škálování** na portálu Azure a také dobu plán. Další informace najdete v tématu [postup konfigurace automatického škálování pro cloudové služby na portálu](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Škálovat nahoru a dolů**: můžete můžete zvětšit nebo zmenšit velikost instance (VM) vyhrazena pro svůj web.<br/><br/>Horizontální navýšení kapacity: můžete přidat více vyhrazenou instancí (VM) pro svůj web.<br/><br/>Můžete nastavit **škálování** na portálu, jakož i plán časy. Další informace najdete v tématu [postup škálování webové aplikace](../../../app-service/web-sites-scale.md). |

Další informace o výběru mezi tyto programovací metody naleznete v tématu [Azure Web Apps, cloudové služby a virtuální počítače: volba aplikace](../../../app-service/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Další kroky
Další informace o spouštění systému SQL Server v Azure virtuální počítače, naleznete v části [SQL Server na Přehled virtuálních počítačů Azure](virtual-machines-windows-sql-server-iaas-overview.md).

