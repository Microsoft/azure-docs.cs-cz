---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c10482029e6cfce7063d205161fed54030919c48
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165423"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Zastavení a spuštění cloudového zařízení

1. Pokud chcete zastavit cloudové zařízení, přejděte do virtuálního počítače pro cloudové zařízení.
    ![Virtuální počítač s řešením StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. Na panelu příkazů klikněte na **Zastavit**.

    ![Virtuální počítač s řešením StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

    ![Virtuální počítač s řešením StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Když zastavíte virtuální počítač, dojde k jeho uvolnění. Během zastavování cloudového zařízení je jeho stav **Uvolňování**. Po zastavení cloudového zařízení je jeho stav **Zastaveno (uvolněno)**.

    ![Virtuální počítač s řešením StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Po zastavení virtuálního počítače ho můžete spustit kliknutím na **Spustit** (tlačítko se stane dostupným). Po spuštění cloudového zařízení je jeho stav **Spuštěno**.

    ![Virtuální počítač s řešením StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

K zastavení a spuštění cloudového zařízení slouží následující rutiny.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Restartování cloudového zařízení

Pokud chcete restartovat cloudové zařízení, přejděte do virtuálního počítače pro cloudové zařízení. Na panelu příkazů klikněte na **Restartovat**. Po zobrazení výzvy potvrďte restartování. Když je cloudové zařízení připravené k použití, jeho stav je **Spuštěno**.

![Virtuální počítač s řešením StorSimple Cloud Appliance](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

K restartování cloudového zařízení slouží následující rutina.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

