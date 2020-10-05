---
title: Rychlý Start – Nastavení IoT Hub Device Provisioning Service v Azure Portal
description: Rychlý Start – Nastavení Azure IoT Hub Device Provisioning Service (DPS) v Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 644635d1ab7b0a43c8df3e10bbbd02d1ea67a94a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91297228"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Rychlý Start: nastavení IoT Hub Device Provisioning Service s využitím Azure Portal

IoT Hub Device Provisioning Service můžete použít s IoT Hub a povolit tak nulové a nepotřebné zřizování za běhu do požadovaného centra IoT Hub bez nutnosti zásahu člověka, což zákazníkům umožňuje zajistit zabezpečený a škálovatelný přístup pro miliony zařízení IoT. Azure IoT Hub Device Provisioning Service podporuje zařízení IoT s ověřováním certifikátů TPM, symetrického klíče a X. 509. Další informace najdete v tématu [přehled IoT Hub Device Provisioning Service](./about-iot-dps.md) .

V tomto rychlém startu se dozvíte, jak nastavit IoT Hub Device Provisioning Service na webu Azure Portal pro zřizování zařízení pomocí následujících kroků:

* Vytvoření IoT Hub pomocí Azure Portal
* Vytvoření služby IoT Hub Device Provisioning a získání rozsahu ID pomocí webu Azure Portal
* Propojení centra IoT se službou Device Provisioning


Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="create-an-iot-hub"></a>Vytvoření centra IoT (neboli IoT Hubu)

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Vytvořit nový IoT Hub Device Provisioning Service

1. Znovu vyberte tlačítko **+ vytvořit prostředek** .

2. Vyhledejte **službu Device Provisioning**na *webu Marketplace* . Vyberte **IoT Hub Device Provisioning Service** a stiskněte tlačítko **vytvořit** . 

3. Zadejte následující informace pro novou instanci služby Device Provisioning a stiskněte **vytvořit**.

    * **Název:** Zadejte jedinečný název nové instance služby Device Provisioning. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.
    * **Předplatné:** Vyberte předplatné, které chcete použít k vytvoření této instance služby Device Provisioning.
    * **Skupina prostředků:** Toto pole umožňuje vytvořit novou skupinu prostředků nebo zvolit existující, která má obsahovat novou instanci. Zvolte stejnou skupinu prostředků, kterou jste vytvořili výše a obsahuje IoT Hub, například **TestResources**. Když umístíte všechny související prostředky do jedné skupiny, můžete je spravovat společně. Například odstraněním skupiny prostředků se odstraní všechny prostředky, které tato skupina obsahuje. Další informace najdete v tématu [Správa skupin prostředků Azure Resource Manager](../azure-resource-manager/management/manage-resource-groups-portal.md).
    * **Umístění:** Vyberte nejbližší umístění zařízení.

      ![Zadejte základní informace o instanci služby Device Provisioning v okně portálu.](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Výběrem tlačítka oznámení můžete monitorovat vytvoření instance prostředku. Po úspěšném nasazení služby vyberte **Připnout na řídicí panel**a pak **Přejít k prostředku**.

    ![Monitorování oznámení nasazení](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Propojení centra IoT a služby Device Provisioning

V této části přidáte konfiguraci do instance služby Device Provisioning. Tato konfigurace nastaví službu IoT Hub, pro kterou jsou zařízení zřízená.

1. V nabídce na levé straně Azure Portal vyberte tlačítko **všechny prostředky** . Vyberte instanci služby Device Provisioning, kterou jste vytvořili v předchozí části. 

    Pokud je vaše nabídka nakonfigurovaná pomocí **informačního rámečku** místo **ukotveného** režimu v nastavení portálu, kliknutím na 3 řádky v levém horním rohu otevřete nabídku portál na levé straně.  

2. V nabídce služby Device Provisioning vyberte **propojená centra IoT**. Stiskněte tlačítko **+ Přidat** zobrazené v horní části. 

3. Na stránce **Přidat odkaz na centrum IoT** zadejte následující informace, které propojí novou instanci služby Device Provisioning se službou IoT Hub. Pak stiskněte **Uložit**. 

    * **Předplatné:** Vyberte předplatné, které obsahuje centrum IoT, které chcete propojit s novou instancí služby Device Provisioning.
    * **Centrum IoT:** Vyberte centrum IoT, které chcete propojit s novou instancí služby Device Provisioning.
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
> [Rychlý Start k vytvoření simulovaného zařízení](./quick-create-simulated-device-symm-key.md)
