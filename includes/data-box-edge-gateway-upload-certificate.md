---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67448637"
---
Správný certifikát SSL zajišťuje odesílání šifrovaných informací do správného serveru. Kromě šifrování umožňuje certifikát také ověřování. Svůj důvěryhodný certifikát SSL můžete nahrát přes rozhraní PowerShell zařízení.

1. [Připojte se k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Pomocí `Set-HcsCertificate` rutiny Nahrajte certifikát. Po zobrazení výzvy zadejte následující parametry:

   - `CertificateFilePath`-Cesta ke sdílené složce, která obsahuje soubor certifikátu ve formátu *. pfx* .
   - `CertificatePassword`– Heslo použité k ochraně certifikátu.
   - `Credentials`– Uživatelské jméno pro přístup ke sdílené složce, která obsahuje certifikát. Po zobrazení výzvy zadejte heslo ke sdílené síťové složce.

     Následující příklad ukazuje použití této rutiny:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

