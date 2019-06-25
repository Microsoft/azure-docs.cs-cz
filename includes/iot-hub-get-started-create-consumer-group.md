---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248894"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidat skupinu uživatelů do služby IoT hub

[Skupiny příjemců](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) poskytují nezávislé zobrazení streamu událostí, která umožňují aplikací a služeb Azure s nezávisle na sobě využívají data z stejný koncový bod centra událostí. V této části přidáte do integrovaný koncový bod služby IoT hub, který se používá později v tomto kurzu k získání dat z koncového bodu skupiny příjemců.

Chcete-li přidat skupinu příjemců do služby IoT hub, postupujte takto:

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT.

2. V levém podokně vyberte **integrovaných koncových bodech**vyberte **události** v pravém podokně a zadejte název v rámci **skupiny příjemců**. Vyberte **Uložit**.

   ![Vytvořit skupinu uživatelů ve službě IoT hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
