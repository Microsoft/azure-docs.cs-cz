---
title: Spuštění zotavení po havárii virtuálních počítačů Hyper-V do sekundární lokality pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak spustit postup zotavení po Havárii pro virtuální počítače Hyper-V v cloudech VMM do sekundárního místního datového centra pomocí služby Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: 3f7e534e9c698e31e1061c35aec713d20c7e570f
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211345"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Spustit postup zotavení po Havárii pro virtuální počítače Hyper-V do sekundární lokality


Tento článek popisuje, jak provést zotavení po havárii (DR) pro virtuální počítače Hyper-V, které jsou spravované v cloudech System Center Virtual Machine Manager V(MM), do sekundární místní lokality, pomocí [Azure Site Recovery](site-recovery-overview.md).

Testovací převzetí služeb při selhání ověřit vaši strategii replikace a provádět zotavení po Havárii přechod bez ztráty dat nebo výpadek. Testovací převzetí služeb nemá žádný vliv na probíhající replikaci nebo na vaše produkční prostředí. 

## <a name="how-do-test-failovers-work"></a>Jak otestovat převzetí služeb při selhání práce?

Při spouštění testovací převzetí služeb při selhání z primární do sekundární lokality. Pokud chcete jednoduše zkontrolujte, že virtuální počítač převezme při selhání, můžete spustit testovací převzetí služeb bez nastavování nic v sekundární lokalitě. Zkontrolujte, jestli aplikace převzetí služeb při selhání funguje podle očekávání, musíte se k nastavení sítě a infrastrukturu v sekundárním umístění.
- Testovací převzetí služeb můžete spouštět na jeden virtuální počítač nebo [plánu obnovení](site-recovery-create-recovery-plans.md).
- Testovací převzetí služeb bez připojení k síti, můžete spustit existující síti nebo s automaticky vytvořené síťové. V následující tabulce jsou k dispozici další podrobnosti o těchto možnostech.
    - Můžete spustit testovací převzetí služeb bez připojení k síti. Tato možnost je užitečná, pokud chcete zkontrolovat, že virtuální počítač byl schopen převzetí služeb při selhání, ale nebudete mít k ověření konfigurace.
    - Spusťte převzetí služeb při selhání s existující síť. Doporučujeme, abyste že nepoužíváte produkční sítě.
    - Převzetí služeb při selhání a nechat automaticky vytvořit testovací síti Site Recovery. V tomto případě se Site Recovery automaticky vytvořit síť a vyčištění po dokončení testovacího převzetí služeb při selhání.
- Je třeba vybrat bod obnovení pro převzetí služeb při selhání testu: 
    - **Nejnovější zpracovaný**: Tato možnost převezme virtuálního počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
    - **Nejnovější konzistentní vzhledem k**: tuto možnost převzetí služeb při selhání virtuálního počítače do bodu nejnovější konzistentní obnovení zpracovanému službou Site Recovery. 
    - **Nejnovější**: Tato možnost nejprve zpracuje všechna data, ke které byl odeslán do služby Site Recovery, chcete-li vytvořit bod obnovení pro každý virtuální počítač před přebírání služeb při selhání se. Tato možnost poskytuje nejnižší cíl bodu obnovení bodu obnovení (rpo), protože virtuální počítač vytvořen po převzetí služeb při selhání bude mít všechna data do Site Recovery replikovala při aktivaci převzetí služeb při selhání.
    - **Nejnovější více virtuálních počítačů zpracovat**: k dispozici pro plány obnovy, které zahrnují jeden nebo více virtuálních počítačů, které mají povolenou konzistencí pro víc virtuálních počítačů. Virtuální počítače s povoleným nastavením převzetí služeb při selhání na nejnovější společný bod obnovení konzistentní vzhledem k několika virtuálním počítačům. Ostatní virtuální počítače převzetí služeb při selhání do nejnovějšího bodu obnovení zpracované.
    - **Nejnovější více virtuálních počítačů konzistentní**: Tato možnost je dostupná pro plány obnovení s jeden nebo více virtuálních počítačů, které mají povolenou konzistencí pro víc virtuálních počítačů. Virtuální počítače, které jsou součástí replikační skupiny převzetí služeb při selhání na nejnovější společný bod obnovení konzistentní vzhledem k aplikaci pro více virtuálních počítačů. Ostatní virtuální počítače převzetí služeb při selhání na jejich nejnovější bod obnovení konzistentní s aplikací.
    - **Vlastní**: tuto možnost použijte k převzetí služeb při selhání konkrétní virtuální počítač, který konkrétnímu bodu obnovení.



## <a name="prepare-networking"></a>Připravte sítě

Když spustíte testovací převzetí služeb, budete vyzváni k nastavení sítě pro testovací počítače repliky, vyberte dle souhrnu v tabulce.

**Možnost** | **Podrobnosti** 
--- | --- 
**None** | Vytvoření testovacího virtuálního počítače na hostitele, na kterém je umístěn replikovaného virtuálního počítače. Není přidaná do cloudu a není připojen k žádné síti.<br/><br/> Na počítači můžete připojit k síti virtuálních počítačů po jeho vytvoření.
**Použít existující** | Vytvoření testovacího virtuálního počítače na hostitele, na kterém je umístěn replikovaného virtuálního počítače. Tam není přidaný do cloudu.<br/><br/>Vytvořte síť virtuálních počítačů, která bude izolovaná od produkční sítě.<br/><br/>Pokud používáte sítě využívající připojení VLAN, doporučujeme vytvořit samostatnou logickou síť (nelze použít v produkčním prostředí) v nástroji VMM pro tento účel. Tato logická síť se používá k vytvoření sítě virtuálních počítačů pro testovací převzetí služeb při selhání.<br/><br/>Logická síť by měly být přidružené minimálně jeden ze síťových adaptérů všech serverů Hyper-V, které jsou hostiteli virtuálních počítačů.<br/><br/>Pro logické sítě VLAN by měl být izolované síťové lokality, které přidáte k logické síti.<br/><br/>Pokud používáte logické sítě na bázi virtualizace sítě Windows, Azure Site Recovery automaticky vytvoří izolované sítě virtuálních počítačů. 
**Vytvoření sítě** | Dočasné testovací síti je vytvořen automaticky v závislosti na nastavení, které jste zadali v **logickou síť** a její související síťové lokality.<br/><br/> Převzetí služeb při selhání ověří, že se vytvoří virtuální počítače. |Tuto možnost používejte, pokud se plán obnovení používá více než jedna síť virtuálních počítačů.<br/><br/> Pokud používáte Windows virtualizace sítě, tato možnost automaticky vytvořit sítě virtuálních počítačů se stejným nastavením (podsítí a fondy IP adres) v síti virtuálního počítače repliky. Tyto sítě virtuálních počítačů se čistí automaticky po dokončení testu převzetí služeb.<br/><br/> Testovací virtuální počítač se vytvoří na hostiteli, na kterém se nachází virtuální počítač repliky. Tam není přidaný do cloudu.

### <a name="best-practices"></a>Osvědčené postupy

- Testování produkční sítě způsobí, že výpadky pro produkční úlohy. Požádejte uživatele, nepoužívat souvisejícími aplikacemi, když probíhá zotavení po havárii.

- Testovací síti nemusí shodovat s typem logická síť nástroje VMM použít pro testovací převzetí služeb při selhání. Ale nebudou fungovat některé kombinace:

     - Pokud replika používá DHCP a založená na síti VLAN izolace, síť virtuálních počítačů repliky nemusí fond statických IP adres. Pro testovací převzetí služeb pomocí virtualizace sítě Windows nebude fungovat, protože nejsou k dispozici žádné fondy adres. 
        
     - Testovací převzetí služeb při selhání nebude fungovat, pokud síť repliky používá bez izolace a testovací síti pomocí virtualizace sítě Windows. Je to proto síti bez izolace nemá podsítě potřeba vytvořit síť virtualizace sítě Windows.
        
- Doporučujeme vám, že nepoužíváte sítě, kterou jste vybrali pro mapování sítě pro testovací převzetí služeb při selhání.

- Jak pro mapovanou sítě virtuálních počítačů jsou připojené virtuální počítače repliky po převzetí služeb při selhání závisí na konfiguraci sítě virtuálních počítačů v konzole VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Síť virtuálních počítačů nakonfigurovaná bez izolace nebo izolace sítě VLAN

Pokud je síť virtuálních počítačů nakonfigurované v nástroji VMM bez izolace nebo izolace sítě VLAN, vezměte na vědomí následující:

- Pokud DHCP je definována pro síť virtuálních počítačů, virtuální počítač repliky je připojen k ID sítě VLAN prostřednictvím nastavení zadané v přidruženou logickou síť síťové lokality. Virtuální počítač obdrží IP adresu ze serveru DHCP k dispozici.
- Není nutné definovat fond statických IP adres pro Cílová síť virtuálních počítačů. Pokud použijete fond statických IP adres pro síť virtuálních počítačů, virtuální počítač repliky je připojen k ID sítě VLAN prostřednictvím nastavení zadané v přidruženou logickou síť síťové lokality.
- Virtuální počítač obdrží IP adresu z fondu, která je definována pro síť virtuálních počítačů. Pokud fond statických IP adres není definován v cílové síti virtuálních počítačů, se nezdaří přidělování IP adres. Vytvořte fond IP adres na zdrojovém i cílovém servery VMM, které budete používat pro ochranu a obnovení.

### <a name="vm-network-with-windows-network-virtualization"></a>Síť virtuálních počítačů s virtualizací sítě Windows

Pokud je síť virtuálních počítačů nakonfigurovaná v nástroji VMM pomocí virtualizace sítě Windows, vezměte na vědomí následující:

- Byste měli definovat statický fond pro Cílová síť virtuálních počítačů, bez ohledu na to, zda je zdrojové síti virtuálních počítačů umožňují použít protokol DHCP nebo fond statických IP adres. 
- Pokud definujete DHCP, cílovém serveru VMM funguje jako DHCP server a mu poskytne IP adresu z fondu, který je definován pro cílovou síť virtuálních počítačů.
- Pokud používá fond statických IP adres není definován pro zdrojový server, cílovém serveru VMM přidělí IP adresu z fondu. V obou případech přidělování IP adres selže, pokud není definován fond statických IP adres.



## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury

Pokud chcete jednoduše zkontrolujte, že virtuální počítač, můžete převzetí služeb při selhání, můžete spustit testovací převzetí služeb bez infrastruktury. Pokud chcete provést úplné postup zotavení po Havárii pro testovací převzetí služeb při selhání aplikace, budete muset připravit infrastrukturu v sekundární lokalitě:

- Pokud spustíte testovací převzetí služeb pomocí stávající síť, Příprava služby Active Directory, DHCP a DNS v dané síti.
- Pokud spustíte testovací převzetí služeb možnost automaticky vytvořit síť virtuálních počítačů, musíte přidat prostředky infrastruktury automaticky vytvořené síťové, před spuštěním testu převzetí služeb. V plánu obnovení usnadní to tak, že přidáte provedení ručního kroku před 1 skupinu v plánu obnovení, který se chystáte použít pro testovací převzetí služeb. Pak přidejte prostředky infrastruktury k síti automaticky vytvořené před spuštěním testu převzetí služeb.


### <a name="prepare-dhcp"></a>Příprava DHCP
Pokud virtuální počítače součástí testovacího převzetí služeb při selhání použít protokol DHCP, vytvořte testovací server DHCP v izolované síti za účelem testovací převzetí služeb při selhání.


### <a name="prepare-active-directory"></a>Příprava služby Active Directory
Ke spuštění testovací převzetí služeb při selhání pro testování aplikace, budete potřebovat kopii produkčního prostředí služby Active Directory ve vašem testovacím prostředí. Další informace najdete v článku [testovací převzetí služeb při selhání důležité informace týkající se služby Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Příprava DNS
Připravte DNS server pro testovací převzetí služeb následujícím způsobem:

* **DHCP**: Pokud virtuální počítače používají protokol DHCP, je třeba aktualizovat IP adresu, testu DNS na testovacím serveru DHCP. Pokud používáte typ sítě Windows virtualizace sítě, VMM server funguje jako DHCP server. Proto se IP adresa DNS se musí aktualizovat v testovací síti převzetí služeb při selhání. V tomto případě virtuální počítače zaregistrovat se na příslušný server DNS.
* **Statická adresa**: Pokud virtuální počítače používat statickou IP adresu, je třeba aktualizovat IP adresu serveru DNS, testu v testovací síti převzetí služeb při selhání. Může být potřeba aktualizovat DNS IP adresou testovací virtuální počítače. K tomuto účelu můžete použít následující ukázkový skript:

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

Tento postup popisuje, jak spustit testovací převzetí služeb při selhání pro plán obnovení. Alternativně můžete spustit převzetí služeb při selhání pro jeden virtuální počítač na **virtuálních počítačů** kartu.

1. Vyberte **plány obnovení** > *recoveryplan_name*. Klikněte na tlačítko **převzetí služeb při selhání** > **testovací převzetí služeb při selhání**.
2. Na **testovací převzetí služeb při selhání** okno, zadejte, jak virtuální počítače replik musí být připojené k sítím po převzetí služeb při selhání testu.
3. Sledovat průběh převzetí služeb při selhání **úlohy** kartu.
4. Po dokončení převzetí služeb při selhání ověřte, že virtuální počítače úspěšně spustí.
5. Jakmile budete hotovi, klikněte na tlačítko **vyčištění testovacího převzetí služeb při selhání** v plánu obnovení. V části **Poznámky** si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání. Tento krok odstraní všechny virtuální počítače a sítě, které byly vytvořeny pomocí Site Recovery během testovacího převzetí služeb při selhání. 

![Testovací převzetí služeb při selhání](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> IP adresa, vzhledem k virtuálnímu počítači během testovacího převzetí služeb při selhání se stejnou IP adresu, která bude dostávat virtuálního počítače plánované nebo neplánované převzetí služeb při selhání (za předpokladu, že IP adresa je k dispozici v testovací síti převzetí služeb při selhání). Pokud stejnou IP adresu není k dispozici v testovací síti převzetí služeb při selhání, virtuálnímu počítači obdrží jinou IP adresu, která je k dispozici v testovací síti převzetí služeb při selhání.



### <a name="run-a-test-failover-to-a-production-network"></a>Spustit testovací převzetí služeb do produkční sítě

Doporučujeme vám, že při spuštění testovací převzetí služeb při selhání do produkční sítě site recovery, který jste zadali během mapování sítě. Ale pokud potřebujete k ověření připojení k síti začátku do konce v virtuálního počítače s převzetím služeb při selhání, mějte na paměti následující body:

* Ujistěte se, že primární virtuální počítač je vypnutý při testu převzetí služeb. Pokud to neuděláte, dva virtuální počítače se stejnou identitou bude běžet ve stejné síti ve stejnou dobu. Tato situace může způsobit nežádoucí důsledky.
* Jakékoli provedené změny, které provedete testovací převzetí služeb virtuálních počítačů při vyčistit testovací převzetí služeb při selhání virtuálního počítače. Tyto změny se nereplikují zpět do primární virtuální počítače.
* Testování takto vede k výpadky pro produkční aplikace. Požádejte uživatele aplikace není k používání aplikace, když probíhá na postup zotavení po Havárii.  


## <a name="next-steps"></a>Další postup
Po úspěšném spuštění zotavení můžete [úplné převzetí služeb při selhání](site-recovery-failover.md).



