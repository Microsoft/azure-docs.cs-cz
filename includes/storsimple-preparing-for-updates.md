---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "67175200"
---
## <a name="preparing-for-updates"></a>Příprava na aktualizace
Před kontrolou a instalací této aktualizace budete muset provést následující kroky:

1. Pořídit snímek dat zařízení v cloudu.
2. Ujistěte se, že pevné IP adresy kontroleru jsou směrovatelné a můžou se připojit k Internetu. Tyto pevné IP adresy se budou používat k aktualizaci služby na vaše zařízení. To můžete otestovat spuštěním následující rutiny na každém řadiči z rozhraní Windows PowerShell zařízení:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Ukázkový výstup pro test-Connection, když se pevné IP adresy můžou připojit k Internetu**

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

Po úspěšném provedení těchto ručních kontrol můžete pokračovat v kontrole a nainstalovat aktualizace.

