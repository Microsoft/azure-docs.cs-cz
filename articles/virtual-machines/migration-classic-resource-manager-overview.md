---
title: Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager
description: Projděte si migraci prostředků z modelu Classic na Azure Resource Manager, která je podporována platformou.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 44b89aae2f446a13207c8e3b586892ad085bd790
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879406"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager-in-linux"></a>Migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager v systému Linux

> [!IMPORTANT]
> V dnešní době se o 90% virtuálních počítačů IaaS používají [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28. února 2020 se klasické virtuální počítače zastaraly a budou plně vyřazeny od 1. března 2023. [Přečtěte si další informace]( https://aka.ms/classicvmretirement) o této zastaralosti a [o tom, jak vás to ovlivní](classic-vm-deprecation.md#how-does-this-affect-me).



Tento článek poskytuje přehled o nástroji pro migraci, který je podporovaný platformou, jak migrovat prostředky z Azure Service Manager (ASM), označované jako klasické modely nasazení Správce prostředků (ARM), a podrobně popisuje, jak připojit prostředky ze dvou modelů nasazení, které se ve vašem předplatném používají, pomocí bran sítě Site-to-site. Další informace o [funkcích Azure Resource Manager a výhodách](../azure-resource-manager/management/overview.md)najdete v článku. 

ASM podporuje dva různé výpočetní produkty, Azure Virtual Machines (Classic), označované jako IaaS virtuální počítače & [azure Cloud Services (Classic)](../cloud-services/index.yml) , označované jako PaaS virtuální počítače nebo webové a pracovní role. Tento dokument se týká pouze migrace Virtual Machines Azure (Classic).

## <a name="goal-for-migration"></a>Cíl migrace
Správce prostředků umožňuje nasazení složitých aplikací prostřednictvím šablon, konfiguraci virtuálních počítačů pomocí rozšíření virtuálních počítačů a správu přístupu a označování v podniku. Azure Resource Manager zahrnuje škálovatelné paralelní nasazení virtuálních počítačů do skupin dostupnosti. Nový model nasazení také poskytuje životní cyklus pro výpočetní prostředky, síť a úložiště nezávisle. Nakonec se zaměřte na povolení zabezpečení ve výchozím nastavení s vynucením virtuálních počítačů ve virtuální síti.

Téměř všechny funkce z modelu nasazení Classic jsou podporovány pro výpočetní výkon, síť a úložiště v rámci Azure Resource Manager. Pokud chcete využívat nové funkce v Azure Resource Manager, můžete migrovat existující nasazení z modelu nasazení Classic.

## <a name="supported-resources--configurations-for-migration"></a>Podporované prostředky & konfigurací pro migraci

### <a name="supported-resources-for-migration"></a>Podporované prostředky pro migraci
* Virtual Machines
* Skupiny dostupnosti
* Účty úložiště
* Virtuální sítě
* Brány VPN Gateway
* [Brány Express Route](../expressroute/expressroute-howto-move-arm.md) _(ve stejném předplatném jako Virtual Network)_
* Network Security Groups (Skupiny zabezpečení sítě)
* Směrovací tabulky
* Vyhrazené IP adresy

## <a name="supported-configurations-for-migration"></a>Podporované konfigurace pro migraci
Tyto klasické prostředky IaaS se během migrace podporují.

| Služba | Konfigurace |
| --- | --- |
| Azure AD Domain Services | [Virtuální sítě, které obsahují službu Azure AD Domain Services](../active-directory-domain-services/migrate-from-classic-vnet.md) |

## <a name="supported-scopes-of-migration"></a>Podporované obory migrace
Existují čtyři různé způsoby, jak dokončit migraci výpočetních, síťových a úložných prostředků:

* [Migrace virtuálních počítačů (mimo virtuální síť)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migrace virtuálních počítačů (ve virtuální síti)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migrace účtů úložiště](#migration-of-storage-accounts)
* [Migrace nepřipojených prostředků](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrace virtuálních počítačů (mimo virtuální síť)
V modelu nasazení Správce prostředků se ve výchozím nastavení pro vaše aplikace vynutilo zabezpečení. Všechny virtuální počítače musí být ve virtuální síti v modelu Správce prostředků. Platforma Azure v rámci migrace restartuje `Stop` `Deallocate` virtuální počítače (, a `Start` ). Máte dvě možnosti pro virtuální sítě, do kterých se Virtual Machines migrují:

* Můžete požádat, aby platforma vytvořila novou virtuální síť a migruje virtuální počítač do nové virtuální sítě.
* Virtuální počítač můžete migrovat do existující virtuální sítě v Správce prostředků.

> [!NOTE]
> V tomto oboru migrace se po určitou dobu nemusí v průběhu migrace Povolit operace roviny správy i rovina dat.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrace virtuálních počítačů (ve virtuální síti)
Pro většinu konfigurací virtuálních počítačů se migrují jenom metadata mezi klasickými a Správce prostředků modely nasazení. Základní virtuální počítače jsou spuštěné na stejném hardwaru, ve stejné síti a ve stejném úložišti. Operace roviny správy se nemusí po určitou dobu během migrace povolit. Rovina dat ale bude i nadále fungovat. To znamená, že vaše aplikace běžící na virtuálních počítačích (Classic) během migrace neúčtují výpadky.

Následující konfigurace se aktuálně nepodporují. Pokud se v budoucnu přidá podpora, některé virtuální počítače v této konfiguraci můžou způsobit výpadky (Projděte si operace zastavení, zrušení přidělení a restartování virtuálních počítačů).

* V jedné cloudové službě máte více než jednu skupinu dostupnosti.
* Máte jednu nebo víc skupin dostupnosti a virtuálních počítačů, které nejsou ve skupině dostupnosti v jedné cloudové službě.

> [!NOTE]
> V tomto oboru migrace se rovina správy nemusí v časovém intervalu během migrace povolit. U některých konfigurací, jak je popsáno výše, dochází k výpadkům datové roviny.
>

### <a name="migration-of-storage-accounts"></a>Migrace účtů úložiště
Pokud chcete umožňovat bezproblémové migrace, můžete nasadit Správce prostředků virtuální počítače v klasickém účtu úložiště. Díky této možnosti můžou být výpočetní prostředky a síťové prostředky migrovány nezávisle na účtech úložiště. Jakmile provedete migraci Virtual Machines a Virtual Network, budete muset migrovat účty úložiště, abyste dokončili proces migrace.

Pokud váš účet úložiště nemá žádné přidružené disky nebo Virtual Machines data a obsahuje jenom objekty blob, soubory, tabulky a fronty, můžete migraci na Azure Resource Manager udělat jako samostatnou migraci bez závislostí.

> [!NOTE]
> Model nasazení Správce prostředků nemá koncept klasických imagí a disků. Když se účet úložiště migruje, klasické image a disky se v Správce prostředkůovém zásobníku nezobrazí, ale záložní virtuální pevné disky zůstanou v účtu úložiště.

Následující snímky obrazovky ukazují, jak upgradovat klasický účet úložiště na účet služby Azure Resource Manager Storage pomocí Azure Portal:
1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Přejděte na svůj účet úložiště.
3. V části **Nastavení** klikněte na možnost **migrovat do ARM**.
4. Kliknutím na **ověřit** určete proveditelnost migrace.
5. Pokud ověření projde, kliknutím na **Příprava** vytvořte migrovaný účet úložiště.
6. Zadáním **Ano** potvrďte migraci a kliknutím na **Potvrdit** dokončete migraci.

    ![Ověřit účet úložiště](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Příprava účtu úložiště](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finalizace migrace účtu úložiště](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migrace nepřipojených prostředků
Účty úložiště bez přidružených disků nebo Virtual Machines dat se můžou migrovat nezávisle.

Skupiny zabezpečení sítě, směrovací tabulky & rezervované IP adresy, které nejsou připojené k žádným Virtual Machines a virtuálním sítím, se dají migrovat taky nezávisle.

<br>

## <a name="unsupported-features-and-configurations"></a>Nepodporované funkce a konfigurace
Některé funkce a konfigurace se aktuálně nepodporují. v následujících částech jsou popsána naše doporučení.

### <a name="unsupported-features"></a>Nepodporované funkce
Následující funkce se momentálně nepodporují. Volitelně můžete tato nastavení odebrat, migrovat virtuální počítače a pak znovu povolit nastavení v modelu nasazení Správce prostředků.

| Poskytovatel prostředků | Funkce | Doporučení |
| --- | --- | --- |
| Výpočetní prostředky | Nepřidružené disky virtuálních počítačů. | Objekty blob VHD na těchto discích se migrují při migraci účtu úložiště. |
| Výpočetní prostředky | Image virtuálních počítačů. | Objekty blob VHD na těchto discích se migrují při migraci účtu úložiště. |
| Síť | Seznamy ACL pro koncové body. | Odeberte seznamy ACL koncových bodů a zkuste migraci zopakovat. |
| Síť | Application Gateway | Před zahájením migrace odeberte Application Gateway a po dokončení migrace znovu vytvořte Application Gateway. |
| Síť | Virtuální sítě s využitím partnerského vztahu virtuálních sítí. | Migrujte Virtual Network na Správce prostředků a potom na peer. Přečtěte si další informace o [partnerském vztahu](../virtual-network/virtual-network-peering-overview.md)virtuálních sítí. |

### <a name="unsupported-configurations"></a>Nepodporované konfigurace
Následující konfigurace se aktuálně nepodporují.

| Služba | Konfigurace | Doporučení |
| --- | --- | --- |
| Resource Manager |Role-Based Access Control (RBAC) pro klasické prostředky |Vzhledem k tomu, že identifikátor URI prostředků se po migraci upraví, doporučujeme, abyste naplánovali aktualizace zásad RBAC, které se musí provést po migraci. |
| Výpočetní prostředky |Několik podsítí přidružených k virtuálnímu počítači |Aktualizujte konfiguraci podsítě tak, aby odkazovala pouze na jednu podsíť. To může vyžadovat odebrání sekundárního síťového adaptéru (který odkazuje na jinou podsíť) z virtuálního počítače a znovu ho připojit po dokončení migrace. |
| Výpočetní prostředky |Virtuální počítače, které patří do virtuální sítě, ale nemají přiřazenou explicitní podsíť |Volitelně můžete virtuální počítač odstranit. |
| Výpočetní prostředky |Virtuální počítače, které mají výstrahy, zásady automatického škálování |Migrace prochází a tato nastavení se zahozena. Důrazně doporučujeme, abyste před provedením migrace vyhodnotili své prostředí. Případně můžete nastavení výstrahy po dokončení migrace znovu nakonfigurovat. |
| Výpočetní prostředky |Rozšíření virtuálních počítačů XML (BGInfo 1. *, ladicí program sady Visual Studio, Nasazení webu a vzdálené ladění) |Tato funkce není podporovaná. Doporučuje se odebrat tato rozšíření z virtuálního počítače, aby bylo možné pokračovat v migraci, jinak budou během procesu migrace automaticky vyřazeny. |
| Výpočetní prostředky |Diagnostika spouštění s Premium Storage |Před pokračováním v migraci zakažte funkci diagnostiky spouštění pro virtuální počítače. Až se migrace dokončí, můžete znovu povolit diagnostiku spouštění v Správce prostředkůovém zásobníku. Kromě toho by se měly odstranit objekty blob používané pro snímky obrazovky a sériové protokoly, takže už se za tyto objekty blob neúčtují. |
| Výpočetní prostředky | Cloudové služby, které obsahují webové a pracovní role | To se v tuto chvíli nepodporuje. |
| Výpočetní prostředky | Cloudové služby, které obsahují více než jednu skupinu dostupnosti nebo více skupin dostupnosti. |To se v tuto chvíli nepodporuje. Před migrací prosím přesuňte Virtual Machines do stejné skupiny dostupnosti. |
| Výpočetní prostředky | Virtuální počítač s rozšířením Azure Security Center | Azure Security Center do Virtual Machines automaticky nainstaluje rozšíření pro monitorování jejich zabezpečení a vyvolávání výstrah. Tato rozšíření se většinou nainstalují automaticky, pokud je u předplatného povolená zásada Azure Security Center. Pokud chcete migrovat Virtual Machines, zakažte v předplatném zásadu Security Center, která odebere rozšíření monitorování Security Center z Virtual Machines. |
| Výpočetní prostředky | Virtuální počítač s příponou Backup nebo Snapshot | Tato rozšíření jsou nainstalována na virtuálním počítači nakonfigurovaném pomocí služby Azure Backup. I když migrace těchto virtuálních počítačů není podporovaná, postupujte podle pokynů [, abyste](./migration-classic-resource-manager-faq.md#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) zachovali zálohy, které byly podniknuty před migrací.  |
| Výpočetní prostředky | Virtuální počítač s rozšířením Azure Site Recovery | Tato rozšíření jsou nainstalována na virtuálním počítači nakonfigurovaném pomocí služby Azure Site Recovery. I když migrace úložiště použitá v Site Recovery funguje, bude to mít vliv na aktuální replikaci. Po migraci úložiště je nutné zakázat a povolit replikaci virtuálního počítače. |
| Síť |Virtuální sítě, které obsahují virtuální počítače a webové role nebo role pracovního procesu |To se v tuto chvíli nepodporuje. Před migrací prosím přesuňte webové a pracovní role do jejich vlastních Virtual Network. Po migraci klasického Virtual Network se migrované Azure Resource Manager Virtual Network můžou navázat s klasickou Virtual Networkou a dosáhnout podobné konfigurace jako předtím.|
| Síť | Klasické okruhy Express Route |To se v tuto chvíli nepodporuje. Tyto okruhy se musí před zahájením migrace IaaS migrovat na Azure Resource Manager. Další informace najdete v tématu [Přesun okruhů ExpressRoute z modelu nasazení Classic do modelu nasazení Správce prostředků](../expressroute/expressroute-move.md).|
| Azure App Service |Virtuální sítě obsahující App Service prostředí |To se v tuto chvíli nepodporuje. |
| Azure HDInsight |Virtuální sítě obsahující služby HDInsight |To se v tuto chvíli nepodporuje. |
| Služby životního cyklu Microsoft Dynamics |Virtuální sítě obsahující virtuální počítače, které jsou spravované službami životního cyklu Dynamics |To se v tuto chvíli nepodporuje. |
| Azure API Management |Virtuální sítě obsahující nasazení služby Azure API Management |To se v tuto chvíli nepodporuje. Pokud chcete migrovat virtuální síť IaaS, změňte virtuální síť nasazení API Management, což není operace bez výpadku. |

## <a name="next-steps"></a>Další kroky

* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](migration-classic-resource-manager-cli.md)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md)