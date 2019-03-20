---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908531"
---
Postup zrušení registrace procesového serveru se liší v závislosti na jeho stavu připojení s konfiguračním serverem.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Zrušení registrace procesového serveru, který je v připojeném stavu

1. Vzdáleně se připojte k procesovému server jako správce.
2. Spusťte **ovládací panely** a otevřete **programy > odinstalovat program**.
3. Odinstalujte program s názvem **Microsoft Azure Site Recovery Mobility Service nebo hlavní cílový Server**.
4. Odinstalujte program s názvem **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Když se odinstalují programy v krocích 3 a 4, můžete odinstalovat **Microsoft Azure Site Recovery Configuration/Process Server závislosti**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Zrušení registrace procesového serveru, který je v odpojeném stavu

> [!WARNING]
> Použití níže uvedené kroky, pokud neexistuje žádný způsob jak oživit virtuální počítač, na kterém byl nainstalován na Procesovém serveru.

1. Přihlaste se ke konfiguračnímu serveru jako správce.
2. Otevřete příkazový řádek pro správu a přejděte do adresáře `%ProgramData%\ASR\home\svsystems\bin`.
3. Nyní spusťte příkaz.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Výše uvedený příkaz poskytne seznam Procesové servery (může být více než jeden, v případě duplicitních položek) number(S.No) sériového portu, IP adresa (IP), název virtuálního počítače, na které procesový server je nasazený (název), srdce beat virtuálního počítače (prezenční signál), jak je znázorněno níže.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Teď zadejte sériové číslo procesového serveru, který si přejete pro zrušení registrace.
6. Tím se vymažou podrobnosti procesového serveru ze systému a zobrazí tuto zprávu: **Název serveru se úspěšně odhlásil > (adresa serveru IP)**

