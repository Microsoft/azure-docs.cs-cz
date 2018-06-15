---
title: Nastavení IP adresy pro připojení k sekundární místní lokalitu po převzetí služeb při selhání s Azure Site Recovery | Microsoft Docs
description: Popisuje postup nastavení IP adresy pro připojení k virtuální počítače v sekundární místní lokalitu po převzetí služeb při selhání Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rayne
ms.openlocfilehash: 531705bc704b3366c1c670ecf07c809ade67bc55
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29378887"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Nastavení IP adresy pro připojení k sekundární místní lokalitu po převzetí služeb při selhání

Po selhání virtuálních počítačů technologie Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality, potřebujete mít možnost připojení k virtuální počítače replik. Tento článek vám k tomu pomůže. 

## <a name="connection-options"></a>Možnosti připojení

Po převzetí služeb při selhání existuje několik způsobů, jak zpracovat adresování IP adres pro virtuální počítače repliky: 

- **Zachovat stejnou IP adresu po převzetí služeb při selhání**: V tomto scénáři replikovaný virtuální počítač má stejnou IP adresu jako primární virtuální počítač. Tato funkce zjednodušuje sítě související vydá po převzetí služeb při selhání, ale vyžaduje některé pracovní infrastruktury.
- **Po převzetí služeb při selhání použít jinou IP adresu**: V tomto scénáři virtuální počítač získá nové adresy IP po převzetí služeb při selhání. 
 

## <a name="retain-the-ip-address"></a>Zachovat IP adresu

Pokud chcete zachovat IP adresy z primární lokality, po převzetí služeb při selhání pro sekundární lokalitu, můžete:

- Nasaďte podsíť roztažené mezi primární a sekundární lokality.
- Proveďte selhání úplné podsíť z primární sekundární lokality. Je potřeba aktualizovat trasy k označení nové umístění na IP adresy.


### <a name="deploy-a-stretched-subnet"></a>Nasazení roztažené podsíť

V konfiguraci roztažené podsíť je k dispozici současně v primárních a sekundárních lokalit. V roztažené podsíť když přesunete počítače a konfigurace adresy IP (vrstvy 3) na sekundární lokalitě, sítě automaticky směruje provoz do nového umístění. 

- Z hlediska vrstvy 2 (Datová vrstva odkaz) budete potřebovat síťové zařízení, která můžete spravovat roztažené sítě VLAN.
- Podle roztažení sítě VLAN, potenciální domény selhání rozšiřuje k oběma sítím. To se stane jediným bodem selhání. Když je nepravděpodobné, v takovém scénáři není možné izolovat incident například všesměrového vysílání storm. 


### <a name="fail-over-a-subnet"></a>Selhání podsíť

Můžete převzít celou podsíť získat výhody roztažené podsíť bez ve skutečnosti roztažení ho. V tomto řešení je k dispozici podsíť v síti zdroje nebo cíle, ale ne v obou současně.

- Pokud chcete zachovat v případě selhání adresní prostor IP adres, můžete prostřednictvím kódu programu uspořádat pro infrastrukturu směrovače tak, aby přesunout do jiné podsítě z jedné lokality.
- Když dojde převzetí služeb při selhání, přesuňte podsítě s jejich přidružené virtuální počítače.
- Hlavní nevýhodou tohoto přístupu je, že v případě selhání, je nutné přesunout celou podsíť.

#### <a name="example"></a>Příklad:

Tady je příklad podsíť dokončení převzetí služeb při selhání. 

- Před převzetí služeb při selhání má primární lokalita aplikace běžící v podsíti 192.168.1.0/24.
- Během převzetí služeb při selhání všechny virtuální počítače v této podsíti se převzetí služeb při selhání pro sekundární lokalitu a zachovat jejich IP adresy. 
- Směrování mezi všemi lokalitami je potřeba upravit tak, aby odrážela skutečnost, že všechny virtuální počítače v podsíti 192.168.1.0/24 mají nyní přesunuta do sekundární lokality.

Následujících obrázcích je znázorněno podsítě před a po převzetí služeb při selhání.


**Před převzetí služeb při selhání**

![Před převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design2.png)

**Po převzetí služeb při selhání**

![Po převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design3.png)

Po převzetí služeb při selhání Site Recovery přidělí IP adresu pro každé síťové rozhraní na virtuálním počítači. Adresa je přidělen z fondu statických adres IP v příslušné síti, pro každou instanci virtuálního počítače.

- Pokud fond IP adres v sekundární lokalitě je stejný jako ve zdrojové lokalitě, Site Recovery přiděluje stejnou IP adresu (zdrojový virtuální počítač), v replice virtuálního počítače. IP adresa je vyhrazená v nástroji VMM, ale není nastaven jako IP adresu převzetí služeb při selhání na hostitele Hyper-V. Těsně před převzetí služeb při selhání je sada adres IP převzetí služeb při selhání na hostiteli technologie Hyper-v.
- Pokud není k dispozici stejnou IP adresu, Site Recovery přiděluje další dostupnou IP adresu z fondu.
- Pokud virtuální počítače používat službu DHCP, není Site Recovery spravovat IP adresy. Je třeba zkontrolovat, že server DHCP v sekundární lokalitě můžete přidělit adresy ze stejného rozsahu jako zdrojová lokalita.

### <a name="validate-the-ip-address"></a>Ověřte adresu IP

Po povolení ochrany pro virtuální počítač, můžete využít následující ukázkový skript k ověření adresy přiřazené k virtuálnímu počítači. Tato IP adresa je nastaven jako IP adresu převzetí služeb při selhání a přiřazené k virtuálnímu počítači v době převzetí služeb při selhání:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Použít jinou IP adresu

V tomto scénáři se změní IP adresy virtuálních počítačů, které převzetí služeb při selhání. Nevýhodou tohoto řešení je údržby vyžaduje.  Záznamy DNS a mezipaměti může být potřeba aktualizovat. To může vést k výpadkům, které lze zmírnit následujícím způsobem:

- Použijte nízké hodnoty TTL pro aplikace v síti intranet.
- Pomocí následujícího skriptu v plánu obnovení Site Recovery pro včasné aktualizace serveru DNS. Skript není nutný, pokud používáte dynamickou registraci DNS.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Příklad: 

V tomto příkladu máme různých IP adres napříč primárních a sekundárních lokalit a třetí lokalita, ze které aplikace hostované na primární server nebo obnovení webu můžete získat přístup.

- Před převzetí služeb při selhání aplikace jsou 192.168.1.0/24 hostované podsítí v primární lokalitě.
- Po převzetí služeb při selhání jsou aplikace nakonfigurované v podsíti 172.16.1.0/24 v sekundární lokalitě.
- Všechny tři servery mají přístup k sobě navzájem.
- Po převzetí služeb při selhání se obnoví v podsíti obnovení aplikace.
- V tomto scénáři je potřeba převzít celou podsíť, a je nutné provést žádné změny a změňte konfiguraci sítě VPN nebo síťové trasy. Převzetí služeb při selhání a některé aktualizace služby DNS, ujistěte se, aby aplikace zůstaly dostupné.
- Pokud chcete povolit dynamické aktualizace DNS nakonfigurovaný, bude registrovat virtuální počítače se při spuštění po převzetí služeb při selhání na novou IP adresu.

**Před převzetí služeb při selhání**

![Jinou IP adresu - před převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design10.png)

**Po převzetí služeb při selhání**

![Jinou IP adresu - po převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Další postup

[Spuštění převzetí služeb při selhání](hyper-v-vmm-failover-failback.md)

