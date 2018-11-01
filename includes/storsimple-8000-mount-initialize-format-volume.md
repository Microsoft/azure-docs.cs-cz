---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 71b031469d8d1292358132df129c11b4297ec0ee
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164792"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-mount-initialize-and-format-a-volume"></a>Připojení, inicializace a formátování svazku
1. Spusťte iniciátor iSCSI od Microsoftu.
2. V okně **iSCSI Initiator Properties (Vlastnosti iniciátoru iSCSI)** na kartě **Zjišťování** klikněte na **Vyhledat portál**.
3. V dialogovém okně **Zjistit cílový portál** zadejte IP adresu svého síťového rozhraní s podporou iSCSI a potom klikněte na **OK**. 
4. V okně **iSCSI Initiator Properties** (Vlastnosti iniciátoru iSCSI) na kartě **Cíle** najděte část **Zjištěné cíle**. U zařízení by se měl zobrazovat stav **Neaktivní**.
5. Vyberte cílové zařízení a potom klikněte na **Připojit**. Po připojení zařízení by se měl stav změnit na **Připojeno**. (Další informace o použití iniciátoru iSCSI od Microsoftu najdete v tématu [Instalace a konfigurace iniciátoru Microsoft iSCSI][1]).
6. Na hostiteli s Windows stiskněte klávesu s logem Windows + X a potom klikněte na **Spustit**. 
7. V dialogovém okně **Spustit** zadejte **Diskmgmt.msc**. Klikněte na **OK**. Zobrazí se dialogové okno **Správa disků**. V pravém podokně se zobrazí svazky na vašem hostiteli.
8. V okně **Správa disků** se připojené svazky zobrazí tak, jak znázorňuje následující obrázek. Klikněte pravým tlačítkem myši na zjištěný svazek (klikněte na název disku) a potom klikněte na **Online**.
   
     ![Inicializace a formátování svazku](./media/storsimple-8000-mount-initialize-format-volume/step7initializeformatvolume.png) 
9. Znovu klikněte pravým tlačítkem myši na svazek (klikněte na název disku) a potom klikněte na **Inicializovat**.
10. Pokud chcete naformátovat jednoduchý svazek, postupujte takto:
    
    1. Vyberte svazek, klikněte na něj pravým tlačítkem myši (klikněte na pravou část) a klikněte na **Nový jednoduchý svazek**.
    2. V průvodci Nový jednoduchý svazek zadejte písmeno svazku a písmeno jednotky a nakonfigurujte u svazku systém souborů NTFS.
    3. Zvolte velikost alokační jednotky 64 kB. Tato velikost alokační jednotky dobře funguje s algoritmy pro odstranění duplicit, které používá řešení StorSimple.
    4. Proveďte rychlé formátování.

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx
