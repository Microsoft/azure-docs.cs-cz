---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "66248894"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidání skupiny příjemců do služby IoT Hub

[Skupiny příjemců](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) poskytují nezávislá zobrazení do datového proudu událostí, který aplikacím a službám Azure umožňuje nezávisle spotřebovat data ze stejného koncového bodu centra událostí. V této části přidáte skupinu příjemců do integrovaného koncového bodu služby IoT Hub, který se použije později v tomto kurzu k vyžádání dat z koncového bodu.

Pokud chcete přidat skupinu příjemců do služby IoT Hub, postupujte takto:

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT.

2. V levém podokně vyberte **Předdefinované koncové body**, v pravém podokně vyberte **události** a zadejte název do pole **skupiny příjemců**. Vyberte **Uložit**.

   ![Vytvoření skupiny příjemců ve službě IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
