---
title: 'Rychlý Start: nastavení zřizování zařízení v Azure Portal'
description: Rychlý Start Azure – nastavení služby Azure IoT Hub Device Provisioning Service v Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 498ec758fe8ad5d596a62d3541b39d860336e726
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151286"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Rychlý Start: nastavení IoT Hub Device Provisioning Service s využitím Azure Portal

Tyto kroky ukazují, jak na portálu nastavit cloudové prostředky Azure pro zřizování zařízení. Tento článek obsahuje kroky pro vytvoření služby IoT Hub, vytvoření nové služby IoT Hub Device Provisioning a jejich propojení. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Vytvoření nové instance služby IoT Hub Device Provisioning

1. Znovu vyberte tlačítko **+ vytvořit prostředek** .

2. Vyhledejte **službu Device Provisioning**na *webu Marketplace* . Vyberte **IoT Hub Device Provisioning Service** a stiskněte tlačítko **vytvořit** . 

3. Zadejte následující informace pro novou instanci služby Device Provisioning a stiskněte **vytvořit**.

    * **Název:** Zadejte jedinečný název vaší nové instance služby Device Provisioning. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.
    * **Předplatné:** Vyberte předplatné, které chcete použít při vytváření této instance služby Device Provisioning.
    * **Skupina prostředků:** Toto pole umožňuje vytvořit novou skupinu prostředků nebo zvolit existující, která má obsahovat novou instanci. Zvolte stejnou skupinu prostředků, kterou jste vytvořili výše a obsahuje IoT Hub, například **TestResources**. Když umístíte všechny související prostředky do jedné skupiny, můžete je spravovat společně. Například odstraněním skupiny prostředků se odstraní všechny prostředky, které tato skupina obsahuje. Další informace najdete v tématu [Správa skupin prostředků Azure Resource Manager](../azure-resource-manager/manage-resource-groups-portal.md).
    * **Umístění:** Vyberte pro zařízení nejbližší umístění.

      ![Zadejte základní informace o instanci služby Device Provisioning v okně portálu.](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Výběrem tlačítka oznámení můžete monitorovat vytvoření instance prostředku. Po úspěšném nasazení služby vyberte **Připnout na řídicí panel**a pak **Přejít k prostředku**.

    ![Monitorování oznámení nasazení](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Propojení centra IoT a služby Device Provisioning

V této části přidáte konfiguraci do instance služby Device Provisioning. Tato konfigurace nastaví službu IoT Hub, pro kterou jsou zařízení zřízená.

1. V nabídce na levé straně Azure Portal vyberte tlačítko **všechny prostředky** . Vyberte instanci služby Device Provisioning, kterou jste vytvořili v předchozí části.  

2. V nabídce služby Device Provisioning vyberte **propojená centra IoT**. Stiskněte tlačítko **+ Přidat** zobrazené v horní části. 

3. Na stránce **Přidat propojení na centrum IoT Hub** zadejte následující informace, abyste propojili vaši novou instanci služby Device Provisioning se službou IoT Hub. Pak stiskněte **Uložit**. 

    * **Předplatné:** Vyberte předplatné obsahující službu IoT Hub, kterou chcete propojit s vaší novou instancí služby Device Provisioning.
    * **IoT Hub:** Vyberte službu IoT Hub k propojení s vaší novou instancí služby Device Provisioning.
    * **Zásady přístupu:** Vyberte **iothubowner** jako přihlašovací údaje pro vytvoření propojení se službou IoT Hub.  

      ![Propojte název centra a připojte se k instanci služby Device Provisioning v okně portálu.](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Vybrané centrum by se teď mělo zobrazit v okně **Propojená centra IoT**. Možná budete muset spustit **aktualizaci** , aby se zobrazila.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem na portálu Azure Portal.

1. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte svou službu Device Provisioning. V horní části podokna s podrobnostmi o zařízení vyberte **Odstranit**.  
2. V nabídce na levé straně Azure Portal vyberte **všechny prostředky** a potom vyberte Centrum IoT. V horní části podokna podrobností centra vyberte **Odstranit**.  

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili službu IoT Hub a instanci služby Device Provisioning a propojili jste tyto dva prostředky. Informace o tom, jak pomocí tohoto nastavení zřídit simulované zařízení, najdete v rychlém startu pro vytvoření simulovaného zařízení.

> [!div class="nextstepaction"]
> [Rychlý Start k vytvoření simulovaného zařízení](./quick-create-simulated-device.md)
