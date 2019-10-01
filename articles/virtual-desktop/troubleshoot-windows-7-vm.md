---
title: Řešení potíží s virtuálními počítači s Windows 7 na virtuálním počítači s Windows – Azure
description: Jak vyřešit problémy s virtuálními počítači s Windows 7 v prostředí virtuálních počítačů s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: 7fdb46ac7cffc4eb6a791304c7a6b58378806296
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679890"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Řešení potíží s virtuálními počítači s Windows 7 na virtuálním počítači s Windows

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

1. Otevřete Azure Portal.
2. Vyberte virtuální počítač, na kterém jste viděli chybovou zprávu.
3. Vyberte **Spustit příkaz**.
4. Spusťte následující příkaz s `<username>` nahrazeným názvem uživatele, který chcete přidat:
   
   ```cmd
   localgroup "Remote Desktop Users" <username> /add
   ```