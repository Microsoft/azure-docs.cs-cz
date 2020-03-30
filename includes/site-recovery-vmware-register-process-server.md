---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67534608"
---
1. Vytvořte připojení ke vzdálené ploše k počítači se spuštěným procesním serverem. 
2. Spusťte nástroj cspsconfigtool.exe a spusťte nástroj konfigurace serveru azure site recovery server.
    - Nástroj se spustí automaticky při prvním přihlášení k procesnímu serveru.
    - Pokud se neotevře automaticky, klikněte na jeho zástupce na ploše.

3. V **části FQDN nebo IP konfiguračního serveru**zadejte název nebo adresu IP konfiguračního serveru, na kterém se má procesový server zaregistrovat.
4. V **portu konfiguračního serveru**zkontrolujte, zda je zadána funkce 443. Toto je port, na kterém konfigurační server naslouchá požadavkům.
5. V **poli Přístupové heslo**pro připojení zadejte přístupové heslo, které jste zadali při nastavovat konfigurační server. Jak najít přístupové heslo:
    -  Na konfiguračním serveru přejděte do instalační složky Obnovení webu **\home\svssystems\bin\**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Spusťte níže uvedený příkaz pro tisk aktuálního přístupového hesla:
    ```
    genpassphrase.exe -n
    ```

6. V **portu přenosu dat**ponechejte výchozí hodnotu, pokud jste nezadali vlastní port.

7. Klikněte na **Uložit** nastavení a zaregistrujte procesní server.

    
    ![Registrace procesního serveru](./media/site-recovery-vmware-register-process-server/register-ps.png)
