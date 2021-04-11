---
title: Nastavení IP adres po převzetí služeb při selhání do sekundární lokality s Azure Site Recovery
description: Popisuje postup nastavení IP adres pro připojení k virtuálním počítačům v sekundární místní lokalitě po zotavení po havárii a převzetí služeb při selhání pomocí Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 1169748d7dae9990728dff91782a20d42e3c860b
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580251"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Nastavení IP adresování pro připojení k sekundárnímu místnímu webu po převzetí služeb při selhání

Po převzetí služeb při selhání virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality je nutné se připojit k virtuálním počítačům repliky. Tento článek vám pomůže to udělat. 

## <a name="connection-options"></a>Možnosti připojení

Po převzetí služeb při selhání existuje několik způsobů, jak zpracovávat IP adresy pro virtuální počítače repliky: 

- **Po převzetí služeb při selhání zachovat stejnou IP adresu**: v tomto scénáři má REPLIKOVANÝ virtuální počítač stejnou IP adresu jako primární virtuální počítač. Tato činnost zjednodušuje problémy související se sítí po převzetí služeb při selhání, ale vyžaduje určitou práci v infrastruktuře.
- **Po převzetí služeb při selhání použít jinou IP adresu**: v tomto scénáři po převzetí služeb při selhání Získá virtuální počítač novou IP adresu. 
 

## <a name="retain-the-ip-address"></a>Zachovat IP adresu

Pokud chcete zachovat IP adresy z primární lokality, po převzetí služeb při selhání do sekundární lokality můžete:

- Nasaďte roztaženou podsíť mezi primárním a sekundárním webovým serverem.
- Proveďte celou podsíť převzetí služeb při selhání z primární na sekundární lokalitu. Je potřeba aktualizovat trasy, aby označovaly nové umístění IP adres.


### <a name="deploy-a-stretched-subnet"></a>Nasazení roztažené podsítě

V roztažené konfiguraci je podsíť dostupná současně jak v primárních, tak i v sekundárních lokalitách. Když v roztažené podsíti přesunete počítač a jeho konfiguraci adresy IP (vrstvy 3) do sekundární lokality, síť automaticky směruje provoz do nového umístění. 

- Z perspektivy vrstvy 2 (Data Link Layer) potřebujete síťové vybavení, které může spravovat roztažené sítě VLAN.
- Roztažením sítě VLAN se potenciální doména selhání rozšíří do obou lokalit. To se stalo jediným bodem selhání. V takovém případě pravděpodobně nebudete schopni izolovat incident, jako je například všesměrové vysílání. 


### <a name="fail-over-a-subnet"></a>Převzetí služeb při selhání v podsíti

V celé podsíti můžete převzít služby při selhání, aby se získaly výhody roztažené podsítě, aniž byste je museli natáhnou. V tomto řešení je podsíť dostupná ve zdrojové nebo cílové lokalitě, ale ne současně.

- Chcete-li zachovat adresní prostor IP adres v případě převzetí služeb při selhání, můžete programově uspořádat, aby infrastruktura směrovače přesunula podsítě z jedné lokality do jiné.
- Když dojde k převzetí služeb při selhání, přesunou se podsítě s přidruženými virtuálními počítači.
- Hlavní nevýhodou tohoto přístupu je, že v případě selhání musíte přesunout celou podsíť.

#### <a name="example"></a>Příklad

Tady je příklad kompletního převzetí služeb při selhání podsítě. 

- Před převzetím služeb při selhání mají primární lokalita aplikace spuštěné v podsíti 192.168.1.0/24.
- Během převzetí služeb při selhání se u všech virtuálních počítačů v této podsíti převezmou sekundární lokalita a uchovávají se jejich IP adresy. 
- Trasy mezi všemi lokalitami je potřeba upravit tak, aby odrážely skutečnost, že všechny virtuální počítače v podsíti 192.168.1.0/24 se teď přesunuly do sekundární lokality.

Následující obrázek ilustruje podsítě před a po převzetí služeb při selhání.


**Před převzetím služeb při selhání**

![Diagram znázorňující podsítě před převzetím služeb při selhání.](./media/hyper-v-vmm-networking/network-design2.png)

**Po převzetí služeb při selhání**

![Diagram znázorňující podsítě po převzetí služeb při selhání.](./media/hyper-v-vmm-networking/network-design3.png)

Po převzetí služeb při selhání Site Recovery přidělí IP adresu pro každé síťové rozhraní virtuálního počítače. Adresa je přidělena z fondu statických IP adres v příslušné síti pro každou instanci virtuálního počítače.

- Pokud je fond IP adres v sekundární lokalitě stejný jako u zdrojové lokality, Site Recovery přidělí stejnou IP adresu (zdrojovému virtuálnímu počítači) k virtuálnímu počítači repliky. IP adresa je vyhrazená v nástroji VMM, ale není nastavená jako IP adresa pro převzetí služeb při selhání na hostiteli Hyper-V. IP adresa převzetí služeb při selhání na hostiteli Hyper-v je nastavená těsně před převzetím služeb při selhání.
- Pokud stejná IP adresa není k dispozici, Site Recovery přidělí další dostupnou IP adresu z fondu.
- Pokud virtuální počítače používají protokol DHCP, Site Recovery nespravuje IP adresy. Je potřeba ověřit, že server DHCP v sekundární lokalitě může přidělovat adresy ze stejného rozsahu, jako je zdrojová lokalita.

### <a name="validate-the-ip-address"></a>Ověřit IP adresu

Po povolení ochrany pro virtuální počítač můžete pomocí následujícího ukázkového skriptu ověřit adresu přiřazenou k virtuálnímu počítači. Tato IP adresa se nastaví jako IP adresa pro převzetí služeb při selhání a přiřadí se k virtuálnímu počítači v době převzetí služeb při selhání:

```powershell
$vm = Get-SCVirtualMachine -Name <VM_NAME>
$na = $vm[0].VirtualNetworkAdapters>
$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
$ip.address
```

## <a name="use-a-different-ip-address"></a>Použít jinou IP adresu

V tomto scénáři se změní IP adresy virtuálních počítačů, u kterých došlo k převzetí služeb při selhání. Nevýhodou tohoto řešení je potřeba údržba.  Je možné, že bude potřeba aktualizovat položky DNS a mezipaměti. To může vést k výpadkům, což může být omezeno následujícím způsobem:

- Pro intranetové aplikace používejte nedostatečné hodnoty TTL.
- Pro včasné aktualizace serveru DNS použijte následující skript v plánu obnovení Site Recovery. Pokud používáte dynamickou registraci DNS, nepotřebujete skript.

    ```powershell
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
    
### <a name="example"></a>Příklad 

V tomto příkladu máme v primárních a sekundárních lokalitách různé IP adresy a existuje třetí lokalita, ze které je možné přistupovat aplikace hostované v primární lokalitě nebo lokalitě pro obnovení.

- Před převzetím služeb při selhání jsou aplikace hostovány podsítí 192.168.1.0/24 v primární lokalitě.
- Po převzetí služeb při selhání se aplikace konfigurují v podsíti 172.16.1.0/24 v sekundární lokalitě.
- Všechny tři lokality mají přístup k sobě navzájem.
- Po převzetí služeb při selhání budou aplikace obnoveny v podsíti pro obnovení.
- V tomto scénáři není nutné převzít služby při selhání celé podsítě a nebude nutné provádět žádné změny pro překonfigurování směrování sítě VPN nebo sítě. Převzetí služeb při selhání a některé aktualizace DNS zajistí, že aplikace zůstanou přístupné.
- Pokud je služba DNS nakonfigurovaná tak, aby umožňovala dynamické aktualizace, virtuální počítače se při spuštění po převzetí služeb při selhání registrují sami pomocí nové IP adresy.

**Před převzetím služeb při selhání**

![Diagram znázorňující různé IP adresy před převzetím služeb při selhání.](./media/hyper-v-vmm-networking/network-design10.png)

**Po převzetí služeb při selhání**

![Diagram znázorňující různé IP adresy po převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Další kroky

[Spuštění převzetí služeb při selhání](hyper-v-vmm-failover-failback.md)

