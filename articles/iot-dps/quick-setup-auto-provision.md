---
title: Set up IoT Hub Device Provisioning Service in the Azure portal
description: Quickstart - Set up the Azure IoT Hub Device Provisioning Service in the Azure portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b983a9591743b1fda79e23aedc1aca88add2a3e2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228527"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with the Azure portal

Tyto kroky ukazují, jak na portálu nastavit cloudové prostředky Azure pro zřizování zařízení. Tento článek obsahuje kroky pro vytvoření služby IoT Hub, vytvoření nové služby IoT Hub Device Provisioning a jejich propojení. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="create-an-iot-hub"></a>Vytvoření IoT Hubu

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Vytvoření nové instance služby IoT Hub Device Provisioning

1. Select the **+ Create a resource** button again.

2. *Search the Marketplace* for the **Device Provisioning Service**. Select **IoT Hub Device Provisioning Service** and hit the **Create** button. 

3. Provide the following information for your new Device Provisioning Service instance and hit **Create**.

    * **Název:** Zadejte jedinečný název vaší nové instance služby Device Provisioning. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.
    * **Předplatné:** Vyberte předplatné, které chcete použít při vytváření této instance služby Device Provisioning.
    * **Skupina prostředků:** Toto pole umožňuje vytvořit novou skupinu prostředků nebo zvolit existující, která má obsahovat novou instanci. Zvolte stejnou skupinu prostředků, kterou jste vytvořili výše a obsahuje IoT Hub, například **TestResources**. Když umístíte všechny související prostředky do jedné skupiny, můžete je spravovat společně. Například odstraněním skupiny prostředků se odstraní všechny prostředky, které tato skupina obsahuje. For more information, see [Manage Azure Resource Manager resource groups](../azure-resource-manager/manage-resource-groups-portal.md).
    * **Umístění:** Vyberte pro zařízení nejbližší umístění.

      ![Enter basic information about your Device Provisioning Service instance in the portal blade](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Select the notification button to monitor the creation of the resource instance. Once the service is successfully deployed, select **Pin to dashboard**, and then **Go to resource**.

    ![Monitorování oznámení nasazení](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Link the IoT hub and your Device Provisioning Service

In this section, you will add a configuration to the Device Provisioning Service instance. Tato konfigurace nastaví službu IoT Hub, pro kterou jsou zařízení zřízená.

1. Select the **All resources** button from the left-hand menu of the Azure portal. Vyberte instanci služby Device Provisioning, kterou jste vytvořili v předchozí části.  

2. From the Device Provisioning Service's menu, select **Linked IoT hubs**. Hit the **+ Add** button seen at the top. 

3. Na stránce **Přidat propojení na centrum IoT Hub** zadejte následující informace, abyste propojili vaši novou instanci služby Device Provisioning se službou IoT Hub. Then hit **Save**. 

    * **Předplatné:** Vyberte předplatné obsahující službu IoT Hub, kterou chcete propojit s vaší novou instancí služby Device Provisioning.
    * **IoT Hub:** Vyberte službu IoT Hub k propojení s vaší novou instancí služby Device Provisioning.
    * **Zásady přístupu:** Vyberte **iothubowner** jako přihlašovací údaje pro vytvoření propojení se službou IoT Hub.  

      ![Link the hub name to link to the Device Provisioning Service instance in the portal blade](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Vybrané centrum by se teď mělo zobrazit v okně **Propojená centra IoT**. You might need to hit **Refresh** for it to show up.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem na portálu Azure Portal.

1. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning Service. At the top of the device detail pane, select **Delete**.  
2. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. At the top of the hub detail pane, select **Delete**.  

## <a name="next-steps"></a>Další kroky

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)
