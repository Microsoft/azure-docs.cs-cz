---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67175674"
---
Podle těchto kroků se připojte k účtu úložiště a ověřte připojení.

1. V Průzkumníku úložiště otevřete dialogové okno **Připojit k úložišti Azure.** V dialogovém okně **Připojit k úložišti Azure** vyberte Použít název a klíč účtu **úložiště**.

    ![Řídicí panel datové schránky](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Vložte **název účtu** a klíč **účtu** (hodnota klíče 1 ze stránky Připojit **a zkopírovat** v místním webovém uživatelském prostředí). Vyberte doménu koncových bodů úložiště jako **other (zadejte níže)** a pak zadejte koncový bod služby objektů blob, jak je znázorněno níže. Zaškrtněte **políčko Použít** možnost HTTP pouze v případě přenosu přes *http*. Pokud používáte *https*, ponechte možnost nezaškrtnutou. Vyberte **další**.

    ![Řídicí panel datové schránky](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. V dialogovém okně **Souhrn připojení** zkontrolujte poskytnuté informace. Vyberte **Connect** (Připojit).

    ![Řídicí panel datové schránky](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. Účet, který jste úspěšně přidali, se zobrazí v levém podokně Průzkumníka úložiště s názvem (Externí, Jiné). Kliknutím na **Kontejnery objektů blob** zobrazíte kontejner.

    ![Řídicí panel datové schránky](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
