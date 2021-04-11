---
title: Virtuální počítače s Windows 7 – virtuální plocha Windows (Classic) – Azure
description: Jak vyřešit problémy s virtuálními počítači s Windows 7 v prostředí s virtuálním počítačem s Windows (Classic)
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 99c1c76092714c58fd13d9ce51ef850971d80c86
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444118"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>Řešení potíží s virtuálními počítači s Windows 7 na virtuálním počítači s Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows.

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