---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008465"
---
1. Navažte Připojení ke vzdálené ploše na počítači, na kterém běží procesový Server. 
2. Spusťte cspsconfigtool.exe a spusťte nástroj pro konfiguraci Azure Site Recovery procesového serveru.
    - Nástroj se spustí automaticky při prvním přihlášení k procesu serveru.
    - Pokud se nespustí automaticky, klikněte na jeho zástupce na ploše.

3. V části **plně kvalifikovaný název domény nebo IP adresa konfiguračního** serveru zadejte název nebo IP adresu konfiguračního serveru, se kterým chcete zaregistrovat procesový Server.
4. V **portu konfiguračního serveru** ověřte, že je zadaná 443. Toto je port, na kterém konfigurační server naslouchá požadavkům.
5. Do pole **heslo pro připojení** zadejte heslo, které jste zadali při nastavování konfiguračního serveru. Postup při hledání hesla:
    -  Na konfiguračním serveru přejděte do instalační složky Site Recovery **\home\svssystems\bin \**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Chcete-li vytisknout aktuální heslo, spusťte následující příkaz:
    ```
    genpassphrase.exe -n
    ```

6. V **přenos dat portu** ponechte výchozí hodnotu, pokud jste nezadali vlastní port.

7. Klikněte na **Uložit** Uložit nastavení a zaregistrujte procesový Server.

    
    ![Registrace procesového serveru](./media/site-recovery-vmware-register-process-server/register-ps.png)
