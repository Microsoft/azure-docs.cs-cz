---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175200"
---
## <a name="preparing-for-updates"></a>Příprava na aktualizace
Před prohledání a aktualizací budete muset provést následující kroky:

1. Pořizovat snímek cloudu dat zařízení.
2. Ujistěte se, že pevné IP adresy řadiče jsou směrovatelné a mohou se připojit k Internetu. Tyto pevné IP adresy budou použity ke službě aktualizací vašeho zařízení. Můžete to otestovat spuštěním následující rutiny na každém řadiči z rozhraní prostředí Windows PowerShell zařízení:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Ukázkový výstup pro testovací připojení, když se pevné IP adresy mohou připojit k Internetu**

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

Po úspěšném dokončení těchto ručních předběžných kontrol můžete pokračovat v prohledávaní a instalaci aktualizací.

