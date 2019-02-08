---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889218"
---
#### <a name="to-cable-your-device-for-power"></a>K zapojení kabeláže zařízení pro výkon
> [!NOTE]
> Obě skříně zařízení StorSimple zahrnují PCMs redundantní. Pro každou skříň musí být nainstalované PCMs a připojený k různým zdrojům napájení k zajištění vysoké dostupnosti.
> 
> 

1. Ujistěte se, že jsou napájení přepínače na všech PCMs na jinou pozici.
2. Na primární zařízení připojte se k obou PCMs napájecích kabelů. Napájecích kabelů jsou označeny červeně v diagramu kabeláže power níže.
3. Ujistěte se, že dvě PCMs na primární zařízení používat samostatné napájecí zdroje.
4. Jak je znázorněno v Power BI kabelů diagram připojte napájecích kabelů k výkonu u jednotek pro distribuci do racku.
5. Opakujte kroky 2 až 4 pro EBOD skříň.
6. Zapněte skříň EBOD překlopení vypínač na každý PCM do polohy zapnuto.
7. Ověřte, jestli je zapnutá skříň EBOD tak, že zkontrolujete, že zelený LED na zadní straně kontroleru EBOD jsou zapnuté.
8. Zapněte primární skříň překlopení každý přepínač PCM do polohy zapnuto.
9. Ověřte, že systém na zajištěním kontroleru zařízení, které jste zapnuli LED.
10. Ujistěte se, že je spojení mezi kontroleru EBOD a řadiče zařízení aktivní tak, že ověříte, že jsou čtyři LED vedle portů SAS na kontroleru EBOD zeleně.
    
    > [!IMPORTANT]
    > K zajištění vysoké dostupnosti pro váš systém, doporučujeme striktně dodržovat zásady napájení kabelů schéma je znázorněno v následujícím diagramu.
    > 
    > 
    
    ![Zapojení kabeláže zařízení 4U výkon](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Kabeláž napájení**
    
    | Štítek | Popis |
    |:--- |:--- |
    | 1 |Primární zařízení |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Řadič 0 |
    | 5 |Řadič 1 |
    | 6 |Kontroleru EBOD 0 |
    | 7 |Kontroleru EBOD 1 |
    | 8 |Skříň EBOD |
    | 9 |Jednotky PDU |

