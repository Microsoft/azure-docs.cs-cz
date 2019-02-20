---
title: Matice podpory pro zálohování počítačů spuštěných pomocí služby Azure Backup agent Microsoft Azure Recovery Services (MARS)
description: Tento článek shrnuje podporu Azure Backup při zálohování počítače, které běží agent Microsoft Azure Recovery Services (MARS).
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9d0f751e8d0bc0275cc5fd2c47aaba7a6058931c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430784"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matice podpory pro zálohování pomocí agenta Microsoft Azure Recovery Services (MARS)

Můžete použít [služby Azure Backup](backup-overview.md) pro zálohování místních počítačů a aplikací a virtuálních počítačů Azure. Tento článek shrnuje podporu nastavení a omezení pro zálohování počítačů s agentem Microsoft Azure Recovery Services (MARS).

## <a name="about-the-mars-agent"></a>O agenta MARS

Agenta MARS používá Azure Backup k zálohování dat z místních počítačů a virtuálních počítačů Azure na zálohování služby Recovery Services vault v Azure. Agenta MARS je možné následujícím způsobem:
- Spuštění agenta na místních počítačích Windows tak, aby můžete zálohovat přímo na zálohování trezoru služby Recovery Services v Azure.
- Spuštění agenta na virtuálních počítačích Windows Azure tak, aby ho můžete zálohovat přímo do trezoru.
- Spusťte agenta Microsoft Azure Backup Server (MABS) nebo System Center Data Protection - server Manager (DPM). V tomto scénáři počítače a úlohy zálohování pro MABS/DPM a pak je založená na MABS/aplikace DPM zálohovat do trezoru v Azure pomocí agenta MARS. 

Co můžete zálohovat, závisí na instalaci agenta.

- [Další informace](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) o architektura zálohování pomocí agenta MARS.
- [Další informace]() o MABS/DPM [zálohování architektura](backup-architecture.md#architecture-back-up-to-dpmmabs)a [požadavky](backup-support-matrix-mabs-dpm.md).


## <a name="supported-installation"></a>Podporované instalace

**Instalace** | **Podrobnosti**
--- | ---
**Stáhněte si nejnovější verzi agenta MARS** | Nejnovější verzi agenta si můžete stáhnout z trezoru, nebo [přímo stahovat](https://aka.ms/azurebackup_agent).
**Instalace přímo do počítače** | Můžete nainstalovat přímo na Windows serveru v místním nebo virtuálním počítači Windows Azure s některým z agenta MARS [podporované operační systémy]().
**Nainstalujte na záložní server** | Při nastavování aplikace DPM nebo MABS zálohování do Azure, stáhněte a nainstalujte agenta MARS na serveru. Může být agent nainstalovaný v souladu s maticí [podporované operační systémy](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) v matici podpory zálohování serveru.

> [!NOTE]
> Ve výchozí virtuální počítače Azure povolené pro zálohování mají Azure Backup instalace rozšíření. Toto rozšíření zálohuje celý virtuální počítač. Můžete nainstalovat a spustit agenta MARS na Virtuálním počítači Azure společně s rozšířením, pokud chcete zálohovat specifických složek a souborů, namísto kompletního virtuálního počítače.
> Při spuštění agenta MARS na Virtuálním počítači Azure, jejich zálohování souborů a složek, které jsou umístěné v dočasné úložiště na virtuálním počítači. Zálohování se nezdaří, pokud souborů a složek se odeberou z dočasného úložiště nebo pokud je odebrán dočasné úložiště.


## <a name="cache-folder-support"></a>Podpora složky mezipaměti

Při zálohování pomocí agenta MARS agenta pořídí snímek dat a ukládá ho do složky místní mezipaměti, a to před odesláním do Azure. Složka mezipaměti (začátku) má několik požadavků.

**Mezipaměť** | **Podrobnosti**
--- | ---
**Velikost mezipaměti** |  Volné místo na velikost složky mezipaměti by měla být alespoň 5 až 10 % celkové velikosti vaše zálohovaná data. 
**Umístění mezipaměti** | Složka mezipaměti musí být místní vůči zálohovaný počítač a musí být online.<br/><br/> Složka mezipaměti by neměl být umístěn ve sdílené síťové složce, vyměnitelného média nebo svazek offline. 
**Složka mezipaměti** | Složka mezipaměti by se měla šifrovat, na svazek s odstraněním duplicit, nebo na složku, která je komprimované a zhuštěné /-bodu rozboru
**Změnit umístění mezipaměti** | Umístění mezipaměti můžete změnit modul zálohování (net stop bengine) se zastavuje a kopírování složky mezipaměti k nové jednotce (zkontrolujte má dost volného místa). Potom aktualizujte dvě položky registru v rámci HKLM\SOFTWARE\Microsoft\Windows Azure Backup (Config/ScratchLocation a konfigurace/CloudBackupProvider/ScratchLocation) do nového umístění a restartování stroje.

## <a name="networking-and-access-support"></a>Podpora sítě a přístup

### <a name="url-access"></a>Přístup k adrese URL

Agenta MARS potřebuje přístup k těmto adresám URL:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>Omezení podpory

**Funkce** | **Podrobnosti**
--- | ---
Řízení šířky pásma | Podporováno<br/><br/> Použití **změnit vlastnosti** agenta MARS k úpravě šířky pásma.
Omezení využití sítě | Není k dispozici, pro zálohovat počítače, které běží Windows Server 2008 R2, Windows Server 2008 SP2 nebo Windows 7.

## <a name="support-for-direct-backups"></a>Podpora pro přímé zálohování

Následující tabulka shrnuje, které operační systémy běžící na místních počítačů a virtuálních počítačů Azure můžete zálohovat přímo do Azure pomocí agenta MARS.

- Všechny operační systémy jsou 64-bit.
- Všechny operační systémy by měly běžet nejnovější aktualizace Service Pack a aktualizace.
- Podrobnosti o které aplikace DPM a MABS dají pomocí agenta MARS zálohovat servery, najdete v tématu [Tato tabulka](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

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
Windows Storage Server 2016/2012 R2/2012(Standard, Workgroup | Ano | Ne


## <a name="backup-limits"></a>Omezení zálohování

Azure Backup nastavený limit velikosti souboru nebo složky zdroje dat, který je možné zálohovat. Velikost položek vybraných k zálohování z jednoho svazku nesmí překročit velikost uvedené v tabulce.

**Operační systém** | **Omezení velikosti**
--- | ---
Windows Server 2012 nebo novější |  54 400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 nebo novější  | 54 400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>Podporované typy souborů pro zálohování

**Typ** | **Podporuje se** 
--- | --- 
Šifrované   | Ano 
Komprimované | Ano
Řídké | Ano 
Komprimované a řídké | Ano
Pevné odkazy  | Nepodporuje se<br/><br/> Přeskočeno
Spojovací bod   | Nepodporuje se<br/><br/> Přeskočeno
Šifrované a zhuštěné |  Nepodporuje se<br/><br/> Přeskočeno
Komprimovaný datový proud   | Nepodporuje se<br/><br/> Přeskočeno
Zhuštěný datový proud   | Nepodporuje se<br/><br/> Přeskočeno
Onedrive (synchronizovat soubory jsou datové proudy zhuštěné)    | Nepodporuje se 

## <a name="supported-drivesvolumes-for-backup"></a>Podporované jednotky/svazky pro zálohování

**Jednotce nebo svazku** | **Podporuje se** | **Podrobnosti**
--- | --- | ---
Svazky jen pro čtení   | Nepodporuje se | Svazek musí být zapisovatelný, stínové kopie svazku pro práci.
Offline svazky | Nepodporuje se |   Svazek musí být online pro stínové kopie svazku pro práci.
Sdílené síťové složky   | Nepodporuje se |   Svazek musí být místní na serveru pro zálohování.
Svazky chráněné nástrojem BitLocker | Nepodporuje se |   Svazek musíte odemknout před zálohování pro práci.
Identifikace systému souborů  | Nepodporuje se |   Pouze pro systém souborů NTFS.
Vyměnitelné médium | Nepodporuje se |   Všechny zdroje položek k zálohování musí mít stav pevné.
S odstraněnými duplicitními daty jednotky | Podporuje se.<br/><br/> Azure Backup převede s odstraněnými duplicitními daty data na normální data. To tato data optimalizuje, šifruje, ukládá a odesílá je do trezoru.

## <a name="support-for-initial-offline-backup"></a>Podpora pro počáteční offline zálohu

Azure Backup podporuje "offline předvyplnění" pro přenos počáteční zálohovací data do Azure s využitím disků. To je užitečné v případě prvotní zálohování by mohla být v rozsahu od terabajtů (TB). Zálohování offline platí pro:

- Přímé zálohování souborů a složek na místních počítačích agenta MARS.
- Zálohování úloh a souborů ze serveru DPM nebo MABS.
- Zálohování offline nelze použít pro soubory stavu systému.


## <a name="support-for-restore"></a>Podpora pro obnovení

- Nové [okamžitá obnova](/backup-instant-restore-capability.md) verze služby Azure Backup umožňuje obnovení dat, než je zkopírován do trezoru.<br/><br/> Pomocí této funkce zálohovaný počítač musí být spuštěná rozhraní .NET Framework 4.5.2 nebo novější.
- Zálohování není možné obnovit do cílového počítače starší verzí operačního systému. Například je možné obnovit zálohu z počítače Windows 7 v systému Windows 8 nebo novější. Nicméně zálohování provést z počítače se systémem Windows 8 nelze obnovit do počítače Windows 7.


## <a name="next-steps"></a>Další postup
- [Další informace](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) o architektura zálohování pomocí agenta MARS.
- [Přečtěte si](backup-support-matrix-mabs-dpm.md) co je podporováno při spuštění agenta MARS na Microsoft Azure Backup Server (MABS) nebo System Center DPM.


