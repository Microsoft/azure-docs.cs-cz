---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553076"
---
Pomocí těchto kroků se připojte k účtu úložiště a ověřte připojení.

1. V Průzkumník služby Storage otevřete dialog **připojit k Azure Storage** . V dialogovém okně **připojit k Azure Storage** vyberte **použít název a klíč účtu úložiště**.

    ![Snímek obrazovky se zobrazí v dialogovém okně připojit k Azure Storage s použitím názvu a klíče účtu úložiště.](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Vložte **název účtu** a **klíč účtu** (hodnota klíče 1 na stránce **připojit a kopírovat** ) v místním webovém uživatelském rozhraní. Vyberte možnost Doména koncových bodů úložiště jako **jinou (níže zadejte)** a pak zadejte koncový bod služby BLOB Service, jak je znázorněno níže. Možnost **použít protokol HTTP** se dá zaškrtnout jenom v případě, že se převádí přes *http*. Pokud používáte *protokol HTTPS*, ponechte možnost nezaškrtnutou. Vyberte **Další**.

    ![Snímek obrazovky se zobrazí dialogové okno připojit s názvem a klíčem s zadanými hodnotami.](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. V dialogovém okně **Souhrn připojení** zkontrolujte zadané informace. Vyberte **Connect** (Připojit).

    ![Snímek obrazovky s vybraným připojením zobrazí dialogové okno Souhrn připojení.](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Účet, který jste úspěšně přidali, se zobrazí v levém podokně Průzkumník služby Storage s (externí, jiný) připojeným k názvu. Kliknutím na **kontejnery objektů BLOB** Zobrazte kontejner.

    ![Snímek obrazovky s vybranými kontejnery objektů BLOB zobrazuje nabídku Průzkumníka.](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
