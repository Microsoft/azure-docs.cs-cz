---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550542"
---
Postupujte podle těchto kroků se připojte k účtu úložiště a ověřit připojení.

1. V Průzkumníku služby Storage, otevřete **připojit ke službě Azure Storage** dialogového okna. V **připojit ke službě Azure Storage** dialogového okna, vyberte **použít název účtu úložiště a klíč**.

    ![Data dodaný řídicí panel](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Vložit vaše **název účtu** a **klíč účtu** (1 hodnotu z klíče **připojit a Kopírovat** stránky v místním webovém uživatelském rozhraní). Vyberte doména koncových bodů úložiště jako **jiné (zadejte níže)** a pak zadejte koncový bod služby blob service, jak je znázorněno níže. Zkontrolujte **použít protokol HTTP** možnost pouze v případě, že přenos přes *http*. Pokud používáte *https*, tuto možnost nechte možnost nezaškrtnutou. Vyberte **Další**.

    ![Data dodaný řídicí panel](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. V **souhrn připojení** dialogového okna, zkontrolujte zadané informace. Vyberte **Connect** (Připojit).

    ![Data dodaný řídicí panel](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Účet, který se úspěšně přidal se zobrazí v levém podokně Storage Exploreru s (externí, jiné) připojeným k názvu. Klikněte na tlačítko **kontejnery objektů Blob** zobrazíte kontejneru.

    ![Data dodaný řídicí panel](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
