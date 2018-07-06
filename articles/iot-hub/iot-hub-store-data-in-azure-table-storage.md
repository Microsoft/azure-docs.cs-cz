---
title: Uložení zpráv IoT hub do úložiště dat Azure | Dokumentace Microsoftu
description: Uložení zpráv IoT hub do úložiště objektů blob v Azure pomocí směrování zpráv služby IoT Hub. IoT hub zprávy obsahují informace, například data ze senzorů, ke kterým se ze zařízení IoT odesílají.
author: rangv
manager: ''
keywords: úložiště dat IOT, úložiště dat ze senzorů iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 84355453a5cb8d8f42abdcbde5432651c9c035b0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856286"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Uložení zpráv IoT hub, které obsahují data ze senzorů do úložiště objektů blob v Azure

![Diagram začátku do konce](./media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte, jak vytvořit účet úložiště Azure a aplikaci Azure function app k uložení zpráv IoT hub ve službě Azure Blob storage.

## <a name="what-you-do"></a>Co můžete dělat

- Vytvoření účtu úložiště Azure
- Nastavení služby IoT hub pro směrování zpráv do služby storage.

## <a name="what-you-need"></a>Co potřebujete

- [Nastavit vaše zařízení](iot-hub-raspberry-pi-kit-node-get-started.md) pro následující požadavky:
  - Aktivní předplatné Azure
  - Služby IoT hub v rámci vašeho předplatného 
  - Běžící aplikaci, která odesílá zprávy do služby IoT hub

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

1. V [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **vytvořit prostředek** > **úložiště** > **účtu úložiště**.

2. Zadejte informace potřebné pro účet úložiště:

   ![Vytvoření účtu úložiště na webu Azure Portal](./media/iot-hub-store-data-in-azure-table-storage/1_azure-portal-create-storage-account.png)

   * **Název**: název účtu úložiště. Název musí být globálně jedinečný.

   * **Druh účtu**: Zvolte `Storage (general purpose v1)`.

   * **Umístění**: Zvolte stejné umístění, která používá službu IoT hub.

   * **Replikace**: Zvolte `Locally-redundant storage (LRS)`.

   * **Výkon**: Zvolte `Standard`.

   * **Vyžádání bezpečného přenosu**: Zvolte `Disabled`.

   * **Předplatné**: Vyberte své předplatné Azure.

   * **Skupina prostředků**: použijte stejnou skupinu prostředků, která používá službu IoT hub.

   * **Připnout na řídicí panel:** Vyberte tuto možnost pro snadný přístup k centru IoT z řídicího panelu.

3. Klikněte na možnost **Vytvořit**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Příprava služby IoT hub pro směrování zpráv do služby storage

Směrování zpráv do úložiště Azure IoT Hub nativně podporuje jako objekty BLOB. Další informace o vlastních koncových bodech služby Azure IoT Hub, můžete se podívat do [seznamu předdefinovaných koncových bodů služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints).

### <a name="add-storage-as-a-custom-endpoint"></a>Přidání úložiště jako vlastní koncový bod

1. Přejděte do služby IoT hub na webu Azure Portal. 

2. Klikněte na tlačítko **koncové body** > **přidat**. 

3. Název koncového bodu a vyberte **kontejner úložiště Azure** jako typ koncového bodu. 

4. Pomocí nástroje pro výběr vybrat účet úložiště, který jste vytvořili v předchozí části. Vytvoření kontejneru úložiště a vyberte ji a potom klikněte na tlačítko **OK**.

   ![Vytvoření vlastního koncového bodu ve službě IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Přidání trasy pro směrování dat do úložiště

1. Klikněte na tlačítko **trasy** > **přidat** a zadejte název pro tuto trasu. 

2. Vyberte **zprávy typu zařízení** jako zdroj dat a vyberte koncový bod úložiště, které jste právě vytvořili jako koncový bod v této trase. 

3. Zadejte `true` jako řetězec dotazu a pak klikněte na tlačítko **Uložit**.

   ![Vytvořit trasu ve službě IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Přidat trasu pro kritická cesta telemetrie (volitelné)

Ve výchozím nastavení služby IoT Hub směrovat všechny zprávy, které neodpovídají žádné jiné trasy na integrovaný koncový bod. Od všech telemetrických zpráv nyní shodovat s pravidlem, které se směruje zprávy do služby storage, budete muset přidat další trasy pro zprávy k zápisu do integrovaného koncového bodu. Neplatí žádné další poplatky pro směrování zpráv do více koncových bodů.

> [!NOTE]
> Pokud se nejedná další zpracování telemetrických zpráv, můžete tento krok přeskočit.

1. Klikněte na tlačítko **přidat** trasy podokně a zadejte název pro tuto trasu. 

2. Vyberte **zprávy typu zařízení** jako zdroj dat a **události** jako koncový bod. 

3. Zadejte `true` jako řetězec dotazu a pak klikněte na tlačítko **Uložit**.

  ![Vytvořit trasu horká cesta ve službě IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Ověření zprávy ve vašem kontejneru úložiště

1. Spuštění ukázkové aplikace na zařízení k odeslání zprávy do služby IoT hub.

2. [Stažení a instalace Průzkumníka služby Azure Storage](http://storageexplorer.com/).

3. Otevřete Průzkumníka služby Storage, klikněte na tlačítko **přidat účet Azure** > **přihlášení**a pak se přihlaste ke svému účtu Azure.

4. Klikněte na vaše předplatné Azure > **účty úložiště** > váš účet úložiště > **kontejnery objektů Blob** > vašeho kontejneru.

   Měli byste vidět zprávy odeslané ze zařízení do služby IoT hub zaznamenána v kontejneru objektů blob.

## <a name="clean-up-resources"></a>Vyčištění prostředků 

V tomto kurzu přidat účet úložiště a pak přidá směrování pro zprávy ze služby IoT Hub má být zapsán do účtu úložiště. Pokud chcete vyčistit prostředky, které jste vytvořili, odeberte informace o směrování a potom k odstranění účtu úložiště. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Klikněte na tlačítko **skupiny prostředků** a vyberte skupinu prostředků, který jste použili. Zobrazí se seznam prostředků ve skupině. 

   > [!NOTE]
   > Pokud chcete odebrat všechny prostředky ve skupině prostředků, klikněte na tlačítko **odstranit** odstranit skupinu prostředků, postupujte podle pokynů. Tato akce odebere všechno, co v této skupině prostředků, takže dokončení vyčištění prostředků a můžete přeskočit k další části.

3. Klikněte na centrum IoT, které jste použili pro účely tohoto kurzu. 

4. V podokně centra IoT klikněte na tlačítko **trasy**. Klikněte na zaškrtávací políčko vedle pravidlo směrování přidat a potom klikněte na **odstranit**. Když se zobrazí výzva, pokud jste Opravdu chcete odstranit tuto cestu, klikněte na tlačítko **Ano**.

   ![Odebrat pravidlo směrování](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-routing.png)

   Zavřete podokno směrování. Budete přesměrováni zpět do podokna skupinu prostředků.

5. Klikněte znovu na služby IoT hub. 

6. V podokně centra IoT klikněte na tlačítko **koncové body**. Klikněte na zaškrtávací políčko vedle koncový bod, který jste přidali pro kontejner úložiště a pak klikněte na **odstranit**. Když se zobrazí výzva, pokud jste Opravdu chcete odstranit vybraný koncový bod, klikněte na tlačítko **Ano**.

    ![Odebrání koncového bodu](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-endpoint.png)

    Zavřete podokno koncových bodů. Budete přesměrováni zpět do podokna skupinu prostředků. 

7.  Klikněte na účet úložiště, kterou vytvoříte pro účely tohoto kurzu. 

8.  V podokně se účet úložiště, klikněte na tlačítko **odstranit** odebrat účet úložiště. Budete přesměrováni **odstranit účet úložiště** podokně.

   ![Odebrat účet úložiště](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-storageaccount.png)

8.  Zadejte název účtu úložiště a pak klikněte na **odstranit** v dolní části podokna. 

## <a name="next-steps"></a>Další postup

Úspěšně jste vytvořili účet úložiště Azure a směrování zpráv ze služby IoT Hub na kontejner objektů blob v tomto účtu úložiště.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
