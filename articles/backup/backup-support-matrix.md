---
title: Matice podpory pro Azure Backup
description: Obsahuje souhrn nastavení podpory a omezení pro službu Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: 120882b15dcf9f27c280984ff6d0df31e38ebb73
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878948"
---
# <a name="support-matrix-for-azure-backup"></a>Matice podpory pro Azure Backup

[Azure Backup](backup-overview.md) můžete použít k zálohování dat na cloudovou platformu Microsoft Azure. Tento článek shrnuje obecné nastavení podpory a omezení pro scénáře a nasazení azure backup.

K dispozici jsou další podpůrné matice:

- Matice podpory pro [zálohování virtuálních zařízení (VM) Azure](backup-support-matrix-iaas.md)
- Matice podpory pro zálohování pomocí [správce ochrany dat system center (DPM) /Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Matice podpory pro zálohování pomocí [agenta Služby Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Podpora pro vault

Azure Backup používá trezory služby Recovery Services k orchestraci a správě záloh. Používá také trezory k ukládání zálohovaných dat.

Následující tabulka popisuje funkce trezorů služby Recovery Services:

**Funkce** | **Podrobnosti**
--- | ---
**Trezory v předplatném** | Až 500 trezorů služby Recovery Services v jednom předplatném.
**Stroje v trezoru** | Až 1 000 virtuálních počítačů Azure v jednom trezoru.<br/><br/> V jednom trezoru lze zaregistrovat až 50 serverů MABS.
**Zdroje dat** | Maximální velikost jednotlivého [zdroje dat](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) je 54 400 GB. Toto omezení se nevztahuje na zálohy virtuálních počítačích Azure. Na celkové množství dat, která můžete zálohovat do trezoru, se nevztahují žádná omezení.
**Zálohy do trezoru** | **Virtuální počítače Azure:** Jednou denně.<br/><br/>**Stroje chráněné dpm/mabs:** Dvakrát denně.<br/><br/> **Stroje zálohované přímo pomocí agenta MARS:** Třikrát denně.
**Zálohy mezi trezory** | Záloha je v rámci oblasti.<br/><br/> Potřebujete trezor v každé oblasti Azure, která obsahuje virtuální počítače, které chcete zálohovat. Nemůžete zálohovat do jiné oblasti.
**Přesunutí trezorů** | Trezory můžete [přesouvat](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) mezi předplatnými nebo mezi skupinami prostředků ve stejném předplatném. Přesouvání trezorů napříč oblastmi však není podporováno.
**Přesun dat mezi úschovnami** | Přesouvání zálohovaných dat mezi úschovnami není podporováno.
**Změna typu úložiště úschovny** | Před uložením záloh můžete upravit typ replikace úložiště (geograficky redundantní úložiště nebo místně redundantní úložiště) pro úložiště. Po spuštění záloh v úschovně nelze změnit typ replikace.

## <a name="on-premises-backup-support"></a>Místní podpora zálohování

Tady je co je podporované, pokud chcete zálohovat místní počítače:

**Počítač** | **Co je zálohováno** | **Umístění** | **Funkce**
--- | --- | --- | ---
**Přímé zálohování počítače se systémem Windows s agentem MARS** | Soubory, složky, stav systému | Záloha do trezoru služby Recovery Services. | Zálohování třikrát denně<br/><br/> Žádné zálohování zhodnošněné na aplikaci<br/><br/> Obnovení souboru, složky, svazku
**Přímé zálohování linuxového stroje s agentem MARS** | Zálohování není podporováno.
**Zálohování na dpm** | Soubory, složky, svazky, stav systému, data aplikací | Zálohovat do místního úložiště DPM. DPM pak zálohy do trezoru. | Snímky podporující aplikace<br/><br/> Úplná rozlišovací schopnost pro zálohování a obnovení<br/><br/> Linux je podporován pro virtuální počítače (Hyper-V/VMware)<br/><br/> Oracle není podporován
**Zálohovat na MABS** | Soubory, složky, svazky, stav systému, data aplikací | Záloha do místního úložiště MABS. MABS pak zálohy až do trezoru. | Snímky podporující aplikace<br/><br/> Úplná rozlišovací schopnost pro zálohování a obnovení<br/><br/> Linux je podporován pro virtuální počítače (Hyper-V/VMware)<br/><br/> Oracle není podporován

## <a name="azure-vm-backup-support"></a>Podpora zálohování virtuálních počítačů Azure

### <a name="azure-vm-limits"></a>Limity virtuálních počítačů Azure

**Limit** | **Podrobnosti**
--- | ---
**Datové disky virtuálních počítačů Azure** | Limit 16 <br> Pokud se chcete zaregistrovat k privátní verzi Preview virtuálních počítačů s víc než 16 disky (až 32 disků), napište nám na AskAzureBackupTeam@microsoft.com.
**Velikost datového disku virtuálního počítače Azure** | Velikost jednotlivých disků může být až 32 TB a maximálně 256 TB v kombinaci pro všechny disky ve virtuálním počítače.

### <a name="azure-vm-backup-options"></a>Možnosti zálohování virtuálních počítačů Azure

Tady je co je podporované, pokud chcete zálohovat virtuální počítače Azure:

**Počítač** | **Co je zálohováno** | **Umístění** | **Funkce**
--- | --- | --- | ---
**Zálohování virtuálních počítačových virtuálních počítačů pomocí rozšíření virtuálního počítače** | Celý virtuální virtuální mísa | Zpátky do trezoru. | Rozšíření nainstalované při povolení zálohování pro virtuální počítače.<br/><br/> Couvat jednou denně.<br/><br/> Zálohování s podporou aplikací pro virtuální počítače se systémem Windows; zálohování konzistentní se soubory pro virtuální počítače s Linuxem. Konzistenci aplikací pro počítače s Linuxem můžete nakonfigurovat pomocí vlastních skriptů.<br/><br/> Obnovení virtuálního počítače nebo disku.<br/><br/> Nelze zálohovat virtuální počítač Azure do místního umístění.
**Zálohování virtuálních počítačů Azure pomocí agenta MARS** | Soubory, složky, stav systému | Zpátky do trezoru. | Zpátky třikrát denně.<br/><br/> Pokud chcete zálohovat konkrétní soubory nebo složky, nikoli celý virtuální virtuální ms, může agent MARS běžet vedle rozšíření virtuálního soudu.
**Virtuální počítač Azure s dpm** | Soubory, složky, svazky, stav systému, data aplikací | Zálohování na místní úložiště virtuálního počítače Azure, který běží DPM. DPM pak zálohy do trezoru. | Snímky podporující aplikace.<br/><br/> Úplná rozlišovací schopnost pro zálohování a obnovení.<br/><br/> Linux podporuje virtuální počítače (Hyper-V/VMware).<br/><br/> Oracle není podporován.
**Virtuální počítač Azure s mabs** | Soubory, složky, svazky, stav systému, data aplikací | Zálohování na místní úložiště virtuálního počítače Azure, které se systémem MABS. MABS pak zálohy až do trezoru. | Snímky podporující aplikace.<br/><br/> Úplná rozlišovací schopnost pro zálohování a obnovení.<br/><br/> Linux podporuje virtuální počítače (Hyper-V/VMware).<br/><br/> Oracle není podporován.

## <a name="linux-backup-support"></a>Podpora zálohování Linuxu

Pokud chcete zálohovat linuxové počítače, co je podporováno, tady je to, co je podporováno:

**Typ zálohy** | **Linux (Azure schválila)**
--- | ---
**Přímé zálohování místního počítače se systémem Linux** | Není podporováno. Agenta MARS lze nainstalovat pouze na počítačích se systémem Windows.
**Použití rozšíření agenta k zálohování virtuálního počítače Azure se systémem Linux** | Zálohování konzistentní s aplikací pomocí [vlastních skriptů](backup-azure-linux-app-consistent.md).<br/><br/> Obnovení na úrovni souboru.<br/><br/> Obnovení vytvořením virtuálního počítače z bodu obnovení nebo disku.
**Zálohování místních počítačů s Linuxem pomocí aplikace DPM** | Zálohování virtuálních počítačů pro hostování v Linuxu v hyper-v a vmware konzistentníse soubory.<br/><br/> Obnovení virtuálních počítačů pro hostující virtuální počítače Hyper-V a VMWare Linux.
**Použití MABS k zálohování místních počítačů se systémem Linux** | Zálohování virtuálních počítačů pro hostování v Linuxu v hyper-v a vmware konzistentníse soubory.<br/><br/> Obnovení virtuálních počítačů pro hostující virtuální počítače Hyper-V a VMWare Linux.
**Zálohování virtuálních počítačů Linux Azure pomocí MABS nebo DPM** | Není podporováno.

## <a name="daylight-saving-time-support"></a>Podpora letního času

Azure Backup nepodporuje automatické nastavení hodin pro letní čas pro zálohování virtuálních her Azure. Neposune hodinu zálohování dopředu ani dozadu. Chcete-li zajistit spuštění zálohy v požadovaném čase, upravte zásady zálohování ručně podle potřeby.

## <a name="disk-deduplication-support"></a>Podpora odstranění duplicit disku

Podpora odstranění duplicit disku je následující:

- Odstranění duplicit disku je podporováno místně, pokud používáte dpm nebo mabs k zálohování virtuálních počítačů Hyper-V se systémem Windows. Windows Server provádí odstranění duplicit (na úrovni hostitele) na virtuálních pevných discích (VHD), které jsou připojené k virtuálnímu počítači jako úložiště záloh.
- Odstranění duplicit není v Azure podporováno pro žádnou součást Zálohování. Když se dpm a MABS nasazují v Azure, disky úložiště připojené k virtuálnímu počítači nelze deduplikovat.

## <a name="security-and-encryption-support"></a>Podpora zabezpečení a šifrování

Azure Backup podporuje šifrování pro data v přenosu a v klidovém stavu.

### <a name="network-traffic-to-azure"></a>Síťový provoz do Azure

- Záložní provoz ze serverů do trezoru služby Recovery Services je šifrován pomocí standardu Advanced Encryption Standard 256.
- Záložní data jsou odesílána přes zabezpečené propojení HTTPS.
- Záložní data jsou uložena v trezoru služby Recovery Services v šifrované podobě.
- Pouze vy máte přístupové heslo k odemknutí těchto dat. Společnost Microsoft nemůže kdykoli dešifrovat zálohovaná data.

    > [!WARNING]
    > Po nastavení trezoru máte přístup pouze k šifrovacímu klíči. Společnost Microsoft nikdy neudržuje kopii a nemá přístup ke klíči. Pokud je klíč chybně umístěn, společnost Microsoft nemůže obnovit záložní data.

### <a name="data-security"></a>Zabezpečení dat

- Když zálohujete virtuální počítače Azure, musíte nastavit šifrování *v rámci* virtuálního počítače.
- Azure Backup podporuje službu Azure Disk Encryption, která používá BitLocker na virtuálních počítačích s Windows a **dm-crypt** na virtuálních počítačích s Linuxem.
- Na back-endu Azure Backup používá [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), který chrání data v klidovém stavu.

**Počítač** | **V tranzitu** | **V klidu**
--- | --- | ---
**Místní počítače se systémem Windows bez dpm/mabs** | ![Ano][green] | ![Ano][green]
**Virtuální počítače Azure** | ![Ano][green] | ![Ano][green]
**Místní počítače s Windows nebo virtuální počítače Azure s dpm** | ![Ano][green] | ![Ano][green]
**Místní počítače s Windows nebo virtuální počítače Azure s MABS** | ![Ano][green] | ![Ano][green]

## <a name="compression-support"></a>Podpora komprese

Zálohování podporuje kompresi záložního provozu, jak je shrnuto v následující tabulce.

- Pro virtuální počítače Azure rozšíření virtuálních zařízení čte data přímo z účtu úložiště Azure přes síť úložiště, takže není nutné komprimovat tento provoz.
- Pokud používáte DPM nebo MABS, můžete ušetřit šířku pásma komprimací dat před zálohováním.

**Počítač** | **Komprese do MABS/DPM (TCP)** | **Komprese do trezoru (HTTPS)**
--- | --- | ---
**Přímé zálohování místních počítačů se systémem Windows** | Není k dispozici | ![Ano][green]
**Zálohování virtuálních počítačů Azure pomocí rozšíření virtuálních počítače** | Není k dispozici | Není k dispozici
**Zálohování v místních počítačích/počítačích Azure pomocí MABS/DPM** | ![Ano][green] | ![Ano][green]

## <a name="retention-limits"></a>Limity uchovávání informací

**Nastavení** | **Omezení**
--- | ---
**Maximální počet bodů obnovení na chráněnou instanci (počítač nebo zatížení)** | 9,999
**Maximální doba vypršení platnosti bodu obnovení** | Bez omezení
**Maximální frekvence zálohování na DPM/MABS** | Každých 15 minut pro SQL Server<br/><br/> Jednou za hodinu pro jiné úlohy
**Maximální frekvence zálohování do trezoru** | **Místní počítače s Windows nebo virtuální počítače Azure se systémem MARS:** Tři denně<br/><br/> **DPM/MABS:** Dva za den<br/><br/> **Zálohování virtuálních počítačů Azure:** Jeden za den
**Uchování bodu obnovení** | Denně, týdně, měsíčně, ročně
**Maximální doba uchovávání** | Závisí na četnosti zálohování
**Body obnovení na disku DPM/MABS** | 64 pro souborové servery; 448 pro aplikační servery <br/><br/>Neomezené body pro obnovení pásky pro místní aplikace DPM

## <a name="cross-region-restore"></a>Obnovení oblasti kříže

Azure Backup přidal funkci Obnovení napříč oblastmi, která posiluje dostupnost dat a možnost odolnosti proti chybám, což zákazníkům poskytuje plnou kontrolu nad obnovením dat do sekundární oblasti. Chcete-li tuto funkci nakonfigurovat, navštivte [článek Onastavit oblast kříže.](backup-create-rs-vault.md#set-cross-region-restore). Tato funkce je podporována pro následující typy správy:

| Typ správy zálohování | Podporuje se                                                    | Podporované oblasti |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Virtuální počítač Azure               | Ano.   Podporované pro šifrované virtuální počítače a virtuální počítače s disky s menší než 4 TB | Všechny veřejné oblasti Azure.  |
| MARS Agent/V areálu | Ne                                                           | –               |
| SQL /SAP HANA          | Ne                                                           | –               |
| Afs                    | Ne                                                           | –               |

## <a name="next-steps"></a>Další kroky

- [Zkontrolujte matici podpory](backup-support-matrix-iaas.md) pro zálohování virtuálních počítačích Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
