---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 592131bf6cca4c4c3c827de23742e8d52bcb4d1c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55888529"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>Připojení, inicializace a formátování svazku
1. Spusťte iniciátor iSCSI od Microsoftu.
2. V okně **iSCSI Initiator Properties (Vlastnosti iniciátoru iSCSI)** na kartě **Zjišťování** klikněte na **Vyhledat portál**.
3. V dialogovém okně **Zjistit cílový portál** zadejte IP adresu svého síťového rozhraní s podporou iSCSI a potom klikněte na **OK**. 
4. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Cíle** najděte část **Zjištěné cíle**. U zařízení by se měl zobrazovat stav **Neaktivní**.
5. Vyberte cílové zařízení a potom klikněte na **Připojit**. Po připojení zařízení by se měl stav změnit na **Připojeno**. (Další informace o použití iniciátoru iSCSI od Microsoftu najdete v tématu [Instalace a konfigurace iniciátoru Microsoft iSCSI][1]).
6. Na hostiteli s Windows stiskněte klávesu s logem Windows + X a potom klikněte na **Spustit**. 
7. V dialogovém okně **Spustit** zadejte **Diskmgmt.msc**. Klikněte na **OK**. Zobrazí se dialogové okno **Správa disků**. V pravém podokně se zobrazí svazky na vašem hostiteli.
8. V okně **Správa disků** se připojené svazky zobrazí tak, jak znázorňuje následující obrázek. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.
   
     ![Inicializace a formátování svazku](./media/storsimple-mount-initialize-format-volume/HCS_InitializeFormatVolume-include.png) 
9. Znovu klikněte pravým tlačítkem myši na svazek (klikněte na název disku) a potom klikněte na **Inicializovat**.
10. Pokud chcete naformátovat jednoduchý svazek, postupujte takto:
    
    1. Vyberte svazek, klikněte na něj pravým tlačítkem myši (klikněte na pravou část) a klikněte na **Nový jednoduchý svazek**.
    2. V průvodci Nový jednoduchý svazek zadejte písmeno svazku a písmeno jednotky a nakonfigurujte u svazku systém souborů NTFS.
    3. Zvolte velikost alokační jednotky 64 kB. Tato velikost alokační jednotky dobře funguje s algoritmy pro odstranění duplicit, které používá řešení StorSimple.
    4. Proveďte rychlé formátování.

![Dostupné video](./media/storsimple-mount-initialize-format-volume/Video_icon.png)**Dostupné video**

Pokud si chcete přehrát video, které ukazuje, jak připojit, inicializovat a naformátovat svazek StorSimple, klikněte [sem](https://azure.microsoft.com/documentation/videos/mount-initialize-and-format-a-storsimple-volume/).

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx
