---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508305"
---
1. Přihlaste se do zařízení Data Box. Ujistěte se, že je odemknuté.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Přejděte na **nastavit síťová rozhraní**. Poznamenejte si IP adresu pro síťové rozhraní použité pro připojení ke klientovi zařízení.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Přejděte na **připojit a Kopírovat** a klikněte na tlačítko **Rest**.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Z **úložiště s přístupem pro účet a nahrát data** dialogové okno, kopie **koncový bod služby Blob Service**.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Spustit **Poznámkový blok** jako správce a potom otevřete **hostitele** se nachází v `C:\Windows\System32\Drivers\etc`.
6. Přidejte následující položku pro váš **hostitele** souboru: `<device IP address> <Blob service endpoint>`
7. Pro referenci použijte na následujícím obrázku. Uložit **hostitele** souboru.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
