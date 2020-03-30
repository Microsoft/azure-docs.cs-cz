---
title: Nastavení služby zřizování zařízení služby IoT Hub na webu Azure Portal
description: Úvodní příručka – nastavení služby Azure IoT Hub Device Provisioning Service (DPS) na webu Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 765b7e5bb7341974a6a16d3f1ed0bcabf415f9f3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76029175"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Úvodní příručka: Nastavení služby zřizování zařízení služby IoT Hub pomocí portálu Azure

Službu zřizování zařízení služby IoT Hub lze použít s ioT hubem, který umožňuje zřizování s nulovým dotykem a za chvíli do požadovaného centra IoT hub bez nutnosti lidského zásahu, což zákazníkům umožňuje zřizovat miliony zařízení IoT bezpečným a škálovatelným způsobem. Služba zřizování zařízení Služby Azure IoT Hub podporuje zařízení IoT s čipem TPM, symetrickým klíčem a ověřováním o certifikátech X.509. Další informace najdete v přehledu [služby IoT Hub Device Provisioning Service.](./about-iot-dps.md)

V tomto rychlém startu se dozvíte, jak nastavit službu zřizování zařízení služby IoT Hub na webu Azure Portal pro zřizování zařízení pomocí následujících kroků:
> [!div class="checklist"]
> * Vytvoření centra IoT Hub pomocí portálu Azure
> * Vytvoření služby IoT Hub Device Provisioning a získání rozsahu ID pomocí webu Azure Portal
> * Propojení centra IoT se službou Device Provisioning


Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.


## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Vytvoření nové služby zřizování zařízení služby IoT Hub

1. Znovu vyberte tlačítko **+ Vytvořit zdroj.**

2. *Vyhledejte na webu Marketplace* **službu Zřizování zařízení**. Vyberte **Službu zřizování zařízení služby IoT Hub** a stiskněte tlačítko **Vytvořit.** 

3. Zadejte následující informace o nové instanci služby Zřizování zařízení a stiskněte **příkaz Vytvořit**.

    * **Název:** Zadejte jedinečný název pro novou instanci služby Zřizování zařízení. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.
    * **Předplatné:** Zvolte předplatné, které chcete použít k vytvoření této instance služby zřizování zařízení.
    * **Skupina prostředků:** Toto pole umožňuje vytvořit novou skupinu prostředků nebo zvolit existující, která má obsahovat novou instanci. Zvolte stejnou skupinu prostředků, kterou jste vytvořili výše a obsahuje IoT Hub, například **TestResources**. Když umístíte všechny související prostředky do jedné skupiny, můžete je spravovat společně. Například odstraněním skupiny prostředků se odstraní všechny prostředky, které tato skupina obsahuje. Další informace najdete [v tématu Správa skupin prostředků Správce prostředků Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
    * **Umístění:** Vyberte nejbližší umístění k vašim zařízením.

      ![Zadejte základní informace o instancích služby Zřizování zařízení do okna portálu.](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Vyberte tlačítko oznámení pro sledování vytvoření instance prostředku. Po úspěšném nasazení služby vyberte **Připnout na řídicí panel**a pak **přejít na prostředek**.

    ![Monitorování oznámení nasazení](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Propojení centra IoT hub u služby Zřizování zařízení

V této části přidáte konfiguraci do instance služby zřizování zařízení. Tato konfigurace nastaví službu IoT Hub, pro kterou jsou zařízení zřízená.

1. V levé nabídce portálu Azure vyberte tlačítko **Všechny prostředky.** Vyberte instanci služby Device Provisioning, kterou jste vytvořili v předchozí části. 

    Pokud je vaše nabídka nakonfigurována pomocí **informačního rámečku** namísto **ukotveného** režimu v nastavení portálu, budete muset kliknout na 3 řádky v levém horním rohu, abyste otevřeli nabídku portálu vlevo.  

2. V nabídce Služby zřizování zařízení vyberte **propojené služby IoT hubs**. Stiskněte tlačítko **+ Přidat** vidět v horní části. 

3. Na **stránce Přidat odkaz na službu IoT hub** zadejte následující informace, které propojí novou instanci služby Device Provisioning service s centrem IoT. Pak stiskněte **klávesu Uložit**. 

    * **Předplatné:** Vyberte předplatné obsahující službu IoT hub, kterou chcete propojit s novou instancí služby Device Provisioning Service.
    * **Centrum Iot:** Vyberte službu IoT hub, kterou chcete propojit s novou instancí služby Zřizování zařízení.
    * **Zásady přístupu:** Vyberte **iothubowner** jako přihlašovací údaje pro vytvoření propojení se službou IoT Hub.  

      ![Propojení názvu rozbočovače s propojením s instancí Služby zřizování zařízení v okně portálu](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Vybrané centrum by se teď mělo zobrazit v okně **Propojená centra IoT**. Možná budete muset stisknout **Refresh,** aby se to zobrazilo.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem na portálu Azure Portal.

1. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte službu zřizování zařízení. V horní části podokna podrobností zařízení vyberte **Odstranit**.  
2. V levé nabídce na webu Azure Portal vyberte **Všechny prostředky** a pak vyberte své centrum IoT. V horní části podokna podrobností rozbočovače vyberte **Odstranit**.  

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili službu IoT hub a instanci služby Zřizování zařízení a propojili jste tyto dva prostředky. Chcete-li se dozvědět, jak pomocí tohoto nastavení zřídit simulované zařízení, pokračujte na rychlý start pro vytvoření simulované zařízení.

> [!div class="nextstepaction"]
> [Rychlý start pro vytvoření simulovaného zařízení](./quick-create-simulated-device-symm-key.md)
