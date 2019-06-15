---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66149602"
---
## <a name="preparing-for-updates"></a>Příprava pro aktualizace
Budete muset před zkontrolovat a nainstalovat aktualizaci, proveďte následující kroky:

1. Pořiďte snímek v cloudu dat zařízení.
2. Ujistěte se, že pevné IP adresy řadiče jsou směrovatelné a můžete se připojit k Internetu. Tyto pevné IP adresy se použije ke zpracování aktualizací do zařízení. Můžete ho otestovat spuštěním následující rutiny na každý kontroler rozhraní prostředí Windows PowerShell pro zařízení:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Ukázkový výstup pro Test-Connection při pevné IP adresy můžete připojit k Internetu**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Po úspěšném dokončení těchto ruční předběžné kontroly můžete kontrolovat a instalovat aktualizace.

