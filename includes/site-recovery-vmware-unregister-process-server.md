---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170030"
---
Postupujte podle kroků pro zvláštní okolnosti.

### <a name="unregister-a-connected-process-server"></a>Zrušit registraci připojený procesový server

1. Vytvořit vzdálené připojení k procesového serveru jako správce.
2. V **ovládací panely**, otevřete **programy > odinstalovat program**.
3. Odinstalujte program **Microsoft Azure Site Recovery Mobility Service nebo hlavní cílový Server**.
4. Odinstalujte program **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Odinstalují se aplikace v krocích 3 a 4, odinstalovat **Microsoft Azure Site Recovery Configuration/Process Server závislosti**.

### <a name="unregister-a-disconnected-process-server"></a>Zrušení registrace odpojeného procesového serveru

Pomocí těchto kroků, pouze pokud neexistuje žádný způsob jak oživit počítače, na kterém je nainstalovaná na procesovém serveru.

1. Přihlaste se konfigurační server jako správce.
2. Otevřete příkazový řádek pro správu a přejděte do `%ProgramData%\ASR\home\svsystems\bin`.
3. Spuštěním tohoto příkazu zobrazíte seznam jednoho nebo více procesových serverů.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - Sér. Ne: proces serveru sériové číslo.
    - IP/Name: IP adresu a název v daném počítači používají procesový server.
    - Heartbeat: Poslední prezenční signál z počítače serveru procesu.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Zadejte sériové číslo, které chcete zrušení registrace procesového serveru.
5. Zrušení registrace procesového serveru odeberte všechny její podrobnosti ze systému a zobrazí zprávu: **Název serveru se úspěšně odhlásil > (adresa serveru IP)**

