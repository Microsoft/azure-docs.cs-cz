---
title: Sériová konzola Azure pro Windows | Microsoft Docs
description: Obousměrná sériová Konzola pro Azure Virtual Machines a Virtual Machine Scale Sets.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266998"
---
# <a name="azure-serial-console-for-windows"></a>Sériová konzola Azure pro Windows

Konzola sériového portu v Azure Portal poskytuje přístup k textové konzole pro virtuální počítače s Windows a instance sady škálování virtuálních počítačů. Toto sériové připojení se připojuje k sériovému portu COM1 virtuálního počítače nebo instance sady škálování virtuálních počítačů a poskytuje přístup k němu nezávisle na stavu sítě nebo operačního systému. Ke konzole sériového přístupu se dá přistupovat jenom pomocí Azure Portal a je povolená jenom pro uživatele, kteří mají roli přístupu přispěvatel nebo vyšší, k virtuálnímu počítači nebo sadě škálování virtuálních počítačů.

Sériová konzola funguje stejným způsobem pro virtuální počítače a instance sady škálování virtuálních počítačů. V tomto dokumentu budou všechny zmínky k virtuálním počítačům implicitně zahrnovat instance sady škálování virtuálních počítačů, pokud není uvedeno jinak.

Dokumentaci k sériové konzole pro Linux najdete v tématu věnovaném [sériové konzole Azure pro Linux](serial-console-linux.md).

> [!NOTE]
> Konzola sériového portu je všeobecně dostupná v globálních oblastech Azure a ve veřejné verzi Preview v Azure Government. Ještě není k dispozici v cloudu Azure Čína.


## <a name="prerequisites"></a>Požadavky

* Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí používat model nasazení správy prostředků. Klasická nasazení nejsou podporovány.

- Váš účet, který používá sériová konzola, musí mít [roli Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) pro virtuální počítač a účet úložiště [diagnostiky spouštění](boot-diagnostics.md) .

- Vaše virtuální počítač nebo instance sady škálování virtuálního počítače musí mít uživatele založené na heslech. Můžete ho vytvořit pomocí funkce [resetovat heslo](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozšíření pro přístup k virtuálním počítačům. V části **Podpora a řešení potíží** vyberte **resetovat heslo** .

* Virtuální počítač pro instanci sady škálování virtuálního počítače musí mít povolenou [diagnostiku spouštění](boot-diagnostics.md) .

    ![Nastavení diagnostiky spouštění](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Povolení funkce sériové konzoly pro Windows Server

> [!NOTE]
> Pokud nevidíte cokoli v konzole sériového portu, ujistěte se, že je na vašem VIRTUÁLNÍm počítači nebo v sadě škálování virtuálního počítače povolená Diagnostika spouštění.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Povolit konzole sériového portu v obrázcích vlastní nebo starší
Novější image Windows serveru v Azure mají ve výchozím nastavení povolenou [Speciální konzolu pro správu](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC). SAC je podporováno ve verzích Windows na serveru ale není k dispozici ve verzích klienta (například Windows 10, Windows 8 nebo Windows 7).

Pro starší Image Windows serveru (vytvořeny před únorem 2018) můžete automaticky povolí konzole sériového portu pomocí webu Azure portal spusťte příkaz funkce. V Azure Portal vyberte **Spustit příkaz**a potom ze seznamu vyberte příkaz s názvem **EnableEMS** .

![Spuštění seznamu příkazů](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Pokud chcete ručně povolit sériovou konzolu pro virtuální počítače s Windows nebo sadu škálování virtuálního počítače vytvořené před únorem 2018, použijte následující postup:

1. Připojení k virtuálnímu počítači Windows pomocí vzdálené plochy
1. Z příkazového řádku pro správu, spusťte následující příkazy:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Restartujte systém pro konzolu SAC povolit.

    ![SAC konzoly](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

V případě potřeby SAC se dá nastavit v offline režimu také:

1. Připojte disk systému windows, pro které chcete SAC nakonfigurovaný jako datového disku k existujícímu virtuálnímu počítači.

1. Z příkazového řádku pro správu, spusťte následující příkazy:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Jak poznám, jestli je povolené SAC?

Pokud [Konzola SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) není povolená, zobrazí se v konzole sériového zobrazení výzva SAC. V některých případech se zobrazí informace o stavu virtuálních počítačů a v ostatních případech je prázdný. Pokud používáte image Windows serveru vytvořeny před únorem 2018, SAC pravděpodobně nebude povolena.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Povolit spouštěcí nabídky Windows v konzole sériového portu

Pokud je potřeba povolit Windows spouštěcí zavaděč výzvy k zobrazení v konzole sériového portu, můžete přidat následující další možnosti do konfiguračních dat spouštění. Další informace naleznete v tématu [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Připojte se k VIRTUÁLNÍmu počítači s Windows nebo k instanci sady škálování virtuálního počítače pomocí vzdálené plochy.

1. Z příkazového řádku pro správu, spusťte následující příkazy:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Restartování systému pro spouštěcí nabídky, aby byl povolen

> [!NOTE]
> Časový limit, který jste nastavili pro spouštěcí nabídky Správce pro zobrazení bude mít vliv na váš čas spuštění operačního systému. Pokud se domníváte, že hodnota 10 sekund časového limitu je příliš krátký nebo příliš dlouhý, nastavte ji na jinou hodnotu.

## <a name="use-serial-console"></a>Použití sériové konzoly

### <a name="use-cmd-or-powershell-in-serial-console"></a>Použití CMD nebo PowerShellu v sériové konzole

1. Připojte se ke konzole sériového portu. Pokud se úspěšně připojíte, zobrazí se výzva **SAC >** :

    ![Připojte se k SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Zadejte `cmd` pro vytvoření kanálu, který má instanci CMD.

1.  Zadejte `ch -si 1` nebo stiskněte klávesovou zkratku `<esc>+<tab>` a přepněte se na kanál, na kterém je spuštěná instance CMD.

1.  Stiskněte klávesu **ENTER**a potom zadejte přihlašovací údaje s oprávněními správce.

1.  Po zadání platné přihlašovací údaje, se otevře CMD instance.

1.  Pokud chcete spustit instanci prostředí PowerShell, zadejte do instance CMD `PowerShell` a potom stiskněte klávesu **ENTER**.

    ![Otevřené instance prostředí PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Použít pro volání NMI konzole sériového portu
Maskable přerušení (NMI) slouží k vytvoření signál, který nebude ignorovat softwaru na virtuálním počítači. V minulosti NMIs se používají ke sledování hardwarových problémů v systémech, které vyžaduje konkrétní odezvy. V současné době se programátoři a správci systému často používají NMI jako mechanismus pro ladění nebo odstraňování potíží se systémy, které nereagují.

Konzole sériového portu je možné odeslat NMI na virtuálním počítači Azure s využitím klávesnice ikonu na panelu příkazů. Jakmile se doručí NMI, konfigurace virtuálního počítače bude řídit, jak systém reaguje při. Windows by šlo o chybách a vytvořte soubor s výpisem paměti při příjmu NMI.

![Odeslat NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Informace o konfiguraci systému Windows k vytvoření souboru s výpisem stavu systému při přijetí NMI najdete v tématu [jak vygenerovat soubor se stavem systému pomocí NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Použít funkční klávesy v konzole sériového portu
Funkční klávesy jsou povolené pro využití konzoly sériového portu ve virtuálních počítačích Windows. F8 v rozevírací nabídce konzoly sériového portu zvyšuje pohodlí snadné zadávání nabídky Rozšířené nastavení spouštění, ale je kompatibilní se všemi klíči jiné funkce konzoly sériového portu. Na klávesnici možná budete muset stisknout **Fn** + **F1** (nebo F2, F3 atd.), a to v závislosti na počítači, ze kterého používáte sériovou konzolu.

### <a name="use-wsl-in-serial-console"></a>Použití WSL v konzole sériového portu
Subsystém Windows pro Linux (WSL) bylo povoleno pro Windows Server 2019 nebo novější, takže je také možné povolit WSL pro použití v rámci konzoly sériového portu, pokud používáte systém Windows Server 2019 nebo novější. To může být výhodné pro uživatele, kteří mají také znalost Linuxové příkazy. Pokyny k povolení WSL pro Windows Server najdete v [instalační příručce](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Restartování instance virtuálního počítače s Windows nebo virtuálního počítače v rámci sériové konzoly
Restartování v rámci konzoly sériového portu můžete iniciovat tak, že přejdete na tlačítko napájení a kliknete na restartovat virtuální počítač. Tím se iniciuje restartování virtuálního počítače a v Azure Portal se zobrazí oznámení týkající se restartování.

To je užitečné v situacích, kdy budete chtít přistupovat ke spouštěcí nabídce, aniž byste museli opustit prostředí sériové konzoly.

![Restart Windows sériové konzoly](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Zakázání sériové konzoly
Ve výchozím nastavení mají všechny odběry povolený přístup pomocí sériové konzoly. Službu sériového portu můžete zakázat buď na úrovni předplatného, nebo na úrovni sady virtuálních počítačů nebo virtuálních počítačů. Podrobné pokyny najdete v tématu [povolení a zakázání služby Azure Serial Console](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Zabezpečení konzoly sériového portu

### <a name="access-security"></a>Zabezpečení přístupu
Přístup ke konzole sériového portu je omezený na uživatele, kteří mají roli přístupu [přispěvatele virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nebo vyšší k virtuálnímu počítači. Pokud váš Azure Active Directory tenant vyžaduje vícefaktorové ověřování (MFA), bude mít přístup ke konzole sériového portu taky MFA, protože přístup ke konzole sériového prostředí probíhá přes [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Zabezpečení kanálu
Přenosu se šifrují všechna data, která se odešle vpřed a zpět.

### <a name="audit-logs"></a>Protokoly auditu
Veškerý přístup ke konzole sériového portu je aktuálně přihlášený k protokolům [diagnostiky spouštění](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) virtuálního počítače. Přístup k tyto protokoly jsou vlastněné a řídí správce virtuálních počítačů Azure.

> [!CAUTION]
> Žádná hesla přístup pro konzolu jsou protokolovány. Nicméně pokud příkazy se spouští v rámci konzoly obsahovat nebo výstup hesla, tajné kódy, uživatelská jména nebo jakoukoli jinou formu identifikovatelné osobní údaje (PII), ty se zapíšou do protokolů diagnostiky spouštění virtuálního počítače. Se zapíšou spolu s všechny ostatní viditelného textu, jako součást provádění konzoly sériového portu přejděte zpět funkce. Tyto protokoly jsou cyklické a přístup k nim mají pouze uživatelé, kteří mají oprávnění ke čtení pro účet úložiště diagnostiky. Však doporučujeme osvědčený postup pomocí vzdálené plochy pro všechno, co, která může zahrnovat tajné kódy a/nebo identifikovatelné osobní údaje.

### <a name="concurrent-usage"></a>Souběžné používání
Pokud je uživatel připojen ke konzole sériového portu a jiný uživatel úspěšně požaduje přístup k tomuto virtuálnímu počítači stejný, bude první uživatel odpojen a druhý uživatel se připojil do stejné relace.

> [!CAUTION]
> To znamená, že odpojený uživatel nebude odhlášen. Možnost vymáhat odhlášení po odpojení (pomocí SIGHUP nebo podobného mechanismu) je stále v plánu. Pro Windows je automatické vypršení časového limitu povolené v SAC; pro Linux můžete nakonfigurovat nastavení terminálu vypršení časového limitu.

## <a name="accessibility"></a>Usnadnění
Klíče se pro Azure konzoly sériového portu se usnadnění přístupu. Za tímto účelem jsme zajistili, že je přístupný pro vizuál a vaše zasažená, jakož i uživatelů, kteří nebudou moct používat myš konzole sériového portu.

### <a name="keyboard-navigation"></a>Procházení pomocí klávesnice
Pomocí klávesy **TAB** na klávesnici přejděte v rozhraní sériové konzoly z Azure Portal. Vaše poloha budou zvýrazněny na obrazovce. Pokud chcete opustit okno konzoly sériového portu, stiskněte klávesu **Ctrl**+**F6** na klávesnici.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Použití konzole sériového portu se čtečkou obrazovky
Konzole sériového portu je integrované podpoře čtečky obrazovky. Navigace pomocí čtečky obrazovky zapnuté vám umožní alternativní text pro aktuálně vybrané tlačítko nahlas číst čtečka obrazovky.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Časté scénáře pro přístup ke konzole sériového portu

Scénář          | Akce v konzole sériového portu
:------------------|:-----------------------------------------
Pravidla brány firewall na nesprávný | Přístup k sériové konzoly a opravte Windows pravidla brány firewall.
Poškození systému souborů a vrácení | Přístup ke konzole sériového portu a proveďte obnovení ze systému souborů.
Problémy s konfigurací protokolu RDP | Přístup ke konzole sériového portu a změnit nastavení. Další informace najdete v [dokumentaci k protokolu RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Uzamknutí sítě v systému | Na webu Azure Portal ke správě systému přístup ke konzole sériového portu. Některé síťové příkazy jsou uvedené v [příkazech Windows: cmd a PowerShell](serial-console-cmd-ps-commands.md).
Interakce s zaváděcího programu pro spouštění | Přístup k BCD prostřednictvím konzoly sériového portu. Informace najdete v tématu [Povolení spouštěcí nabídky Windows v konzole sériového portu](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Známé problémy
O některých problémech se používá konzola sériového portu a operační systém virtuálního počítače. Zde je uveden seznam těchto problémů a postup pro zmírnění rizik pro virtuální počítače s Windows. Tyto problémy a omezení rizik platí pro instance virtuálních počítačů i instancí sady škálování virtuálních počítačů. Pokud se neshodují s chybou, kterou vidíte, přečtěte si část běžné chyby služby sériové konzoly při [běžných chybách sériové konzoly](./serial-console-errors.md).

Problém                             |   Omezení rizik
:---------------------------------|:--------------------------------------------|
Stisknutí klávesy **ENTER** po nápisu připojení nezpůsobí zobrazení výzvy k přihlášení. | Další informace najdete v tématu o tom, že [ENTER nedělá nic](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). K této chybě může dojít, pokud používáte vlastní virtuální počítač, Posílená zařízení nebo spouštěcí konfigurace, který způsobí, že Windows nepodaří správně připojit do sériového portu. K této chybě dojde také v případě, že používáte virtuální počítač s Windows 10, protože u virtuálních počítačů se systémem Windows Server jsou povoleny pouze EMS.
Pouze informace o stavu se zobrazuje při připojení k virtuálnímu počítači s Windows| K této chybě dojde, pokud pro vaši image Windows není povolená Speciální konzola pro správu. Pokyny k ručnímu povolení konzoly SAC na vašem VIRTUÁLNÍm počítači s Windows najdete v tématu [Povolení služby sériového prostředí ve vlastních nebo starších imagí](#enable-the-serial-console-in-custom-or-older-images) . Další informace najdete v tématu [signály stavu systému Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
Konzola SAC nebere v prohlížeči celou oblast sériová konzola. | Jedná se o známý problém s Windows a emulátorem terminálu. Tento problém sledujeme u obou týmů, ale v současné době nedochází ke zmírnění rizik.
Nelze zadat v SAC řádku, pokud je povoleno ladění jádra. | Pomocí protokolu RDP na virtuální počítač spusťte `bcdedit /debug {current} off` z příkazového řádku se zvýšenými oprávněními. Pokud nemůžete používat protokol RDP, můžete místo toho připojit disk s operačním systémem k jinému virtuálnímu počítači Azure a pak ho pomocí `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`připojit jako datový disk, a pak disk znovu vyměnit.
Pokud původní obsah měli opakující se znak, vkládání do prostředí PowerShell ve výsledcích SAC ve třetí znaku. | V případě alternativního řešení spusťte `Remove-Module PSReadLine` a uvolněte modul PSReadLine z aktuální relace. Tato akce neodstraní ani odinstalace modulu.
Některé vstupy klávesnice vytvoří neobvyklý výstup konzoly SAC (například **[A**, **[3 ~** ). | Řídicí sekvence [VT100](https://aka.ms/vtsequences) se v příkazovém řádku konzoly SAC nepodporují.
Vkládání dlouhé řetězce nebude fungovat. | Konzole sériového portu omezení délky řetězce do terminálu na 2 048 znaků, aby se zabránilo přetížení šířky pásma sériového portu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Jak mohu odeslat zpětnou vazbu?**

A. Poskytněte zpětnou vazbu vytvořením problému GitHubu na adrese https://aka.ms/serialconsolefeedback. Případně (méně upřednostňovaná) můžete odeslat zpětnou vazbu prostřednictvím azserialhelp@microsoft.com nebo ve https://feedback.azure.comkategorie virtuálního počítače.

**Otázka: podporuje konzola sériového kopírování/vkládání?**

A. Ano. K zkopírování a vložení do terminálu použijte **kombinaci kláves ctrl**+**SHIFT**+**C** a **CTRL**+**SHIFT**+**V** .

**Otázka. kdo může povolit nebo zakázat sériovou konzolu pro moje předplatné?**

A. K povolení nebo zakázání konzole sériového portu na úrovni celé předplatné, musí mít oprávnění k zápisu do předplatného. Role, které mají oprávnění k zápisu zahrnují role správce nebo vlastníka. Vlastní role můžete také mít oprávnění k zápisu.

**Dotaz, který má přístup ke konzole sériového připojení k virtuálnímu počítači?**

A. Musíte mít roli Přispěvatel virtuálních počítačů nebo vyšší pro virtuální počítač pro přístup ke konzole sériového portu Virtuálního počítače.

**Otázka. moje konzola sériového zobrazení nezobrazuje vše, co mám dělat?**

A. Vaše image je pravděpodobně nesprávně nakonfigurované pro přístup ke konzole sériového portu. Informace o konfiguraci image pro povolení konzole sériového prostředí najdete v tématu [Povolení sériové konzoly ve vlastních nebo starších bitových kopiích](#enable-the-serial-console-in-custom-or-older-images).

**Otázka. je k dispozici konzola sériového portu pro služby Virtual Machine Scale Sets?**

A. Ano, je! Další informace najdete v tématu [sériová Konzola pro Virtual Machine Scale Sets](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Další kroky
* Podrobný průvodce příkazy CMD a PowerShellu, které můžete použít v konzole Windows SAC, najdete v tématu [příkazy Windows: cmd a PowerShell](serial-console-cmd-ps-commands.md).
* K dispozici je také konzola sériového portu pro virtuální počítače se [systémem Linux](serial-console-linux.md) .
* Přečtěte si další informace o [diagnostice spouštění](boot-diagnostics.md).
