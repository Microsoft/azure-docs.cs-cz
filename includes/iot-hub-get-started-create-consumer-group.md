---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66248894"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidání skupiny spotřebitelů do centra IoT hub

[Skupiny spotřebitelů](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) poskytují do datového proudu událostí nezávislá zobrazení, která umožňují aplikacím a službám Azure nezávisle využívat data ze stejného koncového bodu centra událostí. V této části přidáte skupinu spotřebitelů do integrovaného koncového bodu centra IoT hub, který se používá dále v tomto kurzu k vyžádat data z koncového bodu.

Pokud chcete do centra IoT hub přidat skupinu spotřebitelů, postupujte takto:

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT.

2. V levém podokně vyberte **předdefinované koncové body**, v pravém podokně vyberte **Události** a zadejte název v části **Skupiny příjemce**. Vyberte **Uložit**.

   ![Vytvoření skupiny spotřebitelů ve vašem centru IoT hub](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
