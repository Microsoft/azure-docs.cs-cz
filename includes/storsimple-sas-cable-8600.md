---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888635"
---
#### <a name="to-attach-the-sas-cables"></a>Připojení SAS kabely
1. Určete primární a EBOD skříně. Dvě skříně lze identifikovat podle jejich příslušných back rovinami. Viz následující obrázek pro doprovodné materiály. 
   
    ![Rovina primární a skříně EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Zpět na zobrazení primárním serverem a skříně EBOD**
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Primární zařízení |
   | 2 |Skříň EBOD |
2. Vyhledejte sériová čísla na primární a EBOD skříně. Na štítku sériové číslo je opatřit back mazat každou skříň. Sériová čísla musí být stejná na obou skříně. [Obraťte se na Microsoft Support](../articles/storsimple/storsimple-contact-microsoft-support.md) okamžitě, pokud sériová čísla se neshodují. Viz následující obrázek vyhledejte sériová čísla.
   
    ![Zadní zobrazení zobrazující sériové číslo zařízení](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Umístění štítku sériové číslo**
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Vymazat přílohu |
3. Zadaná SAS kabely použijte pro připojení skříň EBOD na primární zařízení následujícím způsobem:
   
   1. Identifikujte čtyři SAS portů v primární skříň a EBOD skříň. Portů SAS proti jsou označeny jako EBOD na primární zařízení a odpovídají A portů na skříni EBOD, jak je znázorněno v SAS kabelů obrázku níže.
   2. Zadaná SAS kabely slouží k připojení k portu A. EBOD
   3. Port EBOD na řadiči 0, musí být připojené k portu A na kontroleru EBOD 0. Port EBOD na řadiči 1 musí být připojené k portu A na kontroleru EBOD 1. Viz následující obrázek pro doprovodné materiály. 
      
      ![SAS kabelů pro vaše zařízení](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS kabely**
      
      | Štítek | Popis |
      |:--- |:--- |
      | A |Primární zařízení |
      | B |Skříň EBOD |
      | 1 |Řadič 0 |
      | 2 |Řadič 1 |
      | 3 |Kontroleru EBOD 0 |
      | 4 |Kontroleru EBOD 1 |
      | 5, 6 |Portů SAS proti na primární skříň (EBOD označené) |
      | 7, 8 |SAS portů na skříni EBOD (Port A) |

