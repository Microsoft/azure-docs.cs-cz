---
title: Použití nástroje Emulator Express ke spouštění a ladění cloudové služby Azure v místním počítači | Dokumentace Microsoftu
description: Použití nástroje Emulator Express ke spouštění a ladění cloudové služby na místním počítači
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
ms.openlocfilehash: 3432e8778b2c5b5fd4db53a82ca23b55d75bcac5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969535"
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Použití nástroje Emulator Express ke spouštění a ladění cloudové služby Azure v místním počítači
Podle použití nástroje Emulator Express, může testování a ladění cloudovou službu bez spuštění sady Visual Studio jako správce. Můžete nastavit nastavení projektu pro použití Emulator Express nebo úplný emulátor, v závislosti na požadavcích vaší cloudové služby. Další informace o úplný emulátor, naleznete v tématu [spuštění aplikace Azure v emulátoru Compute](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Pomocí expresní emulátor v sadě Visual Studio
Při vytvoření projektu Azure v Azure SDK 2.3 nebo novější Emulator Express je automaticky používá. Pro existující projekty, které byly vytvořeny ve starší verzi sady Azure SDK pomocí následujících kroků vyberte Emulator Express:

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **vlastnosti**.

1. Na stránkách vlastností projektů, vyberte **webové** kartu.

    ![Vlastnosti projektu cloudové služby Azure](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. V části **místní vývojový Server**vyberte **možnosti použít službu IIS Express**.

1. V části **emulátor**vyberte **použít expresní emulátor**.
   
1. Pokud chcete spustit expresní emulátor, spusťte následující příkaz z příkazového řádku: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Emulator Express omezení
Omezení nástroje Emulator Express jsou známy následující problémy: 

- Emulator Express je nekompatibilní s Webový Server služby IIS.
- Cloudové služby může obsahovat více rolí, ale je omezený na jednu instanci každé role.
- Čísla portů pod 1 000, nelze přistupovat. Pokud používáte zprostředkovatele ověřování, které obvykle využívá port pod 1 000, potřebujete změnit tuto hodnotu na číslo portu, který je uveden výše 1 000.
- Žádná omezení, které se vztahují k emulátoru Azure Compute platí také pro Emulator Express. Například nemůžete mít více než 50 instancí role jedno nasazení. Další informace o emulátoru Azure Compute, naleznete v tématu [spuštění aplikace Azure v emulátoru Compute](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Další postup
[Ladění cloudové služby Azure](https://msdn.microsoft.com/library/azure/ee405479.aspx)
