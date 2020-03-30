---
title: Konzola Azure Serial Console pro Windows | Dokumenty společnosti Microsoft
description: Obousměrná sériová konzola pro virtuální počítače Azure a škálovací sady virtuálních počítačů.
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
ms.openlocfilehash: 87ccb1c4995337b385f685797980a9fc3962bc6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266998"
---
# <a name="azure-serial-console-for-windows"></a>Konzola Azure Serial Console pro Windows

Serial Console na portálu Azure poskytuje přístup k textové konzoli pro virtuální počítače (VMs) a instance škálovací sady virtuálních počítačů. Toto sériové připojení se připojuje k sériovému portu COM1 instance škálovací sady virtuálního počítače nebo virtuálního počítače a poskytuje k němu přístup nezávisle na stavu sítě nebo operačního systému. K sériové konzoli lze přistupovat jenom pomocí portálu Azure portal a je povolena jenom pro ty uživatele, kteří mají přístupovou roli přispěvatele nebo vyšší pro škálovací sadu virtuálního počítače nebo virtuálního počítače.

Serial Console funguje stejným způsobem pro virtuální počítače a instance škálovací sady virtuálních strojů. V tomto dokumentu budou všechny zmínky o virtuálních počítačích implicitně zahrnovat instance škálovací sady virtuálních strojů, pokud není uvedeno jinak.

Dokumentace ke sériové konzoli pro Linux najdete v [tématu Azure Serial Console for Linux](serial-console-linux.md).

> [!NOTE]
> Serial Console je obecně dostupná v globálních oblastech Azure a ve verzi Public Preview v Azure Government. V cloudu Azure China ještě není dostupná.


## <a name="prerequisites"></a>Požadavky

* Instance škálovací sady virtuálních počítače nebo virtuálního počítače musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporována.

- Váš účet, který používá sériovou konzolu, musí mít [roli přispěvatele virtuálního počítače](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač a účet úložiště [diagnostiky spouštění](boot-diagnostics.md)

- Instance škálovací sady virtuálního počítače nebo virtuálního počítače musí mít uživatele založeného na heslech. Můžete vytvořit jeden s funkcí [resetování hesla](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozšíření přístupu k virtuálnímu počítače. V části **Podpora + řešení potíží** vyberte Obnovit **heslo.**

* Instance škálovací sady virtuálních počítače pro virtuální počítače musí mít [povolenou diagnostiku spouštění.](boot-diagnostics.md)

    ![Nastavení diagnostiky spouštění](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Povolení funkcí konzoly Serial Console pro systém Windows Server

> [!NOTE]
> Pokud v konzole sériové konzole nic nevidíte, ujistěte se, že je na vašem virtuálním počítači nebo škálovací sadě virtuálního počítače povolena diagnostika spouštění.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Povolení sériové konzoly ve vlastních nebo starších irech
Novější image Windows Serveru v Azure mají ve výchozím nastavení [povolenou speciální konzolu pro správu](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC). Sac je podporován a serverové verze systému Windows, ale není k dispozici v klientských verzích (například Windows 10, Windows 8 nebo Windows 7).

U starších bitových kopií Windows Serveru (vytvořených před únorem 2018) můžete automaticky povolit sériovou konzolu pomocí funkce příkazu spustit na webu Azure Portal. Na webu Azure Portal vyberte **příkaz Spustit**a ze seznamu vyberte příkaz s názvem **EnableEMS.**

![Spustit seznam příkazů](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Chcete-li ručně povolit sériovou konzolu pro škálovací sadu virtuálních počítačů se systémem Windows nebo virtuálních strojů vytvořenou před únorem 2018, postupujte takto:

1. Připojení k virtuálnímu počítači s Windows pomocí vzdálené plochy
1. Z příkazového řádku pro správu spusťte následující příkazy:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Restartujte systém pro konzolu SAC, která má být povolena.

    ![Konzola SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

V případě potřeby může být sac povolen také offline:

1. Připojte disk systému Windows, pro který chcete, aby byl sac nakonfigurován jako datový disk, k existujícímu virtuálnímu počítače.

1. Z příkazového řádku pro správu spusťte následující příkazy:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Jak poznám, že je sac povolen?

Pokud [není povolena funkce SAC,](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) sériová konzola se na výzvu SAC nezobrazí. V některých případech se zobrazí informace o stavu virtuálního mísy a v jiných případech je prázdné. Pokud používáte bitovou kopii Windows Serveru vytvořenou před únorem 2018, sac pravděpodobně nebude povolena.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Povolení spouštěcí nabídky systému Windows v konzole sériového portu

Pokud potřebujete povolit zobrazení výzev zavaděče systému Windows v konzole sériového zařízení, můžete do konfiguračních dat spouštění přidat následující další možnosti. Další informace naleznete v [tématu bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Připojte se k instanci škálovací sady virtuálního počítače s Windows nebo virtuálního počítače pomocí vzdálené plochy.

1. Z příkazového řádku pro správu spusťte následující příkazy:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Restartujte systém pro povolení spouštěcí nabídky

> [!NOTE]
> Časový čas, který jste nastavili pro zobrazení nabídky správce spouštění, bude mít vliv na dobu spouštění operačního systému. Pokud si myslíte, že hodnota časového času 10 sekund je příliš krátká nebo příliš dlouhá, nastavte ji na jinou hodnotu.

## <a name="use-serial-console"></a>Použití sériové konzoly

### <a name="use-cmd-or-powershell-in-serial-console"></a>Použití CMD nebo PowerShellu v konzole Serial Console

1. Připojte se k sériové konzoli. Pokud se úspěšně připojíte, zobrazí se výzva **SAC>**:

    ![Připojit ke sac](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Zadejte, `cmd` chcete-li vytvořit kanál, který má instanci CMD.

1.  Zadáním `ch -si 1` `<esc>+<tab>` klávesových zkratek nebo stisknutím klávesových zkratek přepněte na kanál, na jehož instanci je spuštěna, nebo stisknutím klávesových zkratek.

1.  Stiskněte **Enter**a zadejte přihlašovací pověření s oprávněními správce.

1.  Po zadání platných pověření se otevře instance CMD.

1.  Pokud chcete spustit instanci Prostředí PowerShell, zadejte instanci `PowerShell` CMD a stiskněte **Enter**.

    ![Otevřít instanci Prostředí PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Použití sériové konzoly pro volání NMI
Nemaskovatelné přerušení (NMI) je navrženo tak, aby vytvořilo signál, který software ve virtuálním počítači nebude ignorovat. Historicky, NMI byly použity ke sledování problémů s hardwarem v systémech, které vyžadují konkrétní doby odezvy. Programátoři a správci systému dnes často používají NMI jako mechanismus pro ladění nebo odstraňování problémů se systémy, které nereagují.

Sériovou konzolu lze použít k odeslání NMI do virtuálního počítače Azure pomocí ikony klávesnice na panelu příkazů. Po doručení NMI bude konfigurace virtuálního počítače řídit, jak systém reaguje. Systém Windows lze nakonfigurovat tak, aby při příjmu nmi došlo k chybě a vytvořil soubor s výpisem stavu paměti.

![Odeslat NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Informace o konfiguraci systému Windows k vytvoření souboru s výpisem stavu systému při přijetí nmi naleznete v [tématu Jak generovat soubor výpisu stavu systému pomocí nmi](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Použití funkčních kláves v konzole sériového zařízení
Funkční klávesy jsou povoleny pro použití pro sériovou konzolu v virtuálních serverech systému Windows. F8 v rozbalovací nabídce sériové konzole poskytuje pohodlí snadného zadávání nabídky Advanced Boot Settings, ale sériová konzola je kompatibilní se všemi ostatními funkčními klávesami. V závislosti na počítači, ze který používáte sériovou konzolu, může být nutné stisknout klávesu **Fn** + **F1** (nebo F2, F3 atd.).

### <a name="use-wsl-in-serial-console"></a>Použití wsl v konzole sériového portu
Podsystém Windows pro Linux (WSL) byl povolen pro Windows Server 2019 nebo novější, takže je také možné povolit WSL pro použití v sériové konzole, pokud používáte Windows Server 2019 nebo novější. To může být výhodné pro uživatele, kteří mají také znalost příkazů Linuxu. Pokyny k povolení wsl pro systém Windows Server naleznete v [průvodci instalací](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Restartujte instanci škálovatcí sady virtuálních zařízení/virtuálních strojů systému Windows v konzole Serial Console
Restartování v konzole sériové konzole můžete zahájit tak, že přejdete na tlačítko napájení a kliknete na tlačítko Restartovat virtuální počítač. Tím se spustí restartování virtuálního počítače a zobrazí se oznámení v rámci portálu Azure týkající se restartování.

To je užitečné v situacích, kdy můžete chtít získat přístup ke spouštěcí nabídce bez opuštění prostředí konzoly sériového zařízení.

![Restartování konzoly Sériové ho systému Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Zakázání konzoly Sériové houšti
Ve výchozím nastavení mají všechna předplatná povolen přístup ke konzole. Sériovou konzolu můžete zakázat na úrovni předplatného nebo na úrovni škálovací sady virtuálních zařízení/virtuálních strojů. Podrobné pokyny najdete v části [Povolit a zakázat konzolu Azure Serial Console](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu

### <a name="access-security"></a>Zabezpečení přístupu
Přístup k konzole sériového zařízení je omezen na uživatele, kteří mají přístupovou roli [přispěvatele virtuálního počítače](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší k virtuálnímu počítači. Pokud váš tenant Azure Active Directory vyžaduje vícefaktorové ověřování (MFA), bude přístup k sériové konzoli také potřebovat vícefaktorovou pomocí, protože přístup sériové konzole je prostřednictvím [portálu Azure](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Všechna data, která jsou odesílána tam a zpět, jsou šifrována na lince.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke sériové konzoli je aktuálně zaznamenán do protokolů [diagnostiky spouštění](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) virtuálního počítače. Přístup k těmto protokolům vlastní a řídí správce virtuálního počítače Azure.

> [!CAUTION]
> Nejsou zaznamenána žádná přístupová hesla pro konzolu. Pokud však příkazy spuštěné v rámci konzoly obsahují nebo výstup hesla, tajné klíče, uživatelská jména nebo jakoukoli jinou formu osobně identifikovatelné informace (PII), budou zapsány do protokolů diagnostiky spuštění virtuálního počítače. Budou napsány spolu se všemi ostatními viditelným textem jako součást implementace funkce posunu zpět sériové konzole. Tyto protokoly jsou cyklické a přístup k nim mají pouze osoby s oprávněním ke čtení pro účet úložiště diagnostiky. Doporučujeme však dodržovat osvědčené postupy používání vzdálené plochy pro cokoli, co může zahrnovat tajné kódy nebo osobní údaje.

### <a name="concurrent-usage"></a>Souběžné použití
Pokud je uživatel připojen k sériové konzole a jiný uživatel úspěšně požaduje přístup ke stejnému virtuálnímu počítači, bude první uživatel odpojen a druhý uživatel připojen ke stejné relaci.

> [!CAUTION]
> To znamená, že odpojený uživatel nebude odhlášen. Možnost vynutit odhlášení při odpojení (pomocí SIGHUP nebo podobného mechanismu) je stále v plánu. Pro Windows je v SAC povolen automatický časový čas; pro Linux, můžete nakonfigurovat nastavení koncového časového času.

## <a name="accessibility"></a>Usnadnění
Přístupnost je klíčovým zaměřením pro konzolu Azure. Za tímto účelem jsme zajistili, že sériová konzola je přístupná pro zrakově postižené a sluchově postižené, stejně jako pro osoby, které nemusí být schopny používat myš.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Pomocí klávesy **Tab** na klávesnici můžete procházet rozhraní množiny na webu Azure Portal. Vaše poloha bude na obrazovce zvýrazněna. Chcete-li ponechat fokus okna sériové konzole, stiskněte na klávesnici **kombinaci kláves Ctrl**+**F6.**

### <a name="use-the-serial-console-with-a-screen-reader"></a>Použití sériové konzoly se čtečkou obrazovky
Sériová konzola má vestavěnou podporu čtečky obrazovky. Při procházení se zapnutou čtečkou obrazovky umožní, aby program pro čtení z obrazovky přečetl alternativní text aktuálně vybraného tlačítka.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Běžné scénáře pro přístup k sériové konzole

Scénář          | Akce v konzole sériového portu
:------------------|:-----------------------------------------
Nesprávná pravidla brány firewall | Získejte přístup k sériové konzoli a opravte pravidla brány firewall systému Windows.
Poškození/kontrola souborového systému | Získejte přístup k sériové konzole a obnovte souborový systém.
Problémy s konfigurací protokolu RDP | Získejte přístup k konzole sériového zařízení a změňte nastavení. Další informace naleznete v [dokumentaci k prskavce .](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)
Systém uzamčení sítě | Přístup k sériové konzoli z portálu Azure pro správu systému. Některé síťové příkazy jsou uvedeny v [příkazech systému Windows: CMD a PowerShell](serial-console-cmd-ps-commands.md).
Interakce se zavaděcem | Přístup k serveru BCD prostřednictvím sériové konzole. Další informace naleznete [v tématu Enable the Windows boot menu in the serial console](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Známé problémy
Jsme si vědomi některých problémů se sériovou konzolou a operačním systémem virtuálního zařízení. Tady je seznam těchto problémů a postup pro zmírnění pro virtuální servery Windows. Tyto problémy a skutečnosti snižující závažnost rizika platí pro virtuální počítače i instance škálovací sady virtuálních počítačů. Pokud se neshodují s chybou, kterou vidíte, přečtěte si běžné chyby služby sériové konzole na [běžných chybách konzoly sériové hospo-](./serial-console-errors.md)

Problém                             |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Po zobrazení nápisu **Připojení** nezpůsobíte zobrazení výzvy k přihlášení. | Další informace naleznete v tématu [Bít zadejte neprovede žádné .](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) K této chybě může dojít, pokud používáte vlastní virtuální ho, tvrzené zařízení nebo spouštěcí konfigurace, která způsobí, že systém Windows se nezdaří správně připojit k sériovému portu. K této chybě dojde také v případě, že používáte virtuální počítače se systémem Windows 10, protože pouze virtuální počítače Windows Server jsou nakonfigurovány tak, aby měly povolenou TECHNOLOGII EMS.
Při připojování k virtuálnímu virtuálnímu mísu s Windows se zobrazují pouze informace o stavu.| K této chybě dochází, pokud nebyla pro bitovou kopii systému Windows povolena speciální konzola pro správu. Pokyny k ručnímu povolení sac na virtuálním počítači se systémem Windows najdete v tématu [Povolení sériové konzole ve vlastních nebo starších obrázcích.](#enable-the-serial-console-in-custom-or-older-images) Další informace naleznete v tématu [Signály stavu systému Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Sac nezabírají celou oblast sériové konzole v prohlížeči | Jedná se o známý problém se systémem Windows a emulátorem terminálu. Sledujeme tento problém s oběma týmy, ale pro tuto chvíli neexistuje žádné zmírnění.
Pokud je povoleno ladění jádra, nelze zadat na výzvu SAC. | RDP na virtuální `bcdedit /debug {current} off` měn a spustit z příkazového řádku se zvýšenými oprávněními. Pokud nemůžete RDP, můžete místo toho připojit disk operačního systému k jinému virtuálnímu počítači Azure a upravit jej při připojení jako datový disk spuštěním `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`a potom vyměnit disk zpět.
Vložení do prostředí PowerShell v SAC má za následek třetí znak, pokud původní obsah měl opakující se znak. | Chcete-li to `Remove-Module PSReadLine` toto řešení vyřešit, spusťte uvolnění modulu PSReadLine z aktuální relace. Tato akce modul neodstraní ani neodinstaluje.
Některé vstupy klávesnice vytvářejí podivný výstup SAC (například **[A**, **[3~**). | [VT100](https://aka.ms/vtsequences) escape sekvence nejsou podporovány sac výzvou.
Vkládání dlouhých strun nefunguje. | Konzola sériového systému omezuje délku řetězců vnesených do terminálu na 2048 znaků, aby nedošlo k přetížení šířky pásma sériového portu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jak mohu odeslat zpětnou vazbu?**

A. Poskytněte zpětnou vazbu vytvořením problému GitHub u . https://aka.ms/serialconsolefeedback Alternativně (méně preferované), můžete azserialhelp@microsoft.com odeslat zpětnou vazbu prostřednictvím nebo v kategorii virtuálních https://feedback.azure.comstrojů .

**Otázka: Podporuje konzola sériového zařízení kopírování a vkládání?**

A. Ano. Ke kopírování a vkládání do terminálu použijte **klávesy Ctrl**+**Shift**+**C** a **Ctrl**+**Shift**+**V.**

**Otázka: Kdo může povolit nebo zakázat sériovou konzolu pro mé předplatné?**

A. Chcete-li povolit nebo zakázat sériovou konzolu na úrovni předplatného, musíte mít oprávnění k zápisu k předplatnému. Role, které mají oprávnění k zápisu, zahrnují role správce nebo vlastníka. Vlastní role mohou mít také oprávnění k zápisu.

**Otázka: Kdo má přístup k sériové konzoli pro můj virtuální virtuální virtuální mísu?**

A. Chcete-li získat přístup k sériové konzole virtuálního počítače, musíte mít roli přispěvatele virtuálního počítače nebo vyšší.

**Otázka: Na konzole sériové konzole se nic nezobrazuje, co mám dělat?**

A. Obrázek je pravděpodobně nesprávně nakonfigurován pro přístup ke sériové konzoli. Informace o konfiguraci bitové kopie pro povolení sériové konzoly naleznete [v tématu Povolení konzoly sériového kříže ve vlastních nebo starších obrázcích](#enable-the-serial-console-in-custom-or-older-images).

**Otázka: Je sériová konzola k dispozici pro škálovací sady virtuálních strojů?**

A. Ano, je! Viz [Sériová konzola pro škálovací sady virtuálních strojů](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Další kroky
* Podrobný průvodce příkazy CMD a PowerShell, které můžete použít v systému Windows SAC, naleznete v [příkazech systému Windows: CMD a PowerShell](serial-console-cmd-ps-commands.md).
* Sériová konzola je k dispozici také pro virtuální počítače [s Linuxem.](serial-console-linux.md)
* Další informace o [diagnostice spuštění](boot-diagnostics.md).
