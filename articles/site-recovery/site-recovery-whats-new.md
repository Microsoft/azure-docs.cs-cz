---
title: Co je nového v Azure Site Recovery
description: Obsahuje souhrn nových funkcí a nejnovější aktualizace ve službě Azure Site Recovery.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257430"
---
# <a name="whats-new-in-site-recovery"></a>Co je nového ve službě Site Recovery

Služba [Azure Site Recovery](site-recovery-overview.md) se průběžně aktualizuje a vylepšuje. Tento článek vám poskytne informace o nejnovějších verzích, nových funkcích a novém obsahu, abyste měli přehled o nejnovějších verzích. Tato stránka je pravidelně aktualizována.

Oznámení o aktualizacích site recovery můžete sledovat a přihlásit se k odběru oznámení o aktualizacích webu v kanálu [aktualizací Azure.](https://azure.microsoft.com/updates/?product=site-recovery)

## <a name="supported-updates"></a>Podporované aktualizace

Pro součásti site recovery podporujeme verze N-4, kde N je nejnovější verze. V následující tabulce je jejich přehled.

**Aktualizace** |  **Sjednocené nastavení** | **Konfigurační server ova** | **Agent služeb mobility** | **Zprostředkovatel obnovení webu** | **Agent služby obnovení**
--- | --- | --- | --- | --- | ---
[Souhrn 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[Souhrn 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[Kumulativní 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Kumulativní 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Souhrn 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

[Přečtěte si další informace](service-updates-how-to.md) o instalaci a podpoře aktualizací.

> [!NOTE]
> Kumulativní aktualizace 44 se v tabulce nezobrazuje, protože neobsahuje aktualizace pro poskytovatele a agenty obnovení webu.

## <a name="updates-march-2020"></a>Aktualizace (březen 2020)

### <a name="update-rollup-45"></a>Kumulativní aktualizace 45

[Kumulativní aktualizace 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a zprostředkovatelů obnovení webu, jak je podrobně popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je podrobně popsáno v souhrnu.

## <a name="updates-january-2020"></a>Aktualizace (leden 2020)

### <a name="update-rollup-44"></a>Kumulativní aktualizace 44

[Kumulativní aktualizace 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Pro poskytovatele a agenty site recovery nebyly k dispozici žádné aktualizace.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je podrobně popsáno v souhrnu.

### <a name="azure-vmware-disaster-recovery"></a>Obnovení zotavení po havárii Azure VMware

Virtuální počítače Azure teď podporují virtuální počítače povolit šifrování v klidovém stavu s klíči spravovanými zákazníky. [Další informace](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Kumulativní aktualizace 43

[Kumulativní aktualizace 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a poskytovatelů site recovery (jak je podrobně popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu)


## <a name="updates-november-2019"></a>Aktualizace (listopad 2019)

### <a name="update-rollup-42"></a>Kumulativní aktualizace 42

[Kumulativní aktualizace 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a poskytovatelů site recovery (jak je podrobně popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu)


### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Nové funkce pro zotavení po havárii virtuálního počítače Azure jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Uefi** | Site Recovery teď podporuje zotavení po havárii pro virtuální počítače Azure s architekturou zavádění na bázi UEFI.
**Linux** | Site Recovery teď podporuje virtuální počítače Azure se systémem Linux s Azure Disk Encryption (ADE).
**2. generace** | Všechny virtuální počítače Azure generace 2 jsou teď podporované pro zotavení po havárii.
**Oblasti** | Teď můžete povolit zotavení po havárii pro virtuální počítače Azure v norsku geo.

### <a name="vmware-to-azure-disaster-recovery"></a>Zotavení po havárii VMware do Azure

Nové funkce pro zotavení po havárii v systému VMware do Azure jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Uefi** | Site Recovery nyní podporuje zotavení po havárii pro virtuální zařízení VMware s architekturou zavádění založené na UEFI.<br/><br/> Mezi podporované operační systémy patří Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Aktualizace aktualizace servisního zásobníku/SHA-2

Pro zotavení po havárii virtuálních počítačů Azure do sekundární oblasti nebo místní virtuální počítače VMware nebo fyzické servery do Azure, na vědomí následující:

- Od verze 9.30.5407.1 rozšíření služby Mobility (pro virtuální počítače Azure) a agenta služeb mobility (pro vmware/fyzické počítače) musí některé operační systémy počítače spouštět aktualizaci zásobníku údržby a SHA-2. Podrobnosti jsou uvedeny v následující tabulce.
- Nainstalujte aktualizaci a SHA-2 v souladu s propojenou KB. SHA-1 není podporována od září 2019 a pokud není povoleno podepisování kódu SHA-2, rozšíření agenta se nenainstaluje nebo neupgraduje podle očekávání.
- Další informace o [upgradu sha-2 a požadavcích](https://aka.ms/SHA-2KB).

**Operační systém** | **Virtuální počítač Azure** | **VMware VM/fyzický počítač**
--- | --- | ---
**Windows 2008 R2 SP1** | [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Aktualizace (říjen 2019)

### <a name="update-rollup-41"></a>Kumulativní aktualizace 41

[Kumulativní aktualizace 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a poskytovatelů site recovery (jak je podrobně popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu)



### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Nové funkce pro zotavení po havárii virtuálního počítače Azure jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Testování nastavení převzetí služeb při selhání** | Při nastavování zkušebnípřevzetí služeb při selhání, můžete nyní nakonfigurovat nastavení pro testovací převzetí služeb při selhání virtuálního počítače a sítě, včetně IP adresy, NSG, vnitřní vyrovnávání zatížení a veřejné IP adresy pro každou síťovou síťovou síť pro počítač. Tato nastavení jsou volitelná a nemění aktuální chování. Pokud tato nastavení nenakonfigurujete, můžete virtuální síť Azure zvolit v době převzetí služeb při selhání testu. [Další informace](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Plány obnovení** | Plány obnovení jsou teď omezeny na 100 virtuálních aplikací, aby byla zajištěna spolehlivost převzetí služeb při selhání.

### <a name="vmware-to-azure-disaster-recovery"></a>Zotavení po havárii VMware do Azure

Nové funkce pro zotavení po havárii v systému VMware do Azure jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Plány obnovení** | Plány obnovení jsou teď omezeny na 100 virtuálních aplikací, aby byla zajištěna spolehlivost převzetí služeb při selhání.


## <a name="updates-september-2019"></a>Aktualizace (září 2019)

### <a name="update-rollup-40"></a>Kumulativní aktualizace 40

[Kumulativní aktualizace 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a poskytovatelů site recovery (jak je podrobně popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu)




### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Nové funkce pro zotavení po havárii virtuálního počítače Azure jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Vyčištění po navrácení služeb po selhání** | Po převzetí služby při selhání sekundární Azure a potom převzetí min. zpět do primární oblasti, site recovery automaticky vyčistí počítače v sekundární oblasti. Není nutné ručně odstraňovat virtuální počítače a síťové karty.
**Zkušební převzetí služeb při selhání si zachová adresu IP** | Nyní můžete zachovat IP adresu zdrojového virtuálního soudu během cvičení zotavení po havárii a vybrat statickou IP adresu pro převzetí služeb při selhání testu.

### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii serveru VMware/fyzického serveru

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
Výstrahy nového procesního serveru | Přidali jsme nové výstrahy procesního serveru. [Další informace](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Zotavení po havárii hyper-V

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
Účet úložiště | Site Recovery teď podporuje použití účtů úložiště s bránou firewall povolenou pro zotavení po havárii Technologie Hyper-V do Azure.  Můžete vybrat účty úložiště s bránou firewall jako cílový účet nebo pro úložiště mezipaměti. Pokud používáte účet s bránou firewall, ujistěte se, že povolujete možnost povolit důvěryhodné služby společnosti Microsoft.<br/><br/> To je podporováno pro virtuální aplikace Hyper-V s nebo bez System Center VMM.


## <a name="updates-august-2019"></a>Aktualizace (srpen 2019)

### <a name="update-rollup-39"></a>Kumulativní aktualizace 39

[Kumulativní aktualizace 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a poskytovatelů site recovery (jak je podrobně popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu)


### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Nové funkce pro zotavení po havárii virtuálního počítače Azure jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Šifrování bez Azure AD** | Šifrování bez aplikace Azure AD je teď podporované pro replikaci virtuálních počítačů Azure na spravované disky se systémem Windows.
**Síťové prostředky pro převzetí služeb při selhání** | Při převzetí softwaru při selhání do jiné oblasti, můžete nyní připojit nastavení síťových prostředků (skupiny nsg, vyrovnávání zatížení, veřejná IP adresa) k virtuálnímu počítače.

## <a name="updates-july-2019"></a>Aktualizace (červenec 2019)

### <a name="update-rollup-38"></a>Kumulativní aktualizace 38

[Kumulativní aktualizace 38](https://support.microsoft.com/help/4513507/) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a poskytovatelů site recovery (jak je podrobně popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu)


### <a name="general"></a>Obecné

Site Recovery nyní podporuje použití účtů úložiště pro obecné účely v2 pro ukládání mezipaměti nebo cílové úložiště. Dříve byl podporován pouze v1.

### <a name="vmware-to-azure-disaster-recovery"></a>Zotavení po havárii VMware do Azure

Teď můžete replikovat disky s kapacitou až 8 TB, při replikaci do virtuálního počítače Azure se spravovanými disky.


## <a name="updates-june-2019"></a>Aktualizace (červen 2019)

### <a name="update-rollup-37"></a>Kumulativní aktualizace 37

[Kumulativní aktualizace 37](https://support.microsoft.com/help/4508614/) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a poskytovatelů site recovery (jak je podrobně popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu)


### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii serveru VMware/fyzického serveru

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Oddíly GPT** | Od kumulativní aktualizace 37 a dále (služba Mobility verze 9.25.5241.1) je v UEFI podporováno až pět oddílů GPT. Před touto aktualizací byly podporovány čtyři.



## <a name="updates-may-2019"></a>Aktualizace (květen 2019)

### <a name="update-rollup-36"></a>Kumulativní aktualizace 36

[Kumulativní aktualizace 36](https://support.microsoft.com/help/4503156) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a poskytovatelů site recovery (jak je podrobně popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu)

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Replikovat přidané disky** | Povolte replikaci pro datové disky přidané do virtuálního počítače Azure, který už je povolený pro zotavení po havárii. [Další informace](azure-to-azure-enable-replication-added-disk.md).
**Automatické aktualizace** | Při konfiguraci automatických aktualizací pro rozšíření služby Mobility, které běží na virtuálních počítačích Azure povolené pro zotavení po havárii, můžete teď vybrat existující účet automatizace, který chcete použít, namísto použití výchozího účtu vytvořeného site recovery. [Další informace](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii serveru VMware/fyzického serveru

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Monitorování procesního serveru** | Pro zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů, monitorování a řešení problémů s procesním serverem s vylepšenými zprávami o stavu serveru a výstrahami. [Další informace](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Aktualizace (březen 2019)

### <a name="update-rollup-35"></a>Kumulativní aktualizace 35

[Kumulativní aktualizace 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a poskytovatelů site recovery (jak je podrobně popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu)

### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii serveru VMware/fyzického serveru

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Spravované disky** | Replikace místních virtuálních počítačů VMware a fyzických serverů je teď přímo na spravované disky v Azure. Místní data se posílají do účtu úložiště mezipaměti v Azure a body obnovení se vytvářejí na spravovaných discích v cílovém umístění. Tím zajistíte, že nemusíte spravovat více cílových účtů úložiště.
**Konfigurační server** | Site Recovery nyní podporuje konfigurační servery s více síťové karty. Před registrací konfiguračního serveru v úložišti přidejte do virtuálního počítače konfiguračního serveru další adaptéry. Pokud přidáte později, budete muset znovu zaregistrovat server v úschovně.


## <a name="updates-february-2019"></a>Aktualizace (únor 2019)

### <a name="update-rollup-34"></a>Kumulativní aktualizace 34

[Kumulativní aktualizace 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) obsahuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a zprostředkovatelů obnovení webu (jak je podrobně popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu).


### <a name="update-rollup-33"></a>Kumulativní aktualizace 33

[Kumulativní aktualizace 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a zprostředkovatelů obnovení webu (jak je podrobně popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu).


### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Mapování sítě** | Pro zotavení po havárii virtuálního počítače Azure teď můžete použít libovolnou dostupnou cílovou síť, když povolíte replikaci.
**SSD úrovně Standard** | Teď můžete nastavit zotavení po havárii pro virtuální počítače Azure pomocí [standardních disků SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Prostory úložiště – přímé** | Zotavení po havárii můžete nastavit pro aplikace spuštěné v aplikacích virtuálních zařízení Azure pomocí [storage spaces direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) pro vysokou dostupnost.  Použití storage spaces direct (S2D) spolu s site recovery poskytuje komplexní ochranu úloh virtuálních virtuálních zařízení Azure. S2D umožňuje hostovat cluster hosta v Azure. To je užitečné zejména v případě, že virtuální počítač hostuje kritickou aplikaci, jako je například vrstva SAP ASCS, SQL Server nebo souborový server s horizontálním navýšením kapacity.


### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii serveru VMware/fyzického serveru

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Souborový systém Linux BRTFS** | Site Recovery nyní podporuje replikaci virtuálních měn VMware se systémem souborů BRTFS. Replikace není podporována, pokud:<br/><br/>- Podobjem souborového systému BTRFS se po povolení replikace změní.<br/><br/>- Souborový systém je rozložen na více discích.<br/><br/>- Souborový systém BTRFS podporuje RAID.
**Windows Server 2019** | Byla přidána podpora pro počítače se systémem Windows Server 2019.


## <a name="updates-january-2019"></a>Aktualizace (leden 2019)


### <a name="accelerated-networking-azure-vms"></a>Zrychlené sítě (virtuální počítače Azure)

Zrychlená síť umožňuje virtualizaci vstupně-videa jednoho kořene (SR-IOV) do virtuálního počítače a zlepšuje výkon sítě. Když povolíte replikaci pro virtuální počítač Azure, site recovery zjistí, zda je povolena zrychlená síť. Pokud ano, po převzetí služeb při selhání site recovery automaticky nakonfiguruje zrychlené sítě na cílové repliky virtuálního počítače Azure, pro [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) i [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Kumulativní aktualizace 32

[Kumulativní aktualizace 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a zprostředkovatelů obnovení webu (jak je podrobně popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu).

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro RedHat Workstation 6/7 a nové verze jádra pro Ubuntu, Debian a SUSE.
**Prostory úložiště – přímé** | Site Recovery podporuje virtuální počítače Azure pomocí storage spaces direct (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Obnovení po havárii virtuálních zařízení v systému VMware nebo fyzických serverů

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro Redhat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10 a Oracle Linux 7.6 a nové verze jádra pro Ubuntu, Debian a SUSE.


### <a name="update-rollup-31"></a>Kumulativní aktualizace 31

[Kumulativní aktualizace 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a zprostředkovatelů obnovení webu (jak je podrobně popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu).

### <a name="vmware-vmsphysical-servers-replication"></a>Replikace virtuálních zařízení v systému VMware nebo fyzických serverů

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** |  Byla přidána podpora pro Oracle Linux 6.8, Oracle Linux 6.9 a Oracle Linux 7.0 s jádrem kompatibilním s Red Hat a pro nerozbitné podnikové jádro (UEK) Release 5.
**Lvm** | Přidána podpora pro svazky LVM a LVM2.<br/><br/> Adresář /boot na disku oddílu a na svazcích LVM je nyní podporován.
**Adresáře** | Podpora byla přidána pro tyto adresáře nastavené jako samostatné oddíly nebo systémy souborů, které nejsou na stejném systémovém disku:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Byla přidána podpora dynamických disků.
**Zabezpečení před selháním** | Vylepšená doba převzetí služeb při selhání pro virtuální zařízení VMware, kde storvsc a vsbus nejsou spouštěcí mise.
**Podpora pro UEFI** | Virtuální počítače Azure nepodporují typ spouštění UEFI. Teď můžete migrovat místní fyzické servery s UEFI do Azure pomocí site recovery. Program Site Recovery migruje server převedením typu spouštění do systému BIOS před migrací. Site Recovery dříve podporoval tento převod pouze pro virtuální chod. Podpora je k dispozici pro fyzické servery se systémem Windows Server 2012 nebo novějším.

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro Oracle Linux 6.8, Oracle Linux 6.9 a Oracle Linux 7.0 s jádrem kompatibilním s Red Hat a pro nerozbitné podnikové jádro (UEK) Release 5.
**Souborový systém Linux BRTFS** | Podporované pro virtuální počítače Azure.
**Virtuální počítače Azure v zónách dostupnosti** | Můžete povolit replikaci do jiné oblasti pro virtuální počítače Azure nasazené v zónách dostupnosti. Teď můžete povolit replikaci pro virtuální počítač Azure a nastavit cíl pro převzetí služeb při selhání na jednu instanci virtuálního počítače, virtuální počítač v sadě dostupnosti nebo virtuální počítač v zóně dostupnosti. Nastavení nemá vliv na replikaci. [Přečtěte si](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) oznámení.
**Úložiště s podporou brány firewall (portál/Prostředí PowerShell)** | Byla přidána podpora pro [účty úložiště s povolenou bránou firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Virtuální počítače Azure můžete replikovat s nespravovanými disky na účtech úložiště s podporou brány firewall do jiné oblasti Azure pro zotavení po havárii.<br/><br/> Účty úložiště s povolenou bránou firewall můžete použít jako cílové účty úložiště pro nespravované disky.<br/><br/> Podporováno na portálu a pomocí PowerShellu.

## <a name="updates-december-2018"></a>Aktualizace (prosinec 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatické aktualizace pro službu Mobility (virtuální počítače Azure)

Site Recovery přidal možnost automatických aktualizací rozšíření služby Mobility. Rozšíření služby Mobility se nainstaluje na každém virtuálním počítači Azure replikovaném pomocí site recovery. Když povolíte replikaci, vyberete, zda chcete povolit obnovení sítě ke správě aktualizací rozšíření.

Aktualizace nevyžadují restartování virtuálního počítače a nemají vliv na replikaci. [Další informace](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Cenová kalkulačka pro zotavení po havárii virtuálního počítače Azure

Zotavení po havárii virtuálních počítačích Azure nese náklady na licencování virtuálních počítače a náklady na síť a úložiště. Azure poskytuje [cenovou kalkulačku,](https://aka.ms/a2a-cost-estimator) která vám pomůže zjistit tyto náklady. Site Recovery nyní poskytuje [příklad odhadcen,](https://aka.ms/a2a-cost-estimator) že ceny ukázkové nasazení na základě třívrstvé aplikace pomocí šesti virtuálních počítačů s 12 disky standard HDD a 6 Premium SSD disky.

- Ukázka předpokládá změnu dat 10 GB denně pro standard a 20 GB pro prémii.
- Pro konkrétní nasazení můžete změnit proměnné odhadnout náklady.
- Můžete určit počet virtuálních počítačů, počet a typ spravovaných disků a očekávanou celkovou rychlost změny dat očekávanou napříč virtuálními počítačemi.
- Kromě toho můžete použít kompresní faktor pro odhad nákladů na šířku pásma.

[Přečtěte si](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) oznámení.


## <a name="updates-october-2018"></a>Aktualizace (říjen 2018)

### <a name="update-rollup-30"></a>Kumulativní aktualizace 30

[Kumulativní aktualizace 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a zprostředkovatelů obnovení webu (jak je podrobně popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu).

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure
Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora regionu** | Přidána podpora site recovery pro austrálie – střed 1 a austrálie – střed 2.
**Podpora šifrování disku** | Podpora přidána pro zotavení po havárii virtuálních počítačů Azure šifrované pomocí Azure Disk Encryption (ADE) s aplikací Azure AD. [Další informace](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Vyloučení disku** | Neinicializované disky se teď automaticky vyloučí během replikace virtuálních počítačů Azure.
**Úložiště s podporou brány firewall (PowerShell)** | Byla přidána podpora pro [účty úložiště s povolenou bránou firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Virtuální počítače Azure můžete replikovat s nespravovanými disky na účtech úložiště s podporou brány firewall do jiné oblasti Azure pro zotavení po havárii.<br/><br/> Účty úložiště s povolenou bránou firewall můžete použít jako cílové účty úložiště pro nespravované disky.<br/><br/> Podporováno pouze pomocí PowerShellu.


### <a name="update-rollup-29"></a>Kumulativní aktualizace 29

[Kumulativní aktualizace 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a zprostředkovatelů obnovení webu (jak je podrobně popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu).


## <a name="updates-august-2018"></a>Aktualizace (srpen 2018)

### <a name="update-rollup-28"></a>Kumulativní aktualizace 28

[Kumulativní aktualizace 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a zprostředkovatelů obnovení webu (jak je podrobně popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu).

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure
Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidáno podporováno pro RedHat Enterprise Linux 6.10; Centos 6,10.<br/><br/>
**Podpora cloudu** | Podporované zotavení po havárii pro virtuální počítače Azure v německém cloudu.
**Zotavení po havárii napříč odběry** | Podpora replikace virtuálních počítačů Azure v jedné oblasti do jiné oblasti v rámci jiného předplatného v rámci stejného klienta Azure Active Directory. [Další informace](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Obnovení zabezpečení virtuálního připojení virtuálního připojení v systému VMware/fyzického serveru
Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidána podpora pro RedHat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Virtuální počítače založené na Linuxu, které používají styl oddílu GUID (GPT) ve starším režimu kompatibility systému BIOS, jsou teď podporované. Další informace najděte v [nejčastějších dotazech](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) k virtuálnímu počítači Azure.
**Zotavení po havárii pro virtuální chod po migraci** | Podpora povolení zotavení po havárii do sekundární oblasti pro místní virtuální počítač VMware migroval do Azure, aniž by bylo nutné odinstalovat službu mobility na virtuálním počítači před povolením replikace.
**Windows Server 2008** | Podpora 64bitových a 32bitových počítačů se systémem Windows Server 2008 R2/2008.<br/><br/> Pouze migrace (replikace a převzetí služeb při selhání). Navrácení služeb po selhání není podporováno.

## <a name="updates-july-2018"></a>Aktualizace (červenec 2018)

### <a name="update-rollup-27-july-2018"></a>Kumulativní aktualizace 27 (červenec 2018)

[Kumulativní aktualizace 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů a zprostředkovatelů obnovení webu (jak je podrobně popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je podrobně popsáno v souhrnu).

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidána podpora pro Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Obnovení zabezpečení virtuálního připojení virtuálního připojení v systému VMware/fyzického serveru

Funkce přidané tento měsíc jsou shrnuty v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidána podpora pro Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Další kroky

Udržujte si aktuální informace o našich aktualizacích na stránce [Aktualizace Azure.](https://azure.microsoft.com/updates/?product=site-recovery)
