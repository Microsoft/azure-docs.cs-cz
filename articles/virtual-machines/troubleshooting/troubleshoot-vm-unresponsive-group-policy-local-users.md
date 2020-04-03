---
title: Virtuální počítač nereaguje při použití zásad místních uživatelů zásad skupiny & skupin
description: Tento článek obsahuje postup k vyřešení problémů, kdy se při spuštění virtuálního počítače (VM) zaseklá obrazovka zatížení při použití zásady.
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
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620854"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Virtuální počítač nereaguje při použití zásad místních uživatelů zásad skupiny & skupin

Tento článek obsahuje postup k vyřešení problémů, kdy se na obrazovce zatížení zasekne použití zásady během spuštění ve virtuálním počítači Azure (VM).

## <a name="symptom"></a>Příznak

Při použití [boot diagnostiky](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) k zobrazení snímku obrazovky virtuálního počítačů, uvidíte, že obrazovka se zasekl načítání se zprávou: Použití zásad *skupiny místní uživatelé a skupiny zásady*.

![Alternativní text: Obrazovka s použitím nastavení zásad místních uživatelů a skupin zásad skupiny (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Alternativní text: Obrazovka zobrazující použití nastavení zásad skupiny místních uživatelů a skupin (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Příčina

Příznaky tohoto zmrazení jsou způsobeny vadou kódu v dynamická knihovna odkazů služby Windows Profile Service (*profsvc.dll*).

> [!NOTE]
> Tato vada platí pouze pro systémy Windows Server 2012 a Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Dotčená politika

Použitá zásada, která nedokončí své procesy, je:

* *Konfigurace počítače\Zásady\Šablony pro správu\Systémové/Profily uživatelů\Odstranit profily uživatelů starší než zadaný počet dní při restartování systému*

Tato zásada bude viset pouze v případě, že je splněno následujících šest podmínek:

* *Jsou povoleny profily odstranit uživatele starší než zadaný počet dní v zásadách restartování systému.*
* Máte profily, které splnily požadavky na stáří vyžadovat vyčištění.
* Máte součásti, které se zaregistrovaly pro oznámení o odstranění profilů.
* Součásti uskutečňují volání (přímé nebo nepřímé), které potřebují získat data z komponent Správce řízení služeb (SCM) systému Windows, jako je například Start, Stop nebo Query informace o službě.
* Služba je nakonfigurována tak, aby se *spouštěla*jako automatická .
* Tato služba je nastavena tak, aby byla spuštěna v kontextu účtu domény (na rozdíl od použití předdefinovaného účtu, jako je například místní systém).

### <a name="the-code-defect"></a>Vada kódu

Vada kódu je způsobena správcem řízení služeb (SCM) a službami profile, které se pokoušejí současně použít zámky na sobě. Zámky existují zabránit více služeb z provádění změn na stejná data ve stejnou dobu, což by způsobilo poškození. Obvykle více požadavků na zámek by nezpůsobil problém. Nicméně, protože se to děje během startu, ani služba nemůže dokončit své procesy, protože jsou přilepená čekání na sebe.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>Chyba operačního prostoru 5880648 – zablokování správce řízení služeb se zásadami "Odstranit profily uživatelů při restartování"

Existují dvě akce, které se překrývají tak, že:

* Akce 1 získá zámek profilu, ale ještě nezískal zámek SCM.

  **A**

* Akce 2 získá zámek SCM, ale ještě nezískal zámek profilu.

Jakmile dojde k této vzájemné vzájemné vzájemné zablokování, pokus o získání druhého požadovaného zámku zablokuje akci.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Akce 1 - Oznámení o odstranění starého profilu (má **zámek profilu**, potřebuje **zámek SCM)**

1. Nejprve zásady nastavené na odstranění starých profilů získá zámek služby interníprofil.

   * Tento zámek je k dispozici zabránit dvěma vlákny z interakce s profily, zatímco *operace odstranění* je průběh.

2. Zásada vyhledá profily, které jsou dostatečně staré, aby mohly být odstraněny.
3. Jako součást odstranění profilu se komponenta, která zaregistrovala oznámení o odstranění profilu, pokusí **spustit službu**.
4. Před zahájením služby musí Správce řízení služeb (SCM) získat **interní zámek SCM** držený vlákny v **akci 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Akce 2 - Zatížení/vytváření profilu pro data specifická pro uživatele (má **Zámek SCM**, potřebuje **zámek profilu)**

1. Při startu, SCM musí objednat všechny služby *automatického spuštění* podle jejich skupiny, stejně jako všechny služby, které tyto služby jsou závislé na.

2. **SCM získá interní zámek SCM** slouží k řízení přístupu ke spuštění, zastavení nebo konfiguraci služeb při objednávání služeb.

3. Jakmile jsou služby v pořádku, SCM smyčky prostřednictvím každé služby a spustí ji.

4. Pokud je služba spuštěna v kontextu účtu domény, musí být profil načten nebo vytvořen pro účet domény, aby mohl ukládat data specifická pro uživatele.

5. Tento požadavek je odeslán **službě profilů**.

6. Služba profilu potřebuje přístup k **vnitřnímu zámku** získanému v **akci 1**.

## <a name="solution"></a>Řešení

### <a name="process-overview"></a>Přehled procesů

1. Vytvoření a přístup k virtuálnímu virtuálnímu virtuálnímu mněmu pro opravy
2. Povolit kolekci sériové houštin y a výpisy paměti
3. Opětovné sestavení virtuálního virtuálního mísy
4. Shromáždění souboru s výpisem stavu paměti

   > [!NOTE]
   > Při výskytu této chyby při spuštění není hostovaný operační systém funkční. Tento problém vyřešíte v režimu offline.

### <a name="create-and-access-a-repair-vm"></a>Vytvoření a přístup k virtuálnímu virtuálnímu virtuálnímu mněmu pro opravy

1. Pomocí [kroků 1-3 příkazů pro opravu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) připravte virtuální počítač.
2. Pomocí připojení ke vzdálené ploše se připojte k virtuálnímu počítači pro opravu.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Povolit kolekci sériové houštin y a výpisy paměti

Chcete-li povolit kolekci výpisu stavu paměti a konzolu Serial Console, spusťte následující skript:

1. Otevřete relaci příkazového řádku se zvýšenými oprávněními (Spustit jako správce).
2. Spusťte následující příkazy:

   * Povolit sériovou konzolu:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Ověřte, že volné místo na disku operačního systému je stejně jako velikost paměti (RAM) na virtuálním počítači.

   * Pokud není dostatek místa na disku operačního systému, měli byste změnit umístění, kde bude vytvořen soubor výpisu stavu paměti a odkazovat, že na všechny datové disky připojené k virtuálnímu počítači, který má dostatek volného místa. Chcete-li změnit `%SystemRoot%` umístění, nahraďte písmenem jednotky (například "F:") datového disku v následujících příkazech.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Navrhovaná konfigurace pro povolení výpisu operačního systému

**Načíst přerušený disk operačního systému:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Povolit na Ovládacích sadach001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Povolit na Ovládacím prvkuSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Opětovné sestavení virtuálního virtuálního mísy

Pomocí [kroku 5 příkazů pro opravu virtuálních vod](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) znovu sestavte virtuální ho.

### <a name="collect-the-memory-dump-file"></a>Shromáždění souboru s výpisem stavu paměti

Chcete-li tento problém vyřešit, budete muset nejprve shromáždit soubor s výpisem stavu paměti pro selhání a obraťte se na podporu s souborem výpisu stavu paměti. Chcete-li soubor s výpisem stavu paměti shromáždit, postupujte takto:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Připojení disku operačního systému k novému virtuálnímu virtuálnímu počítače pro opravu

1. Pomocí kroků [1-3 příkazů pro opravu virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) připravte nový virtuální počítač.

2. Pomocí připojení ke vzdálené ploše se připojte k virtuálnímu počítači pro opravu.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Vyhledejte soubor s výpisem stavu paměti a odešlete lístek podpory

1. Na virtuálním počítači opravy přejděte do složky Windows na připojeném disku operačního systému. Pokud je písmeno ovladače přiřazené k připojenému disku operačního systému F, musíte přejít na f:\Windows.

2. Vyhledejte soubor memory.dmp a [odešlete lístek podpory](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se souborem s výpisem stavu paměti.

3. Pokud máte potíže s vyhledáním souboru memory.dmp, můžete místo toho použít [volání nmi (nmi) nemaskovatelnépřerušení(NMI).](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) Můžete postupovat podle pokynů pro [generování jádra nebo kompletní](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) soubor výpisu stavu pomocí volání NMI.
