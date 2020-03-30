---
title: Virtuální počítače se systémem Windows 7 Windows Virtual Desktop – Azure
description: Jak vyřešit problémy s virtuálními počítači (VM) windows 7 v prostředí Virtuální plochy Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127383"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Řešení potíží s virtuálními počítači s Windows 7 ve Windows Virtual Desktopu

Tento článek slouží k řešení problémů, které máte při konfiguraci virtuálních počítačů (VM) relací windows virtuálních počítačů.

## <a name="known-issues"></a>Známé problémy

Systém Windows 7 na virtuálních plochách windows nepodporuje následující funkce:

- Virtualizované aplikace (RemoteApps)
- Přesměrování časového pásma
- Automatické škálování DPI

Windows Virtual Desktop může virtualizovat pouze plné plochy pro Windows 7.

I když automatické škálování DPI není podporováno, můžete ručně změnit rozlišení virtuálního počítače kliknutím pravým tlačítkem myši na ikonu v klientovi Vzdálené plochy a výběrem **možnosti Rozlišení**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Chyba: Nelze získat přístup ke skupině Uživatel vzdálené plochy.

Pokud virtuální plocha systému Windows nemůže najít vaše přihlašovací údaje nebo pověření uživatelů ve skupině Uživatelvzdálené plochy, může se zobrazit jedna z následujících chybových zpráv:

- "Tento uživatel není členem skupiny Uživatel vzdálené plochy"
- "Musíte mít udělena oprávnění k přihlášení prostřednictvím služby Vzdálená plocha"

Chcete-li tuto chybu opravit, přidejte uživatele do skupiny Uživatelvzdálené plochy:

1. Otevřete web Azure Portal.
2. Vyberte virtuální počítač, na který se chybová zpráva zobrazuje.
3. Vyberte **Spustit příkaz**.
4. Spusťte následující `<username>` příkaz s nahrazeným jménem uživatele, kterého chcete přidat:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```