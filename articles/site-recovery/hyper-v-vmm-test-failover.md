---
title: Spustit procházení zotavení po Havárii virtuálních počítačů technologie Hyper-V do sekundární lokality pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak spustit procházení DR pro virtuální počítače Hyper-V v cloudech VMM do sekundárního datacentra, pomocí Azure Site Recovery.
services: site-recovery
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: ponatara
ms.openlocfilehash: c389776f62db5fd04f67ef22822e21fd4aee368f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Spustit procházení DR pro virtuální počítače Hyper-V do sekundární lokality


Tento článek popisuje, jak provést přechod zotavení po havárii pro virtuální počítače Hyper-V spravované v cloudech System Center Virtual Machine Manager V(MM) na sekundární místní lokalitu pomocí [Azure Site Recovery](site-recovery-overview.md).

Spustit převzetí služeb při selhání ověření strategie replikace a provést zotavení po Havárii procházení bez ztráty dat nebo výpadek. Testovací převzetí služeb nemá žádný vliv na probíhající replikace, nebo na produkční prostředí. 

## <a name="how-do-test-failovers-work"></a>Jak otestovat pracovní převzetí služeb při selhání?

Můžete spustit testovací převzetí služeb z primárního na sekundární lokalitě. Pokud chcete jednoduše zkontrolujte, že virtuální počítač při selhání, můžete spustit testovací převzetí služeb bez nic nastavení v sekundární lokalitě. Pokud chcete ověřit aplikace převzetí služeb při selhání funguje podle očekávání, musíte nastavit sítě a infrastruktury v sekundárním umístění.
- Na jeden virtuální počítač nebo na můžete spustit testovací převzetí služeb [plán obnovení](site-recovery-create-recovery-plans.md).
- Převzetí služeb při selhání bez sítě, můžete spustit existující síti nebo s automaticky vytvořené síťové. Další informace o těchto možnostech najdete v následující tabulce.
    - Můžete spustit převzetí služeb při selhání bez připojení k síti. Tato možnost je užitečná, pokud chcete jednoduše zkontrolujte, že virtuální počítač byl schopen převzetí služeb při selhání, ale nebudete moci ověřit všechny konfigurace sítě.
    - Spusťte převzetí služeb při selhání s existující síť. Doporučujeme, abyste že nepoužíváte produkční sítě.
    - Spusťte převzetí služeb při selhání a nechat automaticky vytvořit v testovací síti Site Recovery. Site Recovery v takovém případě bude automaticky vytvořit síť a vyčistit po dokončení převzetí služeb při selhání.
- Je nutné vybrat bod obnovení pro testovací převzetí služeb: 
    - **Nejnovější zpracované**: Tato možnost selhání virtuálního počítače na nejnovější bod obnovení, které jsou zpracovávány Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
    - **Nejnovější aplikace konzistentní**: tuto možnost převzetí služeb při selhání virtuálního počítače do bodu nejnovější obnovení konzistentních s aplikací, které jsou zpracovávány Site Recovery. 
    - **Nejnovější**: tuto možnost napřed zpracuje všechna data, která byl odeslán do služby Site Recovery k vytvoření bodu obnovení pro každý virtuální počítač před převzetí služeb při selhání do ní. Tato možnost poskytuje nejnižší plánovaný bod obnovení (plánovaného bodu obnovení), protože virtuální počítač vytvořené po převzetí služeb při selhání budou mít všechna data replikované Site Recovery, kdy bylo spuštěno převzetí služeb při selhání.
    - **Nejnovější více virtuálních počítačů zpracovat**: k dispozici pro plány obnovení, které obsahují jednu nebo více virtuálních počítačů, které mají povolenou konzistencí pro víc virtuálních počítačů. Virtuální počítače s povoleným nastavením převzetí služeb při selhání nejnovější společný bod obnovení konzistentních s více virtuálních počítačů. Ostatní virtuální počítače převzetí služeb při selhání do nejnovějšího bodu obnovení zpracování.
    - **Nejnovější více virtuálních počítačů konzistentní**: Tato možnost je dostupná pro plánů obnovení pomocí jednoho nebo více virtuálních počítačů, které mají povolenou konzistencí pro víc virtuálních počítačů. Virtuální počítače, které jsou součástí replikační skupiny převzetí služeb při selhání nejnovější společný bod obnovení konzistentních s aplikací více virtuálních počítačů. Ostatní virtuální počítače převzetí služeb při selhání na jejich nejnovější bod obnovení konzistentních s aplikací.
    - **Vlastní**: tuto možnost použijte k převzetí služeb konkrétní virtuální počítač do bodu konkrétní obnovení.



## <a name="prepare-networking"></a>Příprava sítě

Při spuštění testu převzetí služeb budete vyzváni k výběru nastavení sítě pro testovací počítače repliky, jak je shrnuto v tabulce.

**Možnost** | **Podrobnosti** 
--- | --- 
**None** | Testovací virtuální počítač se vytvoří na hostitele, na kterém je umístěn repliku virtuálního počítače. Nebyla přidána do cloudu a není připojen k žádné síti.<br/><br/> Na počítači můžete připojit k síti virtuálních počítačů po jeho vytvoření.
**Použít existující** | Testovací virtuální počítač se vytvoří na hostitele, na kterém je umístěn repliku virtuálního počítače. Není přidáno do cloudu.<br/><br/>Vytvořte síť virtuálních počítačů, která bude izolovaná od produkční sítě.<br/><br/>Pokud používáte síť založená na síti VLAN, doporučujeme vytvořit samostatnou logickou síť (nepoužívá se v produkčním prostředí) v nástroji VMM pro tento účel. Tato logická síť se používá k vytvoření sítě virtuálních počítačů pro testovací převzetí služeb při selhání.<br/><br/>Logická síť má být přidružen alespoň jeden ze síťových adaptérů ve všech serverech technologie Hyper-V, které jsou hostiteli virtuálních počítačů.<br/><br/>Pro logické sítě VLAN musí být izolované síťové lokality, které přidáte k logické síti.<br/><br/>Pokud používáte logické sítě na základě virtualizace sítě systému Windows, Azure Site Recovery automaticky vytvoří izolované sítě virtuálních počítačů. 
**Vytvoření sítě** | Dočasné testovací síti se vytvoří automaticky v závislosti na nastavení, které určíte v **logické sítě** a její související síťové lokality.<br/><br/> Převzetí služeb při selhání ověří, že byly vytvořeny virtuální počítače. |Tuto možnost používejte, pokud plán obnovení používá více než jedna síť virtuálních počítačů.<br/><br/> Pokud používáte Windows virtualizace sítě, tato možnost umožňuje automatické vytvoření sítě virtuálních počítačů se stejným nastavením (podsítí a fondy IP adres) v síti virtuálního počítače repliky. Tyto sítě virtuálních počítačů se automaticky vyčistí po dokončení testu převzetí služeb.<br/><br/> Testovací virtuální počítač je vytvořen na hostiteli, na kterém se nachází virtuální počítač repliky. Není přidáno do cloudu.

### <a name="best-practices"></a>Osvědčené postupy

- Testování produkční síť způsobí, že výpadky v produkčním prostředí. Požádejte uživatele, není pro použití související aplikace v průběhu postupu zotavení po havárii.

- Testovací síti nemusí shodovat s typem logická síť VMM používá pro testovací převzetí služeb při selhání. Ale nefungují některé kombinace:

     - Pokud replika používá DHCP a sítí VLAN izolace, síť virtuálních počítačů pro repliku nepotřebuje fond statických adres IP. Proto pro testovací převzetí služeb pomocí virtualizace sítě systému Windows nebude fungovat, protože nejsou k dispozici žádné fondy adres. 
        
     - Testovací převzetí služeb při selhání nebude fungovat, pokud síť repliky pomocí bez izolace a testovací síti pomocí virtualizace sítě systému Windows. To je proto síti bez izolace nemá podsítě potřebné pro vytvoření síť virtualizace sítě systému Windows.
        
- Doporučujeme vám, že nepoužíváte síť, kterou jste vybrali pro mapování sítě pro testovací převzetí služeb při selhání.

- Jak jsou repliky virtuální počítače připojené k namapované sítě virtuálních počítačů po převzetí služeb při selhání závisí na konfiguraci sítě virtuálních počítačů v konzole nástroje VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Síť virtuálních počítačů konfigurovanou bez izolace nebo izolace sítě VLAN

Pokud v nástroji VMM nakonfigurovaný síť virtuálních počítačů bez izolace, nebo izolace sítě VLAN, pamatujte na následující:

- Pokud DHCP je definována pro síť virtuálních počítačů, je připojený virtuální počítač repliky na ID sítě VLAN prostřednictvím nastavení, které jsou určené pro síťové lokality v přidruženou logickou síť. Virtuální počítač přijímá jeho IP adresu ze serveru DHCP k dispozici.
- Nemusíte definovat fond statických adres IP pro síť virtuálních počítačů cíl. Pokud se používá fond statických adres IP pro síť virtuálních počítačů, je připojený virtuální počítač repliky na ID sítě VLAN prostřednictvím nastavení, které jsou určené pro síťové lokality v přidruženou logickou síť.
- Virtuální počítač přijímá jeho IP adresu z fondu, která je definována pro síť virtuálních počítačů. Pokud fond statických adres IP není definován v cílové síti virtuálních počítačů, se nezdaří přidělení IP adresy. Vytvořte fond IP adres na serverech VMM zdroj i cíl, které budete používat pro ochranu a obnovení.

### <a name="vm-network-with-windows-network-virtualization"></a>Síť virtuálních počítačů s virtualizací sítě systému Windows

Pokud je síť virtuálních počítačů nakonfigurované v nástroji VMM se virtualizace sítě systému Windows, pamatujte na následující:

- Měli byste fond statických pro virtuální počítač má cílová síť bez ohledu na to, jestli je zdrojové síti virtuálních počítačů nakonfigurované na používání protokolu DHCP nebo fond statických adres IP. 
- Pokud definujete DHCP, cílovém serveru VMM funguje jako DHCP server a poskytuje IP adresu z fondu, která je definována pro síť virtuálních počítačů cíl.
- Pokud se používá fond statických IP adres je definována pro zdrojový server, cílovém serveru VMM přidělí IP adresu z fondu. V obou případech přidělování IP adres se nezdaří, pokud fond statických adres IP není definován.



## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury

Pokud chcete jednoduše zkontrolujte, že virtuálního počítače při selhání, můžete spustit převzetí služeb při selhání bez infrastruktury. Pokud chcete provést úplné procházení zotavení po Havárii pro testovací převzetí služeb při selhání aplikace, budete muset připravit infrastrukturu v sekundární lokalitě:

- Pokud spustíte testovací převzetí služeb při selhání pomocí stávající síť, Příprava služby Active Directory, DHCP a DNS v síti.
- Pokud spustíte testovací převzetí služeb s možností automaticky vytvořit síť virtuálních počítačů, budete muset přidat prostředky infrastruktury k síti automaticky vytvořený, před spuštěním testu převzetí služeb. V plánu obnovení může to usnadnit přidáním manuální krok před skupiny-1 v plánu obnovení, který se chystáte použít pro testovací převzetí služeb. Pak přidejte prostředky infrastruktury k síti automaticky vytvořené před spuštěním testu převzetí služeb.


### <a name="prepare-dhcp"></a>Příprava DHCP
Pokud součástí virtuálních počítačů testovací převzetí služeb při selhání používal protokol DHCP, vytvořit testovací server DHCP v rámci izolované sítě pro účely testovací převzetí služeb při selhání.


### <a name="prepare-active-directory"></a>Příprava služby Active Directory
Pokud chcete spustit testovací převzetí služeb pro aplikace, testování, je třeba kopii produkčního prostředí služby Active Directory v testovacím prostředí. Další informace najdete v článku [testovací převzetí služeb při selhání důležité informace týkající se služby Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Příprava DNS
Připravte DNS server pro testovací převzetí služeb takto:

* **DHCP**: Pokud virtuální počítače používat službu DHCP, IP adresu testu DNS by měly být aktualizovány na testovací server DHCP. Pokud používáte typ sítě virtualizace sítě systému Windows, VMM server funguje jako DHCP server. Proto je třeba aktualizovat na IP adresu DNS v testovací síti převzetí služeb při selhání. V takovém případě virtuální počítače zaregistrovat sami na relevantní server DNS.
* **Statická adresa**: Pokud virtuální počítače používat statickou IP adresu, na IP adresu serveru DNS testovací aktualizoval v testovací síti převzetí služeb při selhání. Možná budete muset aktualizovat DNS na IP adresu testovací virtuální počítače. Pro tento účel můžete použít následující ukázkový skript:

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

Tento postup popisuje, jak spouštět testovací převzetí služeb při selhání pro plán obnovení. Alternativně můžete spustit převzetí služeb při selhání pro jeden virtuální počítač na **virtuální počítače** kartě.

1. Vyberte **plány obnovení** > *recoveryplan_name*. Klikněte na tlačítko **převzetí služeb při selhání** > **testovací převzetí služeb při selhání**.
2. Na **testovací převzetí služeb při selhání** okno, zadejte, jak virtuální počítače replik, by měly být připojené k sítím po testu převzetí služeb.
3. Sledovat průběh převzetí služeb při selhání **úlohy** kartě.
4. Po dokončení převzetí služeb při selhání ověřte, jestli se virtuální počítače úspěšně spustit.
5. Když jste hotovi, klikněte na tlačítko **vyčistit testovací převzetí služeb při selhání** v plánu obnovení. V části **Poznámky** si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání. Tento krok odstraní všechny virtuální počítače a sítě, které byly vytvořeny pomocí Site Recovery při převzetí služeb při selhání. 

![Testovací převzetí služeb při selhání](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> IP adresa zadány pro virtuální počítač během testovacího převzetí služeb při selhání se stejnou IP adresu, která by virtuální počítač přijímat plánovaném nebo neplánovaném převzetí služeb při selhání (za předpokladu, že IP adresa je k dispozici v testovací síti převzetí služeb při selhání). Pokud není k dispozici v testovací síti převzetí služeb při selhání stejnou IP adresu, virtuální počítač přijímá jinou IP adresu, která je k dispozici v testovací síti převzetí služeb při selhání.



### <a name="run-a-test-failover-to-a-production-network"></a>Spustit testovací převzetí služeb pro produkční síť

Doporučujeme vám, že nemáte spustit testovací převzetí služeb síti produkční obnovení lokality, který jste určili během mapování sítě. Ale pokud je nutná k ověření připojení k síti začátku do konce do virtuálního počítače při selhání, mějte na paměti následující body:

* Ujistěte se, že primární virtuální počítač je vypnutý když provádíte testu převzetí služeb. Pokud to neuděláte, dva virtuální počítače se stejnou identitou bude spuštěna ve stejné síti současně. Tato situace může vést k neočekávanému důsledky.
* Veškeré změny, které provedete testovací převzetí služeb virtuálních počítačů jsou ztraceny, když vyčistit testovací převzetí služeb při selhání virtuálního počítače. Tyto změny nejsou replikovat zpět na primární virtuální počítače.
* Testování jako to vede k výpadku pro produkční aplikace. Požádejte uživatele aplikace není pro použití aplikace v průběhu procházení zotavení po Havárii.  


## <a name="next-steps"></a>Další postup
Po rozbalení zotavení po Havárii byl úspěšně spuštěn, můžete [spustit úplné převzetí služeb při selhání](site-recovery-failover.md).



