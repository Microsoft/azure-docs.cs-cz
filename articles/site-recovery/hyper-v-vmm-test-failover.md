---
title: Spuštění cvičení nhyper-v zotavení po havárii na sekundární lokalitě s Azure Site Recovery
description: Zjistěte, jak spustit cvičení zotavení po havárii pro virtuální počítače Hyper-V v cloudech v cloudech VMM do sekundárního místního datového centra pomocí Azure Site Recovery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0363911574a076b13cb72591fb2564364e096c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257963"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Spuštění cvičení zotavení po havárii pro virtuální servery Hyper-V do sekundární lokality


Tento článek popisuje, jak provést provrtání zotavení po havárii (DR) pro virtuální počítače Hyper-V, které jsou spravované v cloudech Správce virtuálních strojů V(MM) system center, do sekundárního místního webu pomocí [Azure Site Recovery](site-recovery-overview.md).

Spuštění míse test převzetí služeb při selhání k ověření strategie replikace a provést dr. vrtáku bez ztráty dat nebo výpadky. Převzetí služeb při selhání testu nemá žádný vliv na probíhající replikaci nebo na produkční prostředí. 

## <a name="how-do-test-failovers-work"></a>Jak test převzetí služeb při selhání práce?

Spuštění testu převzetí služeb při selhání z primární do sekundární lokality. Pokud jednoduše chcete zkontrolovat, že převzetí služeb při selhání virtuálního počítači, můžete spustit test převzetí služeb při selhání bez nastavení nic na sekundární lokalitě. Pokud chcete ověřit převzetí služeb při selhání aplikace funguje podle očekávání, budete muset nastavit sítě a infrastruktury v sekundárním umístění.
- Můžete spustit test převzetí služeb při selhání na jednom virtuálním počítači nebo na [plán obnovení](site-recovery-create-recovery-plans.md).
- Testovací převzetí služeb při selhání můžete spustit bez sítě, s existující sítí nebo s automaticky vytvořenou sítí. Další podrobnosti o těchto možnostech jsou uvedeny v následující tabulce.
    - Test převzetí služeb při selhání můžete spustit bez sítě. Tato možnost je užitečná, pokud chcete jednoduše zkontrolovat, že virtuální počítače bylo možné převzetí služeb při selhání, ale nebudete moct ověřit žádnou konfiguraci sítě.
    - Spusťte převzetí služeb při selhání s existující sítí. Doporučujeme nepoužívat produkční síť.
    - Spusťte převzetí služeb při selhání a nechte site recovery automaticky vytvořit testovací síť. V takovém případě site recovery vytvoří síť automaticky a vyčistit ji po dokončení testu převzetí služeb při selhání.
- Je třeba vybrat bod obnovení pro převzetí služeb při selhání testu: 
    - **Poslední zpracované**: Tato možnost selže virtuální ho virtuálního zařízení k nejnovějšímu bodu obnovení zpracovaného site recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
    - **Nejnovější konzistentní aplikace**: Tato možnost převzetí služeb při selhání virtuálního virtuálního zařízení na nejnovější bod obnovení konzistentní s aplikací zpracované site recovery. 
    - **Nejnovější**: Tato možnost nejprve zpracuje všechna data, která byla odeslána službě Site Recovery, a vytvoří bod obnovení pro každý virtuální virtuální server před převzetím služeb při selhání. Tato možnost poskytuje nejnižší RPO (Cíl bodu obnovení), protože virtuální ms vytvořený po převzetí služeb při selhání bude mít všechna data replikovaná do obnovení sítě při aktivaci převzetí služeb při selhání.
    - **Nejnovější zpracování více virtuálních**zařízení : K dispozici pro plány obnovení, které obsahují jeden nebo více virtuálních virtuálních zařízení, které mají povolenou konzistenci více virtuálních zařízení. Virtuální virtuální zařízení s povoleným nastavením převzetí služeb při selhání do nejnovějšího běžného bodu obnovení konzistentního s více virtuálními virtuálními zařízeními. Ostatní virtuální virtuální společnosti převzetí služeb při selhání na nejnovější zpracované bod obnovení.
    - **Nejnovější aplikace konzistentní s více virtuálními virtuálními**zařízeními : Tato možnost je dostupná pro plány obnovení s jedním nebo více virtuálními aplikacemi, které mají povolenou konzistenci více virtuálních zařízení. Virtuální aplikace, které jsou součástí replikační skupiny převzetí služeb při selhání na nejnovější společný bod obnovení konzistentní s více virtuálními aplikacemi. Ostatní virtuální virtuální společnosti převzetí služeb při selhání na jejich nejnovější bod obnovení konzistentní s aplikací.
    - **Vlastní**: Pomocí této možnosti můžete převést na služebnou služeb při selhání konkrétní hovirtuální ho dispozičního programu do konkrétního bodu obnovení.



## <a name="prepare-networking"></a>Příprava sítě

Při spuštění testu převzetí služeb při selhání, budete vyzváni k výběru nastavení sítě pro testovací repliky počítačů, jak je shrnuto v tabulce.

| **Možnost** | **Podrobnosti** | |
| --- | --- | --- |
| **Žádné** | Testovací virtuální počítač se vytvoří na hostiteli, na kterém je umístěn virtuální počítač repliky. Nepřidává se do cloudu a není připojen k žádné síti.<br/><br/> Po vytvoření můžete počítač připojit k síti virtuálních počítačí.| |
| **Použít existující** | Testovací virtuální počítač se vytvoří na hostiteli, na kterém je umístěn virtuální počítač repliky. Nepřidává se do cloudu.<br/><br/>Vytvořte síť virtuálních počítače, která je izolovaná od vaší produkční sítě.<br/><br/>Pokud používáte síť založenou na síti VLAN, doporučujeme vytvořit pro tento účel v programu VMM samostatnou logickou síť (nepoužívanou v produkčním prostředí). Tato logická síť se používá k vytvoření sítí virtuálních můře pro testovací převzetí služeb při selhání.<br/><br/>Logická síť by měla být přidružena alespoň k jednomu ze síťových adaptérů všech serverů Hyper-V, které hostují virtuální počítače.<br/><br/>U logických sítí v síti VLAN by měly být síťové lokality, které přidáte do logické sítě, izolovány.<br/><br/>Pokud používáte logickou síť založenou na virtualizaci sítě Windows, Azure Site Recovery automaticky vytvoří izolované sítě virtuálních počítače. | |
| **Vytvoření sítě** | Dočasná testovací síť je vytvořena automaticky na základě nastavení zadaného v **logické síti** a souvisejících síťových lokalitách.<br/><br/> Převzetí služeb při selhání kontroluje, že virtuální chody jsou vytvořeny.<br/><br/> Tuto možnost byste měli použít, pokud plán obnovení používá více než jednu síť virtuálních můek.<br/><br/> Pokud používáte sítě Virtualizace sítě Windows, tato možnost můžete automaticky vytvořit sítě virtuálních počítačů se stejným nastavením (podsítě a fondy IP adres) v síti virtuálního počítače repliky. Tyto sítě virtuálních společností se po dokončení převzetí služeb při selhání automaticky vyčistí.<br/><br/> Testovací virtuální počítač se vytvoří na hostiteli, na kterém existuje virtuální počítač repliky. Nepřidává se do cloudu.|

### <a name="best-practices"></a>Osvědčené postupy

- Testování produkční sítě způsobí prostoje produkčních úloh. Požádejte uživatele, aby při probíhávrtárce pro zotavení po havárii nepoužívali související aplikace.

- Testovací síť nemusí odpovídat typu logické sítě VMM použitému pro převzetí služeb při selhání testu. Ale některé kombinace nefungují:

     - Pokud replika používá izolaci založenou na DHCP a VLAN, síť virtuálních počítačů pro repliku nepotřebuje statický fond IP adres. Použití virtualizace sítě systému Windows pro převzetí služeb při selhání testu tedy nebude fungovat, protože nejsou k dispozici žádné fondy adres. 
        
     - Test převzetí služeb při selhání nebude fungovat, pokud síť repliky nevyužívá žádnou izolaci a testovací síť používá virtualizaci sítě systému Windows. Důvodem je, že síť bez izolace nemá podsítě potřebné k vytvoření sítě Windows Network Virtualization.
        
- Doporučujeme, abyste k převzetí služeb při selhání testu nepoužívali síť vybranou pro mapování sítě.

- Způsob připojení virtuálních počítačů replik k mapovaným sítím virtuálních počítačů po převzetí služeb při selhání závisí na konfiguraci sítě virtuálních počítačů v konzole VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Síť virtuálních počítače nakonfigurovaná bez izolace nebo izolace sítě VLAN

Pokud je síť virtuálních zařízení nakonfigurovaná ve Virtuálním počítače bez izolace nebo izolace sítě VLAN, poznamenejte si následující:

- Pokud je pro síť virtuálních počítačí definována služba DHCP, je virtuální počítač repliky připojen k ID sítě VLAN prostřednictvím nastavení, která jsou určena pro síťovou lokalitu v přidružené logické síti. Virtuální počítač obdrží svou IP adresu z dostupného serveru DHCP.
- Není nutné definovat fond statických IP adres pro cílovou síť virtuálních počítačů. Pokud se pro síť virtuálních počítačů používá statický fond adres IP, je virtuální počítač repliky připojen k ID sítě VLAN prostřednictvím nastavení určených pro síťovou lokalitu v přidružené logické síti.
- Virtuální počítač obdrží svou IP adresu z fondu, který je definovaný pro síť virtuálních počítačů. Pokud není v cílové síti virtuálních počítačů definován fond statických IP adres, přidělení IP adres se nezdaří. Vytvořte fond IP adres na zdrojových i cílových serverech VMM, které budete používat pro ochranu a obnovení.

### <a name="vm-network-with-windows-network-virtualization"></a>Síť virtuálních počítače s virtualizací sítě Windows

Pokud je síť VM nakonfigurovaná ve virtuálním počítači VMM s virtualizací sítě systému Windows, poznamenejte si následující:

- Měli byste definovat statický fond pro cílovou síť virtuálních počítačů, bez ohledu na to, zda je zdrojová síť virtuálních počítačů nakonfigurovaná pro použití DHCP nebo statického fondu adres IP. 
- Pokud definujete DHCP, cílový server VMM funguje jako server DHCP a poskytuje adresu IP z fondu, který je definován pro cílovou síť virtuálních počítačů.
- Pokud je pro zdrojový server definováno použití statického fondu adres IP, přidělí cílový server VMM z fondu adresu IP. V obou případech se přidělení ip adres nezdaří, pokud není definován statický fond adres IP.



## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury

Pokud jednoduše chcete zkontrolovat, že virtuální hod může převzetí služeb při selhání, můžete spustit test převzetí služeb při selhání bez infrastruktury. Pokud chcete provést úplný postup zotavení po havárii k testování převzetí služeb při selhání aplikace, musíte připravit infrastrukturu v sekundární lokalitě:

- Pokud spustíte zkušební převzetí služeb při selhání pomocí existující sítě, připravte v této síti služby Active Directory, DHCP a DNS.
- Pokud spustíte test převzetí služeb při selhání s možností vytvořit síť virtuálních montovny automaticky, je třeba přidat prostředky infrastruktury do automaticky vytvořené sítě, před spuštěním testu převzetí služeb při selhání. V plánu obnovení můžete usnadnit přidáním ruční krok před group-1 v plánu obnovení, který budete používat pro převzetí služeb při selhání testu. Potom přidejte prostředky infrastruktury do automaticky vytvořené sítě před spuštěním testu převzetí služeb při selhání.


### <a name="prepare-dhcp"></a>Příprava služby DHCP
Pokud virtuální počítače zapojené do převzetí služeb při selhání testu používají službu DHCP, vytvořte testovací server DHCP v izolované síti za účelem převzetí služeb při selhání testu.


### <a name="prepare-active-directory"></a>Příprava služby Active Directory
Chcete-li spustit zkušební převzetí služeb při selhání pro testování aplikací, potřebujete kopii produkčního prostředí služby Active Directory v testovacím prostředí. Další informace naleznete v [aspektech převzetí služeb při selhání testu pro službu Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Příprava SLUŽBY DNS
Připravte server DNS na převzetí služeb při selhání testu následujícím způsobem:

* **DHCP**: Pokud virtuální počítače používají službu DHCP, měla by být na testovacím serveru DHCP aktualizována adresa IP testovacího serveru DNS. Pokud používáte síťový typ virtualizace sítě systému Windows, server VMM funguje jako server DHCP. Ip adresa služby DNS by proto měla být aktualizována v testovací síti s podporou převzetí služeb při selhání. V takovém případě se virtuální počítače zaregistrují na příslušném serveru DNS.
* **Statická adresa**: Pokud virtuální počítače používají statickou adresu IP, měla by být ip adresa testovacího serveru DNS aktualizována v testovací síti s podporou převzetí služeb při selhání. Možná budete muset aktualizovat DNS s IP adresou testovacích virtuálních počítačů. Pro tento účel můžete použít následující ukázkový skript:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Tento postup popisuje, jak spustit test převzetí služeb při selhání pro plán obnovení. Případně můžete spustit převzetí služeb při selhání pro jeden virtuální počítač na kartě **Virtuální počítače.**

1. Vyberte **plány** > obnovení*recoveryplan_name*. Klepněte na možnost Převzetí **služeb při selhání** > **při převzetí služeb při selhání .**
2. V **okně Test failover** určete, jak by měly být virtuální počítače repliky připojeny k sítím po převzetí služeb při selhání testu.
3. Sledujte průběh převzetí služeb při selhání na kartě **Úlohy.**
4. Po dokončení převzetí služeb při selhání ověřte, že virtuální chody úspěšně spustit.
5. Po dokončení klikněte na **možnost Převzetí služeb při selhání testu vyčištění** v plánu obnovení. V **poznámkách**zaznamenejte a uložte všechna pozorování spojená s převzetím služeb při selhání testu. Tento krok odstraní všechny virtuální servery a sítě, které byly vytvořeny obnovení webu během převzetí služeb při selhání testu. 

![Testovací převzetí služeb při selhání](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> IP adresa poskytnutá virtuálnímu počítači během převzetí služeb při selhání testu je stejná IP adresa, kterou by virtuální počítač obdržel pro plánované nebo neplánované převzetí služeb při selhání (za předpokladu, že ip adresa je k dispozici v testovací síti s podporou převzetí služeb při selhání). Pokud stejná adresa IP není k dispozici v testovací síti s podporou převzetí služeb při selhání, virtuální počítač obdrží jinou IP adresu, která je k dispozici v testovací síti s podporou převzetí služeb při selhání.



### <a name="run-a-test-failover-to-a-production-network"></a>Spuštění zkušebního převzetí služeb při selhání do produkční sítě

Doporučujeme nespouštět zkušební převzetí služeb při selhání do sítě lokality pro obnovení výroby, kterou jste zadali během mapování sítě. Ale pokud potřebujete ověřit připojení k síti end-to-end ve virtuálním virtuálním připojení s připojením k selhání, poznamenejte si následující body:

* Ujistěte se, že primární virtuální hod se vypne, když provádíte zkušební převzetí služeb při selhání. Pokud tak neučiníte, dva virtuální počítače se stejnou identitou budou spuštěny ve stejné síti ve stejnou dobu. Tato situace může vést k nežádoucím důsledkům.
* Všechny změny, které provedete v testovacím připojení k selhání virtuálních počítačů jsou ztraceny při vyčištění virtuálních počítačů s podporou převzetí služeb při selhání testu. Tyto změny nejsou replikovány zpět do primárních virtuálních discích.
* Testování, jako je tento, vede k prostojům pro produkční aplikaci. Požádejte uživatele aplikace, aby aplikaci nepoužívali, když probíhá cvičení zotavení po havárii.  


## <a name="next-steps"></a>Další kroky
Po úspěšném spuštění cvičení zotavení po havárii můžete [spustit úplné převzetí služeb při selhání](site-recovery-failover.md).



