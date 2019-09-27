---
title: Připojení k virtuálnímu počítači s Windows ve verzi Preview z Windows 10 nebo Windows 7 – Azure
description: Jak se připojit k virtuálnímu počítači s Windows pomocí desktopového klienta Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
ms.openlocfilehash: 49431f75fb801c683a167e1809724eeeef208561
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338456"
---
# <a name="connect-with-the-windows-desktop-client"></a>Připojení pomocí desktopového klienta Windows

> To platí pro: Windows 7 a Windows 10

K prostředkům virtuálních klientů Windows se můžete dostat na zařízeních se systémem Windows 7 nebo Windows 10 pomocí desktopového klienta Windows.

> [!IMPORTANT]
> Virtuální počítač s Windows nepodporuje klienta připojení k aplikacím RemoteApp a vzdálené ploše (RADC) ani klienta Připojení ke vzdálené ploše (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Instalace klienta pro stolní počítače se systémem Windows

Jedná se o klienty, které si aktuálně můžete stáhnout:

- [Windows 64 – bit](https://go.microsoft.com/fwlink/?linkid=2068602)

Tento seznam aktualizujeme, protože klient bude k dispozici pro další verze Windows.

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

Další informace o použití desktopového klienta Windows najdete v tématu [Začínáme s klientem Windows pro stolní počítače](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop).
