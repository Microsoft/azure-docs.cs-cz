---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0c89ed34b21ca0c41d4f7765d99d8fe8bf7c647d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888530"
---
## <a name="preparing-for-updates"></a>Příprava pro aktualizace
Budete muset před zkontrolovat a nainstalovat aktualizaci, proveďte následující kroky:

1. Pořiďte snímek v cloudu dat zařízení.
2. Ujistěte se, že pevné IP adresy řadiče jsou směrovatelné a můžete se připojit k Internetu. Tyto pevné IP adresy se použije ke zpracování aktualizací do zařízení. Můžete ho otestovat spuštěním následující rutiny na každý kontroler rozhraní prostředí Windows PowerShell pro zařízení:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
   
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

