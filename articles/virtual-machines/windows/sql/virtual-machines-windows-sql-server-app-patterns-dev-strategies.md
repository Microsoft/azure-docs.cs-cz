---
title: Modely aplikací SQL serveru na virtuálních počítačích | Dokumentace Microsoftu
description: Tento článek se týká vzory aplikací pro SQL Server na virtuálních počítačích Azure. Nabízí architekti a vývojáři řešení základní kvalitní architektury a návrhu.
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
ms.openlocfilehash: 9ccf7d85b52759a3f2cd64facc6ee8d7255f0fac
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253172"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Souhrn:
Určení, které vzor aplikací nebo vzorů pro svoje aplikace na serveru SQL v Azure prostředí je rozhodnutí o návrhu důležité a vyžaduje důkladného porozumění jak spolu fungují systému SQL Server a jednotlivé komponenty infrastruktury Azure. S SQL serverem ve službách infrastruktury Azure můžete snadno migrovat, udržovat a monitorovat stávajících aplikací SQL serveru vytvořené na Windows Server na virtuální počítače v Azure.

Cílem tohoto článku je poskytnout architekti a vývojáři řešení základní kvalitní architektury a návrhu, které následují po migraci stávajících aplikací do Azure a jak vyvíjet nové aplikace v Azure.

Pro každý vzorek aplikace zjistíte scénáři místní, jeho odpovídající řešení povolenou podporu cloudu a související technických doporučení. Kromě toho článek popisuje strategie vývoje týkající se Azure tak, že můžete navrhnout vaše aplikace správně. Z důvodu mnoho vzory aplikací je to možné doporučujeme, architekti a vývojáři měli zvolit nejvhodnější vzor pro jejich aplikace a uživatele.

**Přispěvatele do technického:** Luis Carlos Vargas sleďů Madhan Arumugam Ramakrishnan

**Odborní recenzenti:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Mashkowski nir a jsem, Sanjay Mishra, Silvano Coriani, Stefan Schackow Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Úvod
Mnoho typů n vrstvé aplikace můžete vyvíjet oddělit součásti různých aplikačních vrstev na různých počítačích, stejně jako samostatné komponenty. Například můžete umístit klientská aplikace a obchodní pravidla komponent v jednom počítači, front-endovou webovou vrstvu a komponenty vrstvy přístupu dat v jiném počítači a back-end databázovou vrstvu v jiném počítači. Tento druh strukturování pomáhá izolovat jednotlivých úrovních, od sebe navzájem. Pokud změníte, odkud data pochází, není nutné měnit klienta nebo webovou aplikaci, ale pouze komponenty vrstvy data access.

Typické *n vrstvá* aplikace obsahuje prezentační vrstvou, obchodní vrstvou a datovou vrstvou:

| Úroveň | Popis |
| --- | --- |
| **Prezentace** |*Prezentační vrstva* (webová vrstva, úroveň front-endu) je vrstva, ve které uživatelé komunikují s aplikací. |
| **firmy** |*Obchodní vrstvy* (střední úroveň) je vrstva, která prezentační vrstva a datová vrstva používají ke komunikaci mezi sebou a zahrnuje základní funkce systému. |
| **Data** |*Datová vrstva* je v podstatě server, který ukládá data aplikace (například server se systémem SQL Server). |

Vrstvy aplikace popisují logické skupiny funkcí a komponenty v aplikaci; zatímco vrstev popisují fyzickou distribuci funkcí a komponenty na samostatné fyzické servery, počítače, sítě nebo vzdáleného umístění. Vrstvy aplikace mohou nacházet na stejném fyzickém počítači (na stejné úrovni), nebo mohou být distribuovány do samostatných počítačů (n vrstvá) a komponenty v každé vrstvě komunikovat s komponentami v jiných vrstvách prostřednictvím dobře definovaných rozhraní. Na úrovni termín jako fyzickou distribuci vzorů, jako je například dvouvrstvé třívrstvé a n vrstvá si můžete představit. A **2vrstvý vzor aplikací** obsahuje dvě aplikačních vrstev: aplikační server a databázový server. Dochází k přímé komunikaci mezi aplikačním serverem a serverem databáze. Aplikační server obsahuje součásti webové a obchodní vrstvy. V **3vrstvý vzor aplikací**, existují tři aplikačních vrstev: webového serveru, aplikačního serveru, který obsahuje vrstvu obchodní logiky a/nebo součástí obchodní vrstvy data access components a databázový server. Komunikace mezi webovým serverem a serverem databáze se stane, prostřednictvím aplikačního serveru. Podrobné informace o úrovních a vrstvy aplikace naleznete v tématu [příručka aplikační architektura v Microsoft](https://msdn.microsoft.com/library/ff650706.aspx).

Předtím, než se pustíte do čtení tohoto článku, měli byste mít znalosti o základních konceptech systému SQL Server a Azure. Informace najdete v tématu [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [systému SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) a [Azure.com](https://azure.microsoft.com/).

Tento článek popisuje několik vzory aplikací, které může být vhodný pro jednoduchá aplikace, stejně jako vysoce složitých firemních aplikací. Před s podrobnostmi o každý vzorek, doporučujeme vám, že můžete byste se seznámit s dostupnými daty služby úložiště v Azure, například [služby Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md), a [SQL Server ve virtuálním počítači Azure](virtual-machines-windows-sql-server-iaas-overview.md). Chcete-li nejlepší rozhodnutí o návrhu pro aplikace, pochopit, kdy k používání služby úložiště které data jasně.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Zvolte systém SQL Server ve virtuálním počítači Azure, když:
* Je třeba řízení na serveru SQL Server a Windows. Například to může zahrnovat verze systému SQL Server, speciální opravy hotfix, konfigurace výkonu atd.
* Potřebujete úplnou kompatibilitu s místním SQL serverem a chcete přesun stávajících aplikací do Azure jako-je.
* Chcete-li využívají možnosti prostředí Azure, ale Azure SQL Database nepodporuje všechny funkce, které vaše aplikace vyžaduje. To může zahrnovat následující oblasti:
  
  * **Velikost databáze**: V době, tento článek byl aktualizován, SQL Database podporuje až 1 TB dat v databázi. Pokud vaše aplikace vyžaduje víc než 1 TB dat a není nutné implementovat vlastní horizontálního dělení řešení, doporučuje se, že používáte systém SQL Server ve virtuálním počítači Azure. Nejnovější informace najdete v tématu [škálování na Azure SQL Database](https://msdn.microsoft.com/library/azure/dn495641.aspx), [Model nákupu DTU-Based](../../../sql-database/sql-database-service-tiers-dtu.md), a [Model založený na virtuálních jádrech zakoupení](../../../sql-database/sql-database-service-tiers-vcore.md)(preview).
  * **Dodržování předpisů HIPAA**: zvolit zdravotní péče zákazníkům a nezávislým výrobcům softwaru (ISV) [systému SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md) místo [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md) protože SQL Server ve službě Virtuální počítač Azure je zahrnutých podle zákona HIPAA Business Associate Agreement (BAA). Informace o dodržování předpisů najdete v tématu [Microsoft Azure Trust Center: dodržování předpisů](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funkce na úrovni instance**: SQL Database v tuto chvíli nepodporuje funkce, které za provozu mimo databázi (například propojené servery agenta úlohy, FileStream, služba Service Broker, atd.). Další informace najdete v tématu [omezení a pokyny pro Azure SQL Database](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>(jednoduchý) úrovně 1: jeden virtuální počítač
V tomto modelu aplikace můžete nasadit svoji aplikaci SQL Server a databáze pro samostatný virtuální počítač v Azure. Stejný virtuální počítač obsahuje klienta/webové aplikace, komponenty business, vrstva přístupu k datům a databázový server. Prezentační, obchodní a kód přístupu k datům jsou logicky oddělené, ale jsou fyzicky umístěny v počítači s jedním serverem. Většina zákazníků začíná s tímto modelem stačí aplikace a pak, horizontální navýšení kapacity přidáním více webové role nebo virtuálního počítače do svého systému.

Tento model aplikace je užitečné, když:

* Chcete provést jednoduché migrace na platformě Azure k vyhodnocení, jestli platforma odpovědi podle požadavků vaší aplikace, nebo ne.
* Chcete zachovat všechny úrovně aplikace hostované ve stejném virtuálním počítači ve stejném datovém centru Azure na snížení latence mezi vrstvami.
* Chcete pro rychlé zřizování vývojových a testovacích prostředí pro krátkou dobu.
* Můžete provádět zátěžové testování pro různé úrovně zatížení, ale současně nechcete a udržovat neustále velký počet fyzických počítačů.

Následující diagram ukazuje jednoduchý místní scénář a jak můžete nasadit svoje řešení cloud je povolený v jeden virtuální počítač v Azure.

![1vrstvý vzor aplikací](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Nasazení obchodní vrstvu (obchodní logiku a data přístup k součástem) ve stejné fyzické úrovni jako prezentační vrstvy můžete maximalizovat výkon aplikace, pokud je nutné použít samostatné úrovně kvůli škálovatelnosti nebo bezpečnostní otázky.

Protože se jedná velmi běžný vzor začínat, můžou být užitečné v následujícím článku na migrace pro přesun dat do virtuálního počítače s SQL serverem: [migrace databáze do služby SQL Server na Virtuálním počítači Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>vrstvy 3 (jednoduchý): více virtuálních počítačů
V tomto modelu aplikace nasadíte tak, že jednotlivých vrstev aplikace v různých virtuálních počítačů 3vrstvé aplikace v Azure. To poskytuje flexibilní prostředí pro jednoduché scénáře škálování zatížení a škálování na víc systémů. Když jeden virtuální počítač obsahuje klienta/webové aplikace, druhé hostuje komponentami vaší firmy a druhý je hostitelem databázového serveru.

Tento model aplikace je užitečné, když:

* Chcete provést migraci složitých databázových aplikací na Azure Virtual Machines.
* Chcete různých aplikačních vrstev, které budou hostované v různých oblastech. Může mít například sdílené databáze, které jsou nasazené do více oblastí pro účely vytváření sestav.
* Chcete přesunout firemních aplikací z místních virtualizovaných platforem Azure Virtual Machines. Podrobné informace o podnikových aplikací, najdete v části [co je podniková aplikace](https://msdn.microsoft.com/library/aa267045.aspx).
* Chcete pro rychlé zřizování vývojových a testovacích prostředí pro krátkou dobu.
* Můžete provádět zátěžové testování pro různé úrovně zatížení, ale současně nechcete a udržovat neustále velký počet fyzických počítačů.

Následující diagram ukazuje, jak můžete umístit jednoduché 3vrstvé aplikace v Azure tak, že jednotlivých vrstev aplikace v různých virtuálních počítačů.

![3vrstvý vzor aplikací](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

V tomto modelu aplikace je jenom jeden virtuální počítač (VM) v každé úrovni. Pokud máte více virtuálních počítačů v Azure, doporučujeme, abyste nastavili virtuální sítě. [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) vytvoří hranici zabezpečení pro důvěryhodného a také umožňuje virtuálním počítačům komunikovat mezi sebou přes privátní IP adresu. Kromě toho vždy ujistěte se, že všechna připojení k Internetu jdou jenom do prezentační vrstvy. Pokud projdete tohoto modelu aplikace, spravovat pravidla skupiny zabezpečení sítě pro řízení přístupu. Další informace najdete v tématu [povolení externího přístupu k vašemu virtuálnímu počítači pomocí webu Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

V diagramu může být Internetové protokoly TCP, UDP, HTTP nebo HTTPS.

> [!NOTE]
> Vytvoření virtuální sítě v Azure je zdarma. Nicméně bude vám účtována brány sítě VPN, která se připojuje k místnímu. Tento poplatek podle množství času, které je zřízená a dostupná připojení.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>úrovně 2 a 3 vrstvy s prezentační vrstvy horizontální navýšení kapacity
V tomto modelu aplikace nasadíte databázové vrstvy 2 nebo 3 vrstvy aplikace na Azure Virtual Machines tak, že jednotlivých vrstev aplikace v různých virtuálních počítačů. Kromě toho horizontální navýšení kapacity prezentační vrstvy z důvodu zvýšení objemu příchozí požadavky klientů.

Tento model aplikace je užitečné, když:

* Chcete přesunout firemních aplikací z místních virtualizovaných platforem Azure Virtual Machines.
* Chcete pro horizontální navýšení kapacity prezentační vrstvy z důvodu zvýšení objemu příchozí požadavky klientů.
* Chcete pro rychlé zřizování vývojových a testovacích prostředí pro krátkou dobu.
* Můžete provádět zátěžové testování pro různé úrovně zatížení, ale současně nechcete a udržovat neustále velký počet fyzických počítačů.
* Chcete vlastní prostředí infrastruktury, ve kterém můžete škálovat nahoru a dolů na vyžádání.

Následující diagram ukazuje, jak můžete umístit aplikačních vrstev v několika virtuálních počítačů v Azure díky horizontálnímu navýšení kapacity prezentační vrstvy z důvodu zvýšení objemu příchozí požadavky klientů. Jak je vidět v diagramu, je zodpovědný za distribuci provozu napříč několika virtuálními počítači a také určení, které webový server pro připojení k Azure Load Balancer. Má několik instancí webových serverů za nástrojem pro vyrovnávání zatížení poskytuje vysokou dostupnost prezentační vrstvy.

![Vzor aplikací – prezentační vrstva horizontální navýšení kapacity](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Osvědčené postupy pro vrstvy 2, 3 vrstvy nebo n vrstvá vzory, které mají více virtuálních počítačů v jedné úrovni
Doporučuje se, že při umisťování virtuálních počítačů, které patří do stejné úrovně ve stejné cloudové službě a ve stejném dostupnost nastavit. Například umístit sadu webových serverů **CloudService1** a **AvailabilitySet1** a sada databázových serverů v **CloudService2** a  **AvailabilitySet2**. Dostupnosti v Azure umožňuje uzly vysoké dostupnosti umístěte do samostatných doménách selhání a upgradovacími doménami.

Využívat více instancí virtuálního počítače do vrstvy, musíte nakonfigurovat nástroj pro vyrovnávání zatížení Azure mezi aplikačními vrstvami. Ke konfiguraci nástroje pro vyrovnávání zatížení na jednotlivých úrovních, vytvořte koncový bod s vyrovnáváním zatížení na jednotlivých úrovních virtuálních počítačích samostatně. Konkrétní úrovni nejprve vytvořte virtuální počítače ve stejné cloudové službě. Tím se zajistí, že mají stejnou veřejnou virtuální IP adresu. Dále vytvořte koncový bod v jednom z virtuálních počítačů na dané úrovni. Pak přiřaďte stejný koncový bod jinými virtuálními počítači v dané úrovni pro vyrovnávání zatížení. Vytvořením sady s vyrovnáváním zatížení distribuovat provoz napříč několika virtuálními počítači a také povolit nástroj pro vyrovnávání zatížení určit, který uzel k připojení při selhání uzlu virtuálního počítače back-endu. Například má více instancí webových serverů za nástrojem pro vyrovnávání zatížení zajišťuje vysokou dostupnost prezentační vrstvy.

Jako osvědčený postup vždy ujistěte se, že všechna připojení k Internetu nejprve přejděte do prezentační vrstvy. Prezentační vrstva přistupuje k nim obchodní vrstvou a potom obchodní vrstvy přistupuje k datové vrstvě. Další informace o tom, jak povolit přístup do prezentační vrstvy najdete v tématu [povolení externího přístupu k vašemu virtuálnímu počítači pomocí webu Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Všimněte si, že nástroj pro vyrovnávání zatížení v Azure funguje podobně jako v místním prostředí vyrovnávání zatížení. Další informace najdete v tématu [Vyrovnávání zatížení pro služby infrastruktury Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Kromě toho doporučujeme nastavit si privátní sítě pro virtuální počítače pomocí Azure Virtual Network. To umožňuje, aby mohla komunikovat mezi sebou přes privátní IP adresu. Další informace najdete v tématu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>úrovně 2 a 3 vrstvy s obchodní vrstvy horizontální navýšení kapacity
V tomto modelu aplikace nasadíte databázové vrstvy 2 nebo 3 vrstvy aplikace na Azure Virtual Machines tak, že jednotlivých vrstev aplikace v různých virtuálních počítačů. Kromě toho můžete distribuovat aplikace součásti serveru na několika virtuálních počítačů z důvodu složitosti aplikace.

Tento model aplikace je užitečné, když:

* Chcete přesunout firemních aplikací z místních virtualizovaných platforem Azure Virtual Machines.
* Chcete distribuovat aplikace součásti serveru na několika virtuálních počítačů z důvodu složitosti aplikace.
* Chcete přesunout obchodní logiky těžkých místní aplikace LOB (-obchodní) na Azure Virtual Machines. OBCHODNÍ aplikace jsou sady aplikací pro kritické počítače, které jsou nezbytné ke spuštění podniku, jako je například monitorování účtů, lidských zdrojů (HR), mezd, Správa dodavatelského řetězce a prostředek plánování aplikace.
* Chcete pro rychlé zřizování vývojových a testovacích prostředí pro krátkou dobu.
* Můžete provádět zátěžové testování pro různé úrovně zatížení, ale současně nechcete a udržovat neustále velký počet fyzických počítačů.
* Chcete vlastní prostředí infrastruktury, ve kterém můžete škálovat nahoru a dolů na vyžádání.

Následující diagram ukazuje scénáři místní a jeho řešení cloud je povolený. V tomto scénáři je umístit aplikačních vrstev v několika virtuálních počítačů v Azure díky horizontálnímu navýšení kapacity obchodní vrstvy, který obsahuje vrstvu obchodní logiky a součásti pro přístup k datům. Jak je vidět v diagramu, je zodpovědný za distribuci provozu napříč několika virtuálními počítači a také určení, které webový server pro připojení k Azure Load Balancer. Má několik instancí aplikačních serverů za nástrojem pro vyrovnávání zatížení poskytuje vysokou dostupnost obchodní vrstvy. Další informace najdete v tématu [osvědčené postupy pro vrstvy 2, 3 vrstvy nebo n vrstvá aplikace vzory, které mají více virtuálních počítačů v jedné úrovni](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Vzoru aplikace s obchodní vrstvy horizontální navýšení kapacity](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>úrovně 2 a 3 vrstvy s prezentace a obchodní vrstvy horizontální navýšení kapacity a HADR
V tomto vzoru aplikaci nasadíte databázové vrstvy 2 nebo 3 vrstvy aplikace na Azure Virtual Machines díky distribuci prezentační vrstvy (webový server) a komponenty obchodní vrstvy (aplikačního serveru) k několika virtuálním počítačům. Kromě toho implementovat řešení pro zotavení po havárii a s vysokou dostupností pro vaše databáze v Azure virtual machines.

Tento model aplikace je užitečné, když:

* Chcete přesunout místních virtualizovaných platforem podnikové aplikace do Azure pomocí implementace vysoké dostupnosti pro SQL Server a možnosti zotavení po havárii.
* Chcete pro horizontální navýšení kapacity prezentační vrstvou a obchodní vrstvy z důvodu zvýšení objemu příchozí požadavky klientů a složitosti vaší aplikace.
* Chcete pro rychlé zřizování vývojových a testovacích prostředí pro krátkou dobu.
* Můžete provádět zátěžové testování pro různé úrovně zatížení, ale současně nechcete a udržovat neustále velký počet fyzických počítačů.
* Chcete vlastní prostředí infrastruktury, ve kterém můžete škálovat nahoru a dolů na vyžádání.

Následující diagram ukazuje scénáři místní a jeho řešení cloud je povolený. V tomto scénáři jste horizontální navýšení kapacity prezentační vrstvou a obchodní vrstvy součástí několika virtuálních počítačů v Azure. Kromě toho implementovat vysokou dostupnost a obnovení (HADR) techniky pro databáze serveru SQL v Azure.

Spuštění více kopií aplikace v různých virtuálních počítačů Ujistěte se, že se jejich požadavky Vyrovnávání zatížení. Až budete mít několik virtuálních počítačů, budete muset ověřit, že všechny virtuální počítače jsou přístupná a běží na jednom místě v čase. Při konfiguraci Vyrovnávání zatížení, Azure Load Balancer sleduje stav virtuálních počítačů a směruje příchozí volání uzly v dobrém stavu virtuálního počítače funguje správně. Informace o tom, jak nastavit vyrovnávání zatížení virtuálních počítačů najdete v tématu [Vyrovnávání zatížení pro služby infrastruktury Azure](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Má několik instancí webových a aplikačních serverů za nástrojem pro vyrovnávání zatížení poskytuje vysokou dostupnost prezentační a obchodní vrstvy.

![Horizontální navýšení kapacity a vysoká dostupnost](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Osvědčené postupy pro vzory aplikací vyžadujících SQL HADR
Když nastavíte vysokou dostupnost serveru SQL Server a řešení zotavení po havárii ve službě Azure Virtual Machines, nastavení virtuální sítě pro virtuální počítače pomocí [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) je povinné.  Virtuální počítače v rámci virtuální sítě budou mít stabilní privátní IP adresy i potom, co výpadky služby, proto se můžete vyhnout aktualizace čas potřebný pro překlad názvů DNS. Virtuální sítě navíc umožňuje rozšířit vaše místní síť do Azure a vytvoří hranici zabezpečení důvěryhodné. Pokud vaše aplikace má omezení podnikové doméně (například Windows ověřování služby Active Directory), například nastavení [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) je nezbytné.

Většina zákazníků, kteří spouštějí produkční kód v Azure, jsou primární i sekundární repliky uchovávání v Azure.

Úplné informace a kurzy o vysokou dostupnost a techniky pro zotavení po havárii najdete v tématu [vysokou dostupnost a zotavení po havárii pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>úrovně 2 a 3 vrstvy pomocí virtuálních počítačů Azure a Cloud Services
V tomto modelu aplikace, nasazení vrstvy 2 nebo 3 vrstvy aplikace do Azure pomocí [Azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (webových a pracovních rolí – platforma jako služba (PaaS)) a [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) () Infrastruktura jako služba (IaaS)). Pomocí [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) pro prezentační úroveň nebo obchodní vrstvou a systému SQL Server v [Azure Virtual Machines](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) dat úroveň je přínosné pro většinu aplikací spuštěných v Azure. Důvodem je, že máte výpočetní instance Cloud Services a systémem poskytuje snadnou správu, nasazení, monitorování a škálování na víc systémů.

S cloudovými službami Azure udržuje infrastruktury za vás provádí údržbu, opravy v operačních systémech a pokusí o zotavení z chyb služby a hardwaru. Pokud vaše aplikace potřebuje horizontální navýšení kapacity, automatické a ruční horizontální navýšení kapacity možnosti jsou dostupné pro projekt cloudové služby zvýšením nebo snížením počtu instancí nebo virtuálních počítačů, které jsou používány vaší aplikací. Kromě toho můžete použít místní sady Visual Studio k nasazení aplikace do projektu cloudové služby v Azure.

V souhrnu Pokud už nechcete, vlastnictví rozsáhlé úlohy správy pro prezentační a obchodní vrstvy aplikace není nutná žádná komplexní konfigurace softwaru nebo operačního systému, použít Azure Cloud Services Pokud Azure SQL Database nepodporuje všechny funkce, které hledáte, použijte SQL Server ve virtuálním počítači Azure pro datovou vrstvu. Spuštění aplikace v Azure Cloud Services a ukládání dat ve službě Azure Virtual Machines kombinuje výhody obou služeb. Podrobné porovnání najdete v části v tomto tématu na [porovnání vývojové strategie v Azure](#comparing-web-development-strategies-in-azure).

V tomto modelu aplikace obsahuje prezentační vrstvy webové role, která je spuštěna komponenta Cloud Services v prostředí Azure pro spouštění a je tento příklad upravený pro programování webové aplikace podporuje službu IIS a ASP.NET. Úroveň firmy nebo back-end zahrnuje role pracovního procesu, který komponentu cloudové aplikace běží v prostředí Azure pro spouštění a je užitečné pro vývoj zobecněný a může provádět zpracování na pozadí pro webovou roli. Na úrovni databáze se nachází v virtuálního počítače s SQL serverem v Azure. Přímo nebo přes obchodní vrstvu – součásti role pracovního procesu se stane komunikaci mezi prezentační vrstvou a databázovou vrstvu.

Tento model aplikace je užitečné, když:

* Chcete přesunout místních virtualizovaných platforem podnikové aplikace do Azure pomocí implementace vysoké dostupnosti pro SQL Server a možnosti zotavení po havárii.
* Chcete vlastní prostředí infrastruktury, ve kterém můžete škálovat nahoru a dolů na vyžádání.
* Azure SQL Database nepodporuje všechny funkce, které musí vaše aplikace nebo databáze.
* Můžete provádět zátěžové testování pro různé úrovně zatížení, ale současně nechcete a udržovat neustále velký počet fyzických počítačů.

Následující diagram ukazuje scénáři místní a jeho řešení cloud je povolený. V tomto scénáři je umístit prezentační vrstvy do webové role, obchodní vrstvy v rolích pracovního procesu, ale Datová vrstva ve virtuálních počítačích v Azure. Spuštění více kopií prezentační vrstvy do jiných webových rolí zajišťuje pro vyrovnávání zatížení způsobeného požadavky mezi nimi. Když zkombinujete Azure Cloud Services s Azure Virtual Machines, doporučujeme, abyste nastavili [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) také. S [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), budete mít stabilní a trvalé soukromé IP adresy v rámci stejné cloudové služby v cloudu. Jakmile definujete virtuální sítě pro virtuální počítače a cloudové služby, mohou začít komunikaci mezi servery přes privátní IP adresu. Kromě toho s virtual machines a Azure web/role pracovního procesu ve stejném [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) poskytuje nízkou latenci a lepší zabezpečení připojení. Další informace najdete v tématu [co je Cloudová služba](../../../cloud-services/cloud-services-choose-me.md).

Jak je vidět v diagramu, Azure Load Balancer distribuuje provoz napříč několika virtuálními počítači a také určuje, které webový server nebo připojit k aplikačnímu serveru. Má několik instancí webových a aplikačních serverů za nástrojem pro vyrovnávání zatížení poskytuje vysokou dostupnost prezentační vrstvou a obchodní vrstvou. Další informace najdete v tématu [osvědčených postupů pro vzory aplikace vyžadující SQL HADR](#best-practices-for-application-patterns-requiring-sql-hadr).

![Modely aplikací s cloudovými službami](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Dalším přístupem k implementaci tohoto modelu aplikace je použití konsolidované webová role, která obsahuje prezentační vrstvou a obchodní úroveň komponenty, jak je znázorněno v následujícím diagramu. Tato aplikace model je vhodný pro aplikace, které vyžadují stavové návrhu. Protože Azure poskytuje bezstavové výpočetních uzlů na webových a pracovních rolí, doporučujeme implementovat logiku k ukládání stavu relace pomocí jedné z následujících technologií: [ukládání do mezipaměti Azure](https://azure.microsoft.com/documentation/services/redis-cache/), [Azure Table Storage](../../../cosmos-db/table-storage-how-to-use-dotnet.md) nebo [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).

![Modely aplikací s cloudovými službami](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Vzor se virtuální počítače Azure, Azure SQL Database a Azure App Service (webové aplikace)
Hlavním cílem tohoto modelu aplikace je ukazují, jak kombinovat Azure infrastruktury jako služba (IaaS) komponenty součástí Azure platforma jako služba (PaaS) v rámci vašeho řešení. Tento model se zaměřuje na Azure SQL Database pro relační datové úložiště. Nezahrnuje systému SQL Server ve virtuálním počítači Azure, které je součástí sady Azure infrastruktury jako služby.

V tomto modelu aplikace nasadit aplikaci databáze do Azure pomocí umístění prezentační a obchodní vrstvy do stejného virtuálního počítače a přístup k databázi v servery Azure SQL Database (SQL Database). Prezentační vrstva můžete implementovat pomocí tradičních webové služby IIS řešení. Nebo můžete implementovat kombinované prezentační a obchodní vrstvou pomocí [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Tento model aplikace je užitečné, když:

* Už máte existující server služby SQL Database nakonfigurovaná v Azure a chcete otestovat aplikaci rychle.
* Chcete testovat funkce prostředí Azure.
* Chcete pro rychlé zřizování vývojových a testovacích prostředí pro krátkou dobu.
* Vaše obchodní logiku a data komponenty přístup může být obsaženy v rámci webové aplikace.

Následující diagram ukazuje scénáři místní a jeho řešení cloud je povolený. V tomto scénáři je umístit aplikačních vrstev v jednom virtuálním počítači v Azure a přístup k data ve službě Azure SQL Database.

![Vzor hybridní aplikace](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Pokud se rozhodnete implementovat kombinované webové a aplikační vrstvy pomocí Azure Web Apps, doporučujeme ponechat střední vrstvy nebo aplikační vrstvy jako dynamické knihovny (DLL) v kontextu webové aplikace.

Kromě toho si přečtěte doporučení podle [porovnání webové vývojové strategie v Azure](#comparing-web-development-strategies-in-azure) části na konci tohoto článku budete vědět více o programovacích postupech.

## <a name="n-tier-hybrid-application-pattern"></a>Vzor N-vrstvá hybridních aplikací
Ve vzoru vícevrstvé hybridní aplikace implementaci aplikace ve více vrstvách rozloženy mezi místními a Azure. Vytvoříte tedy flexibilní a opakovaně použitelné hybridního systému, který můžete upravit nebo přidat konkrétní úroveň beze změny na jiných úrovních. Pro rozšíření vaší podnikové sítě do cloudu, můžete použít [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) služby.

Tento model hybridní aplikace je užitečné, když:

* Chcete vytvářet aplikace, které běží částečně v cloudu a částečně místně.
* Chcete migrovat některé nebo všechny prvky stávající místní aplikace do cloudu.
* Chcete přesunout z místních virtualizovaných platforem podnikové aplikace do Azure.
* Chcete vlastní prostředí infrastruktury, ve kterém můžete škálovat nahoru a dolů na vyžádání.
* Chcete pro rychlé zřizování vývojových a testovacích prostředí pro krátkou dobu.
* Chcete, aby nákladově efektivní způsob, jak zálohy pro podnikové aplikace s databází.

Následující diagram ukazuje vzoru aplikace s vícevrstvé hybridní, která zahrnuje místní i Azure. Jak je znázorněno v diagramu, místní infrastruktura zahrnuje [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) řadič domény pro podporu ověřování a autorizaci uživatelů. Všimněte si, že diagram ukazuje scénář, kde některé části datové vrstvy za provozu v datacentru v místním zatímco některé části datové vrstvy za provozu v Azure. V závislosti na potřebách vaší aplikace můžete implementovat několik dalších hybridní scénáře. Například může udržovat prezentační vrstvou a obchodní vrstvou v místním prostředí, ale datové vrstvě v Azure.

![N-vrstvý vzor aplikací](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

V Azure, můžete použít služby Active Directory jako samostatný cloudový adresář pro vaši organizaci nebo můžete také integrovat stávající místní služby Active Directory s [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Jak je vidět v diagramu, součástí obchodní vrstvy můžete přístup k více zdrojům dat, například k [systému SQL Server v Azure](virtual-machines-windows-sql-server-iaas-overview.md) přes privátní interní IP adresa, na místní SQL Server prostřednictvím [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), nebo [SQL Database](../../../sql-database/sql-database-technical-overview.md) pomocí technologií pro zprostředkovatele dat .NET Framework. V tomto diagramu Azure SQL Database je služba storage volitelnými daty.

Ve vzoru vícevrstvé hybridní aplikace můžete implementovat následující pracovní postup v uvedeném pořadí:

1. Určení podnikových databázových aplikací, které je třeba přesunout do cloudu s využitím [Microsoft Assessment and Planning (MAP) Toolkit](https://microsoft.com/map). MAP Toolkit shromažďuje data inventáře a výkonu z počítačů, které uvažujete o pro virtualizaci a poskytuje doporučení na kapacitu a plánování posouzení.
2. Plánování prostředků a konfigurace potřebná na platformě Azure, jako jsou účty úložiště a virtuální počítače.
3. Nastavit připojení k síti mezi podnikové sítě v místním a [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Pokud chcete nastavit připojení mezi podnikovou síť místní a virtuální počítač v Azure, použijte jednu z těchto dvou metod:
   
   1. Navázání připojení mezi místními a Azure prostřednictvím veřejných koncových bodů na virtuálním počítači v Azure. Tato metoda poskytuje snadné nastavení a vám umožní použít ověřování systému SQL Server na virtuálním počítači. Kromě toho nastavte vaše pravidla skupin zabezpečení sítě pro řízení veřejné provoz do virtuálního počítače. Další informace najdete v tématu [povolení externího přístupu k vašemu virtuálnímu počítači pomocí webu Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Navázat připojení přes tunelové propojení Azure virtuální privátní sítě (VPN) mezi místními a Azure. Tato metoda umožňuje rozšířit zásady k virtuálnímu počítači v Azure. Kromě toho lze nastavení pravidel brány firewall a používat ověřování Windows na virtuálním počítači. Azure v současné době podporuje zabezpečené sítě VPN site-to-site a připojení VPN typu point-to-site:
      
      * Pomocí zabezpečeného připojení site-to-site může vytvořit připojení k síti mezi místní sítí a virtuální sítí v Azure. Doporučuje se pro připojení vaší místní prostředí datového centra do Azure.
      * Pomocí zabezpečeného připojení point-to-site může vytvořit připojení k síti mezi vaší virtuální sítě v Azure a jednotlivých počítačů spuštěných kdekoli. Většinou doporučujeme pro účely vývoje a testování.
      
      Informace o tom, jak se připojit k serveru SQL v Azure najdete v tématu [připojit k SQL Server na virtuálním počítači Azure](virtual-machines-windows-sql-connect.md).
4. Nastavení naplánované úlohy a výstrahy, které zálohovat místní data na disku virtuálního počítače v Azure. Další informace najdete v tématu [SQL zálohování a obnovení serveru pomocí služby Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx) a [zálohování a obnovení pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).
5. V závislosti na potřebách vaší aplikace může implementovat jedno z následující tři běžné scénáře:
   
   1. Webový server, aplikační server a nezávislé na data možné ponechat v databázový server v Azure, zatímco uchovávat citlivá data v místním.
   2. Můžete ponechat webového serveru a aplikačního serveru místní vzhledem k tomu databázový server ve virtuálním počítači v Azure.
   3. Můžete ponechat databázový server, server webové a aplikační server místní vzhledem k tomu mít tyto repliky databáze na virtuálních počítačích v Azure. Toto nastavení umožňuje místní webové servery nebo vytváření sestav aplikace pro přístup k repliky databáze v Azure. Proto můžete dosáhnout snížení zatížení v místní databázi. Doporučujeme vám, že tento scénář pro těžké implementovat číst účely vývoje a úlohy. Informace o vytvoření repliky databáze v Azure, najdete v části skupiny dostupnosti AlwaysOn na [vysokou dostupnost a zotavení po havárii pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Porovnání webové vývojové strategie v Azure
K implementaci a nasazení vícevrstvé aplikace založené na SQL serveru v Azure, můžete použít jednu z těchto dvou programovacích metod:

* Nastavte tradiční webový server (IIS - Internetová informační služba) v Azure a přístupu k databázím serveru SQL Server ve službě Azure Virtual Machines.
* Implementace a nasadit cloudovou službu do Azure. Ujistěte se, že tuto cloudovou službu můžete přístup k databázím v systému SQL Server ve službě Azure Virtual machines. Cloudové služby může obsahovat několik webových a pracovních rolí.

Následující tabulka obsahuje porovnání tradičními webovými vývoj s Azure Cloud Services a Azure Web Apps s ohledem na SQL Server ve službě Azure Virtual Machines. Tabulka obsahuje Azure Web Apps, protože je možné použít SQL Server na virtuálním počítači Azure jako zdroj dat pro Azure Web Apps přes jeho veřejné virtuální IP adresa nebo název DNS.

|  | Vývoj pro tradiční web ve službě Azure Virtual Machines | Cloudové služby v Azure | Webhosting s Azure Web Apps |
| --- | --- | --- | --- |
| **Migrace aplikací z místního** |Stávající aplikace jako-je. |Aplikace potřebují webových a pracovních rolí. |Stávající aplikace jako-je ale vhodný pro samostatná webových aplikací a webových služeb, které vyžadují rychlou škálovatelnost. |
| **Vývoj a nasazení** |Visual Studio, Webmatrixu, Visual Web Developer, WebDeploy, FTP, TFS, Správce služby IIS, prostředí PowerShell. |Visual Studio, sady Azure SDK, TFS, prostředí PowerShell. U každé cloudové služby má dvě prostředí, do kterých můžete nasadit balíček služby a konfigurace: přípravným a produkčním prostředím. Cloudovou službu můžete nasadit do přípravného prostředí a otestovat ho před Přesun do produkčního prostředí. |Visual Studio, Webmatrixu, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, webové nasazení, prostředí PowerShell. |
| **Správa a nastavení** |Zodpovídáte za úlohy správy na aplikace, data, pravidla brány firewall, virtuální sítě a operační systém. |Zodpovídáte za úlohy správy na aplikace, data, pravidla brány firewall a virtuální sítě. |Zodpovídáte za úlohy správy na pouze data a aplikace. |
| **Vysoká dostupnost a zotavení po havárii (HADR)** |Doporučujeme umísťovat virtuální počítače ve stejné skupině dostupnosti a ve stejné cloudové službě. Zachování vašich virtuálních počítačů ve stejné skupině dostupnosti umožňují Azure uzly vysoké dostupnosti umístěte do samostatných doménách selhání a upgradovacími doménami. Podobně udržování vašich virtuálních počítačů ve stejné cloudové službě umožňuje Vyrovnávání zatížení a virtuální počítače mohou komunikovat přímo s možností vzájemné v místní síti v rámci datového centra Azure.<br/><br/>Zodpovídáte za implementaci vysoké dostupnosti a řešení zotavení po havárii pro SQL Server ve službě Azure Virtual Machines žádné výpadky. Podpora technologií HADR, naleznete v tématu [vysokou dostupnost a zotavení po havárii pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Zodpovídáte za zálohování dat a aplikací.<br/><br/>Azure můžou přesunout virtuální počítače, pokud se hostitelský počítač v datovém centru nezdaří kvůli problémům hardwaru. Kromě toho může existovat plánované výpadky virtuálního počítače při aktualizaci hostitelského počítače pro zabezpečení nebo softwarové aktualizace. Proto doporučujeme udržovat alespoň dva virtuální počítače v jednotlivých vrstev aplikace zajistit trvalou dostupnost. Azure neposkytuje smlouva SLA pro jeden virtuální počítač. |Azure spravuje selhání vyplývající z podkladové softwaru hardware nebo operační systém. Doporučujeme vám, že implementujete víc instancí webové nebo pracovní role k zajištění vysoké dostupnosti vaší aplikace. Informace najdete v tématu [Cloud Services, Virtual Machines a smlouvu o úrovni služeb virtuální sítě](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Zodpovídáte za zálohování dat a aplikací.<br/><br/>Pro databáze, které se nacházejí v databázi serveru SQL Server na Virtuálním počítači Azure zodpovídáte za implementaci vysokou dostupnost a obnovení řešení žádné výpadky. Podpora technologií HDAR naleznete v tématu vysokou dostupnost a zotavení po havárii pro SQL Server ve službě Azure Virtual Machines.<br/><br/>**Zrcadlení databáze serveru SQL**: použití se službou Azure Cloud Services (webové nebo pracovní role). Virtuální počítače s SQL serverem a projekt cloudové služby můžou být ve stejné virtuální síti Azure. Pokud virtuální počítač s SQL serverem není ve stejné virtuální síti, musíte vytvořit Alias serveru SQL pro směrování komunikace do instance systému SQL Server. Kromě toho název aliasu musí odpovídat názvu serveru SQL Server. |Vysoká dostupnost je zděděno z role pracovního procesu systému Azure, Azure blob storage a Azure SQL Database. Například Azure Storage uchovává tři repliky všech objektů blob, tabulky a data ve frontě. V jednu chvíli, Azure SQL Database uchovává tří replik dat s – jedna primární replika a dva sekundární repliky. Další informace najdete v tématu [služby Azure Storage](https://azure.microsoft.com/documentation/services/storage/) a [Azure SQL Database](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Při používání SQL serveru na virtuálním počítači Azure jako zdroj dat pro Azure Web Apps, mějte na paměti, že Azure Web Apps nepodporuje Azure Virtual Network. Jinými slovy všechna připojení z Azure Web Apps pro virtuální počítače s SQL serverem v Azure musí procházet přes veřejné koncové body virtuálních počítačů. Tato akce může způsobit určitá omezení pro zajištění vysoké dostupnosti a scénáře zotavení po havárii. Například klientská aplikace ve službě Azure Web Apps připojení k virtuálnímu počítači SQL serveru pomocí zrcadlení databáze nebude moct připojit k novým primárním serverem, protože zrcadlení databáze vyžaduje, abyste nastavili Azure Virtual Network mezi virtuálními počítači hostitele systému SQL Server v Azure. Proto pomocí **zrcadlení databáze serveru SQL** s Azure Web Apps v tuto chvíli nepodporuje.<br/><br/>**Skupiny dostupnosti AlwaysOn SQL serveru**: skupin dostupnosti AlwaysOn můžete nastavit, pokud virtuální počítače s SQL serverem v Azure pomocí Azure Web Apps. Ale budete muset nakonfigurovat naslouchací proces skupiny dostupnosti AlwaysOn pro směrování komunikace k primární replice prostřednictvím veřejných koncových bodů s vyrovnáváním zatížení. |
| **připojení mezi místními sítěmi** |Azure Virtual Network můžete použít pro připojení k místní. |Azure Virtual Network můžete použít pro připojení k místní. |Virtuální síť Azure je podporována. Další informace najdete v tématu [integrace virtuální sítě webových aplikací](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Škálovatelnost** |Vertikální škálování je k dispozici zvýšení velikosti virtuálních počítačů nebo přidáním dalších disků. Další informace o velikostech virtuálních počítačů najdete v tématu [velikostí virtuálních počítačů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Pro databázový Server**: horizontální navýšení kapacity je k dispozici prostřednictvím techniky dělení databáze a skupiny dostupnosti AlwaysOn pro SQL Server.<br/><br/>Pro náročné úlohy čtení, můžete použít [skupin dostupnosti AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) na několika sekundárních uzlů a také replikace systému SQL Server.<br/><br/>Pro úlohy náročné zápisu můžete implementovat horizontální dělení dat napříč několika fyzickými servery k poskytování horizontální navýšení kapacity aplikace.<br/><br/>Kromě toho můžete implementovat Škálováním pomocí [SQL serveru pomocí směrování závislé na datech](https://technet.microsoft.com/library/cc966448.aspx). Pomocí dat závislé směrování (DDR), budete muset implementovat mechanismus dělení v klientské aplikaci, obvykle v obchodní vrstvě vrstvy, směrovat požadavky databáze do více uzlů serveru SQL Server. Obchodní vrstva obsahuje mapování na tom, jak je data rozdělit na oddíly a který uzel obsahuje data.<br/><br/>Je možné škálovat aplikace, které jsou spuštěné virtuální počítače. Další informace najdete v tématu [jak škálovat aplikaci](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Důležitá poznámka**: **automatického škálování** funkce v Azure umožňuje automaticky zvětšit nebo zmenšit virtuální počítače, které jsou používány vaší aplikace. Tato funkce zaručuje, že činnost koncového uživatele nemá negativní vliv během špiček, a virtuální počítače jsou vypnutí při nízkém vyžádání. Je doporučeno, nenastavujte možnost automatického škálování pro cloudovou službu, pokud obsahuje virtuální počítače s SQL serverem. Důvodem je, že funkce automatického škálování umožňuje zapnout virtuální počítač při větším než některé prahová hodnota využití procesoru v tomto virtuálním počítači a chcete-li vypnout virtuální počítač při využití procesoru nižší než Azure. Funkce automatického škálování je užitečné pro bezstavové aplikace, jako jsou třeba webové servery, kde jakýkoli virtuální počítač můžete spravovat úlohy bez všechny odkazy na všechny předchozí stav. Funkce automatického škálování, ale není užitečné stavových aplikací, jako je SQL Server, kde pouze jedna instance umožňuje zápis do databáze. |Vertikální škálování je k dispozici prostřednictvím více webových a pracovních rolí. Další informace o velikostech virtuálních počítačů pro webové role a role pracovního procesu najdete v tématu [konfigurace velikosti pro Cloud Services](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Při použití **Cloud Services**, můžete definovat víc rolí k distribuci zpracování. dosáhnete také flexibilní škálování vaší aplikace. U každé cloudové služby obsahuje jeden nebo více webové role nebo role pracovního procesu, každá má své vlastní soubory aplikace a konfigurace. Můžete vertikálně navýšit kapacitu cloudovou službu zvýšením počtu instancí role (virtuální počítače) nasazené pro roli a vertikální snížení kapacity cloudovou službu snížením počtu instancí role. Podrobné informace najdete v tématu [spouštění modelů Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Horizontální navýšení kapacity je k dispozici prostřednictvím podpory Azure integrovanou vysokou dostupnost prostřednictvím [Cloud Services, Virtual Machines a smlouvu o úrovni služeb virtuální sítě](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) a nástroje pro vyrovnávání zatížení.<br/><br/>Pro vícevrstvou aplikaci doporučujeme vám, že se můžete připojit web/worker role aplikace k databázi serveru virtuálních počítačů pomocí Azure Virtual Network. Kromě toho Azure poskytuje Vyrovnávání zatížení pro virtuální počítače ve stejné cloudové službě, rozšíření uživatelských požadavků mezi nimi. Virtuální počítače připojené tímto způsobem může komunikovat přímo s sebou v místní síti v rámci datového centra Azure.<br/><br/>Můžete nastavit **automatického škálování** na webu Azure portal, jakož i časů plánu. Další informace najdete v tématu [konfigurace automatického škálování pro Cloudovou službu na portálu](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Škálovat nahoru a dolů**: vám může zvětšit nebo zmenšit velikost instance (VM) vyhrazené pro váš web.<br/><br/>Horizontální navýšení kapacity: můžete přidat více rezervovaných instancí (VM) pro webový server.<br/><br/>Můžete nastavit **automatického škálování** na portálu, jakož i časů plánu. Další informace najdete v tématu [postupy škálování webové aplikace](../../../app-service/web-sites-scale.md). |

Další informace o volbě mezi tyto programovací metody, naleznete v tématu [Azure Web Apps, Cloud Services a virtuální počítače: kdy co použít](../../../app-service/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Další kroky
Další informace o spouštění systému SQL Server ve službě Azure Virtual machines, najdete v části [SQL Server na Azure Virtual Machines Overview](virtual-machines-windows-sql-server-iaas-overview.md).

