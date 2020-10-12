---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67534608"
---
1. Navažte Připojení ke vzdálené ploše na počítači, na kterém běží procesový Server. 
2. Spusťte cspsconfigtool.exe a spusťte nástroj pro konfiguraci Azure Site Recovery procesového serveru.
    - Nástroj se spustí automaticky při prvním přihlášení k procesu serveru.
    - Pokud se nespustí automaticky, klikněte na jeho zástupce na ploše.

3. V části **plně kvalifikovaný název domény nebo IP adresa konfiguračního**serveru zadejte název nebo IP adresu konfiguračního serveru, se kterým chcete zaregistrovat procesový Server.
4. V **portu konfiguračního serveru**ověřte, že je zadaná 443. Toto je port, na kterém konfigurační server naslouchá požadavkům.
5. Do pole **heslo pro připojení**zadejte heslo, které jste zadali při nastavování konfiguračního serveru. Postup při hledání hesla:
    -  Na konfiguračním serveru přejděte do instalační složky Site Recovery **\home\svssystems\bin \* *:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Chcete-li vytisknout aktuální heslo, spusťte následující příkaz:
    ```
    genpassphrase.exe -n
    ```

6. V **přenos dat portu**ponechte výchozí hodnotu, pokud jste nezadali vlastní port.

7. Klikněte na **Uložit** Uložit nastavení a zaregistrujte procesový Server.

    
    ![Registrace procesového serveru](./media/site-recovery-vmware-register-process-server/register-ps.png)
