---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 0a9aaa86d44e71e429f2bfff13a56ddcb1ee2071
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550525"
---
1. Přihlaste se do zařízení Data Box. Ujistěte se, že je odemknuté.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Přejděte na **nastavit síťová rozhraní**. Poznamenejte si IP adresu pro síťové rozhraní použité pro připojení ke klientovi zařízení.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Přejděte na **připojit a Kopírovat** a klikněte na tlačítko **Rest (Preview)**.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Z **úložiště s přístupem pro účet a nahrát data** dialogové okno, kopie **koncový bod služby Blob Service**.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Spustit **Poznámkový blok** jako správce a potom otevřete **hostitele** se nachází v `C:\Windows\System32\Drivers\etc`.
6. Přidejte následující položku pro váš **hostitele** souboru: `<device IP address> <Blob service endpoint>`
7. Pro referenci použijte na následujícím obrázku. Uložit **hostitele** souboru.

    ![Data dodaný řídicí panel](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
