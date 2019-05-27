---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9919521c8cb77f23f50a8097c4e630b4467dc725
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127714"
---
### <a name="installation-failures"></a>Selhání instalace
| **Ukázková chybová zpráva** | **Doporučená akce** |
|--------------------------|------------------------|
|CHYBA Načtení účtů selhalo. Chyba: System.IO.IOException: Nelze číst data z přenosového připojení během instalace a registrace serveru.| Ujistěte se, že je v počítači povolený protokol TLS 1.0. |

### <a name="registration-failures"></a>Selhání registrace
Selhání registrace lze ladit kontrolou protokolů ve složce **%ProgramData%\ASRLogs**.

| **Ukázková chybová zpráva** | **Doporučená akce** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Zpráva: ACS50008: SAML token je neplatný.<br>ID trasování: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>ID korelace: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Časové razítko: **2016-12-12 14:50:08Z<br>** | Ujistěte se, že se čas na vašich systémových hodinách neliší od místního času o více než 15 minut. Znovu spusťte instalační program a dokončete registraci.|
|**09:35:27** : výjimka DRRegistrationException při pokusu o získání všech trezoru obnovení po havárii pro vybraný certifikát:: Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: SAML token je neplatný.<br>ID trasování: e5ad1af1-2d39-4970-8eef-096e325c9950<br>ID korelace: abe9deb8-3e64-464d-8375-36db9816427a<br>Časové razítko: **2016-05-19 01:35:39Z**<br> | Ujistěte se, že se čas na vašich systémových hodinách neliší od místního času o více než 15 minut. Znovu spusťte instalační program a dokončete registraci.|
|06:28:45: Nepodařilo se vytvořit certifikát.<br>06:28:45: Instalace nemůže pokračovat. Nelze vytvořit certifikát vyžadovaný k ověření v Site Recovery. Znovu spusťte instalaci. | Ujistěte se, že spouštíte instalaci jako místní správce. |
