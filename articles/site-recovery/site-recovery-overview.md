---
title: Informace o Azure Site Recovery | Microsoft Docs
description: Poskytuje přehled služby Azure Site Recovery a shrnuje scénáře nasazení.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a3242c9402d738d580f4abf5f1a0bb64f2fa7728
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919899"
---
# <a name="about-site-recovery"></a>O službě Azure Site Recovery

Vítá vás služba Azure Site Recovery! Tento článek přináší stručný přehled služby.

Jako organizace musíte implementovat strategii BCRD (obchodní kontinuita a zotavení po havárii), která zajistí bezpečnost vašich dat a provoz vašich aplikací a úloh, když dojde k plánovaným nebo neplánovaným výpadkům.

Služba Azure Recovery Services přispívá ke strategii BCDR:

- **Služba Site Recovery:** Site Recovery pomáhá zajistit kontinuitu podnikových procesů tím, že zajišťuje provoz obchodních aplikací a úloh během výpadků. Site Recovery replikuje úlohy spuštěné na fyzických a virtuálních počítačích z primární lokality do sekundárního umístění. Když dojde k výpadku v primární lokalitě, převezme služby při selhání sekundární lokalita a přistupujete k aplikacím z ní. Po opětovném zprovoznění můžete navrátit služby do primární lokality.  
- **Služba Backup:** Služba [Azure Backup](https://docs.microsoft.com/azure/backup/) zajišťuje zabezpečení a obnovitelnost vašich dat tím, že je zálohuje do Azure.

Site Recovery může spravovat replikaci pro:

- Virtuální počítače Azure replikované mezi oblastmi Azure.
- Místní virtuální počítače a fyzické servery replikované do Azure nebo sekundární lokality.


## <a name="what-does-site-recovery-provide"></a>Co Site Recovery poskytuje?


**Funkce** | **Podrobnosti**
--- | ---
**Jednoduché řešení BCDR** | Pomocí Site Recovery můžete nastavit a spravovat replikaci, převzetí služeb při selhání a navrácení služeb po obnovení z jednoho místa na webu Azure Portal.
**Replikace virtuálních počítačů Azure** | Můžete nastavit zotavení po havárii virtuálních počítačů Azure z primární oblasti do sekundární.
**Replikace místních virtuálních počítačů** | Místní virtuální počítače a fyzické servery můžete replikovat do Azure nebo do sekundárního místního datového centra. Replikací do Azure se eliminují náklady a složitost spojené s udržováním sekundárního datového centra.
**Replikace úloh** | Můžete replikovat libovolné úlohy spuštěné na podporovaných virtuálních počítačích Azure, místních virtuálních počítačích Hyper-V a VMware a na fyzických serverech s Windows nebo Linuxem.
**Odolnost dat** | Site Recovery orchestruje replikaci bez zachycování dat aplikací. Při replikaci do Azure se data se ukládají službě Azure Storage s odolností, kterou tato služba nabízí. Pokud dojde k převzetí služeb při selhání, na základě replikovaných dat se vytvoří virtuální počítače Azure.
**Cíle plánované doby obnovení (RTO) a plánovaných bodů obnovení (RPO)** | Udržujte cíle plánované doby obnovení (RTO) a plánovaných bodů obnovení (RPO) v mezích limitů vaší organizace. Site Recovery poskytuje průběžnou replikaci virtuálních počítačů Azure a VMware a umožňuje frekvenci replikací pouhých 30 sekund. Plánovanou dobu obnovení (RTO) můžete ještě snížit integrací se službou [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Zachování konzistence aplikací při převzetí služeb při selhání** | Můžete replikovat s využitím bodů obnovení se snímky konzistentními vzhledem k aplikacím. Tyto snímky zachycují data na disku, veškerá data v paměti a všechny probíhající transakce.
**Testování bez výpadků** | Můžete snadno nacvičovat postupy zotavení po havárii, aniž by to mělo dopad na probíhající replikaci.
**Flexibilní převzetí služeb při selhání** | Pro očekávané výpadky můžete spouštět plánovaná převzetí služeb při selhání bez ztráty dat. V případě neočekávaných havárií pak můžou proběhnout neplánovaná převzetí služeb při selhání s minimálními ztrátami dat (v závislosti na frekvenci replikací). Služby potom můžete po obnovení snadno navrátit do primární lokality, až bude znovu dostupná.
**Přizpůsobené plány obnovení** | Pomocí plánů obnovení můžete přizpůsobit a nastavit pořadí převzetí služeb při selhání a zotavení vícevrstvých aplikací na několika virtuálních počítačích. Můžete počítače seskupit dohromady v plánu obnovení a volitelně přidat skripty a ruční akce. Plány obnovení je možné integrovat s runbooky služby Azure Automation.
**Integrace BCDR** | Site Recovery se integruje s dalšími technologiemi BCDR. Site Recovery je například možné použít k ochraně back-endu SQL Serveru u firemních úloh (s využitím nativní podpory pro SQL Server AlwaysOn) a správě převzetí služeb při selhání skupin dostupnosti.
**Integrace služby Azure Automation** | Bohatá knihovna Azure Automation poskytuje skripty specifické pro aplikace a připravené pro produkční prostředí, které je možné stáhnout a integrovat se Site Recovery.
**Integrace sítě** | Site Recovery se integruje s Azure a poskytuje jednoduchou správu aplikační sítě, včetně rezervace IP adres, konfigurace nástrojů pro vyrovnávání zatížení a integrace služby Azure Traffic Manager pro efektivní přepínání sítí.


## <a name="what-can-i-replicate"></a>Co mohu replikovat?

**Podporuje se** | **Podrobnosti**
--- | ---
**Scénáře replikace** | Replikace virtuálních počítačů Azure z jedné oblasti Azure do jiné<br/><br/>  Replikace místních virtuálních počítačů VMware a Hyper-V a fyzických serverů (s Windows nebo Linuxem) do Azure<br/><br/> Replikace místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, které spravuje System Center VMM, a fyzických serverů do sekundární lokality
**Oblasti** | Prohlédněte si [podporované oblasti](https://azure.microsoft.com/regions/services/) pro Site Recovery. |
**Replikované počítače** | Zkontrolujte požadavky na replikaci pro [virtuální počítače Azure](azure-to-azure-support-matrix.md#support-for-replicated-machine-os-versions), [místní fyzické servery a virtuální počítače VMware](vmware-physical-azure-support-matrix.md#replicated-machines) a [místní virtuální počítače Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms).
**Hostitelé/servery VMware** | Virtuální počítače VMware, které chcete replikovat, můžou být umístěné na [podporovaných hostitelích a serverech virtualizace](vmware-physical-azure-support-matrix.md).
**Úlohy** | Můžete replikovat jakoukoli úlohu běžící v počítači, u kterého se podporuje replikace. Kromě toho tým Site Recovery [pro řadu aplikací](site-recovery-workload.md#workload-summary) provedl testování specifické pro aplikace.



## <a name="next-steps"></a>Další kroky
* Další informace o [podpoře úloh](site-recovery-workload.md)
* [Začínáme](azure-to-azure-quickstart.md) s replikací virtuálních počítačů Azure mezi oblastmi. 
