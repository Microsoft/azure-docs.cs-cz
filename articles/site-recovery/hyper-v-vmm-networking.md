---
title: Nastavení IP adres pro připojení k sekundární místní lokality po převzetí služeb při selhání pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje postup nastavení přidělování IP adres pro připojení k virtuálním počítačům v sekundární místní lokality po zotavení po havárii a převzetí služeb při selhání pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rayne
ms.openlocfilehash: 50ef6ff452713ec8a8023c5d5e3d58a4b91ea323
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849798"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Nastavení IP adres pro připojení po převzetí služeb při selhání do sekundární místní lokality

Po převzetí služeb při selhání virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality, potřebujete mít možnost připojení k virtuální počítače replik. Tento článek pomůže vám to udělat. 

## <a name="connection-options"></a>Možnosti připojení

Po převzetí služeb při selhání existuje několik způsobů, jak zpracovat přidělování IP adres pro virtuální počítače repliky: 

- **Zachovat stejné IP adresy po převzetí služeb při selhání**: V tomto scénáři se replikovaný virtuální počítač má stejné IP adresy jako primárního virtuálního počítače. To zjednodušuje sítě související s problémy po převzetí služeb při selhání, ale vyžaduje úkony infrastruktury.
- **Použít jinou IP adresu po převzetí služeb při selhání**: V tomto scénáři virtuální počítač dostane novou IP adresu po převzetí služeb při selhání. 
 

## <a name="retain-the-ip-address"></a>Zachovat IP adresu

Pokud chcete zachovat IP adresy z primární lokality, po převzetí služeb při selhání do sekundární lokality, můžete:

- Nasazení roztažených podsítě mezi primární a sekundární lokality.
- Převzetí služeb úplné podsítě z primární do sekundární lokality. Je potřeba aktualizovat trasy a určete nové umístění IP adresy.


### <a name="deploy-a-stretched-subnet"></a>Nasazení roztažených podsítě

V konfiguraci s roztažené podsíť je k dispozici současně v primárních a sekundárních lokalit. V roztažených podsítě při přesunu na počítači a jeho konfigurace adresy IP (vrstva 3) do sekundární lokality v síti automaticky směruje provoz do nového umístění. 

- Z pohledu vrstvy 2 (data link layer) je nutné síťová zařízení, která může spravovat roztažené sítě VLAN.
- Roztáhnout sítě VLAN, potenciální doména selhání rozšiřuje na oba weby. To se stává jediným bodem selhání. Zatímco je nepravděpodobné, v takové situaci není možné k izolaci incidentem, jako je například vysílání storm. 


### <a name="fail-over-a-subnet"></a>Převzetí služeb při selhání podsíť

Můžete převzetí služeb při selhání celé podsítě pro získání výhod roztažené podsítě, aniž by ve skutečnosti roztažení. V tomto řešení podsíť je k dispozici ve zdrojové nebo cílové lokalitě, ale ne v obou současně.

- Pokud chcete zachovat v případě selhání adresní prostor IP adres, můžete prostřednictvím kódu programu uspořádat infrastruktury směrovače přesunout podsítě z jedné lokality do jiného.
- Když dojde k selhání, přesouvat podsítě s jejich přidružených virtuálních počítačích.
- Hlavní nevýhodou tohoto přístupu je, že v případě selhání, je nutné přesunout celé podsítě.

#### <a name="example"></a>Příklad:

Tady je příklad podsítě dokončení převzetí služeb při selhání. 

- Před převzetí služeb při selhání má primární lokalita aplikace běžící v podsíti 192.168.1.0/24.
- Během převzetí služeb při selhání všechny virtuální počítače v této podsíti jsou převzetí služeb při selhání do sekundární lokality a zachování jejich IP adresy. 
- Trasy mezi všemi lokalitami je potřeba upravit tak, aby odrážely skutečnost, že všechny virtuální počítače v podsíti 192.168.1.0/24 nyní přesunuty do sekundární lokality.

Následujících obrázcích je znázorněno podsítí před a po převzetí služeb při selhání.


**Před převzetí služeb při selhání**

![Před převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design2.png)

**Po převzetí služeb při selhání**

![Po převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design3.png)

Site Recovery po převzetí služeb při selhání, přidělí IP adresu pro každé síťové rozhraní ve virtuálním počítači. Adresa se přiděluje z fondu statických IP adres v příslušné síti jednotlivých instancí virtuálních počítačů.

- Pokud fond IP adres v sekundární lokalitě je stejné jako zdrojové lokality, Site Recovery přiděluje stejnou IP adresu (zdrojový virtuální počítač), replikovanému virtuálnímu počítači. IP adresa je vyhrazená v nástroji VMM, ale není nastaven jako IP adresu převzetí služeb při selhání na hostiteli Hyper-V. IP adresa převzetí služeb při selhání na hostiteli Hyper-v je nastavená těsně před převzetí služeb při selhání.
- Pokud není k dispozici stejné IP adresy, Site Recovery přiděluje další dostupnou IP adresu z fondu.
- Pokud virtuální počítače používají protokol DHCP, Site Recovery nebude spravovat IP adresy. Je potřeba zkontrolovat, že server DHCP v sekundární lokalitě můžete přidělit adresy ze stejného rozsahu jako zdrojové lokality.

### <a name="validate-the-ip-address"></a>Ověření IP adresu

Po povolení ochrany pro virtuální počítač, můžete použít následující ukázkový skript Ověřte adresu přiřazenou k virtuálnímu počítači. Tato IP adresa je nastaven jako IP adresu převzetí služeb při selhání a přiřazené k virtuálnímu počítači v okamžiku převzetí služeb při selhání:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Použít jinou IP adresu

V tomto scénáři se změní IP adresy virtuálních počítačů, které převzetí služeb při selhání. Nevýhodou toto řešení je údržba vyžaduje.  Záznamy DNS a mezipaměti může být nutné aktualizovat. To může způsobit výpadek, které se dají zmírnit následujícím způsobem:

- Pomocí nízké hodnoty TTL pro intranetové aplikace.
- Pomocí následujícího skriptu v plánu obnovení Site Recovery pro včasnou aktualizaci serveru DNS. Pokud používáte dynamickou registraci DNS nepotřebujete skriptu.

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

V tomto příkladu máme různých IP adresách v primárních a sekundárních lokalit, a je třetí lokalita, ze kterých aplikací hostovaných na primární server nebo obnovení lokality je přístupný.

- Před převzetí služeb při selhání jsou aplikace hostované podsítě 192.168.1.0/24 v primární lokalitě.
- Po převzetí služeb při selhání se aplikace konfigurují v podsíti 172.16.1.0/24 v sekundární lokalitě.
- Všechny tři servery mají přístup k sobě navzájem.
- Aplikace se po převzetí služeb při selhání, obnoví v podsíti pro obnovení.
- V tomto scénáři není nutné převzít služby při selhání celé podsítě a nevyžaduje žádné změny k překonfigurování VPN nebo síťové trasy. Převzetí služeb při selhání a nějaké aktualizace služby DNS, ujistěte se, že aplikace zůstanou přístupné.
- Pokud DNS je nakonfigurovaná k povolení dynamické aktualizace, pak virtuální počítače se zaregistrovat pomocí nové IP adresy, při spuštění po převzetí služeb při selhání.

**Před převzetí služeb při selhání**

![Různé IP adresy – před převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design10.png)

**Po převzetí služeb při selhání**

![Různé IP adresy – po převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Další postup

[Převzetí služeb při selhání](hyper-v-vmm-failover-failback.md)

