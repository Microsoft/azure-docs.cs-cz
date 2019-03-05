---
title: Aktivace Azure Functions pomocí webhooků v Azure IoT Central
description: Vytvoření aplikace funkcí, které se spustí pokaždé, když se aktivuje pravidlo v Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 512956d2de0f9a838cc6378345a334e489d1d120
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306863"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Aktivace Azure Functions pomocí webhooků v Azure IoT Central

*Toto téma platí pro tvůrce a správce.*

Spustit kód bez serveru webhooku výstupu z IoT Central pravidla pomocí Azure Functions. Nemusíte zřizovat virtuální počítač nebo publikovat webovou aplikaci k používání Azure Functions, ale místo toho můžete spustit tento kód serverlessly. Pomocí služby Azure Functions k transformaci datová část webhooku před odesláním do konečného cíle jako je SQL database nebo služby Event Grid.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="how-to-connect-azure-functions"></a>Postup připojení Azure Functions

1. [Vytvořit novou aplikaci function app na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Vytvořit novou aplikaci function app na webu Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Rozbalte aplikaci function app a vyberte **tlačítko +** vedle funkce. Pokud jde o první funkci ve vaší aplikaci funkcí, vyberte možnost **Vlastní funkce**. Zobrazí se kompletní sada šablon funkcí.

    ![Zvolte vlastní funkci v aplikaci Function app](media/howto-trigger-azure-functions/customfunction.png)

3. Do vyhledávacího pole zadejte **"generic"** a zvolte jazyk požadovaný pro šablonu funkce aktivované obecným webhookem. Toto téma používá funkci v jazyce C#. 

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
