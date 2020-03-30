---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67448637"
---
Správný certifikát SSL zajišťuje, že odesíláte šifrované informace na správný server. Kromě šifrování certifikát také umožňuje ověřování. Svůj vlastní důvěryhodný certifikát SSL můžete nahrát prostřednictvím rozhraní prostředí PowerShell zařízení.

1. [Připojte se k rozhraní prostředí PowerShell](#connect-to-the-powershell-interface).
2. Pomocí `Set-HcsCertificate` rutiny nahrajte certifikát. Po zobrazení výzvy zadejte následující parametry:

   - `CertificateFilePath`- Cesta ke sdílené složce, která obsahuje soubor certifikátu ve formátu *.pfx.*
   - `CertificatePassword`- Heslo používané k ochraně certifikátu.
   - `Credentials`- Uživatelské jméno pro přístup ke sdílené položce, která obsahuje certifikát. Po zobrazení výzvy zadejte heslo ke sdílené síťové složce.

     Následující příklad ukazuje použití této rutiny:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

