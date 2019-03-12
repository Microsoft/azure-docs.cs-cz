---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c689dc4f7e0957218d6504532c4b481d7673def8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554966"
---
Správný certifikát SSL zajišťuje, že posíláte šifrované informace ke správnému serveru. Kromě šifrování tento certifikát také umožňuje pro ověřování. Můžete nahrát vlastní důvěryhodný certifikát SSL prostřednictvím rozhraní PowerShell zařízení.

1. [Připojení k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Použití `Set-HcsCertificate` rutiny, na kterou certifikát nahrajete. Po zobrazení výzvy zadejte následující parametry:

    - `CertificateFilePath` – Cesta ke sdílené složce, která obsahuje soubor certifikátu v *.pfx* formátu.
    - `CertificatePassword` – Heslo použité k ochraně certifikátu.
    - `Credentials` -Uživatelské jméno a heslo pro přístup do sdílené složky, který obsahuje certifikát.

    Následující příklad ukazuje použití této rutiny:

    ```
    Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
    ```

