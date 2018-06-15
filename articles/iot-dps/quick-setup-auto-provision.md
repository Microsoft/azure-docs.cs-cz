---
title: Nastavení služby Device Provisioning na webu Azure Portal | Dokumentace Microsoftu
description: Rychlý start Azure – Nastavení služby Azure IoT Hub Device Provisioning na webu Azure Portal
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 276bd33f5724db4d67da0cc31b16297915c9a417
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629435"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Nastavení služby Azure IoT Hub Device Provisioning pomocí webu Azure Portal

Tyto kroky ukazují, jak na portálu nastavit cloudové prostředky Azure pro zřizování zařízení. To zahrnuje vytvoření služby IoT Hub a nové služby IoT Hub Device Provisioning a jejich propojení. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

1. Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Vyberte **Internet věcí**, pak **IoT Hub** a klikněte na tlačítko **Vytvořit**. 

3. Zadejte **Název** centra IoT. Vyberte z dostupných možností cenovou úroveň, zadejte [Jednotky služby IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/), vyberte počet oddílů pro zprávy typu zařízení-cloud a předplatné, které se pro tento prostředek použije. Zadejte název nové nebo existující skupiny prostředků a vyberte umístění. Jakmile budete hotovi, klikněte na **Vytvořit**.

    ![Zadání základních informací o centru IoT v okně portálu](./media/quick-setup-auto-provision/create-iot-hub-portal.png)  

4. Po úspěšném nasazení centra IoT se automaticky otevře okno s přehledem centra.


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Vytvoření nové instance služby IoT Hub Device Provisioning

1. Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. *V Marketplace vyhledejte***službu Device Provisioning**. Vyberte **Služba IoT Hub Device Provisioning** a klikněte na tlačítko **Vytvořit**. 

3. Zadejte **Název** instance služby Device Provisioning. Vyberte předplatné, které se pro tuto instanci použije, a zadejte název nové nebo existující skupiny prostředků. Vyberte umístění. Jakmile budete hotovi, klikněte na **Vytvořit**.

    ![Zadání základních informací o instanci služby Device Provisioning v okně portálu](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Po úspěšném nasazení služby se automaticky otevře okno s jejím přehledem.


## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Propojení centra IoT a služby Device Provisioning

1. Klikněte na tlačítko **Všechny prostředky** v nabídce vlevo na webu Azure Portal. Vyberte instanci služby Device Provisioning, kterou jste vytvořili v předchozí části.  

2. V okně s přehledem služby Device Provisioning vyberte **Propojená centra IoT**. Klikněte na tlačítko **+ Přidat** v horní části. 

3. V okně portálu **Přidat propojení s centrem IoT** vyberte buď aktuální předplatné, nebo zadejte název a připojovací řetězec pro jiné předplatné. V rozevíracím seznamu vyberte název centra. Jakmile budete hotovi, klikněte na **Uložit**. 

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
