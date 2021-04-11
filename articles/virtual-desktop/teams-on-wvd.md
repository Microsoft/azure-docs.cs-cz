---
title: Microsoft Teams na virtuálním počítači s Windows – Azure
description: Jak používat Microsoft Teams na virtuálním počítači s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 04/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0c528f183106472850d6b5d2a8b492ea8939eda6
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285254"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Použití Microsoft Teams na virtuálním počítači s Windows

>[!IMPORTANT]
>Optimalizace médií pro týmy je podporovaná pro Microsoft 365 vládní organizace (RSZ) a GCC-High prostředí. Pro Microsoft 365 DoD není podporována optimalizace médií pro týmy.

>[!NOTE]
>Optimalizace médií pro Microsoft Teams je k dispozici pouze pro stolního klienta Windows na počítačích s Windows 10. Optimalizace médií vyžadují desktopový klient Windows verze 1.2.1026.0 nebo novější.

Microsoft Teams na virtuálním počítači s Windows podporuje chat a spolupráci. S optimalizací médií podporuje také funkce volání a schůzky. Další informace o tom, jak používat Microsoft Teams v prostředí infrastruktury virtuálních klientských počítačů (VDI), najdete v tématu [týmy pro infrastrukturu virtualizovaných ploch](/microsoftteams/teams-for-vdi/).

Díky optimalizaci médií pro Microsoft Teams klient pro stolní počítače s Windows zpracovává zvuk a video místně pro volání a schůzky týmů. Na virtuálním počítači s Windows můžete dál používat Microsoft Teams s ostatními klienty bez optimalizovaného volání a schůzek. Týmy a funkce pro spolupráci jsou podporované na všech platformách. Pokud chcete přesměrovat místní zařízení ve vzdálené relaci, přečtěte si [Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) pro fond hostitelů](#customize-remote-desktop-protocol-properties-for-a-host-pool).

## <a name="prerequisites"></a>Požadavky

Než budete moct používat Microsoft Teams na virtuálním počítači s Windows, musíte provést tyto akce:

- [Připravte síť](/microsoftteams/prepare-network/) pro Microsoft Teams.
- Nainstalujte [klienta pro stolní počítače Windows](connect-windows-7-10.md) na zařízení s Windows 10 nebo Windows 10 IoT Enterprise, které splňuje hardwarové požadavky Microsoft Teams [pro týmy na počítači s Windows](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/).
- Připojte se k virtuálnímu počítači s Windows 10 nebo Windows 10 Enterprise (VM).

## <a name="install-the-teams-desktop-app"></a>Instalace desktopové aplikace Teams

V této části se dozvíte, jak nainstalovat desktopovou aplikaci Teams na Windows 10 s více relacemi nebo na image virtuálního počítače s Windows 10 Enterprise. Pokud se chcete dozvědět víc, podívejte se na [instalaci nebo aktualizaci desktopové aplikace Teams na VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi).

### <a name="prepare-your-image-for-teams"></a>Příprava image pro týmy

Pokud chcete povolit optimalizaci médií pro týmy, nastavte na hostiteli následující klíč registru:

1. V nabídce Start spusťte program **Regedit** jako správce. Přejděte na **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams**. Pokud ještě neexistuje, vytvořte klíč Teams.

2. Pro klíč Teams vytvořte následující hodnotu:

| Název             | Typ   | Data/hodnota  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Instalace služby WebSocket pro týmy

Nainstalujte do image virtuálního počítače nejnovější [službu Vzdálená plocha WebRTC redirect](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt) . Pokud dojde k chybě instalace, nainstalujte [nejnovější Microsoft Visual C++ Distribuovatelný](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) a zkuste to znovu.

#### <a name="latest-websocket-service-versions"></a>Nejnovější verze služby WebSocket

V následující tabulce jsou uvedeny nejnovější verze služby WebSocket Service:

|Verze        |Datum vydání  |
|---------------|--------------|
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10200611001"></a>Aktualizace pro 1.0.2006.11001 verze

- Opravili jsme problém, kdy se minimalizuje aplikace Teams během volání nebo setkání způsobilo vyřazení příchozího videa.
- Přidání podpory pro výběr jednoho monitorování pro sdílení v relacích s více monitory.

### <a name="install-microsoft-teams"></a>Instalace Microsoft Teams

Aplikaci Teams Desktop můžete nasadit pomocí instalace pro jednotlivé počítače nebo uživatele. Instalace Microsoft Teams do prostředí virtuálních počítačů s Windows:

1. Stáhněte [balíček MSI Teams](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) , který odpovídá vašemu prostředí. Doporučujeme použít 64 instalačního programu na 64 operačním systému.

      > [!IMPORTANT]
      > Nejnovější aktualizace desktopového klienta Teams verze 1.3.00.21759 opravila problém, který týmy ukázaly časové pásmo UTC v chatu, kanálech a kalendáři. V nové verzi klienta se zobrazí časové pásmo vzdálené relace.

2. Spusťte jeden z následujících příkazů k instalaci MSI na hostitelský virtuální počítač:

    - Instalace vázaná na uživatele

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        Tento proces je výchozí instalací, která nainstaluje týmy do složky uživatele **% data%** . Týmy nebudou správně fungovat s instalací pro jednotlivé uživatele při netrvalé instalaci.

    - Instalace vázaná na počítač

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        Tím se nainstalují týmy do složky Program Files (x86) v operačním systému 32 a do složky Program Files v 64 operačním systému. V tuto chvíli je instalace zlaté image dokončená. Instalace týmů na počítač se vyžaduje pro netrvalá nastavení.

        K dispozici jsou dva příznaky, které mohou být nastaveny při instalaci týmů, **ALLUSER = 1** a **AllUsers = 1**. Je důležité pochopit rozdíl mezi těmito parametry. Parametr **ALLUSER = 1** se používá pouze v prostředích VDI k určení instalace pro jednotlivé počítače. Parametr **AllUsers = 1** lze použít v prostředích mimo VDI a VDI. Když nastavíte tento parametr, **teams Machine-Wide Installer** se zobrazí v programu a funkcích v Ovládacích panelech a také v aplikacích & funkcí v nastavení systému Windows. Všichni uživatelé s přihlašovacími údaji správce na počítači můžou odinstalovat týmy.

        > [!NOTE]
        > Uživatelé a správci nemůžou v současné době přihlášení zakázat automatické spuštění pro týmy.

3. Pokud chcete odinstalovat MSI z hostitelského virtuálního počítače, spusťte tento příkaz:

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Tím odinstaluje týmy ze složky Program Files (x86) nebo Program Files v závislosti na prostředí operačního systému.

      > [!NOTE]
      > Když instalujete týmy s nastavením MSI ALLUSER = 1, budou automatické aktualizace zakázané. Doporučujeme, abyste týmy aktualizovali aspoň jednou měsíčně. Další informace o nasazení desktopové aplikace Teams získáte v části [nasazení desktopové aplikace Teams do virtuálního počítače](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/).

### <a name="verify-media-optimizations-loaded"></a>Ověřit načtené optimalizace médií

Po instalaci služby WebSocket a desktopové aplikace Teams použijte následující postup k ověření, že se načetly optimalizace médií v Teams:

1. Ukončete aplikaci teams a restartujte ji.

2. Vyberte svůj obrázek profilu uživatele a pak vyberte **o** aplikaci.

3. Vyberte **verzi**.

      Pokud se načetly optimalizace médií, zobrazí se v banneru **optimalizované médium virtuálních počítačů s Windows**. Pokud se v banneru zobrazuje **médium s virtuálním počítačem s Windows není připojené**, ukončete aplikaci teams a zkuste to znovu.

4. Vyberte svůj obrázek profilu uživatele a pak vyberte **Nastavení**.

      Pokud se načtou optimalizace médií, budou se zvuková zařízení a kamery dostupné místně zobrazit v nabídce zařízení. Pokud se v nabídce zobrazuje **vzdálené zvuky**, ukončete aplikaci teams a zkuste to znovu. Pokud se zařízení v nabídce ještě nezobrazí, podívejte se na nastavení ochrany osobních údajů na místním počítači. Ujistěte se, že **Nastavení**  >    >  **oprávnění aplikace** soukromí nastavení **povoluje aplikacím přístup k vašemu mikrofonu** **zapnuté**. Odpojte se od vzdálené relace a pak znovu připojte a znovu zkontrolujte zvuková zařízení a videa. Pokud chcete spojit volání a schůzky s videem, musíte taky udělit oprávnění aplikacím pro přístup k vaší kameře.

      Pokud se optimalizace nenačte, odinstalujte a znovu nainstalujte týmy a znovu zkontrolujte.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Použití týmů ve virtualizovaném prostředí se liší od použití týmů v nevirtualizovaném prostředí. Další informace o omezeních týmů ve virtualizovaných prostředích najdete v [týmech pro infrastrukturu virtualizovaných ploch](/microsoftteams/teams-for-vdi#known-issues-and-limitations).

### <a name="client-deployment-installation-and-setup"></a>Nasazení, instalace a nastavení klienta

- Při instalaci vázané na počítač se týmy na VDI neaktualizují automaticky stejným způsobem jako klienti, kteří nejsou týmy VDI. Chcete-li aktualizovat klienta, je nutné aktualizovat image virtuálního počítače instalací nové služby MSI.
- Optimalizace médií pro týmy je podporovaná jenom pro desktopové klienta Windows na počítačích s Windows 10.
- Použití explicitních proxy serverů HTTP definovaných u koncového bodu není podporováno.

### <a name="calls-and-meetings"></a>Volání a schůzky

- Klient stolních počítačů v prostředích virtuálních ploch Windows nepodporuje vytváření živých událostí, ale můžete se připojit k živým událostem. Prozatím doporučujeme místo toho vytvořit živé události z [webového klienta Teams](https://teams.microsoft.com) ve vzdálené relaci.
- Volání nebo schůzky aktuálně nepodporují sdílení aplikací. Relace plochy podporují sdílení plochy.
- Poskytněte řízení a převzít řízení, které se v tuto chvíli nepodporují.
- Týmy na virtuálním počítači s Windows podporují jenom jeden příchozí vstup videa. To znamená, že když se někdo pokusí sdílet svoji obrazovku, zobrazí se jejich obrazovka místo na obrazovce vedoucího ovládacího prvku schůzky.
- V důsledku omezení WebRTC je řešení příchozího a odchozího streamování videa omezené na 720p.
- Aplikace Teams nepodporuje tlačítka HID ani ovládací prvky diody LED s jinými zařízeními.
- Nové prostředí pro schůzky (NME) se v prostředích VDI v současnosti nepodporuje.

Pro týmy známé problémy, které nesouvisejí s virtualizovanými prostředími, najdete informace v tématu [Podpora týmů ve vaší organizaci](/microsoftteams/known-issues).

## <a name="collect-teams-logs"></a>Shromažďovat protokoly týmů

Pokud narazíte na problémy s desktopovou aplikací Teams v prostředí virtuálních počítačů s Windows, shromážděte na virtuálním počítači hostitele protokoly klienta ve službě **% data% \Microsoft\Teams\logs.txt** .

Pokud narazíte na problémy s voláními a schůzkami, Shromážděte protokoly webového klienta v týmu pomocí kombinace kláves **CTRL**  +  **+**  +  **SHIFT**  +  **1**. Protokoly se zapisují do **protokolu%USERPROFILE%\Downloads\MSTeams diagnostics DATE_TIME.txt** na HOSTITELSKÉm virtuálním počítači.

## <a name="contact-microsoft-teams-support"></a>Kontaktujte podporu Microsoft Teams

Pokud chcete kontaktovat podporu Microsoft teams, otevřete [Centrum pro správu Microsoft 365](/microsoft-365/admin/contact-support-for-business-products).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) pro fond hostitelů

Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) (RDP) fondu hostitelů, jako je například prostředí pro více monitorů nebo povolení přesměrování mikrofonu a zvuku, umožňuje poskytovat optimální prostředí pro uživatele podle svých potřeb.

Povolení přesměrování zařízení není nutné při použití týmů s optimalizací médií. Pokud používáte týmy bez optimalizace médií, nastavte následující vlastnosti protokolu RDP tak, aby umožňovaly přesměrování mikrofonu a kamery:

- `audiocapturemode:i:1` povolí záznam zvuku z místního zařízení a přesměruje zvukové aplikace ve vzdálené relaci.
- `audiomode:i:0` přehrává zvuk na místním počítači.
- `camerastoredirect:s:*` přesměruje všechny kamery.

Další informace najdete v podrobnostech o [Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) pro fond hostitelů](customize-rdp-properties.md).
