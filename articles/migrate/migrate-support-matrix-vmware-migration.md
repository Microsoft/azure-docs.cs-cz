---
title: Podpora migrace VMware v Azure Migrate
description: Přečtěte si o podpoře migrace virtuálních počítačů VMware v Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: eee16b244ae4f9d517bdd42a0b7f37b1494ac480
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538133"
---
# <a name="support-matrix-for-vmware-migration"></a>Matice podpory pro migraci voblasti VMware

Tento článek shrnuje nastavení podpory a omezení pro migraci virtuálních počítačů VMware s [migrací Azure: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) . Pokud hledáte informace o hodnocení virtuálních počítačů VMware pro migraci do Azure, přečtěte si [matici podpory hodnocení](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Možnosti migrace

Virtuální mame v mware můžete migrovat několika způsoby:

- S migrací bez agenta: Migrujte virtuální počítače bez nutnosti instalace nic na ně. Nasadíte [zařízení Azure Migrate](migrate-appliance.md) pro migraci bez agenta.
- S migrací na základě agenta: Nainstalujte agenta na virtuální počítač pro replikaci. Pro migraci založenou na agentovi je třeba nasadit [zařízení replikace](migrate-replication-appliance.md).

Projděte si [tento článek](server-migrate-overview.md) a zjistěte, kterou metodu chcete použít.

## <a name="migration-limitations"></a>Omezení migrace

- Můžete vybrat až 10 virtuálních můzí najednou pro replikaci. Pokud chcete migrovat více počítačů, replikujte ve skupinách po 10.
- U migrace bez agentů společnosti VMware můžete spustit až 100 replikací současně.

## <a name="agentless-vmware-servers"></a>Servery Agentless-VMware

**Vmware** | **Podrobnosti**
--- | ---
**VMware vCenter Server** | Verze 5.5, 6.0, 6.5 nebo 6.7.
**Hostitel VMware vSphere ESXI** | Verze 5.5, 6.0, 6.5 nebo 6.7.
**Oprávnění serveru vCenter** | Migrace bez agenta používá [migrate Appliance](migrate-appliance.md). Zařízení potřebuje tato oprávnění:<br/><br/> - **Datastore.Browse**: Povolit procházení souborů protokolu virtuálního počítače k řešení potíží s vytvořením a odstraněním snímku.<br/><br/> - **Datastore.LowLevelFileOperations**: Povolit operace čtení/zápisu/odstranění/přejmenování v prohlížeči úložiště dat, aby bylo možné vyřešit vytvoření a odstranění snímku.<br/><br/> - **VirtualMachine.Configuration.DiskChangeTracking**: Povolit povolení nebo zakázání sledování změn disků virtuálních počítačů, chcete-li vytáhnout změněné bloky dat mezi snímky.<br/><br/> - **VirtualMachine.Configuration.DiskLease**: Povolit operace zapůjčení disku pro virtuální počítač, číst disk pomocí VMware vSphere Virtual Disk Development Kit (VDDK).<br/><br/> - **VirtualMachine.Provisioning.AllowDiskAccess**: (konkrétně pro vSphere 6.0 a vyšší) Povolit otevření disku na virtuálním počítači pro náhodný přístup ke čtení na disku pomocí VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowReadOnlyDiskAccess**: Povolit otevření disku na virtuálním počítači, chcete-li číst disk pomocí vddk.<br/><br/> - **VirtualMachine.Provisioning.AllowDiskRandomAccess**: Povolit otevření disku na virtuálním počítači, chcete-li číst disk pomocí sady VDDK.<br/><br/> - **VirtualMachine.Provisioning.AllowVirtualMachineDownload**: Umožňuje operace čtení souborů přidružených k virtuálnímu počítači, ke stažení protokolů a řešení potíží, pokud dojde k selhání.<br/><br/> - **VirtualMachine.SnapshotManagement.***: Povolit vytváření a správu snímků virtuálních zařízení pro replikaci.<br/><br/> - **Virtual Machine.Interaction.Power Off**: Povolit, aby se virtuální počítač vypnul během migrace do Azure.



## <a name="agentless-vmware-vms"></a>Virtuální měna Bez agenta-VMware

**Podpora** | **Podrobnosti**
--- | ---
**Podporované operační systémy** | [Operační](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) systémy Windows a [Linux,](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) které Azure podporuje, lze migrovat pomocí migrace bez agentů.
**Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby je můžou běžet v Azure. Azure Migrate provádí tyto změny automaticky pro následující operační systémy:<br/> - Red Hat Enterprise Linux 6.5+, 7.0+<br/> - Centos 6,5+, 7,0+</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8<br/><br/> U jiných operačních systémů je třeba provést před migrací úpravy ručně. Příslušné články obsahují pokyny, jak to udělat.
**Linux boot** | Pokud /boot je na vyhrazeném oddílu, by měl být umístěn na disku operačního systému a nesmí být rozloženy na více disků.<br/> Pokud /boot je součástí kořenového (/) oddílu, pak '/' oddíl by měl být na disku operačního systému a ne span jiné disky.
**UEFI bota** | Virtuální aplikace se spouštěním UEFI nejsou pro migraci podporované.
**Velikost disku** | 2 TB OS disk; 8 TB pro datové disky.
**Omezení disku** |  Až 60 disků na virtuální hod.
**Šifrované disky/svazky** | Virtuální počítače se šifrovanými disky/svazky nejsou pro migraci podporované.
**Cluster sdíleného disku** | Není podporováno.
**Nezávislé disky** | Není podporováno.
**Disky RDM/passthroughthrough** | Pokud virtuální počítače mají RDM nebo průchod disky, tyto disky se nebudou replikovat do Azure.
**NFS** | Svazky nfs připojené jako svazky na virtuálních počítačích nebudou replikovány.
**Cíle iSCSI** | Virtuální aplikace s cíli iSCSI nejsou podporovány pro migraci bez agenta.
**Vícecestné VO** | Není podporováno.
**Úložiště vMotion** | Není podporováno. Replikace nebude fungovat, pokud virtuální montovana používá úložiště vMotion.
**Síťové karty pro spojení** | Není podporováno.
**IPv6** | Není podporováno.
**Cílový disk** | Virtuální počítače se můžou migrovat jenom na spravované disky (standardní HDD, premium SSD) v Azure.
**Současná replikace** | 100 virtuálních připojení na server vCenter. Pokud máte více, migrujte je v dávkách 100.


## <a name="agentless-azure-migrate-appliance"></a>Zařízení migrace Bez agenta AZUA 

Migrace bez agenta používá [zařízení Azure Migrate](migrate-appliance.md). Zařízení můžete nasadit jako virtuální hod VMWare pomocí šablony OVA, importované na server vCenter nebo pomocí [skriptu prostředí PowerShell](deploy-appliance-script.md).

- Seznamte se s [požadavky na zařízení](migrate-appliance.md#appliance---vmware) pro společnost VMware.
- Přečtěte si o adresách URL, ke kterým zařízení potřebuje přístup ve [veřejných](migrate-appliance.md#public-cloud-urls) a [vládních](migrate-appliance.md#government-cloud-urls) cloudech.
- Ve službě Azure Government je nutné nasadit zařízení pomocí skriptu.

## <a name="agentless-ports"></a>Porty bez agentů

**Zařízení** | **Připojení**
--- | ---
Přístroj | Odchozí připojení na portu 443 pro nahrání replikovaných dat do Azure a pro komunikaci se službami Azure Migrate, které orchestrují replikaci a migraci.
Server vCenter | Příchozí připojení na portu 443, která umožňují zařízení organizovat replikaci – vytvářet snímky, kopírovat data, uvolňovat snímky
hostitel vSphere/EXSI | Příchozí na portu TCP 902 pro zařízení replikovat data ze snímků.


## <a name="agent-based-vmware-servers"></a>Servery VMware založené na agentech
Tato tabulka shrnuje podporu hodnocení a omezení virtualizačních serverů VMware.

**Požadavky na vmware** | **Podrobnosti**
--- | ---
**VMware vCenter Server** | Verze 5.5, 6.0, 6.5 nebo 6.7.
**Hostitel VMware vSphere ESXI** | Verze 5.5, 6.0, 6.5 nebo 6.7.
**Oprávnění serveru vCenter** | Účet jen pro čtení pro server vCenter.

## <a name="agent-based-vmware-vms"></a>Virtuální virtuální měna VMware založená na agentech

Tabulka shrnuje podporu virtuálních monů VMware pro virtuální vmvware VMware, které chcete migrovat pomocí migrace založené na agentovi.

**Podpora** | **Podrobnosti**
--- | ---
**Pracovní vytížení počítače** | Migrace Azure podporuje migraci všech úloh (například služby Active Directory, SQL server atd.) spuštěných na podporovaném počítači.
**Operační systémy** | Nejnovější informace naleznete v [podpoře operačního systému](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pro site recovery. Azure Migrate poskytuje podporu operačního systému identického virtuálního počítače.
**Linux souborový systém / hostovací úložiště** | Nejnovější informace naleznete v [podpoře systému souborů Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pro site recovery. Azure Migrate má identickou podporu systému souborů Linux.
**Síť/úložiště** | Nejnovější informace naleznete v požadavcích [sítě](../site-recovery/vmware-physical-azure-support-matrix.md#network) a [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pro site recovery. Azure Migrate poskytuje stejné požadavky na síť/úložiště.
**Požadavky na Azure** | Nejnovější informace najděte požadavky na [síť Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)a [výpočetní prostředky](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) pro site recovery. Azure Migrate má stejné požadavky na migraci VMware.
**Služba Mobility** | Agent služby Mobility musí být nainstalován na každém virtuálním počítači, který chcete migrovat.
**UEFI bota** | Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač systému BIOS.<br/><br/> Disk operačního systému by měl mít až čtyři oddíly a svazky by měly být formátovány pomocí systému souborů NTFS.
**Cílový disk** | Virtuální počítače se můžou migrovat jenom na spravované disky (standardní HDD, premium SSD) v Azure.
**Velikost disku** | 2 TB OS disk; 8 TB pro datové disky.
**Omezení disku** |  Až 63 disků na virtuální počítače.
**Šifrované disky/svazky** | Virtuální počítače se šifrovanými disky/svazky nejsou pro migraci podporované.
**Cluster sdíleného disku** | Není podporováno.
**Nezávislé disky** | Podporuje se.
**Průchozí disky** | Podporuje se.
**NFS** | Svazky nfs připojené jako svazky na virtuálních počítačích nebudou replikovány.
**Cíle iSCSI** | Virtuální aplikace s cíli iSCSI nejsou podporovány pro migraci bez agenta.
**Vícecestné VO** | Není podporováno.
**Úložiště vMotion** | Podporuje se
**Síťové karty pro spojení** | Není podporováno.
**IPv6** | Není podporováno.




## <a name="agent-based-replication-appliance"></a>Zařízení replikace založené na agentovi 

Když nastavíte zařízení pro replikaci pomocí šablony OVA uvedené v centru Migrace Azure, zařízení spustí Windows Server 2016 a splňuje požadavky na podporu. Pokud zařízení replikace nastavíte ručně na fyzickém serveru, ujistěte se, že splňuje požadavky.

- Seznamte se s [požadavky na zařízení replikace](migrate-replication-appliance.md#appliance-requirements) pro společnost VMware.
- MySQL musí být nainstalován na zařízení. Informace o [možnostech instalace](migrate-replication-appliance.md#mysql-installation).
- Přečtěte si o adresách URL, ke kterým musí replikační zařízení přistupovat ve [veřejných](migrate-replication-appliance.md#url-access) a [vládních](migrate-replication-appliance.md#azure-government-url-access) cloudech.
- Zkontrolujte [porty,](migrate-replication-appliance.md#port-access) ke kterým musí zařízení replikace přistupovat.

## <a name="agent-based-ports"></a>Porty založené na agentovi

**Zařízení** | **Připojení**
--- | ---
Virtuální počítače | Služba Mobility spuštěná na virtuálních počítačích komunikuje s místním replikačním zařízením (konfiguračním serverem) na příchozím portu HTTPS 443 pro správu replikace.<br/><br/> Virtuální počítače odesílají data replikace na procesní server (spuštěný na konfiguračním serveru) na příchozím portu HTTPS 9443. Tento port lze upravit.
Zařízení pro replikaci | Replikační zařízení orchestruje replikaci s Azure přes port HTTPS 443 odchozí.
Procesní server | Procesní server přijímá replikační data, optimalizuje je a šifruje a odesílá je do úložiště Azure přes odchozí port 443.<br/> Ve výchozím nastavení je na zařízení replikace spuštěn procesní server.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Všechny místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce. Při obnovení webu spustí kontrolu požadavků na replikaci, kontrola se nezdaří, pokud některé požadavky nejsou splněny.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověřuje podporované operační systémy VMware VMM pro migraci.<br/> Můžete migrovat všechny úlohy spuštěné v podporovaném operačním systému. | Kontrola se nezdaří, pokud není podporována.
Architektura hostovaného operačního systému | 64bitový. | Kontrola se nezdaří, pokud není podporována.
Velikost disku s operačním systémem | Až 2 048 GB. | Kontrola se nezdaří, pokud není podporována.
Počet disků s operačním systémem | 1 | Kontrola se nezdaří, pokud není podporována.
Počet datových disků | 64 nebo méně. | Kontrola se nezdaří, pokud není podporována.
Velikost datového disku | Až 4 095 GB | Kontrola se nezdaří, pokud není podporována.
Síťové adaptéry | Je podporováno více adaptérů. |
Sdílený virtuální pevný disk | Není podporováno. | Kontrola se nezdaří, pokud není podporována.
Disk FC | Není podporováno. | Kontrola se nezdaří, pokud není podporována.
BitLocker | Není podporováno. | Nástroj BitLocker musí být zakázán před povolením replikace pro počítač.
název virtuálního počítače | Od 1 do 63 znaků.<br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslem. |  Aktualizujte hodnotu ve vlastnostech počítače v obnovení webu.
Připojit se po migraci windows | Připojení k virtuálním počítačům Azure se systémem Windows po migraci:<br/> - Před migrací povolí RDP na místním virtuálním počítači. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.<br/> Pro přístup k síti VPN mezi lokalitami povolte protokol RDP a povolte protokol RDP v programu **Windows Firewall** -> **Povolené aplikace a funkce** pro domény a **soukromé** sítě. Kromě toho zkontrolujte, zda je zásada sítě SAN operačního systému nastavena na **onlineall**. [Další informace](prepare-for-migration.md). |
Připojit se po migraci-Linux | Připojení k virtuálním počítačům Azure po migraci pomocí SSH:<br/> Před migrací v místním počítači zkontrolujte, zda je služba Zabezpečené prostředí nastavena na start a zda pravidla brány firewall umožňují připojení SSH.<br/> Po převzetí služeb při selhání na virtuálním počítači Azure povolte příchozí připojení k portu SSH pro pravidla skupiny zabezpečení sítě pro převzetí služeb při selhání přes virtuální počítač a pro podsíť Azure, ke které je připojen. Kromě toho přidejte veřejnou IP adresu pro virtuální ho. |  


## <a name="next-steps"></a>Další kroky

[Vyberte](server-migrate-overview.md) možnost migrace vmware.
