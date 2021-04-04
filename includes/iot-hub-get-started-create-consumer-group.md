---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026795"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidání skupiny příjemců do služby IoT Hub

[Skupiny příjemců](../articles/event-hubs/event-hubs-features.md#event-consumers) poskytují nezávislá zobrazení do datového proudu událostí, který aplikacím a službám Azure umožňuje nezávisle spotřebovat data ze stejného koncového bodu centra událostí. V této části přidáte skupinu příjemců do integrovaného koncového bodu služby IoT Hub, který se použije později v tomto kurzu k vyžádání dat z koncového bodu.

Pokud chcete přidat skupinu příjemců do služby IoT Hub, postupujte takto:

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT.

2. V levém podokně vyberte **Předdefinované koncové body**, v pravém podokně vyberte **události** a zadejte název do pole **skupiny příjemců**. Vyberte **Uložit**.

   ![Vytvoření skupiny příjemců ve službě IoT Hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)