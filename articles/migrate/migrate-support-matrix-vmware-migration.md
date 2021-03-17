---
title: Podpora pro migraci VMware v Azure Migrate
description: Přečtěte si o podpoře migrace virtuálních počítačů VMware v Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: cbb1db15eed53af1d0e4590e1b228e5e47680560
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614917"
---
# <a name="support-matrix-for-vmware-migration"></a>Matice podpory pro migraci VMware

Tento článek shrnuje nastavení podpory a omezení pro migraci virtuálních počítačů VMware pomocí [Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) . Pokud hledáte informace o vyhodnocování virtuálních počítačů VMware pro migraci do Azure, Projděte si přehled [podpory pro vyhodnocení](migrate-support-matrix-vmware.md).


## <a name="migration-options"></a>Možnosti migrace

Virtuální počítače VMware můžete migrovat několika způsoby:

- **Migrace bez agenta**: migrujte virtuální počítače, aniž byste museli instalovat cokoli. Nasadíte [zařízení Azure Migrate](migrate-appliance.md) pro migraci bez agenta.
- **Použití migrace na základě agentů**: Nainstalujte na virtuální počítač agenta pro replikaci. Pro migraci na základě agenta nasadíte [zařízení replikace](migrate-replication-appliance.md).

Přečtěte si [Tento článek](server-migrate-overview.md) a zjistěte, kterou metodu chcete použít.

## <a name="migration-limitations"></a>Omezení migrace

- Můžete vybrat až 10 virtuálních počítačů najednou pro replikaci prostřednictvím Azure Portal. Pokud chcete migrovat více počítačů, proveďte replikaci do skupin po 10. Počet virtuálních počítačů, které se dají replikovat prostřednictvím rutin PowerShellu, není nijak omezený. Naším doporučením je replikovat maximálně 500 virtuálních počítačů z jednoho vCenter přes PowerShell, aby se zajistil optimální výkon.
- Pro migraci bez agentů VMware můžete z každého vCenter Server spustit současně až 500 replikace.

## <a name="agentless-migration"></a>Migrace bez agentů 

V této části jsou shrnuté požadavky na migraci bez agentů.

### <a name="vmware-requirements-agentless"></a>Požadavky VMware (bez agentů)

Tabulka shrnuje požadavky hypervisoru VMware.

**VMware** | **Podrobnosti**
--- | ---
**vCenter Server VMware** | Verze 5,5, 6,0, 6,5, 6,7, 7,0.
**VMware vSphere hostitele ESXI** | Verze 5,5, 6,0, 6,5, 6,7, 7,0.
**vCenter Server oprávnění** | Migrace bez agentů používá [zařízení migrace](migrate-appliance.md). Zařízení potřebuje tato oprávnění v vCenter Server:<br/><br/> - **Úložiště dat. procházet** (úložiště dat-> procházet úložiště dat): povolí procházení souborů protokolu virtuálních počítačů, aby bylo možné řešit problémy při vytváření a odstraňování snímků.<br/><br/> - **Úložiště dat. Správa** souborů (úložiště dat – > operací se soubory na nízké úrovni): v prohlížeči úložiště dat povolte operace čtení, zápisu, odstranění a přejmenování, abyste mohli řešit problémy při vytváření a odstraňování snímků.<br/><br/> - **VirtualMachine.Config. Sledování změn ve** (sledování změn virtuálního počítače – >): povolí nebo zakáže sledování změn disků virtuálních počítačů, aby bylo možné načíst změněné bloky dat mezi snímky.<br/><br/> - **VirtualMachine.Config. DiskLease** (virtuální počítač – > zapůjčení disku): Povolte operace zapůjčení disku pro virtuální počítač a přečtěte si disk pomocí sady VMware vSphere (Virtual disk Development Kit) pro VDDK.<br/><br/> - **VirtualMachine. Provisioning. DiskRandomRead** (zřizování virtuálních počítačů->-> povolí přístup k disku jen pro čtení): povolí otevření disku na virtuálním počítači, aby se disk mohl přečíst pomocí VDDK.<br/><br/> - **VirtualMachine. Provisioning. DiskRandomAccess** (zřizování virtuálních počítačů-> – > povoluje přístup k disku): povoluje otevření disku na virtuálním počítači pro čtení disku pomocí VDDK.<br/><br/> - **VirtualMachine. Provisioning. GetVmFiles** (zřizování virtuálních počítačů->-> povolit stahování virtuálního počítače): umožňuje operace čtení souborů přidružených k virtuálnímu počítači ke stažení protokolů a řešení potíží, pokud dojde k selhání.<br/><br/> - **VirtualMachine. State. \* *_ (Správa snímků pro virtuální počítače a >): Povolí vytváření a správu snímků virtuálních počítačů pro replikaci. <br/> <br/> -_* VirtualMachine. interagovat. stavu PowerOff** (interakce s virtuálním počítačem a > – > vypnutí): povolí vypnutí virtuálního počítače během migrace do Azure.



### <a name="vm-requirements-agentless"></a>Požadavky na virtuální počítače (bez agentů)

Tabulka shrnuje požadavky na migraci bez agenta pro virtuální počítače VMware.

**Podpora** | **Podrobnosti**
--- | ---
**Podporované operační systémy** | Můžete migrovat operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](../virtual-machines/linux/endorsed-distros.md) , které podporuje Azure.
**Virtuální počítače s Windows v Azure** | Před migrací možná budete muset [provést nějaké změny](prepare-for-migration.md#verify-required-changes-before-migrating) virtuálních počítačů. 
**Virtuální počítače se systémem Linux v Azure** | Některé virtuální počítače můžou vyžadovat změny, aby je bylo možné spouštět v Azure.<br/><br/> Pro Linux Azure Migrate provede změny automaticky pro tyto operační systémy:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Cent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> – SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8, 9 <br/> Oracle Linux 7,7, 7,7-CI<br/> Pro jiné operační systémy provedete [požadované změny](prepare-for-migration.md#verify-required-changes-before-migrating) ručně.
**Spouštění ze systému Linux** | Pokud je/Boot ve vyhrazeném oddílu, měl by být umístěn na disku s operačním systémem a nesmí být rozložen na více disků.<br/> Pokud je/Boot součástí kořenového oddílu (/), musí být oddíl '/' na disku s operačním systémem a nesmí zabírat jiné disky.
**Spouštění UEFI** | Podporuje se. Virtuální počítače založené na rozhraní UEFI se migrují na virtuální počítače Azure generace 2. 
**Velikost disku** | 2 TB disk s operačním systémem;  32 TB datových disků.
**Omezení disku** |  Až 60 disků na virtuální počítač.
**Šifrované disky/svazky** | Virtuální počítače se zašifrovanými disky nebo svazky se nepodporují pro migraci.
**Cluster sdíleného disku** | Nepodporováno
**Nezávislé disky** | Nepodporováno
**RDM/průchozí disky** | Pokud virtuální počítače mají RDM nebo průchozí disky, tyto disky se nebudou replikovat do Azure.
**NFS** | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat.
**cíle iSCSI** | Virtuální počítače s cíli iSCSI nejsou podporované pro migraci bez agenta.
**Multipath v/v** | Nepodporováno
**VMotion úložiště** | Nepodporováno Pokud virtuální počítač používá úložiště vMotion, replikace nebude fungovat.
**Seskupené síťové adaptéry** | Nepodporováno
**IPv6** | Nepodporováno
**Cílový disk** | Virtuální počítače se dají migrovat jenom na spravované disky (standardní pevný disk, Standard SSD, Premium SSD) v Azure.
**Současná replikace** | 500 virtuálních počítačů na vCenter Server. Pokud máte víc, migrujte je v dávkách 500.
**Automatická instalace agenta virtuálního počítače Azure (Agent systému Windows a Linux)** | Podporováno pro systémy Windows Server 2008 R2 a vyšší. <br/> Podporováno pro počítače RHEL6, RHEL7, CentOS7, Ubuntu 14,04, Ubuntu 16,04, Ubuntu 18.04. Projděte si seznam [požadovaných balíčků](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux#requirements)pro tyto operační systémy Linux.

### <a name="appliance-requirements-agentless"></a>Požadavky na zařízení (bez agenta)

Migrace bez agentů používá [zařízení Azure Migrate](migrate-appliance.md). Zařízení můžete nasadit jako virtuální počítač VMware pomocí šablony vajíček, naimportovat do vCenter Server nebo pomocí [skriptu PowerShellu](deploy-appliance-script.md).

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---vmware) pro VMware.
- Přečtěte si o adresách URL, které zařízení potřebuje k přístupu ve [veřejných](migrate-appliance.md#public-cloud-urls) a [státních](migrate-appliance.md#government-cloud-urls) cloudech.
- V Azure Government musíte zařízení nasadit [pomocí skriptu](deploy-appliance-script-government.md).

### <a name="port-requirements-agentless"></a>Požadavky na porty (bez agenta)

**Zařízení** | **Připojení**
--- | ---
Náplně | Odchozí připojení na portu 443 pro nahrání replikovaných dat do Azure a komunikaci s Azure Migrate službami orchestrace replikace a migrace.
Server vCenter | Příchozí připojení na portu 443, aby zařízení mohla orchestrovat replikaci – vytvářet snímky, kopírovat data, snímky verzí
Hostitel vSphere/ESXI | Příchozí na portu TCP 902, aby zařízení mohl replikovat data ze snímků.

## <a name="agent-based-migration"></a>Migrace založená na agentovi 


V této části jsou shrnuté požadavky na migraci na základě agenta.


### <a name="vmware-requirements-agent-based"></a>Požadavky VMware (založené na agentech)

Tato tabulka shrnuje podporu a omezení hodnocení pro virtualizační servery VMware.

**Požadavky VMware** | **Podrobnosti**
--- | ---
**vCenter Server VMware** | Verze 5,5, 6,0, 6,5 nebo 6,7.
**VMware vSphere hostitele ESXI** | Verze 5,5, 6,0, 6,5 nebo 6,7.
**vCenter Server oprávnění** | Účet jen pro čtení pro vCenter Server.

### <a name="vm-requirements-agent-based"></a>Požadavky na virtuální počítače (založené na agentech)

Tabulka shrnuje podporu virtuálních počítačů VMware pro virtuální počítače VMware, které chcete migrovat pomocí migrace založené na agentech.

**Podpora** | **Podrobnosti**
--- | ---
**Zatížení počítače** | Azure Migrate podporuje migraci jakékoli úlohy (například Active Directory, SQL Server atd.) běžící v podporovaném počítači.
**Operační systémy** | Nejnovější informace najdete v části [Podpora operačního systému](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pro Site Recovery. Azure Migrate poskytuje stejnou podporu operačního systému virtuálního počítače.
**Systém souborů Linux/úložiště hostů** | Nejnovější informace najdete v části [Podpora systému souborů Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pro Site Recovery. Azure Migrate má stejnou podporu systému souborů Linux.
**Síť/úložiště** | Nejnovější informace najdete v části požadavky na [síť](../site-recovery/vmware-physical-azure-support-matrix.md#network) a [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pro Site Recovery. Azure Migrate poskytuje identické požadavky na síť a úložiště.
**Požadavky na Azure** | Nejnovější informace najdete v části požadavky na [síť](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)a [výpočetní](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) prostředky Azure pro Site Recovery. Azure Migrate má stejné požadavky na migraci VMware.
**Služba Mobility** | Na každý virtuální počítač, který chcete migrovat, musí být nainstalovaný agent služby mobility.
**Spouštění UEFI** | Podporuje se. Virtuální počítače založené na rozhraní UEFI se migrují na virtuální počítače Azure generace 2. 
**Rozhraní UEFI – zabezpečené spouštění**         | Migrace se nepodporuje.
**Cílový disk** | Virtuální počítače se dají migrovat jenom na spravované disky (standardní pevný disk, Standard SSD, Premium SSD) v Azure.
**Velikost disku** | 2 TB disk s operačním systémem; 32 TB datových disků.
**Omezení disku** |  Až 63 disků na virtuální počítač.
**Šifrované disky/svazky** | Virtuální počítače se zašifrovanými disky nebo svazky se nepodporují pro migraci.
**Cluster sdíleného disku** | Nepodporováno
**Nezávislé disky** | Podporuje se.
**Průchozí disky** | Podporuje se.
**NFS** | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat.
**cíle iSCSI** | Podporuje se.
**Multipath v/v** | Nepodporováno
**VMotion úložiště** | Podporováno
**Seskupené síťové adaptéry** | Nepodporováno
**IPv6** | Nepodporováno




### <a name="appliance-requirements-agent-based"></a>Požadavky na zařízení (založené na agentech)

Při nastavování zařízení replikace pomocí šablony vajíček, která je k dispozici v centru Azure Migrate, zařízení spustí systém Windows Server 2016 a splňuje požadavky na podporu. Pokud se zařízení replikace nastavuje ručně na fyzickém serveru, ujistěte se, že splňuje požadavky.

- Seznamte se s [požadavky na zařízení replikace](migrate-replication-appliance.md#appliance-requirements) pro VMware.
- V zařízení musí být nainstalovaný MySQL. Přečtěte si o [možnostech instalace](migrate-replication-appliance.md#mysql-installation).
- Přečtěte si o adresách URL, které zařízení replikace potřebuje k přístupu ve [veřejných](migrate-replication-appliance.md#url-access) a [státních](migrate-replication-appliance.md#azure-government-url-access) cloudech.
- Zkontrolujte [porty](migrate-replication-appliance.md#port-access) , ke kterým musí mít zařízení replikace přístup.

### <a name="port-requirements-agent-based"></a>Požadavky na porty (založené na agentech)

**Zařízení** | **Připojení**
--- | ---
Virtuální počítače | Služba mobility spuštěná na virtuálních počítačích komunikuje s místním zařízením replikace (konfiguračním serverem) na portu HTTPS 443 příchozím pro správu replikací.<br/><br/> Virtuální počítače odesílají data replikace na procesový Server (spuštěný na počítači konfiguračního serveru) na portu HTTPS 9443 příchozí. Tento port lze změnit.
Replikační zařízení | Zařízení replikace orchestruje replikaci pomocí Azure přes odchozí port HTTPS 443.
Procesový Server | Procesový server přijímá data replikace, optimalizuje je a šifruje je a odesílá je do Azure Storage přes odchozí port 443.<br/> Ve výchozím nastavení běží na zařízení replikace procesový Server.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Všechny místní virtuální počítače replikované do Azure (s migrací bez agentů nebo s migrací založenou na agentech) musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce. 

**Komponenta** | **Požadavky** 
--- | --- | ---
Hostovaný operační systém | Ověří podporované operační systémy virtuálních počítačů VMware pro migraci.<br/> Můžete migrovat libovolné úlohy běžící v podporovaném operačním systému. 
Architektura hostovaného operačního systému | 64-bit. 
Velikost disku s operačním systémem | Až 2 048 GB. 
Počet disků s operačním systémem | 1 
Počet datových disků | 64 nebo méně. 
Velikost datového disku | Až 32 TB
Síťové adaptéry | Podporuje se několik adaptérů.
Sdílený virtuální pevný disk | Nepodporováno 
Disk FC | Nepodporováno 
BitLocker | Nepodporováno<br/><br/> Před migrací počítače je třeba zakázat nástroj BitLocker.
název virtuálního počítače | Od 1 do 63 znaků.<br/><br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. 
Připojit po migraci – Windows | Připojení k virtuálním počítačům Azure s Windows po migraci:<br/><br/> – Před migrací povolte RDP na místním virtuálním počítači.<br/><br/> Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.<br/><br/> V případě přístupu typu Site-to-site k síti VPN Povolte protokol RDP a Povolte protokol RDP v **bráně Windows Firewall**  ->  **povolené aplikace a funkce** pro **domény a privátní** sítě.<br/><br/> Dále ověřte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](prepare-for-migration.md).
Připojit po migraci – Linux | Připojení k virtuálním počítačům Azure po migraci pomocí SSH:<br/><br/> Před migrací na místním počítači ověřte, že je služba Secure Shell nastavená na Start a že pravidla brány firewall umožňují připojení SSH.<br/><br/> Po převzetí služeb při selhání povolte na virtuálním počítači Azure příchozí připojení k portu SSH pro pravidla skupiny zabezpečení sítě na virtuálním počítači, u kterého došlo k převzetí služeb při selhání, a pro podsíť Azure, ke které je připojený.<br/><br/> Kromě toho přidejte veřejnou IP adresu pro virtuální počítač.  


## <a name="next-steps"></a>Další kroky

[Vyberte](server-migrate-overview.md) možnost migrace VMware.
