---
title: Matice podpory pro Azure Backup
description: Poskytuje přehled podpory nastavení a omezení pro službu Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 51bd4b935b32bea20d3f5de0b8cda62dfdbf07b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236623"
---
# <a name="azure-backup-support-matrix"></a>Matice podpory pro Azure Backup

Můžete použít [Azure Backup](backup-overview.md) zálohovat data na cloudové platformě Microsoft Azure. Tento článek shrnuje obecných nastavení a omezení pro scénáře zálohování Azure a nasazení.

K dispozici jsou další matice podpory:

- Matice podpory pro [zálohování virtuálních počítačů (VM) Azure](backup-support-matrix-iaas.md)
- Matice podpory pro zálohování pomocí [System Center Data Protection Manager (DPM) nebo Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Matice podpory pro zálohování pomocí [agenta Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Podpora trezoru

Azure Backup používá k organizování a správě zálohy trezory služby Recovery Services. Využívá také trezory ukládat zálohovaná data. 

Následující tabulka popisuje funkce trezory služby Recovery Services:

**Funkce** | **Podrobnosti**
--- | ---
**Trezorů v předplatném** | Až 500 trezorů služby Recovery Services v rámci jednoho předplatného.
**Počítače v trezoru** | Až 1 000 virtuálních počítačů Azure v jednom trezoru.<br/><br/> Až 50 MABS lze zaregistrovat servery v jednom trezoru.
**Zdroje dat v trezoru úložiště** | Maximální 54,400 GB. Neexistuje žádné omezení pro zálohy virtuálních počítačů Azure.
**Zálohování do trezoru** | **Virtuální počítače Azure:** Jednou za den.<br/><br/>**Počítače chráněné službou DPM nebo MABS:** Dvakrát denně.<br/><br/> **Počítače přímo pomocí agenta MARS zálohovat:** Třikrát denně. 
**Mezi trezorů záloh** | Zálohování je v rámci oblasti.<br/><br/> Je nutné ve všech oblastech Azure, která obsahuje virtuální počítače, které chcete zálohovat trezoru. Nelze zálohovat do jiné oblasti. 
**Přesun trezorů** | Je možné [přesun trezorů](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) napříč předplatnými nebo mezi skupinami prostředků ve stejném předplatném.
**Přesun dat mezi trezorů** | Přechod zálohovaných dat mezi trezory se nepodporuje.
**Změna typu trezoru úložiště** | Typ replikace úložiště (geograficky redundantní úložiště nebo místně redundantní úložiště) pro trezor můžete upravit před zálohy jsou uložené. Po zálohování začne v trezoru, nelze změnit typ replikace.

## <a name="on-premises-backup-support"></a>Místní podpora zálohování

Zde je, co je podporováno, pokud chcete zálohovat na místních počítačích:

**Počítač** | **Co se zálohuje** | **Umístění** | **Funkce**
--- | --- | --- | ---
**Přímé zálohování počítače s Windows pomocí agenta MARS** | Soubory, složky, stav systému | Zálohovat do trezoru služby Recovery Services. | Zálohování třikrát za den<br/><br/> Záloha s ohledem na aplikace<br/><br/> Obnovení souboru, složky, svazku
**Přímé zálohování počítač s Linuxem pomocí agenta MARS** | Zálohování není podporováno
**Zálohování do aplikace DPM** | Soubory, složky, svazky, stav systému, dat aplikací | Zálohovat na místní úložiště aplikace DPM. Aplikace DPM se potom zálohuje do trezoru. | Snímky schopné rozeznávat aplikace<br/><br/> Členitost úplné zálohování a obnovení<br/><br/> Linux podporované pro virtuální počítače (Hyper-V nebo VMware)<br/><br/> Oracle nepodporuje
**Zálohování do MABS** | Soubory, složky, svazky, stav systému, dat aplikací | Zálohovat na místní úložiště MABS. MABS se potom zálohuje do trezoru. | Snímky schopné rozeznávat aplikace<br/><br/> Členitost úplné zálohování a obnovení<br/><br/> Linux podporované pro virtuální počítače (Hyper-V nebo VMware)<br/><br/> Oracle nepodporuje

## <a name="azure-vm-backup-support"></a>Azure podporuje zálohování virtuálních počítačů

### <a name="azure-vm-limits"></a>Omezení pro virtuální počítač v Azure

**Limit** | **Podrobnosti**
--- | ---
**Azure datových disků virtuálního počítače** | Limit 16
**Azure velikost datového disku virtuálního počítače** | Jednotlivé disky můžou být až 4 095 GB

### <a name="azure-vm-backup-options"></a>Možnosti zálohování Azure VM

Zde je, co je podporováno, pokud chcete zálohovat virtuální počítače Azure:

**Počítač** | **Co se zálohuje** | **Umístění** | **Funkce**
--- | --- | --- | ---
**Zálohování virtuálních počítačů Azure pomocí rozšíření virtuálního počítače** | Celý virtuální počítač | Zálohujte do trezoru. | Rozšíření nainstaluje, když povolíte zálohování pro virtuální počítač.<br/><br/> Proveďte zálohu jednou za den.<br/><br/> Zálohování s podporou aplikací pro virtuální počítače s Windows; konzistentní zálohování virtuálních počítačů s Linuxem. Můžete nakonfigurovat aplikace ani konzistence vzhledem k pro počítače s Linuxem pomocí vlastních skriptů.<br/><br/> Obnovte virtuální počítač nebo disk.<br/><br/> Nelze zálohovat virtuální počítač Azure do místního umístění.
**Zálohování virtuálních počítačů Azure pomocí agenta MARS** | Soubory, složky | Zálohujte do trezoru. | Proveďte zálohu třikrát denně.<br/><br/> Pokud chcete zálohovat konkrétní soubory nebo složky, nikoli celý virtuální počítač, můžete spustit agenta MARS společně s rozšířením virtuálního počítače.
**Virtuální počítač Azure s aplikací DPM** | Soubory, složky, svazky, stav systému, dat aplikací | Zálohovat na místní úložiště virtuálního počítače Azure, na kterém běží aplikace DPM. Aplikace DPM se potom zálohuje do trezoru. | Snímky schopné rozeznávat aplikace.<br/><br/> Členitost úplné zálohování a obnovení.<br/><br/> Linux pro virtuální počítače (Hyper-V nebo VMware) nepodporuje.<br/><br/> Oracle nepodporuje.
**Virtuální počítač Azure s MABS** | Soubory, složky, svazky, stav systému, dat aplikací | Zálohovat na místní úložiště virtuálního počítače Azure, na kterém běží MABS. MABS se potom zálohuje do trezoru. | Snímky schopné rozeznávat aplikace.<br/><br/> Členitost úplné zálohování a obnovení.<br/><br/> Linux pro virtuální počítače (Hyper-V nebo VMware) nepodporuje.<br/><br/> Oracle nepodporuje.

## <a name="linux-backup-support"></a>Podpora zálohování Linux

Zde je, co je podporováno, pokud chcete zálohování počítačů s Linuxem:

**Typ zálohování** | **Linuxu (schváleného Azure)**
--- | ---
**Přímé zálohování v místním počítači se systémem Linux** | Nepodporuje se. Agenta MARS lze nainstalovat pouze na počítačích s Windows.
**Pomocí rozšíření agenta pro zálohování Virtuálního počítače se systémem Linux** | Zálohování konzistentní s aplikací s použitím [vlastní skripty](backup-azure-linux-app-consistent.md).<br/><br/> Obnovení na úrovni souboru.<br/><br/> Obnovte tak, že vytvoříte virtuální počítač z disku a bod obnovení.
**Použití DPM k zálohování v místním nebo virtuálním počítači Azure, na kterém běží Linux** | Konzistentní zálohování virtuálních počítačů hosta s Linuxem v Hyper-V a VMWare.<br/><br/> Obnovení virtuálních počítačů Hyper-V a VMWare virtuálních počítačů hosta s Linuxem.<br/><br/> Konzistentní zálohování není k dispozici pro virtuální počítač Azure.
**Použití MABS k zálohování v místním počítači nebo virtuálním počítači Azure, na kterém běží Linux** | Konzistentní zálohování virtuálních počítačů hosta s Linuxem v Hyper-V a VMWare.<br/><br/> Obnovení virtuálních počítačů Hyper-V a virtuálních počítačů VMWare s Linuxem hosta.<br/><br/> Konzistentní zálohování není k dispozici pro virtuální počítače Azure.

## <a name="daylight-saving-time-support"></a>Podpora letního času

Azure Backup nepodporuje úpravu automatické hodiny pro letní čas pro zálohy virtuálních počítačů Azure. Upravte zásady zálohování ručně podle potřeby.

## <a name="disk-deduplication-support"></a>Podpora odstranění duplicit disku

Podpora odstranění duplicit disku je následujícím způsobem:

- Odstranění duplicit disku je podporovaných místních, když používáte k zálohování virtuálních počítačů Hyper-V se systémem Windows Server DPM nebo MABs. Windows Server odstraňuje duplicity disku (na úrovni hostitele) na virtuální pevné disky (VHD), které jsou připojené k virtuálnímu počítači jako úložiště pro zálohování.
- Odstranění duplicit se nepodporuje v Azure pro žádné komponenty služby Backup. Když jsou v Azure nasazené aplikace DPM a MABS, nelze provést odstranění duplicit úložné disky připojené k virtuálnímu počítači.

## <a name="security-and-encryption-support"></a>Podpora šifrování a zabezpečení

Azure Backup podporuje šifrování přenášených i uložených dat.

### <a name="network-traffic-to-azure"></a>Síťový provoz do Azure

- Provoz zálohování z serverů do trezoru služby Recovery Services je šifrován pomocí rozšířené šifrování Standard 256.
- Zálohovaná data se odesílají prostřednictvím zabezpečeného spojení HTTPS.
- Zálohovaná data se ukládají do trezoru služby Recovery Services v šifrovaném tvaru.
- Můžete mít jenom heslo k odemknutí těchto dat. Microsoft nelze dešifrovat data záloh a kdykoli.

    > [!WARNING]
    > Po nastavení trezoru, máte přístup jen vy k šifrovacímu klíči. Microsoft nikdy udržuje kopii a nemá přístup ke klíči. Pokud dojde ke ztrátě klíče, Microsoft nemůže zálohovaná data obnovit.

### <a name="data-security"></a>Zabezpečení dat

- Pokud zálohujete virtuální počítače Azure, budete muset nastavit šifrování *v rámci* virtuálního počítače.
- Azure Backup podporuje službu Azure Disk Encryption, která používá BitLocker na virtuálních počítačích s Windows a **dm-crypt** na virtuálních počítačích s Linuxem.
- Na back-endu, Azure Backup používá [šifrování služby Azure Storage](../storage/common/storage-service-encryption.md), které chrání data v klidovém stavu.

**Počítač** | **Při přenosu** | **V klidovém stavu**
--- | --- | ---
**Místní počítače Windows bez DPM nebo MABS** | ![Ano][green] | ![Ano][green]
**Virtuální počítače Azure** | ![Ano][green] | ![Ano][green]
**Místní počítače Windows nebo virtuálních počítačů Azure pomocí DPM** | ![Ano][green] | ![Ano][green]
**Místní počítače Windows nebo virtuální počítače Azure s MABS** | ![Ano][green] | ![Ano][green]

## <a name="compression-support"></a>Podpora komprese

Backup podporuje kompresi provoz zálohování dle souhrnu v následující tabulce.

- Pro virtuální počítače Azure rozšíření virtuálního počítače čte data přímo z účtu úložiště Azure přes síť úložiště, takže není potřeba tento provoz komprimovat.
- Pokud používáte DPM nebo MABS, můžete šetřit šířku pásma pomocí komprese dat před jeho zálohování.

**Počítač** | **Komprimovat MABS/DPM (TCP)** | **Zkomprimujte do trezoru (HTTPS)**
--- | --- | ---
**Přímé zálohování počítačů s Windows v místním** | Není k dispozici | ![Ano][green]
**Zálohování virtuálních počítačů Azure pomocí rozšíření virtuálního počítače** | Není k dispozici | Není k dispozici
**Zálohování na počítačích typu místní nebo Azure s použitím MABS/aplikace DPM** | ![Ano][green] | ![Ano][green]

## <a name="retention-limits"></a>Limity uchování

**Nastavení** | **Omezení**
--- | ---
**Maximální počet bodů obnovení na chráněnou instanci (počítač nebo úloha)** | 9,999
**Maximální čas vypršení platnosti pro bod obnovení** | Bez omezení
**Maximální frekvence zálohování do DPM nebo MABS** | Každých 15 minut pro SQL Server<br/><br/> Jednou za hodinu pro ostatní úlohy
**Maximální frekvence zálohování do trezoru** | **Místní počítače Windows nebo virtuální počítače Azure s MARS:** Tři za den<br/><br/> **DPM/MABS:** Dva za den<br/><br/> **Zálohování virtuálních počítačů Azure:** Jedna za den
**Uchování bodu obnovení** | Denně, týdně, měsíčně, ročně
**Maximální doba uchovávání** | Závisí na četnosti zálohování
**Body obnovení na disku aplikace DPM nebo MABS** | 64 pro souborové servery; 448 pro servery aplikace <br/><br/>Neomezený počet pásek body obnovení pro místní aplikace DPM

## <a name="next-steps"></a>Další postup

- [Systém podpory replikace z revize](backup-support-matrix-iaas.md) pro zálohování virtuálních počítačů Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
