---
title: Podpora migrace fyzického serveru v Migraci Azure
description: Přečtěte si o podpoře migrace fyzického serveru v Azure Migrate.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269546"
---
# <a name="support-matrix-for-physical-server-migration"></a>Matice podpory pro migraci fyzického serveru

Tento článek shrnuje nastavení podpory a omezení pro migraci fyzických serverů pomocí [migrace Azure: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) . Pokud hledáte informace o posouzení fyzických serverů pro migraci do Azure, přečtěte si [matici podpory hodnocení](migrate-support-matrix-physical.md).


## <a name="overview"></a>Přehled

Místní počítače můžete migrovat jako fyzické servery pomocí replikace založené na agentovi. Pomocí tohoto nástroje můžete migrovat širokou škálu počítačů do Azure:

- Místní fyzické servery.
- Virtuální počítače virtualizované platformami, jako je Xen, KVM.
- Virtuální aplikace Hyper-V nebo virtuální aplikace VMware, pokud z nějakého důvodu nechcete používat standardní toky [Technologie Hyper-V](tutorial-migrate-hyper-v.md) nebo [VMware.](server-migrate-overview.md)
- Virtuální virtuální mích spuštěné v privátních cloudech.
- Virtuální servery spuštěné ve veřejných cloudech, jako jsou Amazon Web Services (AWS) nebo Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Omezení migrace

Pro replikaci můžete vybrat až 10 počítačů najednou. Pokud chcete migrovat více počítačů, replikujte ve skupinách po 10.


## <a name="physical-server-requirements"></a>Požadavky na fyzický server

Tabulka shrnuje podporu fyzických serverů, které chcete migrovat pomocí migrace založené na agentovi.

**Podpora** | **Podrobnosti**
--- | ---
**Pracovní vytížení počítače** | Migrace Azure podporuje migraci všech úloh (například služby Active Directory, SQL server atd.) spuštěných na podporovaném počítači.
**Operační systémy** | Nejnovější informace naleznete v [podpoře operačního systému](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pro site recovery. Azure Migrate poskytuje identickou podporu operačního systému.
**Linux souborový systém / hostovací úložiště** | Nejnovější informace naleznete v [podpoře systému souborů Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pro site recovery. Azure Migrate poskytuje identickou podporu systému souborů Linuxu.
**Síť/úložiště** | Nejnovější informace naleznete v požadavcích [sítě](../site-recovery/vmware-physical-azure-support-matrix.md#network) a [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pro site recovery. Azure Migrate poskytuje stejné požadavky na síť/úložiště.
**Požadavky na Azure** | Nejnovější informace najděte požadavky na [síť Azure](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)a [výpočetní prostředky](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) pro site recovery. Azure Migrate má stejné požadavky na migraci fyzického serveru.
**Služba Mobility** | Agent služby Mobility musí být nainstalován v každém počítači, který chcete migrovat.
**UEFI bota** | Migrovaný počítač v Azure se automaticky převede na spouštěcí virtuální počítač Azure systému BIOS.<br/><br/> Disk operačního systému by měl mít až čtyři oddíly a svazky by měly být formátovány pomocí systému souborů NTFS.
**Cílový disk** | Počítače lze migrovat jenom na spravované disky (standardní hdd, premium SSD) v Azure.
**Velikost disku** | 2 TB OS disk; 8 TB pro datové disky.
**Omezení disku** |  Až 63 disků na počítač.
**Šifrované disky/svazky** |  Počítače se šifrovanými disky/svazky nejsou pro migraci podporovány.
**Cluster sdíleného disku** | Není podporováno.
**Nezávislé disky** | Podporuje se.
**Průchozí disky** | Podporuje se.
**NFS** | Svazky systémem nfs připojené jako svazky na počítačích nebudou replikovány.
**Cíle iSCSI** | Počítače s cíli iSCSI nejsou podporovány pro migraci bez agenta.
**Vícecestné VO** | Není podporováno.
**Úložiště vMotion** | Podporuje se
**Síťové karty pro spojení** | Není podporováno.
**IPv6** | Není podporováno.



## <a name="replication-appliance-requirements"></a>Požadavky zařízení replikace

Pokud zařízení replikace nastavíte ručně na fyzickém serveru, ujistěte se, že splňuje požadavky shrnuté v tabulce. Když nastavíte replikační zařízení Azure Migrate jako virtuální počítač VMware pomocí šablony OVA uvedené v centru Migrace Azure, zařízení se nastaví s Windows Server 2016 a splňuje požadavky na podporu. 

- Informace o [požadavcích na zařízení replikace](migrate-replication-appliance.md#appliance-requirements).
- MySQL musí být nainstalován na zařízení. Informace o [možnostech instalace](migrate-replication-appliance.md#mysql-installation).
- Přečtěte si o [adresách URL,](migrate-replication-appliance.md#url-access) ke kterým musí replikační zařízení přistupovat.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Všechny místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce. Při obnovení webu spustí kontrolu požadavků na replikaci, kontrola se nezdaří, pokud některé požadavky nejsou splněny.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověřuje podporované operační systémy.<br/> Můžete migrovat všechny úlohy spuštěné v podporovaném operačním systému. | Kontrola se nezdaří, pokud není podporována.
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

[Migrovat](tutorial-migrate-physical-virtual-machines.md) fyzické servery.
