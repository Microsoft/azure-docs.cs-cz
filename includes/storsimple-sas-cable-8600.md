---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175041"
---
#### <a name="to-attach-the-sas-cables"></a>Připojení kabelů SAS
1. Identifikujte primární a EBOD skříně. Tyto dva skříně lze identifikovat při pohledu na jejich zadní roviny. Pokyny naleznete na následujícím obrázku. 
   
    ![Zadní rovina primárních skříní a skříní EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Pohled na primární a EBOD skříně**
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Primární skříň |
   | 2 |Skříň EBOD |
2. Vyhledejte sériová čísla na primární a EBOD skříně. Nálepka s pořadovým číslem je připevněna k zadnímu uchu každého krytu. Sériová čísla musí být v obou skříních shodná. Pokud se sériová čísla neshodují, obraťte se okamžitě na [podporu společnosti Microsoft.](../articles/storsimple/storsimple-contact-microsoft-support.md) Sériová čísla naleznete na následujícím obrázku.
   
    ![Pohled zezadu na skříň s pořadovým číslem](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Umístění štítku s pořadovým číslem**
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Ucho krytu |
3. Pomocí dodaných kabelů SAS připojte skříň EBOD k primární skříni následujícím způsobem:
   
   1. Identifikujte čtyři porty SAS v primární skříni a skříni EBOD. Porty SAS jsou označeny jako EBOD v primární skříni a odpovídají portu A ve skříni EBOD, jak je znázorněno na obrázku kabeláže SAS níže.
   2. Pomocí dodaných kabelů SAS připojte port EBOD k portu A.
   3. Port EBOD na řadiči 0 by měl být připojen k portu A na řadiči EBOD 0. Port EBOD na řadiči 1 by měl být připojen k portu A na řadiči EBOD 1. Pokyny naleznete na následujícím obrázku. 
      
      ![Kabeláž SAS pro vaše zařízení](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **Kabeláž SAS**
      
      | Popisek | Popis |
      |:--- |:--- |
      | A |Primární skříň |
      | B |Skříň EBOD |
      | 1 |Kontroler 0 |
      | 2 |Kontroler 1 |
      | 3 |Řadič EBOD 0 |
      | 4 |Řadič EBOD 1 |
      | 5, 6 |Porty SAS na primární skříni (s označením EBOD) |
      | 7, 8 |Porty SAS ve skříni EBOD (port A) |

