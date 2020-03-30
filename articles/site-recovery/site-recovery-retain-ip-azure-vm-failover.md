---
title: Zachování IP adres po převzetí služeb při selhání virtuálního počítače Azure pomocí Azure Site Recovery
description: Popisuje, jak zachovat IP adresy při převzetí služby při selhání přes virtuální počítače Azure pro zotavení po havárii do sekundární oblasti s Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257560"
---
# <a name="retain-ip-addresses-during-failover"></a>Zachovat IP adresy během převzetí služeb při selhání

[Azure Site Recovery](site-recovery-overview.md) umožňuje zotavení po havárii pro virtuální počítače Azure replikací virtuálních počítačů do jiné oblasti Azure, převzetí mů e-li k výpadku a selhání zpět do primární oblasti, když se věci vrátí do normálu.

Během převzetí služeb při selhání můžete chtít zachovat adresování IP v cílové oblasti identické se zdrojovou oblastí:

- Ve výchozím nastavení při povolení zotavení po havárii pro virtuální počítače Azure, site recovery vytvoří cílové prostředky na základě nastavení zdrojových prostředků. Pro virtuální počítače Azure nakonfigurované se statickými IP adresami se Site Recovery pokusí zřídit stejnou IP adresu pro cílový virtuální počítač, pokud se nepoužívá. Úplné vysvětlení, jak obnovení webu zpracovává adresování, [naleznete v tomto článku](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Pro jednoduché aplikace je dostatečná výchozí konfigurace. Pro složitější aplikace může být nutné zřídit další prostředky, abyste se ujistili, že připojení funguje podle očekávání po převzetí služeb při selhání.


Tento článek obsahuje některé příklady pro zachování IP adres ve složitějších ukázkových scénářích. Příklady zahrnují:

- Převzetí služeb při selhání pro společnost se všemi prostředky spuštěnými v Azure
- Převzetí služeb při selhání pro společnost s hybridním nasazením a prostředky spuštěné místně i v Azure

## <a name="resources-in-azure-full-failover"></a>Prostředky v Azure: úplné převzetí služeb při selhání

Společnost A má všechny své aplikace spuštěné v Azure.

### <a name="before-failover"></a>Před převzetím služeb při selhání

Zde je architektura před převzetím služeb při selhání.

- Společnost A má identické sítě a podsítě ve zdrojových a cílových oblastech Azure.
- Chcete-li snížit cíl doby obnovení (RTO), společnost používá uzly replik pro SQL Server Always On, řadiče domény atd. Tyto uzly replik jsou v jiné virtuální síti v cílové oblasti, takže mohou vytvořit připojení sítě VPN mezi lokalitami mezi zdrojovou a cílovou oblastí. To není možné, pokud je ve zdroji a cíli použit stejný adresní prostor IP.  
- Před převzetím služeb při selhání je síťová architektura následující:
    - Primární oblast je Azure Východní Asie
        - Východní Asie má virtuální síť (**zdrojová virtuální síť)** s adresním prostorem 10.1.0.0/16.
        - Východní Asie má úlohy rozdělené mezi tři podsítě ve virtuální síti:
            - **Podsíť 1**: 10.1.1.0/24
            - **Podsíť 2**: 10.1.2.0/24
            - **Podsíť 3**: 10.1.3.0/24
    - Sekundární (cílová) oblast je Azure Jihovýchodní Asie
        - Jihovýchodní Asie má virtuální síť pro obnovení (**virtuální síť pro obnovení**) identická se **zdrojovou virtuální sítí**.
        - Jihovýchodní Asie má další virtuální síť **(Azure VNet)** s adresním prostorem 10.2.0.0/16.
        - **Síť Azure virtuální sítě** obsahuje podsíť (**podsíť 4**) s adresním prostorem 10.2.4.0/24.
        - Uzly replik pro SQL Server Always On, **Subnet 4**řadič domény atd.
    - **Zdrojová virtuální síť** a **virtuální síť Azure jsou** připojené k připojení vpn site-to-site.
    - **Virtuální síť pro obnovení** není připojena k žádné jiné virtuální síti.
    - **Společnost A** přiřazuje/ověřuje cílové IP adresy pro replikované položky. Cílová IP adresa je stejná jako zdrojová IP adresa pro každý virtuální virtuální měnový virtuální ms.

![Prostředky v Azure před úplným převzetím služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po převzetí služeb při selhání

Pokud dojde k výpadku místní zdroj, společnost A může přepojit všechny své prostředky do cílové oblasti.

- S cílové IP adresy již na místě před převzetí služeb při selhání, společnost A můžete organizovat převzetí služeb při selhání a automaticky navázat připojení po převzetí služeb při selhání mezi **virtuální síť pro obnovení** a virtuální sítě **Azure**. To je znázorněno v následujícím diagramu..
- V závislosti na požadavcích na aplikaci lze připojení mezi dvěma virtuálními sítěmi **(virtuální síť pro obnovení** a **virtuální sítí Azure)** v cílové oblasti navázat před, během (jako mezikrok) nebo po převzetí služeb při selhání.
  - Společnost může použít [plány obnovení](site-recovery-create-recovery-plans.md) k určení, kdy budou navázána připojení.
  - Můžou se připojit mezi virtuálními sítěmi pomocí partnerského vztahu virtuální sítě nebo sítě VPN.
      - Partnerský vztah virtuální sítě nepoužívá bránu VPN a má různá omezení.
      - [Ceny](https://azure.microsoft.com/pricing/details/virtual-network) partnerského vztahu virtuální sítě se počítají jinak než [ceny](https://azure.microsoft.com/pricing/details/vpn-gateway)brány VPN virtuální sítě. U převzetí služeb při selhání obecně doporučujeme použít stejnou metodu připojení jako zdrojové sítě, včetně typu připojení, aby se minimalizovaly nepředvídatelné síťové incidenty.

    ![Prostředky v Azure úplné převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Prostředky v Azure: převzetí služeb při selhání izolované aplikace

Možná budete muset převzetí služeb při selhání na úrovni aplikace. Například k převzetí služeb při selhání konkrétní aplikace nebo vrstvy aplikace umístěné ve vyhrazené podsíti.

- V tomto scénáři, i když můžete zachovat adresování IP, není obecně vhodné, protože zvyšuje pravděpodobnost nekonzistence připojení. Také ztratíte připojení podsítě k jiným podsítím v rámci stejné virtuální sítě Azure.
- Lepší způsob, jak provést převzetí služeb při selhání na úrovni podsítě, je použít různé cílové IP adresy pro převzetí služeb při selhání (pokud potřebujete připojení k jiným podsítím na zdrojové virtuální síti) nebo izolovat každou aplikaci ve své vlastní vyhrazené virtuální síti ve zdrojové oblasti. Pomocí druhého přístupu můžete vytvořit připojení mezi sítěmi ve zdrojové oblasti a emuloovat stejné chování při převzetí služeb při selhání do cílové oblasti.  

V tomto příkladu společnost A umístí aplikace do zdrojové oblasti ve vyhrazených virtuálních sítích a vytvoří připojení mezi těmito virtuálními sítěmi. Pomocí tohoto návrhu mohou provádět převzetí služeb při selhání izolované aplikace a zachovat zdrojové privátní IP adresy v cílové síti.

### <a name="before-failover"></a>Před převzetím služeb při selhání

Před převzetím služeb při selhání je architektura následující:

- Virtuální počítače aplikací jsou hostované v primární oblasti Azure – východní Asie:
    - **Aplikace1** Virtuální virtuální virtuální chod y jsou umístěné ve **virtuální síti Source VNet 1**: 10.1.0.0/16.
    - **Aplikace2** Virtuální virtuální virtuální chod y jsou umístěné ve **virtuální síti Source VNet 2**: 10.2.0.0/16.
    - **Zdrojová virtuální síť 1** má dvě podsítě.
    - **Zdrojová virtuální síť 2** má dvě podsítě.
- Sekundární (cílová) oblast je Azure jihovýchodní Asie – jihovýchodní Asie má virtuální sítě pro obnovení (**virtuální sítě pro obnovení 1** a virtuální sítě pro obnovení **2**), které jsou identické se **zdrojovou virtuální sítí 1** a **zdrojovou virtuální sítí 2**.
        - **Virtuální síť pro obnovení 1** a virtuální síť pro obnovení **2** mají dvě podsítě, které odpovídají podsítím ve **zdrojové virtuální síti 1** a **zdrojové virtuální síti 2** – jihovýchodní Asie má další virtuální síť **(síť Azure)** s adresním prostorem 10.3.0.0/16.
        - **Síť Azure virtuální sítě** obsahuje podsíť (**podsíť 4**) s adresním prostorem 10.3.4.0/24.
        - Replika uzly pro SQL Server Always **Subnet 4**On, řadič domény atd.
- Existuje několik připojení VPN mezi lokalitami: 
    - **Zdrojová virtuální síť 1** a **virtuální síť Azure**
    - **Zdrojová virtuální síť 2** a **virtuální síť Azure**
    - **Zdrojová virtuální síť 1** a **zdrojová virtuální síť 2** jsou propojeny s lokalitou VPN na webu
- **Virtuální síť pro obnovení 1** a virtuální síť pro obnovení **2** nejsou připojené k žádným jiným virtuálním sítím.
- **Společnost A** konfiguruje brány VPN na **virtuální síti pro obnovení 1** a virtuální síti pro obnovení **2**, aby se snížilo rto.  
- **Obnovení Virtuální sítě 1** a **obnovení Virtuální sítě 2** nejsou připojeny k žádné jiné virtuální síti.
- Chcete-li snížit cíl doby obnovení (RTO), brány VPN jsou nakonfigurované na **virtuální síti pro obnovení1** a **obnovení virtuální sítě 2** před převzetíslužeb služeb při selhání.

    ![Prostředky v Azure před převzetím služeb při selhání aplikace](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Po převzetí služeb při selhání

V případě výpadku nebo problému, který ovlivňuje jednu aplikaci (v **Source VNet 2 v našem příkladu), může společnost A obnovit postiženou aplikaci následujícím způsobem:


- Odpojte připojení VPN mezi **zdrojovou virtuální sítí 1** a **zdrojovou virtuální sítí 2**a mezi **zdrojovou virtuální sítí 2** a virtuální sítí **Azure** .
- Navázat připojení VPN mezi **zdrojovou virtuální sítí 1** a **obnovou virtuální sítě 2**a mezi **obnovovací sítí Virtuální sítě 2** a virtuální sítí **Azure**.
- Převzetí služeb při selhání virtuálních virtuálních sítí ve **zdrojové virtuální síti 2** na **virtuální síť pro obnovení 2**.

![Prostředky při převzetí služeb při selhání aplikace Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Tento příklad lze rozbalit tak, aby zahrnoval více aplikací a síťových připojení. Doporučujeme sledovat podobný model připojení, pokud je to možné, při převzetí selhání ze zdroje na cíl.
- Brány VPN používají k navazovat připojení veřejné IP adresy a směrování brány. Pokud nechcete používat veřejné IP adresy nebo se chcete vyhnout dalšímu směrování, můžete použít [partnerský vztah virtuální sítě Azure](../virtual-network/virtual-network-peering-overview.md) k partnerským virtuálním sítím v [podporovaných oblastech Azure](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Hybridní zdroje: úplné převzetí služeb při selhání

V tomto scénáři **společnost B** provozuje hybridní podnikání, s částí aplikační infrastruktury spuštěné v Azure a zbytek běží místně. 

### <a name="before-failover"></a>Před převzetím služeb při selhání

Zde je to, co síťová architektura vypadá před převzetím služeb při selhání.

- Virtuální počítače aplikací jsou hostované v Azure východní Asii.
- Východní Asie má virtuální síť (**zdrojová virtuální síť)** s adresním prostorem 10.1.0.0/16.
  - Východní Asie má úlohy rozdělené mezi tři podsítě ve **zdrojové virtuální síti**:
    - **Podsíť 1**: 10.1.1.0/24
    - **Podsíť 2**: 10.1.2.0/24
    - **Podsíť 3:** 10.1.3.0/24 s využitím virtuální sítě Azure s adresním prostorem 10.1.0.0/16. Tato virtuální síť se jmenuje **Zdrojová virtuální síť.**
      - Sekundární (cílová) oblast je Azure jihovýchodní Asie:
  - Jihovýchodní Asie má virtuální síť pro obnovení (**virtuální síť pro obnovení**) identická se **zdrojovou virtuální sítí**.
- Virtuální počítače ve východní Asii jsou připojené k místnímu datovému centru pomocí Azure ExpressRoute nebo VPN site-to-site.
- Chcete-li snížit RTO, společnost B zřídí brány na virtuální síti pro obnovení v Azure jihovýchodní Asii před převzetí služeb při selhání.
- Společnost B přiřazuje/ověřuje cílové IP adresy pro replikované virtuální ho. Cílová ADRESA IP je stejná jako zdrojová IP adresa pro každý virtuální virtuální ms.


![Připojení k místnímu počítači Azure před převzetím služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po převzetí služeb při selhání


Pokud dojde k výpadku místní zdroj, společnost B může přepojit všechny své prostředky do cílové oblasti.

- S cílové IP adresy již na místě před převzetí služeb při selhání, společnost B můžete organizovat převzetí služeb při selhání a automaticky navázat připojení po převzetí služeb při selhání mezi **virtuální síť pro obnovení** a virtuální sítě **Azure**.
- V závislosti na požadavcích na aplikaci lze připojení mezi dvěma virtuálními sítěmi **(virtuální síť pro obnovení** a **virtuální sítí Azure)** v cílové oblasti navázat před, během (jako mezikrok) nebo po převzetí služeb při selhání. Společnost může použít [plány obnovení](site-recovery-create-recovery-plans.md) k určení, kdy budou navázána připojení.
- Původní připojení mezi Azure Východní Asie a místní datové centrum by měla být odpojena před navázáním připojení mezi Azure jihovýchodní Asie a místní datové centrum.
- Místní směrování je překonfigurováno tak, aby ukazovalo na cílovou oblast a brány po převzetí služeb při selhání.

![Připojení k místnímu počítači Azure po převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hybridní prostředky: převzetí služeb při selhání izolované aplikace

Společnost B nemůže přepojit izolované aplikace na úrovni podsítě. Důvodem je, že adresní prostor na zdrojové a obnovovací virtuální sítě je stejný a původní zdroj místního připojení je aktivní.

 - Pro odolnost proti chybám aplikací společnost B bude muset umístit každou aplikaci ve své vlastní vyhrazené virtuální síti Azure.
 - S každou aplikací v samostatné virtuální síti může společnost B přepojit izolované aplikace a směrovat zdrojová připojení do cílové oblasti.

## <a name="next-steps"></a>Další kroky

Další informace o [plánech obnovení](site-recovery-create-recovery-plans.md).
