---
title: Ke spuštění a ladění Azure cloud service v místním počítači pomocí emulátoru Express | Microsoft Docs
description: Pomocí emulátoru Express ke spouštění a ladění cloudové služby v místním počítači
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: mikejo
ms.openlocfilehash: 638aa005321963ce8bdcbb7df4317fc4bdec74ae
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292196"
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Pomocí emulátoru Express ke spouštění a ladění Azure cloud service v místním počítači
Pomocí emulátoru Express, můžete si otestovat a ladění cloudové služby bez spuštění sady Visual Studio jako správce. Můžete nastavit nastavení projektu k použití emulátoru Express nebo úplný emulátor, v závislosti na požadavcích vaší cloudové služby. Další informace o úplný emulátor najdete v tématu [spustit aplikaci Azure v emulátoru výpočetní](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Pomocí expresní emulátor služby v sadě Visual Studio
Při vytváření projektu Azure v Azure SDK 2.3 nebo novější, expresní emulátor automaticky použita. Pro existující projekty, které byly vytvořeny z předchozích verzí sady Azure SDK pomocí následujících kroků vyberte emulátoru Express:

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **vlastnosti**.

1. Na stránkách vlastností projektů, vyberte **webové** kartě.

    ![Vlastnosti projektu Azure cloud service](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. V části **místní vývojový Server**, vyberte **možnost použít službu IIS Express**.

1. V části **emulátoru**, vyberte **použít expresní emulátor**.
   
1. Spusťte emulátor Express, spusťte na příkazovém řádku následující příkaz: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Omezení expresní emulátor
Omezení expresní emulátor jsou známy následující problémy: 

- Expresní emulátor není kompatibilní s webového serveru IIS.
- Cloudové služby může obsahovat víc rolí, ale je omezený na jednu instanci každé role.
- Nelze získat přístup čísla portů pod 1 000. Pokud používáte zprostředkovatele ověřování, který obvykle využívá port pod 1 000, může musíte tuto hodnotu změňte číslo portu, která je vyšší než 1 000.
- Jakákoli omezení, která se týkají výpočetní emulátor Azure se rovněž vztahují na emulátoru Express. Například nemůže mít více než 50 instancí role na nasazení. Další informace o výpočetní emulátor Azure najdete v tématu [spustit aplikaci Azure v emulátoru výpočetní](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Další postup
[Ladění cloudové služby Azure](https://msdn.microsoft.com/library/azure/ee405479.aspx)
