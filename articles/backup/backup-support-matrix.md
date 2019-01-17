---
title: Matice podpory služby Azure Backup
description: Poskytuje přehled podpory nastavení a omezení pro službu Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b1fa723863e6485e977e075986c3779efed1e689
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360218"
---
# <a name="azure-backup-support-matrix"></a>Matice podpory služby Azure Backup

Můžete použít [služby Azure Backup](backup-overview.md) k zálohování dat do cloudu Microsoft Azure. Tento článek shrnuje podporu nastavení a omezení pro scénáře zálohování Azure a nasazení.

## <a name="vault-support"></a>Podpora trezoru

Azure používá zálohy, které trezory služby Recovery Services k organizování a správě zálohy a ukládat zálohovaná data.

**Nastavení** | **Podrobnosti**
--- | ---
Počet trezorů | Až 500 trezorů služby Recovery Services v rámci jednoho předplatného.
Počítače v trezoru | Až 1 000 virtuálních počítačů Azure v jednom trezoru.<br/><br/> Až 50 místní počítače, které běží Azure Backup agent (agent Microsoft Azure Recovery Services (MABS)) může být registrováno v jednom trezoru.
Zdroj dat v trezoru úložiště | Maximální 54400 GB. Neexistuje žádné omezení pro zálohy virtuálních počítačů Azure.
Zálohování do trezoru | Virtuální počítače Azure: jednou za den. Počítače chráněné službou DPM nebo MABS: dvakrát denně; Zálohování počítačů přímo pomocí agenta MARS: třikrát denně.  
Přesun trezoru | Zálohování trezory služby Recovery Services můžete přesunout mezi předplatné a skupiny prostředků. [Další informace](backup-azure-move-recovery-services-vault.md).
Přesun dat mezi trezorů | Přechod zálohovaných dat mezi trezory se nepodporuje.
Typ replikace úložiště | Typ replikace úložiště (GRS/LRS) pro trezor můžete upravit před zálohy jsou uložené. Po zálohování začne v trezoru, nelze změnit typ replikace.



## <a name="on-premises-backup-support"></a>Místní podpora zálohování 

Zde je, co je podporováno, pokud chcete zálohovat na místních počítačích.

**Počítač** | **Umístění** | **Zálohování** | **Funkce**
--- | --- | --- | ---
**Windows fyzické nebo virtuální (žádné záložního serveru)** | Soubory, složky, stav systému | Zálohovat do trezoru služby Recovery services | Zálohování třikrát denně.<br/><br/> Žádné zálohování s podporou aplikací.<br/><br/> Obnovení souboru, složky, svazku.
**Linux fyzické nebo virtuální (žádné záložního serveru)** | Zálohování není podporováno.
**Fyzické nebo virtuální s aplikací DPM** | Soubory, složky, svazky, stav systému, dat aplikací. | Zálohování aplikace DPM (na disk místně připojen k serveru DPM nebo na pásku.<br/><br/> Aplikace DPM se potom zálohuje do trezoru. | Snímky schopné rozeznávat aplikace<br/><br/> Členitost úplné zálohování a obnovení.<br/><br/> Linux pro virtuální počítače (Hyper-V nebo VMware) nepodporuje.<br/><br/>. Oracle nepodporuje.
**Fyzicky virtuální s MABS** | Soubory, složky, svazky, stav systému, dat aplikací. | Zálohovat na MABS (na disk místně připojených k serveru MABS. Páska se nepodporuje.<br/><br/> MABS se potom zálohuje do trezoru. | Snímky schopné rozeznávat aplikace<br/><br/> Členitost úplné zálohování a obnovení.<br/><br/> Linux pro virtuální počítače (Hyper-V nebo VMware) nepodporuje.<br/><br/>. Oracle nepodporuje.


## <a name="azure-vms"></a>Virtuální počítače Azure

### <a name="azure-vm-limits"></a>Omezení pro virtuální počítač v Azure

**Limit** | **Podrobnosti**
--- | ---
Azure datových disků virtuálního počítače | Limit 16.
Azure velikost datového disku virtuálního počítače | Jednotlivé disk může být až 4 095 GB.


### <a name="azure-vm-backup-options"></a>Možnosti zálohování Azure VM

Zde je, co je podporováno, pokud chcete zálohovat virtuální počítače Azure.

**Počítač** | **Umístění** | **Zálohování** | **Funkce**
--- | --- | --- | ---
**Virtuální počítače Azure (bez záložního serveru)** | Soubory, složky, stav systému | Zálohovat do trezoru. | Zálohování jednou denně.<br/><br/> Zálohování s podporou aplikací pro Windows, virtuální počítače konzistentní zálohování virtuálních počítačů s Linuxem. Můžete nakonfigurovat aplikace ani konzistence vzhledem k pro počítače s Linuxem pomocí vlastních skriptů.<br/><br/> Obnovte virtuální počítač nebo disk.<br/><br/> Nelze zálohovat na virtuálním počítači Azure do místního umístění.
**Virtuální počítač Azure s aplikací DPM** | Soubory, složky, svazky, stav systému, dat aplikací. | Zálohuje do aplikace DPM běžící na Azure (na disk místně připojen k serveru DPM. Páska se nepodporuje.<br/><br/> Aplikace DPM se potom zálohuje do trezoru. | Snímky schopné rozeznávat aplikace<br/><br/> Členitost úplné zálohování a obnovení.<br/><br/> Linux pro virtuální počítače (Hyper-V nebo VMware) nepodporuje.<br/><br/>. Oracle nepodporuje.
**Virtuální počítač Azure s MABS** | Soubory, složky, svazky, stav systému, dat aplikací. | Zálohovat na MABS běžící v Azure (na disk místně připojených k serveru MABS). Páska se nepodporuje.<br/><br/> MABS se potom zálohuje do trezoru. | Snímky schopné rozeznávat aplikace<br/><br/> Členitost úplné zálohování a obnovení.<br/><br/> Linux pro virtuální počítače (Hyper-V nebo VMware) nepodporuje.<br/><br/>. Oracle nepodporuje.





## <a name="linux-backup-support"></a>Podpora zálohování Linux

Zde je, co je podporováno, pokud chcete zálohování počítačů s Linuxem.

**Backup** | **Linuxu (schváleného Azure)**
--- | --- 
**Počítače s Linuxem v místním (bez DPM nebo MABS)**. | Ne. Agenta MARS lze nainstalovat pouze na počítačích s Windows. 
**Virtuální počítač Azure (bez DPM nebo MABS)** | Konzistentní zálohování pomocí [vlastní skripty](backup-azure-linux-app-consistent.md).<br/><br/> Obnovení na úrovni souboru.<br/><br/> Obnovte tak, že vytvoříte virtuální počítač z disku a bod obnovení.
**V místním počítači nebo Azure virtuální počítač s aplikací DPM** | Záloha s konzistentními soubory virtuálních počítačů hosta s Linuxem v Hyper-V a VMWaru<br/><br/> Obnovení virtuálního počítače pro virtuální počítače hosta s Linuxem v Hyper-V a VMwaru</br></br> Konzistentní zálohování není k dispozici pro virtuální počítače Azure
**Místní počítač nebo virtuální počítač s MABS Azure** | Záloha s konzistentními soubory virtuálních počítačů hosta s Linuxem v Hyper-V a VMWaru<br/><br/> Obnovení virtuálních počítačů Hyper-V a virtuálních počítačů VMWare s Linuxem hosta</br></br> Konzistentní zálohování není k dispozici pro virtuální počítače Azure.

## <a name="disk-support"></a>Podporu pro disky

Podpora odstranění duplicit disku je následujícím způsobem:
- Při odstraňování duplicitních dat na disku je podporovaných místních při použití aplikace DPM nebo MABs zálohovat virtuální počítače Hyper-V se systémem Windows. Windows Server odstraňuje duplicity disku (na úrovni hostitele) na virtuálních pevných discích (VHD), které jsou k virtuálnímu počítači připojené jako úložiště pro zálohu.
- Odstranění duplicit se nepodporuje v Azure pro žádné komponenty služby Backup. Pokud System Center DPM a Backup Server nasazené v Azure, nelze provést odstranění duplicit úložné disky připojené k virtuálnímu počítači.


## <a name="securityencryption-support"></a>Podpora šifrování a zabezpečení

Azure Backup podporuje šifrování přenášených i uložených dat:

Síťový provoz do Azure:
- Zálohování provoz ze serverů do trezoru služby Recovery Services je šifrován pomocí rozšířené šifrování Standard 256.
- Zálohovaná data se odesílají prostřednictvím zabezpečeného spojení HTTPS.
- Zálohovaná data se ukládají do trezoru služby Recovery Services v šifrovaném tvaru.
- Můžete mít jenom heslo k odemknutí těchto dat. Microsoft nelze dešifrovat data záloh a kdykoli.
    > [!WARNING]
    > Po nastavení trezoru, máte přístup jen vy k šifrovacímu klíči. Microsoft nikdy udržuje kopii a nemá přístup ke klíči. Pokud dojde ke ztrátě klíče, Microsoft nemůže zálohovaná data obnovit.
Zabezpečení dat:
- Při zálohování virtuálních počítačů Azure, budete muset nastavit šifrování *v rámci* virtuálního počítače.
- Azure Backup podporuje službu Azure Disk Encryption, která používá BitLocker na virtuálních počítačích s Windows a **dm-crypt** na virtuálních počítačích s Linuxem.
- Na straně back-endu Azure Backup používá [šifrování služby Azure Storage](../storage/common/storage-service-encryption.md), které chrání neaktivní uložená data.


**Počítač** | **Při přenosu** | **V klidovém stavu**
--- | --- | ---
Místní počítače Windows bez DPM nebo MABS | ![Ano][green] | ![Ano][green]
Virtuální počítače Azure | ![Ano][green] | ![Ano][green] 
Virtuální počítače na místní nebo Azure pomocí aplikace DPM | ![Ano][green] | ![Ano][green]
Na – místní nebo virtuální počítače Azure s MABS | ![Ano][green] | ![Ano][green]



## <a name="compression-support"></a>Podpora komprese

Backup podporuje kompresi provoz zálohování dle souhrnu v následující tabulce. Poznámky:

- Pro virtuální počítače Azure rozšíření virtuálního počítače čte data přímo z účtu úložiště Azure přes síť úložiště, takže není potřeba tento provoz komprimovat.
- Pokud používáte DPM nebo MABS lze komprimovat data předtím, než se zálohuje do DPM nebo MABS, ušetříte šířku pásma. 

**Počítač** | **Komprimovat MABS/DPM (TCP)** | **Komprese (HTTPS) do trezoru**
--- | --- | ---
Místní počítače Windows bez DPM nebo MABS | Není k dispozici | Ano
Virtuální počítače Azure | Není k dispozici | Není k dispozici
Virtuální počítače na místní nebo Azure pomocí aplikace DPM | ![Ano][green] | ![Ano][green]
Na – místní nebo virtuální počítače Azure s MABS | ![Ano][green] | ![Ano][green]



## <a name="retention-limits"></a>Limity uchování

**Nastavení** | **Omezení** 
--- | --- 
Maximální počet bodů obnovení na chráněnou instanci (počítače a úlohy | 9999
Maximální čas vypršení platnosti pro bod obnovení | Bez omezení
Maximální frekvence zálohování do DPM nebo MABS | Každých 15 minut pro SQL Server<br/><br/> Jednou hodinu pro ostatní úlohy.
Maximální frekvence zálohování do trezoru | Místní virtuální počítače Windows machines nebo Azure s MARS: tři za den<br/><br/> DPM/MABS: Dva za den<br/><br/> Zálohování virtuálních počítačů Azure: Jednou za den
Uchování bodu obnovení | Denně, týdně, měsíčně, ročně.
Maximální doba uchovávání | Závisí na četnosti zálohování.
Body obnovení na disku aplikace DPM nebo MABS | 64 pro souborové servery, 448 pro servery aplikací.<br/><br/> Body obnovení pásky neomezené pro místní aplikace DPM.

## <a name="next-steps"></a>Další postup

- [Zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md)
- [Zálohování počítačů Windows přímo](tutorial-backup-windows-server-to-azure.md), bez zálohování serveru.
- [Nastavit MABS](backup-azure-microsoft-azure-backup.md) pro zálohování do Azure a pak znovu úloh rozšiřování MABS.
- [Nastavení DPM](backup-azure-dpm-introduction.md) pro zálohování do Azure a pak zálohování úloh DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
