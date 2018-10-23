---
title: Nastavení služby Device Provisioning na webu Azure Portal | Dokumentace Microsoftu
description: Rychlý start Azure – Nastavení služby Azure IoT Hub Device Provisioning na webu Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 07/12/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5533dacc4a782cbdb6026c4a4d5c5bed9e39147a
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320148"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Nastavení služby Azure IoT Hub Device Provisioning pomocí webu Azure Portal

Tyto kroky ukazují, jak na portálu nastavit cloudové prostředky Azure pro zřizování zařízení. Tento článek obsahuje kroky pro vytvoření služby IoT Hub, vytvoření nové služby IoT Hub Device Provisioning a jejich propojení. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Vytvoření nové instance služby IoT Hub Device Provisioning

1. Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. *V Marketplace vyhledejte***službu Device Provisioning**. Vyberte **Služba IoT Hub Device Provisioning** a klikněte na tlačítko **Vytvořit**. 

3. Zadejte následující informace pro vaši novou instanci služby Device Provisioning a klikněte na **Vytvořit**.

    * **Název:** Zadejte jedinečný název vaší nové instance služby Device Provisioning. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.
    * **Předplatné:** Vyberte předplatné, které chcete použít při vytváření této instance služby Device Provisioning.
    * **Skupina prostředků:** Toto pole umožňuje vytvořit novou skupinu prostředků nebo zvolit existující, která má obsahovat novou instanci. Zvolte stejnou skupinu prostředků, kterou jste vytvořili výše a obsahuje IoT Hub, například **TestResources**. Když umístíte všechny související prostředky do jedné skupiny, můžete je spravovat společně. Například odstraněním skupiny prostředků se odstraní všechny prostředky, které tato skupina obsahuje. Další informace najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-portal.md).
    * **Umístění:** Vyberte pro zařízení nejbližší umístění.

    ![Zadání základních informací o instanci služby Device Provisioning v okně portálu](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Pokud chcete monitorovat vytváření instance prostředku, klikněte na tlačítko oznámení. Po úspěšném nasazení služby klikněte na **Připnout na řídicí panel** a pak na **Přejít k prostředku**.

    ![Monitorování oznámení nasazení](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Propojení centra IoT a služby Device Provisioning

V této části přidáte konfiguraci do instance služby Device Provisioning. Tato konfigurace nastaví službu IoT Hub, pro kterou jsou zařízení zřízená.

1. Klikněte na tlačítko **Všechny prostředky** v nabídce vlevo na webu Azure Portal. Vyberte instanci služby Device Provisioning, kterou jste vytvořili v předchozí části.  

2. V okně s přehledem služby Device Provisioning vyberte **Propojená centra IoT**. Klikněte na tlačítko **+ Přidat** v horní části. 

3. Na stránce **Přidat propojení s centrem IoT** zadejte následující informace, abyste propojili vaši novou instanci služby Device Provisioning s centrem IoT. Potom klikněte na **Uložit**. 

    * **Předplatné:** Vyberte předplatné obsahující centrum IoT, které chcete propojit s vaší novou instancí služby Device Provisioning.
    * **Centrum IoT:** Vyberte centrum IoT k propojení s vaší novou instancí služby Device Provisioning.
    * **Zásady přístupu:** Vyberte **iothubowner** jako přihlašovací údaje pro vytvoření propojení se službou IoT Hub.  

    ![Propojení názvu centra s instancí služby Device Provisioning v okně portálu](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Vybrané centrum by se teď mělo zobrazit v okně **Propojená centra IoT**. Možná budete muset kliknout na tlačítko **Aktualizovat**, aby se okno **Propojená centra IoT Hub** zobrazilo.



## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem na webu Azure Portal.

1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte svou službu Device Provisioning. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.  
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte své centrum IoT. V horní části okna **Všechny prostředky** klikněte na **Odstranit**.  

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili centrum IoT a instanci služby Device Provisioning a propojili jste tyto dva prostředky. Pokud chcete zjistit, jak pomocí tohoto nastavení zřídit simulované zařízení, pokračujte k rychlému startu pro vytvoření simulovaného zařízení.

> [!div class="nextstepaction"]
> [Rychlý start k vytvoření simulovaného zařízení](./quick-create-simulated-device.md)
