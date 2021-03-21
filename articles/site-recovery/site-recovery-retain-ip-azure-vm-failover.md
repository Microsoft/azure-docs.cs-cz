---
title: Udržování IP adres po převzetí služeb při selhání virtuálního počítače Azure pomocí Azure Site Recovery
description: Popisuje, jak zachovat IP adresy při selhání prostřednictvím virtuálních počítačů Azure pro zotavení po havárii do sekundární oblasti s Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023546"
---
# <a name="retain-ip-addresses-during-failover"></a>Zachování IP adres během převzetí služeb při selhání

[Azure Site Recovery](site-recovery-overview.md) umožňuje zotavení po havárii pro virtuální počítače Azure replikací virtuálních počítačů do jiné oblasti Azure a převzetí služeb při selhání, pokud dojde k výpadku a navrácení služeb po obnovení do primární oblasti, když se vrátí zpátky na normální.

Během převzetí služeb při selhání možná budete chtít zachovat IP adresy v cílové oblasti shodné se zdrojovou oblastí:

- Ve výchozím nastavení se při povolování zotavení po havárii pro virtuální počítače Azure Site Recovery vytvoří cílové prostředky na základě nastavení zdrojového prostředku. U virtuálních počítačů Azure nakonfigurovaných pomocí statických IP adres se Site Recovery pokusí zřídit stejnou IP adresu pro cílový virtuální počítač, pokud se nepoužívá. Úplné vysvětlení, jak Site Recovery zpracovává adresování, najdete v [tomto článku](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Pro jednoduché aplikace je výchozí konfigurace dostačující. U složitějších aplikací možná budete muset zřídit další prostředek, abyste se ujistili, že připojení funguje po převzetí služeb při selhání podle očekávání.


Tento článek obsahuje několik příkladů pro zachování IP adres ve složitějších ukázkových scénářích. Mezi příklady patří:

- Převzetí služeb při selhání pro společnost se všemi prostředky běžícími v Azure
- Převzetí služeb při selhání pro společnost s hybridním nasazením a prostředky, na kterých běží místní i v Azure

## <a name="resources-in-azure-full-failover"></a>Prostředky v Azure: úplné převzetí služeb při selhání

Společnost A má všechny aplikace běžící v Azure.

### <a name="before-failover"></a>Před převzetím služeb při selhání

Tady je architektura před převzetím služeb při selhání.

- Společnost A má stejné sítě a podsítě ve zdrojové a cílové oblasti Azure.
- Aby se snížila plánovaná doba obnovení (RTO), společnost používá uzly repliky pro SQL Server Always On, řadiče domény atd. Tyto uzly repliky se nacházejí v jiné virtuální síti v cílové oblasti, aby mohly vytvořit připojení VPN typu Site-to-site mezi zdrojovou a cílovou oblastí. To není možné, pokud se ve zdroji a cíli používá stejný adresní prostor IP adres.  
- Před převzetím služeb při selhání je Síťová architektura následující:
    - Primární oblast je Azure Východní Asie
        - Východní Asie má síť VNet (**zdrojová virtuální síť**) s adresním prostorem 10.1.0.0/16.
        - Východní Asie obsahuje úlohy rozdělené mezi tři podsítě ve virtuální síti:
            - **Podsíť 1**: 10.1.1.0/24
            - **Podsíť 2**: 10.1.2.0/24
            - **Podsíť 3**: 10.1.3.0/24
    - Sekundární (cílová) oblast je Azure jihovýchodní Asie.
        - Jihovýchodní Asie mají síť VNet pro obnovení (**virtuální síť pro obnovení**) shodná se **zdrojovou virtuální** sítí.
        - Jihovýchodní Asie má další virtuální síť (**virtuální síť Azure**) s adresním prostorem 10.2.0.0/16.
        - **Virtuální síť Azure** obsahuje podsíť (**podsíť 4**) s adresním prostorem 10.2.4.0/24.
        - Uzly repliky pro SQL Server Always On, řadič domény atd. se nacházejí v **podsíti 4**.
    - **Zdrojová virtuální síť** a **virtuální síť Azure** jsou propojené s připojením VPN typu Site-to-site.
    - **Virtuální síť pro obnovení** není připojená k žádné jiné virtuální síti.
    - **Společnost A** přiřadí/OVĚŘÍ cílové IP adresy pro replikované položky. Cílová IP adresa je stejná jako zdrojová IP adresa pro každý virtuální počítač.

![Prostředky v Azure před úplným převzetím služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po převzetí služeb při selhání

Pokud dojde k výpadku zdrojového regionu, společnost A může převzít služby při selhání všech svých prostředků do cílové oblasti.

- S cílovými IP adresami, které už jsou v platnosti před převzetím služeb při selhání, může společnost A orchestrovat převzetí služeb při **selhání a automaticky** navazovat připojení po převzetí služeb při selhání To je znázorněno v následujícím diagramu...
- V závislosti na požadavcích aplikace je možné v cílové oblasti navázat připojení mezi dvěma virtuální sítě (**virtuální síť pro obnovení** a **virtuální síť Azure**) v rámci (jako zprostředkující krok) nebo po převzetí služeb při selhání.
  - Společnost může pomocí [plánů obnovení](site-recovery-create-recovery-plans.md) určit, kdy se budou navázat připojení.
  - Můžou se připojit mezi virtuální sítě pomocí partnerského vztahu virtuálních sítí nebo sítě VPN typu Site-to-site.
      - VNet peering nepoužívá bránu VPN a má odlišná omezení.
      - [Ceny](https://azure.microsoft.com/pricing/details/virtual-network) VNet peering se vypočítávají jinak než [ceny](https://azure.microsoft.com/pricing/details/vpn-gateway)VNet-to-VNet VPN Gateway. Pro převzetí služeb při selhání obecně doporučujeme použít stejnou metodu připojení jako zdrojové sítě, včetně typu připojení, pro minimalizaci nepředvídatelných incidentů sítě.

    ![Prostředky v Azure – úplné převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Prostředky v Azure: převzetí služeb při selhání izolované aplikace

Možná budete muset převzít služby při selhání na úrovni aplikace. Například pro převzetí služeb při selhání konkrétní aplikace nebo vrstvy aplikace nacházející se ve vyhrazené podsíti.

- V tomto scénáři, i když můžete uchovat IP adresování, není obecně vhodné, protože zvyšuje pravděpodobnost nekonzistence připojení. Ztratíte taky připojení podsítě k ostatním podsítím v rámci stejné virtuální sítě Azure.
- Lepším způsobem, jak převzetí služeb při selhání aplikace na úrovni podsítě, je použít pro převzetí služeb při selhání jiné cílové IP adresy (Pokud potřebujete připojení k ostatním podsítím ve zdrojové virtuální síti) nebo izolovat každou aplikaci ve své vlastní vyhrazené virtuální síti ve zdrojové oblasti. Díky druhému přístupu můžete navázat připojení mezi sítěmi ve zdrojové oblasti a při převzetí služeb při selhání do cílové oblasti emulovat stejné chování.  

V tomto příkladu společnost A umístí aplikace do zdrojové oblasti v vyhrazených virtuální sítě a naváže spojení mezi těmito virtuální sítě. V tomto návrhu můžou provádět převzetí služeb při selhání izolovaného aplikace a uchovávat zdrojové privátní IP adresy v cílové síti.

### <a name="before-failover"></a>Před převzetím služeb při selhání

Před převzetím služeb při selhání je architektura následující:

- Virtuální počítače aplikace jsou hostované v primární oblasti Azure Východní Asie:
    - **App1** Virtuální počítače jsou umístěné ve **virtuální** síti VNet 1:10.1.0.0/16.
    - **App2** Virtuální počítače se nacházejí ve **zdrojové virtuální** síti ve virtuální síti 2:10.2.0.0/16.
    - **Zdrojová virtuální síť 1** obsahuje dvě podsítě.
    - **Zdrojová virtuální síť 2** má dvě podsítě.
- Sekundární (cílová) oblast je Azure jihovýchodní Asie – jihovýchodní Asie má virtuální sítě obnovení (**virtuální síť pro obnovení 1** a **virtuální síť pro obnovení**), která je shodná se **zdrojovou virtuální sítí 1** a **zdrojovou virtuální sítí 2**.
        - **Virtuální síť pro obnovení** a **virtuální síť pro obnovení 2** mají dvě podsítě, které odpovídají podsítím ve **zdrojové virtuální síti 1** a **zdrojové virtuální** síti. 2 – jihovýchodní Asie mají další virtuální síť (**virtuální síť Azure**) s adresním prostorem 10.3.0.0/16.
        - **Virtuální síť Azure** obsahuje podsíť (**podsíť 4**) s adresním prostorem 10.3.4.0/24.
        – Uzly repliky pro SQL Server Always On, řadič domény atd. se nacházejí v **podsíti 4**.
- Existuje několik připojení typu Site-to-Site VPN: 
    - **Zdrojová virtuální síť 1** a **virtuální síť Azure**
    - **Zdrojová virtuální síť 2** a **virtuální síť Azure**
    - **Zdrojová virtuální síť 1** a **zdrojová virtuální síť 2** jsou propojené s VPN site-to-site.
- **Virtuální síť pro obnovení 1** a **virtuální síť pro obnovení 2** nejsou připojené k žádným jiným virtuální sítě.
- **Společnost A** nakonfiguruje brány VPN pro **obnovení virtuální sítě 1** a **virtuální sítě pro obnovení**, aby se snížilo RTO.  
- **VNet1 obnovení** a **obnovení VNet2** nejsou připojené k žádné jiné virtuální síti.
- Aby se snížila plánovaná doba obnovení (RTO), jsou brány VPN nakonfigurované na **VNet1 pro obnovení** a **obnovení VNet2** před převzetím služeb při selhání.

    ![Prostředky v Azure před převzetím služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Po převzetí služeb při selhání

V případě výpadku nebo problému, který má vliv na jednu aplikaci (v našem příkladu * * zdrojová virtuální síť 2), může společnost A obnovit ovlivněnou aplikaci následujícím způsobem:


- Odpojte připojení VPN mezi **zdrojovým VNet1** a **zdrojovým VNet2** a mezi **zdrojovou VNet2** a **virtuální sítí Azure** .
- Navažte připojení VPN mezi **zdrojovým VNet1** a **obnovením VNet2** a mezi **VNet2 obnovení** a **virtuální sítí Azure**.
- Převzetí služeb při selhání u virtuálních počítačů ve **zdrojovém VNet2** do **VNet2 pro obnovení**.

![Prostředky ve službě Azure App Failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Tento příklad se dá rozšířit tak, aby zahrnoval víc aplikací a síťových připojení. Doporučuje se postupovat podle modelu připojení podobného typu, pokud je to možné, když dojde k převzetí služeb při selhání ze zdroje do cíle.
- Brány VPN používají k navázání připojení veřejné IP adresy a směrování bran. Pokud nechcete používat veřejné IP adresy nebo chcete zabránit nadbytečnému směrování, můžete použít [partnerský vztah](../virtual-network/virtual-network-peering-overview.md) virtuálních sítí Azure k partnerským virtuálním sítím v [podporovaných oblastech Azure](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Hybridní prostředky: úplné převzetí služeb při selhání

V tomto scénáři **Společnost B** spouští hybridní firmu s částí aplikační infrastruktury běžící na Azure a zbývajícím místně spuštěným místním prostředím. 

### <a name="before-failover"></a>Před převzetím služeb při selhání

V takovém případě bude architektura sítě vypadat ještě před převzetím služeb při selhání.

- Virtuální počítače aplikace jsou hostované v Azure Východní Asie.
- Východní Asie má síť VNet (**zdrojová virtuální síť**) s adresním prostorem 10.1.0.0/16.
  - Východní Asie obsahuje úlohy rozdělené mezi tři podsítě ve **zdrojové virtuální** síti:
    - **Podsíť 1**: 10.1.1.0/24
    - **Podsíť 2**: 10.1.2.0/24
    - **Podsíť 3**: 10.1.3.0/24, která využívá Azure Virtual Network s adresním prostorem 10.1.0.0/16. Tato virtuální síť se nazývá **zdrojová virtuální** síť.
      - Sekundární (cílová) oblast je Azure jihovýchodní Asie:
  - Jihovýchodní Asie mají síť VNet pro obnovení (**virtuální síť pro obnovení**) shodná se **zdrojovou virtuální** sítí.
- Virtuální počítače v Východní Asie jsou připojené k místnímu datovému centru pomocí Azure ExpressRoute nebo VPN typu Site-to-site.
- Aby se snížilo RTO, společnost B zřídí brány pro virtuální síť pro obnovení ve službě Azure jihovýchodní Asie před převzetím služeb při selhání.
- Společnost B přiřadí/ověří cílové IP adresy replikovaných virtuálních počítačů. Cílová IP adresa je stejná jako zdrojová IP adresa pro každý virtuální počítač.


![Připojení mezi místními počítači před převzetím služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po převzetí služeb při selhání


Pokud dojde k výpadku zdrojového regionu, společnost B může převzít služby při selhání všech svých prostředků do cílové oblasti.

- S cílovými IP adresami, které už jsou v platnosti před převzetím služeb při selhání, může společnost B orchestrovat převzetí služeb při **selhání a automaticky** navazovat připojení po převzetí služeb při selhání
- V závislosti na požadavcích aplikace je možné v cílové oblasti navázat připojení mezi dvěma virtuální sítě (**virtuální síť pro obnovení** a **virtuální síť Azure**) v rámci (jako zprostředkující krok) nebo po převzetí služeb při selhání. Společnost může pomocí [plánů obnovení](site-recovery-create-recovery-plans.md) určit, kdy se budou navázat připojení.
- Původní připojení mezi službou Azure Východní Asie a místním datacentrem by mělo být před navázáním spojení mezi Azure jihovýchodní Asie a místním datacentrem odpojeno.
- Místní směrování se překonfiguruje tak, aby odkazovalo na cílovou oblast a brány po převzetí služeb při selhání.

![Připojení k místnímu prostředí Azure po převzetí služeb při selhání](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hybridní prostředky: převzetí služeb při selhání izolované aplikace

Společnost B nemůže převzít služby izolovaných aplikací na úrovni podsítě. Důvodem je to, že adresní prostor na straně zdroje a obnovení virtuální sítě je stejný a původní zdroj pro místní připojení je aktivní.

 - V případě odolnosti aplikace B bude nutné každou aplikaci umístit do vlastní vyhrazené virtuální sítě Azure.
 - U každé aplikace v samostatné virtuální síti může společnost B převzít služby při selhání izolovaných aplikací a zdrojová připojení směrování do cílové oblasti.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [plánech obnovení](site-recovery-create-recovery-plans.md).
