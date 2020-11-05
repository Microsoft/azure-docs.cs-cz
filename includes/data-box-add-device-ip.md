---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 6b716640bc8117ccc2d473b337ee3e555e8d9b0a
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375792"
---
1. Přihlaste se k zařízení Data Box. Ujistěte se, že je odemčený.

    ![Řídicí panel Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Přejít na **nastavení síťových rozhraní**. Poznamenejte si IP adresu zařízení pro síťové rozhraní používané pro připojení ke klientovi.

    ![Řídicí panel Data Box 2](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Přejděte na **připojit a zkopírujte** a klikněte na **REST**.

    ![Řídicí panel Data Box 3](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. V dialogu pro **přístup k účtu úložiště a nahrávání dat** zkopírujte **koncový bod služby BLOB Service**.

    ![Řídicí panel Data Box 4](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Spusťte **Poznámkový blok** jako správce a pak otevřete soubor **hosts** v umístění `C:\Windows\System32\Drivers\etc` .
6. Do souboru **hostitelů** přidejte následující položku: `<device IP address> <Blob service endpoint>`
7. Pro referenci použijte následující obrázek. Uložte soubor **hostitelů** .

    ![Řídicí panel Data Box 5](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
