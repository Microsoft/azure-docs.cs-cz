---
title: Microsoft Teams na virtuálním počítači s Windows – Azure
description: Jak používat Microsoft Teams na virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187524"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Použití Microsoft Teams na virtuálním počítači s Windows

> Platí pro: Windows 10 a Windows 10 IoT Enterprise

Virtualizovaná prostředí představují jedinečnou sadu výzev pro aplikace pro spolupráci, jako jsou Microsoft teams, včetně zvýšené latence, vysokého využití procesoru hostitele a špatného celkového výkonu zvuku a videa. Pokud se chcete dozvědět víc o používání Microsoft Teams v prostředích VDI, Projděte si [týmy pro infrastrukturu virtualizované plochy](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Požadavky

Než budete moct používat Microsoft Teams na virtuálním počítači s Windows, musíte provést tyto akce:

- Nainstalujte [desktopový klient Windows](connect-windows-7-and-10.md) na zařízení s Windows 10, které splňuje [hardwarové požadavky](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)Microsoft Teams.
- Připojte se k virtuálnímu počítači s Windows 10 nebo Windows 10 Enterprise (VM).
- [Připravte síť](https://docs.microsoft.com/microsoftteams/prepare-network) pro Microsoft Teams.

## <a name="use-unoptimized-microsoft-teams"></a>Použití neoptimalizovaných Microsoft Teams

Pomocí Microsoft Teams v prostředích virtuálních počítačů s Windows můžete využívat funkce chatu a spolupráce v Microsoft Teams. Virtuální počítač s Windows nepodporuje týmy na optimalizaci audio/video (AV) infrastruktury VDI. Volání a schůzky nejsou podporovány. Pokud umožňují zásady vaší organizace, můžete pořád dělat zvukové hovory a připojovat se ke schůzkám, ale neoptimalizovaná kvalita zvuku v voláních se bude lišit v závislosti na konfiguraci hostitele a nemusí být spolehlivá. Pokud se chcete dozvědět víc, podívejte se [na tým o výkonu infrastruktury virtuálních klientských](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations)počítačů.

### <a name="prepare-your-image-for-teams"></a>Příprava image pro týmy

Pokud chcete povolit týmy pro instalaci na počítač, nastavte na hostiteli následující klíč registru:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Instalace Microsoft Teams

Aplikaci Teams Desktop můžete nasadit pomocí instalace pro jednotlivé počítače. Instalace Microsoft Teams do prostředí virtuálních počítačů s Windows:

1. Stáhněte [balíček MSI Teams](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) , který odpovídá vašemu prostředí. Doporučujeme použít 64 instalačního programu na 64 operačním systému.
2. Spuštěním tohoto příkazu nainstalujete MSI na hostitelský virtuální počítač.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Tím se budou instalovat týmy buď do programových souborů, nebo do programových souborů (x86). Při příštím přihlášení a zahájení týmů aplikace požádá o vaše přihlašovací údaje.

      > [!NOTE]
      > Uživatelé a správci nemůžou v současné době přihlášení zakázat automatické spuštění pro týmy.

      Pokud chcete odinstalovat MSI z hostitelského virtuálního počítače, spusťte tento příkaz:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Pokud instalujete týmy s nastavením MSI ALLUSER = 1, budou automatické aktualizace zakázané. Doporučujeme, abyste týmy aktualizovali aspoň jednou měsíčně. Další informace o nasazení desktopové aplikace Teams získáte v části [nasazení desktopové aplikace Teams do virtuálního počítače](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) pro fond hostitelů
Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) (RDP) fondu hostitelů, jako je například prostředí pro více monitorů, umožňující přesměrování mikrofonu a zvuku, vám umožní zajistit optimální prostředí pro uživatele podle svých potřeb. Vlastnosti protokolu RDP můžete přizpůsobit ve virtuální ploše Windows pomocí parametru **-CustomRdpProperty** v rutině **set-RdsHostPool** .
Úplný seznam podporovaných vlastností a jejich výchozích hodnot najdete v tématu [podporované nastavení souboru RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .
