---
title: Sériová konzola Azure pro Windows | Microsoft Docs
description: Bi-Directional sériové konzole pro Azure Virtual Machines a Virtual Machine Scale Sets pomocí příkladu Windows.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 9cf3f9a1cd933526c5e376d232fa5acbc97fad47
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969717"
---
# <a name="azure-serial-console-for-windows"></a>Sériová konzola Azure pro Windows

Konzola sériového portu v Azure Portal poskytuje přístup k textové konzole pro virtuální počítače s Windows a instance sady škálování virtuálních počítačů. Toto sériové připojení se připojuje k sériovému portu COM1 virtuálního počítače nebo instance sady škálování virtuálních počítačů a poskytuje přístup k němu nezávisle na stavu sítě nebo operačního systému. Ke konzole sériového přístupu se dá přistupovat jenom pomocí Azure Portal a je povolená jenom pro uživatele, kteří mají roli přístupu přispěvatel nebo vyšší, k virtuálnímu počítači nebo sadě škálování virtuálních počítačů.

Sériová konzola funguje stejným způsobem pro virtuální počítače a instance sady škálování virtuálních počítačů. V tomto dokumentu budou všechny zmínky k virtuálním počítačům implicitně zahrnovat instance sady škálování virtuálních počítačů, pokud není uvedeno jinak.

Konzola sériového portu je všeobecně dostupná v globálních oblastech Azure a ve verzi Public Preview v Azure Government. Ještě není k dispozici v cloudu Azure Čína.

Dokumentaci k sériové konzole pro Linux najdete v tématu věnovaném [sériové konzole Azure pro Linux](serial-console-linux.md).

> [!NOTE]
> Sériová konzola je aktuálně nekompatibilní se spravovaným účtem úložiště diagnostiky spouštění. Pokud chcete použít sériovou konzolu, ujistěte se, že používáte vlastní účet úložiště.


## <a name="prerequisites"></a>Předpoklady

* Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporovaná.

- Váš účet, který používá sériová konzola, musí mít [roli Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač a účet úložiště [diagnostiky spouštění](boot-diagnostics.md) .

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí mít uživatele založené na heslech. Můžete ho vytvořit pomocí funkce [resetovat heslo](../extensions/vmaccess.md#reset-password) rozšíření pro přístup k virtuálním počítačům. V části **Podpora a řešení potíží** vyberte **resetovat heslo** .

* Virtuální počítač pro instanci sady škálování virtuálního počítače musí mít povolenou [diagnostiku spouštění](boot-diagnostics.md) .

    ![Nastavení diagnostiky spouštění](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Povolení funkce sériové konzoly pro Windows Server

> [!NOTE]
> Pokud nevidíte cokoli v konzole sériového portu, ujistěte se, že je na vašem VIRTUÁLNÍm počítači nebo v sadě škálování virtuálního počítače povolená Diagnostika spouštění.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Povolení konzoly sériového portu ve vlastních nebo starších bitových kopiích
Novější image Windows serveru v Azure mají ve výchozím nastavení povolenou [Speciální konzolu pro správu](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) (SAC). Konzola SAC je podporovaná na serverových verzích Windows, ale není dostupná na klientských verzích (například Windows 10, Windows 8 nebo Windows 7).

Pro starší image Windows serveru (vytvořené před únorem 2018) můžete automaticky aktivovat sériovou konzolu prostřednictvím funkce spustit příkaz Azure Portal. V Azure Portal vyberte **Spustit příkaz**a potom ze seznamu vyberte příkaz s názvem **EnableEMS** .

![Spustit seznam příkazů](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Pokud chcete ručně povolit sériovou konzolu pro virtuální počítače s Windows nebo sadu škálování virtuálního počítače vytvořené před únorem 2018, použijte následující postup:

1. Připojení k virtuálnímu počítači s Windows pomocí vzdálené plochy
1. Z příkazového řádku pro správu spusťte následující příkazy:
    - `bcdedit /ems {current} on`nebo `bcdedit /ems '{current}' on` Pokud používáte PowerShell
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Restartujte systém, aby se aktivovala konzola konzoly SAC.

    ![Konzola konzoly SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

V případě potřeby je možné konzolu SAC povolit i offline:

1. Připojte disk Windows, pro který chcete, aby byla Konzola SAC nakonfigurovaná jako datový disk pro existující virtuální počítač.

1. Z příkazového řádku pro správu spusťte následující příkazy:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Návody víte, zda je povolena Konzola SAC?

Pokud [Konzola SAC](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v=ws.10)) není povolená, zobrazí se v konzole sériového zobrazení výzva SAC. V některých případech se zobrazí informace o stavu virtuálního počítače a v ostatních případech je tato informace prázdná. Pokud používáte bitovou kopii systému Windows Server vytvořenou před únorem 2018, Konzola SAC pravděpodobně nebude povolena.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Povolení spouštěcí nabídky Windows v konzole sériového portu

Pokud potřebujete povolit zobrazování výzev pro spouštěcí zavaděč Windows v konzole sériového prostředí, můžete pro konfigurační data spouštění přidat následující další možnosti. Další informace naleznete v tématu [bcdedit](/windows-hardware/drivers/devtest/bcdedit--set).

1. Připojte se k VIRTUÁLNÍmu počítači s Windows nebo k instanci sady škálování virtuálního počítače pomocí vzdálené plochy.

1. Z příkazového řádku pro správu spusťte následující příkazy:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Restartováním systému spusťte spouštěcí nabídku, která se má povolit.

> [!NOTE]
> Časový limit nastavený pro zobrazení nabídky Správce spouštění bude mít vliv na čas spuštění operačního systému. Pokud si myslíte, že hodnota 10 sekund časového limitu je příliš krátká nebo příliš dlouhá, nastavte ji na jinou hodnotu.

## <a name="use-serial-console"></a>Použití sériové konzoly

### <a name="use-cmd-or-powershell-in-serial-console"></a>Použití CMD nebo PowerShellu v sériové konzole

1. Připojte se ke konzole sériového portu. Pokud se úspěšně připojíte, zobrazí se výzva **SAC>**:

   ![Připojení k konzole SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)
1. Zadejte `cmd` pro vytvoření kanálu, který má instanci cmd.

1. Zadejte `ch -si 1` nebo stiskněte klávesovou `<esc>+<tab>` zkratku pro přepnutí na kanál, na kterém je spuštěná instance cmd.

1. Stiskněte klávesu **ENTER**a potom zadejte přihlašovací údaje s oprávněními správce.

1. Po zadání platných přihlašovacích údajů se otevře instance CMD.

1. Pokud chcete spustit instanci prostředí PowerShell, zadejte `PowerShell` v instanci cmd a potom stiskněte klávesu **ENTER**.

   ![Otevřít instanci prostředí PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Použití konzoly sériového portu pro volání NMI
Nemaskovatelné přerušení (NMI) je navržené k vytvoření signálu, který software na virtuálním počítači Neignoruje. Historicky jsme NMIs použili k monitorování problémů s hardwarem v systémech, které vyžadovaly určitou dobu odezvy. V současné době se programátoři a správci systému často používají NMI jako mechanismus pro ladění nebo odstraňování potíží se systémy, které nereagují.

Sériová konzola se dá použít k odeslání NMI virtuálnímu počítači Azure pomocí ikony klávesnice na panelu příkazů. Po doručení NMI bude konfigurace virtuálního počítače řídit, jak systém reaguje. Systém Windows je možné nakonfigurovat tak, aby došlo k chybě, a při příjmu NMI vytvořit soubor s výpisem paměti.

![Odeslat NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Informace o konfiguraci systému Windows k vytvoření souboru s výpisem stavu systému při přijetí NMI najdete v tématu [jak vygenerovat soubor se stavem systému pomocí NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Použití funkčních kláves v konzole sériového portu
Klíče funkcí jsou povolené pro použití pro sériovou konzolu na virtuálních počítačích s Windows. Funkce F8 v rozevíracím seznamu konzoly sériového portu nabízí možnost snadného přechodu do nabídky Upřesnit nastavení spouštění, ale sériové konzola je kompatibilní se všemi ostatními klíči funkcí. Na klávesnici možná budete muset stisknout klávesu **FN**  +  **F1** (nebo F2, F3 atd.) v závislosti na počítači, ze kterého používáte sériová konzola.

### <a name="use-wsl-in-serial-console"></a>Použití WSL v sériové konzole
Subsystém Windows pro Linux (WSL) byl povolen pro Windows Server 2019 nebo novější, takže je také možné povolit WSL pro použití v rámci konzoly sériového portu, pokud používáte systém Windows Server 2019 nebo novější. To může být užitečné pro uživatele, kteří mají taky známé příkazy pro Linux. Pokyny k povolení WSL pro Windows Server najdete v [instalační příručce](/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Restartování instance virtuálního počítače s Windows nebo virtuálního počítače v rámci sériové konzoly
Restartování v rámci konzoly sériového portu můžete iniciovat tak, že přejdete na tlačítko napájení a kliknete na restartovat virtuální počítač. Tím se iniciuje restartování virtuálního počítače a v Azure Portal se zobrazí oznámení týkající se restartování.

To je užitečné v situacích, kdy budete chtít přistupovat ke spouštěcí nabídce, aniž byste museli opustit prostředí sériové konzoly.

![Restart Windows sériové konzoly](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Zakázání sériové konzoly
Ve výchozím nastavení mají všechny odběry povolený přístup pomocí sériové konzoly. Službu sériového portu můžete zakázat buď na úrovni předplatného, nebo na úrovni sady virtuálních počítačů nebo virtuálních počítačů. Podrobné pokyny najdete v tématu [povolení a zakázání služby Azure Serial Console](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Sériová konzola zabezpečení

### <a name="access-security"></a>Přístup k zabezpečení
Přístup ke konzole sériového portu je omezený na uživatele, kteří mají roli přístupu [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší k virtuálnímu počítači. Pokud váš Azure Active Directory tenant vyžaduje vícefaktorové ověřování (MFA), bude mít přístup ke konzole sériového portu taky MFA, protože přístup ke konzole sériového prostředí probíhá přes [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Všechna data, která se odesílají zpátky a zpátky, se šifrují na lince.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je aktuálně přihlášený k protokolům [diagnostiky spouštění](./boot-diagnostics.md) virtuálního počítače. Přístup k těmto protokolům jsou vlastněné a řízené správcem virtuálního počítače Azure.

> [!CAUTION]
> Do protokolu nejsou protokolována žádná přístupová hesla pro konzolu. Pokud se ale v konzole spouštějí příkazy, které obsahují nebo využívají hesla, tajné klíče, uživatelská jména nebo jakékoli jiné formy osobních údajů (PII), zapíší se do protokolů diagnostiky spouštění virtuálního počítače. Budou zapsány spolu s veškerým jiným viditelným textem jako součást implementace funkce Posun zpět pomocí sériové konzoly. Tyto protokoly jsou cyklické a k nim mají přístup jenom jednotlivci s oprávněním ke čtení k účtu úložiště diagnostiky. Doporučujeme však, abyste v rámci používání vzdálené plochy vypracovali cokoli, co může zahrnovat tajné kódy nebo PII.

### <a name="concurrent-usage"></a>Souběžné využití
Pokud je uživatel připojen ke konzole sériového portu a jiný uživatel úspěšně požaduje přístup ke stejnému virtuálnímu počítači, bude první uživatel odpojen a druhý uživatel připojen ke stejné relaci.

> [!CAUTION]
> To znamená, že odpojený uživatel nebude odhlášen. Možnost vymáhat odhlášení po odpojení (pomocí SIGHUP nebo podobného mechanismu) je stále v plánu. V případě systému Windows je v konzole SAC povolen automatický časový limit. pro Linux můžete nakonfigurovat nastavení časový limit terminálu.

## <a name="accessibility"></a>Usnadnění
Přístupnost je klíčovým fokusem pro konzolu sériového rozhraní Azure. Za tímto účelem jsme zajistili přístup ke konzole sériového rozhraní pro lidi, kteří mají zhoršené zhoršení, nebo kteří mají těžkou sluchu, a také uživatele, kteří by nemohli používat myš.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Pomocí klávesy **TAB** na klávesnici přejděte v rozhraní sériové konzoly z Azure Portal. Vaše poloha se zvýrazní na obrazovce. Pokud chcete opustit okno konzoly sériového portu, stiskněte na své klávesnici **kombinaci kláves CTRL** + **F6** .

### <a name="use-the-serial-console-with-a-screen-reader"></a>Použití konzoly sériového portu se čtečkou obrazovky
Konzola sériového rozhraní obsahuje integrovanou podporu čtečky obrazovky. Navigace se zapnutým čtečkou obrazovky umožní, aby se text ALT pro aktuálně vybrané tlačítko načetl nahlasem pro čtečku obrazovky.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Běžné scénáře přístupu ke konzole sériového portu

Scénář          | Akce v konzole sériového portu
:------------------|:-----------------------------------------
Nesprávná pravidla brány firewall | Přístup ke konzole sériového portu a oprava pravidel brány Windows Firewall.
Poškození nebo ověření systému souborů | Přejděte do konzoly sériového portu a obnovte systém souborů.
Problémy s konfigurací RDP | Přejděte do konzoly sériového portu a změňte nastavení. Další informace najdete v [dokumentaci k protokolu RDP](/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Systém síťové uzamčení | Přístup ke konzole sériového portu z Azure Portal pro správu systému. Některé síťové příkazy jsou uvedené v [příkazech Windows: cmd a PowerShell](serial-console-cmd-ps-commands.md).
Interakce s nástrojem pro spouštění | Přístup k BCD prostřednictvím konzoly sériového portu. Informace najdete v tématu [Povolení spouštěcí nabídky Windows v konzole sériového portu](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Známé problémy
O některých problémech se používá konzola sériového portu a operační systém virtuálního počítače. Zde je uveden seznam těchto problémů a postup pro zmírnění rizik pro virtuální počítače s Windows. Tyto problémy a omezení rizik platí pro instance virtuálních počítačů i instancí sady škálování virtuálních počítačů. Pokud se neshodují s chybou, kterou vidíte, přečtěte si část běžné chyby služby sériové konzoly při [běžných chybách sériové konzoly](./serial-console-errors.md).

Problém                             |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Stisknutí klávesy **ENTER** po nápisu připojení nezpůsobí zobrazení výzvy k přihlášení. | Další informace najdete v tématu o tom, že [ENTER nedělá nic](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). K této chybě může dojít, pokud používáte vlastní virtuální počítač, zesílené zařízení nebo spouštěcí konfiguraci, které způsobí, že se Windows nemusí správně připojit k sériovému portu. K této chybě dojde také v případě, že používáte virtuální počítač s Windows 10, protože u virtuálních počítačů se systémem Windows Server jsou povoleny pouze EMS.
Při připojování k virtuálnímu počítači s Windows se zobrazí jenom informace o stavu.| K této chybě dojde, pokud pro vaši image Windows není povolená Speciální konzola pro správu. Pokyny k ručnímu povolení konzoly SAC na vašem VIRTUÁLNÍm počítači s Windows najdete v tématu [Povolení služby sériového prostředí ve vlastních nebo starších imagí](#enable-the-serial-console-in-custom-or-older-images) . Další informace najdete v tématu [signály stavu systému Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Konzola SAC nebere v prohlížeči celou oblast sériová konzola. | Jedná se o známý problém s Windows a emulátorem terminálu. Tento problém sledujeme u obou týmů, ale v současné době nedochází ke zmírnění rizik.
Pokud je povoleno ladění jádra, nelze v konzole SAC zadat dotaz. | RDP na virtuální počítač a spuštění `bcdedit /debug {current} off` z příkazového řádku se zvýšenými oprávněními. Pokud nemůžete používat protokol RDP, můžete místo toho připojit disk s operačním systémem k jinému virtuálnímu počítači Azure a při spuštění ho upravit, a to tak, že ho znovu připojíte `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` .
Vložení do PowerShellu v konzole SAC má za následek třetí znak, pokud původní obsah obsahuje opakující se znak. | V případě alternativního řešení spusťte příkaz `Remove-Module PSReadLine` k uvolnění modulu PSReadLine z aktuální relace. Tato akce neprovede odstranění ani odinstalaci modulu.
Některé vstupy klávesnice vytvoří neobvyklý výstup konzoly SAC (například **[A**, **[3 ~**). | Řídicí sekvence [VT100](/windows/console/console-virtual-terminal-sequences) se v příkazovém řádku konzoly SAC nepodporují.
Vložení dlouhých řetězců nefunguje. | Konzola sériového portu omezuje délku řetězců vložených do terminálu na 2048 znaků, aby se zabránilo přetížení šířky pásma sériového portu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Č. Jak mohu odeslat zpětnou vazbu?**

A. Poskytněte zpětnou vazbu vytvořením problému GitHubu na adrese https://aka.ms/serialconsolefeedback . Případně (méně upřednostňovaná) můžete odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo v kategorii virtuálního počítače v https://feedback.azure.com .

**Č. Podporuje sériová konzola kopírování/vkládání?**

A. Yes. Pomocí **kombinace kláves CTRL** + **+ SHIFT**+ Shift + + **C** **Ctrl** + **Shift** + **V** zkopírujte a vložte do terminálu.

**Č. Kdo může povolit nebo zakázat sériovou konzolu pro moje předplatné?**

A. Pokud chcete povolit nebo zakázat sériovou konzolu na úrovni předplatného, musíte mít oprávnění k zápisu do předplatného. Role, které mají oprávnění k zápisu, zahrnují role správce nebo vlastníka. Vlastní role můžou mít taky oprávnění k zápisu.

**Č. Kdo má přístup ke konzole sériového připojení k virtuálnímu počítači?**

A. Aby virtuální počítač měl přístup ke konzole sériového připojení virtuálního počítače, musíte mít roli Přispěvatel virtuálních počítačů nebo vyšší.

**Č. Moje konzola sériového zobrazení nezobrazuje vše, co mám dělat?**

A. Bitová kopie je pro přístup k sériové konzole nejspíš nesprávně nakonfigurovaná. Informace o konfiguraci image pro povolení konzole sériového prostředí najdete v tématu [Povolení sériové konzoly ve vlastních nebo starších bitových kopiích](#enable-the-serial-console-in-custom-or-older-images).

**Č. Je k dispozici sériová Konzola pro sady škálování virtuálních počítačů?**

A. Ano, je! Další informace najdete v tématu [sériová Konzola pro Virtual Machine Scale Sets](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Další kroky
* Podrobný průvodce příkazy CMD a PowerShellu, které můžete použít v konzole Windows SAC, najdete v tématu [příkazy Windows: cmd a PowerShell](serial-console-cmd-ps-commands.md).
* K dispozici je také konzola sériového portu pro virtuální počítače se [systémem Linux](serial-console-linux.md) .
* Přečtěte si další informace o [diagnostice spouštění](boot-diagnostics.md).