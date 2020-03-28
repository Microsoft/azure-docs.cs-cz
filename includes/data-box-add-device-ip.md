---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67175672"
---
1. Přihlaste se k zařízení Datové schránky. Ujistěte se, že je odemčen.

    ![Řídicí panel datové schránky](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Přejděte na **Nastavení síťových rozhraní**. Poznamenejte si ip adresu zařízení pro síťové rozhraní používané pro připojení ke klientovi.

    ![Řídicí panel datové schránky](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Přejděte na **Připojit a zkopírujte** a klepněte na **tlačítko Rest**.

    ![Řídicí panel datové schránky](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Z **účtu Access Storage a nahrát data** dialogového okna zkopírujte **koncový bod služby Objektů blob**.

    ![Řídicí panel datové schránky](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Spusťte **program Poznámkový blok** jako správce `C:\Windows\System32\Drivers\etc`a otevřete soubor **hosts** umístěný na adrese .
6. Do souboru **hosts** přidejte následující položku:`<device IP address> <Blob service endpoint>`
7. Pro referenci použijte následující obrázek. Uložte soubor **hosts.**

    ![Řídicí panel datové schránky](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
