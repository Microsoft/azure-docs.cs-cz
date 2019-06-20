---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: cf39baf34096691144181332566cf567ebc02310
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175534"
---
1. Navázat připojení ke vzdálené ploše na daném počítači používají procesový server. 
2. Spusťte cspsconfigtool.exe spusťte konfigurační nástroj Azure Site Recovery Process Server.
    - Nástroj se spustí automaticky při prvním přihlášení k procesového serveru.
    - Pokud se neotevře automaticky, klikněte na jeho zástupce na ploše.

3. V **konfigurační server plně kvalifikovaný název domény nebo IP adresa**, zadejte název nebo IP adresu konfiguračního serveru, pomocí kterého se má registrace procesového serveru.
4. V **Port serveru konfigurace**, zkontrolujte, zda je zadán 443. Toto je port, na kterém konfigurační server naslouchá požadavkům.
5. V **přístupové heslo připojení**, zadejte heslo, které jste zadali při nastavení konfiguračního serveru. Vyhledání přístupového hesla:
    -  Na konfiguračním serveru, přejděte do instalační složky Site Recovery * *\home\svssystems\bin\** . 
    - Spusťte tento příkaz: **genpassphrase.exe.n**. Zobrazí umístění přístupové heslo, které lze poté si poznamenejte.

6. V **Port pro přenos dat**, ponechte výchozí hodnotu, pokud jste zadali vlastní port.

7. Klikněte na tlačítko **Uložit** uložíte nastavení a zaregistrujte procesový server.

    
    ![Registrace procesového serveru](./media/site-recovery-vmware-register-process-server/register-ps.png)
