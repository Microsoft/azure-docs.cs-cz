---
title: Kurz – nastavení cloudu pro Azure IoT Hub Device Provisioning Service na portálu
description: V tomto kurzu se dozvíte, jak můžete nastavit cloudové prostředky pro zřizování zařízení v [Azure Portal](https://portal.azure.com) pomocí IoT Hub Device PROVISIONING Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f45c3def84c548ba12221efa59e9ebbd4699df71
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316065"
---
# <a name="tutorial-configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Kurz: Konfigurace cloudových prostředků pro zřizování zařízení pomocí IoT Hub Device Provisioning Service

Tento kurz ukazuje, jak nastavit cloud pro automatické zřizování zařízení pomocí služby IoT Hub Device Provisioning. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření služby IoT Hub Device Provisioning a získání rozsahu ID pomocí webu Azure Portal
> * Vytvoření centra IoT (neboli IoT Hubu)
> * Propojení centra IoT se službou Device Provisioning
> * Nastavení zásad přidělování ve službě Device Provisioning

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Vytvoření instance služby Device Provisioning a získání rozsahu ID

Pomocí těchto kroků vytvořte novou instanci služby Device Provisioning.

1. V levém horním rohu Azure Portal klikněte na **vytvořit prostředek**.

2. Do vyhledávacího pole zadejte **Device Provisioning**. 

3. Klikněte na **Služba IoT Hub Device Provisioning**.

4. Vyplňte formulář **Služba IoT Hub Device Provisioning** s použitím následujících informací:
    
   | Nastavení       | Navrhovaná hodnota | Popis | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Název** | Jakýkoli jedinečný název | -- | 
   | **Předplatné** | Vaše předplatné  | Podrobnosti o vašich předplatných najdete v tématu [Předplatná](https://account.windowsazure.com/Subscriptions). |
   | **Skupina prostředků** | myResourceGroup | Platné názvy skupin prostředků najdete v tématu [Pravidla a omezení pojmenování](/azure/architecture/best-practices/resource-naming). |
   | **Umístění** | Libovolné platné umístění | Informace o oblastech najdete v tématu [Oblasti služeb Azure](https://azure.microsoft.com/regions/). |   

   ![Zadání základních informací o službě Device Provisioning na portálu](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Klikněte na **Vytvořit**. Za malou chvíli se vytvoří instance služby Device Provisioning a zobrazí se stránka **Přehled**.

6. Ze stránky **Přehled** pro novou instanci služby zkopírujte pro pozdější použití hodnotu položky **Obor ID**. Tato hodnota slouží k identifikaci ID registrací a poskytuje záruku, že je ID registrace jedinečné.

7. Pro pozdější použití zkopírujte i hodnotu položky **Koncový bod služby**. 

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT (neboli IoT Hubu)

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro Centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Nyní jste vytvořili službu IoT Hub a máte název hostitele a připojovací řetězec služby IoT Hub, které potřebujete k dokončení kurzu.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Propojení služby Device Provisioning s centrem IoT

Dalším krokem je propojení služby Device Provisioning s centrem IoT, aby služba IoT Hub Device Provisioning mohla v tomto centru registrovat zařízení. Služba může zřizovat zařízení pouze v centrech IoT propojených se službou Device Provisioning. Postupujte takto:

1. Na stránce **Všechny prostředky** klikněte na instanci služby Device Provisioning, kterou jste vytvořili dříve.

2. Na stránce služby Device Provisioning klikněte na **Propojená centra IoT**.

3. Klikněte na **Přidat**.

4. Na stránce **Přidat propojení na centrum IoT Hub** zadejte následují informace a klikněte na **Uložit**:

    * **Předplatné:** Přesvědčte se, že je vybrané předplatné obsahující IoT Hub. Na IoT Hub nacházející se v jiném předplatném můžete odkázat.

    * **IoT Hub:** Zvolte název IoT Hubu, který chcete propojit s instancí služby Device Provisioning.

    * **Zásady přístupu:** Vyberte **iothubowner** jako přihlašovací údaje, které se použijí k vytvoření propojení s IoT Hubem.

   ![Propojení názvu centra se službou Device Provisioning na portálu](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Nastavení zásad přidělování ve službě Device Provisioning

Zásada přidělování je IoT Hub Device Provisioning Service nastavení, které určuje, jak se zařízení přiřazují do služby IoT Hub. Existují tři podporované zásady přidělování: 

1. **Nejnižší latence:** Zařízení se zřizují v centru IoT, které má se zařízením nejnižší latenci.

2. **Rovnoměrně vážená distribuce (výchozí):** Zařízení se zřizují se stejnou pravděpodobností ve všech propojených centrech IoT. Toto nastavení je výchozí. Pokud zřizujete zařízení pouze v jednom centru IoT, můžete nechat toto nastavení. 

3. **Statická konfigurace prostřednictvím seznamu registrací:** Specifikace požadovaného centra IoT v seznamu registrací má přednost před zásadami přidělování na úrovni služby Device Provisioning.

Pokud chcete nastavit zásady přidělování, na stránce služby Device Provisioning klikněte na **Spravovat zásady přidělování**. Ujistěte se, že jsou zásady nastavené na hodnotu **Rovnoměrně vážená distribuce** (výchozí). Pokud provedete nějaké změny, až budete hotovi, klikněte na **Uložit**.

![Správa zásad přidělování](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další kurzy v této kolekci vycházejí z tohoto kurzu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto kurzu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené v rámci tohoto kurzu na webu Azure Portal.

1. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte svou instanci služby IoT Hub Device Provisioning. V horní části stránky **Všechny prostředky** klikněte na **Odstranit**.  

2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyberte své centrum IoT. V horní části stránky **Všechny prostředky** klikněte na **Odstranit**.
 
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření služby IoT Hub Device Provisioning a získání rozsahu ID pomocí webu Azure Portal
> * Vytvoření centra IoT (neboli IoT Hubu)
> * Propojení centra IoT se službou Device Provisioning
> * Nastavení zásad přidělování ve službě Device Provisioning

V dalším kurzu se dozvíte, jak nastavit zařízení pro zřizování.

> [!div class="nextstepaction"]
> [Nastavení zařízení pro zřizování](tutorial-set-up-device.md)
