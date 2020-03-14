---
title: Podpora migrace Hyper-V v Azure Migrate
description: Přečtěte si o podpoře migrace Hyper-V s Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1eab96df7ee58a8170f75b41c5a2a06f033ced19
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245821"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matice podpory pro migraci technologie Hyper-V

Tento článek shrnuje nastavení a omezení podpory pro migraci virtuálních počítačů Hyper-V pomocí [Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) . Pokud hledáte informace o vyhodnocování virtuálních počítačů Hyper-V pro migraci do Azure, Projděte si přehled [podpory vyhodnocení](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Omezení migrace

Pro replikaci můžete vybrat až 10 virtuálních počítačů najednou. Pokud chcete migrovat více počítačů, replikujte je ve skupinách po 10.


## <a name="hyper-v-hosts"></a>Hostitelé Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení**       | Hostitel Hyper-V může být samostatný nebo nasazený v clusteru. <br/>Na hostitelích Hyper-V musí být nainstalovaný software pro replikaci Azure Migrate (Zprostředkovatel replikace technologie Hyper-V).|
| **Oprávnění**           | Na hostiteli Hyper-V potřebujete oprávnění správce. |
| **Operační systém hostitele** | Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2. |
| **Přístup URL** | Software zprostředkovatele replikace na hostitelích Hyper-V bude potřebovat přístup k těmto ADRESám URL:<br/><br/> -login.microsoftonline.com: řízení přístupu a Správa identit pomocí služby Active Directory.<br/><br/> -*. backup.windowsazure.com: přenos a koordinace dat replikace. Migrujte adresy URL služby.<br/><br/> -*. blob.core.windows.net: Nahrajte data do účtů úložiště.<br/><br/> -dc.services.visualstudio.com: Nahrajte protokoly aplikací používané pro interní monitorování.<br/><br/> -time.windows.com: ověřuje časovou synchronizaci mezi systémovým a globálním časem.
| **Přístup k portu** |  Odchozí připojení na portu HTTPS 443 pro odesílání dat replikace virtuálních počítačů.

## <a name="hyper-v-vms"></a>Virtuální počítače Hyper-V

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) podporované Azure. |
| **Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby je bylo možné spouštět v Azure. Před migrací musíte provést úpravy ručně. Příslušné články obsahují pokyny k tomu, jak to provést. |
| **Spouštění ze systému Linux**                 | Pokud je/Boot ve vyhrazeném oddílu, měl by být umístěn na disku s operačním systémem a nesmí být rozložen na více disků.<br/> Pokud je/Boot součástí kořenového oddílu (/), musí být oddíl '/' na disku s operačním systémem a nesmí zabírat jiné disky. |
| **Spouštění UEFI**                  | Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač se systémem BIOS. Na virtuálním počítači by měl běžet jenom Windows Server 2012 a novější. Disk s operačním systémem by měl mít až pět oddílů nebo méně a velikost disku s operačním systémem by měla být menší než 300 GB.
  |
| **Velikost disku**                  | 2 TB pro disk s operačním systémem, 4 TB pro datové disky.
| **Číslo disku** | Maximálně 16 disků na virtuální počítač.
| **Šifrované disky/svazky**    | Migrace se nepodporuje. |
| **RDM/průchozí disky**      | Migrace se nepodporuje. |
| **Sdílený disk** | Virtuální počítače používající sdílené disky se pro migraci nepodporují.
| **NFS**                        | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat. |
| **ISCSI**                      | Virtuální počítače s cíli iSCSI se nepodporují pro migraci.
| **Cílový disk**                | Můžete migrovat jenom na virtuální počítače Azure se spravovanými disky. |
| **Protokolů** | Není podporováno.
| **Seskupování síťových adaptérů** | Není podporováno.
| **Azure Site Recovery** | Pokud je virtuální počítač povolený pro replikaci pomocí Azure Site Recovery, nejde replikovat pomocí Azure Migrate migrace serveru.
| **Porty** | Odchozí připojení na portu HTTPS 443 pro odesílání dat replikace virtuálních počítačů.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítače Azure

Všechny místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Velikost disku operačního systému | Až 2 048 GB. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet disků operačního systému | 1 | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet datových disků | 16 nebo méně. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost datového disku | Až 4 095 GB | Pokud je tato operace Nepodporovaná, ověřte chybu.
Síťové adaptéry | Podporuje se několik adaptérů. |
Sdílené VHD | Není podporováno. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Disk FC | Není podporováno. | Pokud je tato operace Nepodporovaná, ověřte chybu.
BitLocker | Není podporováno. | Před povolením replikace pro počítač musí být BitLocker zakázán.
název virtuálního počítače | Od 1 do 63 znaků.<br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. |  Aktualizujte hodnotu ve vlastnostech počítače v Site Recovery.
Připojit po migraci – Windows | Připojení k virtuálním počítačům Azure s Windows po migraci:<br/> – Před migrací povolí RDP na místním virtuálním počítači. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.<br/> V případě přístupu typu Site-to-site k síti VPN Povolte protokol RDP a Povolte protokol RDP v **bráně Windows Firewall** -> **povolené aplikace a funkce** pro **domény a privátní** sítě. Dále ověřte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](prepare-for-migration.md). |
Připojit po migraci – Linux | Připojení k virtuálním počítačům Azure po migraci pomocí SSH:<br/> Před migrací na místním počítači ověřte, že je služba Secure Shell nastavená na Start a že pravidla brány firewall umožňují připojení SSH.<br/> Po převzetí služeb při selhání povolte na virtuálním počítači Azure příchozí připojení k portu SSH pro pravidla skupiny zabezpečení sítě na virtuálním počítači, u kterého došlo k převzetí služeb při selhání, a pro podsíť Azure, ke které je připojený. Kromě toho přidejte veřejnou IP adresu pro virtuální počítač. |  

## <a name="next-steps"></a>Další kroky

[Migrujte virtuální počítače Hyper-V](tutorial-migrate-hyper-v.md) pro migraci.
