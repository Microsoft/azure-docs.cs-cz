---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175043"
---
#### <a name="to-cable-your-device-for-power"></a>Kabel zařízení pro napájení
> [!NOTE]
> Obě skříně na vašem zařízení StorSimple obsahují redundantní PCM. Pro každou skříň musí být PCM instalovány a připojeny k různým zdrojům napájení, aby byla zajištěna vysoká dostupnost.
> 
> 

1. Ujistěte se, že vypínače na všech PCM jsou v poloze OFF.
2. V primární mašitě připojte napájecí kabely k oběma PCM. Napájecí kabely jsou označeny červeně v schématu elektrické kabeláže níže.
3. Ujistěte se, že dva PCM v primární skříni používají samostatné zdroje napájení.
4. Připojte napájecí kabely k napájení rozvaděčů, jak je znázorněno na schématu napájecí kabely.
5. Opakujte kroky 2 až 4 pro skříň EBOD.
6. Zapněte skříň EBOD přepnutím vypínače na každém PCM do polohy ON.
7. Zkontrolujte, zda je skříň EBOD zapnuta, a zkontrolujte, zda jsou zapnuty zelené LED diody na zadní straně regulátoru EBOD.
8. Zapněte primární skříň přepnutím každého přepínače PCM do polohy ON.
9. Ověřte, zda je systém zapnutý, a to tak, že se zapnou led diody řadiče zařízení.
10. Ujistěte se, že spojení mezi řadičem EBOD a řadičem zařízení je aktivní ověřením, že čtyři LED diody vedle portu SAS na řadiči EBOD jsou zelené.
    
    > [!IMPORTANT]
    > Chcete-li zajistit vysokou dostupnost systému, doporučujeme přísně dodržovat schéma napájecí kabeláže uvedené v následujícím diagramu.
    > 
    > 
    
    ![Kabel zařízení 4U pro napájení](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Elektrická kabeláž**
    
    | Popisek | Popis |
    |:--- |:--- |
    | 1 |Primární skříň |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Kontroler 0 |
    | 5 |Kontroler 1 |
    | 6 |Řadič EBOD 0 |
    | 7 |Řadič EBOD 1 |
    | 8 |Skříň EBOD |
    | 9 |PDU |

