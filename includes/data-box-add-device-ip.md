---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "67175672"
---
1. Přihlaste se k zařízení Data Box. Ujistěte se, že je odemčený.

    ![Řídicí panel Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Přejít na **nastavení síťových rozhraní**. Poznamenejte si IP adresu zařízení pro síťové rozhraní používané pro připojení ke klientovi.

    ![Řídicí panel Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Přejděte na **připojit a zkopírujte** a klikněte na **REST**.

    ![Řídicí panel Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. V dialogu pro **přístup k účtu úložiště a nahrávání dat** zkopírujte **koncový bod služby BLOB Service**.

    ![Řídicí panel Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Spusťte **Poznámkový blok** jako správce a pak otevřete soubor **hosts** v `C:\Windows\System32\Drivers\etc`umístění.
6. Do souboru **hostitelů** přidejte následující položku:`<device IP address> <Blob service endpoint>`
7. Pro referenci použijte následující obrázek. Uložte soubor **hostitelů** .

    ![Řídicí panel Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
