---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 3e4edc55834d322dede36e9f429f5af7a4a79846
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375800"
---
Pomocí těchto kroků se připojte k účtu úložiště a ověřte připojení.

1. V Průzkumník služby Storage otevřete dialog **připojit k Azure Storage** . V dialogovém okně **připojit k Azure Storage** vyberte **použít název a klíč účtu úložiště**.

    ![Řídicí panel Data Box](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Vložte **název účtu** a **klíč účtu** (hodnota klíče 1 na stránce **připojit a kopírovat** ) v místním webovém uživatelském rozhraní. Vyberte možnost Doména koncových bodů úložiště jako **jinou (níže zadejte)** a pak zadejte koncový bod služby BLOB Service, jak je znázorněno níže. Možnost **použít protokol HTTP** se dá zaškrtnout jenom v případě, že se převádí přes *http*. Pokud používáte *protokol HTTPS* , ponechte možnost nezaškrtnutou. Vyberte **Další**.

    ![Řídicí panel Data Box 2](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. V dialogovém okně **Souhrn připojení** zkontrolujte zadané informace. Vyberte **Connect** (Připojit).

    ![Řídicí panel Data Box 3](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Účet, který jste úspěšně přidali, se zobrazí v levém podokně Průzkumník služby Storage s (externí, jiný) připojeným k názvu. Kliknutím na **kontejnery objektů BLOB** Zobrazte kontejner.

    ![Řídicí panel Data Box 4](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
