---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161211"
---
Správný certifikát SSL zajišťuje, že posíláte šifrované informace ke správnému serveru. Kromě šifrování tento certifikát také umožňuje pro ověřování. Můžete nahrát vlastní důvěryhodný certifikát SSL prostřednictvím rozhraní PowerShell zařízení.

1. [Připojení k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Použití `Set-HcsCertificate` rutiny, na kterou certifikát nahrajete. Po zobrazení výzvy zadejte následující parametry:

   - `CertificateFilePath` – Cesta ke sdílené složce, která obsahuje soubor certifikátu v *.pfx* formátu.
   - `CertificatePassword` – Heslo použité k ochraně certifikátu.
   - `Credentials` -Uživatelské jméno a heslo pro přístup do sdílené složky, který obsahuje certifikát.

     Následující příklad ukazuje použití této rutiny:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

