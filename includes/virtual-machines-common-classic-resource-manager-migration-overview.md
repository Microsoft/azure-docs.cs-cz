---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: tanmaygore
ms.service: virtual-machines
ms.topic: include
ms.date: 02/06/2020
ms.author: tagore
ms.custom: include file
ms.openlocfilehash: 4e07334e859f2c1401547cc3f88988830b71c5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77192648"
---
Tento článek popisuje, jak migrovat prostředky infrastruktury jako služby (IaaS) z modelů nasazení Classic do Resource Manager a podrobnosti o připojení prostředků ze dvou modelů nasazení, které existují společně ve vašem předplatném pomocí virtuální sítě brány site-to-site. Další informace o [funkcích a výhodách Azure Resource Manageru](../articles/azure-resource-manager/management/overview.md)najdete v dalších informacích . 

## <a name="goal-for-migration"></a>Cíl migrace
Správce prostředků umožňuje nasazovat složité aplikace prostřednictvím šablon, konfiguruje virtuální počítače pomocí rozšíření virtuálních počítačů a zahrnuje správu přístupu a označování. Azure Resource Manager zahrnuje škálovatelné paralelní nasazení pro virtuální počítače do sad dostupnosti. Nový model nasazení také poskytuje správu životního cyklu výpočetních prostředků, sítě a úložiště nezávisle. Nakonec je zaměření na povolení zabezpečení ve výchozím nastavení s vynucenívirtuálních počítačů ve virtuální síti.

Téměř všechny funkce z modelu klasického nasazení jsou podporované pro výpočetní prostředky, sítě a úložiště v rámci Azure Resource Manager. Chcete-li těžit z nových funkcí ve Správci prostředků Azure, můžete migrovat existující nasazení z modelu klasického nasazení.

## <a name="supported-resources-for-migration"></a>Podporované prostředky pro migraci
Tyto klasické prostředky IaaS jsou podporovány během migrace

* Virtuální počítače
* Skupiny dostupnosti
* Účty úložiště
* Virtuální sítě
* Brány VPN Gateway
* Brány expresních tras _(pouze ve stejném předplatném jako virtuální síť)_
* Network Security Groups (Skupiny zabezpečení sítě)
* Směrovací tabulky
* Vyhrazené IP adresy

## <a name="supported-scopes-of-migration"></a>Podporované obory migrace
Migraci výpočetních prostředků, síťových a úložných prostředků lze provést čtyřmi různými způsoby:

* [Migrace virtuálních počítačů (NENÍ ve virtuální síti)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migrace virtuálních počítačů (ve virtuální síti)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migrace účtů úložiště](#migration-of-storage-accounts)
* [Migrace nepřipojených zdrojů](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrace virtuálních počítačů (NENÍ ve virtuální síti)
V modelu nasazení Resource Manager je ve výchozím nastavení vynuceno zabezpečení vašich aplikací. Všechny virtuální počítače musí být ve virtuální síti v modelu Správce prostředků. Platforma Azure restartuje (`Stop`, `Deallocate`, a `Start`) virtuální počítače jako součást migrace. Máte dvě možnosti pro virtuální sítě, do kterých budou virtuální počítače migrovány:

* Můžete požádat platformu k vytvoření nové virtuální sítě a migraci virtuálního počítače do nové virtuální sítě.
* Virtuální počítač můžete migrovat do existující virtuální sítě ve Správci prostředků.

> [!NOTE]
> V tomto rozsahu migrace operace rovina mise a operace roviny dat nemusí být povoleny po určitou dobu během migrace.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrace virtuálních počítačů (ve virtuální síti)
U většiny konfigurací virtuálních počítačů migruje pouze metadata mezi modely nasazení Classic a Resource Manager. Základní virtuální počítačky jsou spuštěny na stejném hardwaru, ve stejné síti a se stejným úložištěm. Provoz mezi řídící rovinou nemusí být během migrace po určitou dobu povolen. Rovina dat však nadále funguje. To znamená, že vaše aplikace spuštěné na virtuálních počítačích (klasické) nevznikají prostoje během migrace.

Následující konfigurace nejsou aktuálně podporovány. Pokud je podpora přidána v budoucnu, některé virtuální počítače v této konfiguraci může dojít k prostojům (projít stop, navrátit a restartovat operace virtuálních počítačů).

* V jedné cloudové službě máte více než jednu sadu dostupnosti.
* Máte jednu nebo více sad dostupnosti a virtuálních aplikací, které nejsou v sadě dostupnosti v jedné cloudové službě.

> [!NOTE]
> V tomto oboru migrace nemusí být rovina správy povolena po určitou dobu během migrace. Pro určité konfigurace, jak je popsáno výše, dojde k prostojům roviny dat.
>

### <a name="migration-of-storage-accounts"></a>Migrace účtů úložiště
Chcete-li povolit bezproblémovou migraci, můžete nasadit virtuální chod Správce prostředků v klasickém účtu úložiště. Díky této funkci výpočetní a síťové prostředky mohou a měly by být migrovány nezávisle na účtech úložiště. Jakmile migrujete přes virtuální počítače a virtuální síť, musíte migrovat přes účty úložiště, abyste dokončili proces migrace.

Pokud váš účet úložiště nemá žádné přidružené disky nebo data virtuálních počítačů a obsahuje jenom objekty BLOB, soubory, tabulky a fronty, migrace do Správce prostředků Azure lze provést jako samostatnou migraci bez závislostí.

> [!NOTE]
> Model nasazení Správce prostředků nemá koncept klasické image a disky. Při migraci účtu úložiště klasické bitové kopie a disky nejsou viditelné v zásobníku Správce prostředků, ale záložní virtuální pevné disky zůstávají v účtu úložiště.

Následující snímky obrazovky ukazují, jak upgradovat klasický účet úložiště na účet úložiště Azure Resource Manager pomocí portálu Azure:
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte na svůj účet úložiště.
3. V části **Nastavení** klikněte na **Migrovat do ARM**.
4. Kliknutím na **ověřit** určit proveditelnost migrace.
5. Pokud ověření projde, klikněte na **Připravit** a vytvořte migrovaný účet úložiště.
6. Chcete-li migraci potvrdit, zadejte **ano** a migraci dokončete klepnutím na **tlačítko Potvrdit.**

    ![Ověřit účet úložiště](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Příprava účtu úložiště](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Dokončení migrace účtu úložiště](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migrace nepřipojených zdrojů
Účty úložiště bez přidružených disků nebo dat virtuálních počítačů mohou být migrovány nezávisle.

Skupiny zabezpečení sítě, tabulky směrování & vyhrazené IP adresy, které nejsou připojeny k žádným virtuálním počítačům a virtuálním sítím, lze také migrovat nezávisle.

<br>

## <a name="unsupported-features-and-configurations"></a>Nepodporované funkce a konfigurace
Některé funkce a konfigurace nejsou aktuálně podporovány. v následujících částech jsou popsána naše doporučení týkající se nich.

### <a name="unsupported-features"></a>Nepodporované funkce
Následující funkce nejsou aktuálně podporovány. Volitelně můžete tato nastavení odebrat, migrovat virtuální počítače a potom znovu povolit nastavení v modelu nasazení Správce prostředků.

| Poskytovatel prostředků | Funkce | Doporučení |
| --- | --- | --- |
| Služba Compute | Nepřidružené disky virtuálních počítačů. | Objekty BLOB virtuálního pevného disku za těmito disky se migrují při migraci účtu úložiště. |
| Služba Compute | Image virtuálních strojů. | Objekty BLOB virtuálního pevného disku za těmito disky se migrují při migraci účtu úložiště. |
| Network (Síť) | Seznamy ACL pro koncové body. | Odeberte alokace AC a opakujte migraci. |
| Network (Síť) | Application Gateway | Před zahájením migrace odeberte aplikační bránu a po dokončení migrace znovu vytvořte aplikační bránu. |
| Network (Síť) | Virtuální sítě pomocí partnerského vztahu virtuální sítě. | Migrujte virtuální síť do Správce prostředků a potom druhou stranou. Další informace o [partnerské síti virtuální ch odpyka](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Nepodporované konfigurace
Následující konfigurace nejsou aktuálně podporovány.

| Služba | Konfigurace | Doporučení |
| --- | --- | --- |
| Resource Manager |Řízení přístupu na základě rolí (RBAC) pro klasické prostředky |Vzhledem k tomu, že identifikátor URI prostředků je po migraci změněn, doporučujeme naplánovat aktualizace zásad RBAC, které je třeba provést po migraci. |
| Služba Compute |Více podsítí přidružených k virtuálnímu počítači |Aktualizujte konfiguraci podsítě tak, aby odkazovala pouze na jednu podsíť. To může vyžadovat odebrání sekundární síťové rozhraní (která odkazuje na jinou podsíť) z virtuálního zařízení a znovu ji připojit po dokončení migrace. |
| Služba Compute |Virtuální počítače, které patří do virtuální sítě, ale nemají přiřazenu explicitní podsíť |Volitelně můžete virtuální ho odstranit. |
| Služba Compute |Virtuální počítače, které mají výstrahy, zásady automatického škálování |Migrace prochází a tato nastavení jsou vynechány. Důrazně doporučujeme vyhodnotit prostředí před dokončením migrace. Případně můžete změnit konfiguraci nastavení výstrah po dokončení migrace. |
| Služba Compute |Rozšíření virtuálních stránek XML (BGInfo 1.*, Debugger visual studia, nasazení webu a vzdálené ladění) |Tato funkce není podporovaná. Doporučujeme odebrat tato rozšíření z virtuálního počítače, abyste mohli pokračovat v migraci, jinak budou během procesu migrace automaticky zrušena. |
| Služba Compute |Diagnostika spouštění s úložištěm Premium |Před pokračováním v migraci zakažte funkci Diagnostika spouštění virtuálních společností. Diagnostiku spouštění můžete po dokončení migrace znovu povolit v zásobníku Správce prostředků. Kromě toho objekty BLOB, které se používají pro snímek obrazovky a sériové protokoly by měly být odstraněny, takže se již neúčtují za tyto objekty BLOB. |
| Služba Compute | Cloudové služby, které obsahují webové role/role pracovního procesu | Tato tato podpora není v současné době podporována. |
| Služba Compute | Cloudové služby, které obsahují více než jednu sadu dostupnosti nebo více sad dostupnosti. |Tato tato podpora není v současné době podporována. Před migrací přesuňte virtuální počítače do stejné skupiny dostupnosti. |
| Služba Compute | Virtuální počítač s rozšířením Azure Security Center | Azure Security Center automaticky nainstaluje rozšíření na virtuální počítače, aby monitorovaljejich zabezpečení a vyvolávač výstrah. Tato rozšíření se obvykle nainstalují automaticky, pokud je v předplatném povolena zásada Azure Security Center. Chcete-li migrovat virtuální počítače, zakažte zásady centra zabezpečení v předplatném, které odeberou rozšíření monitorování Centra zabezpečení z virtuálních počítačů. |
| Služba Compute | Virtuální počítače s rozšířením zálohování nebo snímku | Tato rozšíření se instalují ve virtuálním počítači nakonfigurovaném se službou Azure Backup. Zatímco migrace těchto virtuálních počítače není podporována, postupujte podle pokynů [zde](/azure/virtual-machines/windows/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) zachovat zálohy, které byly pořízeny před migrací.  |
| Služba Compute | Virtuální počítač s rozšířením Azure Site Recovery | Tato rozšíření se instalují ve virtuálním počítači nakonfigurovaném pomocí služby Azure Site Recovery. Zatímco migrace úložiště používaného s obnovením lokality bude fungovat, aktuální replikace bude mít vliv. Po migraci úložiště je potřeba zakázat a povolit replikaci virtuálních zařízení. |
| Network (Síť) |Virtuální sítě, které obsahují virtuální počítače a webové/pracovní role |Tato tato podpora není v současné době podporována. Před migrací přesuňte role webu nebo pracovního procesu do vlastní virtuální sítě. Po migraci klasické virtuální sítě lze migrovnanou virtuální síť Azure Resource Manager uvést pomocí klasické virtuální sítě, aby bylo dosaženo podobné konfigurace jako dříve.|
| Network (Síť) | Klasické obvody Expresní trasy |Tato tato podpora není v současné době podporována. Tyto okruhy je potřeba migrovat do Správce prostředků Azure před zahájením migrace IaaS. Další informace naleznete [v tématu Přesunutí okruhů ExpressRoute z klasického do modelu nasazení Správce prostředků](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Virtuální sítě, které obsahují prostředí služby App Service |Tato tato podpora není v současné době podporována. |
| Azure HDInsight |Virtuální sítě, které obsahují služby HDInsight |Tato tato podpora není v současné době podporována. |
| Služby životního cyklu Microsoft Dynamics |Virtuální sítě, které obsahují virtuální počítače spravované službou Dynamics Lifecycle Services |Tato tato podpora není v současné době podporována. |
| Azure AD Domain Services |Virtuální sítě, které obsahují služby Domény Azure AD |Tato tato podpora není v současné době podporována. |
| Azure API Management |Virtuální sítě, které obsahují nasazení Azure API Management |Tato tato podpora není v současné době podporována. Chcete-li migrovat virtuální síť IaaS, změňte virtuální síť nasazení správy rozhraní API, což je operace bez prostojů. |
