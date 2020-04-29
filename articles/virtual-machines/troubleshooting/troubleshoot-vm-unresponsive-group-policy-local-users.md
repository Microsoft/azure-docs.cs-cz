---
title: Při použití zásad Zásady skupiny místních uživatelů & skupin nereaguje virtuální počítač.
description: Tento článek popisuje kroky pro řešení problémů, při kterých se při spuštění na virtuálním počítači Azure zablokuje načítání obrazovky.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620854"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Při použití zásad Zásady skupiny místních uživatelů & skupin nereaguje virtuální počítač.

Tento článek popisuje kroky pro řešení problémů, při kterých se při spuštění na virtuálním počítači (VM) v systému Azure zablokuje obrazovka Load-in.

## <a name="symptom"></a>Příznak

Když pomocí [diagnostiky spouštění](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) zobrazíte snímek obrazovky virtuálního počítače, uvidíte, že obrazovka se zablokuje načítání s touto zprávou: *použití zásady skupiny zásad místních uživatelů a skupin*.

![ALT text: obrazovka, která se používá při načítání zásad Zásady skupiny místních uživatelů a skupin (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![ALT text: obrazovka, která se používá při načítání zásad Zásady skupiny místních uživatelů a skupin (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Příčina

Příznaky tohoto zablokování jsou způsobeny poškozením kódu v knihovně dynamického propojení (*ProfSvc. dll*) služby profilů systému Windows.

> [!NOTE]
> Tato vada se vztahuje pouze na systémy Windows Server 2012 a Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Příslušné zásady

Použitá zásada, která nekončí jeho procesy, je:

* *Počítače \ šablony \ systémové/uživatelské Profiles\Delete uživatelské profily starší než zadaný počet dní při restartování systému*

Tato zásada bude zareagovat pouze v případě, že platí následující šest podmínek:

* Možnost *Odstranit profily uživatelů starší než zadaný počet dnů v zásadách restartování systému* je povolena.
* Máte profily, které splnily požadavky na stáří pro vyžadování vyčištění.
* Máte součásti, které jsou zaregistrované pro oznámení o odstranění pro profily.
* Komponenty volají (přímé nebo nepřímé), které potřebují získat data z komponent správce řízení služeb (SCM) systému Windows, například spustit, zastavit nebo dotazovat informace o službě.
* Máte službu nakonfigurovanou tak, aby spouštěla *automaticky*.
* Tato služba je nastavená tak, aby běžela pod kontextem doménového účtu (na rozdíl od použití předdefinovaného účtu, jako je třeba místní systém).

### <a name="the-code-defect"></a>Závada kódu

Vada kódu je způsobena tím, že se správce řízení služeb (SCM) a profilové služby pokoušejí použít zámky navzájem současně. Zámky existují, aby nedocházelo k tomu, aby více služeb provádělo změny stejných dat ve stejnou dobu, což by způsobilo poškození. Obvykle více požadavků na uzamčení nezpůsobí problém. Vzhledem k tomu, že se to děje během spouštění, nemůže ani služba dokončit své procesy, protože se zablokuje, že se na sebe navzájem čekají.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>Chyba operačního systému 5880648 – správce řízení služeb se zablokuje pomocí zásad "Odstranit profily uživatelů při restartování".

Existují dvě akce, které se překrývají, aby:

* Akce 1 získá zámek profilu, ale ještě nezískal zámek SCM.

  **ANI**

* Akce 2 získá zámek SCM, ale ještě nezískal zámek profilu.

Jakmile dojde k tomuto zablokování, pokusy o získání druhého požadovaného zámku zablokují akci.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Akce 1 – oznámení o odstranění starého profilu (má **Zámek profilu**, vyžaduje **Zámek SCM**)

1. Nejprve zásada nastavená tak, aby odstranila staré profily, získá interní zámek profilu služby.

   * Tento zámek je k dispozici, aby nedocházelo k tomu, aby dvě vlákna komunikovala s profily, zatímco probíhá *operace odstranění* .

2. Zásada vyhledá profily, které jsou dostatečně staré, aby je bylo možné odstranit.
3. V rámci odstranění profilu se pokusí **Spustit službu**součást, která je zaregistrovaná pro oznámení o odstranění profilu.
4. Před spuštěním služby musí správce řízení služeb (SCM) získat **interní zámek SCM** uložený vlákny v **akci 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Akce 2 – načtení nebo vytvoření profilu pro data specifická pro uživatele (má **Zámek SCM**, vyžaduje **Zámek profilu**)

1. Při spuštění musí SCM objednat všechny *Automatické spouštěcí* služby podle jejich skupiny a také všechny služby, na kterých jsou tyto služby závislé.

2. **SCM získá interní zámek SCM** , který se používá k řízení přístupu ke spouštění, zastavování nebo konfiguraci služeb při jejich nařazení do služby.

3. Jakmile jsou služby v pořádku, prochází služba SCM každou službu a spustí ji.

4. Pokud je služba spuštěna pod kontextem účtu domény, musí být profil buď načten, nebo vytvořen pro účet domény, aby mohl ukládat data specifická pro uživatele.

5. Tato žádost se pošle do **profilovací služby**.

6. Služba profilů potřebuje přístup k **internímu zámku** získanému v **akci 1**.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesu

1. Vytvoření a přístup k opravnému virtuálnímu počítači
2. Povolit kolekci sériového stavu a výpisu paměti
3. Opětovné sestavení virtuálního počítače
4. Shromáždit soubor výpisu paměti

   > [!NOTE]
   > Při výskytu této chyby spuštění není hostovaný operační systém funkční. Tento problém vyřešíte tak, že budete řešit potíže v offline režimu.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k opravnému virtuálnímu počítači

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) Připravte opravný virtuální počítač.
2. Pomocí Připojení ke vzdálené ploše připojit k opravnému virtuálnímu počítači.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Povolit kolekci sériového stavu a výpisu paměti

Pokud chcete povolit shromažďování výpisů paměti a sériovou konzolu, spusťte následující skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte následující příkazy:

   * Povolit sériovou konzolu:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Ověřte, že volné místo na disku s operačním systémem je ve virtuálním počítači ve velikosti paměti (RAM).

   * Pokud na disku operačního systému není dostatek místa, měli byste změnit umístění, kde se vytvoří soubor s výpisem paměti, a odkazovat na libovolný datový disk připojený k virtuálnímu počítači, který má dostatek volného místa. Chcete-li změnit umístění, `%SystemRoot%` nahraďte písmeno jednotky (například "F:") datového disku v následujících příkazech.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Navrhovaná konfigurace pro povolení výpisu operačního systému

**Načíst poškozený disk s operačním systémem:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Povolit na ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Povolit na ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního počítače

Pomocí [kroku 5 příkazů pro opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) znovu sestavte virtuální počítač.

### <a name="collect-the-memory-dump-file"></a>Shromáždit soubor výpisu paměti

Chcete-li tento problém vyřešit, je třeba nejprve shromáždit soubor výpisu paměti pro chybu a kontaktovat podporu pomocí souboru s výpisem paměti. Chcete-li shromáždit soubor s výpisem paměti, postupujte podle následujících kroků:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojit disk s operačním systémem k nové opravě virtuálního počítače

1. K přípravě nového opravného virtuálního počítače použijte kroky [1-3 příkazů pro opravu virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .

2. Pomocí Připojení ke vzdálené ploše připojit k opravnému virtuálnímu počítači.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem paměti a odešlete lístek podpory.

1. V části opravit virtuální počítač otevřete složku Windows na připojeném disku s operačním systémem. Pokud je písmeno přiřazené k připojenému disku s operačním systémem F, je nutné přejít na F:\Windows.

2. Vyhledejte soubor Memory. dmp a pak [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem paměti.

3. Pokud se vám nedaří najít soubor Memory. dmp, možná budete chtít místo toho použít [volání NMI (nemaskovaná přerušení) v sériové konzole](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Podle pokynů průvodce můžete [vygenerovat jádro nebo kompletní soubor s výpisem stavu systému](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) pomocí NMI volání.
