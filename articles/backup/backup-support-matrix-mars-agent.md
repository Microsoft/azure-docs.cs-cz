---
title: Matice podpory pro zálohování počítačů spuštěných pomocí služby Azure Backup agent Microsoft Azure Recovery Services (MARS)
description: Tento článek shrnuje podporu Azure Backup při zálohování počítače, na kterých běží agent Microsoft Azure Recovery Services (MARS).
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 3e2c6a550a9358656fd0870c7e785d131c5b6380
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894389"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matice podpory pro zálohování pomocí agenta Microsoft Azure Recovery Services (MARS)

Můžete použít [služby Azure Backup](backup-overview.md) pro zálohování místních počítačů a aplikací a k zálohování virtuálních počítačů Azure (VM). Tento článek shrnuje podporu nastavení a omezení při použití agenta Microsoft Azure Recovery Services (MARS) k zálohování počítačů.

## <a name="the-mars-agent"></a>Agenta MARS

Azure Backup používá agenta MARS zálohovat data z místních počítačů a virtuálních počítačů Azure do trezoru služby Recovery Services zálohování v Azure. Agenta MARS provádět následující akce:
- Spusťte na místních počítačích Windows tak, aby můžete zálohovat přímo na zálohování trezoru služby Recovery Services v Azure.
- Spuštění na virtuálních počítačích s Windows tak, aby můžete zálohovat přímo do trezoru.
- Spusťte na serveru System Center Data Protection Manager (DPM) nebo Microsoft Azure Backup Server (MABS). V tomto scénáři počítače a úlohy zálohování na DPM server nebo MABS. Agenta MARS tento server pak zálohuje do trezoru v Azure. 

Možnosti zálohování závisí na instalaci agenta. Další informace najdete v tématu [architektury Azure Backup pomocí agenta MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Informace o zálohování architektura MABS a aplikace DPM najdete v tématu [zpět do DPM nebo MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Viz také [požadavky](backup-support-matrix-mabs-dpm.md) pro zálohování architekturu.

**Instalace** | **Podrobnosti**
--- | ---
Stáhněte si nejnovější verzi agenta MARS | Nejnovější verzi agenta si můžete stáhnout z trezoru, nebo [přímo stahovat](https://aka.ms/azurebackup_agent).
Instalace přímo do počítače | Můžete nainstalovat agenta MARS přímo na Windows serveru v místním nebo na virtuálním počítači s Windows, na kterém běží některý [podporované operační systémy](https://docs.microsoft.com/en-us/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Nainstalujte na záložní server | Při nastavování aplikace DPM nebo MABS zálohování do Azure, stáhněte a nainstalujte agenta MARS na serveru. Agenta můžete nainstalovat na [podporované operační systémy](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) v matici podpory zálohování serveru.

> [!NOTE]
> Ve výchozím nastavení mají virtuální počítače Azure, které jsou povolené pro zálohování Azure Backup instalace rozšíření. Toto rozšíření zálohuje celý virtuální počítač. Můžete nainstalovat a spustit agenta MARS na Virtuálním počítači Azure společně s rozšířením, pokud chcete zálohovat specifických složek a souborů, namísto kompletního virtuálního počítače.
> Při spuštění agenta MARS na Virtuálním počítači Azure, zálohuje soubory nebo složky, které jsou v dočasné úložiště na virtuálním počítači. Zálohování se nezdaří, pokud soubory nebo složky se odeberou z dočasného úložiště nebo pokud je odebrán dočasné úložiště.


## <a name="cache-folder-support"></a>Podpora složky mezipaměti

Při použití agenta MARS zálohovat data agenta pořídí snímek dat a ukládá ho do místní mezipaměť složky, před odesláním dat do Azure. Složka mezipaměti (začátku) má několik požadavků:

**Mezipaměť** | **Podrobnosti**
--- | ---
Velikost |  Volné místo ve složce mezipaměti by měla být alespoň 5 až 10 procent celkové velikosti zálohovaných dat. 
Umístění | Složka mezipaměti musí být místně uložené na počítač, který se zálohuje a musí být online. Složka mezipaměti by neměly být ve sdílené síťové složce, vyměnitelného média nebo svazek offline. 
Složka | Složka mezipaměti by se měla šifrovat na svazek s odstraněnou duplicitou nebo ve složce, která je komprimován, který je zhuštěn nebo, který má spojovací bod.
Umístění změny | Zastavte modul zálohování můžete změnit umístění mezipaměti (`net stop bengine`) a kopírování složky mezipaměti k nové jednotce. (Zkontrolujte, že nový disk má dost volného místa.) Pak aktualizujte dvě položky registru v rámci **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** a **Config/CloudBackupProvider/ScratchLocation**) do nového umístění a restartujte modul.

## <a name="networking-and-access-support"></a>Podpora sítě a přístup

### <a name="url-access"></a>Přístup k adrese URL

Agenta MARS potřebuje přístup k těmto adresám URL:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Omezení podpory

**Funkce** | **Podrobnosti**
--- | ---
Řízení šířky pásma | Podporuje se. V agenta MARS pomocí **změnit vlastnosti** k úpravě šířky pásma.
Omezení využití sítě | Není k dispozici pro zálohování počítače se systémem Windows Server 2008 R2, Windows Server 2008 SP2 nebo Windows 7.

## <a name="support-for-direct-backups"></a>Podpora pro přímé zálohování

Vám pomůže agenta MARS zálohovat přímo do Azure v některých operačních systémech, které běží na místních počítačů a virtuálních počítačů Azure. Operační systémy musí být 64bitový a by měla být spuštěná nejnovější aktualizace Service Pack a aktualizace. V následující tabulce najdete souhrn těchto operačních systémů:

**Operační systém** | **Soubory nebo složky** | **Stav systému**
--- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ano | Ne
Windows 8.1 (Enterprise, Pro)| Ano |Ne
Windows 8 (Enterprise, Pro) | Ano | Ne
Windows 7 (Ultimate, Enterprise, Pro, kde je Premium nebo Basic, Starter) | Ano | Ne
Windows Server 2016 (Standard, Datacenter, Essentials) | Ano | Ano
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Ano | Ano
Windows Server 2012 (Standard, Datacenter, Foundation) | Ano | Ano
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Ano | Ano
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Ano | Ne
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Ano | Ne

Další informace najdete v tématu [operační systémy podporované MABS a aplikace DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Omezení zálohování

Azure Backup omezuje velikost souboru nebo složky zdroje dat, která se dají zálohovat. Položky, které zálohujete z jednoho svazku nesmí překročit velikost uvedené v této tabulce:

**Operační systém** | **Omezení velikosti**
--- | ---
Windows Server 2012 nebo novější |  54 400 GB
Windows Server 2008 R2 SP1 |    1,700 GB
Windows Server 2008 SP2 | 1,700 GB
Windows 8 nebo novější  | 54 400 GB
Windows 7   | 1,700 GB


## <a name="supported-file-types-for-backup"></a>Podporované typy souborů pro zálohování

**Typ** | **Podpora** 
--- | --- 
Šifrované   | Podporuje se. 
Komprimované | Podporuje se.
Řídké | Podporuje se.
Komprimované a řídké | Podporuje se.
Pevné odkazy  | Nepodporuje se. Vynecháno.
Spojovací bod   | Nepodporuje se. Vynecháno.
Šifrované a zhuštěné |  Nepodporuje se. Vynecháno.
Komprimovaný datový proud   | Nepodporuje se. Vynecháno.
Zhuštěný datový proud   | Nepodporuje se. Vynecháno.
OneDrive (synchronizované soubory jsou datové proudy zhuštěné)  | Nepodporuje se. 

## <a name="supported-drives-or-volumes-for-backup"></a>Podporované jednotky nebo svazky pro zálohování

**Jednotce nebo svazku** | **Podpora** | **Podrobnosti**
--- | --- | ---
Svazky jen pro čtení   | Nepodporuje se | Služba Stínová kopie svazku (VSS) funguje pouze v případě, že svazek je zapisovatelný.
Offline svazky | Nepodporuje se |   Služby VSS funguje pouze v případě, že svazek je online.
Sdílené síťové složky   | Nepodporuje se |   Svazek musí být místní na serveru.
Svazky chráněné nástrojem BitLocker | Nepodporuje se |   Před zahájením zálohování, musí být svazek odemčený.
Identifikace systému souborů  | Nepodporuje se |   Je podporován pouze v systému souborů NTFS.
Vyměnitelné médium | Nepodporuje se |   Musí mít všechny zdroje položek k zálohování *oprava* stav.
S odstraněnými duplicitními daty jednotky | Podporováno | Azure Backup převede s odstraněnými duplicitními daty data na normální data. Ho optimalizuje, šifruje, ukládá a posílá ji do trezoru.

## <a name="support-for-initial-offline-backup"></a>Podpora pro počáteční offline zálohu

Azure Backup podporuje *offline předvyplnění* pro přenos počáteční zálohovací data do Azure s použitím disků. Tato podpora je užitečné v případě prvotní zálohování by mohla být v rozsahu velikost terabajtů (TB). Zálohování offline platí pro:

- Přímé zálohování souborů a složek na místní počítače, na kterých běží agenta MARS.
- Zálohování úloh a souborů ze serveru DPM nebo MABS.

Zálohování offline nelze použít pro soubory stavu systému.

## <a name="support-for-data-restoration"></a>Podpora pro obnovení dat

S použitím [rychlé obnovení](backup-instant-restore-capability.md) funkce služby Azure Backup můžete obnovit data předtím, než je zkopírován do trezoru. Musí být počítač zálohujete spuštěn rozhraní .NET Framework 4.5.2 nebo novější.

Zálohování není možné obnovit do cílového počítače, na kterém běží starší verze operačního systému. Například může být obnovena záloha provést z počítače se systémem Windows 7 na Windows 8 nebo novější. Ale zálohování provést z počítače se systémem Windows 8 nelze obnovit v počítači se systémem Windows 7.

## <a name="next-steps"></a>Další postup
- Další informace o [architekturu, která používá agenta MARS zálohovat](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Zjistěte, co je podporovaná, až vám [spuštění agenta MARS na serveru aplikace DPM nebo MABS](backup-support-matrix-mabs-dpm.md).
