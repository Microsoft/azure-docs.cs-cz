---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448637"
---
Správný certifikát SSL zajišťuje, že posíláte šifrované informace ke správnému serveru. Kromě šifrování tento certifikát také umožňuje pro ověřování. Můžete nahrát vlastní důvěryhodný certifikát SSL prostřednictvím rozhraní PowerShell zařízení.

1. [Připojení k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Použití `Set-HcsCertificate` rutiny, na kterou certifikát nahrajete. Po zobrazení výzvy zadejte následující parametry:

   - `CertificateFilePath` – Cesta ke sdílené složce, která obsahuje soubor certifikátu v *.pfx* formátu.
   - `CertificatePassword` – Heslo použité k ochraně certifikátu.
   - `Credentials` -Uživatelské jméno pro přístup do sdílené složky, který obsahuje certifikát. Zadejte heslo do sdílené síťové složky Po zobrazení výzvy.

     Následující příklad ukazuje použití této rutiny:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

