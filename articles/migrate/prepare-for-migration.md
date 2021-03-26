---
title: Příprava počítačů na migraci pomocí Azure Migrate
description: Přečtěte si, jak připravit místní počítače na migraci pomocí Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: d8f9d4e0b002348f286f45c6b45c96531c5d6530
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558223"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Příprava místních počítačů na migraci do Azure

Tento článek popisuje, jak připravit místní počítače před jejich migrací do Azure pomocí nástroje [Azure Migrate: Server pro migraci](migrate-services-overview.md#azure-migrate-server-migration-tool) .

V tomto článku:
> [!div class="checklist"]
> * Zkontrolujte omezení migrace.
> * Vyberte metodu pro migraci virtuálních počítačů VMware.
> * Ověřte požadavky hypervisoru a operačního systému pro počítače, které chcete migrovat.
> * Zkontrolujte přístup k adrese URL a portu pro počítače, které chcete migrovat.
> * Zkontrolujte změny, které může být třeba provést před zahájením migrace.
> * Ověřit požadavky na virtuální počítače Azure pro migrované počítače
> * Připravte počítače, abyste se mohli po migraci připojit k virtuálním počítačům Azure.



## <a name="verify-migration-limitations"></a>Ověřit omezení migrace

Tabulka shrnuje omezení zjišťování, hodnocení a migrace pro Azure Migrate. Doporučujeme, abyste počítače vyhodnotili před migrací, ale nemusíte je.

**Scénář** | **Projekt** | **Zjišťování/posouzení** | **Migrace**
--- | --- | --- | ---
**Virtuální počítače VMware** | Objevte a posuďte až 35 000 virtuálních počítačů v jednom Azure Migrate projektu. | Zjistěte, jak 10 000 virtuálních počítačů VMware s jedním [zařízením Azure Migrate](common-questions-appliance.md) pro VMware. | **Migrace bez agenta**: souběžně můžete z každého vCenter Server replikovat maximálně 500 virtuálních počítačů. **Migrace založená na agentovi**: [zařízení replikace](migrate-replication-appliance.md) můžete [škálovat](./agent-based-migration-architecture.md#performance-and-scaling) pro replikaci velkého počtu virtuálních počítačů.<br/><br/> Na portálu můžete pro replikaci vybrat až 10 počítačů najednou. Pokud chcete replikovat více počítačů, přidejte je do dávek po 10.
**Virtuální počítače Hyper-V** | Objevte a posuďte až 35 000 virtuálních počítačů v jednom Azure Migrate projektu. | Zjištění až 5 000 virtuálních počítačů Hyper-V s jedním zařízením Azure Migrate | Zařízení se nepoužívá pro migraci technologie Hyper-V. Místo toho se Zprostředkovatel replikace technologie Hyper-V spustí na každém hostiteli Hyper-V.<br/><br/> Kapacita replikace je ovlivněná faktory výkonu, jako jsou třeba změny virtuálních počítačů, a nahrává šířku pásma pro data replikace.<br/><br/> Na portálu můžete pro replikaci vybrat až 10 počítačů najednou. Pokud chcete replikovat více počítačů, přidejte je do dávek po 10.
**Fyzický počítač** | Objevte a posuďte až 35 000 počítačů v jednom Azure Migrate projektu. | Pro fyzické servery si můžete najít až 250 fyzických serverů s jedním zařízením Azure Migrate. | Kapacitu [zařízení replikace](migrate-replication-appliance.md) můžete [škálovat](./agent-based-migration-architecture.md#performance-and-scaling) a replikovat tak velký počet serverů.<br/><br/> Na portálu můžete pro replikaci vybrat až 10 počítačů najednou. Pokud chcete replikovat více počítačů, přidejte je do dávek po 10.

## <a name="select-a-vmware-migration-method"></a>Vyberte metodu migrace VMware.

Pokud migrujete virtuální počítače VMware do Azure, [Porovnejte](server-migrate-overview.md#compare-migration-methods) metody migrace bez agentů a na základě agentů a rozhodněte se, co pro vás funguje.

## <a name="verify-hypervisor-requirements"></a>Ověřit požadavky hypervisoru

- Ověřte požadavky bez [agentů VMware](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)nebo [založené na agentech VMware](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) .
- Ověřte požadavky na [hostitele Hyper-V](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) .


## <a name="verify-operating-system-requirements"></a>Ověřit požadavky na operační systém

Ověření podporovaných operačních systémů pro migraci:

- Pokud migrujete virtuální počítače VMware nebo virtuální počítače Hyper-V, ověřte požadavky na virtuální počítače VMware bez [agentů](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)a migraci [na základě agenta](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) a požadavky pro [virtuální počítače Hyper-V](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms).
- Ověřte, jestli jsou [operační systémy Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) podporované v Azure.
- Ověřte [distribuce systému Linux](../virtual-machines/linux/endorsed-distros.md) podporované v Azure.

## <a name="review-url-and-port-access"></a>Zkontrolovat přístup k adrese URL a portu

Zkontrolujte, které adresy URL a porty jsou k dispozici během migrace.

**Scénář** | **Podrobnosti** |  **Adresy URL** | **Porty**
--- | --- | --- | ---
**Migrace bez agentů VMware** | K migraci používá [zařízení Azure Migrate](migrate-appliance-architecture.md) . Na virtuálních počítačích VMware není nic nainstalované. | Projděte si veřejné cloudy a [adresy URL](migrate-appliance.md#url-access) pro státní správu, které jsou potřeba pro zjišťování, posuzování a migraci pomocí zařízení. | [Zkontrolujte](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) požadavky na porty pro migraci bez agenta.
**Migrace založená na agentech VMware** | K migraci používá [replikační zařízení](migrate-replication-appliance.md) . Agent služby mobility je nainstalovaný na virtuálních počítačích. | Projděte si [veřejné cloudy](migrate-replication-appliance.md#url-access) a [Azure Government](migrate-replication-appliance.md#azure-government-url-access) adresy URL, ke kterým musí mít zařízení replikace přístup. | [Zkontrolujte](migrate-replication-appliance.md#port-access) porty používané při migraci na základě agenta.
**Migrace Hyper-V** | Používá k migraci zprostředkovatele nainstalovaného na hostitelích Hyper-V. Na virtuálních počítačích Hyper-V není nic nainstalované. | Projděte si [veřejné cloudy](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) a [Azure Government](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) adresy URL, které poskytovatel replikace běžící na hostitelích potřebuje k přístupu. | Zprostředkovatel replikace na hostiteli Hyper-V používá odchozí připojení na portu HTTPS 443 k odesílání dat replikace virtuálních počítačů.
**Fyzický počítač** | K migraci používá [replikační zařízení](migrate-replication-appliance.md) . Agent služby mobility je nainstalovaný na fyzických počítačích. | Projděte si [veřejné cloudy](migrate-replication-appliance.md#url-access) a [Azure Government](migrate-replication-appliance.md#azure-government-url-access) adresy URL, ke kterým musí mít zařízení replikace přístup. | [Zkontrolujte](migrate-replication-appliance.md#port-access) porty používané při fyzické migraci.

## <a name="verify-required-changes-before-migrating"></a>Před migrací ověřit požadované změny

Před migrací do Azure jsou na virtuálních počítačích potřeba nějaké změny.

- U některých operačních systémů Azure Migrate provádí změny automaticky během procesu replikace nebo migrace.
- Pro jiné operační systémy musíte nastavení nakonfigurovat ručně.
- Před zahájením migrace je důležité nakonfigurovat nastavení ručně. Pokud před provedením změny migrujete virtuální počítač, nemusí se virtuální počítač spustit v Azure.

Zkontrolujte tabulky a Identifikujte změny, které je třeba provést.

### <a name="windows-machines"></a>Počítače s Windows

Požadované změny jsou shrnuty v tabulce.

**Akce** | **VMware (migrace bez agenta)** | **VMware (založený na agentech)/Physical počítače** | **Windows v Hyper-V** 
--- | --- | --- | ---
**Konfigurace zásad sítě SAN jako všech online**<br/><br/> Tím se zajistí, že svazky Windows na virtuálním počítači Azure budou používat stejná přiřazení písmen jednotek jako místní virtuální počítač. | Nastaví se automaticky pro počítače se systémem Windows Server 2008 R2 nebo novějším.<br/><br/> Nakonfigurujte ručně pro starší operační systémy. | Nastaví se automaticky ve většině případů. | Nakonfigurujte ručně.
**Nainstalovat integraci hosta technologie Hyper-V** | [Ruční instalace](prepare-windows-server-2003-migration.md#install-on-vmware-vms) na počítačích se systémem Windows Server 2003. | [Ruční instalace](prepare-windows-server-2003-migration.md#install-on-vmware-vms) na počítačích se systémem Windows Server 2003. | [Ruční instalace](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) na počítačích se systémem Windows Server 2003.
**Povolte službu Azure Serial Console**.<br/><br/>Pro pomoc s řešením potíží [Povolte konzolu](/troubleshoot/azure/virtual-machines/serial-console-windows) na virtuálních počítačích Azure. Nemusíte restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí image disku. Spuštění bitové kopie disku je ekvivalentní restartování nového virtuálního počítače. | Povolit ručně | Povolit ručně | Povolit ručně
**Připojit po migraci**<br/><br/> Pokud se chcete po migraci připojit, je potřeba provést několik kroků, než migrujete. | [Nastavte](#prepare-to-connect-to-azure-windows-vms) ručně. | [Nastavte](#prepare-to-connect-to-azure-windows-vms) ručně. | [Nastavte](#prepare-to-connect-to-azure-windows-vms) ručně.


#### <a name="configure-san-policy"></a>Konfigurace zásad sítě SAN

Ve výchozím nastavení jsou virtuálním počítačům Azure přiřazená jednotka D pro použití jako dočasné úložiště.

- Přiřazení této jednotky způsobí, že se všechna ostatní připojená přiřazení jednotek úložiště zvýší o jedno písmeno.
- Například pokud vaše místní instalace používá datový disk, který je přiřazený k jednotce D pro instalace aplikací, přiřazení této jednotky se po migraci virtuálního počítače do Azure zvýší na jednotku E. 
- Abyste zabránili tomuto automatickému přiřazení a zajistili, že Azure přiřadí k dočasnému svazku další písmeno volné jednotky, nastavte zásady sítě SAN (Storage Area Network) na **OnlineAll**:

Nakonfigurujte toto nastavení ručně následujícím způsobem:

1. Na místním počítači (ne na hostitelském serveru) otevřete příkazový řádek se zvýšenými oprávněními.
2. Zadejte **DiskPart**.
3. Zadejte **San**. Pokud není zachováno písmeno jednotky hostovaného operačního systému,  vrátí se offline **sdílená nebo offline** .
4. V příkazovém řádku **nástroje DiskPart** zadejte **zásady sítě San = OnlineAll**. Toto nastavení zajistí, že se disky budou do režimu online, a zajistíte tak čtení a zápis na oba disky.
5. Během migrace testu můžete ověřit, že jsou písmena jednotek zachovaná.


### <a name="linux-machines"></a>Počítače se systémem Linux

Azure Migrate tyto akce automaticky dokončí pro tyto verze

- Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x (agent virtuálního počítače Azure Linux se taky nainstaluje automaticky během migrace)
- Cent OS 7,7, 7,6, 7,5, 7,4, 6. x (agent virtuálního počítače Azure Linux se taky nainstaluje automaticky během migrace)
- SUSE Linux Enterprise Server 12 SP1 +
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 19,04, 19,10, 18.04 LTS, 16.04 LTS, 14.04 LTS (agent virtuálního počítače Azure Linux se taky nainstaluje automaticky během migrace)
- Ubuntu 18.04 LTS, 16.04 LTS
- Debian 9, 8, 7
- Oracle Linux 7,7, 7,7-CI

Pro jiné verze připravte počítače podle souhrnu v tabulce.  


**Akce** | **Podrobnosti** | **Verze systému Linux**
--- | --- | ---
**Nainstalovat integrační služby Hyper-V Linux** | Znovu sestavte inicializační image Linux, aby obsahovala potřebné ovladače technologie Hyper-V. Když znovu sestavíte inicializační image, zajistíte, že se virtuální počítač spustí v Azure. | Ve výchozím nastavení je k dispozici většina nových verzí distribucí systému Linux.<br/><br/> Pokud není zahrnutý, nainstalujte je ručně pro všechny verze kromě těch, které jsou uvedeny výše.
**Povolit protokolování na sériové konzole Azure** | Povolení protokolování konzoly vám pomůže při odstraňování potíží. Nemusíte restartovat virtuální počítač. Virtuální počítač Azure se spustí pomocí image disku. Spuštění bitové kopie disku je ekvivalentní restartování nového virtuálního počítače.<br/><br/> Pokud chcete povolit, postupujte podle [těchto pokynů](/troubleshoot/azure/virtual-machines/serial-console-linux) .
**Aktualizovat soubor mapování zařízení** | Aktualizujte soubor mapování zařízení pomocí přidružení typu název zařízení-svazek, aby se používaly trvalé identifikátory zařízení. | Nainstalujte ručně pro všechny verze kromě těch, které jsou uvedené výše. (Dá se použít jenom ve scénáři VMware založeného na agentech)
**Aktualizovat položky fstab** |  Aktualizujte položky tak, aby používaly trvalé identifikátory svazků.    | Aktualizovat ručně pro všechny verze kromě těch, které jsou vyvolány výše.
**Odebrat pravidlo udev** | Odeberte všechna udev pravidla, která vyhradí názvy rozhraní na základě adresy Mac atd. | Odeberte ručně pro všechny verze kromě těch, které jsou vyvolány výše.
**Aktualizace síťových rozhraní** | Aktualizace síťových rozhraní pro příjem IP adres na základě protokolu DHCP. NST | Aktualizovat ručně pro všechny verze kromě těch, které jsou vyvolány výše.
**Povolit SSH** | Ujistěte se, že je povolený protokol SSH a služba sshd je nastavená tak, aby se automaticky spustila při restartování.<br/><br/> Zajistěte, aby příchozí požadavky na připojení SSH nebyly blokované pomocí brány firewall operačního systému nebo pravidel pro skriptování.| Povolte ruční povolení pro všechny verze kromě těch, které jsou uvedené výše.

Následující tabulka shrnuje kroky prováděné automaticky pro operační systémy uvedené výše.


| Akce                                      | \-Migrace VMware založená na agentech | Migrace VMware bez agentů | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Nainstalovat \- integrační služby Hyper-V Linux | Yes                           | Yes                        | Není potřeba |
| Povolit protokolování na sériové konzole Azure         | Yes                           | Yes                        | No         |
| Aktualizovat soubor mapování zařízení                      | Yes                           | No                         | No         |
| Aktualizovat položky fstab                        | Yes                           | Yes                        | No         |
| Odebrat pravidlo udev                            | Yes                           | Yes                        | No         |
| Aktualizace síťových rozhraní                   | Yes                           | Yes                        | No         |
| Povolit SSH                                  | No                            | No                         | No         |

Přečtěte si další informace o postupu při [spuštění virtuálního počítače se systémem Linux v Azure](../virtual-machines/linux/create-upload-generic.md)a pokyny pro některé z oblíbených distribucí pro Linux.

Projděte si seznam [požadovaných balíčků](../virtual-machines/extensions/agent-linux.md#requirements) pro instalaci agenta virtuálního počítače se systémem Linux. Azure Migrate automaticky nainstaluje agenta virtuálního počítače se systémem Linux pro počítače RHEL6, RHEL7, CentOS7 (6 by se měla podporovat podobně jako RHEL), Ubuntu 14,04, Ubuntu 16,04 a Ubuntu 18.04 při použití metody migrace VMware bez agenta.

## <a name="check-azure-vm-requirements"></a>Ověřit požadavky na virtuální počítače Azure

Místní počítače, které se replikují do Azure, musí splňovat požadavky na virtuální počítače Azure pro operační systém a architekturu, disky, nastavení sítě a pojmenovávání virtuálních počítačů.

Před migrací si přečtěte požadavky na virtuální počítače Azure pro migraci [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)a [fyzického serveru](migrate-support-matrix-physical-migration.md#azure-vm-requirements) .



## <a name="prepare-to-connect-after-migration"></a>Příprava na připojení po migraci

Virtuální počítače Azure se vytváří během migrace do Azure. Po dokončení migrace musíte být schopni se připojit k novým virtuálním počítačům Azure. K úspěšnému připojení se vyžaduje více kroků.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Příprava na připojení k virtuálním počítačům Azure s Windows

Na místních počítačích s Windows:

1. Nakonfigurujte nastavení systému Windows. Mezi tato nastavení patří odebrání statických trvalých tras nebo proxy WinHTTP.
2. Ujistěte se, že jsou spuštěné [požadované služby](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) .
3. Pokud chcete povolit vzdálená připojení k místnímu počítači, povolte vzdálenou plochu (RDP). Naučte [se používat prostředí PowerShell k povolení protokolu RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
4. Pokud po migraci chcete získat přístup k virtuálnímu počítači Azure přes Internet, v bráně Windows Firewall na místním počítači Povolte protokol TCP a UDP ve veřejném profilu a nastavte RDP jako povolenou aplikaci pro všechny profily.
5. Pokud chcete po migraci získat přístup k virtuálnímu počítači Azure přes síť VPN typu Site-to-site, v bráně Windows Firewall na místním počítači Povolte protokol RDP pro doménu a privátní profily. Naučte se, jak [povolíte provoz protokolu RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules). 
6. Ujistěte se, že při migraci nečekají žádné aktualizace Windows na místním virtuálním počítači. V takovém případě můžou aktualizace po migraci začít instalovat na virtuálním počítači Azure a až do dokončení aktualizace se nebudete moct přihlásit k virtuálnímu počítači.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Příprava na připojení k virtuálním počítačům Azure se systémem Linux

Na místních počítačích se systémem Linux:

1. Ověřte, že je služba Secure Shell nastavená tak, aby se spouštěla automaticky při spuštění systému.
2. Zkontrolujte, že pravidla brány firewall umožňují připojení SSH.

### <a name="configure-azure-vms-after-migration"></a>Konfigurace virtuálních počítačů Azure po migraci

Po dokončení migrace proveďte tyto kroky na virtuálních počítačích Azure, které se vytvoří:

1. Pokud se chcete připojit k virtuálnímu počítači přes Internet, přiřaďte virtuálnímu počítači veřejnou IP adresu. Pro virtuální počítač Azure je nutné použít jinou veřejnou IP adresu, než jste použili pro místní počítač. [Další informace](../virtual-network/virtual-network-public-ip-address.md).
2. Ověřte, že pravidla skupiny zabezpečení sítě (NSG) na virtuálním počítači povolují příchozí připojení k portu RDP nebo SSH.
3. Zkontrolujte [diagnostiku spouštění](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) a zobrazte si virtuální počítač.


## <a name="next-steps"></a>Další kroky

Rozhodněte, kterou metodu chcete použít k [migraci virtuálních počítačů VMware](server-migrate-overview.md) do Azure, nebo zahajte migraci [virtuálních počítačů Hyper-V](tutorial-migrate-hyper-v.md) nebo [fyzických serverů nebo virtualizovaných nebo cloudových virtuálních počítačů](tutorial-migrate-physical-virtual-machines.md).

## <a name="see-whats-supported"></a>Podívejte se, co je podporováno

U virtuálních počítačů VMware podporuje migrace serverů bez [agentů nebo migraci na základě agenta](server-migrate-overview.md).

- **Virtuální počítače VMware**: Ověřte [požadavky na migraci a podporu](migrate-support-matrix-vmware-migration.md) pro virtuální počítače VMware.
- **Virtuální počítače Hyper-v**: Ověřte [požadavky na migraci a podporu](migrate-support-matrix-hyper-v-migration.md) pro virtuální počítače Hyper-v.
- **Fyzické počítače**: Ověřte [požadavky na migraci a podporu](migrate-support-matrix-physical-migration.md) místních fyzických počítačů a dalších virtualizovaných serverů.