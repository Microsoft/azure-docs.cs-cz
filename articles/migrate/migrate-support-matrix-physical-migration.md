---
title: Podpora migrace fyzického serveru v Azure Migrate
description: Přečtěte si o podpoře migrace fyzického serveru v Azure Migrate.
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 06/14/2020
ms.openlocfilehash: 95456298b3275c83152115d700a4539cab2d2a67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318156"
---
# <a name="support-matrix-for-physical-server-migration"></a>Matice podpory pro migraci fyzických serverů

Tento článek shrnuje nastavení podpory a omezení pro migraci fyzických serverů s [Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) . Pokud hledáte informace o vyhodnocování fyzických serverů pro migraci do Azure, Projděte si přehled [podpory pro vyhodnocení](migrate-support-matrix-physical.md).

## <a name="migrating-machines-as-physical"></a>Migrace počítačů jako fyzických

K migraci místních počítačů jako fyzických serverů můžete použít replikaci založenou na agentech. Pomocí tohoto nástroje můžete migrovat široké spektrum počítačů do Azure:

- Místní fyzické servery.
- Virtuální počítače virtualizované pomocí platforem, jako je Xen, KVM.
- Virtuální počítače Hyper-V nebo virtuální počítače VMware z nějakého důvodu nechcete používat standardní toky [Hyper-v](tutorial-migrate-hyper-v.md) nebo [VMware](server-migrate-overview.md) .
- Virtuální počítače běžící v privátních cloudech.
- Virtuální počítače běžící ve veřejných cloudech, například Amazon Web Services (AWS) nebo Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Omezení migrace

Pro replikaci můžete vybrat až 10 počítačů najednou. Pokud chcete migrovat více počítačů, proveďte replikaci do skupin po 10.


## <a name="physical-server-requirements"></a>Požadavky na fyzický server

Tabulka shrnuje podporu pro fyzické servery, které chcete migrovat pomocí migrace založené na agentech.

**Podpora** | **Podrobnosti**
--- | ---
**Zatížení počítače** | Azure Migrate podporuje migraci jakékoli úlohy (například Active Directory, SQL Server atd.) běžící v podporovaném počítači.
**Operační systémy** | Nejnovější informace najdete v části [Podpora operačního systému](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) pro Site Recovery. Azure Migrate poskytuje stejnou podporu operačního systému.
**Systém souborů Linux/úložiště hostů** | Nejnovější informace najdete v části [Podpora systému souborů Linux](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) pro Site Recovery. Azure Migrate poskytuje stejnou podporu systému souborů Linux.
**Síť/úložiště** | Nejnovější informace najdete v části požadavky na [síť](../site-recovery/vmware-physical-azure-support-matrix.md#network) a [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#storage) pro Site Recovery. Azure Migrate poskytuje identické požadavky na síť a úložiště.
**Požadavky na Azure** | Nejnovější informace najdete v části požadavky na [síť](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [úložiště](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)a [výpočetní](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) prostředky Azure pro Site Recovery. Azure Migrate má stejné požadavky na migraci fyzického serveru.
**Služba Mobility** | Na každý počítač, který chcete migrovat, musí být nainstalovaný agent služby mobility.
**Spouštění UEFI** | Podporuje se. Počítače založené na rozhraní UEFI se migrují na virtuální počítače Azure generace 2.  <br/><br/> Disk s operačním systémem by měl mít až čtyři oddíly a svazky by měly být naformátované pomocí systému souborů NTFS.
**Rozhraní UEFI – zabezpečené spouštění**         | Migrace se nepodporuje.
**Cílový disk** | Počítače se dají migrovat jenom na spravované disky (standardní pevný disk, Standard SSD, Premium SSD) v Azure.
**Velikost disku** | 2 TB disk s operačním systémem (spouštěcí počítač BIOS); 4 TB disk s operačním systémem (spouštění UEFI); 8 TB pro datové disky.
**Omezení disku** |  Až 63 disků na počítač.
**Šifrované disky/svazky** |  Počítače se zašifrovanými disky/svazky se pro migraci nepodporují.
**Cluster sdíleného disku** | Nepodporováno
**Nezávislé disky** | Podporuje se.
**Průchozí disky** | Podporuje se.
**NFS** | Svazky NFS připojené jako svazky na počítačích se nebudou replikovat.
**cíle iSCSI** | Počítače s cíli iSCSI nejsou podporované pro migraci bez agenta.
**Multipath v/v** | Nepodporováno
**VMotion úložiště** | Podporováno
**Seskupené síťové adaptéry** | Nepodporováno
**IPv6** | Nepodporováno



## <a name="replication-appliance-requirements"></a>Požadavky zařízení replikace

Pokud se zařízení replikace nastavuje ručně na fyzickém serveru, ujistěte se, že splňuje požadavky shrnuté v tabulce. Když nastavíte zařízení pro replikaci Azure Migrate jako virtuální počítač VMware pomocí šablony pro VAJÍČKy poskytované v centru Azure Migrate, zařízení se nastaví s Windows serverem 2016 a splňuje požadavky na podporu. 

- Seznamte se s [požadavky na zařízení replikace](migrate-replication-appliance.md#appliance-requirements).
- V zařízení musí být nainstalovaný MySQL. Přečtěte si o [možnostech instalace](migrate-replication-appliance.md#mysql-installation).
- Seznamte se s [adresami URL](migrate-replication-appliance.md#url-access) , ke kterým musí mít zařízení replikace přístup.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Všechny místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce. Když Site Recovery spustí kontrolu předpokladů pro replikaci, tato akce se nezdaří, pokud nejsou splněné některé z těchto požadavků.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověřuje podporované operační systémy.<br/> Můžete migrovat libovolné úlohy běžící v podporovaném operačním systému. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Architektura hostovaného operačního systému | 64-bit. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost disku s operačním systémem | Až 2 048 GB. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet disků s operačním systémem | 1 | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet datových disků | 64 nebo méně. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost datového disku | Až 4 095 GB | Pokud je tato operace Nepodporovaná, ověřte chybu.
Síťové adaptéry | Podporuje se několik adaptérů. |
Sdílený virtuální pevný disk | Nepodporováno | Pokud je tato operace Nepodporovaná, ověřte chybu.
Disk FC | Nepodporováno | Pokud je tato operace Nepodporovaná, ověřte chybu.
BitLocker | Nepodporováno | Před povolením replikace pro počítač musí být BitLocker zakázán.
název virtuálního počítače | Od 1 do 63 znaků.<br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. |  Aktualizujte hodnotu ve vlastnostech počítače v Site Recovery.
Připojit po migraci – Windows | Připojení k virtuálním počítačům Azure s Windows po migraci:<br/> – Před migrací povolí RDP na místním virtuálním počítači. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.<br/> V případě přístupu typu Site-to-site k síti VPN Povolte protokol RDP a Povolte protokol RDP v **bráně Windows Firewall**  ->  **povolené aplikace a funkce** pro **domény a privátní** sítě. Dále ověřte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](prepare-for-migration.md). |
Připojit po migraci – Linux | Připojení k virtuálním počítačům Azure po migraci pomocí SSH:<br/> Před migrací na místním počítači ověřte, že je služba Secure Shell nastavená na Start a že pravidla brány firewall umožňují připojení SSH.<br/> Po převzetí služeb při selhání povolte na virtuálním počítači Azure příchozí připojení k portu SSH pro pravidla skupiny zabezpečení sítě na virtuálním počítači, u kterého došlo k převzetí služeb při selhání, a pro podsíť Azure, ke které je připojený. Kromě toho přidejte veřejnou IP adresu pro virtuální počítač. |  


## <a name="next-steps"></a>Další kroky

[Migrujte](tutorial-migrate-physical-virtual-machines.md) fyzické servery.
