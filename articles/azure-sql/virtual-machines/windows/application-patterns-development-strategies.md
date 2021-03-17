---
title: SQL Server vzorové aplikace na virtuálních počítačích | Microsoft Docs
description: Tento článek popisuje vzory aplikací pro SQL Server v Azure Virtual Machines. Poskytuje architektům řešení a vývojářům základ pro dobrou architekturu a návrh aplikace.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: e877f775c6a7bc83ce6c4a559c9f74b606c952fc
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359485"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-on-azure-virtual-machines"></a>Modely aplikací a vývojové strategie pro SQL Server v Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Souhrn:
Rozhodnutí, které modely nebo vzory aplikací, které se mají použít pro aplikace založené na SQL Server v prostředí Azure, je důležité rozhodnutí o návrhu. vyžaduje se přitom plně pochopit, jak SQL Server a jednotlivé komponenty infrastruktury Azure spolupracují. Díky SQL Server ve službách infrastruktury Azure můžete snadno migrovat, udržovat a monitorovat stávající aplikace SQL Server vytvořené na Windows serveru na virtuální počítače v Azure.

Cílem tohoto článku je poskytnout architektům řešení a vývojářům základ pro dobrou architekturu a návrh aplikací, které můžou postupovat při migraci stávajících aplikací do Azure a při vývoji nových aplikací v Azure.

Pro každý model aplikace najdete místní scénář, jeho příslušné řešení s podporou cloudu a související technická doporučení. Článek navíc popisuje strategie vývoje specifické pro Azure, abyste mohli správně navrhovat aplikace. Vzhledem k mnoha možným vzorům aplikací doporučujeme, aby architekti a vývojáři zvolili nejvhodnější model pro své aplikace a uživatele.

**Techničtí přispěvatelé:** Luis Carlos Vargas sleď, Madhan Arumugam Ramakrishnan

**Techničtí kontroloři:** Corey Sanders, nakreslilo McDaniel, Narayan Annamalai, jmenuji Nir a Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin jin

## <a name="introduction"></a>Úvod
Můžete vyvíjet mnoho typů n-vrstvých aplikací oddělením komponent různých vrstev aplikace na různých počítačích a také v samostatných součástech. Například můžete umístit součásti klientských aplikací a obchodních pravidel do jednoho počítače, do front-endové součásti webové vrstvy a úrovně přístupu k datům v jiném počítači a na úrovni back-end databáze v jiném počítači. Tento druh struktury pomáhá izolovat každou vrstvu od sebe navzájem. Pokud změníte místo, odkud data pocházejí, nemusíte měnit klienta nebo webovou aplikaci, ale pouze součásti úrovně přístupu k datům.

Typická *n-vrstvá* aplikace zahrnuje prezentační vrstvu, obchodní vrstvu a datovou vrstvu:

| Úroveň | Popis |
| --- | --- |
| **Zobrazení** |*Prezentační vrstva* (webová vrstva, front-end úroveň) je vrstva, ve které uživatelé pracují s aplikací. |
| **Firemní** |*Obchodní vrstva* (střední vrstva) je vrstva, kterou prezentační vrstva a Datová vrstva používá ke komunikaci mezi sebou a zahrnuje základní funkce systému. |
| **Data** |*Datová vrstva* je v podstatě Server, na kterém jsou uložena data aplikace (například server se systémem SQL Server). |

Aplikační vrstvy popisují logická seskupení funkcí a komponent v aplikaci. vzhledem k tomu, že úrovně popisují fyzickou distribuci funkcí a komponent na samostatných fyzických serverech, počítačích, sítích nebo vzdálených umístěních. Vrstvy aplikace se mohou nacházet ve stejném fyzickém počítači (stejné vrstvě) nebo mohou být distribuovány přes samostatné počítače (n-vrstvou) a komponenty v jednotlivých vrstvách komunikují s komponentami v jiných vrstvách prostřednictvím dobře definovaných rozhraní. Pojem úrovně můžete představit jako odkaz na způsoby fyzické distribuce, jako je například dvě vrstva, tři vrstvy a n-vrstvá. **Vzor vícevrstvé aplikace** obsahuje dvě aplikační vrstvy: aplikační server a databázový server. Mezi aplikačním serverem a databázovým serverem dojde k přímé komunikaci. Aplikační server obsahuje komponenty webové i obchodní vrstvy. Ve **vzoru aplikace na 3 vrstvě** existují tři aplikační vrstvy: webový server, aplikační server, který obsahuje vrstvu obchodní logiky nebo komponenty pro přístup k datům obchodních vrstev a databázový server. Komunikace mezi webovým serverem a databázovým serverem probíhá prostřednictvím aplikačního serveru. Podrobné informace o vrstvách a úrovních aplikace najdete v tématu [Průvodce architekturou aplikací Microsoftu](/previous-versions/msp-n-p/ff650706(v=pandp.10)).

Než začnete s čtením tohoto článku, měli byste si být vědomi základních konceptů SQL Server a Azure. Informace najdete v tématu [SQL Server Knihy online](/sql/sql-server/), [SQL Server v Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) a [Azure.com](https://azure.microsoft.com/).

Tento článek popisuje několik vzorů aplikací, které mohou být vhodné pro vaše jednoduché aplikace a také pro vysoce složité podnikové aplikace. Před podrobnostem každého vzoru doporučujeme, abyste se seznámili s dostupnými službami datových úložišť v Azure, jako jsou [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL Database](../../database/sql-database-paas-overview.md)a [SQL Server na virtuálním počítači Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Aby se zajistilo, že se pro vaše aplikace vylepší rozhodnutí o návrhu, a to podle toho, kdy se služba Data Storage jasně používá.

### <a name="choose-sql-server-on-azure-virtual-machines-when"></a>V Azure Virtual Machines vyberte SQL Server, když:

* Potřebujete mít pod kontrolou SQL Server a Windows. To může například zahrnovat verzi SQL Server, speciální opravy hotfix, konfiguraci výkonu atd.
* Potřebujete plnou kompatibilitu s SQL Server a chcete přesunout existující aplikace do Azure tak, jak jsou.
* Chcete využívat možnosti prostředí Azure, ale Azure SQL Database nepodporuje všechny funkce, které vaše aplikace vyžaduje. To může zahrnovat následující oblasti:
  
  * **Velikost databáze**: v době, kdy se tento článek aktualizoval, SQL Database podporuje databázi o velikosti až 1 TB dat. Pokud vaše aplikace vyžaduje více než 1 TB dat a nechcete implementovat vlastní řešení horizontálního dělení, doporučujeme použít SQL Server na virtuálním počítači Azure. Nejnovější informace najdete v tématu horizontální navýšení kapacity [Azure SQL Database](/previous-versions/azure/dn495641(v=azure.100)), [nákupní model založený na DTU](../../database/service-tiers-dtu.md)a [vCoreý model nákupu](../../database/service-tiers-vcore.md)(Preview).
  * **HIPAA dodržování předpisů**: zdravotní péče a nezávislí výrobci softwaru (ISV) mohou zvolit [SQL Server v azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) namísto [Azure SQL Database](../../database/sql-database-paas-overview.md) , protože SQL Server na Azure Virtual Machines se zabývá smlouvou HIPAA Business přidružte Agreement (Baa). Informace o dodržování předpisů najdete v tématu [Microsoft Azure Trust Center: dodržování předpisů](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Funkce na úrovni instance**: v současné době SQL Database nepodporuje funkce, které jsou živé mimo databázi (například propojené servery, úlohy agenta, FileStream, Service Broker atd.). Další informace najdete v tématu [pokyny a omezení pro Azure SQL Database](/previous-versions/azure/ff394102(v=azure.100)).

## <a name="1-tier-simple-single-virtual-machine"></a>1 vrstva (jednoduchá): jeden virtuální počítač
V tomto modelu aplikace nasadíte svou aplikaci SQL Server a databázi do samostatného virtuálního počítače v Azure. Stejný virtuální počítač obsahuje vaše klientské/webové aplikace, obchodní komponenty, vrstvy přístupu k datům a databázový server. Kód pro prezentace, obchodní a přístup k datům je logicky oddělený, ale fyzicky se nachází v počítači s jedním serverem. Většina zákazníků začíná s tímto modelem aplikace a následně se škáluje přidáním dalších webových rolí nebo virtuálních počítačů do systému.

Tento model aplikace je užitečný v těchto případech:

* Chcete provést jednoduchou migraci na platformu Azure a vyhodnotit, jestli platforma odpovídá požadavkům vaší aplikace, nebo ne.
* Chcete zachovat všechny aplikační vrstvy hostované ve stejném virtuálním počítači ve stejném datovém centru Azure, abyste snížili latenci mezi úrovněmi.
* Chcete rychle zřídit vývojové a testovací prostředí pro krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně úloh, ale zároveň nechcete, aby se všechny fyzické počítače udržovaly po celou dobu.

Následující diagram znázorňuje jednoduchý místní scénář a způsob nasazení jeho řešení s podporou cloudu na jednom virtuálním počítači v Azure.

![model 1 vrstvy aplikace](./media/application-patterns-development-strategies/IC728008.png)

Nasazení obchodní vrstvy (komponenty Business Logic a Data Access) na stejné fyzické úrovni, když je prezentační vrstva, může maximalizovat výkon aplikace, pokud nemusíte používat samostatnou vrstvu z důvodu škálovatelnosti nebo zabezpečení.

Vzhledem k tomu, že se jedná o velmi společný vzor, který je možné začít používat, můžete najít následující článek o migraci, která je užitečná pro přesunutí vašich dat na virtuální počítač SQL Server: [migrace databáze na SQL Server na virtuálním počítači Azure](migrate-to-vm-from-sql-server.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 – vrstva (jednoduchá): více virtuálních počítačů
V tomto modelu aplikace nasadíte na Azure na jiném virtuálním počítači vrstvu aplikace na 3 vrstvě. Díky tomu získáte flexibilní prostředí pro snadné škálování a škálování na více instancí. Pokud jeden virtuální počítač obsahuje vaše klientské/webové aplikace, druhý hostitel, který je součástí vaší firmy, a druhý, který je hostitelem databázového serveru.

Tento model aplikace je užitečný v těchto případech:

* Chcete provést migraci komplexních databázových aplikací do Azure Virtual Machines.
* Chcete, aby různé aplikační vrstvy byly hostovány v různých oblastech. Můžete mít například sdílené databáze nasazené do více oblastí pro účely generování sestav.
* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do Azure Virtual Machines. Podrobné informace o podnikových aplikacích najdete v tématu [co je podniková aplikace](/previous-versions/visualstudio/aa267045(v=vs.60)).
* Chcete rychle zřídit vývojové a testovací prostředí pro krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně úloh, ale zároveň nechcete, aby se všechny fyzické počítače udržovaly po celou dobu.

Následující obrázek ukazuje, jak můžete umístit jednoduchou 3D aplikaci do Azure tím, že na jiný virtuální počítač umístíte každou vrstvu aplikace.

![Model 3 vrstvy aplikace](./media/application-patterns-development-strategies/IC728009.png)

V tomto modelu aplikace je v každé vrstvě jenom jeden virtuální počítač. Pokud máte v Azure více virtuálních počítačů, doporučujeme nastavit virtuální síť. [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) vytvoří hranici důvěryhodného zabezpečení a zároveň umožňuje virtuálním počítačům komunikovat mezi sebou přes privátní IP adresu. Kromě toho vždy zajistěte, aby všechna připojení k Internetu přešla pouze do prezentační vrstvy. Při použití tohoto modelu aplikace spravujte pravidla skupiny zabezpečení sítě, abyste mohli řídit přístup. Další informace najdete v tématu [Povolení externího přístupu k virtuálnímu počítači pomocí Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

V diagramu mohou být protokoly sítě Internet TCP, UDP, HTTP nebo HTTPS.

> [!NOTE]
> Nastavení virtuální sítě v Azure je zdarma. Účtuje se ale za bránu VPN, která se připojuje k místnímu prostředí. Tento poplatek vychází z doby, po kterou je připojení zřízené a dostupné.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>Oboustranná a 3 vrstva s možností škálování na více instancí prezentační vrstvy
V tomto modelu aplikace nasadíte 2 nebo 3-vrstvou databázovou aplikaci do Azure Virtual Machines tím, že každou vrstvu aplikace umístíte na jiný virtuální počítač. Kromě toho můžete škálovat vrstvu prezentace z důvodu zvýšeného objemu příchozích požadavků klientů.

Tento model aplikace je užitečný v těchto případech:

* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do Azure Virtual Machines.
* Chcete škálovat vrstvu prezentace z důvodu zvýšeného objemu příchozích požadavků klientů.
* Chcete rychle zřídit vývojové a testovací prostředí pro krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně úloh, ale zároveň nechcete, aby se všechny fyzické počítače udržovaly po celou dobu.
* Chcete vlastnit prostředí infrastruktury, které se může na vyžádání škálovat nahoru a dolů.

Následující obrázek ukazuje, jak můžete umístit aplikační vrstvy ve více virtuálních počítačích v Azure tím, že nakonfigurujete velikost prezentační vrstvy z důvodu zvýšení objemu příchozích požadavků klientů. Jak je vidět v diagramu, Azure Load Balancer zodpovídá za distribuci provozu napříč několika virtuálními počítači a také určení toho, ke kterému webovému serveru se má připojit. Více instancí webových serverů za nástroj pro vyrovnávání zatížení zajišťuje vysokou dostupnost prezentační vrstvy.

![Vzor aplikace – škála prezentační vrstvy](./media/application-patterns-development-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Osvědčené postupy pro modely na úrovni 2, 3 nebo n vrstev s několika virtuálními počítači v jedné vrstvě
Doporučuje se umístit virtuální počítače, které patří do stejné úrovně, do stejné cloudové služby a do stejné skupiny dostupnosti. Například umístěte sadu webových serverů v **CloudService1** a **AvailabilitySet1** a sadu databázových serverů v **CloudService2** a **AvailabilitySet2**. Skupina dostupnosti v Azure umožňuje umístění uzlů s vysokou dostupností do samostatných domén selhání a upgradovacích domén.

Aby bylo možné využít více instancí virtuálních počítačů vrstvy, je nutné nakonfigurovat Azure Load Balancer mezi aplikačními vrstvami. Pokud chcete nakonfigurovat Load Balancer v jednotlivých vrstvách, vytvořte koncový bod s vyrovnáváním zatížení na virtuálních počítačích jednotlivých vrstev samostatně. V případě určité úrovně nejprve vytvořte virtuální počítače ve stejné cloudové službě. Tím se zajistí, že mají stejnou veřejnou virtuální IP adresu. Dále vytvořte koncový bod na jednom z virtuálních počítačů na dané úrovni. Pak přiřaďte stejný koncový bod k ostatním virtuálním počítačům na této úrovni pro vyrovnávání zatížení. Vytvořením sady s vyrovnáváním zatížení rozšíříte provoz napříč několika virtuálními počítači a zároveň umožníte Load Balancer určit, který uzel se připojí, když dojde k chybě uzlu virtuálního počítače back-endu. Například více instancí webových serverů za nástroj pro vyrovnávání zatížení zajišťuje vysokou dostupnost prezentační vrstvy.

Osvědčeným postupem je vždycky zajistit, aby se všechna připojení k Internetu nejdřív přešla do prezentační vrstvy. Prezentační vrstva přistupuje k obchodní vrstvě a pak obchodní vrstva přistupuje k datové vrstvě. Další informace o povolení přístupu k prezentační vrstvě najdete v tématu [Povolení externího přístupu k virtuálnímu počítači pomocí Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Všimněte si, že Load Balancer v Azure funguje podobně jako služby Vyrovnávání zatížení v místním prostředí. Další informace najdete v tématu [Vyrovnávání zatížení pro služby infrastruktury Azure](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Kromě toho doporučujeme nastavit privátní síť pro virtuální počítače pomocí Azure Virtual Network. To umožňuje vzájemnou komunikaci mezi sebou přes soukromou IP adresu. Další informace najdete v tématu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>škála dvou vrstev a 3 vrstvy s možností škálování na více obchodních vrstev
V tomto modelu aplikace nasadíte na Azure Virtual Machines aplikační databázovou aplikaci ve dvou vrstvách nebo na 3 vrstvě tím, že na jiném virtuálním počítači umístíte každou vrstvu aplikace. Kromě toho můžete chtít distribuovat součásti aplikačního serveru do několika virtuálních počítačů z důvodu složitosti vaší aplikace.

Tento model aplikace je užitečný v těchto případech:

* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do Azure Virtual Machines.
* Chcete distribuovat součásti aplikačního serveru do několika virtuálních počítačů z důvodu složitosti vaší aplikace.
* Do služby Azure Virtual Machines chcete přesunout náročné místní obchodní aplikace (obchodní). Obchodní aplikace jsou sadou důležitých počítačových aplikací, které jsou důležité pro provoz podniku, jako jsou účetní, lidské prostředky (HR), výplaty, Správa dodavatelských řetězců a aplikace pro plánování prostředků.
* Chcete rychle zřídit vývojové a testovací prostředí pro krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně úloh, ale zároveň nechcete, aby se všechny fyzické počítače udržovaly po celou dobu.
* Chcete vlastnit prostředí infrastruktury, které se může na vyžádání škálovat nahoru a dolů.

Následující diagram znázorňuje místní scénář a jeho řešení s podporou cloudu. V tomto scénáři umístíte aplikační vrstvy do několika virtuálních počítačů v Azure tak, že se škáluje na úrovni obchodní vrstvy, která obsahuje vrstvu obchodní logiky a součásti pro přístup k datům. Jak je vidět v diagramu, Azure Load Balancer zodpovídá za distribuci provozu napříč několika virtuálními počítači a také určení toho, ke kterému webovému serveru se má připojit. Více instancí aplikačních serverů za nástroj pro vyrovnávání zatížení zajišťuje vysokou dostupnost obchodní vrstvy. Další informace najdete v tématu [osvědčené postupy pro vícevrstvé nebo n-vrstvé vzory aplikací, které mají více virtuálních počítačů v jedné vrstvě](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Model aplikace s škálováním na více úrovní obchodní vrstvy](./media/application-patterns-development-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>škálování a HADR na úrovni prezentační a obchodní vrstvy
V tomto modelu aplikace nasadíte do Azure Virtual Machines aplikační databázovou aplikaci ve dvou vrstvách nebo na 3 vrstvě, a to distribucí prezentační vrstvy (webového serveru) a součástí obchodní vrstvy (aplikačního serveru) na několik virtuálních počítačů. Kromě toho implementujete řešení vysoké dostupnosti a zotavení po havárii (HADR) pro vaše databáze v Azure Virtual Machines.

Tento model aplikace je užitečný v těchto případech:

* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do Azure tím, že implementujete SQL Server možnosti vysoké dostupnosti a zotavení po havárii.
* Chcete škálovat vrstvu prezentace a obchodní vrstvu z důvodu zvýšeného objemu příchozích požadavků klientů a složitosti vaší aplikace.
* Chcete rychle zřídit vývojové a testovací prostředí pro krátkou dobu.
* Chcete provést zátěžové testování pro různé úrovně úloh, ale zároveň nechcete, aby se všechny fyzické počítače udržovaly po celou dobu.
* Chcete vlastnit prostředí infrastruktury, které se může na vyžádání škálovat nahoru a dolů.

Následující diagram znázorňuje místní scénář a jeho řešení s podporou cloudu. V tomto scénáři můžete škálovat vrstvu prezentace a součásti obchodní vrstvy ve více virtuálních počítačích v Azure. Kromě toho implementujete techniky vysoké dostupnosti a zotavení po havárii (HADR) pro databáze SQL Server v Azure.

Spuštění více kopií aplikace v různých virtuálních počítačích zajistí vyrovnávání zatížení mezi nimi. Pokud máte více virtuálních počítačů, musíte se ujistit, že jsou všechny virtuální počítače přístupné a spuštěné v jednom okamžiku. Pokud nakonfigurujete vyrovnávání zatížení, Azure Load Balancer sleduje stav virtuálních počítačů a správně směruje příchozí volání do funkčních uzlů virtuálních počítačů. Informace o tom, jak nastavit vyrovnávání zatížení virtuálních počítačů, najdete v tématu [Vyrovnávání zatížení pro služby infrastruktury Azure](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Více instancí webových a aplikačních serverů za nástroj pro vyrovnávání zatížení zajišťuje vysokou dostupnost prezentační a obchodní vrstvy.

![Škálování na více instancí a vysoká dostupnost](./media/application-patterns-development-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Osvědčené postupy pro vzorce aplikací vyžadující SQL HADR
Při nastavení SQL Server vysoké dostupnosti a zotavení po havárii ve službě Azure Virtual Machines se jako povinný používá nastavení virtuální sítě pro virtuální počítače pomocí služby [azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) . Virtuální počítače v rámci Virtual Network budou mít stabilní privátní IP adresu i po výpadku služby, takže se můžete vyhnout době aktualizace potřebné k překladu názvů DNS. Kromě toho virtuální síť umožňuje rozšířit vaši místní síť do Azure a vytvořit důvěryhodnou hranici zabezpečení. Například pokud má vaše aplikace omezení firemních domén (například ověřování systému Windows, Active Directory), je nutné nastavit [Virtual Network Azure](../../../virtual-network/virtual-networks-overview.md) .

Většina zákazníků, kteří používají produkční kód v Azure, udržuje primární i sekundární repliky v Azure.

Komplexní informace a kurzy týkající se vysoké dostupnosti a technik zotavení po havárii najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="2-tier-and-3-tier-using-azure-virtual-machines-and-cloud-services"></a>2 – vrstva a 3 – s využitím Azure Virtual Machines a Cloud Services
V tomto vzoru aplikace nasadíte 2 nebo 3-vrstvou aplikaci do Azure pomocí [azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (webové role a role pracovního procesu – platforma jako služba (PaaS)) a [Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (infrastruktura jako služba (IaaS)). Použití [Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) pro prezentační vrstvu/obchodní vrstvu a SQL Server v [Azure Virtual Machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pro datovou vrstvu je výhodné pro většinu aplikací spuštěných v Azure. Důvodem je, že má výpočetní instance spuštěná v Cloud Services poskytuje jednoduchou správu, nasazení, monitorování a škálování na více instancí.

Díky Cloud Services Azure udržuje infrastrukturu za vás, provádí běžnou údržbu, opravuje operační systémy a pokouší se o zotavení po selhání služeb a hardwaru. Pokud vaše aplikace potřebuje pro projekt cloudové služby k dispozici možnosti horizontálního navýšení kapacity, automatické a ruční škálování, můžete zvýšit nebo snížit počet instancí nebo virtuálních počítačů, které vaše aplikace používá. Kromě toho můžete použít místní aplikaci Visual Studio k nasazení aplikace do projektu cloudové služby v Azure.

Pokud nechcete, aby aplikace pro prezentační/obchodní vrstvu vlastní rozsáhlé úlohy správy a vaše aplikace nevyžaduje žádnou složitou konfiguraci softwaru nebo operačního systému, použijte Azure Cloud Services. Pokud Azure SQL Database nepodporuje všechny funkce, které hledáte, použijte pro datovou vrstvu SQL Server na virtuálním počítači Azure. Spuštění aplikace v Azure Cloud Services a ukládání dat do Azure Virtual Machines kombinuje výhody obou služeb. Podrobné porovnání najdete v části v tomto tématu o [porovnání strategií vývoje v Azure](#comparing-web-development-strategies-in-azure).

Prezentační vrstva v tomto modelu aplikace zahrnuje webovou roli, což je Cloud Services Komponenta spuštěná ve spouštěcím prostředí Azure a je přizpůsobená pro programování webových aplikací, jak je podporuje služba IIS a ASP.NET. Úroveň pro firmy nebo back-end zahrnuje roli pracovního procesu, což je Cloud Services Komponenta spuštěná v prostředí provádění Azure a je užitečná pro zobecněný vývoj a může provádět zpracování na pozadí pro webovou roli. Databázová vrstva se nachází ve virtuálním počítači s SQL Server v Azure. Komunikace mezi prezentační vrstvou a databázovou vrstvou probíhá přímo nebo přes komponenty obchodní vrstvy – role pracovního procesu.

Tento model aplikace je užitečný v těchto případech:

* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do Azure tím, že implementujete SQL Server možnosti vysoké dostupnosti a zotavení po havárii.
* Chcete vlastnit prostředí infrastruktury, které se může na vyžádání škálovat nahoru a dolů.
* Azure SQL Database nepodporuje všechny funkce, které vaše aplikace nebo databáze potřebuje.
* Chcete provést zátěžové testování pro různé úrovně úloh, ale zároveň nechcete, aby se všechny fyzické počítače udržovaly po celou dobu.

Následující diagram znázorňuje místní scénář a jeho řešení s podporou cloudu. V tomto scénáři umístíte prezentační vrstvu do webových rolí, do obchodní vrstvy v rolích pracovního procesu, ale do datové vrstvy na virtuálních počítačích v Azure. Spuštění více kopií prezentační vrstvy v různých webových rolích zajišťuje vyrovnávání zatížení žádostí napříč nimi. Když kombinujete Azure Cloud Services s Azure Virtual Machines, doporučujeme, abyste nastavili také [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) . S [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)můžete mít stabilní a trvalé privátní IP adresy v rámci stejné cloudové služby v cloudu. Po definování virtuální sítě pro virtuální počítače a cloudové služby můžou začít komunikovat mezi sebou přes soukromou IP adresu. Kromě toho, že virtuální počítače a role webu nebo pracovního procesu Azure ve stejné [službě azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) poskytují nízkou latenci a bezpečnější konektivitu. Další informace najdete v tématu [co je cloudová služba](../../../cloud-services/cloud-services-choose-me.md).

Jak je vidět v diagramu, Azure Load Balancer distribuuje provoz napříč několika virtuálními počítači a zároveň určuje, ke kterému webovému serveru nebo aplikačnímu serveru se má připojit. Více instancí webových a aplikačních serverů za nástroj pro vyrovnávání zatížení zajišťuje vysokou dostupnost prezentační vrstvy a obchodní vrstvy. Další informace najdete v tématu [osvědčené postupy pro vzorce aplikací vyžadující SQL hadr](#best-practices-for-application-patterns-requiring-sql-hadr).

![Diagram znázorňuje místní fyzické nebo virtuální počítače připojené ke instancím webových rolí ve službě Azure Virtual Network prostřednictvím nástroje pro vyrovnávání zatížení Azure.](./media/application-patterns-development-strategies/IC728013.png)

Dalším přístupem k implementaci tohoto modelu aplikace je použití konsolidované webové role, která obsahuje obě komponenty prezentační vrstvy i obchodní vrstvy, jak je znázorněno v následujícím diagramu. Tento model aplikace je vhodný pro aplikace, které vyžadují stavový návrh. Vzhledem k tomu, že Azure poskytuje bezstavové výpočetní uzly na webových a pracovních rolích, doporučujeme implementovat logiku pro ukládání stavu relace pomocí jedné z následujících technologií: [Azure Caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [azure Table Storage](../../../cosmos-db/tutorial-develop-table-dotnet.md) nebo [Azure SQL Database](../../database/sql-database-paas-overview.md).

![Diagram znázorňuje místní fyzické nebo virtuální počítače připojené k konsolidovaným instancím webových a pracovních rolí ve službě Azure Virtual Network.](./media/application-patterns-development-strategies/IC728014.png)

## <a name="pattern-with-azure-virtual-machines-azure-sql-database-and-azure-app-service-web-apps"></a>Vzor s Azure Virtual Machines, Azure SQL Database a Azure App Service (Web Apps)
Hlavním cílem tohoto modelu aplikace je Ukázat, jak kombinovat komponenty infrastruktury jako služby (IaaS) Azure s komponentami Azure Platform-as-a-Service (PaaS) ve vašem řešení. Tento model se zaměřuje na Azure SQL Database pro relační úložiště dat. Nezahrnuje SQL Server na virtuálním počítači Azure, který je součástí infrastruktury Azure jako nabídky služeb.

V tomto modelu aplikace nasadíte databázovou aplikaci do Azure tím, že umístíte prezentační a obchodní vrstvy do stejného virtuálního počítače a získáte přístup k databázi na serverech Azure SQL Database (SQL Database). Prezentační vrstvu můžete implementovat pomocí tradičních webových řešení založených na službě IIS. Nebo můžete implementovat kombinovanou prezentační a obchodní vrstvu pomocí [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/).

Tento model aplikace je užitečný v těchto případech:

* Už máte v Azure nakonfigurovaný existující SQL Database Server a chcete aplikaci testovat rychle.
* Chcete testovat možnosti prostředí Azure.
* Chcete rychle zřídit vývojové a testovací prostředí pro krátkou dobu.
* Vaše obchodní logika a komponenty pro přístup k datům můžou být v rámci webové aplikace samostatně obsažené.

Následující diagram znázorňuje místní scénář a jeho řešení s podporou cloudu. V tomto scénáři umístíte aplikační vrstvy do jediného virtuálního počítače v Azure a získáte přístup k datům v Azure SQL Database.

![Smíšený vzor aplikace](./media/application-patterns-development-strategies/IC728015.png)

Pokud se rozhodnete implementovat kombinaci webových a aplikačních vrstev pomocí Azure Web Apps, doporučujeme, abyste zachovali střední vrstvu nebo aplikační vrstvu jako knihovny DLL (Dynamic Link Library) v kontextu webové aplikace.

Kromě toho si přečtěte doporučení v části [porovnání strategií vývoje webu v Azure](#comparing-web-development-strategies-in-azure) na konci tohoto článku, kde najdete další informace o technikách programování.

## <a name="n-tier-hybrid-application-pattern"></a>N-vrstvý vzor hybridní aplikace
Ve vzoru hybridní aplikace v n-vrstvách implementujete svou aplikaci na více úrovních distribuovaných mezi místními prostředími a Azure. Proto vytvoříte flexibilní a opakovaně použitelný hybridní systém, který můžete upravit nebo přidat, aniž byste museli měnit ostatní úrovně. Pokud chcete svou podnikovou síť rozšíříte do cloudu, použijte službu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) .

Tento vzor hybridní aplikace je užitečný v těchto případech:

* Chcete sestavovat aplikace, které běží částečně v cloudu, a částečně místně.
* Chcete migrovat některé nebo všechny prvky existující místní aplikace do cloudu.
* Chcete přesunout podnikové aplikace z místních virtualizovaných platforem do Azure.
* Chcete vlastnit prostředí infrastruktury, které se může na vyžádání škálovat nahoru a dolů.
* Chcete rychle zřídit vývojové a testovací prostředí pro krátkou dobu.
* Potřebujete nákladově efektivní způsob, jak vytvářet zálohy pro podnikové databázové aplikace.

Následující diagram znázorňuje model hybridní aplikace v n-vrstvách, který je rozložený mezi místními a Azure. Jak je znázorněno v diagramu, místní infrastruktura zahrnuje [Active Directory Domain Services](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831484(v=ws.11)) řadič domény pro podporu ověřování a autorizace uživatelů. Všimněte si, že diagram znázorňuje scénář, ve kterém některé části datové vrstvy žijí v místním datovém centru, zatímco některé části datové vrstvy jsou v Azure živé. V závislosti na potřebách vaší aplikace můžete implementovat několik dalších hybridních scénářů. Můžete například uchovávat prezentační vrstvu a obchodní vrstvu v místním prostředí, ale datovou vrstvu v Azure.

![Model N-vrstvé aplikace](./media/application-patterns-development-strategies/IC728016.png)

V Azure můžete jako samostatný cloudový adresář pro vaši organizaci použít službu Active Directory, nebo můžete integrovat stávající místní službu Active Directory s [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Jak je vidět v diagramu, komponenty obchodní vrstvy mají přístup k několika zdrojům dat, jako je například [SQL Server v Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) prostřednictvím privátní interní IP adresy, místní SQL Server prostřednictvím [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)nebo [SQL Database](../../database/sql-database-paas-overview.md) technologie poskytovatele dat .NET Framework. V tomto diagramu je Azure SQL Database volitelnou službou data Storage.

Ve vzoru hybridní aplikace v n-vrstvách můžete implementovat následující pracovní postup v zadaném pořadí:

1. Identifikujte podnikové databázové aplikace, které je potřeba přesunout do cloudu pomocí sady [Microsoft Assessment and Planning (map) Toolkit](https://microsoft.com/map). MAPOVá sada shromažďuje data inventáře a výkonu z počítačů, u kterých zvažujete virtualizaci, a poskytuje doporučení pro plánování kapacity a hodnocení.
2. Naplánujte prostředky a konfiguraci potřebné na platformě Azure, jako jsou účty úložiště a virtuální počítače.
3. Nastavte připojení k síti mezi podnikovou a místní sítí a [Virtual Network Azure](../../../virtual-network/virtual-networks-overview.md). Pokud chcete nastavit připojení mezi podnikovou sítí a virtuálním počítačem v Azure, použijte jednu z následujících dvou metod:
   
   1. Navažte spojení mezi místními a Azure prostřednictvím veřejných koncových bodů na virtuálním počítači v Azure. Tato metoda poskytuje snadnou instalaci a umožňuje ve vašem virtuálním počítači používat ověřování SQL Server. Kromě toho nastavte pravidla skupiny zabezpečení sítě pro řízení veřejného provozu virtuálního počítače. Další informace najdete v tématu [Povolení externího přístupu k virtuálnímu počítači pomocí Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Navažte spojení mezi místními a Azure prostřednictvím tunelového připojení virtuální privátní sítě (VPN) Azure. Tato metoda umožňuje rozšiřování zásad domény na virtuální počítač v Azure. Kromě toho můžete nastavit pravidla brány firewall a použít ověřování systému Windows ve vašem virtuálním počítači. V současné době podporuje Azure zabezpečená připojení VPN typu Site-to-site a VPN typu Point-to-site:
      
      * Díky zabezpečenému připojení Site-to-site můžete vytvořit připojení k síti mezi vaší místní sítí a virtuální sítí v Azure. Doporučuje se připojit vaše místní prostředí datového centra k Azure.
      * Díky zabezpečenému připojení Point-to-site můžete vytvořit připojení k síti mezi vaší virtuální sítí v Azure a jednotlivými počítači, na kterých běží kdekoli. Většinou se doporučuje pro účely vývoje a testování.
      
      Informace o tom, jak se připojit k SQL Server v Azure, najdete v tématu [připojení k virtuálnímu počítači s SQL Server v Azure](ways-to-connect-to-sql.md).
4. Nastavte naplánované úlohy a výstrahy, které zálohují místní data na disku virtuálního počítače v Azure. Další informace najdete v tématu [SQL Server zálohování a obnovování pomocí služby Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) a [zálohování a obnovení pro SQL Server v Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md).
5. V závislosti na potřebách vaší aplikace můžete implementovat jeden z následujících tří běžných scénářů:
   
   1. Webový server, aplikační server a citlivá data můžete zachovat na databázovém serveru v Azure, zatímco místní citlivá data budete uchovávat místně.
   2. Webový server a aplikační server můžete uchovávat v místním prostředí, zatímco databázový server ve virtuálním počítači v Azure.
   3. Svůj databázový server, webový server a aplikační server můžete uchovávat v místním prostředí, zatímco repliky databáze na virtuálních počítačích v Azure udržujete. Toto nastavení povoluje místním webovým serverům nebo aplikacím pro vytváření sestav přístup k replikám databáze v Azure. Proto můžete dosáhnout snížení zatížení v místní databázi. Doporučujeme, abyste tento scénář implementovali pro náročné úlohy čtení a vývojové účely. Informace o vytváření replik databáze v Azure najdete v tématu Skupiny dostupnosti AlwaysOn při [vysoké dostupnosti a zotavení po havárii pro SQL Server v azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Porovnání strategií vývoje webu v Azure
K implementaci a nasazení vícevrstvé aplikace založené na SQL Server v Azure můžete použít jednu z následujících dvou programovacích metod:

* Nastavení tradičního webového serveru (IIS-Internetová informační služba) v Azure a přístup k databázím v SQL Server v Azure Virtual Machines.
* Implementujte a nasaďte cloudovou službu do Azure. Pak se ujistěte, že tato cloudová služba má přístup k databázím v SQL Server v Azure Virtual Machines. Cloudová služba může zahrnovat několik webových a pracovních rolí.

V následující tabulce najdete porovnání tradičního vývoje webu pomocí Azure Cloud Services a Azure Web Apps s ohledem na SQL Server na Virtual Machines Azure. Tabulka zahrnuje Azure Web Apps, protože je možné použít SQL Server na virtuálním počítači Azure jako zdroj dat pro Azure Web Apps prostřednictvím veřejné virtuální IP adresy nebo názvu DNS.

|  | Tradiční vývoj pro web v Azure Virtual Machines | Cloudové služby v Azure | Hostování webů pomocí Azure Web Apps |
| --- | --- | --- | --- |
| **Migrace aplikace z místního prostředí** |Existující aplikace tak, jak jsou. |Aplikace potřebují webové a pracovní role. |Stávající aplikace jsou tak, jak jsou, ale jsou vhodné pro webové aplikace a webové služby, které vyžadují rychlou škálovatelnost. |
| **Vývoj a nasazení** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Správce služby IIS, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Každá cloudová služba má dvě prostředí, do kterých můžete nasadit balíček a konfiguraci služby: pracovní a produkční. Cloudovou službu můžete nasadit do přípravného prostředí a otestovat ji před tím, než ji povýšíte do produkčního prostředí. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Nasazení webu, PowerShell. |
| **Správa a instalace** |Zodpovídáte za úlohy správy aplikace, dat, pravidel brány firewall, virtuální sítě a operačního systému. |Zodpovídáte za úlohy správy aplikace, data, pravidla brány firewall a virtuální sítě. |Zodpovídáte za úlohy správy jenom pro aplikace a data. |
| **Vysoká dostupnost a zotavení po havárii (HADR)** |Virtuální počítače doporučujeme umístit do stejné skupiny dostupnosti a do stejné cloudové služby. Udržování virtuálních počítačů ve stejné skupině dostupnosti umožňuje, aby Azure umístil uzly vysoké dostupnosti do samostatných domén selhání a upgradovacích domén. Podobně platí, že virtuální počítače ve stejné cloudové službě umožňují vyrovnávání zatížení a virtuální počítače přímo komunikovat přes místní síť v rámci datového centra Azure.<br/><br/>Zodpovídáte za implementaci řešení vysoké dostupnosti a zotavení po havárii pro SQL Server v Azure Virtual Machines, abyste se vyhnuli výpadkům. Podporované technologie HADR najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).<br/><br/>Zodpovídáte za zálohování vašich vlastních dat a aplikací.<br/><br/>Azure může přesunout virtuální počítače, pokud se hostitelský počítač v datovém centru nezdařil z důvodu problémů s hardwarem. Kromě toho by mohlo dojít ke plánovanému výpadku vašeho virtuálního počítače, když je hostitelský počítač aktualizován z důvodu zabezpečení nebo aktualizací softwaru. Proto doporučujeme udržovat alespoň dva virtuální počítače v každé vrstvě aplikace, aby se zajistila nepřetržitá dostupnost. Azure neposkytuje smlouvu SLA pro jeden virtuální počítač. |Azure spravuje selhání vyplývající z základního softwaru hardwaru nebo operačního systému. Doporučujeme, abyste implementovali více instancí webové role nebo role pracovního procesu, abyste zajistili vysokou dostupnost vaší aplikace. Informace najdete v tématu [Cloud Services, Virtual Machines a Virtual Network smlouva SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Zodpovídáte za zálohování vašich vlastních dat a aplikací.<br/><br/>Pro databáze nacházející se v databázi SQL Server na virtuálním počítači Azure zodpovídáte za implementaci řešení vysoké dostupnosti a zotavení po havárii, abyste se vyhnuli výpadkům. Podporované technologie HDAR najdete v tématu Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines.<br/><br/>**SQL Server zrcadlení databáze**: použijte službu Azure Cloud Services (Web/role pracovního procesu). SQL Server virtuální počítače a projekt cloudové služby můžou být ve stejném Virtual Network Azure. Pokud SQL Server virtuální počítač není ve stejném Virtual Network, budete muset vytvořit SQL Server alias ke směrování komunikace s instancí SQL Server. Kromě toho musí název aliasu odpovídat názvu SQL Server. |Vysoká dostupnost se dědí z rolí pracovních procesů Azure, úložiště objektů BLOB v Azure a Azure SQL Database. Azure Storage například udržuje tři repliky všech dat objektů blob, tabulek a front. V jednom okamžiku Azure SQL Database udržuje tři repliky dat, jednu primární repliku a dvě sekundární repliky. Další informace najdete v tématu [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) a [Azure SQL Database](../../database/sql-database-paas-overview.md).<br/><br/>Při použití SQL Server na virtuálním počítači Azure jako zdroj dat pro Azure Web Apps Pamatujte na to, že Azure Web Apps nepodporuje Virtual Network Azure. Jinými slovy, všechna připojení z Azure Web Apps k SQL Server virtuálním počítačům v Azure musí projít veřejnými koncovými body virtuálních počítačů. To může způsobit některá omezení pro scénáře vysoké dostupnosti a zotavení po havárii. Například klientská aplikace v Azure Web Apps připojení k SQL Servermu virtuálnímu počítači s zrcadlením databáze by se nemohla připojit k novému primárnímu serveru, protože zrcadlení databáze vyžaduje, abyste nastavili Azure Virtual Network mezi SQL Server hostitelskými virtuálními počítači v Azure. Proto se použití **SQL Server zrcadlení databáze** s Azure Web Apps v současnosti nepodporuje.<br/><br/>**SQL Server skupiny dostupnosti AlwaysOn**: při použití Web Apps Azure s SQL Server virtuálními počítači v Azure můžete nastavit skupiny dostupnosti AlwaysOn. Musíte ale nakonfigurovat naslouchací proces skupiny dostupnosti AlwaysOn pro směrování komunikace s primární replikou prostřednictvím veřejných koncových bodů s vyrovnáváním zatížení. |
| **Připojení mezi místními sítěmi** |K místnímu připojení můžete použít Azure Virtual Network. |K místnímu připojení můžete použít Azure Virtual Network. |Podporuje se Azure Virtual Network. Další informace najdete v tématu [Web Apps Virtual Network Integration](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Škálovatelnost** |Horizontální navýšení kapacity je dostupné zvýšením velikosti virtuálních počítačů nebo přidáním dalších disků. Další informace o velikostech virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů v Azure](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Pro databázový server**: horizontální navýšení kapacity je k dispozici prostřednictvím technik vytváření oddílů databáze a SQL Server skupin dostupnosti AlwaysOn.<br/><br/>Pro úlohy s velkým množstvím čtení můžete použít [skupiny dostupnosti AlwaysOn](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) na několika sekundárních uzlech i na SQL Server replikaci.<br/><br/>Pro úlohy s velkým zápisem můžete implementovat horizontální data dělení na více fyzických serverech, aby se zajistilo škálování aplikace.<br/><br/>Kromě toho můžete implementovat horizontální navýšení kapacity pomocí [SQL Server se směrováním závislým na datech](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10)). V případě směrování závislého na datech (DDR) je nutné implementovat mechanismus dělení v klientské aplikaci, obvykle v úrovni obchodní vrstvy, pro směrování požadavků databáze na více SQL Serverch uzlů. Obchodní vrstva obsahuje mapování, jak jsou data rozdělená na oddíly a která uzel obsahuje data.<br/><br/>Můžete škálovat aplikace, na kterých běží virtuální počítače. Další informace najdete v tématu [Jak škálovat aplikaci](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Důležité upozornění**: funkce **automatického škálování** v Azure umožňuje automatické zvýšení nebo snížení počtu virtuálních počítačů, které vaše aplikace používá. Tato funkce zaručuje, že činnost koncového uživatele nemá negativní dopad na dobu špičky a virtuální počítače se po nízkém zatížení vypnou. Doporučujeme, abyste pro cloudovou službu nestavili možnost automatického škálování, pokud zahrnuje SQL Server virtuálních počítačů. Důvodem je, že funkce automatického škálování umožňuje službě Azure zapnout virtuální počítač, když je využití procesoru na tomto VIRTUÁLNÍm počítači vyšší než určité prahové hodnoty, a vypnout virtuální počítač, když je využití procesoru nižší než. Funkce automatického škálování je užitečná pro bezstavové aplikace, jako jsou třeba webové servery, kde libovolný virtuální počítač může spravovat úlohy bez jakýchkoli odkazů na předchozí stav. Funkce automatického škálování ale není užitečná pro stavové aplikace, jako je například SQL Server, kde pouze jedna instance umožňuje zápis do databáze. |Horizontální navýšení kapacity je dostupné pomocí několika webových a pracovních rolí. Další informace o velikostech virtuálních počítačů pro webové role a role pracovního procesu najdete v tématu [Konfigurace velikostí pro Cloud Services](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Při použití **Cloud Services** můžete definovat více rolí pro distribuci zpracování a také dosáhnout flexibilního škálování aplikace. Každá cloudová služba zahrnuje jednu nebo více webových rolí a rolí pracovních procesů, z nichž každý má vlastní soubory a konfiguraci aplikace. Můžete škálovat cloudovou službu zvýšením počtu instancí rolí (virtuálních počítačů) nasazených pro roli a škálovat cloudovou službu snížením počtu instancí rolí. Podrobné informace najdete v tématu [modely spouštění Azure](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Škálování na více instancí je dostupné prostřednictvím integrované podpory Azure s vysokou dostupností prostřednictvím [Cloud Services, Virtual Machines a Virtual Network smlouva SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) a Load Balancer.<br/><br/>Pro vícevrstvé aplikace doporučujeme připojit aplikaci webové aplikace a role pracovního procesu k virtuálním počítačům s databázovým serverem prostřednictvím Azure Virtual Network. Kromě toho Azure poskytuje vyrovnávání zatížení pro virtuální počítače ve stejné cloudové službě a šíří požadavky uživatelů napříč nimi. Virtuální počítače připojené tímto způsobem můžou přímo komunikovat přes místní síť v rámci datového centra Azure.<br/><br/>**Automatické škálování** můžete nastavit na Azure Portal a také na časy plánování. Další informace najdete v tématu [Konfigurace automatického škálování pro cloudovou službu na portálu](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Horizontální navýšení a snížení kapacity**: můžete zvětšit nebo zmenšit velikost instance (virtuálního počítače) rezervovaného pro váš web.<br/><br/>Horizontální navýšení kapacity: pro svůj web můžete přidat další rezervované instance (VM).<br/><br/>**Automatické škálování** můžete nastavit na portálu i na časy plánování. Další informace najdete v tématu [Jak škálovat Web Apps](../../../app-service/manage-scale-up.md). |

Další informace o volbě těchto programovacích metod najdete v tématu [Azure Web Apps, Cloud Services a virtuálních počítačů: Kdy použít](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Další kroky
Další informace o spouštění SQL Server v Azure Virtual Machines najdete v tématu [SQL Server na azure Virtual Machines Overview](sql-server-on-azure-vm-iaas-what-is-overview.md).