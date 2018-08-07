---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 08/06/2018
ms.author: ramamill
ms.openlocfilehash: 81390d38b4c0c38b7ac6883ae2bf18c64542fa00
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582889"
---
Postup zrušení registrace procesového serveru se liší v závislosti na jeho stavu připojení s konfiguračním serverem.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Zrušení registrace procesového serveru, který je v připojeném stavu

1. Vzdáleně se připojte k procesovému server jako správce.
2. Spusťte **ovládací panely** a otevřete **Programy > Odinstalovat program**.
3. Odinstalujte program s názvem **Microsoft Azure Site Recovery Configuration/Process Server**.
4. Po dokončení kroku 3 můžete odinstalovat **závislosti pro Microsoft Azure Site Recovery Configuration/Process Server**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Zrušení registrace procesového serveru, který je v odpojeném stavu

> [!WARNING]
> Uvedené pokyny by se měly použít, pokud neexistuje žádný způsob jak oživit virtuální počítač, na kterém byl nainstalován na procesní server.

1. Přihlaste se ke konfiguračnímu serveru jako správce.
2. Otevřete příkazový řádek pro správu a přejděte do adresáře `%ProgramData%\ASR\home\svsystems\bin`.
3. Nyní spusťte příkaz.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Výše uvedený příkaz poskytne seznam Procesové servery (může být více než jeden, v případě duplicitních položek) number(S.No) sériového portu, IP adresa (IP), název virtuálního počítače, na které procesový server je nasazený (název), srdce beat virtuálního počítače (prezenční signál), jak je znázorněno níže.
    ![Zrušit registraci cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Teď zadejte sériové číslo procesového serveru, který si přejete pro zrušení registrace.
6. Tím se vymažou podrobnosti procesového serveru ze systému a zobrazí tuto zprávu: **byla úspěšně odregistrována název serveru > (adresa serveru IP)**

