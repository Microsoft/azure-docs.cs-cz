---
title: Microsoft Teams na virtuálním počítači s Windows – Azure
description: Jak používat Microsoft Teams na virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 65f0cf66dc6cef33e98258106e5f1573efdf39e5
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84203852"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Použití Microsoft Teams na virtuálním počítači s Windows

>[!IMPORTANT]
>Optimalizace médií pro Microsoft Teams je aktuálně ve verzi Public Preview. Před nasazením týmů pro produkční úlohy doporučujeme vyhodnotit uživatelské prostředí optimalizované týmy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.

>[!NOTE]
>Optimalizace médií pro Microsoft Teams je k dispozici pouze pro stolního klienta Windows na počítačích s Windows 10. Optimalizace médií vyžadují desktopový klient Windows verze 1.2.1026.0 nebo novější.

Microsoft Teams na virtuálním počítači s Windows podporuje chat a spolupráci. S optimalizací médií podporuje také funkce volání a schůzky. Další informace o tom, jak používat Microsoft Teams v prostředí infrastruktury virtuálních klientských počítačů (VDI), najdete v tématu [týmy pro infrastrukturu virtualizovaných ploch](/microsoftteams/teams-for-vdi/).

Díky optimalizaci médií pro Microsoft Teams klient pro stolní počítače s Windows zpracovává zvuk a video místně pro volání a schůzky týmů. Na virtuálním počítači s Windows můžete dál používat Microsoft Teams s ostatními klienty bez optimalizovaného volání a schůzek. Týmy a funkce pro spolupráci jsou podporované na všech platformách. Pokud chcete přesměrovat místní zařízení ve vzdálené relaci, přečtěte si [Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) pro fond hostitelů](#customize-remote-desktop-protocol-properties-for-a-host-pool).

## <a name="prerequisites"></a>Požadavky

Než budete moct používat Microsoft Teams na virtuálním počítači s Windows, musíte provést tyto akce:

- [Připravte síť](/microsoftteams/prepare-network/) pro Microsoft Teams.
- Nainstalujte [desktopový klient Windows](connect-windows-7-and-10.md) na zařízení s Windows 10, které splňuje [hardwarové požadavky](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)Microsoft Teams.
- Připojte se k virtuálnímu počítači s Windows 10 nebo Windows 10 Enterprise (VM).

## <a name="install-the-teams-desktop-app"></a>Instalace desktopové aplikace Teams

V této části se dozvíte, jak nainstalovat desktopovou aplikaci Teams na Windows 10 s více relacemi nebo na image virtuálního počítače s Windows 10 Enterprise. Pokud se chcete dozvědět víc, podívejte se na [instalaci nebo aktualizaci desktopové aplikace Teams na VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi/).

### <a name="prepare-your-image-for-teams"></a>Příprava image pro týmy

Pokud chcete povolit týmy pro instalaci na počítač, nastavte na hostiteli následující klíč registru:

1. V nabídce Start spusťte program **Regedit** jako správce. Přejděte na **HKEY_LOCAL_MACHINE \software\microsoft\teams**.
2. Pro klíč Teams vytvořte následující hodnotu:

| Název             | Typ   | Data/hodnota  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Instalace služby WebSocket pro týmy

Nainstalujte do image virtuálního počítače [službu WebSocket](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4vkL6) . Pokud dojde k chybě instalace, nainstalujte [nejnovější Microsoft Visual C++ Distribuovatelný](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) a zkuste to znovu.

### <a name="install-microsoft-teams"></a>Instalace Microsoft Teams

Aplikaci Teams Desktop můžete nasadit pomocí instalace pro jednotlivé počítače. Instalace Microsoft Teams do prostředí virtuálních počítačů s Windows:

1. Stáhněte [balíček MSI Teams](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/) , který odpovídá vašemu prostředí. Doporučujeme použít 64 instalačního programu na 64 operačním systému.
2. Spuštěním tohoto příkazu nainstalujete MSI na hostitelský virtuální počítač.

      ```console
      msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1 ALLUSERS=1
      ```

      Tím se nainstalují týmy do složky Program Files (x86) v operačním systému 64 a do složky Program Files v 32 operačním systému. V tuto chvíli je instalace zlaté image dokončená. Instalace týmů na počítač se vyžaduje pro netrvalá nastavení.

      Při příštím otevření týmů v relaci budete požádáni o zadání vašich přihlašovacích údajů.

      > [!NOTE]
      > Uživatelé a správci nemůžou v současné době přihlášení zakázat automatické spuštění pro týmy.

      Pokud chcete odinstalovat MSI z hostitelského virtuálního počítače, spusťte tento příkaz:

      ```console
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Tím odinstaluje týmy ze složky Program Files (x86) nebo Program Files v závislosti na prostředí operačního systému.

      > [!NOTE]
      > Když instalujete týmy s nastavením MSI ALLUSER = 1, budou automatické aktualizace zakázané. Doporučujeme, abyste týmy aktualizovali aspoň jednou měsíčně. Další informace o nasazení desktopové aplikace Teams získáte v části [nasazení desktopové aplikace Teams do virtuálního počítače](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/).

### <a name="verify-media-optimizations-loaded"></a>Ověřit načtené optimalizace médií

Po instalaci služby WebSocket a desktopové aplikace Teams použijte následující postup k ověření, že se načetly optimalizace médií v Teams:

1. Vyberte svůj obrázek profilu uživatele a pak vyberte **o**aplikaci.
2. Vyberte **verzi**.

      Pokud jste načetli optimalizace médií, zobrazí se v banneru **WVD Media optimalizované pro multimédia**. Pokud se v banneru zobrazuje **WVD médium Nepřipojeno**, ukončete aplikaci teams a zkuste to znovu.

3. Vyberte svůj obrázek profilu uživatele a pak vyberte **Nastavení**.

      Pokud se načtou optimalizace médií, budou se zvuková zařízení a kamery dostupné místně zobrazit v nabídce zařízení. Pokud se v nabídce zobrazuje **vzdálené zvuky**, ukončete aplikaci teams a zkuste to znovu. Pokud se zařízení v nabídce ještě nezobrazují, ověřte, že jste provedli všechny výše uvedené kroky instalace.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

Použití týmů ve virtualizovaném prostředí se liší od použití týmů v nevirtualizovaném prostředí. Další informace o omezeních týmů ve virtualizovaných prostředích najdete v [týmech pro infrastrukturu virtualizovaných ploch](/microsoftteams/teams-for-vdi#known-issues-and-limitations/).

### <a name="client-deployment-installation-and-setup"></a>Nasazení, instalace a nastavení klienta

- Při instalaci vázané na počítač se týmy na VDI neaktualizují automaticky stejným způsobem jako klienti, kteří nejsou týmy VDI. Chcete-li aktualizovat klienta, je nutné aktualizovat image virtuálního počítače instalací nové služby MSI.
- Týmy aktuálně zobrazují časové pásmo UTC v chatu, kanálech a kalendáři.
- Optimalizace médií pro týmy je podporovaná jenom pro desktopové klienta Windows na počítačích s Windows 10.
- Použití explicitních proxy serverů HTTP definovaných u koncového bodu není podporováno.

### <a name="calls-and-meetings"></a>Volání a schůzky

- Desktopový klient pro týmy v prostředích virtuálních ploch Windows nepodporuje živé události. Prozatím doporučujeme připojit živé události od [webového klienta Teams](https://teams.microsoft.com) ve vzdálené relaci.
- Minimalizace aplikace Teams během volání nebo schůzky může vést k tomu, že při rozbalení aplikace dojde k nezobrazení příchozího informačního kanálu videa.
- Volání nebo schůzky aktuálně nepodporují sdílení aplikací. Relace plochy podporují sdílení plochy.
- Když se v nastavení s více monitory sdílí Desktop, sdílí se všechna monitorování.
- Poskytněte řízení a převzít řízení, které se v tuto chvíli nepodporují.
- Týmy na virtuálním počítači s Windows podporují jenom jeden příchozí vstup videa. To znamená, že když se někdo pokusí sdílet svoji obrazovku, zobrazí se jejich obrazovka místo na obrazovce vedoucího ovládacího prvku schůzky.
- V důsledku omezení WebRTC je řešení příchozího a odchozího streamování videa omezené na 720p.
- Aplikace Teams nepodporuje tlačítka HID ani ovládací prvky diody LED s jinými zařízeními.

Pro týmy známé problémy, které nesouvisejí s virtualizovanými prostředími, najdete informace v tématu [Podpora týmů ve vaší organizaci](/microsoftteams/known-issues/) .

## <a name="feedback"></a>Váš názor

Poskytněte zpětnou vazbu pro Microsoft Teams na virtuální ploše Windows na webu Teams [UserVoice](https://microsoftteams.uservoice.com/).

## <a name="collect-teams-logs"></a>Shromažďovat protokoly týmů

Pokud narazíte na problémy s desktopovou aplikací Teams v prostředí virtuálních počítačů s Windows, shromážděte na virtuálním počítači hostitele protokoly klienta v části **%AppData%\Microsoft\Teams\logs.txt** .

Pokud narazíte na problémy s voláními a schůzkami, Shromážděte protokoly webového klienta v týmu pomocí kombinace kláves **CTRL**  +  **+**  +  **SHIFT**  +  **1**. Protokoly se zapisují do **protokolu%USERPROFILE%\Downloads\MSTeams diagnostics DATE_TIME. txt** na HOSTITELSKÉm virtuálním počítači.

## <a name="contact-microsoft-teams-support"></a>Kontaktujte podporu Microsoft Teams

Pokud chcete kontaktovat podporu Microsoft teams, otevřete [Centrum pro správu Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/contact-support-for-business-products?view=o365-worldwide&tabs=online).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) pro fond hostitelů

Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) (RDP) fondu hostitelů, jako je například prostředí pro více monitorů nebo povolení přesměrování mikrofonu a zvuku, umožňuje poskytovat optimální prostředí pro uživatele podle svých potřeb.

Nastavte následující vlastnosti protokolu RDP, aby se povolilo přesměrování mikrofonu a kamery:

|Vlastnosti protokolu RDP   | Popis |
|-----------------|-------------|
|audiocapturemode: i: 1  | Povolit zachytávání zvuku z místního zařízení a přesměrování na zvukovou aplikaci ve vzdálené relaci |
|audiomode: i: 0         | Přehrát zvuk v místním počítači |
|camerastoredirect: s: * | Přesměrovat všechny kamery |

Další informace najdete v podrobnostech o [Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) pro fond hostitelů](customize-rdp-properties.md).
