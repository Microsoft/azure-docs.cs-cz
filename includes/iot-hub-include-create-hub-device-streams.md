---
title: zahrnout soubor (ve verzi Preview datových proudů zařízení)
description: zahrnout soubor (ve verzi Preview datových proudů zařízení)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a02c16a75bbdf4827ce6a6c5f50751caf9c5aab8
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "67445996"
---
Tato část popisuje, jak vytvořit centrum IoT pomocí [Azure Portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **vytvořit prostředek**a pak vyberte **Internet věcí**.

1. V seznamu vpravo vyberte **Centrum IoT**. Otevře se první stránka pro vytvoření centra IoT.

   ![Vytvoření služby IoT Hub v Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Vyplňte jednotlivá pole:

   a. V rozevíracím seznamu **předplatné** vyberte předplatné, které chcete použít pro Centrum IoT.

   b. V části **Skupina prostředků**proveďte jednu z následujících akcí: 
      * Pokud chcete vytvořit novou skupinu prostředků, vyberte **vytvořit novou** a zadejte název, který chcete použít. 
      * Pokud chcete použít existující skupinu prostředků, vyberte **použít existující** a potom v rozevíracím seznamu vyberte skupinu prostředků. 
      
        Další informace najdete v tématu [Správa skupin prostředků Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   c. V rozevíracím seznamu **oblast** vyberte oblast, ve které chcete umístit rozbočovač. Vyberte oblast, která podporuje IoT Hub streamy zařízení ve verzi Preview, buď **střed USA** , nebo **střed USA EUAP**.

   d. Do pole **IoT Hub název** zadejte název centra IoT. Název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Pokud chcete pokračovat v vytváření služby IoT Hub **, vyberte Další: Velikost a škálování**.

   ![Nastavení velikosti a škálování pro nové centrum IoT pomocí Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   V tomto podokně můžete přijmout výchozí nastavení a v dolní části vybrat možnost **zkontrolovat a vytvořit** . Vezměte v úvahu následující možnosti:

   * V rozevíracím seznamu **úroveň cen a úrovně škálování** vyberte jednu z úrovní Standard (**S1**, **S2**nebo **S3**) nebo **F1: Úroveň**Free. Tato volba může být také založená na velikosti vašeho loďstva a nestreamované úlohy, které očekáváte v centru (například zprávy telemetrie). Například úroveň Free je určena pro testování a vyhodnocení. Umožňuje připojení zařízení 500 ke službě IoT Hub a až 8 000 zpráv za den. Každé předplatné Azure může vytvořit jednu službu IoT Hub v bezplatné úrovni. 

   * Pro **počet IoT Hubch jednotek**: Tato volba závisí na nestreamované zátěži, kterou očekáváte v centru. Pro nyní můžete vybrat 1.

   Další informace o možnostech vrstev najdete v tématu [Volba správné úrovně služby IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

1. Pokud chcete zkontrolovat své volby, vyberte kartu **Revize + vytvořit** . Otevře se podokno podobné následujícímu:

   ![Informace o vytvoření nového centra IoT Hub](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Pokud chcete vytvořit nové centrum IoT, vyberte **vytvořit**. Tento proces trvá několik minut.
