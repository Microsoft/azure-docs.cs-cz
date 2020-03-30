---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175535"
---
Postupujte podle pokynů pro konkrétní okolnosti.

### <a name="unregister-a-connected-process-server"></a>Zrušení registrace připojeného procesního serveru

1. Navázání vzdáleného připojení k procesnímu serveru jako správce.
2. V **Ovládacích panelech**stevírat **programy > odinstalovat program**.
3. Odinstalujte program **Microsoft Azure Site Recovery Mobility Service/Master Target Server**.
4. Odinstalujte program **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Po odinstalaci programů v krocích 3 a 4 odinstalujte **závislosti konfigurace a zpracovatelského serveru webu Microsoft Azure**.

### <a name="unregister-a-disconnected-process-server"></a>Zrušení registrace odpojeného procesního serveru

Tyto kroky použijte pouze v případě, že neexistuje žádný způsob, jak oživit počítač, na kterém je nainstalován procesní server.

1. Přihlaste se konfiguračníserver jako správce.
2. Otevřete příkazový řádek Pro `%ProgramData%\ASR\home\svsystems\bin`správu a přejděte na .
3. Spuštěním tohoto příkazu získáte seznam jednoho nebo více procesních serverů.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - Sér. Ne: sériové číslo procesního serveru.
    - IP/Name: Adresa IP a název počítače se spuštěnou procesním serverem.
    - Prezenční signál: Poslední prezenční signál z počítače procesního serveru.
    ![Zrušit registraci-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Zadejte sériové číslo procesního serveru, který chcete zrušit registraci.
5. Zrušení registrace procesního serveru odebere ze systému všechny podrobnosti a zobrazí se zpráva: **Úspěšně neregistrovaný> název serveru (server-IP-adresa)**

