---
title: Připojení k virtuálnímu počítači s Windows 10 nebo 7 – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí desktopového klienta Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b16818856ca8196b82eb8f618cf22b5fc1b6854
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612686"
---
# <a name="connect-with-the-windows-desktop-client"></a>Připojení s desktopovým klientem Windows

> Platí pro: Windows 7, Windows 10 a Windows 10 IoT Enterprise

K prostředkům virtuálních klientů Windows na zařízeních s Windows 7, Windows 10 a Windows 10 IoT Enterprise můžete přistupovat pomocí desktopového klienta Windows.

>[!NOTE]
>Klient Windows automaticky nastaví výchozí hodnotu 2019 Release pro Windows Virtual Desktop. Pokud však klient zjistí, že uživatel má také prostředky Azure Resource Manager, automaticky přidá prostředky nebo upozorní uživatele, že jsou k dispozici. 

> [!IMPORTANT]
> Virtuální počítač s Windows nepodporuje klienta připojení k aplikacím RemoteApp a vzdálené ploše (RADC) ani klienta Připojení ke vzdálené ploše (MSTSC).

> [!IMPORTANT]
> Virtuální počítač s Windows v současné době nepodporuje klienta vzdálené plochy z Windows Storu. Podpora tohoto klienta se přidá v budoucí verzi.

## <a name="install-the-windows-desktop-client"></a>Instalace klienta pro stolní počítače se systémem Windows

Vyberte klienta, který odpovídá vaší verzi Windows:

- [Windows 64 – bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 – bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [ARM64 Windows](https://go.microsoft.com/fwlink/?linkid=2098961)

Klienta můžete nainstalovat pro aktuálního uživatele, který nevyžaduje oprávnění správce, nebo může správce nainstalovat a nakonfigurovat klienta tak, aby k němu měli přístup všichni uživatelé zařízení.

Po instalaci je možné klienta spustit z nabídky Start, a to tak, že vyhledáte **vzdálenou plochu**.

## <a name="subscribe-to-a-feed"></a>Přihlášení k odběru informačního kanálu

Získejte seznam spravovaných prostředků, které máte k dispozici po přihlášení k odběru informačního kanálu poskytnutého vaším správcem. Přihlášení k odběru zpřístupňuje prostředky na vašem místním počítači.

Přihlášení k odběru informačního kanálu:

1. Otevřete klienta Windows Desktop.
2. Vyberte možnost **přihlásit k odběru** na hlavní stránce pro připojení ke službě a načtení prostředků.
3. Po zobrazení výzvy se přihlaste pomocí svého uživatelského účtu.

Po úspěšném přihlášení by se měl zobrazit seznam prostředků, ke kterým máte přístup.

Prostředky můžete spustit jedním ze dvou způsobů.

- Na hlavní stránce klienta dvakrát klikněte na prostředek a spusťte ho.
- Prostředek se spustí stejným způsobem jako ostatní aplikace z nabídky Start.
  - Aplikace můžete vyhledat také na panelu hledání.

Po přihlášení k odběru informačního kanálu se obsah informačního kanálu pravidelně aktualizuje v pravidelných intervalech. Prostředky je možné přidat, změnit nebo odebrat na základě změn provedených správcem.

## <a name="next-steps"></a>Další kroky

Další informace o použití desktopového klienta Windows najdete v tématu [Začínáme s klientem Windows pro stolní počítače](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
