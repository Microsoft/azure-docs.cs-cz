---
title: Zachování IP adresy během převzetí služeb při selhání virtuálního počítače Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje, jak zachovat IP adresy po převzetí služeb při selhání virtuálních počítačů Azure pro zotavení po havárii do sekundární oblasti Azure Site Recovery
ms.service: site-recovery
ms.date: 10/16/2018
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 4e75ba210e12a39d2c4cfb9753bbc2da2893746b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567396"
---
# <a name="retain-ip-addresses-during-failover"></a>Zachování IP adresy během převzetí služeb při selhání

[Azure Site Recovery](site-recovery-overview.md) umožňuje zotavení po havárii pro virtuální počítače Azure tak, že replikace virtuálních počítačů do jiné oblasti Azure, převzetí služeb při selhání v případě, že dojde k výpadku a selhání zpátky do primární oblasti při věci zpět na normální.

Během převzetí služeb při selhání můžete chtít zachovat IP adres v cílové oblasti stejné jako zdrojové oblasti:

- Ve výchozím nastavení Pokud povolíte obnovení po havárii pro virtuální počítače Azure Site Recovery vytvoří cílové prostředky na základě zdroje prostředků nastavení. Pro Azure: virtuální počítače nakonfigurované se statickými IP adresami Site Recovery se pokusí zřízení stejnou IP adresu pro cílový virtuální počítač, pokud není používán. Úplné vysvětlení způsobu, jakým Site Recovery zpracovává adresování [k tomuto článku](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Pro jednoduché aplikace stačí výchozí konfiguraci. Pro složitější aplikace může být nutné ke zřízení dalších prostředků, abyste měli jistotu, že připojení funguje podle očekávání po převzetí služeb při selhání.


Tento článek obsahuje příklady pro zachování IP adresy v složitější příklad scénáře. Příklady:

- Převzetí služeb při selhání pro společnost ke všem prostředkům v Azure
- Převzetí služeb při selhání pro společnost s hybridní nasazení a prostředky spuštěné místně a v Azure

## <a name="resources-in-azure-full-failover"></a>Prostředky v Azure: úplné převzetí služeb při selhání

Společnost A má své aplikace běžící v Azure.

### <a name="before-failover"></a>Před převzetí služeb při selhání

Tady je architektura před převzetí služeb při selhání.

- Společnosti A má stejné sítě a podsítí ve zdrojové a cílové oblasti Azure.
- Pokud chcete snížit plánovanou dobu obnovení (RTO), společnost používá uzlech replik pro SQL Server AlwaysOn, řadiče domény, atd. Tyto uzly repliky jsou v jiné virtuální síti v cílové oblasti tak, aby se může vytvořit připojení site-to-site VPN mezi zdrojovou a cílovou oblastí. To není možné, pokud se používá stejné adresní prostor IP adres v zdroj a cíl.  
- Před převzetí služeb při selhání Síťová architektura vypadá takto:
    - Primární oblast je Azure východní Asie
        - Východní Asie má virtuální síť (**zdrojová virtuální síť**) s adres 10.1.0.0/16 místa.
        - Východní Asie má úlohy rozdělit mezi tři podsítě ve virtuální síti:
            - **Podsíť 1**: 10.1.1.0/24
            - **Podsíť 2**: 10.1.2.0/24,
            - **Podsíť 3**: 10.1.3.0/24
    - Je sekundární (cíl) oblast Azure jihovýchodní Asie
        - Jihovýchodní Asie má virtuální síť (**virtuální sítě pro zotavení**) stejný jako **zdrojová virtuální síť**.
        - Další virtuální síť má jihovýchodní Asie (**virtuální sítě Azure**) s 10.2.0.0/16 prostor adres.
        - **Virtuální síť Azure** obsahuje podsíť (**podsítě 4**) s 10.2.4.0/24 prostor adres.
        - Řadič etc uzlech replik pro SQL Server AlwaysOn, jsou umístěny v **podsítě 4**.
    - **Zdrojové virtuální sítě** a **virtuální sítě Azure** připojeni pomocí připojení site-to-site VPN.
    - **Virtuální sítě pro zotavení** není spojena s jinou virtuální síť.
    - **Společnosti A** přiřadí/ověří cílové IP adresy pro replikované položky. Cílová IP adresa je stejná jako zdrojová IP adresa pro každý virtuální počítač.

![Prostředky v Azure před úplnou převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po převzetí služeb při selhání

Pokud dojde k oblastnímu výpadku zdroj, může společnost A převzetí služeb při selhání všechny její prostředky na cílové oblasti.

- Pomocí IP adresy už na místě před převzetí služeb při selhání, firmy A orchestrovat převzetí služeb při selhání a automatickému vytvoření připojení po převzetí služeb při selhání mezi **virtuální sítě pro zotavení** a **virtuální sítě Azure**. To je znázorněno v následujícím diagramu...
- V závislosti na požadavcích aplikace, připojení mezi dvěma virtuálními sítěmi (**virtuální sítě pro zotavení** a **virtuální sítě Azure**) v cílové oblasti lze zavedených před, během (jako přechodný krok při) nebo po převzetí služeb.
    - Společnost můžete použít [plány obnovení](site-recovery-create-recovery-plans.md) k určení, kdy se připojení naváže.
    - Jejich propojení virtuálních sítí pomocí VNet peering nebo VPN typu site-to-site.
        - VNet peering nepoužívá bránu sítě VPN a má jiná omezení.
        - Partnerský vztah virtuální sítě [ceny](https://azure.microsoft.com/pricing/details/virtual-network) se vypočítává odlišně od připojení typu VNet-to-VNet VPN Gateway [ceny](https://azure.microsoft.com/pricing/details/vpn-gateway). Pro převzetí služeb při selhání obecně doporučujeme používat stejnou metodu připojení jako zdrojové sítě, včetně typu připojení, chcete-li minimalizovat neočekávané síti incidenty.

    ![Prostředky v Azure úplné převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Prostředky v Azure: izolovaný režim převzetí služeb při selhání aplikace

Možná budete muset převzetí služeb při selhání na úrovni aplikace. Například pro převzetí služeb při selhání na konkrétní aplikaci nebo vrstvy aplikace umístěna ve vyhrazené podsíti.

- V tomto scénáři i když můžete zachovat IP adresy, není obecně vhodné od jeho hodnota se zvyšuje riziko nesrovnalostí připojení. Také ztratíte připojení podsítě do jiných podsítí v rámci stejné virtuální síti Azure.
- Lepší způsob, jak provést převzetí služeb při selhání na úrovni podsítě aplikace je použít jiné cílové IP adresy pro převzetí služeb při selhání (Pokud potřebujete připojení k jiné podsíti ve zdroji VNet) nebo k izolaci jednotlivých aplikací ve své vlastní vyhrazené virtuální sítě ve zdrojové oblasti. Druhý přístup můžete navázat připojení mezi sítěmi ve zdrojové oblasti a emulovat stejné chování při převzetí služeb při selhání do cílové oblasti.  

V tomto příkladu A místa aplikacím ve zdrojové oblasti v vyhrazené virtuální sítě a naváže připojení mezi těmito virtuálními sítěmi. V tomto návrhu může provádět převzetí služeb při selhání izolované aplikace a zachovat zdroj privátních IP adres v cílové síti.

### <a name="before-failover"></a>Před převzetí služeb při selhání

Před převzetí služeb při selhání architektura vypadá takto:

- Virtuální počítače aplikací jsou hostované v primární oblasti Azure Asie – východ:
    - **Počítač app1** jsou umístěné virtuální počítače ve virtuální síti **zdrojové virtuální sítě 1**: 10.1.0.0/16.
    - **Počítači App2** jsou umístěné virtuální počítače ve virtuální síti **zdrojové virtuální sítě 2**: 10.2.0.0/16.
    - **Zdrojové virtuální sítě 1** se dvěma podsítěmi.
    - **Zdrojové virtuální sítě 2** se dvěma podsítěmi.
- (Cíl) sekundární oblasti Azure jihovýchodní Asie – obnovení virtuální sítě má jihovýchodní Asie (**1 virtuální sítě pro zotavení** a **2 virtuální sítě pro zotavení**), které jsou stejné jako **zdrojové virtuální sítě 1** a **Zdroje Vnet2**.
        - **Virtuální síť pro obnovení 1** a **2 virtuální sítě pro zotavení** mají dvě podsítě, které odpovídají podsítě v **zdrojové virtuální sítě 1** a **zdrojové virtuální sítě 2** -jihovýchodní Asie má Další virtuální síť (**virtuální sítě Azure**) s 10.3.0.0/16 prostor adres.
        - **Virtuální síť Azure** obsahuje podsíť (**podsítě 4**) s 10.3.4.0/24 prostor adres.
        -Replika uzly pro SQL Server AlwaysOn, řadič etc jsou umístěny v **podsítě 4**.
- Existuje několik připojení site-to-site VPN: 
    - **Zdrojové virtuální sítě 1** a **virtuální síť Azure**
    - **Zdroj Vnet2** a **virtuální síť Azure**
    - **Zdrojové virtuální sítě 1** a **zdrojové virtuální sítě 2** jsou spojeny s VPN typu site-to-site
- **Virtuální síť pro obnovení 1** a **2 virtuální sítě pro zotavení** nejsou připojené k jiné virtuální sítě.
- **Společnosti A** nakonfiguruje bran VPN na **1 virtuální sítě pro zotavení** a **2 virtuální sítě pro zotavení**, abyste snížili RTO.  
- **Obnovení ze sítě VNet1** a **obnovení ze sítě VNet2** nejsou připojené žádné virtuální sítě.
- Pokud chcete snížit plánovanou dobu obnovení (RTO), brány VPN jsou nakonfigurované na **obnovení ze sítě VNet1** a **obnovení ze sítě VNet2** před převzetí služeb při selhání.

    ![Prostředky v Azure před převzetí služeb při selhání aplikace](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Po převzetí služeb při selhání

V případě výpadku nebo problém, který má vliv na jednu aplikaci (v ** zdrojové virtuální sítě 2 v našem příkladu), ovlivněných aplikace společnosti A dají obnovit takto:


- Odpojit připojení sítě VPN mezi **zdroje ze sítě VNet1** a **zdroje ze sítě VNet2**a mezi **zdroje ze sítě VNet2** a **virtuální sítě Azure** .
- Navázání připojení VPN mezi **zdroje ze sítě VNet1** a **obnovení ze sítě VNet2**a mezi **obnovení ze sítě VNet2** a **virtuální sítě Azure**.
- Převzetí služeb při selhání virtuálních počítačů v **zdroje ze sítě VNet2** k **obnovení ze sítě VNet2**.

![Prostředky v Azure app převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- V tomto příkladu je možné rozšířit zahrnují další aplikace a připojení k síti. Doporučuje se dodržovat model podobné jako připojení, pokud je to možné, při převzetí služeb při selhání ze zdroje do cíle.
- VPN Gateway pomocí veřejné IP adresy a směrování brány k navázání připojení. Pokud nechcete použít veřejné IP adresy, nebo chcete se vyhnout dalších segmentů směrování, můžete použít [Azure VNet peering](../virtual-network/virtual-network-peering-overview.md) na vytvoření partnerského vztahu virtuálních sítí mezi [podporovaných oblastech Azure](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Prostředky hybrid: úplné převzetí služeb při selhání

V tomto scénáři **společnosti B** spustí hybridní obchodní, s část infrastruktury aplikace běžící na Azure a zbytek spuštěné místně. 

### <a name="before-failover"></a>Před převzetí služeb při selhání

Zde je, jak Síťová architektura vypadá před převzetí služeb při selhání.

- Aplikace virtuální počítače jsou hostované v Azure východní Asie.
-  Východní Asie má virtuální síť (**zdrojová virtuální síť**) s adres 10.1.0.0/16 místa.
    - Východní Asie má úlohy rozdělit mezi třemi podsítěmi v **zdrojová virtuální síť**:
        - **Podsíť 1**: 10.1.1.0/24
        - **Podsíť 2**: 10.1.2.0/24,
        - **Podsíť 3**: 10.1.3.0/24utilizing virtuální sítě Azure s adres 10.1.0.0/16 místa. Tato virtuální síť jmenuje **zdrojová virtuální síť**
 - Oblast sekundární (cíl) je Asie – jihovýchod Azure:
    - Jihovýchodní Asie má virtuální síť (**virtuální sítě pro zotavení**) stejný jako **zdrojová virtuální síť**.
- Virtuální počítače ve východní Asie jsou připojené k místnímu datovému centru s využitím Azure ExpressRoute nebo web na serveru VPN.
- Pokud chcete snížit RTO, zřídí společnosti B brány na virtuální síť pro obnovení v Azure jihovýchodní Asie před převzetí služeb při selhání.
- Společnost B přiřadí/ověří cílové IP adresy pro replikované virtuální počítače. Cílová IP adresa je stejná jako zdrojová IP adresa pro každý virtuální počítač.


![Připojení na místní – Azure před převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po převzetí služeb při selhání


Pokud dojde k oblastnímu výpadku zdroj, může společnost B převzetí služeb při selhání všechny její prostředky na cílové oblasti.

- S cílové IP adresy už na místě před převzetí služeb při selhání, můžete orchestrovat převzetí služeb při selhání a automatickému vytvoření připojení po převzetí služeb při selhání mezi společnosti B **virtuální sítě pro zotavení** a **virtuální sítě Azure**.
- V závislosti na požadavcích aplikace, připojení mezi dvěma virtuálními sítěmi (**virtuální sítě pro zotavení** a **virtuální sítě Azure**) v cílové oblasti lze zavedených před, během (jako přechodný krok při) nebo po převzetí služeb. Společnost můžete použít [plány obnovení](site-recovery-create-recovery-plans.md) k určení, kdy se připojení naváže.
- Původní připojení mezi Azure východní Asie a místním datovým centrem by měl odpojen před navázáním připojení mezi Azure jihovýchodní Asii a v místním datovém centru.
- Místní směrování je překonfigurovat tak, aby odkazoval na cílové oblasti a bran příspěvku převzetí služeb při selhání.

![Připojení na místní – Azure po převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Prostředky hybrid: izolovaný režim převzetí služeb při selhání aplikace

Společnost B nemůže převzetí služeb při selhání izolované aplikace na úrovni podsítě. Je to proto adresní prostor na zdroji a obnovení virtuálních sítí je stejné, a původní zdroj pro místní připojení je aktivní.

 - Pro odolnost aplikace B společnosti potřebovat k umístění jednotlivých aplikací do vlastní vyhrazené virtuální sítě Azure.
 - S každou aplikaci v samostatné virtuální sítě můžou společnosti B převzetí služeb při selhání izolované aplikace a směrovat připojení ke zdroji na cílové oblasti.

## <a name="next-steps"></a>Další postup

Další informace o [plány obnovení](site-recovery-create-recovery-plans.md).
