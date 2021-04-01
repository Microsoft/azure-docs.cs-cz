---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019176"
---
Použijte postup podle svých konkrétních okolností.

### <a name="unregister-a-connected-process-server"></a>Zrušení registrace připojeného procesového serveru

1. Navažte vzdálené připojení k procesu serveru jako správce.
2. V **Ovládacích panelech** otevřete **programy > odinstalovat program**.
3. Odinstalujte program **Microsoft Azure Site Recovery služby mobility nebo hlavního cílového serveru**.
4. Odinstalujte program **Microsoft Azure Site Recovery konfiguračním nebo procesovém serveru**.
5. Po odinstalování programů v krocích 3 a 4 odinstalujte **Microsoft Azure Site Recovery konfigurace a procesový Server závislosti**.

### <a name="unregister-a-disconnected-process-server"></a>Zrušení registrace odpojeného procesového serveru

Tyto kroky použijte pouze v případě, že není k dispozici žádný způsob, jak potřebuji obnovit počítač, na kterém je nainstalován procesový Server.

1. Přihlaste konfigurační server jako správce.
2. Otevřete příkazový řádek pro správu a přejděte na `%ProgramData%\ASR\home\svsystems\bin` .
3. Spuštěním tohoto příkazu získáte seznam jednoho nebo více procesových serverů.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - USA: Ne: sériové číslo procesového serveru.
    - IP/název: IP adresa a název počítače, na kterém běží procesový Server.
    - Prezenční signál: poslední prezenční signál z počítače procesového serveru.
    ! [Snímek obrazovky zobrazuje nešifrované zobrazení informací o procesových serverech a textu. Zvolte prosím jeden z výše uvedených serverů pro zrušení registrace. (Media/Site-Recovery-VMware-Unregister-Process-Server/Unregister-cmd.PNG)

4. Zadejte sériové číslo procesového serveru, který chcete zrušit.
5. Zrušení registrace procesového serveru odebere ze systému všechny jeho podrobnosti a zobrazí zprávu: **úspěšně odregistrované servery – název> (Server-IP-Address)**

