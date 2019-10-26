---
title: Aktivace Azure Functions pomocí webhooků v Azure IoT Central
description: Vytvořte aplikaci Function App, která se spustí pokaždé, když se v Azure IoT Central aktivuje pravidlo.
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a9590e5554956418859a07b43fb57724783343fe
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952846"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Aktivace Azure Functions pomocí webhooků v Azure IoT Central

*Toto téma se týká tvůrců a správců.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Pomocí Azure Functions můžete spustit kód bez serveru na výstupu Webhooku z pravidel IoT Central. Nemusíte zřizovat virtuální počítač nebo publikovat webovou aplikaci, abyste ji mohli použít Azure Functions, ale místo toho můžete spustit tento kód bez serveru. Pomocí Azure Functions transformovat datovou část Webhooku před odesláním do konečného umístění, jako je například databáze SQL nebo Event Grid.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="how-to-connect-azure-functions"></a>Postup připojení Azure Functions

1. [Vytvořte novou aplikaci Function App v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Vytvoření nové aplikace Function App v Azure Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Rozbalte aplikaci Function App a vyberte **tlačítko +** vedle položky funkce. Pokud je tato funkce první ve vaší aplikaci Function App, vyberte **v části portál** jako vývojové prostředí a vyberte **pokračovat**.

    ![Volba vlastní funkce v aplikaci Function App](media/howto-trigger-azure-functions/customfunction.png)

3. Zvolte **Webhook + rozhraní API** Template a vyberte **vytvořit**. Toto téma používá službu Azure Functions založenou na rozhraní .NET.

    ![Vybrat obecný Trigger Webhooku](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. V nové funkci vyberte **</> získat adresu URL funkce**a potom zkopírujte a uložte hodnotu. Tuto hodnotu použijete ke konfiguraci webhooku.

    ![Získat adresu URL funkce](media/howto-trigger-azure-functions/getfunctionurl.png)

4. V IoT Central přejděte na pravidlo, které chcete připojit k aplikaci Function App.

5. Přidejte akci Webhooku. Zadejte **Zobrazovaný název** a vložte adresu URL funkce, kterou jste zkopírovali dříve do **adresy URL zpětného volání**.

    ![Do pole Adresa URL pro zpětné volání zadejte adresu URL funkce.](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Uložte pravidlo. Když se teď pravidlo aktivuje, Webhook vyvolá spuštění aplikace Function App. V aplikaci Function App můžete vybrat **monitorování** a zobrazit historii vyvolání funkce. Pomocí App Insights nebo klasického zobrazení se můžete podívat na historii.

    ![Monitorování historie vyvolání funkce](media/howto-trigger-azure-functions/monitorfunction.PNG)

Další informace najdete v Azure Functions článku o [Vytvoření funkce aktivované obecným webhookem](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak nastavit a používat Webhooky, je doporučený další krok prozkoumat [vytváření pracovních postupů v Microsoft Flow](howto-add-microsoft-flow.md).
