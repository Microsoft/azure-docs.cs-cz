---
title: Nastavení adresování IP po převzetí služeb při selhání na sekundární lokalitu pomocí Azure Site Recovery
description: Popisuje, jak nastavit IP adresování pro připojení k virtuálním počítačům v sekundárním místním webu po zotavení po havárii a převzetí služeb při selhání s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a61f7ff69e648262eb721eb61a98b09dbbee924c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961426"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Nastavení adresování IP pro připojení k sekundárnímu místnímu pracovišti po převzetí služeb při selhání

Po převzetí služeb při selhání virtuálních počítačích Hyper-V v cloudech správce virtuálních strojů (VMM) do sekundární lokality, musíte být schopni se připojit k virtuálním počítačům repliky. Tento článek vám pomůže to provést. 

## <a name="connection-options"></a>Možnosti připojení

Po převzetí služeb při selhání existuje několik způsobů, jak zpracovat adresování IP pro repliky virtuálních počítačů: 

- **Zachovat stejnou adresu IP po převzetí služeb při selhání**: V tomto scénáři replikovaný virtuální počítače má stejnou adresu IP jako primární virtuální počítače. To zjednodušuje problémy související se sítí po převzetí služeb při selhání, ale vyžaduje některé práce infrastruktury.
- **Po převzetí služeb při selhání použijte jinou ADRESU IP**: V tomto scénáři získá virtuální počítače po převzetí služeb při selhání novou ADRESU IP. 
 

## <a name="retain-the-ip-address"></a>Zachovat IP adresu

Pokud chcete zachovat IP adresy z primární lokality, po převzetí služeb při selhání do sekundární lokality můžete:

- Nasaďte roztaženou podsíť mezi primární a sekundární lokalitou.
- Proveďte převzetí služeb při selhání celé podsítě z primární do sekundární lokality. Je třeba aktualizovat trasy, které označují nové umístění adres IP.


### <a name="deploy-a-stretched-subnet"></a>Nasazení roztažené podsítě

V roztažené konfiguraci je podsíť k dispozici současně v primární i sekundární lokalitě. V roztažené podsíti při přesunutí konfigurace počítače a jeho adresy IP (vrstva 3) do sekundární lokality síť automaticky směruje provoz do nového umístění. 

- Z pohledu vrstvy 2 (vrstva datového propojení) potřebujete síťové vybavení, které dokáže spravovat roztaženou síť Ovou síť VLAN.
- Roztažením sítě VLAN se potenciální doména selhání rozšíří na obě sítě. To se stane jediným bodem selhání. I když je to nepravděpodobné, v takovém scénáři nemusí být možné izolovat incident, jako je například vysílání bouře. 


### <a name="fail-over-a-subnet"></a>Převzetí služeb při selhání podsítě

Můžete převzetí služeb při selhání přes celou podsíť získat výhody roztažené podsítě, aniž by ve skutečnosti roztažení. V tomto řešení je podsíť k dispozici ve zdrojové nebo cílové lokalitě, ale ne v obou současně.

- Chcete-li zachovat adresní prostor IP v případě převzetí služeb při selhání, můžete programově zajistit, aby infrastruktura směrovače přesouvala podsítě z jedné sítě do druhé.
- Když dojde k převzetí služeb při selhání, podsítě přesunout s jejich přidružené virtuální chodů.
- Hlavní nevýhodou tohoto přístupu je, že v případě selhání je třeba přesunout celou podsíť.

#### <a name="example"></a>Příklad

Tady je příklad převzetí služeb při selhání celé podsítě. 

- Před převzetím služeb při selhání má primární lokalita aplikace spuštěné v podsíti 192.168.1.0/24.
- Během převzetí služeb při selhání jsou všechny virtuální servery v této podsíti převzetí služeb při selhání do sekundární lokality a zachovat jejich IP adresy. 
- Trasy mezi všemi lokalitami je třeba upravit tak, aby odrážely skutečnost, že všechny virtuální servery v podsíti 192.168.1.0/24 se nyní přesunuly do sekundární lokality.

Následující grafiky ilustrují podsítě před a po převzetí služeb při selhání.


**Před převzetím služeb při selhání**

![Před převzetím služeb při selhání](./media/hyper-v-vmm-networking/network-design2.png)

**Po převzetí služeb při selhání**

![Po převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design3.png)

Po převzetí služeb při selhání site recovery přidělí IP adresu pro každé síťové rozhraní na virtuálním počítači. Adresa je přidělena z fondu statických IP adres v příslušné síti pro každou instanci virtuálního počítačů.

- Pokud je fond IP adres v sekundární lokalitě stejný jako ve zdrojové lokalitě, site recovery přidělí stejnému IP adresu (zdrojového virtuálního počítače) virtuálnímu počítači repliky. IP adresa je vyhrazena v VMM, ale není nastavena jako ip adresa s podporou převzetí služeb při selhání na hostiteli Hyper-V. Ip adresa při převzetí služeb při selhání na hostiteli technologie Hyper-v je nastavena těsně před převzetím služeb při selhání.
- Pokud stejná adresa IP není k dispozici, site recovery přidělí další dostupnou IP adresu z fondu.
- Pokud virtuální servery používají DHCP, obnovení webu nespravuje IP adresy. Je třeba zkontrolovat, zda server DHCP v sekundární lokalitě může přidělovat adresy ze stejnéoblasti jako zdrojová lokalita.

### <a name="validate-the-ip-address"></a>Ověřit IP adresu

Po povolení ochrany pro virtuální ho, můžete použít následující ukázkový skript k ověření adresy přiřazené k virtuálnímu jemu. Tato adresa IP je nastavena jako adresa IP s podporou převzetí služeb při selhání a přiřazena k virtuálnímu virtuálnímu soudu v době převzetí služeb při selhání:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Použití jiné IP adresy

V tomto scénáři se změní IP adresy virtuálních počítačů, které převzetí služeb při selhání. Nevýhodou tohoto řešení je nutná údržba.  Může být nutné aktualizovat položky DNS a mezipaměti. To může mít za následek prostoje, které mohou být zmírněny následujícím způsobem:

- Pro intranetové aplikace použijte nízké hodnoty TTL.
- Pro včasnou aktualizaci serveru DNS použijte následující skript v plánu obnovení webu. Pokud používáte dynamickou registraci DNS, skript nepotřebujete.

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
    
### <a name="example"></a>Příklad 

V tomto příkladu máme různé IP adresy v primárních a sekundárních lokalitách a existuje třetí lokalita, ze které lze přistupovat k aplikacím hostovaným v primární lokalitě nebo lokalitě pro obnovení.

- Před převzetím služeb při selhání jsou aplikace hostované podsítí 192.168.1.0/24 v primární lokalitě.
- Po převzetí služeb při selhání jsou aplikace konfigurovány v podsíti 172.16.1.0/24 v sekundární lokalitě.
- Všechny tři weby mají přístup k sobě navzájem.
- Po převzetí služeb při selhání budou aplikace obnoveny v podsíti pro obnovení.
- V tomto scénáři není nutné převzetí služeb při selhání přes celou podsíť a žádné změny jsou potřebné k překonfigurování VPN nebo síťových tras. Převzetí služeb při selhání a některé aktualizace DNS zajišťují, že aplikace zůstanou přístupné.
- Pokud je služba DNS nakonfigurována tak, aby umožňovala dynamické aktualizace, virtuální počítače se zaregistrují pomocí nové ip adresy při spuštění po převzetí služeb při selhání.

**Před převzetím služeb při selhání**

![Jiná IP adresa – před převzetím služeb při selhání](./media/hyper-v-vmm-networking/network-design10.png)

**Po převzetí služeb při selhání**

![Jiná IP adresa - po převzetí služeb při selhání](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Další kroky

[Spuštění převzetí služeb při selhání](hyper-v-vmm-failover-failback.md)

