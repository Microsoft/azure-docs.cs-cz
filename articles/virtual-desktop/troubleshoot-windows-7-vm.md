---
title: Virtuální počítač se systémem Windows 7 – Azure
description: Jak vyřešit problémy s virtuálními počítači s Windows 7 v prostředí virtuálních počítačů s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79127383"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Řešení potíží s virtuálními počítači s Windows 7 ve Windows Virtual Desktopu

Tento článek použijte k řešení problémů, které máte při konfiguraci virtuálních počítačů hostitele relace virtuálních počítačů (VM) Windows.

## <a name="known-issues"></a>Známé problémy

Windows 7 na virtuálních plochách Windows nepodporuje následující funkce:

- Virtualizované aplikace (RemoteApp)
- Přesměrování časového pásma
- Automatické škálování DPI

Virtuální plocha Windows může virtualizovat jenom kompletní plochy pro Windows 7.

I když automatické škálování v DPI není podporováno, můžete ručně změnit rozlišení na virtuálním počítači kliknutím pravým tlačítkem myši na ikonu v klientovi vzdálené plochy a výběrem možnosti **řešení**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Chyba: nepovedlo se získat přístup ke skupině uživatelů vzdálené plochy.

Pokud virtuální počítač s Windows nemůže najít nebo přihlašovací údaje vašich uživatelů ve skupině uživatelů vzdálené plochy, může se zobrazit jedna z následujících chybových zpráv:

- "Tento uživatel není členem skupiny uživatelů vzdálené plochy"
- "Je nutné mít udělena oprávnění k přihlášení prostřednictvím služby Vzdálená plocha."

Tuto chybu opravíte tak, že přidáte uživatele do skupiny uživatelů vzdálené plochy:

1. Otevřete web Azure Portal.
2. Vyberte virtuální počítač, na kterém jste viděli chybovou zprávu.
3. Vyberte **Spustit příkaz**.
4. Spusťte následující příkaz, `<username>` který nahrazuje jméno uživatele, kterého chcete přidat:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```