---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 05/18/2018
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: 629cdf3907f45419ecfa5fce59430a163767c8fb
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943262"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Platforma podporovaná migrace z klasického do Azure Resource Manageru prostředků IaaS
Tento článek popisuje, jak migrovat infrastrukturu jako službu (IaaS) prostředky z klasického na modelech nasazení Resource Manager a podrobnosti o tom, jak připojit prostředky z modelů dvě nasazení, které společně existovat ve vašem předplatném pomocí virtuální sítě brány Site-to-site. Další informace o [funkce služby Správce prostředků Azure a výhody](../articles/azure-resource-manager/resource-group-overview.md). 

## <a name="goal-for-migration"></a>Cíl pro migraci
Resource Manager umožňuje nasazení složitých aplikací prostřednictvím šablon, nakonfiguruje virtuální počítače pomocí rozšíření virtuálního počítače a zahrnuje správu přístupu a označování. Azure Resource Manager zahrnuje škálovatelné a paralelní nasazení pro virtuální počítače do skupiny dostupnosti. Nový model nasazení také poskytuje správu životního cyklu výpočty, síť a úložiště nezávisle. Nakonec je zaměřená na povolení zabezpečení ve výchozím nastavení s vynucení virtuální počítače ve virtuální síti.

Téměř všechny funkce z modelu nasazení classic jsou podporovány pro výpočty, síť a úložiště v Azure Resource Manager. Abyste mohli využívat výhod nových možností ve službě Správce prostředků Azure, můžete migrovat existující nasazení z modelu nasazení Classic.

## <a name="supported-resources-for-migration"></a>Podporované prostředky pro migraci
Při migraci jsou podporovány tyto klasické prostředky IaaS

* Virtuální počítače
* Skupiny dostupnosti
* Cloud Services
* Účty úložiště
* Virtuální sítě
* Brány VPN Gateway
* Express trasy brány _(ve stejném předplatném jako virtuální síť pouze)_
* Network Security Groups (Skupiny zabezpečení sítě)
* Směrovací tabulky
* Vyhrazené IP adresy

## <a name="supported-scopes-of-migration"></a>Podporované obory migrace
Dokončení migrace výpočty, síť a úložiště prostředků čtyři různými způsoby:

* [Migrace virtuálních počítačů (ne ve virtuální síti)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migrace virtuálních počítačů (ve virtuální síti)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migrace účtů úložiště](#migration-of-storage-accounts)
* [Migrace odpojit prostředků](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrace virtuálních počítačů (ne ve virtuální síti)
V modelu nasazení Resource Manager zabezpečení je vynucené pro vaše aplikace ve výchozím nastavení. Všechny virtuální počítače musí být ve virtuální síti v modelu Resource Manager. Restartování platformy Azure (`Stop`, `Deallocate`, a `Start`) jako součást migrace virtuálních počítačů. Máte dvě možnosti pro virtuální sítě, které virtuální počítače se budou migrovat do:

* Může požádat o platformu pro vytvoření nové virtuální sítě a migrace virtuálního počítače do nové virtuální sítě.
* Virtuální počítač můžete migrovat do existující virtuální síť ve službě Správce prostředků.

> [!NOTE]
> V tomto rozsahu migrace operations management roviny a datové roviny operace nemusí mít pro určitou dobu během migrace.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrace virtuálních počítačů (ve virtuální síti)
Pro většinu konfiguraci virtuálních počítačů je pouze metadata migraci mezi modelem nasazení Classic a Resource Manager. Základní virtuální počítače jsou spuštěné na stejném hardwaru, ve stejné síti a na stejné úložiště. Pro určitou dobu během migrace nemusí být povoleno roviny management operace. Datové roviny však nadále fungovat. To znamená, že vaše aplikace spuštěné na virtuálních počítačích (klasické) nevznikají výpadek během migrace.

Následující konfigurace nejsou aktuálně podporovány. Pokud dojde k přidání podpory v budoucnu, některé virtuální počítače v této konfiguraci může způsobit výpadek (přejděte prostřednictvím zastavit, navrácení a restartujte počítač operations).

* Máte více než jeden dostupnosti v rámci jedné cloudové služby.
* Máte jeden nebo více sad dostupnosti a virtuální počítače, které nejsou ve skupině dostupnosti nastavena v jednom cloudové služby.

> [!NOTE]
> Roviny správy pro určitou dobu během migrace nemusí být povoleno v tomto rozsahu migrace. Pro některé konfigurace jak bylo popsáno výše, datové roviny nastane.
>

### <a name="migration-of-storage-accounts"></a>Migrace účtů úložiště
Povolit bezproblémové migrace, můžete nasadit virtuální počítače správce prostředků v účtu úložiště classic. Díky této funkci výpočetní a síťové prostředky můžete a mají být migrovány nezávisle na účty úložiště. Po migraci přes virtuální počítače a virtuální sítě, budete muset migrovat přes účty úložiště pro dokončení procesu migrace.

Pokud váš účet úložiště nemá žádné přidružené disků nebo virtuálních počítačů data a má jenom objekty BLOB, soubory, tabulky a fronty lze jako samostatná migrace bez závislosti provést migraci na Azure Resource Manager.

> [!NOTE]
> Model nasazení Resource Manager nemá koncept Classic Image a disky. Pokud účet úložiště je migrované, Classic Image a disky nejsou viditelné v zásobníku Resource Manager ale základní virtuální pevné disky zůstávají v účtu úložiště.
>

### <a name="migration-of-unattached-resources"></a>Migrace odpojit prostředků
Účty úložiště bez přidružených disků nebo virtuálních počítačů data může migrovat nezávisle.

Skupiny zabezpečení sítě, směrovací tabulky a vyhrazené IP adresy, které nejsou připojeny žádné virtuální počítače a virtuální sítě můžete také migrovat nezávisle.

<br>

## <a name="unsupported-features-and-configurations"></a>Nepodporované funkce a konfigurace
Některé funkce a konfigurace nejsou aktuálně podporovány; Následující části popisují Naše doporučení je obcházet.

### <a name="unsupported-features"></a>Nepodporované funkce
Aktuálně nejsou podporovány následující funkce. Volitelně můžete tato nastavení odebrat, migrovat virtuální počítače a pak znovu povolte nastavení v modelu nasazení Resource Manager.

| Poskytovatel prostředků | Funkce | Doporučení |
| --- | --- | --- |
| Compute | Disky nepřidružený virtuálního počítače. | Objekty BLOB virtuálního pevného disku za tyto disky bude migrována při migraci účtu úložiště |
| Compute | Bitové kopie virtuálních počítačů. | Objekty BLOB virtuálního pevného disku za tyto disky bude migrována při migraci účtu úložiště |
| Síť | Seznamy ACL koncových bodů. | Odeberte seznamy ACL koncových bodů a zkuste migraci opakovat. |
| Síť | Application Gateway | Odeberte služby Application Gateway před zahájením migrace a potom bránu znovu vytvořte aplikaci po dokončení migrace. |
| Síť | Virtuální sítě pomocí virtuální sítě partnerský vztah. | Migrovat virtuální sítě do Resource Manager a potom partnerský uzel. Další informace o [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Nepodporované konfigurace
Následující konfigurace nejsou aktuálně podporovány.

| Služba | Konfigurace | Doporučení |
| --- | --- | --- |
| Resource Manager |Na základě řízení přístupu role (RBAC) pro klasické prostředky |Protože je identifikátor URI prostředků upravené po migraci, doporučujeme, abyste naplánovali RBAC aktualizace zásad, které je třeba provést po migraci. |
| Compute |Více podsítí, které jsou přidružené k virtuálnímu počítači |Aktualizujte konfiguraci podsítě odkazovat jenom jednu podsíť. To může vyžadovat odeberte sekundární síťový adaptér (který se odkazuje na jinou podsítí) z virtuálního počítače a znovu připojit po dokončení migrace. |
| Compute |Virtuální počítače, které patří k virtuální síti, ale nemáte podsíť explicitní přiřazen |Volitelně můžete odstranit virtuální počítač. |
| Compute |Virtuální počítače, které mají výstrahy, zásady škálování |Migrace prochází a tato nastavení jsou vyřadit. Důrazně doporučujeme vyhodnotit prostředí, před provedením migrace. Alternativně můžete znovu nakonfigurovat nastavení výstrah po dokončení migrace. |
| Compute |Rozšíření virtuálního počítače XML (BGInfo 1.*, Visual Studio Debugger, Web Deploy a vzdálené ladění) |To není podporováno. Doporučujeme, odeberte tyto přípony z virtuálního počítače migraci pokračovat, nebo se budou automaticky odstraněna během procesu migrace. |
| Compute |Diagnostika spouštění Storage úrovně Premium |Zakážete funkci Diagnostika spouštění pro virtuální počítače před pokračováním migrace. Diagnostika spouštění v zásobníku Resource Manager můžete znovu povolit po dokončení migrace. Kromě toho objekty BLOB, které jsou používány pro snímek obrazovky a sériové protokoly měla by být odstraněna, jsou už účtovat poplatek za tyto objekty BLOB. |
| Compute | Cloudové služby, které obsahují webové/role pracovního procesu | Tato možnost není aktuálně podporována. |
| Compute | Cloudové služby, které obsahují více než jeden dostupnosti nastavit nebo nastaví více dostupnosti. |Tato možnost není aktuálně podporována. Přesuňte virtuální počítače na stejné dostupnosti nastavit před migrací. |
| Compute | Virtuální počítač s rozšíření Azure Security Center | Rozšíření Azure Security Center automaticky nainstaluje na virtuálních počítačích vyvolat výstrahy a sledovat jejich zabezpečení. Tato rozšíření obvykle získat nainstalovat automaticky, pokud Azure Security Center je povolena v předplatném. Pokud chcete migrovat virtuální počítače, zakažte center zásad zabezpečení v odběru, což způsobí odebrání Security Center monitorování rozšíření z virtuálních počítačů. |
| Compute | Virtuální počítač s rozšíření zálohování nebo snímek | Tato rozšíření jsou nainstalovány na virtuálním počítači nakonfigurovaná služba Azure Backup. Při migraci těchto virtuálních počítačů není podporována, postupujte podle pokynů [sem](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) zachovat zálohování, které byly provedeny před migrací.  |
| Síť |Virtuální sítě, které obsahují virtuální počítače a webové/role pracovního procesu |Tato možnost není aktuálně podporována. Před migrací přesuňte role Web nebo Worker ke své vlastní virtuální síti. Jakmile je migrován klasickou virtuální síť, migrované virtuální sítě Azure Resource Manager můžete peered s klasickou virtuální síť, zajistit podobnou konfiguraci jako předtím.|
| Síť | Classic okruhy Expressroute |Tato možnost není aktuálně podporována. Tyto okruhy se musí migrovat na Azure Resource Manager před zahájením migrace IaaS. Další informace najdete v tématu [okruhy ExpressRoute přesouvání z klasického modelu nasazení Resource Manager](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Virtuální sítě, které obsahují služby App Service Environment |Tato možnost není aktuálně podporována. |
| Azure HDInsight |Virtuální sítě, které obsahují služby HDInsight |Tato možnost není aktuálně podporována. |
| Služby životního cyklu aplikace Microsoft Dynamics |Virtuální sítě, které obsahují virtuální počítače, které jsou spravovány nástrojem služby Dynamics životního cyklu |Tato možnost není aktuálně podporována. |
| Azure AD Domain Services |Virtuální sítě, které obsahují služby Azure AD Domain services |Tato možnost není aktuálně podporována. |
| Azure RemoteApp |Virtuální sítě, které obsahují nasazení Azure RemoteApp |Tato možnost není aktuálně podporována. |
| Azure API Management |Virtuální sítě, které obsahují nasazení Azure API Management |Tato možnost není aktuálně podporována. Migrovat virtuální síť IaaS, změňte nasazení API Management, který je žádná operace výpadek sítě VNET. |
