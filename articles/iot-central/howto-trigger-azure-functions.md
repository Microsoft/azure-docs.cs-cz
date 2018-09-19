---
title: Aktivace Azure Functions pomocí webhooků v Azure IoT Central
description: Vytvoření aplikace funkcí, které se spustí pokaždé, když se aktivuje pravidlo v Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 694c259472bf7e18f0ae3d424acf5b5cfb7ea7ab
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294250"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Aktivace Azure Functions pomocí webhooků v Azure IoT Central

*Toto téma platí pro tvůrce a správce.*

Spustit kód bez serveru webhooku výstupu z IoT Central pravidla pomocí Azure Functions. Nemusíte zřizovat virtuální počítač nebo publikovat webovou aplikaci k používání Azure Functions, ale místo toho můžete spustit tento kód serverlessly. Pomocí služby Azure Functions k transformaci datová část webhooku před odesláním do konečného cíle jako je SQL database nebo služby Event Grid. 

## <a name="prerequisites"></a>Požadavky
+ Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="how-to-connect-azure-functions"></a>Postup připojení Azure Functions

1. [Vytvořte novou aplikaci function app na webu Azure Portal. ](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Vytvořit novou aplikaci function app na webu Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Rozbalte aplikaci function app a klikněte **tlačítko +** vedle funkce. Pokud jde o první funkci ve vaší aplikaci funkcí, vyberte možnost **Vlastní funkce**. Zobrazí se kompletní sada šablon funkcí.
    
    ![Zvolte vlastní funkci v aplikaci Function app](media/howto-trigger-azure-functions/customfunction.png)

3. Do vyhledávacího pole zadejte **"generic"** a zvolte jazyk požadovaný pro šablonu funkce aktivované obecným webhookem. Toto téma používá funkci v jazyce C#. 

    ![Vyberte trigger obecným webhookem](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. V nové funkci klikněte na **</> Získat adresu URL funkce** a potom zkopírujte a uložte příslušnou hodnotu. Tuto hodnotu použijete ke konfiguraci webhooku. 

    ![Získat adresu URL funkce](media/howto-trigger-azure-functions/getfunctionurl.png)
4. IoT Central přejděte na pravidlo, které chcete připojit k vaší aplikaci funkcí.

5. Přidání akce webhooku. Zadejte **zobrazovaný název** a vložte adresu URL funkce, které jste si zkopírovali do **adresu URL zpětného volání**.

    ![Zadejte adresu URL funkce do pole Adresa URL zpětného volání](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Uložte pravidlo. Teď když se pravidlo aktivuje, webhook se vyvolá ke spuštění aplikace function app. V aplikaci function app, můžete kliknout na **monitorování** zobrazíte historie volání funkce. Můžete se podívat na historii App Insights nebo klasické zobrazení.

    ![Monitorování historie volání funkce](media/howto-trigger-azure-functions/monitorfunction.PNG)

Další informace najdete v článku Azure Functions o [vytvoření funkce aktivované obecným webhookem](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). 

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili, jak nastavit a používat webhooky, navrhované dalším krokem je prozkoumat [vytváření pracovních postupů v Microsoft Flow](howto-add-microsoft-flow.md).
