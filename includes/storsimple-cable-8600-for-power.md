---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175043"
---
#### <a name="to-cable-your-device-for-power"></a>Postup zapojení zařízení do elektrického proudu
> [!NOTE]
> Mezi skříňky na zařízení StorSimple patří redundantní PCMs. Pro každou skříň je nutné nainstalovat PCMs a připojit se k různým zdrojům napájení, abyste zajistili vysokou dostupnost.
> 
> 

1. Ujistěte se, že přepínače napájení na všech PCMs jsou mimo danou pozici.
2. V primární skříni Připojte napájecí šňůry k obou PCMs. Napájecí šňůry jsou v diagramu napájecích kabelů označeny červeně.
3. Ujistěte se, že dvě PCMs v primární skříni používají samostatné zdroje napájení.
4. Připojte napájecí šňůry k napájení v jednotkách distribuce stojanu, jak je znázorněno v diagramu napájecích kabelů.
5. Opakujte kroky 2 až 4 pro EBOD skříň.
6. Zapněte EBOD skříň tím, že překlopete vypínač na každé PCM na pozici.
7. Ověřte, že je skříň EBOD zapnutá, pomocí kontroly, jestli jsou zapnuté zelené indikátory LED na zadní straně kontroleru EBOD.
8. Zapněte primární skříň překlopením jednotlivých přepínačů PCM na pozici.
9. Ověřte, že je systém zapnutý, a to tak, že se zapnou diody řadiče zařízení.
10. Ujistěte se, že připojení mezi řadičem EBOD a řadičem zařízení je aktivní, tak, že ověříte, že čtyři diody LED vedle portu SAS na řadiči EBOD jsou zelené.
    
    > [!IMPORTANT]
    > Abychom zajistili vysokou dostupnost vašeho systému, doporučujeme, abyste přesně dodržovali schéma kabelů napájení, které vidíte v následujícím diagramu.
    > 
    > 
    
    ![Kabel zařízení 4U pro napájení](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Kabely napájení**
    
    | Popisek | Popis |
    |:--- |:--- |
    | 1 |Primární skříň |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Kontroler 0 |
    | 5 |Kontroler 1 |
    | 6 |EBOD Controller 0 |
    | 7 |EBOD řadič 1 |
    | 8 |EBOD skříň |
    | 9 |Jednotek |

