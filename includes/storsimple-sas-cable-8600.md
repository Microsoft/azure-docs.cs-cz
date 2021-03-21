---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f08a6b3f7abfc79bff6baff2a339053905612535
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025591"
---
#### <a name="to-attach-the-sas-cables"></a>Připojení kabelů SAS
1. Identifikujte primární a EBOD skříně. Tyto dvě skříně se dají identifikovat tak, že si prohlížíte jejich příslušné zadní plochy. Pokyny najdete na následujícím obrázku. 
   
    ![Zadní rovina primárních a EBOD skříní](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Pohled zpět na primární a EBOD skříně**
   
   | Popisek | Description |
   |:--- |:--- |
   | 1 |Primární skříň |
   | 2 |EBOD skříň |
2. Vyhledejte sériová čísla na primárních a EBOD skříních. Nálepka sériovým číslem je připevněna k zadnímu ušnímu číslu každé skříně. Sériová čísla musí být v obou skříních stejná. Pokud se sériová čísla neshodují, [kontaktujte podpora Microsoftu](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) hned. Pokud chcete najít sériová čísla, přečtěte si následující obrázek.
   
    ![Zadní pohled na skříň zobrazující sériové číslo](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Umístění nálepky sériových čísel**
   
   | Popisek | Description |
   |:--- |:--- |
   | 1 |Ušní skříň |
3. Pomocí zadaných kabelů SAS připojte skříň EBOD k primární skříni následujícím způsobem:
   
   1. Identifikujte čtyři porty SAS v primární skříni a skříni EBOD. Porty SAS jsou označeny jako EBOD v primární skříni a odpovídají portu A ve skříni EBOD, jak je znázorněno na obrázku SAS na obrázku níže.
   2. Pomocí zadaných kabelů SAS připojte port EBOD k portu A.
   3. Port EBOD na kontroleru 0 by měl být připojen k portu A na řadiči EBOD 0. Port EBOD na řadiči 1 by měl být připojen k portu A na řadiči EBOD 1. Pokyny najdete na následujícím obrázku. 
      
      ![Kabeláž SAS pro vaše zařízení](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **Kabeláž SAS**
      
      | Popisek | Description |
      |:--- |:--- |
      | A |Primární skříň |
      | B |EBOD skříň |
      | 1 |Kontroler 0 |
      | 2 |Kontroler 1 |
      | 3 |EBOD Controller 0 |
      | 4 |EBOD řadič 1 |
      | 5, 6 |Porty SAS v primární skříni (označený EBOD) |
      | 7, 8 |Porty SAS na skříni EBOD (port A) |