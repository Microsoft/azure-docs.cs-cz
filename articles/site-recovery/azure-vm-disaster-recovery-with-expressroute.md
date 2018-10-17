---
title: Integrace Azure ExpressRoute se zotavení po havárii pro virtuální počítače Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje, jak nastavit zotavení po havárii pro virtuální počítače Azure pomocí Azure Site Recovery a Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 03fac23ea17a6baa1b43e748a4390cf142661a19
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353529"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrace Azure ExpressRoute se zotavení po havárii pro virtuální počítače Azure


Tento článek popisuje, jak integrovat Azure ExpressRoute s [Azure Site Recovery](site-recovery-overview.md), při nastavování zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure.

Site Recovery umožňuje zotavení po havárii virtuálních počítačů Azure pomocí replikace dat virtuálního počítače Azure do Azure.

- Pokud používáte virtuální počítače Azure [Azure managed disks](../virtual-machines/windows/managed-disks-overview.md), data virtuálního počítače se replikují na replikované spravovaného disku v sekundární oblasti.
- Pokud virtuální počítače Azure se používat spravované disky, virtuální počítač data se replikují do účtu služby Azure storage.
- Koncové body replikace jsou veřejné, ale provoz replikace pro virtuální počítače Azure není napříč Internetem.

ExpressRoute umožňuje rozšířit místní sítě do cloudu Microsoft Azure přes soukromé připojení zajišťované poskytovatelem připojení. Pokud máte nakonfigurovaný ExpressRoute, integruje s využitím Site Recovery následujícím způsobem:

- **Během replikace mezi oblastmi Azure**: provoz replikace pro zotavení po havárii virtuálního počítače Azure je jenom v rámci Azure a ExpressRoute není potřeba ani používanou k replikaci. Pokud se připojujete z místní lokality do virtuálních počítačů Azure v primární lokalitě Azure, existují však některé problémy, které je třeba vědět, když při nastavování zotavení po havárii pro tyto virtuální počítače Azure.
- **Převzetí služeb při selhání mezi oblastmi Azure**: když dojde k výpadku, můžete převzetí služeb při selhání virtuálních počítačů Azure z primární do sekundární oblasti Azure. Po převzetí služeb při selhání do sekundární oblasti, existuje mnoho kroků provést, aby přístup k virtuálním počítačům Azure v sekundární oblasti přes ExpressRoute.


## <a name="before-you-begin"></a>Než začnete

Než začnete, ujistěte se, že rozumíte následující pojmy:

- ExpressRoute [okruhy](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute [domény směrování](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
- ExpressRoute [umístění](../expressroute/expressroute-locations.md).
- Virtuální počítač Azure [architektura replikace](azure-to-azure-architecture.md)
- Jak [nastavení replikace](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítače Azure.
- Jak [převzetí služeb při selhání](azure-to-azure-tutorial-failover-failback.md) virtuální počítače Azure.


## <a name="general-recommendations"></a>Obecná doporučení

Osvědčený postup a zajistit efektivní cíle plánované doby obnovení (RTO) pro zotavení po havárii doporučujeme, abyste že při nastavování Site Recovery můžete integrovat s ExpressRoute se postupujte takto:

- Zřízení síťové součásti před převzetí služeb při selhání do sekundární oblasti:
    - Když povolíte replikaci pro virtuální počítače Azure, Site Recovery můžete automaticky nasadit síťové prostředky, jako jsou sítě, podsítě a bran v cílové oblasti Azure, na základě zdrojové síťové nastavení.
    - Site Recovery nejde automaticky nastavit síťové prostředky, jako jsou brány virtuální sítě.
    - Doporučujeme, abyste že zřízení těchto dalších síťových prostředků před převzetí služeb při selhání. Malý výpadek souvisí s tímto nasazením, a pokud jste účet pro něj při plánování nasazení může mít dopad čas celkové zotavení.
- Spusťte po havárii pravidelně cvičení obnovení:
    - Postup ověří vaši strategii replikace bez ztráty dat, bez výpadku a bez dopadu na vaše produkční prostředí. To pomáhá předejít problémy s konfigurací poslední minutu, které může nepříznivě ovlivnit RTO.
    - Při spuštění testovací převzetí služeb při selhání na postup zotavení, doporučujeme použít samostatnou síť virtuálních počítačů Azure, ne výchozí síť, který je nastavený při aktivaci replikace.
- Použijte jinou adresní prostory IP adres, pokud máte jeden okruh ExpressRoute.
    - Doporučujeme použít jiný prostor IP adres pro cílová virtuální síť. Tím se vyhnete problémy při navazování připojení při místní výpadky.
    - Pokud nemůžete použít samostatné adresní prostor, je nutné spustit po havárii obnovení procházení testovací převzetí služeb při selhání na samostatný test sítě s jinou IP adresy. Nejde propojit dvě virtuální sítě s překrývající se adresní prostor IP adres ke stejnému okruhu ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replikace virtuálních počítačů Azure při používání ExpressRoute


Pokud chcete nastavení replikace pro virtuální počítače Azure v primární lokalitě a připojujete k těmto virtuálním počítačům z místní lokality přes ExpressRoute, zde je, co je potřeba udělat:

1. [Povolení replikace](azure-to-azure-tutorial-enable-replication.md) pro každý virtuální počítač Azure.
2. Volitelně můžete nechat Site Recovery nastavení sítě:
    - Když nakonfigurujete a povolíte replikaci, Site Recovery nastaví sítí, podsítí a podsítě brány v cílové oblasti Azure, tak, aby odpovídala těm ve zdrojové oblasti. Site Recovery také zajišťuje mapování mezi zdrojovou a cílovou virtuální sítí.
    - Pokud nechcete, aby Site Recovery k tomu automaticky, vytvoření cílové straně síťových prostředků, než povolíte replikaci.
3. Vytvoření dalších síťových prvků:
    - Site Recovery nepodporuje vytvoření směrovací tabulky, brány virtuální sítě, připojení brány virtuální sítě, VNet peering, nebo jiné síťové prostředky a připojení v sekundární oblasti.
    - Je potřeba vytvořit tyto další síťové prvky v sekundární oblasti, kdykoli před spuštěním převzetí služeb při selhání z primární oblasti.
    - Můžete použít [plány obnovení](site-recovery-create-recovery-plans.md) a skripty pro automatizaci nastavení a připojení těchto síťových prostředků.
1. Pokud máte síťové virtuální zařízení (NVA) nasazené pro řízení toku síťového provozu, Všimněte si, že:
    - Azure pro výchozí systémovou trasu pro replikaci virtuálních počítačů Azure je 0.0.0.0/0.
    - Nasazení síťové virtuální zařízení obvykle také definovat výchozí trasa (0.0.0.0/0), která vynutí odchozí internetové přenosy do probíhat přes síťové virtuální zařízení. Výchozí trasa se používá při najdete žádná jiná konfigurace konkrétní trasy.
    - Pokud je to tento případ, síťové virtuální zařízení mohou být přetíženy, pokud všechny replikace provoz prochází přes síťové virtuální zařízení.
    - Stejné omezení platí také při použití výchozí trasy pro směrování veškerého provozu virtuálních počítačů Azure do místní nasazení.
    - V tomto scénáři doporučujeme, aby vám [vytvořit koncový bod služby sítě](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) ve vaší virtuální síti pro službu Microsoft.Storage tak, aby provoz replikace nebude neopustí hranice Azure.

## <a name="replication-example"></a>Příklad replikace

V případě podnikového nasazení obvykle mají úlohy rozdělit mezi více virtuálními sítěmi Azure, pomocí připojení k centrální rozbočovač pro externí připojení k Internetu a místními umístěními. Hvězdicová topologie se obvykle používá spolu s ExpressRoute.

![V místní – Azure pomocí ExpressRoute před převzetí služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Oblast**. Aplikace nasazená v oblasti východní Asie Azure.
- **Virtuální sítě paprsků**. Jsou aplikace nasazené ve dvou virtuálních sítích paprsků:
    - **Zdroj vNet1**: 10.1.0.0/24.
    - **Zdroj vNet2**: 10.2.0.0/24.
    - Virtuální síť každého paprsku je připojen k **virtuální síť centra**.
- **Virtuální síť centra**. Je virtuální síť centra **virtuální síť centra zdroje**: 10.10.10.0/24.
    - Tato virtuální síť centra funguje jako vrátný.
    - Veškerá komunikace mezi podsítěmi, projděte si toto centrum.
 - Centrum vNet podsítě **. Virtuální síť centra má dvě podsítě:
     - **Podsíť síťových virtuálních zařízení**: 10.10.10.0/25. Tato podsíť obsahuje síťové virtuální zařízení (. 10.10.10.10).
     - **Podsíť brány**: 10.10.10.128/25. Tato podsíť obsahuje bránu ExpressRoute připojené k připojení ExpressRoute, který směruje k místní lokalitě přes privátní partnerský vztah domény směrování.
- Místní datové centrum má připojení okruhu ExpressRoute prostřednictvím partnerů hrany v Hongkong –.
- Všechny směrování je řízen pomocí tabulek Azure trasu (UDR).
- Veškerý odchozí provoz mezi virtuálními sítěmi nebo do místního datového centra je směrován přes síťové virtuální zařízení.

### <a name="hub-and-spoke-peering-settings"></a>Střed a paprsek nastavení partnerského vztahu

#### <a name="spoke-to-hub"></a>Od paprsku k centru

**Směr** | **Nastavení** | **Stav**
--- | --- | ---
Od paprsku k centru | Povolit virtuální síťová adresa | Povoleno
Od paprsku k centru | Povolit přesměrovaný přenos | Povoleno
Od paprsku k centru | Povolit průchod bránou | Zakázáno
Od paprsku k centru | Odebrání brány použít | Povoleno

 ![Paprsků centra konfigurace partnerského vztahu](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Od centra k paprsku

**Směr** | **Nastavení** | **Stav**
--- | --- | ---
Od centra k paprsku | Povolit virtuální síťová adresa | Povoleno
Od centra k paprsku | Povolit přesměrovaný přenos | Povoleno
Od centra k paprsku | Povolit průchod bránou | Povoleno
Od centra k paprsku | Odebrání brány použít | Zakázáno

 ![Centrum paprsků konfigurace partnerského vztahu](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Příklady kroků

V našem příkladu následující by měl nastat při povolení replikace pro virtuální počítače Azure ve zdrojové síti:

1. Můžete [povolit replikaci](azure-to-azure-tutorial-enable-replication.md) pro virtuální počítač.
2. Site Recovery vytvoří v cílové oblasti repliky virtuálních sítí, podsítí a podsítě brány.
3. Mapování mezi sítěmi zdrojové a cílové sítě repliky, který vytvoří nastavení vytvoří Site Recovery.
4. Ruční vytvoření brány virtuální sítě, připojení brány virtuální sítě, partnerský vztah virtuální sítě, nebo jakékoli jiné síťové prostředky nebo připojení.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Při použití ExpressRoute převzetí služeb při selhání virtuálních počítačů Azure

Po převzetí služeb při selhání virtuálních počítačů Azure do cílové oblasti Azure pomocí Site Recovery, které můžete k nim přistupovat pomocí ExpressRoute [soukromého partnerského vztahu](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

- Je potřeba připojení k cílové virtuální sítě s novým připojením ExpressRoute. Existující připojení ExpressRoute se automaticky převedena.
- Způsob, ve kterém můžete nastavit připojení ExpressRoute k cílové síti, závisí na vaší topologie ExpressRoute.


### <a name="access-with-two-circuits"></a>Přístup se dva okruhy

#### <a name="two-circuits-with-two-peering-locations"></a>Dva okruhy s dvěma umístění partnerského vztahu

Tato konfigurace pomáhá chránit okruhy ExpressRoute proti regionálního. Pokud vaše primární partnerského vztahu loation ocitne mimo provoz, můžete pokračovat v připojení z jiného umístění.

- Okruh připojené do produkčního prostředí je obvykle primární. Sekundární okruh obvykle má menší šířku pásma, které je možné zvýšit, pokud dojde k havárii.
- Po převzetí služeb při selhání můžete vytvořit připojení k cílové síti ze sekundární okruh ExpressRoute. Alternativně můžete mít nastavené a připravené v případě havárie, snížit celkový čas obnovení připojení.
- Souběžná připojení pro obě primární a cílové virtuální sítě Ujistěte se, že používá směrování jen na místní sekundární okruh a připojení po převzetí služeb při selhání.
- Zdrojové a cílové virtuální sítě můžete dostávat nové IP adresy nebo zachovat shodné, po převzetí služeb při selhání. V obou případech je možné navázat připojení sekundární před převzetí služeb při selhání.


#### <a name="two-circuits-with-single-peering-location"></a>Dva okruhy pomocí jednoho umístění partnerského vztahu

Tato konfigurace pomáhá chránit proti selhání primární okruh ExpressRoute, ale ne v případě, že jeden umístění partnerského vztahu ExpressRoute se ocitne mimo provoz, vliv na obou okruzích.

- Souběžná připojení z místního datového centra zdrojová virtuální síť s primární okruh a cílové virtuální sítě s sekundární okruh může mít.
- Současných připojení k primární a cílové Ujistěte se, která pro místní směrování pouze používá sekundární okruh a připojení po převzetí služeb při selhání.
-   Obě okruhy nemůže připojit ke stejné virtuální síti okruhy vytvořené ve stejném umístění partnerského vztahu.

### <a name="access-with-a-single-circuit"></a>Přístup pomocí jednoho okruhu

V této konfiguraci je jenom jeden okruh Expressroute. I když je okruh má redundantní připojení v případě, že jeden ocitne mimo provoz, jednu trasu okruhu neposkytne odolnost, pokud partnerského vztahu oblast přestane fungovat. Poznámky:

- Můžete replikovat virtuální počítače Azure do jakékoliv oblasti Azure v [stejné zeměpisné polohy](azure-to-azure-support-matrix.md#region-support). Není-li cílovou oblastí Azure ve stejném umístění jako zdroj, je potřeba povolit ExpressRoute Premium, pokud používáte jeden okruh ExpressRoute. Další informace o [umístění ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) a [ceny za ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Nemůžete se připojit zdrojové a cílové virtuální sítě současně k okruhu Pokud se používá stejné adresní prostor IP adres v cílové oblasti. V tomto scénáři:    
    -  Odpojit připojení na straně zdroje a potom musí vytvořit připojení na straně cíl. Tato změna připojení možnost využívat skripty jako součást plánu obnovení Site Recovery. Poznámky:
        - Selhání v oblasti pokud je primární oblast nedostupná operace odpojení, může selhat. Může to mít dopad vytvoření připojení k cílové oblasti.
        - Pokud jste nevytvořili připojení v cílové oblasti a později obnoví primární oblasti, pokud může dojít k drops paketů dvě souběžná připojení se pokusí připojit k stejném adresním prostoru.
        - Chcete-li tomu zabránit, ukončete ihned primární připojení.
        - Za virtuální počítač navrácení služeb po obnovení do primární oblasti primární připojovací lze znovu navázat, po odpojení sekundárního připojení.
-   Pokud různé adresní prostory se používá v cílové virtuální sítě, můžete současně připojit k virtuálním sítím zdroj a cíl z stejnému okruhu ExpressRoute.


## <a name="failover-example"></a>Příklad převzetí služeb při selhání

V našem příkladu používáme následující topologie:

- Dva různé okruhy ExpressRoute ve dvou různých umístění partnerského vztahu.
- Zachovat privátní IP adresy pro virtuální počítače Azure po převzetí služeb při selhání.
- Cílová oblast pro obnovení je Azure jihovýchodní Asie.
- Sekundární připojení okruhu ExpressRoute pokládáme stav, prostřednictvím partnera edge in Singapore.

Pro jednoduchou topologie, který používá jeden okruh ExpressRoute pomocí stejné IP adresy po převzetí služeb při selhání [k tomuto článku](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

### <a name="example-steps"></a>Příklady kroků
Co je potřeba automatizovat zotavení v tomto příkladu se pro:

1. Uvedený postup [nastavení replikace](#azure-vm-replication-steps).
2. [Převzetí služeb při selhání virtuálních počítačů Azure](azure-to-azure-tutorial-failover-failback.md), tímto postupem další během nebo po převzetí služeb.

    a. Vytvořte bránu Azure ExpressRoute ve virtuální síti centra cílové oblasti. To je nutné se připojit virtuální síť centra cíl k okruhu ExpressRoute.

    b. Vytvoření připojení z virtuální sítě centra cíl k cíli okruh ExpressRoute.

    c. Nastavte partnerské vztahy virtuálních sítí mezi centrem cílové oblasti a virtuálními sítěmi paprsků. Vlastnosti partnerského vztahu v cílové oblasti budou stejné jako ty ve zdrojové oblasti.

    d. Nastavte trasy definované uživatelem ve virtuální síti centra a dvě virtuální sítě paprsků.

    - Vlastnosti straně cíle trasy definované uživatelem se shodují s nastaveními na straně zdroje při pomocí stejné IP adres.
    - S jinou cílovou IP adresy třeba upravit odpovídajícím způsobem trasy definované uživatelem.


Jako součást možnost využívat skripty výše uvedené kroky [plánu obnovení](site-recovery-create-recovery-plans.md). V závislosti na připojení k aplikaci a požadavky na čas obnovení je možné výše uvedené kroky provést i před spuštěním převzetí služeb při selhání.

#### <a name="after-recovery"></a>Po obnovení

Po obnovení virtuálních počítačů a dokončením připojení, je obnovení prostředí.

![V místní – Azure pomocí ExpressRoute po převzetí služeb při selhání](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Další postup

Další informace o používání [plány obnovení](site-recovery-create-recovery-plans.md) automatizovat převzetí služeb při selhání aplikace.
