---
title: Zahrnout soubor (datové proudy zařízení ve verzi preview)
description: Zahrnout soubor (datové proudy zařízení ve verzi preview)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 93c71fa8b0c39cc16d2a8e24472e8d68717a6c32
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733256"
---
Tato část popisuje, jak vytvořit IoT hub pomocí [webu Azure portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **vytvořit prostředek**a pak vyberte **Internet of Things**.

1. V seznamu na pravé straně vyberte **služby Iot Hub**. Otevře se první stránka pro vytvoření služby IoT hub.

   ![Vytvoření služby IoT hub na webu Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Vyplňte jednotlivá pole:

   a. V **předplatné** rozevíracího seznamu vyberte předplatné, které chcete použít pro službu IoT hub.

   b. Pro **skupiny prostředků**, proveďte jednu z následujících akcí: 
      * Chcete-li vytvořit novou skupinu prostředků, vyberte **vytvořit nový** a zadejte název, který chcete použít. 
      * Chcete-li použít existující skupinu prostředků, vyberte **použít existující** a pak v rozevíracím seznamu vyberte skupinu prostředků. 
      
        Další informace najdete v tématu [skupiny prostředků spravovat Azure Resource Manageru](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. V **oblasti** rozevírací seznam, vyberte oblast, ve kterém chcete centrem umístit. Vyberte oblast, která podporuje Náhled datové proudy zařízení služby IoT Hub, buď **USA (střed)** nebo **střed USA – EUAP**.

   d. V **název centra IoT** zadejte název pro službu IoT hub. Název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Chcete-li pokračovat, vytvoření služby IoT hub, vyberte **Další: Dimenzování a škálování**.

   ![Nastavení velikosti a měřítka novou službu IoT hub pomocí webu Azure portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   V tomto podokně můžete přijmout výchozí nastavení a vyberte **revize + vytvořit** v dolní části. Zvažte následující možnosti:

   * V **úrovně cen a škálování** rozevíracího seznamu vyberte jednu z úrovně standard (**S1**, **S2**, nebo **S3**) nebo **F1: Úroveň Free**. Tato volba může také řídí velikost vašeho fleet a jiných než pro datový proud úlohy, které očekáváte, že ve vašem Centru (například telemetrických zpráv). Například na úrovni free je určená pro účely testování a vyhodnocení. Umožňuje 500 zařízení k připojení ke službě IoT hub a až 8 000 zpráv denně. Každé předplatné Azure můžete vytvořit v jednom centru IoT v bezplatné úrovni. 

   * Pro **jednotky centra IoT číslo**: Tato volba závisí na jiné datové proudy úlohy, které očekáváte, že ve vašem Centru. Teď můžete vybrat 1.

   Další informace o možnostech vrstvy, naleznete v tématu [výběru správné úrovně IoT hub](../articles/iot-hub/iot-hub-scaling.md).

1. Chcete-li zkontrolujte zvolené volby, vyberte **zkontrolujte + vytvořit** kartu. V podokně, které se otevře je podobný následujícímu:

   ![Informace o vytvoření nového centra IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Chcete-li vytvořit novou službu IoT hub, vyberte **vytvořit**. Tento proces trvá několik minut.
