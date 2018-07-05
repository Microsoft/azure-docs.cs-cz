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
ms.openlocfilehash: d1a6ff8dbd17d2792709a1ce065bcf793154e585
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37780668"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager
Tento článek popisuje, jak migrovat infrastrukturu jako službu (IaaS) prostředky z Classicu na modely nasazení Resource Manager a podrobnosti o tom, jak připojit prostředky z dva modely nasazení, které společně existovat ve vašem předplatném pomocí virtuální sítě brány Site-to-site. Další informace o [výhody a funkce Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md). 

## <a name="goal-for-migration"></a>Cíl pro migraci
Resource Manager umožňuje nasazovat složité aplikace pomocí šablony, konfiguruje virtuální počítače pomocí rozšíření virtuálních počítačů a integruje správu přístupu a označování. Azure Resource Manageru obsahuje škálovatelné a paralelní nasazení pro virtuální počítače do skupiny dostupnosti. Nový model nasazení také poskytuje správu životního cyklu výpočetní, síťové a úložiště nezávisle na sobě. Když zapnete zabezpečení ve výchozím nastavení s vynucením virtuálními počítači ve virtuální síti je nakonec zaměřením.

Téměř všechny funkce z modelu nasazení classic jsou podporovány pro compute, network a storage v Azure Resource Manageru. Chcete-li využívat nové funkce v Azure Resource Manageru, můžete migrovat stávající nasazení z modelu nasazení Classic.

## <a name="supported-resources-for-migration"></a>Podporované zdroje pro migraci
Při migraci jsou podporovány tyto klasické prostředky IaaS

* Virtuální počítače
* Skupiny dostupnosti
* Cloud Services s virtuálními počítači
* Účty úložiště
* Virtuální sítě
* Brány VPN Gateway
* Express Route brány _(ve stejném předplatném jako virtuální síť jenom)_
* Network Security Groups (Skupiny zabezpečení sítě)
* Směrovací tabulky
* Vyhrazené IP adresy

## <a name="supported-scopes-of-migration"></a>Podporované obory migrace
Existují různé způsoby dokončení migrace prostředků compute, network a storage:

* [Migrace virtuálních počítačů (ne ve virtuální síti)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migrace virtuálních počítačů (ve virtuální síti)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migrace účtů úložiště](#migration-of-storage-accounts)
* [Migrace nepřipojené prostředků](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrace virtuálních počítačů (ne ve virtuální síti)
V modelu nasazení Resource Manager zabezpečení ve výchozím nastavení vynucuje pro vaše aplikace. Všechny virtuální počítače musí být ve virtuální síti v modelu Resource Manager. Restartování platformy Azure (`Stop`, `Deallocate`, a `Start`) jako součást migrace virtuálních počítačů. Máte dvě možnosti pro virtuální sítě, které virtuální počítače se budou migrovat do:

* Můžete požádat o platformy s cílem vytvořit novou virtuální síť a migrovat virtuální počítač do nové virtuální sítě.
* Migrujte virtuální počítač do existující virtuální sítě v Resource Manageru.

> [!NOTE]
> V tomto oboru migrace nemusí být operace rovině správy i rovina dat provoz povolený pro určitou dobu během migrace.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrace virtuálních počítačů (ve virtuální síti)
U většiny konfigurací virtuálních počítačů pouze metadata migrace mezi modely nasazení Classic a Resource Manageru. Základní virtuální počítače jsou spuštěné na stejném hardwaru, ve stejné síti a stejnému úložišti. Operace rovině správy nemusí být povolený pro určitou dobu během migrace. Ale rovina dat i nadále fungovat. To znamená, že vaše aplikace spuštěné na virtuální počítače (classic) nejsou spojené prostoje během migrace.

Následující konfigurace nejsou aktuálně podporovány. Pokud dojde k přidání podpory v budoucnu, několik virtuálních počítačů v této konfiguraci může způsobit výpadek (přejít přes stop, uvolnění a restartujte virtuální počítač operace).

* Máte více než jedné skupině dostupnosti v jedné cloudové službě.
* Máte jeden nebo více skupin dostupnosti a virtuální počítače, které nejsou ve skupině dostupnosti v jedné cloudové službě.

> [!NOTE]
> V tomto oboru migrace nemusí být roviny správy povolený pro určitou dobu během migrace. Pro některé konfigurace, jak bylo popsáno výše, dojde k výpadku rovina dat.
>

### <a name="migration-of-storage-accounts"></a>Migrace účtů úložiště
Umožňuje bezproblémovou migraci můžete nasadit virtuální počítače Resource Manageru v účtu klasického úložiště. Díky této funkci výpočetní a síťové prostředky můžete a by se měly migrovat nezávisle na účty úložiště. Po dokončení migrace virtuálních počítačů a virtuální sítě, které potřebujete migrovat přes vaše účty úložiště pro dokončení procesu migrace.

Pokud váš účet úložiště nemá žádné přidružené disky ani data virtuálních počítačů a má jenom objekty BLOB, soubory, tabulky a fronty migrace do Azure Resource Manageru může být provedeno jako samostatná migrace bez závislostí.

> [!NOTE]
> Model nasazení Resource Manager nemá koncept disků a imagí Classic. Když účtu úložiště jsou migrované, klasické Image a disky nejsou viditelné v zásobníku správce prostředků ale záložní virtuální pevné disky zůstanou v účtu úložiště.
>

### <a name="migration-of-unattached-resources"></a>Migrace nepřipojené prostředků
Účty úložiště bez přidružené disky nebo data virtuálních počítačů může migrovat nezávisle.

Skupiny zabezpečení sítě, směrovací tabulky a vyhrazené IP adresy, které nejsou připojené žádné virtuální počítače a virtuální sítě, můžete také migrovat nezávisle na sobě.

<br>

## <a name="unsupported-features-and-configurations"></a>Nepodporované funkce a konfigurace
Některé funkce a konfigurace nejsou aktuálně podporovány. Naše doporučení týkající se jim v následujících částech.

### <a name="unsupported-features"></a>Nepodporované funkce
Následující funkce nejsou aktuálně podporovány. Můžete volitelně odebrat tato nastavení, migrovat virtuální počítače a potom znovu povolit nastavení v modelu nasazení Resource Manager.

| Poskytovatel prostředků | Funkce | Doporučení |
| --- | --- | --- |
| Compute | Nepřidružené virtuální počítače s disky. | Objekty BLOB VHD za tyto disky se migrují při migraci účtu úložiště |
| Compute | Image virtuálních počítačů. | Objekty BLOB VHD za tyto disky se migrují při migraci účtu úložiště |
| Síť | Seznamy ACL koncových bodů. | Odeberte seznamy ACL koncových bodů a pak zkuste migraci opakovat. |
| Síť | Application Gateway | Odebrat službu Application Gateway před zahájením migrace a po dokončení migrace znovu vytvořit Application Gateway. |
| Síť | Virtuálních sítí pomocí VNet Peering. | Migrace virtuální sítě na Resource Manager a potom vytvořit partnerský vztah. Další informace o [VNet Peering](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Nepodporované konfigurace
Následující konfigurace nejsou aktuálně podporovány.

| Služba | Konfigurace | Doporučení |
| --- | --- | --- |
| Resource Manager |Na základě řízení přístupu role (RBAC) pro klasické prostředky |Protože identifikátor URI prostředků je upravené po migraci, doporučuje se, že máte v plánu aktualizace zásady RBAC, které je třeba provést po migraci. |
| Compute |Více podsítí, které jsou přidružené k virtuálnímu počítači |Aktualizujte konfiguraci podsítě odkazovat jenom jednu podsíť. To může vyžadovat odebrání sekundární síťové karty (který odkazuje na jiné podsíti) z virtuálního počítače a znovu připojit po dokončení migrace. |
| Compute |Virtuální počítače, které patří do virtuální sítě, ale nemají explicitní podsíť přiřazené |Volitelně můžete odstranit virtuální počítač. |
| Compute |Virtuální počítače, které se mají výstrahy, zásady automatického škálování |Prochází migrace a tato nastavení se zahodí. Důrazně doporučujeme vyhodnotit prostředí, než se pustíte do migrace. Alternativně můžete změnit konfiguraci nastavení oznámení po dokončení migrace. |
| Compute |Rozšíření virtuálních počítačů XML (BGInfo 1.*, ladicí program sady Visual Studio, Web Deploy a vzdálené ladění) |To není podporováno. Doporučuje se, že odeberete tato rozšíření z virtuálního počítače, které chcete migraci pokračovat, nebo budou zahozeny automaticky během procesu migrace. |
| Compute |Diagnostika spouštění díky službě Premium storage |Zakažte funkci Diagnostika spouštění virtuálních počítačů před pokračováním v migraci. Po dokončení migrace můžete znovu povolit diagnostiku spouštění v zásobníku správce prostředků. Kromě toho objekty BLOB, které se používají pro sériové protokoly a snímku obrazovky je potřeba odstranit tak už se účtují pro tyto objekty BLOB. |
| Compute | Cloudové služby, které obsahují webové nebo pracovní role | To není aktuálně podporováno. |
| Compute | Nastavení cloudových služeb, které obsahují více než jednu dostupnosti nebo více skupiny dostupnosti. |To není aktuálně podporováno. Přesuňte virtuální počítače do stejné skupině dostupnosti před migrací. |
| Compute | Virtuální počítač pomocí rozšíření Azure Security Center | Rozšíření Azure Security Center automaticky nainstaluje na vašich virtuálních počítačů a sledovat jejich zabezpečení a vyvolají upozornění. Tato rozšíření obvykle se nainstalují automaticky pokud zásad Azure Security Center je povolená pro předplatné. Pokud chcete migrovat virtuální počítače, zakažte zásadu security center v rámci předplatného, čímž se odebere sledování rozšíření z virtuálních počítačů služby Security Center. |
| Compute | Rozšíření zálohování nebo snímek virtuálního počítače | Tato rozšíření jsou nainstalovány ve virtuálním počítači nakonfigurovat pomocí služby Azure Backup. Během migrace tyto virtuální počítače není podporováno, postupujte podle pokynů [tady](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) Udržovat zálohy, které byly provedeny před migrací.  |
| Síť |Virtuální sítě, které obsahují virtuální počítače a webové nebo pracovní role |To není aktuálně podporováno. Přesuňte role Web/Worker do své vlastní virtuální sítě před migrací. Po migraci klasického virtuálního počítače migrované virtuální síť Azure Resource Manageru můžete propojit s klasickou virtuální síť k dosažení podobnou konfiguraci jako předtím.|
| Síť | Klasické okruhy Expressroute |To není aktuálně podporováno. Tyto okruhy musí migrovat na Azure Resource Manager před zahájením migrace IaaS. Další informace najdete v tématu [okruhy ExpressRoute přesun z klasického modelu nasazení Resource Manageru](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Virtuální sítě, které obsahují služby App Service Environment |To není aktuálně podporováno. |
| Azure HDInsight |Virtuální sítě, které obsahují služby HDInsight |To není aktuálně podporováno. |
| Microsoft Dynamics Lifecycle Services |Virtuální sítě, které obsahují virtuální počítače, které jsou spravovány Dynamics Lifecycle Services |To není aktuálně podporováno. |
| Azure AD Domain Services |Virtuální sítě, které obsahují služby Azure AD Domain services |To není aktuálně podporováno. |
| Azure RemoteApp |Virtuální sítě, které obsahují nasazení vzdálené aplikace RemoteApp Azure |To není aktuálně podporováno. |
| Azure API Management |Virtuální sítě, které obsahují nasazení Azure API Management |To není aktuálně podporováno. Migrace virtuální sítě IaaS, změňte virtuální síť nasazení API Management, což je žádná operace výpadek. |
