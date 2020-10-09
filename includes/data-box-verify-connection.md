---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175674"
---
Pomocí těchto kroků se připojte k účtu úložiště a ověřte připojení.

1. V Průzkumník služby Storage otevřete dialog **připojit k Azure Storage** . V dialogovém okně **připojit k Azure Storage** vyberte **použít název a klíč účtu úložiště**.

    ![Řídicí panel Data Box](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Vložte **název účtu** a **klíč účtu** (hodnota klíče 1 na stránce **připojit a kopírovat** ) v místním webovém uživatelském rozhraní. Vyberte možnost Doména koncových bodů úložiště jako **jinou (níže zadejte)** a pak zadejte koncový bod služby BLOB Service, jak je znázorněno níže. Možnost **použít protokol HTTP** se dá zaškrtnout jenom v případě, že se převádí přes *http*. Pokud používáte *protokol HTTPS*, ponechte možnost nezaškrtnutou. Vyberte **Další**.

    ![Řídicí panel Data Box](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. V dialogovém okně **Souhrn připojení** zkontrolujte zadané informace. Vyberte **Připojit**.

    ![Řídicí panel Data Box](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Účet, který jste úspěšně přidali, se zobrazí v levém podokně Průzkumník služby Storage s (externí, jiný) připojeným k názvu. Kliknutím na **kontejnery objektů BLOB** Zobrazte kontejner.

    ![Řídicí panel Data Box](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
