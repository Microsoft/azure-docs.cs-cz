---
title: Matice podpory pro agenta MARS
description: Tento článek shrnuje Azure Backup podporu při zálohování počítačů, na kterých běží agent služby Microsoft Azure Recovery Services (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 26a47c2648d1307d2e7da2b25455f3f036cbf32d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997235"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matice podpory pro zálohování s agentem Microsoft Azure Recovery Services (MARS)

[Službu Azure Backup](backup-overview.md) můžete použít k zálohování místních počítačů a aplikací a k zálohování virtuálních počítačů Azure (VM). Tento článek shrnuje nastavení a omezení podpory při použití agenta Microsoft Azure Recovery Services (MARS) k zálohování počítačů.

## <a name="the-mars-agent"></a>Agent MARS

Azure Backup používá agenta MARS k zálohování dat z místních počítačů a virtuálních počítačů Azure do trezoru služby Backup Recovery Services v Azure. Agent MARS může:

- Spouštějte na místních počítačích s Windows, aby se mohly zálohovat přímo do trezoru služby Backup Recovery Services v Azure.
- Spusťte na virtuálních počítačích s Windows, aby se mohly zálohovat přímo do trezoru.
- Spusťte na serveru Microsoft Azure Backup (MABS) nebo na serveru System Center Data Protection Manager (DPM). V tomto scénáři se počítače a úlohy zálohují do MABS nebo na server DPM. Agent MARS pak zálohuje tento server do trezoru v Azure.

> [!NOTE]
>Azure Backup nepodporuje automatickou úpravu času pro letní čas (DST). Upravte zásady, abyste zajistili, že se budou brát v úvahu letní úspory, aby nedocházelo k nesouladu mezi skutečným časem a plánovaným časem zálohování.

Možnosti zálohování závisí na tom, kde je agent nainstalovaný. Další informace najdete v tématu [architektura Azure Backup pomocí agenta Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Informace o architektuře zálohování MABS a DPM najdete v tématu [zálohování do DPM nebo MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Viz také [požadavky](backup-support-matrix-mabs-dpm.md) na architekturu zálohování.

**Instalace** | **Podrobnosti**
--- | ---
Stáhnout nejnovějšího agenta MARS | Nejnovější verzi agenta si můžete stáhnout z trezoru nebo [si ho stáhnout přímo](https://aka.ms/azurebackup_agent).
Instalace přímo na počítač | Agenta MARS můžete nainstalovat přímo na místní Windows Server nebo na virtuální počítač s Windows, na kterém běží některý z [podporovaných operačních systémů](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).
Instalace na záložní server | Když nastavíte DPM nebo MABS pro zálohování do Azure, stáhnete a nainstalujete agenta MARS na server. Agenta můžete nainstalovat v [podporovaných operačních systémech](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) v matici podpory záložního serveru.

> [!NOTE]
> Ve výchozím nastavení mají virtuální počítače Azure s povoleným zálohováním instalaci rozšíření Azure Backup. Toto rozšíření zálohuje celý virtuální počítač. Můžete nainstalovat a spustit agenta MARS na virtuálním počítači Azure společně s příponou, pokud chcete zálohovat konkrétní složky a soubory, a ne celý virtuální počítač.
> Když spustíte agenta MARS na virtuálním počítači Azure, zálohuje soubory nebo složky, které jsou v dočasném úložišti virtuálního počítače. Zálohování se nezdaří, pokud jsou soubory nebo složky odebrány z dočasného úložiště nebo pokud je dočasné úložiště odstraněno.

## <a name="cache-folder-support"></a>Podpora složky mezipaměti

Když použijete agenta MARS k zálohování dat, agent pořizuje snímek dat a uloží ho do složky místní mezipaměti předtím, než odešle data do Azure. Složka cache (Scratch) má několik požadavků:

**Cache** | **Podrobnosti**
--- | ---
Velikost |  Volné místo ve složce mezipaměti by mělo být alespoň 5 až 10 procent celkové velikosti zálohovaných dat.
Umístění | Složka mezipaměti musí být uložená místně na počítači, který se zálohuje, a musí být online. Složka mezipaměti by neměla být ve sdílené síťové složce, na vyměnitelném médiu nebo na svazku offline.
Složka | Složka mezipaměti by neměla být zašifrovaná na svazku s odstraněnými duplicitními daty nebo v komprimované složce, která je zhuštěná, nebo má bod rozboru.
Změny umístění | Umístění mezipaměti můžete změnit zastavením zálohovacího stroje ( `net stop bengine` ) a zkopírováním složky mezipaměti do nové jednotky. (Zajistěte, aby na nové jednotce bylo dost místa.) Pak aktualizujte dvě položky registru v části **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**config/ScratchLocation** a **config/CloudBackupProvider/ScratchLocation**) na nové místo a restartujte modul.

## <a name="networking-and-access-support"></a>Podpora sítí a přístupu

### <a name="url-and-ip-access"></a>Adresa URL a přístup k IP adrese

Agent MARS potřebuje přístup k těmto adresám URL:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

A na tyto IP adresy:

- 20.190.128.0/18
- 40.126.0.0/18

Přístup ke všem adresám URL a IP adresám uvedeným výše používá protokol HTTPS na portu 443.

Při zálohování souborů a složek z virtuálních počítačů Azure pomocí agenta MARS musí být virtuální síť Azure taky nakonfigurovaná tak, aby povolovala přístup. Pokud používáte skupiny zabezpečení sítě (NSG), pomocí značky služby *AzureBackup* povolte odchozí přístup k Azure Backup. Kromě značky Azure Backup musíte také umožňovat připojení k ověřování a přenosu dat vytvořením podobných [pravidel NSG](../virtual-network/network-security-groups-overview.md#service-tags) pro Azure AD (*azureactivedirectory selhala*) a Azure Storage (*úložiště*). Následující kroky popisují proces vytvoření pravidla pro Azure Backup značku:

1. Ve **všech službách** klikněte na **skupiny zabezpečení sítě** a vyberte skupinu zabezpečení sítě.
2. V části **Nastavení** vyberte **odchozí pravidla zabezpečení** .
3. Vyberte **Přidat**. Zadejte všechny požadované podrobnosti pro vytvoření nového pravidla, jak je popsáno v [Nastavení pravidla zabezpečení](../virtual-network/manage-network-security-group.md#security-rule-settings). Ujistěte se, že možnost **cíl** je nastavená na *příznak služby* a **cílová značka služby** je nastavená na *AzureBackup*.
4. Vyberte **Přidat** a uložte nově vytvořené odchozí pravidlo zabezpečení.

Podobně můžete vytvořit NSG odchozí pravidla zabezpečení pro Azure Storage a Azure AD. Další informace o značkách služby najdete v [tomto článku](../virtual-network/service-tags-overview.md).

### <a name="azure-expressroute-support"></a>Podpora Azure ExpressRoute

Data můžete zálohovat přes Azure ExpressRoute s veřejným partnerským vztahem (k dispozici pro staré okruhy) a partnerským vztahem Microsoftu. Zálohování přes soukromý partnerský vztah se nepodporuje.

S veřejným partnerským vztahem: Zajistěte přístup k následujícím doménám nebo adresám:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

S partnerským vztahem Microsoftu vyberte následující služby nebo oblasti a příslušné hodnoty komunity:

- Azure Backup (podle umístění vašeho trezoru Recovery Services)
- Azure Active Directory (12076:5060)
- Azure Storage (podle umístění vašeho trezoru Recovery Services)

Další informace najdete v tématu [požadavky na směrování ExpressRoute](../expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>Veřejné partnerské vztahy se pro nové okruhy zastaraly.

### <a name="private-endpoint-support"></a>Podpora privátních koncových bodů

Pomocí privátních koncových bodů teď můžete bezpečně zálohovat data ze serverů do trezoru Recovery Services. Vzhledem k tomu, že Azure Active Directory v současné době nepodporuje privátní koncové body, IP adresy a plně kvalifikované názvy domény, které vyžaduje Azure Active Directory, bude nutné povolit odchozí přístup samostatně.

Když použijete agenta MARS k zálohování místních prostředků, ujistěte se, že vaše místní síť (obsahující vaše prostředky, které se mají zálohovat) má partnerský vztah s virtuální sítí Azure, která obsahuje soukromý koncový bod pro trezor. Pak můžete pokračovat v instalaci agenta MARS a nakonfigurovat zálohování. Je však třeba zajistit, aby veškerá komunikace pro zálohování procházela pouze prostřednictvím partnerské sítě.

Pokud po registraci agenta MARS pro trezor odeberete privátní koncové body, budete muset kontejner znovu zaregistrovat do trezoru. Nemusíte pro ně zastavovat ochranu.

Přečtěte si další informace o [privátních koncových bodech pro Azure Backup](private-endpoints.md).

### <a name="throttling-support"></a>Podpora omezování

**Funkce** | **Podrobnosti**
--- | ---
Řízení šířky pásma | Podporuje se. V agentovi MARS upravte šířku pásma pomocí **možnosti změnit vlastnosti** .
Omezení sítě | Není k dispozici pro zálohované počítače se systémem Windows Server 2008 R2, Windows Server 2008 SP2 nebo Windows 7.

## <a name="supported-operating-systems"></a>Podporované operační systémy

>[!NOTE]
> Agent MARS nepodporuje SKU jádra systému Windows Server.

Můžete použít agenta MARS k zálohování přímo do Azure v operačních systémech uvedených níže:

1. Místní Windows servery
2. Virtuální počítače Azure s Windows

Operační systémy musí být 64 bitů a měly by být spuštěny nejnovější aktualizace a aktualizace Service Pack. Následující tabulka shrnuje tyto operační systémy:

**Operační systém** | **Soubory/složky** | **Stav systému** | **Požadavky na software/modul**
--- | --- | --- | ---
Windows 10 (Enterprise, pro, Home) | Yes | No |  Ověřte odpovídající verzi serveru pro software/modul požadavky.
Windows 8.1 (Enterprise, pro)| Yes |No | Ověřte odpovídající verzi serveru pro software/modul požadavky.
Windows 8 (Enterprise, pro) | Yes | No | Ověřte odpovídající verzi serveru pro software/modul požadavky.
Windows Server 2016 (Standard, Datacenter, Essentials) | Yes | Yes | – .NET 4,5 <br> – Windows PowerShell <br> -Nejnovější kompatibilní Microsoft VC + + distribuovatelné <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Yes | Yes | – .NET 4,5 <br> – Windows PowerShell <br> -Nejnovější kompatibilní Microsoft VC + + distribuovatelné <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2012 (Standard, Datacenter, základ) | Yes | Yes |– .NET 4,5 <br> – Windows PowerShell <br> -Nejnovější kompatibilní Microsoft VC + + distribuovatelné <br> – Microsoft Management Console (MMC) 3,0 <br> – Údržba a Správa imagí nasazení (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, pracovní skupina) | Yes | No | – .NET 4,5 <br> – Windows PowerShell <br> -Nejnovější kompatibilní Microsoft VC + + distribuovatelné <br> – Microsoft Management Console (MMC) 3,0
Windows Server 2019 (Standard, Datacenter, Essentials) | Yes | Yes | – .NET 4,5 <br> – Windows PowerShell <br> -Nejnovější kompatibilní Microsoft VC + + distribuovatelné <br> – Microsoft Management Console (MMC) 3,0

Další informace najdete v tématu [podporované MABS a operační systémy DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Operační systémy na konci podpory

Následující operační systémy jsou na konci podpory a důrazně se doporučuje upgradovat operační systém, aby zůstal i nadále chráněný.

Pokud stávající závazky brání upgradu operačního systému, zvažte migraci serverů Windows na virtuální počítače Azure a využití záloh virtuálních počítačů Azure pro pokračování v zachování ochrany. Další informace o migraci Windows serveru najdete na [stránce věnované migraci](https://azure.microsoft.com/migration/windows-server/) .

U místních nebo hostovaných prostředí, kde nemůžete upgradovat operační systém ani migrovat na Azure, aktivujte u těchto počítačů rozšířené aktualizace zabezpečení, abyste mohli i nadále chránit a podporovat. Všimněte si, že pro rozšířené aktualizace zabezpečení jsou vhodné jenom konkrétní edice. Další informace najdete na [stránce s nejčastějšími dotazy](https://www.microsoft.com/windows-server/extended-security-updates) .

| **Operační systém**                                       | **Soubory/složky** | **Stav systému** | **Požadavky na software/modul**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, pro, Home Premium/Basic, Starter) | Yes               | No                 | Ověřte odpovídající verzi serveru pro software/modul požadavky. |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, základ) | Yes               | Yes                | – .NET 3,5, .NET 4,5 <br>  – Windows PowerShell <br>  -Kompatibilní Microsoft VC + + Redistributable <br>  – Microsoft Management Console (MMC) 3,0 <br>  – Údržba a Správa imagí nasazení (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, základ)  | Yes               | No                 | – .NET 3,5, .NET 4,5 <br>  – Windows PowerShell <br>  -Kompatibilní Microsoft VC + + Redistributable <br>  – Microsoft Management Console (MMC) 3,0 <br>  – Údržba a Správa imagí nasazení (DISM.exe) <br>  – Virtual Server 2005 Base + KB KB948515 |

## <a name="backup-limits"></a>Omezení zálohování

### <a name="size-limits"></a>Omezení velikosti

Azure Backup omezuje velikost zdroje dat souboru nebo složky, které se dají zálohovat. Položky, které zálohujete z jednoho svazku, nemůžou překročit velikost shrnuté v této tabulce:

**Operační systém** | **Omezení velikosti**
--- | ---
Windows Server 2012 nebo novější |54 400 GB
Windows Server 2008 R2 SP1 |1 700 GB
Windows Server 2008 SP2| 1 700 GB
Windows 8 nebo novější| 54 400 GB
Windows 7| 1 700 GB

### <a name="minimum-retention-limits"></a>Minimální limity uchovávání

Níže jsou uvedeny minimální doby uchovávání, které lze nastavit pro různé body obnovení:

|Bod obnovení |Doba trvání  |
|---------|---------|
|Denní bod obnovení    |   7 dní      |
|Týdenní bod obnovení     |    4 týdny     |
|Měsíční bod obnovení    |   3 měsíce      |
|Roční bod obnovení  |      1 rok   |

### <a name="other-limitations"></a>Další omezení

- MARS nepodporuje ochranu více počítačů se stejným názvem na jeden trezor.

## <a name="supported-file-types-for-backup"></a>Podporované typy souborů pro zálohování

**Typ** | **Podpora**
--- | ---
Šifrovaná<sup>*</sup>| Podporuje se.
Komprimované | Podporuje se.
Řídké | Podporuje se.
Komprimované a zhuštěné |Podporuje se.
Pevné odkazy| Nepodporováno Přeskočeno.
Spojovací bod| Nepodporováno Přeskočeno.
Šifrované a zhuštěné |Nepodporováno Přeskočeno.
Komprimovaný datový proud| Nepodporováno Přeskočeno.
Zhuštěný datový proud| Nepodporováno Přeskočeno.
OneDrive (synchronizované soubory jsou zhuštěné streamy)| Nepodporováno
Složky s povoleným Replikace DFS | Nepodporováno

\* Zajistěte, aby měl agent MARS přístup k požadovaným certifikátům pro přístup k šifrovaným souborům. Nedostupné soubory se přeskočí.

## <a name="supported-drives-or-volumes-for-backup"></a>Podporované jednotky nebo svazky pro zálohování

**Jednotka/svazek** | **Podpora** | **Podrobnosti**
--- | --- | ---
Svazky jen pro čtení| Nepodporováno | Služba Stínová kopie svazku (VSS) funguje pouze v případě, že svazek je zapisovatelný.
Offline svazky| Nepodporováno |Služba Stínová kopie svazku funguje jenom v případě, že je svazek online.
Sdílená síťová složka| Nepodporováno |Svazek musí být na serveru místní.
Svazky zamčené nástrojem BitLocker| Nepodporováno |Svazek musí být před zahájením zálohování odemčen.
Identifikace systému souborů| Nepodporováno |Podporován je pouze systém souborů NTFS.
Vyměnitelná média| Nepodporováno |Všechny zdroje záložních položek musí mít *pevný* stav.
Jednotky s odstraněnými duplicitními daty | Podporováno | Azure Backup převede data s odstraněnými duplicitními daty na normální data. Optimalizuje, šifruje, ukládá a odesílá data do trezoru.

## <a name="support-for-initial-offline-backup"></a>Podpora počátečního zálohování offline

Azure Backup podporuje *osazení offline* pro přenos počátečních zálohovaných dat do Azure pomocí disků. Tato podpora je užitečná v případě, že počáteční záloha bude pravděpodobně v rozsahu velikosti terabajtů (TBs). Zálohování offline je podporováno pro:

- Přímé zálohování souborů a složek na místních počítačích, na kterých je spuštěný agent MARS.
- Zálohování úloh a souborů ze serveru DPM nebo MABS.

Offline zálohování nelze použít pro soubory stavu systému.

## <a name="support-for-data-restoration"></a>Podpora pro obnovení dat

Pomocí funkce [okamžitého obnovení](backup-instant-restore-capability.md) Azure Backup můžete data obnovit, než se zkopírují do trezoru. Počítač, který zálohujete, musí běžet .NET Framework 4.5.2 nebo novější.

Zálohy nelze obnovit do cílového počítače, na kterém je spuštěna dřívější verze operačního systému. Například záloha pořízená z počítače se systémem Windows 7 se dá obnovit v systému Windows 8 nebo novějším. Zálohy pořízené z počítače se systémem Windows 8 nelze obnovit v počítači se systémem Windows 7.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [architektuře zálohování, která používá agenta Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Zjistěte, co se podporuje při [spuštění agenta Mars v MABS nebo DPM serveru](backup-support-matrix-mabs-dpm.md).
