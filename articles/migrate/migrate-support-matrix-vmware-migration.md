---
title: Podpora pro migraci VMware v Azure Migrate
description: Přečtěte si o podpoře migrace virtuálních počítačů VMware v Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 9d8dc4dadc975a0fb69ea207f6062b72231460ef
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269507"
---
# <a name="support-matrix-for-vmware-migration"></a>Matice podpory pro migraci VMware

Tento článek shrnuje nastavení podpory a omezení pro migraci virtuálních počítačů VMware pomocí [Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) . Pokud hledáte informace o vyhodnocování virtuálních počítačů VMware pro migraci do Azure, Projděte si přehled [podpory pro vyhodnocení](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Možnosti migrace

Virtuální počítače VMware můžete migrovat několika způsoby:

- Migrace bez agenta: migrujte virtuální počítače, aniž byste museli instalovat cokoli. Nasadíte [zařízení Azure Migrate](migrate-appliance.md) pro migraci bez agenta.
- S migrací založenou na agentech: Nainstalujte do virtuálního počítače agenta pro replikaci. Pro migraci na základě agenta je nutné nasadit [zařízení replikace](migrate-replication-appliance.md).

Přečtěte si [Tento článek](server-migrate-overview.md) a zjistěte, kterou metodu chcete použít.

## <a name="migration-limitations"></a>Omezení migrace

- Pro replikaci můžete vybrat až 10 virtuálních počítačů najednou. Pokud chcete migrovat více počítačů, proveďte replikaci do skupin po 10.
- Pro migraci bez agentů VMware můžete současně spustit až 100 replikace.

## <a name="agentless-vmware-servers"></a>Servery VMware bez agentů

**VMware** | **Podrobnosti**
--- | ---
**vCenter Server VMware** | Verze 5,5, 6,0, 6,5 nebo 6,7.
**VMware vSphere hostitele ESXI** | Verze 5,5, 6,0, 6,5 nebo 6,7.
**vCenter Server oprávnění** | Migrace bez agentů používá [zařízení migrace](migrate-appliance.md). Zařízení potřebuje tato oprávnění:<br/><br/> - **úložiště dat. Procházejte**: povolení procházení souborů protokolu virtuálních počítačů při řešení potíží s vytvářením a odstraňováním snímků.<br/><br/> - **DataStore. LowLevelFileOperations**: povolí operace čtení/zápisu/odstranění/přejmenování v prohlížeči úložiště dat, aby bylo možné řešit problémy při vytváření a odstraňování snímků.<br/><br/> - **VirtualMachine. Configuration. DiskChangeTracking**: umožňuje povolit nebo zakázat sledování změn disků virtuálních počítačů, aby se daly načíst změněné bloky dat mezi snímky.<br/><br/> - **VirtualMachine. Configuration. DiskLease**: Povolte operace zapůjčení disku pro virtuální počítač a přečtěte si disk pomocí sady VMware vSphere Virtual disk Development Kit (VDDK).<br/><br/> - **VirtualMachine. Provisioning. AllowDiskAccess**: (konkrétně pro vSphere 6,0 a vyšší) povolí otevření disku na virtuálním počítači s náhodným přístupem pro čtení na disku pomocí VDDK.<br/><br/> - **VirtualMachine. Provisioning. AllowReadOnlyDiskAccess**: povolení otevření disku na virtuálním počítači pro čtení disku pomocí VDDK.<br/><br/> - **VirtualMachine. Provisioning. AllowDiskRandomAccess**: povolení otevření disku na virtuálním počítači pro čtení disku pomocí VDDK.<br/><br/> - **VirtualMachine. Provisioning. AllowVirtualMachineDownload**: umožňuje operace čtení souborů přidružených k virtuálnímu počítači ke stažení protokolů a řešení potíží, pokud dojde k selhání.<br/><br/> -* * VirtualMachine. SnapshotManagement. * * *: Povolí vytváření a správu snímků virtuálních počítačů pro replikaci.<br/><br/> - **virtuální počítač. interakce. vypnutí**: povolí vypnutí virtuálního počítače během migrace do Azure.



## <a name="agentless-vmware-vms"></a>Virtuální počítače VMware bez agentů

**Podpora** | **Podrobnosti**
--- | ---
**Podporované operační systémy** | Operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) podporované službou Azure je možné migrovat pomocí migrace bez agentů.
**Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby je bylo možné spouštět v Azure. Azure Migrate provede tyto změny automaticky pro následující operační systémy:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> – CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> – Debian 7, 8<br/><br/> Pro jiné operační systémy je nutné provést úpravy ručně před migrací. Příslušné články obsahují pokyny k tomu, jak to provést.
**Spouštění ze systému Linux** | Pokud je/Boot ve vyhrazeném oddílu, měl by být umístěn na disku s operačním systémem a nesmí být rozložen na více disků.<br/> Pokud je/Boot součástí kořenového oddílu (/), musí být oddíl '/' na disku s operačním systémem a nesmí zabírat jiné disky.
**Spouštění UEFI** | Virtuální počítače se spouštěním UEFI se pro migraci nepodporují.
**Velikost disku** | 2 TB disk s operačním systémem; 4 TB datových disků
**Omezení disku** |  Až 60 disků na virtuální počítač.
**Šifrované disky/svazky** | Virtuální počítače se zašifrovanými disky nebo svazky se nepodporují pro migraci.
**Cluster sdíleného disku** | Není podporováno.
**Nezávislé disky** | Není podporováno.
**RDM/průchozí disky** | Pokud virtuální počítače mají RDM nebo průchozí disky, tyto disky se nebudou replikovat do Azure.
**NFS** | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat.
**cíle iSCSI** | Virtuální počítače s cíli iSCSI nejsou podporované pro migraci bez agenta.
**Multipath v/v** | Není podporováno.
**VMotion úložiště** | Není podporováno. Pokud virtuální počítač používá úložiště vMotion, replikace nebude fungovat.
**Seskupené síťové adaptéry** | Není podporováno.
**Protokolů** | Není podporováno.
**Cílový disk** | Virtuální počítače se dají migrovat jenom na spravované disky (Standard HDD, Premium SSD) v Azure.
**Současná replikace** | 100 virtuálních počítačů na vCenter Server. Pokud máte víc, migrujte je v dávkách 100.


## <a name="agentless-azure-migrate-appliance"></a>Zařízení bez agenta Azure Migrate 
Migrace bez agentů používá zařízení Azure Migrate nasazené na virtuálním počítači VMware.

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---vmware) pro VMware.
- Přečtěte si o [adresách URL](migrate-appliance.md#url-access) , ke kterým zařízení potřebuje mít přístup.

## <a name="agentless-ports"></a>Bez agentů – porty

**Zařízení** | **Vázán**
--- | ---
Náplně | Odchozí připojení na portu 443 pro nahrání replikovaných dat do Azure a komunikaci s Azure Migrate službami orchestrace replikace a migrace.
Server vCenter | Příchozí připojení na portu 443, aby zařízení mohla orchestrovat replikaci – vytvářet snímky, kopírovat data, snímky verzí
Hostitel vSphere/EXSI | Příchozí na portu TCP 902, aby zařízení mohl replikovat data ze snímků.


## <a name="agent-based-vmware-servers"></a>Servery VMware založené na agentech
Tato tabulka shrnuje podporu a omezení hodnocení pro virtualizační servery VMware.

**Požadavky VMware** | **Podrobnosti**
--- | ---
**vCenter Server VMware** | Verze 5,5, 6,0, 6,5 nebo 6,7.
**VMware vSphere hostitele ESXI** | Verze 5,5, 6,0, 6,5 nebo 6,7.
**vCenter Server oprávnění** | Účet jen pro čtení pro vCenter Server.

## <a name="agent-based-vmware-vms"></a>Virtuální počítače VMware založené na agentech

Tabulka shrnuje podporu virtuálních počítačů VMware pro virtuální počítače VMware, které chcete migrovat pomocí migrace založené na agentech.

**Podpora** | **Podrobnosti**
--- | ---
**Zatížení počítače** | Azure Migrate podporuje migraci jakékoli úlohy (například Active Directory, SQL Server atd.) běžící v podporovaném počítači.
**Operační systémy** | Nejnovější informace najdete v části [Podpora operačního systému](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pro Site Recovery. Azure Migrate poskytuje stejnou podporu operačního systému virtuálního počítače.
**Systém souborů Linux/úložiště hostů** | Nejnovější informace najdete v části [Podpora systému souborů Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pro Site Recovery. Azure Migrate má stejnou podporu systému souborů Linux.
**Síť/úložiště** | Nejnovější informace najdete v části požadavky na [síť](../site-recovery/vmware-physical-azure-support-matrix.md#network) a [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pro Site Recovery. Azure Migrate poskytuje identické požadavky na síť a úložiště.
**Požadavky na Azure** | Nejnovější informace najdete v části požadavky na [síť](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)a [výpočetní](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) prostředky Azure pro Site Recovery. Azure Migrate má stejné požadavky na migraci VMware.
**Služba Mobility** | Na každý virtuální počítač, který chcete migrovat, musí být nainstalovaný agent služby mobility.
**Spouštění UEFI** | Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač se systémem BIOS.<br/><br/> Disk s operačním systémem by měl mít až čtyři oddíly a svazky by měly být naformátované pomocí systému souborů NTFS.
**Cílový disk** | Virtuální počítače se dají migrovat jenom na spravované disky (Standard HDD, Premium SSD) v Azure.
**Velikost disku** | 2 TB disk s operačním systémem; 8 TB pro datové disky.
**Omezení disku** |  Až 63 disků na virtuální počítač.
**Šifrované disky/svazky** | Virtuální počítače se zašifrovanými disky nebo svazky se nepodporují pro migraci.
**Cluster sdíleného disku** | Není podporováno.
**Nezávislé disky** | Podporuje se.
**Průchozí disky** | Podporuje se.
**NFS** | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat.
**cíle iSCSI** | Virtuální počítače s cíli iSCSI nejsou podporované pro migraci bez agenta.
**Multipath v/v** | Není podporováno.
**VMotion úložiště** | Podporuje se
**Seskupené síťové adaptéry** | Není podporováno.
**Protokolů** | Není podporováno.




## <a name="agent-based-replication-appliance"></a>Zařízení replikace založené na agentovi 

Při nastavování zařízení replikace pomocí šablony vajíček, která je k dispozici v centru Azure Migrate, zařízení spustí systém Windows Server 2016 a splňuje požadavky na podporu. Pokud se zařízení replikace nastavuje ručně na fyzickém serveru, ujistěte se, že splňuje požadavky.

- Seznamte se s [požadavky na zařízení replikace](migrate-replication-appliance.md#appliance-requirements) pro VMware.
- V zařízení musí být nainstalovaný MySQL. Přečtěte si o [možnostech instalace](migrate-replication-appliance.md#mysql-installation).
- Přečtěte si o [adresách URL](migrate-replication-appliance.md#url-access) a [portech](migrate-replication-appliance.md#port-access) , které musí zařízení replikace mít.

## <a name="agent-based-ports"></a>Porty založené na agentech

**Zařízení** | **Vázán**
--- | ---
Virtuální počítače | Služba mobility spuštěná na virtuálních počítačích komunikuje s místním zařízením replikace (konfiguračním serverem) na portu HTTPS 443 příchozím pro správu replikací.<br/><br/> Virtuální počítače odesílají data replikace na procesový Server (spuštěný na počítači konfiguračního serveru) na portu HTTPS 9443 příchozí. Tento port lze změnit.
Replikační zařízení | Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
Procesový Server | Procesový server přijímá data replikace, optimalizuje je a šifruje je a odesílá je do Azure Storage přes odchozí port 443.<br/> Ve výchozím nastavení běží na zařízení replikace procesový Server.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítače Azure

Všechny místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce. Když Site Recovery spustí kontrolu předpokladů pro replikaci, tato akce se nezdaří, pokud nejsou splněné některé z těchto požadavků.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověří podporované operační systémy virtuálních počítačů VMware pro migraci.<br/> Můžete migrovat libovolné úlohy běžící v podporovaném operačním systému. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Architektura hostovaného operačního systému | 64-bit. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost disku operačního systému | Až 2 048 GB. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet disků operačního systému | 1 | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet datových disků | 64 nebo méně. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost datového disku | Až 4 095 GB | Pokud je tato operace Nepodporovaná, ověřte chybu.
Síťové adaptéry | Podporuje se několik adaptérů. |
Sdílené VHD | Není podporováno. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Disk FC | Není podporováno. | Pokud je tato operace Nepodporovaná, ověřte chybu.
BitLocker | Není podporováno. | Před povolením replikace pro počítač musí být BitLocker zakázán.
název virtuálního počítače | Od 1 do 63 znaků.<br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. |  Aktualizujte hodnotu ve vlastnostech počítače v Site Recovery.
Připojit po migraci – Windows | Připojení k virtuálním počítačům Azure s Windows po migraci:<br/> – Před migrací povolí RDP na místním virtuálním počítači. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.<br/> V případě přístupu typu Site-to-site k síti VPN Povolte protokol RDP a Povolte protokol RDP v **bráně Windows Firewall** -> **povolené aplikace a funkce** pro **domény a privátní** sítě. Dále ověřte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](prepare-for-migration.md). |
Připojit po migraci – Linux | Připojení k virtuálním počítačům Azure po migraci pomocí SSH:<br/> Před migrací na místním počítači ověřte, že je služba Secure Shell nastavená na Start a že pravidla brány firewall umožňují připojení SSH.<br/> Po převzetí služeb při selhání povolte na virtuálním počítači Azure příchozí připojení k portu SSH pro pravidla skupiny zabezpečení sítě na virtuálním počítači, u kterého došlo k převzetí služeb při selhání, a pro podsíť Azure, ke které je připojený. Kromě toho přidejte veřejnou IP adresu pro virtuální počítač. |  


## <a name="next-steps"></a>Další kroky

[Vyberte](server-migrate-overview.md) možnost migrace VMware.
