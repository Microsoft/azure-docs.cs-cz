---
title: Podpora migrace Hyper-V v Azure Migrate
description: Přečtěte si o podpoře migrace Hyper-V s Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 4ba8b8cea784167ad045c5635ce512a68b48d897
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442302"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matice podpory pro migraci technologie Hyper-V

Tento článek shrnuje nastavení a omezení podpory pro migraci virtuálních počítačů Hyper-V pomocí [Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) . Pokud hledáte informace o vyhodnocování virtuálních počítačů Hyper-V pro migraci do Azure, Projděte si přehled [podpory vyhodnocení](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Omezení migrace

Pro replikaci můžete vybrat až 10 virtuálních počítačů najednou. Pokud chcete migrovat více počítačů, replikujte je ve skupinách po 10.


## <a name="hyper-v-host-requirements"></a>Požadavky na hostitele Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení**       | Hostitel Hyper-V může být samostatný nebo nasazený v clusteru. <br/>Na hostitelích Hyper-V je nainstalovaný software pro replikaci Azure Migrate (Zprostředkovatel replikace technologie Hyper-V).|
| **Oprávnění**           | Na hostiteli Hyper-V potřebujete oprávnění správce. |
| **Operační systém hostitele** | Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2 s nejnovějšími aktualizacemi. Upozorňujeme, že instalace jádra serveru těchto operačních systémů je taky podporovaná. |
| **Jiné požadavky na software** | .NET Framework 4,7 nebo novější |
| **Přístup k portu** |  Odchozí připojení na portu HTTPS 443 pro odesílání dat replikace virtuálních počítačů.
| **Volné místo na disku (mezipaměť)** |  600 GB |
| **Volné místo na disku (disk pro uchovávání)** |  600 GB |


## <a name="hyper-v-vms"></a>Virtuální počítače Hyper-V

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux](../virtual-machines/linux/endorsed-distros.md) podporované Azure. |
**Windows Server 2003** | Pro virtuální počítače s Windows serverem 2003 je nutné před migrací [nainstalovat integrační služby technologie Hyper-V](prepare-windows-server-2003-migration.md) . | 
**Virtuální počítače se systémem Linux v Azure** | Některé virtuální počítače můžou vyžadovat změny, aby je bylo možné spouštět v Azure.<br/><br/> Pro Linux Azure Migrate provede změny automaticky pro tyto operační systémy:<br/> -Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x<br/> -Cent OS 7,7, 7,6, 7,5, 7,4, 6. x</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> – SUSE Linux Enterprise Server 15 SP1 <br/>-Ubuntu 19,04, 19,10, 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> – Debian 7, 8 <br/> Oracle Linux 7,7, 7,7-CI<br/> Pro jiné operační systémy provedete [požadované změny](prepare-for-migration.md#verify-required-changes-before-migrating) ručně.
| **Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby je bylo možné spouštět v Azure. Před migrací proveďte úpravy ručně. Příslušné články obsahují pokyny k tomu, jak to provést. |
| **Spouštění ze systému Linux**                 | Pokud je/Boot ve vyhrazeném oddílu, měl by být umístěn na disku s operačním systémem a nesmí být rozložen na více disků.<br/> Pokud je/Boot součástí kořenového oddílu (/), musí být oddíl '/' na disku s operačním systémem a nesmí zabírat jiné disky. |
| **Spouštění UEFI**                  | Podporuje se. Virtuální počítače založené na rozhraní UEFI se migrují na virtuální počítače Azure generace 2.  |
| **Rozhraní UEFI – zabezpečené spouštění**         | Migrace se nepodporuje.|
| **Velikost disku**                  | 2 TB pro disk s operačním systémem (spouštěcí počítač BIOS), 4 TB pro disk s operačním systémem (spouštění UEFI), 4 TB pro datové disky.|
| **Číslo disku** | Maximálně 16 disků na virtuální počítač.|
| **Šifrované disky/svazky**    | Migrace se nepodporuje.|
| **RDM/průchozí disky**      | Migrace se nepodporuje.|
| **Sdílený disk** | Virtuální počítače používající sdílené disky se pro migraci nepodporují.|
| **NFS**                        | Svazky NFS připojené jako svazky na virtuálních počítačích se nebudou replikovat.|
| **ISCSI**                      | Virtuální počítače s cíli iSCSI se nepodporují pro migraci.
| **Cílový disk**                | Můžete migrovat jenom na virtuální počítače Azure se spravovanými disky. |
| **IPv6** | Nepodporováno|
| **Seskupování síťových adaptérů** | Nepodporováno|
| **Azure Site Recovery** | Pokud je virtuální počítač povolený pro replikaci pomocí Azure Site Recovery, nejde replikovat pomocí Azure Migrate migrace serveru.|
| **Přístavu** | Odchozí připojení na portu HTTPS 443 pro odesílání dat replikace virtuálních počítačů.|

### <a name="url-access-public-cloud"></a>Přístup k adrese URL (veřejný cloud)

Software zprostředkovatele replikace na hostitelích Hyper-V bude potřebovat přístup k těmto adresám URL.

**Adresa URL** | **Podrobnosti**
--- | ---
login.microsoftonline.com | Řízení přístupu a Správa identit pomocí služby Active Directory.
backup.windowsazure.com | Přenos a koordinace dat replikace.
*.hypervrecoverymanager.windowsazure.com | Používá se pro správu replikace.
*.blob.core.windows.net | Nahrajte data do účtů úložiště. 
dc.services.visualstudio.com | Nahrávat protokoly aplikací používané pro interní monitorování
time.windows.com | Ověřuje časovou synchronizaci mezi systémovým a globálním časem.

### <a name="url-access-azure-government"></a>Přístup k adrese URL (Azure Government)

Software zprostředkovatele replikace na hostitelích Hyper-V bude potřebovat přístup k těmto adresám URL.

**Adresa URL** | **Podrobnosti**
--- | ---
login.microsoftonline.us | Řízení přístupu a Správa identit pomocí služby Active Directory.
backup.windowsazure.us | Přenos a koordinace dat replikace.
*. hypervrecoverymanager.windowsazure.us | Používá se pro správu replikace.
*. blob.core.usgovcloudapi.net | Nahrajte data do účtů úložiště.
dc.services.visualstudio.com | Nahrávat protokoly aplikací používané pro interní monitorování
time.nist.gov | Ověřuje časovou synchronizaci mezi systémovým a globálním časem.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Všechny místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Velikost disku s operačním systémem | Až 2 048 GB. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet disků s operačním systémem | 1 | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet datových disků | 16 nebo méně. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost datového disku | Až 4 095 GB | Pokud je tato operace Nepodporovaná, ověřte chybu.
Síťové adaptéry | Podporuje se několik adaptérů. |
Sdílený virtuální pevný disk | Nepodporováno | Pokud je tato operace Nepodporovaná, ověřte chybu.
Disk FC | Nepodporováno | Pokud je tato operace Nepodporovaná, ověřte chybu.
BitLocker | Nepodporováno | Před povolením replikace pro počítač musí být BitLocker zakázán.
název virtuálního počítače | Od 1 do 63 znaků.<br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. |  Aktualizujte hodnotu ve vlastnostech počítače v Site Recovery.
Připojit po migraci – Windows | Připojení k virtuálním počítačům Azure s Windows po migraci:<br/><br/> – Před migrací povolte RDP na místním virtuálním počítači. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.<br/><br/> – Pro přístup typu Site-to-Site VPN Povolte protokol RDP a povolte RDP v **bráně Windows Firewall**  ->  **povolené aplikace a funkce** pro **domény a privátní** sítě. Dále ověřte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Přečtěte si další informace](prepare-for-migration.md). |
Připojit po migraci – Linux | Připojení k virtuálním počítačům Azure po migraci pomocí SSH:<br/><br/> – Před migrací na místním počítači ověřte, že je služba Secure Shell nastavená na Start a že pravidla brány firewall umožňují připojení SSH.<br/><br/> – Po migraci povolte na virtuálním počítači Azure příchozí připojení k portu SSH pro pravidla skupiny zabezpečení sítě na virtuálním počítači, u kterého došlo k převzetí služeb při selhání, a pro podsíť Azure, ke které je připojený. Kromě toho přidejte veřejnou IP adresu pro virtuální počítač. |  

## <a name="next-steps"></a>Další kroky

[Migrujte virtuální počítače Hyper-V](tutorial-migrate-hyper-v.md) pro migraci.
