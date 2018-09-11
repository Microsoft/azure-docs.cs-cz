---
title: Aktivace Azure Functions pomocí webhooků v Azure IoT Central
description: Vytvoření aplikace funkcí, které se spustí pokaždé, když se aktivuje pravidlo v Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 09/06/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b14dc4eeec1ab543c407b1bb1cf8a5ce46f3ecb
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356368"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Aktivace Azure Functions pomocí webhooků v Azure IoT Central

Spustit kód bez serveru webhooku výstupu z IoT Central pravidla pomocí Azure Functions. Nemusíte zřizovat virtuální počítač nebo publikovat webovou aplikaci k používání Azure Functions, ale místo toho můžete spustit tento kód serverlessly. Pomocí služby Azure Functions k transformaci datová část webhooku před odesláním do konečného cíle jako je SQL database nebo služby Event Grid. 

## <a name="prerequisites"></a>Požadavky
+ Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="how-to-connect-azure-functions"></a>Postup připojení Azure Functions

1. [Vytvořte novou aplikaci function app na webu Azure Portal. ](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).
2. Rozbalte aplikaci function app a klikněte tlačítko vedle funkce +. Pokud tato funkce je první z nich ve své aplikaci function app, vyberte vlastní funkce. Zobrazí se kompletní sada šablon funkcí.
3. Do vyhledávacího pole zadejte obecné a zvolte jazyk požadovaný pro šablonu funkce aktivované obecným webhookem. Toto téma používá funkci v jazyce C#. 
4. V nové funkci klikněte na **</> Získat adresu URL funkce** a potom zkopírujte a uložte příslušnou hodnotu. Tuto hodnotu použijete ke konfiguraci webhooku. 
4. V IoT Central vyhledejte pravidlo, které chcete připojit k vaší aplikaci funkcí. 
5. Přidání akce webhooku. Zadejte **zobrazovaný název** a vložte adresu URL funkce, které jste si zkopírovali dříve.
6. Uložte pravidlo. Teď když se pravidlo aktivuje, webhook se vyvolá ke spuštění aplikace function app. V aplikaci function app můžete sledovat v protokolech a pokaždé, když se aktivuje funkci najdete v tématu.

Další informace najdete v článku Azure Functions o [vytvoření funkce aktivované obecným webhookem](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function). 

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili, jak nastavit a používat webhooky, navrhované dalším krokem je prozkoumat [vytváření pracovních postupů v Microsoft Flow](howto-add-microsoft-flow.md).
