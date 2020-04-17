---
title: Podpora migrace technologie Hyper-V v migraci Azure
description: Přečtěte si o podpoře migrace hyper-V pomocí Migrace Azure.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 8ec0b72cac75518ac938faa202b28d055409e8dc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538184"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matice podpory pro migraci Hyper-V

Tento článek shrnuje nastavení podpory a omezení pro migraci virtuálních počítačů Hyper-V s [migrací Azure: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool) . Pokud hledáte informace o hodnocení virtuálních počítačů Hyper-V pro migraci do Azure, přečtěte si [matici podpory hodnocení](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Omezení migrace

Můžete vybrat až 10 virtuálních můzí najednou pro replikaci. Pokud chcete migrovat více počítačů, replikujte ve skupinách po 10.


## <a name="hyper-v-hosts"></a>Hostitelé technologie Hyper-V

| **Podpora**                | **Podrobnosti**               
| :-------------------       | :------------------- |
| **Nasazení**       | Hostitel Hyper-V může být samostatný nebo nasazený v clusteru. <br/>Software pro replikaci Azure Migrate (zprostředkovatel replikace Hyper-V) se instaluje na hostitelích Hyper-V.|
| **Oprávnění**           | Potřebujete oprávnění správce pro hostitele Hyper-V. |
| **Hostitelský operační systém** | Windows Server 2019, Windows Server 2016 nebo Windows Server 2012 R2. |
| **Přístup k portu** |  Odchozí připojení na portu HTTPS 443 pro odesílání dat replikace virtuálního počítače.

### <a name="url-access-public-cloud"></a>Přístup k adresám URL (veřejný cloud)

Software poskytovatele replikace na hostitelích Technologie Hyper-V bude potřebovat přístup k těmto adresám URL.

**Adresa URL** | **Podrobnosti**
--- | ---
login.microsoftonline.com | Řízení přístupu a správa identit pomocí služby Active Directory.
backup.windowsazure.com | Přenos a koordinace replikačních dat.
*.hypervrecoverymanager.windowsazure.com | Používá se pro migraci.
*.blob.core.windows.net | Nahrajte data do účtů úložiště. 
dc.services.visualstudio.com | Nahrajte protokoly aplikací používané pro interní monitorování.
time.windows.com | Ověří synchronizaci času mezi systémem a globálním časem.

### <a name="url-access-azure-government"></a>Přístup k adresám URL (Azure Government)

Software poskytovatele replikace na hostitelích Technologie Hyper-V bude potřebovat přístup k těmto adresám URL.

**Adresa URL** | **Podrobnosti**
--- | ---
login.microsoftonline.us | Řízení přístupu a správa identit pomocí služby Active Directory.
backup.windowsazure.us | Přenos a koordinace replikačních dat.
*.hypervrecoverymanager.windowsazure.us | Používá se pro migraci.
*.blob.core.usgovcloudapi.net | Nahrajte data do účtů úložiště.
dc.services.visualstudio.com | Nahrajte protokoly aplikací používané pro interní monitorování.
time.nist.gov | Ověří synchronizaci času mezi systémem a globálním časem.


## <a name="hyper-v-vms"></a>Virtuální počítače Hyper-V

| **Podpora**                  | **Podrobnosti**               
| :----------------------------- | :------------------- |
| **Operační systém** | Všechny operační systémy [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) a [Linux,](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) které azure podporuje. |
| **Požadované změny pro Azure** | Některé virtuální počítače můžou vyžadovat změny, aby je můžou běžet v Azure. Před migrací proveďte úpravy ručně. Příslušné články obsahují pokyny, jak to udělat. |
| **Linux boot**                 | Pokud /boot je na vyhrazeném oddílu, by měl být umístěn na disku operačního systému a nesmí být rozloženy na více disků.<br/> Pokud /boot je součástí kořenového (/) oddílu, pak '/' oddíl by měl být na disku operačního systému a ne span jiné disky. |
| **UEFI bota**                  | Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač systému BIOS. Virtuální ms by měl být spuštěn windows server 2012 a novější pouze. Disk operačního systému by měl mít až pět oddílů nebo méně a velikost disku operačního systému by měla být menší než 300 GB.
  |
| **Velikost disku**                  | 2 TB pro disk operačního systému, 4 TB pro datové disky.
| **Číslo disku** | Maximálně 16 disků na virtuální modul.
| **Šifrované disky/svazky**    | Není podporováno pro migraci. |
| **Disky RDM/passthroughthrough**      | Není podporováno pro migraci. |
| **Sdílený disk** | Virtuální počítače používající sdílené disky nejsou pro migraci podporované.
| **NFS**                        | Svazky nfs připojené jako svazky na virtuálních počítačích nebudou replikovány. |
| **Iscsi**                      | Virtuální aplikace s cíli iSCSI nejsou pro migraci podporované.
| **Cílový disk**                | Můžete migrovat na virtuální počítače Azure se spravovanými disky jenom. |
| **IPv6** | Není podporováno.
| **Spojení nic** | Není podporováno.
| **Azure Site Recovery** | Pokud je virtuální počítač povolený pro replikaci pomocí Azure Site Recovery, nemůžete se replikovat pomocí migrace migrace serveru Azure.
| **Porty** | Odchozí připojení na portu HTTPS 443 pro odesílání dat replikace virtuálního počítače.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Všechny místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Velikost disku s operačním systémem | Až 2 048 GB. | Kontrola se nezdaří, pokud není podporována.
Počet disků s operačním systémem | 1 | Kontrola se nezdaří, pokud není podporována.
Počet datových disků | 16 nebo méně. | Kontrola se nezdaří, pokud není podporována.
Velikost datového disku | Až 4 095 GB | Kontrola se nezdaří, pokud není podporována.
Síťové adaptéry | Je podporováno více adaptérů. |
Sdílený virtuální pevný disk | Není podporováno. | Kontrola se nezdaří, pokud není podporována.
Disk FC | Není podporováno. | Kontrola se nezdaří, pokud není podporována.
BitLocker | Není podporováno. | Nástroj BitLocker musí být zakázán před povolením replikace pro počítač.
název virtuálního počítače | Od 1 do 63 znaků.<br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslem. |  Aktualizujte hodnotu ve vlastnostech počítače v obnovení webu.
Připojit se po migraci windows | Připojení k virtuálním počítačům Azure se systémem Windows po migraci:<br/> - Před migrací povolí RDP na místním virtuálním počítači. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.<br/> Pro přístup k síti VPN mezi lokalitami povolte protokol RDP a povolte protokol RDP v programu **Windows Firewall** -> **Povolené aplikace a funkce** pro domény a **soukromé** sítě. Kromě toho zkontrolujte, zda je zásada sítě SAN operačního systému nastavena na **onlineall**. [Další informace](prepare-for-migration.md). |
Připojit se po migraci-Linux | Připojení k virtuálním počítačům Azure po migraci pomocí SSH:<br/> Před migrací v místním počítači zkontrolujte, zda je služba Zabezpečené prostředí nastavena na start a zda pravidla brány firewall umožňují připojení SSH.<br/> Po převzetí služeb při selhání na virtuálním počítači Azure povolte příchozí připojení k portu SSH pro pravidla skupiny zabezpečení sítě pro převzetí služeb při selhání přes virtuální počítač a pro podsíť Azure, ke které je připojen. Kromě toho přidejte veřejnou IP adresu pro virtuální ho. |  

## <a name="next-steps"></a>Další kroky

[Migrace virtuálních virtuálních jevů Hyper-V](tutorial-migrate-hyper-v.md) pro migraci.
