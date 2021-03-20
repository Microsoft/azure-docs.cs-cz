---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8c60e0275853f3c879db22f5414f0fbbbdb47b85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050358"
---
## <a name="preparing-for-updates"></a>Příprava na aktualizace
Před kontrolou a instalací této aktualizace budete muset provést následující kroky:

1. Pořídit snímek dat zařízení v cloudu.
2. Ujistěte se, že pevné IP adresy kontroleru jsou směrovatelné a můžou se připojit k Internetu. Tyto pevné IP adresy se budou používat k aktualizaci služby na vaše zařízení. To můžete otestovat spuštěním následující rutiny na každém řadiči z rozhraní Windows PowerShell zařízení:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Ukázkový výstup pro Test-Connection, když se pevné IP adresy můžou připojit k Internetu**

    ```output
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
    ```

Po úspěšném provedení těchto ručních kontrol můžete pokračovat v kontrole a nainstalovat aktualizace.

