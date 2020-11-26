---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/02/2019
ms.author: alkohli
ms.openlocfilehash: 203c977fe9109cd8b2b6de561e975e20aacf700e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185529"
---
Pokud Průzkumník služby Storage používáte poprvé, musíte provést následující kroky.

1. V horním panelu příkazů přejdete na **upravit > cílová Azure Stack rozhraní API**.

    ![Konfigurace Průzkumník služby Storage](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-1.png)

2. Restartujte Průzkumník služby Storage, aby se změny projevily.


Pomocí těchto kroků se připojte k účtu úložiště a ověřte připojení.

1. V Průzkumník služby Storage vyberte účty úložiště. Klikněte pravým tlačítkem a vyberte možnost **připojit k Azure Storage** . 

    ![Konfigurace Průzkumník služby Storage 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-2.png)

2. V dialogovém okně **připojit k Azure Storage** vyberte **použít název a klíč účtu úložiště**.

    ![Konfigurace Průzkumník služby Storage 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-3.png)

2. V dialogovém okně **připojit s názvem a klíčem** proveďte následující kroky:

    1. Zadejte zobrazovaný název účtu úložiště Edge. 
    2. Zadejte název účtu hraničního úložiště.
    3. Vložte přístupový klíč, který jste získali z místních rozhraní API pro zařízení prostřednictvím Azure Resource Manager.
    4. Vyberte doména úložiště jako **jiný (níže zadejte)** a pak zadejte příponu koncového bodu služby BLOB Service ve formátu: `<appliance name>.<DNSdomain>` . 
    5. Políčko při přenosu **použít možnost protokolu HTTP** je přes *protokol HTTP*. 
    6. Vyberte **Další**.

    ![Konfigurace Průzkumník služby Storage 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-4.png)    

3. V dialogovém okně **Souhrn připojení** zkontrolujte zadané informace. Vyberte **Connect** (Připojit).

    ![Konfigurace Průzkumník služby Storage 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-5.png)

4. Účet, který jste úspěšně přidali, se zobrazí v levém podokně Průzkumník služby Storage s (externí, jiný) připojeným k názvu. Vyberte **kontejnery objektů BLOB** pro zobrazení kontejneru.

    ![Zobrazit kontejnery objektů BLOB](media/azure-stack-edge-gateway-verify-connection-storage-explorer/connect-with-storage-explorer-6.png)

Dalším krokem k ověření je, že přenos dat skutečně funguje přes toto připojení správně.

Provedením následujících kroků načtete data do svého účtu úložiště Edge na zařízení a automaticky se navrství na namapovaný Azure Storage účet.

1. Vyberte kontejner, do kterého chcete načíst data v účtu úložiště Edge. Vyberte **nahrát** a pak vyberte **nahrát soubory**.

    ![Ověření přenosu dat](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-1.png)

2. V dialogovém okně **nahrát soubory** přejděte na a vyberte soubory, které chcete nahrát. Vyberte **Další**.

    ![Ověření přenosu dat 2](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-2.png)

3. Ověřte, že se soubory nahrály. Nahrané soubory se zobrazí v kontejneru.

    ![Ověření přenosu dat 3](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-3.png)

4. V dalším kroku se připojíte k účtu Azure Storage namapovanému na tento účet úložiště Edge. Všechna data, která se nahrají na hraniční účet úložiště, by se měla automaticky navrstvit na účet Azure Storage. 
    
    Připojovací řetězec pro účet Azure Storage získáte tak, že přejdete na **účet Azure Storage > přístupové klíče** a zkopírujete připojovací řetězec.

    ![Ověření přenosu dat 4](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-5.png)

    Použijte připojovací řetězec pro připojení k Azure Storagemu účtu.  

    ![Ověření přenosu dat 5](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-4.png)


5. V dialogovém okně **Souhrn připojení** zkontrolujte zadané informace. Vyberte **Connect** (Připojit).

    ![Ověření přenosu dat 6](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-6.png)

6. Uvidíte, že soubory, které jste nahráli v účtu úložiště Edge, se přenesly na účet Azure Storage.

    ![Ověření přenosu dat 7](media/azure-stack-edge-gateway-verify-connection-storage-explorer/verify-data-transfer-7.png)
