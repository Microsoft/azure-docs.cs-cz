---
title: O Azure Site Recovery
description: Poskytuje přehled služby Azure Site Recovery a shrnuje scénáře zotavení po havárii a nasazení migrace.
ms.topic: overview
ms.date: 03/17/2020
ms.custom: MVC
ms.openlocfilehash: 19860e64182cd73fe9f9fa1246f440a03109d465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92281875"
---
# <a name="about-site-recovery"></a>O službě Azure Site Recovery

Vítá vás služba Azure Site Recovery! Tento článek přináší stručný přehled služby.

Jako organizace potřebujete, abyste přijali strategii pro provozní kontinuitu a zotavení po havárii (BCDR), která udržuje vaše data v bezpečí a vaše aplikace a úlohy online, když dojde k plánovaným a neplánovaným výpadkům.

Azure Recovery Services přispívá ke strategii BCDR:

- **Služba Site Recovery:** Site Recovery pomáhá zajistit kontinuitu podnikových procesů tím, že zajišťuje provoz obchodních aplikací a úloh během výpadků. Site Recovery replikuje úlohy spuštěné na fyzických a virtuálních počítačích z primární lokality do sekundárního umístění. Když dojde k výpadku v primární lokalitě, převezme služby při selhání sekundární lokalita a přistupujete k aplikacím z ní. Po opětovném zprovoznění můžete navrátit služby do primární lokality.
- **Služba zálohování**: Služba [Azure Backup](../backup/index.yml) udržuje vaše data bezpečná a obnovitelná.

Site Recovery může spravovat replikaci pro:

- Virtuální počítače Azure replikované mezi oblastmi Azure.
- Místní virtuální počítače, Azure Stack virtuální počítače a fyzické servery.

## <a name="what-does-site-recovery-provide"></a>Co Site Recovery poskytuje?

**Funkce** | **Podrobnosti**
--- | ---
**Jednoduché řešení BCDR** | Pomocí Site Recovery můžete nastavit a spravovat replikaci, převzetí služeb při selhání a navrácení služeb po obnovení z jednoho místa na webu Azure Portal.
**Replikace virtuálních počítačů Azure** | Můžete nastavit zotavení po havárii virtuálních počítačů Azure z primární oblasti do sekundární.
**Replikace místních virtuálních počítačů** | Místní virtuální počítače a fyzické servery můžete replikovat do Azure nebo do sekundárního místního datového centra. Replikací do Azure se eliminují náklady a složitost spojené s udržováním sekundárního datového centra.
**Replikace úloh** | Můžete replikovat libovolné úlohy spuštěné na podporovaných virtuálních počítačích Azure, místních virtuálních počítačích Hyper-V a VMware a na fyzických serverech s Windows nebo Linuxem.
**Odolnost dat** | Site Recovery orchestruje replikaci bez zachycení dat aplikace. Při replikaci do Azure se data se ukládají službě Azure Storage s odolností, kterou tato služba nabízí. Pokud dojde k převzetí služeb při selhání, na základě replikovaných dat se vytvoří virtuální počítače Azure.
**Cíle plánované doby obnovení (RTO) a plánovaných bodů obnovení (RPO)** | Udržujte cíle plánované doby obnovení (RTO) a plánovaných bodů obnovení (RPO) v mezích limitů vaší organizace. Site Recovery poskytuje průběžnou replikaci virtuálních počítačů Azure a VMware a umožňuje frekvenci replikací pouhých 30 sekund. Plánovanou dobu obnovení (RTO) můžete ještě snížit integrací se službou [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Zachování konzistence aplikací při převzetí služeb při selhání** | Můžete replikovat s využitím bodů obnovení se snímky konzistentními vzhledem k aplikacím. Tyto snímky zachycují data na disku, veškerá data v paměti a všechny probíhající transakce.
**Testování bez výpadků** | Můžete snadno nacvičovat postupy zotavení po havárii, aniž by to mělo dopad na probíhající replikaci.
**Flexibilní převzetí služeb při selhání** | Plánované převzetí služeb při selhání můžete spustit pro očekávané výpadky s nulovou ztrátou dat. Nebo neplánované převzetí služeb při selhání s minimální ztrátou dat, v závislosti na četnosti replikací, pro neočekávané havárie. Služby potom můžete po obnovení snadno navrátit do primární lokality, až bude znovu dostupná.
**Přizpůsobené plány obnovení** | Pomocí plánů obnovení můžete přizpůsobit a nasekvencovat převzetí služeb při selhání a obnovení vícevrstvých aplikací běžících na několika virtuálních počítačích. Můžete počítače seskupit dohromady v plánu obnovení a volitelně přidat skripty a ruční akce. Plány obnovení je možné integrovat s runbooky služby Azure Automation.
**Integrace BCDR** | Site Recovery se integruje s dalšími technologiemi BCDR. Site Recovery je například možné použít k ochraně back-endu SQL Serveru u firemních úloh (s využitím nativní podpory pro SQL Server AlwaysOn) a správě převzetí služeb při selhání skupin dostupnosti.
**Integrace služby Azure Automation** | Bohatá knihovna Azure Automation poskytuje skripty specifické pro aplikace a připravené pro produkční prostředí, které je možné stáhnout a integrovat se Site Recovery.
**Integrace sítě** | Site Recovery se integruje s Azure pro správu sítě aplikací. Například pro rezervaci IP adres, konfiguraci nástrojů pro vyrovnávání zatížení a používání Azure Traffic Manager pro efektivní přepínání sítí.

## <a name="what-can-i-replicate"></a>Co mohu replikovat?

**Podporováno** | **Podrobnosti**
--- | ---
**Scénáře replikace** | Replikace virtuálních počítačů Azure z jedné oblasti Azure do jiné<br/><br/>  Replikace místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, fyzických serverů (Windows a Linux) Azure Stack virtuálních počítačů do Azure.<br/><br/> Replikace instancí Windows AWS do Azure<br/><br/> Replikace místních virtuálních počítačů VMware, virtuálních počítačů Hyper-V, které spravuje System Center VMM, a fyzických serverů do sekundární lokality
**Oblasti** | Prohlédněte si [podporované oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=site-recovery) pro Site Recovery. |
**Replikované počítače** | Zkontrolujte požadavky na replikaci pro [virtuální počítače Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), [místní fyzické servery a virtuální počítače VMware](vmware-physical-azure-support-matrix.md#replicated-machines) a [místní virtuální počítače Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms).
**Úlohy** | Můžete replikovat jakoukoli úlohu běžící v počítači, u kterého se podporuje replikace. A Site Recovery tým provedl testy konkrétní aplikace pro určitý [počet aplikací](site-recovery-workload.md#workload-summary).

## <a name="next-steps"></a>Další kroky

- Další informace o [podpoře úloh](site-recovery-workload.md)
- [Začínáme](azure-to-azure-quickstart.md) s replikací virtuálních počítačů Azure mezi oblastmi.
