---
title: Použití služby Azure IoT Hub Device Provisioning Service ke zřízení zařízení v několika centrech IoT s vyrovnáváním zatížení | Microsoft Docs
description: Automatické zřízení zařízení služby DPS v několika centrech IoT s vyrovnáváním zatížení na webu Azure Portal
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: d0a3720fe729d5e260bbe5b0902460c8c7cfc7cb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629622"
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Zřízení zařízení v několika centrech IoT s vyrovnáváním zatížení

Tento kurz předvádí, jak zřídit zařízení pro více center IoT s vyrovnáváním zatížení pomocí služby Device Provisioning Service (DPS). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použít web Azure Portal ke zřízení druhého zařízení ve druhém centru IoT 
> * Přidat položku seznamu registrací do druhého zařízení
> * Nastavit zásady přidělování služby DPS na **rovnoměrnou distribuci**
> * Propojit nové centrum IoT se službou DPS

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Tento kurz je založený na předchozím kurzu týkajícím se [zřízení zařízení v centru](tutorial-provision-device-to-hub.md).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Použití webu Azure Portal ke zřízení druhého zařízení ve druhém centru IoT

Postupujte podle kroků v kurzu týkajícím se [zřízení zařízení v centru](tutorial-provision-device-to-hub.md) a zřiďte druhé zařízení v jiném centru IoT.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Přidání položky seznamu registrací do druhého zařízení

Seznam registrací informuje službu DPS o tom, jaká metoda ověření identity (metoda potvrzení identity zařízení) se u zařízení používá. Dalším krokem je přidání položky seznamu registrací pro druhé zařízení. 

1. Na stránce pro službu DPS klikněte na **Spravovat registrace**. Zobrazí se stránka **Přidat položku seznamu registrací**. 
2. V horní části stránky klikněte na **Přidat**.
2. Vyplňte pole a pak klikněte na **Uložit**.

## <a name="set-the-dps-allocation-policy"></a>Nastavení zásad přidělování služby DPS

Zásady přidělování jsou nastavením služby DPS, které určuje způsob přiřazování zařízení k centru IoT. Existují tři podporované zásady přidělování: 

1. **Nejnižší latence:** Zařízení se zřizují v centru IoT, které má se zařízením nejnižší latenci.
2. **Rovnoměrně vážená distribuce (výchozí):** Zařízení se zřizují se stejnou pravděpodobností ve všech propojených centrech IoT. Toto je výchozí nastavení. Pokud zřizujete zařízení pouze v jednom centru IoT, můžete nechat toto nastavení. 
3. **Statická konfigurace prostřednictvím seznamu registrací:** Specifikace požadovaného centra IoT v seznamu registrací má přednost před zásadami přidělování na úrovni služby DPS.

Postupujte podle těchto kroků k nastavení zásad přidělování:

1. Pokud chcete nastavit zásady přidělování, na stránce služby DPS klikněte na **Spravovat zásady přidělování**.
2. Nastavte zásady přidělování na **Rovnoměrně vážená distribuce**.
3. Klikněte na **Uložit**.

## <a name="link-the-new-iot-hub-to-dps"></a>Propojení nového centra IoT se službou DPS

Propojte službu DPS a centrum IoT, aby služba DPS mohla registrovat zařízení v tomto centru.

1. Na stránce **Všechny prostředky** klikněte na dříve vytvořenou službu DPS.
2. Na stránce služby DPS klikněte na **Propojená centra IoT Hub**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Na stránce **Přidat propojení s centrem IoT** pomocí přepínačů určete, jestli je propojené centrum IoT umístěné v aktuálním předplatném nebo v jiném předplatném. Pak v poli **Centrum IoT** zvolte název centra IoT.
5. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Použít web Azure Portal ke zřízení druhého zařízení ve druhém centru IoT 
> * Přidat položku seznamu registrací do druhého zařízení
> * Nastavit zásady přidělování služby DPS na **rovnoměrnou distribuci**
> * Propojit nové centrum IoT se službou DPS

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
