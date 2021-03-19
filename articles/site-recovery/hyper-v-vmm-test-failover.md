---
title: Spuštění postupu zotavení po havárii NHyper-V na sekundární lokalitu pomocí Azure Site Recovery
description: Přečtěte si, jak spustit postup zotavení po havárii pro virtuální počítače Hyper-V v cloudech VMM do sekundárního místního datacentra pomocí Azure Site Recovery.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sideeksh
ms.openlocfilehash: 569af28f5773d843f49dd9c8143b45e308ae142e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87420409"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Spuštění postupu zotavení po havárii pro virtuální počítače Hyper-V do sekundární lokality


Tento článek popisuje, jak provést zotavení po havárii (DR) pro virtuální počítače Hyper-V, které jsou spravované v cloudech System Center Virtual Machine Manager V (MM), do sekundární místní lokality pomocí [Azure Site Recovery](site-recovery-overview.md).

Spuštěním testovacího převzetí služeb při selhání ověříte strategii replikace a provedete postup zotavení po havárii, aniž by došlo ke ztrátě dat nebo výpadkům. Testovací převzetí služeb při selhání nemá žádný vliv na probíhající replikaci nebo na produkční prostředí. 

## <a name="how-do-test-failovers-work"></a>Jak testovací převzetí služeb při selhání funguje?

Spustíte testovací převzetí služeb při selhání z primární lokality do sekundární lokality. Pokud chcete jenom zkontrolovat, že virtuální počítač převezme služby při selhání, můžete spustit testovací převzetí služeb při selhání bez nastavování v sekundární lokalitě. Pokud chcete ověřit, jestli aplikace převzetí služeb při selhání funguje podle očekávání, budete muset nastavit síť a infrastrukturu v sekundárním umístění.
- Testovací převzetí služeb při selhání můžete spustit na jednom virtuálním počítači nebo v [plánu obnovení](site-recovery-create-recovery-plans.md).
- Testovací převzetí služeb při selhání můžete spustit bez sítě, se stávající sítí nebo s automaticky vytvořenou sítí. Další podrobnosti o těchto možnostech najdete v následující tabulce.
    - Testovací převzetí služeb při selhání můžete spustit bez sítě. Tato možnost je užitečná, pokud chcete jenom zkontrolovat, že virtuální počítač byl schopný převzít služby při selhání, ale nebudete moct ověřit žádnou konfiguraci sítě.
    - Spusťte převzetí služeb při selhání pomocí existující sítě. Nedoporučujeme používat produkční síť.
    - Spusťte převzetí služeb při selhání a umožněte Site Recovery automaticky vytvořit testovací síť. V tomto případě Site Recovery vytvoří síť automaticky a vyčistí ji při dokončení testovacího převzetí služeb při selhání.
- Je nutné vybrat bod obnovení pro testovací převzetí služeb při selhání: 
    - **Poslední zpracování**: Tato možnost neúspěšně provedla virtuální počítač pro poslední bod obnovení zpracovaný pomocí Site Recovery. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.
    - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost převzetí služeb při selhání virtuálního počítače na nejnovější bod obnovení konzistentní vzhledem k aplikacím zpracovaného Site Recovery. 
    - **Nejnovější**: Tato možnost nejprve zpracuje všechna data, která byla odeslána do služby Site Recovery Service, aby bylo možné vytvořit bod obnovení pro každý virtuální počítač před tím, než dojde k převzetí služeb při selhání. Tato možnost poskytuje nejnižší cíl bodu obnovení (RPO), protože virtuální počítač vytvořený po převzetí služeb při selhání bude mít všechna data replikována do Site Recovery při aktivaci převzetí služeb při selhání.
    - **Nejnovější zpracovaný vícenásobný virtuální počítač**: k dispozici pro plány obnovení, které zahrnují jeden nebo více virtuálních počítačů s povolenou konzistencí pro víc virtuálních počítačů. Virtuální počítače s povoleným nastavením převezmou na nejnovější společný bod obnovení konzistentní s více virtuálními počítači. Ostatní virtuální počítače převezmou služby na nejnovější zpracovaný bod obnovení.
    - **Nejnovější konzistentní vzhledem k aplikacím pro více virtuálních počítačů**: Tato možnost je k dispozici pro plány obnovení s povoleným zajištěním konzistence více virtuálních počítačů s jedním nebo více virtuálními počítači. Virtuální počítače, které jsou součástí replikační skupiny, převezmou nejnovější běžný bod obnovení konzistentní s aplikacemi pro více virtuálních počítačů. Jiné virtuální počítače převezme služby při selhání do svého nejnovějšího bodu obnovení konzistentního vzhledem k aplikacím.
    - **Vlastní**: tuto možnost použijte, pokud chcete převzít služby při selhání určitého virtuálního počítače na určitý bod obnovení.



## <a name="prepare-networking"></a>Příprava sítě

Při spuštění testovacího převzetí služeb při selhání se zobrazí výzva k výběru nastavení sítě pro počítače testovací repliky, jak je shrnuto v tabulce.

| **Možnost** | **Podrobnosti** | |
| --- | --- | --- |
| **Žádný** | Testovací virtuální počítač se vytvoří na hostiteli, na kterém je umístěný virtuální počítač repliky. Není přidaný do cloudu a není připojený k žádné síti.<br/><br/> Počítač můžete po vytvoření připojit k síti virtuálních počítačů.| |
| **Použít existující** | Testovací virtuální počítač se vytvoří na hostiteli, na kterém je umístěný virtuální počítač repliky. Není přidaný do cloudu.<br/><br/>Vytvořte síť virtuálních počítačů, která je izolovaná od produkční sítě.<br/><br/>Pokud používáte síť na bázi VLAN, doporučujeme pro tento účel vytvořit samostatnou logickou síť (nepoužitou v produkčním prostředí). Tato logická síť slouží k vytváření sítí virtuálních počítačů pro testovací převzetí služeb při selhání.<br/><br/>Logická síť by měla být přidružená alespoň k jednomu ze síťových adaptérů všech serverů Hyper-V, které jsou hostiteli virtuálních počítačů.<br/><br/>U logických sítí VLAN by se měly izolované síťové lokality, které přidáte do logické sítě.<br/><br/>Pokud používáte logickou síť založenou na virtualizaci sítě Windows, Azure Site Recovery automaticky vytvoří izolované sítě virtuálních počítačů. | |
| **Vytvoření sítě** | Dočasná testovací síť je vytvořena automaticky na základě nastavení, které zadáte v **logické síti** a v příslušných síťových lokalitách.<br/><br/> Převzetí služeb při selhání kontroluje, jestli jsou virtuální počítače vytvořené.<br/><br/> Tuto možnost byste měli použít, pokud plán obnovení používá více než jednu síť virtuálních počítačů.<br/><br/> Pokud používáte sítě virtualizace sítě Windows, tato možnost umožňuje automaticky vytvořit sítě virtuálních počítačů se stejnými nastaveními (podsítě a fondy IP adres) v síti virtuálního počítače repliky. Tyto sítě virtuálních počítačů se vyčistí automaticky po dokončení testovacího převzetí služeb při selhání.<br/><br/> Testovací virtuální počítač se vytvoří na hostiteli, na kterém existuje virtuální počítač repliky. Není přidaný do cloudu.|

### <a name="best-practices"></a>Osvědčené postupy

- Testování produkční sítě způsobuje výpadek produkčních úloh. Požádejte uživatele, aby při přechodu na zotavení po havárii nepoužívali související aplikace.

- Testovací síť nemusí odpovídat typu logické sítě VMM, který se používá pro testovací převzetí služeb při selhání. Některé kombinace ale nefungují:

     - Pokud replika používá izolaci pomocí protokolu DHCP a sítě VLAN, síť virtuálních počítačů pro repliku nepotřebuje fond statických IP adres. Proto použití virtualizace sítě Windows pro testovací převzetí služeb při selhání nebude fungovat, protože nejsou k dispozici žádné fondy adres. 
        
     - Testovací převzetí služeb při selhání nebude fungovat, pokud síť repliky nepoužívá izolaci a testovací síť používá virtualizaci sítě systému Windows. Důvodem je to, že síť bez izolace nemá podsítě potřebné k vytvoření sítě virtualizace sítě systému Windows.
        
- Pro testovací převzetí služeb při selhání doporučujeme, abyste nepoužívali síť, kterou jste vybrali pro mapování sítě.

- Způsob připojení virtuálních počítačů replik k namapovaným sítím virtuálních počítačů po převzetí služeb při selhání závisí na tom, jak je síť virtuálních počítačů nakonfigurovaná v konzole VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Síť virtuálních počítačů konfigurovaná bez izolace izolace nebo sítě VLAN

Pokud je v nástroji VMM nakonfigurovaná síť virtuálních počítačů bez izolace nebo izolace sítě VLAN, pamatujte na toto:

- Pokud je pro síť virtuálních počítačů definován protokol DHCP, je virtuální počítač repliky připojen k ID sítě VLAN prostřednictvím nastavení, která jsou zadána pro síťovou lokalitu v přidružené logické síti. Virtuální počítač obdrží svou IP adresu z dostupného serveru DHCP.
- Pro cílovou síť virtuálních počítačů není nutné definovat fond statických IP adres. Pokud se pro síť virtuálních počítačů používá fond statických IP adres, je virtuální počítač repliky připojený k ID sítě VLAN pomocí nastavení, která jsou zadána pro síťovou lokalitu v přidružené logické síti.
- Virtuální počítač obdrží svou IP adresu z fondu, který je definován pro síť virtuálních počítačů. Pokud není v cílové síti virtuálních počítačů definován fond statických IP adres, přidělování IP adres se nezdaří. Vytvořte fond IP adres na zdrojovém i cílovém serveru VMM, který budete používat pro ochranu a obnovení.

### <a name="vm-network-with-windows-network-virtualization"></a>Síť virtuálních počítačů s virtualizací sítě systému Windows

Pokud je v nástroji VMM nakonfigurovaná síť virtuálních počítačů s virtualizací sítě Windows, pamatujte na toto:

- Pro cílovou síť virtuálních počítačů byste měli definovat statický fond bez ohledu na to, jestli je zdrojová síť virtuálních počítačů nakonfigurovaná tak, aby používala protokol DHCP nebo fond statických IP adres. 
- Pokud definujete protokol DHCP, cílový server VMM funguje jako server DHCP a poskytuje IP adresu z fondu, který je definovaný pro cílovou síť virtuálních počítačů.
- Pokud je pro zdrojový server definovaná použití fondu statických IP adres, cílový server VMM přidělí IP adresu z fondu. V obou případech se přidělení IP adresy nezdaří, pokud není definovaný fond statických IP adres.



## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury

Pokud chcete jenom zkontrolovat, jestli virtuální počítač může převzít služby při selhání, můžete spustit testovací převzetí služeb při selhání bez infrastruktury. Pokud chcete provést úplný postup zotavení po selhání pomocí nástroje DR a otestovat převzetí služeb při selhání, je nutné připravit infrastrukturu v sekundární lokalitě:

- Pokud spustíte testovací převzetí služeb při selhání pomocí existující sítě, připravte v této síti službu Active Directory, DHCP a DNS.
- Pokud spustíte testovací převzetí služeb při selhání s možností vytvoření sítě virtuálních počítačů automaticky, musíte před spuštěním testovacího převzetí služeb při selhání přidat prostředky infrastruktury do automaticky vytvořené sítě. V plánu obnovení to můžete usnadnit přidáním ručního kroku před skupinu-1 v plánu obnovení, který budete používat pro testovací převzetí služeb při selhání. Pak přidejte prostředky infrastruktury do automaticky vytvořené sítě před spuštěním testovacího převzetí služeb při selhání.


### <a name="prepare-dhcp"></a>Příprava DHCP
Pokud virtuální počítače, které jsou součástí testovacího převzetí služeb při selhání, používají protokol DHCP, vytvořte v izolované síti testovací server DHCP pro účely testovacího převzetí služeb při selhání.


### <a name="prepare-active-directory"></a>Příprava služby Active Directory
Pro spuštění testovacího převzetí služeb při testování aplikací potřebujete kopii produkčního prostředí Active Directory v testovacím prostředí. Další informace najdete v části [testovací převzetí služeb při selhání pro službu Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Příprava DNS
Připravte server DNS pro testovací převzetí služeb při selhání následujícím způsobem:

* **DHCP**: Pokud virtuální počítače používají protokol DHCP, měla by být na testovacím serveru DHCP aktualizována IP adresa testovacího serveru DNS. Pokud používáte typ sítě virtualizace sítě systému Windows, server VMM funguje jako server DHCP. Proto by se měla v síti testovacího převzetí služeb při selhání aktualizovat IP adresa DNS. V takovém případě se virtuální počítače registrují na příslušný server DNS.
* **Statická adresa**: Pokud virtuální počítače používají statickou IP adresu, měla by být v síti testovacího převzetí služeb při selhání aktualizována IP adresa testovacího serveru DNS. Možná budete muset aktualizovat DNS s použitím IP adresy testovacích virtuálních počítačů. K tomuto účelu můžete použít následující vzorový skript:

  ```powershell
  Param(
  [string]$Zone,
  [string]$name,
  [string]$IP
  )
  $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
  $newrecord = $record.clone()
  $newrecord.RecordData[0].IPv4Address  =  $IP
  Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
  ```

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Tento postup popisuje, jak spustit testovací převzetí služeb při selhání pro plán obnovení. Alternativně můžete spustit převzetí služeb při selhání pro jeden virtuální počítač na kartě **Virtual Machines** .

1. Vyberte **plány obnovení**  >  *recoveryplan_name*. Klikněte na **převzetí služeb při selhání**  >  **test Failover**.
2. V okně **Test převzetí služeb při selhání** určete, jak se mají virtuální počítače repliky po testovacím převzetí služeb při selhání připojit k sítím.
3. Sledujte průběh převzetí služeb při selhání na kartě **úlohy** .
4. Po dokončení převzetí služeb při selhání ověřte, že se virtuální počítače úspěšně spustily.
5. Až skončíte, klikněte na **Vyčištění testovacího převzetí služeb při selhání** v plánu obnovení. V části **poznámky** si zaznamenejte a uložte všechny poznámky spojené s testovacím převzetím služeb při selhání. Tento krok odstraní všechny virtuální počítače a sítě, které Site Recovery vytvořil během testovacího převzetí služeb při selhání. 

![Testovací převzetí služeb při selhání](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> IP adresa zadaná pro virtuální počítač během testovacího převzetí služeb při selhání je stejná IP adresa, kterou by virtuální počítač dostal pro plánované nebo neplánované převzetí služeb při selhání (předpokládá se, že IP adresa je k dispozici v síti testovacího převzetí služeb při selhání). Pokud v síti testovacího převzetí služeb při selhání není dostupná stejná IP adresa, virtuální počítač obdrží jinou IP adresu, která je k dispozici v síti testovacího převzetí služeb při selhání.



### <a name="run-a-test-failover-to-a-production-network"></a>Spuštění testovacího převzetí služeb při selhání do produkční sítě

Nedoporučujeme spouštět testovací převzetí služeb při selhání do vaší provozní sítě lokality pro obnovení, kterou jste zadali během mapování sítě. Pokud ale potřebujete ověřit koncové síťové připojení ve virtuálním počítači s podporou převzetí služeb při selhání, mějte na paměti následující body:

* Ujistěte se, že při provádění testovacího převzetí služeb při selhání dojde k vypnutí primárního virtuálního počítače. Pokud to neuděláte, dva virtuální počítače se stejnou identitou budou běžet ve stejné síti současně. Tato situace může vést k neočekávaným důsledkům.
* Při vyčištění virtuálních počítačů testovacího převzetí služeb při selhání dojde ke ztrátě všech změn, které provedete na virtuálních počítačích testovacího převzetí služeb při selhání. Tyto změny se nereplikují zpátky do primárních virtuálních počítačů.
* Testování, jako by to vedlo k výpadkům vašich produkčních aplikací. Požádejte uživatele aplikace, aby aplikaci nepoužívali, když probíhá postup zotavení po havárii.  


## <a name="next-steps"></a>Další kroky
Po úspěšném spuštění postupu zotavení po havárii můžete [Spustit úplné převzetí služeb při selhání](site-recovery-failover.md).



