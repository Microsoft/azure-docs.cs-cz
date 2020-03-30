---
title: Připojení k Virtuální ploše Windows 10 nebo 7 – Azure
description: Jak se připojit k virtuální ploše systému Windows pomocí klienta Plochy systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154334"
---
# <a name="connect-with-the-windows-desktop-client"></a>Připojení s desktopovým klientem Windows

> Platí pro: Windows 7, Windows 10 a Windows 10 IoT Enterprise

K prostředkům Virtuální plochy Windows můžete přistupovat na zařízeních se systémem Windows 7, Windows 10 a Windows 10 IoT Enterprise pomocí klienta Windows Desktop.

> [!IMPORTANT]
> Virtuální plocha systému Windows nepodporuje klienta RADC (RemoteApp and Desktop Connections) ani klienta MSTSC (Připojení ke vzdálené ploše).

> [!IMPORTANT]
> Virtuální plocha systému Windows momentálně nepodporuje klienta vzdálené plochy z Windows Storu. Podpora pro tohoto klienta bude přidána v budoucí verzi.

## <a name="install-the-windows-desktop-client"></a>Instalace klienta Plochy systému Windows

Vyberte klienta, který odpovídá vaší verzi Windows:

- [Windows 64bitový](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32bitový](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Můžete nainstalovat klienta pro aktuálního uživatele, který nevyžaduje práva správce, nebo správce může nainstalovat a nakonfigurovat klienta tak, aby k němu měli přístup všichni uživatelé na zařízení.

Po instalaci lze klienta spustit z nabídky Start vyhledáním **vzdálené plochy**.

## <a name="subscribe-to-a-feed"></a>Přihlásit se k odběru informačního kanálu

Získejte seznam spravovaných zdrojů, které máte k dispozici, a to tak, že se přihlásíte k odběru informačního kanálu poskytnutého správcem. Přihlášením se k odběru zpřístupníte prostředky v místním počítači.

Přihlášení k odběru informačního kanálu:

1. Otevřete klienta Windows Desktop.
2. Vyberte **Přihlásit se** na hlavní stránce, abyste se připojili ke službě a načetli své prostředky.
3. Po zobrazení výzvy se přihlaste pomocí svého uživatelského účtu.

Po úspěšném přihlášení byste měli vidět seznam prostředků, ke které máte přístup.

Prostředky můžete spustit jednou ze dvou metod.

- Na hlavní stránce klienta poklepejte na prostředek a spusťte jej.
- Spusťte prostředek obvyklým způsobem, jako byste v nabídce Start normálně spustili jiné aplikace.
  - Aplikace můžete také vyhledat na panelu hledání.

Jakmile se odepsal zdroj, obsah krmiva se pravidelně aktualizuje automaticky. Prostředky mohou být přidány, změněny nebo odebrány na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o používání klienta Plochy systému Windows naleznete v části [Začínáme s klientem Plochy systému Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
