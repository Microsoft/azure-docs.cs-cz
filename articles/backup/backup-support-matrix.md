---
title: Matice podpory pro Azure Backup
description: Poskytuje souhrn nastavení podpory a omezení pro službu Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2019
ms.custom: references_regions
ms.openlocfilehash: ee37e994c9e72c3d3c98455360dc68746f49d278
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743127"
---
# <a name="support-matrix-for-azure-backup"></a>Matice podpory pro Azure Backup

[Azure Backup](backup-overview.md) můžete použít k zálohování dat na cloudovou platformu Microsoft Azure. Tento článek shrnuje obecná nastavení podpory a omezení pro Azure Backup scénáře a nasazení.

K dispozici jsou další matrice podpory:

- Matice podpory pro [zálohování virtuálních počítačů Azure](backup-support-matrix-iaas.md)
- Matice podpory pro zálohování pomocí nástroje [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Matice podpory pro zálohování pomocí [agenta Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Podpora trezoru

Azure Backup používá k orchestraci a správě záloh Recovery Services trezory. Používá taky trezory k ukládání zálohovaných dat.

Následující tabulka popisuje funkce trezorů Recovery Services:

**Funkce** | **Podrobnosti**
--- | ---
**Trezory v předplatném** | V jednom předplatném je až 500 Recovery Services trezory.
**Počítače v trezoru** | Až 2000 zdrojů dat napříč všemi úlohami (jako jsou virtuální počítače Azure, SQL Server virtuální počítače, servery MABS a tak dále) se dají chránit v jednom trezoru.<br><br>Až 1 000 virtuálních počítačů Azure v jednom trezoru.<br/><br/> V jednom trezoru se dají zaregistrovat až 50 serverů MABS.
**Zdroje dat** | Maximální velikost jednotlivého [zdroje dat](./backup-azure-backup-faq.md#how-is-the-data-source-size-determined) je 54 400 GB. Toto omezení se nevztahuje na zálohy virtuálních počítačů Azure. Na celkové množství dat, která můžete zálohovat do trezoru, neplatí žádná omezení.
**Zálohy do trezoru** | **Virtuální počítače Azure:** Jednou denně.<br/><br/>**Počítače chráněné aplikací DPM/MABS:** Dvakrát denně.<br/><br/> **Počítače zálohované přímo pomocí agenta Mars:** Třikrát denně.
**Zálohy mezi trezory** | Zálohování je v rámci jedné oblasti.<br/><br/> Potřebujete trezor v každé oblasti Azure, která obsahuje virtuální počítače, které chcete zálohovat. Nemůžete zálohovat do jiné oblasti.
**Přesunout trezory** | [Trezory můžete přesouvat](./backup-azure-move-recovery-services-vault.md) mezi předplatnými nebo mezi skupinami prostředků ve stejném předplatném. Přesun trezorů mezi oblastmi se ale nepodporuje.
**Přesun dat mezi trezory** | Přesouvání zálohovaných dat mezi trezory se nepodporuje.
**Upravit typ úložiště trezoru** | Před uložením záloh můžete upravit typ replikace úložiště (geograficky redundantní úložiště nebo místně redundantní úložiště) pro trezor. Po zahájení zálohování v trezoru se typ replikace nedá upravit.
**Zónově redundantní úložiště (ZRS)** | K dispozici v oblastech Velká Británie – jih (UKS) a Jižní Východní Asie (v moři).

## <a name="on-premises-backup-support"></a>Podpora místních záloh

Co je se podporuje, pokud chcete zálohovat místní počítače:

**Počítač** | **Co se zálohuje** | **Umístění** | **Funkce**
--- | --- | --- | ---
**Přímé zálohování počítače s Windows pomocí agenta MARS** | Soubory, složky, stav systému | Zálohujte do úložiště Recovery Services. | Zálohování třikrát za den<br/><br/> Žádné zálohování s podporou aplikací<br/><br/> Obnovení souboru, složky, svazku
**Přímá záloha počítače se systémem Linux s agentem MARS** | Zálohování není podporováno
**Zálohování do DPM** | Soubory, složky, svazky, stav systému, data aplikací | Zálohujte do místního úložiště DPM. DPM se pak zálohuje do trezoru. | Snímky s podporou aplikací<br/><br/> Úplná členitost pro zálohování a obnovení<br/><br/> Linux podporovaný pro virtuální počítače (Hyper-V/VMware)<br/><br/> Oracle není podporován
**Zálohování do MABS** | Soubory, složky, svazky, stav systému, data aplikací | Zálohujte do MABS místního úložiště. MABS se pak zálohuje do trezoru. | Snímky s podporou aplikací<br/><br/> Úplná členitost pro zálohování a obnovení<br/><br/> Linux podporovaný pro virtuální počítače (Hyper-V/VMware)<br/><br/> Oracle není podporován

## <a name="azure-vm-backup-support"></a>Podpora zálohování virtuálních počítačů Azure

### <a name="azure-vm-limits"></a>Omezení virtuálních počítačů Azure

**Počtu** | **Podrobnosti**
--- | ---
**Datové disky virtuálních počítačů Azure** | Podívejte se na [podporu pro zálohování virtuálních počítačů Azure](./backup-support-matrix-iaas.md#vm-storage-support).
**Velikost datového disku virtuálního počítače Azure** | Velikost jednotlivých disků může být až 32 TB a pro všechny disky ve virtuálním počítači je v kombinaci maximálně 256 TB.

### <a name="azure-vm-backup-options"></a>Možnosti zálohování virtuálních počítačů Azure

Co je se podporuje, pokud chcete zálohovat virtuální počítače Azure:

**Počítač** | **Co se zálohuje** | **Umístění** | **Funkce**
--- | --- | --- | ---
**Zálohování virtuálních počítačů Azure pomocí rozšíření virtuálního počítače** | Celý virtuální počítač | Zálohujte do trezoru. | Rozšíření nainstalované při povolení zálohování pro virtuální počítač.<br/><br/> Zálohujte se jednou denně.<br/><br/> Zálohování s podporou aplikací pro virtuální počítače s Windows; Zálohování konzistentní se soubory pro virtuální počítače se systémem Linux. Konzistenci aplikací pro počítače se systémem Linux můžete nakonfigurovat pomocí vlastních skriptů.<br/><br/> Obnovte virtuální počítač nebo disk.<br/><br/> Virtuální počítač Azure nejde zálohovat do místního umístění.
**Zálohování virtuálních počítačů Azure pomocí agenta MARS** | Soubory, složky, stav systému | Zálohujte do trezoru. | Zálohujte třikrát denně.<br/><br/> Pokud chcete zálohovat konkrétní soubory nebo složky namísto celého virtuálního počítače, můžete agenta MARS spustit společně s rozšířením virtuálního počítače.
**Virtuální počítač Azure s DPM** | Soubory, složky, svazky, stav systému, data aplikací | Zálohujte do místního úložiště virtuálního počítače Azure, na kterém běží DPM. DPM se pak zálohuje do trezoru. | Snímky s podporou aplikací.<br/><br/> Úplná členitost pro zálohování a obnovení.<br/><br/> Linux podporovaný pro virtuální počítače (Hyper-V/VMware).<br/><br/> Oracle není podporován.
**Virtuální počítač Azure s MABS** | Soubory, složky, svazky, stav systému, data aplikací | Zálohujte do místního úložiště virtuálního počítače Azure, na kterém běží MABS. MABS se pak zálohuje do trezoru. | Snímky s podporou aplikací.<br/><br/> Úplná členitost pro zálohování a obnovení.<br/><br/> Linux podporovaný pro virtuální počítače (Hyper-V/VMware).<br/><br/> Oracle není podporován.

## <a name="linux-backup-support"></a>Podpora zálohování pro Linux

Co je se podporuje, pokud chcete zálohovat počítače se systémem Linux:

**Typ zálohování** | **Linux (schváleno Azure)**
--- | ---
**Přímá záloha místního počítače se systémem Linux** | Nepodporováno Agenta MARS lze nainstalovat pouze do počítačů se systémem Windows.
**Použití rozšíření agenta pro zálohování virtuálního počítače Azure se systémem Linux** | Zálohování konzistentní s aplikací pomocí [vlastních skriptů](backup-azure-linux-app-consistent.md).<br/><br/> Obnovení na úrovni souborů.<br/><br/> Obnovte vytvořením virtuálního počítače z bodu obnovení nebo disku.
**Použití DPM k zálohování místních počítačů se systémem Linux** | Zálohování virtuálních počítačů hosta v systému Linux na technologii Hyper-V a VMware.<br/><br/> Obnovení virtuálních počítačů s virtuálními počítači hosta Hyper-V a VMware Linux.
**Zálohování místních počítačů se systémem Linux pomocí MABS** | Zálohování virtuálních počítačů hosta v systému Linux na technologii Hyper-V a VMware.<br/><br/> Obnovení virtuálních počítačů s virtuálními počítači hosta Hyper-V a VMware Linux.
**Použití MABS nebo DPM k zálohování virtuálních počítačů Azure se systémem Linux** | Nepodporováno

## <a name="daylight-saving-time-support"></a>Podpora letního času

Azure Backup nepodporuje automatické úpravy hodin pro letní čas pro zálohování virtuálních počítačů Azure. Neposune hodinu zálohy dopředu nebo dozadu. Pokud chcete zajistit, aby záloha běžela v požadované době, upravte zásady zálohování ručně podle potřeby.

## <a name="disk-deduplication-support"></a>Podpora odstranění duplicitních dat disku

Podpora odstranění duplicitních disků je následující:

- Odstranění duplicitních dat je podporováno v místním prostředí při použití DPM nebo MABS k zálohování virtuálních počítačů Hyper-V se systémem Windows. Windows Server provádí odstranění duplicitních dat (na úrovni hostitele) na virtuálních pevných discích (VHD), které jsou připojené k virtuálnímu počítači jako úložiště zálohování.
- Odstranění duplicitních dat se v Azure nepodporuje pro žádnou součást zálohování. Když jsou DPM a MABS nasazené v Azure, disky úložiště připojené k virtuálnímu počítači se nedají odstranit z duplicitních dat.

## <a name="security-and-encryption-support"></a>Podpora zabezpečení a šifrování

Azure Backup podporuje šifrování pro data v přenosu a na REST.

### <a name="network-traffic-to-azure"></a>Síťový provoz do Azure

- Provoz zálohování ze serverů do trezoru Recovery Services je zašifrovaný pomocí standard AES (Advanced Encryption Standard) 256.
- Zálohovaná data se odesílají přes zabezpečené připojení HTTPS.

### <a name="data-security"></a>Zabezpečení dat

- Zálohovaná data jsou uložená v trezoru Recovery Services v šifrované podobě.
- Když se data zálohují z místních serverů s agentem MARS, data se před odesláním do Azure Backup a dešifrují až po stažení z Azure Backup pomocí hesla.
- Při zálohování virtuálních počítačů Azure je potřeba nastavit šifrování *v rámci* virtuálního počítače.
- Azure Backup podporuje službu Azure Disk Encryption, která používá BitLocker na virtuálních počítačích s Windows a **dm-crypt** na virtuálních počítačích s Linuxem.
- Na back-endu Azure Backup používá [šifrování Azure Storage služby](../storage/common/storage-service-encryption.md), které chrání neaktivní neaktivní data.

**Počítač** | **Při přenosu** | **V klidovém umístění**
--- | --- | ---
**Místní počítače s Windows bez DPM/MABS** | ![Ano][green] | ![Ano][green]
**Virtuální počítače Azure** | ![Ano][green] | ![Ano][green]
**Místní počítače s Windows nebo virtuální počítače Azure s DPM** | ![Ano][green] | ![Ano][green]
**Místní počítače s Windows nebo virtuální počítače Azure s MABS** | ![Ano][green] | ![Ano][green]

## <a name="compression-support"></a>Kompresní podpora

Zálohování podporuje komprimaci provozu zálohování, jak je shrnuto v následující tabulce.

- V případě virtuálních počítačů Azure rozšíření virtuálního počítače načte data přímo z účtu úložiště Azure přes síť úložiště, takže není potřeba tento provoz komprimovat.
- Pokud používáte DPM nebo MABS, můžete ušetřit šířku pásma komprimací dat před jejich zálohováním.

**Počítač** | **Komprimovat do MABS/DPM (TCP)** | **Komprimovat do trezoru (HTTPS)**
--- | --- | ---
**Přímá záloha místních počítačů s Windows** | Není k dispozici | ![Ano][green]
**Zálohování virtuálních počítačů Azure pomocí rozšíření virtuálního počítače** | Není k dispozici | Není k dispozici
**Zálohování místních nebo Azure počítačů pomocí MABS/DPM** | ![Ano][green] | ![Ano][green]

## <a name="retention-limits"></a>Omezení uchování

**Nastavení** | **Omezení**
--- | ---
**Maximální počet bodů obnovení na chráněnou instanci (počítač nebo zatížení)** | 9 999
**Maximální doba vypršení platnosti bodu obnovení** | Bez omezení
**Maximální četnost zálohování pro DPM/MABS** | Každých 15 minut pro SQL Server<br/><br/> Jednou za hodinu pro jiné úlohy
**Maximální četnost zálohování do trezoru** | **Místní počítače s Windows nebo virtuální počítače Azure se spuštěným Mars:** Tři za den<br/><br/> **DPM/MABS:** Dva za den<br/><br/> **Zálohování virtuálních počítačů Azure:** Za jeden den
**Uchování bodu obnovení** | Denně, týdně, měsíčně, ročně
**Maximální doba uchovávání** | Závisí na četnosti zálohování
**Body obnovení na disku DPM/MABS** | 64 pro souborové servery; 448 pro aplikační servery <br/><br/>Neomezené body obnovení pásky pro místní DPM

## <a name="cross-region-restore"></a>Obnovení mezi oblastmi

Azure Backup přidal funkci obnovení mezi oblastmi k posílení dostupnosti dat a schopností odolnosti, což vám umožní úplné řízení obnovení dat do sekundární oblasti. Pokud chcete tuto funkci nakonfigurovat, přejděte [na článek nastavení obnovení mezi oblastmi.](backup-create-rs-vault.md#set-cross-region-restore) Tato funkce je podporována pro následující typy správy:

| Typ správy zálohování | Podporováno                                                    | Podporované oblasti |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Virtuální počítač Azure               | Ano.   Podporováno pro šifrované virtuální počítače a virtuální počítače s méně než 4 TB disků | Všechny veřejné oblasti Azure a cloudy svrchovan.  |
| SQL/SAP HANA | Ano                                                          | Všechny veřejné oblasti s výjimkou Francie |
| Agent MARS/místní  | Ne                                                           | Není k dispozici               |
| AFS (sdílené složky Azure)                 | Ne                                                           | Není k dispozici               |

## <a name="next-steps"></a>Další kroky

- [Seznamte se s maticí podpory](backup-support-matrix-iaas.md) pro zálohování virtuálních počítačů Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
