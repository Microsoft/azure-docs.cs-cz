---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553205"
---
1. Přihlaste se k zařízení Data Box. Ujistěte se, že je odemčený.

    ![Snímek obrazovky se zobrazeným řídicím panelem zařízení se zobrazuje jako odemknutý.](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Přejít na **nastavení síťových rozhraní**. Poznamenejte si IP adresu zařízení pro síťové rozhraní používané pro připojení ke klientovi.

    ![Snímek obrazovky zobrazuje nastavení sítě, kde vidíte adresu I P.](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Přejděte na **připojit a zkopírujte** a klikněte na **REST**.

    ![Snímek obrazovky se zobrazí v podokně připojit a kopírovat, kde můžete vybrat položku REST jako nastavení přístupu.](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. V dialogu pro **přístup k účtu úložiště a nahrávání dat** zkopírujte **koncový bod služby BLOB Service**.

    ![Snímek obrazovky se zobrazí v dialogovém okně přístup k účtu úložiště a nahrání dat, kde můžete zkopírovat koncový bod služby BLOB Service.](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Spusťte **Poznámkový blok** jako správce a pak otevřete soubor **hosts** v umístění `C:\Windows\System32\Drivers\etc` .
6. Do souboru **hostitelů** přidejte následující položku: `<device IP address> <Blob service endpoint>`
7. Pro referenci použijte následující obrázek. Uložte soubor **hostitelů** .

    ![Snímek obrazovky se zobrazí v programu Poznámkový blok s adresou I P, kterou přidal koncový bod služby BLOB Service.](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
