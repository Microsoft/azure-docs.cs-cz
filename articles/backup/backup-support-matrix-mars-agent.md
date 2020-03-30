---
title: Matice podpory pro agenta MARS
description: Tento článek shrnuje podporu azure backup při zálohování počítačů, které jsou spuštěny Microsoft Azure Recovery Services (MARS) agent.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247862"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matice podpory pro zálohování s agentem Microsoft Azure Recovery Services (MARS)

[Službu Azure Backup](backup-overview.md) můžete použít k zálohování místních počítačů a aplikací a k zálohování virtuálních počítačů (Virtuální počítače) Azure. Tento článek shrnuje nastavení a omezení podpory při použití agenta Služby obnovení Microsoft Azure (MARS) k zálohování počítačů.

## <a name="the-mars-agent"></a>Agent MARS

Azure Backup používá agenta MARS k zálohování dat z místních počítačů a virtuálních počítačů Azure do trezoru služby obnovení záloh v Azure. Agent MARS může:

- Spouštět místní počítače s Windows, aby mohly zálohovat přímo do trezoru služby Recovery Services zálohování v Azure.
- Spouštět na virtuálních počítačích s Windows, aby je bylo možné zálohovat přímo do trezoru.
- Spouštět na serveru Microsoft Azure Backup Server (MABS) nebo na serveru System Center Data Protection Manager (DPM). V tomto scénáři počítače a úlohy zálohovat mabs nebo na server DPM. Agent MARS pak zálohuje tento server do trezoru v Azure.

> [!NOTE]
>Azure Backup nepodporuje automatické nastavení hodin pro letní čas (DST). Upravte zásadu tak, aby bylo zajištěno, že letní čas je vzat v úvahu, aby se zabránilo nesouladu mezi skutečným časem a plánovaným časem zálohování.

Možnosti zálohování závisí na tom, kde je agent nainstalován. Další informace naleznete v [tématu Architektura zálohování Azure pomocí agenta MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Informace o architektuře zálohování MABS a DPM naleznete v [tématu Zálohování až dpm nebo MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Viz také [požadavky](backup-support-matrix-mabs-dpm.md) na architekturu zálohování.

**Instalace** | **Podrobnosti**
--- | ---
Stáhněte si nejnovějšího agenta MARS | Nejnovější verzi agenta si můžete stáhnout z trezoru nebo [si ji stáhnout přímo](https://aka.ms/azurebackup_agent).
Instalace přímo na počítači | Agenta MARS můžete nainstalovat přímo na místní server se systémem Windows nebo na virtuální počítač se systémem Windows, na který běží některý z [podporovaných operačních systémů](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Instalace na záložní server | Když nastavíte DPM nebo MABS pro zálohování do Azure, stáhnete a nainstalujete agenta MARS na server. Agenta můžete nainstalovat do [podporovaných operačních systémů](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) v matici podpory záložního serveru.

> [!NOTE]
> Ve výchozím nastavení mají virtuální počítače Azure, které jsou povolené pro zálohování, instalaci rozšíření Azure Backup. Toto rozšíření zálohuje celý virtuální ms. Agenta MARS můžete nainstalovat a spustit na virtuálním počítači Azure vedle rozšíření, pokud chcete zálohovat konkrétní složky a soubory, nikoli celý virtuální počítač.
> Když spustíte agenta MARS na virtuálním počítači Azure, zálohuje soubory nebo složky, které jsou v dočasném úložišti na virtuálním počítači. Zálohování se nezdaří, pokud jsou odebrány soubory nebo složky z dočasného úložiště nebo pokud je dočasné úložiště odebráno.

## <a name="cache-folder-support"></a>Podpora složky mezipaměti

Při použití agenta MARS k zálohování dat agent pořídí snímek dat a uloží je do složky místní mezipaměti před odesláním dat do Azure. Složka cache (scratch) má několik požadavků:

**Mezipaměti** | **Podrobnosti**
--- | ---
Velikost |  Volné místo ve složce mezipaměti by mělo být alespoň 5 až 10 procent celkové velikosti záložních dat.
Umístění | Složka mezipaměti musí být místně uložena v počítači, který je zálohován, a musí být online. Složka mezipaměti by neměla být ve sdílené síťové složce, na vyměnitelném médiu ani na svazku offline.
Složka | Složka mezipaměti by neměla být šifrována na svazku s odstraněním duplicit nebo ve složce, která je komprimovaná, řídká nebo má bod sazby.
Změny umístění | Umístění mezipaměti můžete změnit zastavením`net stop bengine`záložního modulu ( ) a zkopírováním složky mezipaměti na novou jednotku. (Ujistěte se, že nová jednotka má dostatek místa.) Potom aktualizujte dvě položky registru v části **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** a **Config/CloudBackupProvider/ScratchLocation**) do nového umístění a restartujte motor.

## <a name="networking-and-access-support"></a>Podpora sítí a přístupu

### <a name="url-and-ip-access"></a>Přístup k adrese URL a IP

Agent MARS potřebuje přístup k těmto adresám URL:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

A na tyto IP adresy:

- 20.190.128.0/18
- 40.126.0.0/18

Přístup ke všem výše uvedeným adresám URL a ADRESám IP používá protokol HTTPS na portu 443.

### <a name="azure-expressroute-support"></a>Podpora Azure ExpressRoute

Svá data můžete zálohovat přes Azure ExpressRoute pomocí veřejného partnerského vztahu (dostupného pro staré okruhy) a partnerského vztahu Microsoftu. Zálohování přes soukromý partnerský vztah není podporováno.

S veřejným partnerským vztahem: Zajistěte přístup k následujícím doménám/adresám:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

V partnerské společnosti Microsoft vyberte následující služby nebo oblasti a příslušné hodnoty komunity:

- Služba Azure Active Directory (12076:5060)
- Oblast Microsoft Azure (podle umístění trezoru služby Recovery Services)
- Azure Storage (podle umístění trezoru služby Recovery Services)

Další informace naleznete v [požadavcích na směrování ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Veřejný partnerský vztah je zastaralé pro nové okruhy.

### <a name="throttling-support"></a>Podpora pro Omezení

**Funkce** | **Podrobnosti**
--- | ---
Ovládání šířky pásma | Podporuje se. V agentovi MARS upravte šířku pásma pomocí **funkce Změnit vlastnosti.**
Omezení sítě | Není k dispozici pro zálohované počítače se systémem Windows Server 2008 R2, Windows Server 2008 SP2 nebo Windows 7.

## <a name="supported-operating-systems"></a>Podporované operační systémy

>[!NOTE]
> Agent MARS nepodporuje klíčové stoku jádra systému Windows Server.

Agent mars uvidíte k zálohování přímo do Azure v operačních systémech uvedených níže, které běží na:

1. Místní servery Windows
2. Virtuální počítače Azure s Windows

Operační systémy musí být 64 bit ů a měly by být spuštěny nejnovější balíčky služeb a aktualizace. Následující tabulka shrnuje tyto operační systémy:

**Operační systém** | **Soubory/složky** | **Stav systému** | **Požadavky na software/modul**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ano | Ne |  Zkontrolujte odpovídající verzi serveru, zda neobsahuje požadavky na software a modul.
Windows 8.1 (Enterprise, Pro)| Ano |Ne | Zkontrolujte odpovídající verzi serveru, zda neobsahuje požadavky na software a modul.
Windows 8 (Enterprise, Pro) | Ano | Ne | Zkontrolujte odpovídající verzi serveru, zda neobsahuje požadavky na software a modul.
Windows Server 2016 (standardní, datové centrum, základní informace) | Ano | Ano | - .NET 4,5 <br> - Prostředí Windows PowerShell <br> - Nejnovější kompatibilní Microsoft VC ++ Redistributable <br> - Konzola Pro správu společnosti Microsoft (MMC) 3.0
Windows Server 2012 R2 (standard, datové centrum, základy, základy) | Ano | Ano | - .NET 4,5 <br> - Prostředí Windows PowerShell <br> - Nejnovější kompatibilní Microsoft VC ++ Redistributable <br> - Konzola Pro správu společnosti Microsoft (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Ano | Ano |- .NET 4,5 <br> -Windows PowerShell <br> - Nejnovější kompatibilní Microsoft VC ++ Redistributable <br> - Konzola Pro správu společnosti Microsoft (MMC) 3.0 <br> - Nasazení image servis a správa (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (standardní, pracovní skupina) | Ano | Ne | - .NET 4,5 <br> - Prostředí Windows PowerShell <br> - Nejnovější kompatibilní Microsoft VC ++ Redistributable <br> - Konzola Pro správu společnosti Microsoft (MMC) 3.0
Windows Server 2019 (standardní, datové centrum, základní informace) | Ano | Ano | - .NET 4,5 <br> - Prostředí Windows PowerShell <br> - Nejnovější kompatibilní Microsoft VC ++ Redistributable <br> - Konzola Pro správu společnosti Microsoft (MMC) 3.0

Další informace naleznete v [tématu Podporované operační systémy MABS a DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Operační systémy na konci podpory

Následující operační systémy jsou na konci podpory a důrazně doporučujeme upgradovat operační systém, aby zůstal chráněn.

Pokud existující závazky brání upgradu operačního systému, zvažte migraci serverů Windows do virtuálních počítačů Azure a využijte zálohy virtuálních zařízení Azure, abyste zůstali chráněni. Další informace o migraci serveru Windows [naleznete na stránce migrace zde.](https://azure.microsoft.com/migration/windows-server/)

V místních nebo hostovaných prostředích, kde nemůžete upgradovat operační systém nebo migrovat do Azure, aktivujte rozšířené aktualizace zabezpečení pro počítače, aby zůstaly chráněné a podporované. Všimněte si, že pro rozšířené aktualizace zabezpečení jsou způsobilé pouze určité edice. Další informace najdete na [stránce s nejčastějšími](https://www.microsoft.com/cloud-platform/extended-security-updates) dotazy.

| **Operační systém**                                       | **Soubory/složky** | **Stav systému** | **Požadavky na software/modul**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Ano               | Ne                 | Zkontrolujte odpovídající verzi serveru, zda neobsahuje požadavky na software a modul. |
| Windows Server 2008 R2 (standard, podnik, datové centrum, založení) | Ano               | Ano                | - .NET 3,5, .NET 4,5 <br>  - Prostředí Windows PowerShell <br>  - Kompatibilní Microsoft VC ++ Redistributable <br>  - Konzola Pro správu společnosti Microsoft (MMC) 3.0 <br>  - Nasazení image servis a správa (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Ano               | Ne                 | - .NET 3,5, .NET 4,5 <br>  - Prostředí Windows PowerShell <br>  - Kompatibilní Microsoft VC ++ Redistributable <br>  - Konzola Pro správu společnosti Microsoft (MMC) 3.0 <br>  - Nasazení image servis a správa (DISM.exe) <br>  - Virtual Server 2005 základna + KB KB948515 |

## <a name="backup-limits"></a>Limity zálohování

### <a name="size-limits"></a>Omezení velikosti

Azure Backup omezuje velikost souboru nebo zdroje dat složky, který lze zálohovat. Položky, které zálohujete z jednoho svazku, nesmí překročit velikostsu shrnutého v této tabulce:

**Operační systém** | **Omezení velikosti**
--- | ---
Windows Server 2012 nebo novější |54 400 GB
Windows Server 2008 R2 SP1 |1 700 GB
Windows Server 2008 SP2| 1 700 GB
Windows 8 nebo novější| 54 400 GB
Windows 7| 1 700 GB

### <a name="other-limitations"></a>Další omezení

- MARS nepodporuje ochranu více počítačů se stejným názvem do jedné úschovny.

## <a name="supported-file-types-for-backup"></a>Podporované typy souborů pro zálohování

**Typ** | **Podpora**
--- | ---
Šifrované<sup>*</sup>| Podporuje se.
Komprimované | Podporuje se.
Řídké | Podporuje se.
Stlačený a řídké |Podporuje se.
Pevné odkazy| Není podporováno. Přeskočen.
Spojovací bod| Není podporováno. Přeskočen.
Šifrované a řídké |Není podporováno. Přeskočen.
Komprimovaný datový proud| Není podporováno. Přeskočen.
Řídký proud| Není podporováno. Přeskočen.
OneDrive (synchronizované soubory jsou řídké streamy)| Není podporováno.
Složky s povolenou službou Replikace distribuovaného systému souborů | Není podporováno.

\*Ujistěte se, že agent MARS má přístup k požadovaným certifikátům pro přístup k zašifrovaným souborům. Nepřístupné soubory budou přeskočeny.

## <a name="supported-drives-or-volumes-for-backup"></a>Podporované jednotky nebo svazky pro zálohování

**Jednotka/svazek** | **Podpora** | **Podrobnosti**
--- | --- | ---
Svazky jen pro čtení| Nepodporuje se | Služba VSS (Volume Copy Shadow Service) funguje pouze v případě, že je svazek zapisovatelný.
Offline svazky| Nepodporuje se |VSS funguje pouze v případě, že je svazek online.
Sdílená síť| Nepodporuje se |Svazek musí být místní na serveru.
Svazky uzamčené nástrojem BitLocker| Nepodporuje se |Svazek musí být před spuštěním zálohy odemčen.
Identifikace systému souborů| Nepodporuje se |Podporován je pouze systém souborů NTFS.
Vyměnitelné médium| Nepodporuje se |Všechny zdroje položek zálohování musí mít *pevný* stav.
Jednotky s odstraněním duplicit | Podporuje se | Azure Backup převede data s odstraněním duplicit na normální data. Optimalizuje, šifruje, ukládá a odesílá data do trezoru.

## <a name="support-for-initial-offline-backup"></a>Podpora počátečního zálohování offline

Azure Backup podporuje *offline osevací* k přenosu počátečních záložních dat do Azure pomocí disků. Tato podpora je užitečná, pokud je pravděpodobné, že počáteční záloha bude v rozsahu velikostí terabajtů (TBs). Zálohování offline je podporováno pro:

- Přímé zálohování souborů a složek v místních počítačích, na kterých je spuštěn agent MARS.
- Zálohování úloh a souborů ze serveru DPM nebo MABS.

Offline zálohování nelze použít pro soubory stavu systému.

## <a name="support-for-data-restoration"></a>Podpora obnovy dat

Pomocí funkce [okamžitéobnovení](backup-instant-restore-capability.md) služby Azure Backup můžete obnovit data před jejich zkopírováním do trezoru. Zařízení, které zálohujete, musí být spuštěno rozhraní .NET Framework 4.5.2 nebo vyšší.

Zálohy nelze obnovit do cílového počítače, ve které je spuštěna starší verze operačního systému. Například zálohu převzatou z počítače se systémem Windows 7 lze obnovit v systému Windows 8 nebo novějším. Ale zálohu převzatou z počítače se systémem Windows 8 nelze obnovit v počítači se systémem Windows 7.

## <a name="next-steps"></a>Další kroky

- Další informace o [architektuře zálohování, která používá agenta MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Zjistěte, co je podporováno při [spuštění agenta MARS na serveru MABS nebo DPM](backup-support-matrix-mabs-dpm.md).
