---
title: Aktivace Azure Functions pomocí webhooků v Azure IoT Central
description: Vytvoření aplikace funkcí, které se spustí pokaždé, když se aktivuje pravidlo v Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0d92e9bdf8ec207e5ef0e3f891c162182b5a4fff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60518374"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Aktivace Azure Functions pomocí webhooků v Azure IoT Central

*Toto téma platí pro tvůrce a správce.*

Spustit kód bez serveru webhooku výstupu z IoT Central pravidla pomocí Azure Functions. Nemusíte zřizovat virtuální počítač nebo publikovat webovou aplikaci k používání Azure Functions, ale místo toho můžete spustit tento kód bez serveru. Pomocí služby Azure Functions k transformaci datová část webhooku před odesláním do konečného cíle jako je SQL database nebo služby Event Grid.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="how-to-connect-azure-functions"></a>Postup připojení Azure Functions

1. [Vytvořit novou aplikaci function app na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Vytvořit novou aplikaci function app na webu Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Rozbalte aplikaci function app a vyberte **tlačítko +** vedle funkce. Pokud tato funkce je první z nich ve své aplikaci function app, vyberte **portálu** jako vývojové prostředí a vyberte **pokračovat**.

    ![Zvolte vlastní funkci v aplikaci Function app](media/howto-trigger-azure-functions/customfunction.png)

3. Zvolte **Webhook + API** šablony a vyberte **vytvořit**. Toto téma používá funkci Azure na základě rozhraní .NET.

    ![Vyberte trigger obecným webhookem](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. V nové funkci, vyberte **<> / získat adresu URL funkce**a pak zkopírujte a uložte hodnotu. Tuto hodnotu použijete ke konfiguraci webhooku.

    ![Získat adresu URL funkce](media/howto-trigger-azure-functions/getfunctionurl.png)

4. IoT Central přejděte na pravidlo, které chcete připojit k vaší aplikaci funkcí.

5. Přidání akce webhooku. Zadejte **zobrazovaný název** a vložte adresu URL funkce, které jste si zkopírovali do **adresu URL zpětného volání**.

    ![Zadejte adresu URL funkce do pole Adresa URL zpětného volání](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Uložte pravidlo. Teď když se pravidlo aktivuje, vyvolá webhook ke spuštění aplikace function app. V aplikaci function app, můžete vybrat **monitorování** zobrazíte historie volání funkce. Můžete se podívat na historii App Insights nebo klasické zobrazení.

    ![Monitorování historie volání funkce](media/howto-trigger-azure-functions/monitorfunction.PNG)

Další informace najdete v článku Azure Functions o [vytvoření funkce aktivované obecným webhookem](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili, jak nastavit a používat webhooky, navrhované dalším krokem je prozkoumat [vytváření pracovních postupů v Microsoft Flow](howto-add-microsoft-flow.md).
