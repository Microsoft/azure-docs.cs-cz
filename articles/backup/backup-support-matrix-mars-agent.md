---
title: Matice podpory pro agenta Microsoft Azure Recovery Services (MARS) – Azure Backup
description: Tento článek shrnuje Azure Backup podporu při zálohování počítačů, na kterých běží agent služby Microsoft Azure Recovery Services (MARS).
author: dcurwin
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: dacurwin
manager: carmonm
ms.openlocfilehash: 8c983772f58c1ea01db175b47225ccfafa515b96
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951982"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matice podpory pro zálohování s agentem Microsoft Azure Recovery Services (MARS)

[Službu Azure Backup](backup-overview.md) můžete použít k zálohování místních počítačů a aplikací a k zálohování virtuálních počítačů Azure (VM). Tento článek shrnuje nastavení a omezení podpory při použití agenta Microsoft Azure Recovery Services (MARS) k zálohování počítačů.

## <a name="the-mars-agent"></a>Agent MARS

Azure Backup používá agenta MARS k zálohování dat z místních počítačů a virtuálních počítačů Azure do trezoru služby Backup Recovery Services v Azure. Agent MARS může:
- Spouštějte na místních počítačích s Windows, aby se mohly zálohovat přímo do trezoru služby Backup Recovery Services v Azure.
- Spusťte na virtuálních počítačích s Windows, aby se mohly zálohovat přímo do trezoru.
- Spusťte na serveru Microsoft Azure Backup (MABS) nebo na serveru System Center Data Protection Manager (DPM). V tomto scénáři se počítače a úlohy zálohují do MABS nebo na server DPM. Agent MARS pak zálohuje tento server do trezoru v Azure.

Možnosti zálohování závisí na tom, kde je agent nainstalovaný. Další informace najdete v tématu [architektura Azure Backup pomocí agenta Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Informace o architektuře zálohování MABS a DPM najdete v tématu [zálohování do DPM nebo MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Viz také [požadavky](backup-support-matrix-mabs-dpm.md) na architekturu zálohování.

**Instalace** | **Podrobnosti**
--- | ---
Stáhnout nejnovějšího agenta MARS | Nejnovější verzi agenta si můžete stáhnout z trezoru nebo [si ho stáhnout přímo](https://aka.ms/azurebackup_agent).
Instalace přímo na počítač | Agenta MARS můžete nainstalovat přímo na místní Windows Server nebo na virtuální počítač s Windows, na kterém běží některý z [podporovaných operačních systémů](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instalace na záložní server | Když nastavíte DPM nebo MABS pro zálohování do Azure, stáhnete a nainstalujete agenta MARS na server. Agenta můžete nainstalovat v [podporovaných operačních systémech](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) v matici podpory záložního serveru.

> [!NOTE]
> Ve výchozím nastavení mají virtuální počítače Azure s povoleným zálohováním instalaci rozšíření Azure Backup. Toto rozšíření zálohuje celý virtuální počítač. Můžete nainstalovat a spustit agenta MARS na virtuálním počítači Azure společně s příponou, pokud chcete zálohovat konkrétní složky a soubory, a ne celý virtuální počítač.
> Když spustíte agenta MARS na virtuálním počítači Azure, zálohuje soubory nebo složky, které jsou v dočasném úložišti virtuálního počítače. Zálohování se nezdaří, pokud jsou soubory nebo složky odebrány z dočasného úložiště nebo pokud je dočasné úložiště odstraněno.


## <a name="cache-folder-support"></a>Podpora složky mezipaměti

Když použijete agenta MARS k zálohování dat, agent pořizuje snímek dat a uloží ho do složky místní mezipaměti předtím, než odešle data do Azure. Složka cache (Scratch) má několik požadavků:

**Mezipaměť** | **Podrobnosti**
--- | ---
Size |  Volné místo ve složce mezipaměti by mělo být alespoň 5 až 10 procent celkové velikosti zálohovaných dat.
Location | Složka mezipaměti musí být uložená místně na počítači, který se zálohuje, a musí být online. Složka mezipaměti by neměla být ve sdílené síťové složce, na vyměnitelném médiu nebo na svazku offline.
Folder | Složka mezipaměti by měla být zašifrovaná na svazku s odstraněnými duplicitními daty nebo v komprimované složce, která je zhuštěná, nebo má bod rozboru.
Změny umístění | Umístění mezipaměti můžete změnit zastavením zálohovacího stroje (`net stop bengine`) a zkopírováním složky mezipaměti do nové jednotky. (Zajistěte, aby na nové jednotce bylo dost místa.) Pak aktualizujte dvě položky registru v části **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**config/ScratchLocation** a **config/CloudBackupProvider/ScratchLocation**) na nové místo a restartujte modul.

## <a name="networking-and-access-support"></a>Podpora sítí a přístupu

### <a name="url-access"></a>Přístup URL

Agent MARS potřebuje přístup k těmto adresám URL:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Podpora omezování

**Funkce** | **Podrobnosti**
--- | ---
Řízení šířky pásma | Podporuje se. V agentovi MARS upravte šířku pásma pomocí **možnosti změnit vlastnosti** .
Omezení sítě | Není k dispozici pro zálohované počítače se systémem Windows Server 2008 R2, Windows Server 2008 SP2 nebo Windows 7.

## <a name="support-for-direct-backups"></a>Podpora přímých záloh

Agenta MARS můžete použít k zálohování přímo do Azure v některých operačních systémech, které běží na místních počítačích a virtuálních počítačích Azure. Operační systémy musí být 64 bitů a měly by být spuštěny nejnovější aktualizace a aktualizace Service Pack. Následující tabulka shrnuje tyto operační systémy:

**Operační systém** | **Soubory/složky** | **Stav systému** 
--- | --- | --- 
Windows 10 (Enterprise, pro, Home) | Ano | Ne
Windows 8.1 (Enterprise, pro)| Ano |Ne
Windows 8 (Enterprise, Pro) | Ano | Ne
Windows 7 (Ultimate, Enterprise, pro, Home Premium/Basic, Starter) | Ano | Ne
Windows Server 2016 (Standard, Datacenter, Essentials) | Ano | Ano
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Ano | Ano
Windows Server 2012 (Standard, Datacenter, základ) | Ano | Ano
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, základ) | Ano | Ano
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Ano | Ne
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Ano | Ne

Další informace najdete v tématu [podporované MABS a operační systémy DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Omezení zálohování

Azure Backup omezuje velikost zdroje dat souboru nebo složky, které se dají zálohovat. Položky, které zálohujete z jednoho svazku, nemůžou překročit velikost shrnuté v této tabulce:

**Operační systém** | **Omezení velikosti**
--- | ---
Windows Server 2012 nebo novější |  54 400 GB
Windows Server 2008 R2 SP1 |    1 700 GB
Windows Server 2008 SP2 | 1 700 GB
Windows 8 nebo novější  | 54 400 GB
Windows 7   | 1 700 GB


## <a name="supported-file-types-for-backup"></a>Podporované typy souborů pro zálohování

**Typ** | **Podpora**
--- | ---
Zašifrováno   | Podporuje se.
Komprimované | Podporuje se.
Řídké | Podporuje se.
Komprimované a zhuštěné | Podporuje se.
Pevné odkazy  | Nepodporuje se. Přeskočeno.
Bod rozboru   | Nepodporuje se. Přeskočeno.
Šifrované a zhuštěné |  Nepodporuje se. Přeskočeno.
Komprimovaný datový proud   | Nepodporuje se. Přeskočeno.
Zhuštěný datový proud   | Nepodporuje se. Přeskočeno.
OneDrive (synchronizované soubory jsou zhuštěné streamy)  | Nepodporuje se.

## <a name="supported-drives-or-volumes-for-backup"></a>Podporované jednotky nebo svazky pro zálohování

**Jednotka/svazek** | **Podpora** | **Podrobnosti**
--- | --- | ---
Svazky jen pro čtení   | Nepodporuje se | Služba Stínová kopie svazku (VSS) funguje pouze v případě, že svazek je zapisovatelný.
Offline svazky | Nepodporuje se |   Služba Stínová kopie svazku funguje jenom v případě, že je svazek online.
Sdílená síťová složka   | Nepodporuje se |   Svazek musí být na serveru místní.
Svazky chráněné nástrojem BitLocker | Nepodporuje se |   Svazek musí být před zahájením zálohování odemčen.
Identifikace systému souborů  | Nepodporuje se |   Podporován je pouze systém souborů NTFS.
Vyměnitelná média | Nepodporuje se |   Všechny zdroje záložních položek musí mít *pevný* stav.
Jednotky s odstraněnými duplicitními daty | Podporováno | Azure Backup převede data s odstraněnými duplicitními daty na normální data. Optimalizuje, šifruje, ukládá a odesílá data do trezoru.

## <a name="support-for-initial-offline-backup"></a>Podpora počátečního zálohování offline

Azure Backup podporuje *osazení offline* pro přenos počátečních zálohovaných dat do Azure pomocí disků. Tato podpora je užitečná v případě, že počáteční záloha bude pravděpodobně v rozsahu velikosti terabajtů (TBs). Zálohování offline je podporováno pro:

- Přímé zálohování souborů a složek na místních počítačích, na kterých je spuštěný agent MARS.
- Zálohování úloh a souborů ze serveru DPM nebo MABS.

Offline zálohování nelze použít pro soubory stavu systému.

## <a name="support-for-data-restoration"></a>Podpora pro obnovení dat

Pomocí funkce [okamžitého obnovení](backup-instant-restore-capability.md) Azure Backup můžete data obnovit, než se zkopírují do trezoru. Počítač, který zálohujete, musí běžet .NET Framework 4.5.2 nebo novější.

Zálohy nelze obnovit do cílového počítače, na kterém je spuštěna dřívější verze operačního systému. Například záloha pořízená z počítače se systémem Windows 7 se dá obnovit v systému Windows 8 nebo novějším. Zálohy pořízené z počítače se systémem Windows 8 nelze obnovit v počítači se systémem Windows 7.

## <a name="next-steps"></a>Další postup
- Přečtěte si další informace o [architektuře zálohování, která používá agenta Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Zjistěte, co se podporuje při [spuštění agenta Mars v MABS nebo DPM serveru](backup-support-matrix-mabs-dpm.md).
