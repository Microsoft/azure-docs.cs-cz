---
title: Rychlý Start – odesílání zpráv SMS v Azure Logic Apps pomocí komunikačních služeb Azure
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak odesílat zprávy SMS v Azure Logic Apps pracovních postupech pomocí konektoru služby Azure Communication Services.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 9ecb4d6c5c6701633606bf952c09063a8d96f8d1
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778935"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Rychlý Start: odesílání zpráv SMS v Azure Logic Apps s využitím komunikačních služeb Azure

Pomocí konektoru SMS a [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md) [služby Azure Communications Services](../../overview.md) můžete vytvářet automatizované pracovní postupy nebo *Logic Apps*, které mohou odesílat zprávy SMS. V tomto rychlém startu se dozvíte, jak automaticky posílat textové zprávy v reakci na aktivační událost, což je první krok v pracovním postupu aplikace logiky. Aktivační událostí může být příchozí e-mailová zpráva, plán opakování, událost [Azure Event Grid](../../../event-grid/overview.md) prostředku nebo jakákoli jiná [aktivační událost, kterou podporuje Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Snímek obrazovky zobrazující Azure Portal, který je otevřený v návrháři aplikace logiky, a ukazuje ukázkovou aplikaci logiky, která používá akci odeslat SMS pro konektor Azure Communication Services.":::

I když se tento rychlý Start zaměřuje na použití konektoru k reakci na Trigger, můžete konektor použít také k reakci na jiné akce, což jsou kroky, které následují po triggeru v pracovním postupu. Pokud Logic Apps teprve začínáte, přečtěte si téma [co je Azure Logic Apps](../../../logic-apps/logic-apps-overview.md) před zahájením práce.

> [!NOTE]
> Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným nebo si [vytvořte účet Azure zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Aktivní prostředek služby Azure Communication Services nebo [vytvořte prostředek služby Communications Services](../create-communication-resource.md).

- Aktivní prostředek Logic Apps (aplikace logiky) nebo [vytvořte prázdnou aplikaci logiky, ale s triggerem, který chcete použít](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). Konektor SMS komunikačních služeb v současné době poskytuje jenom akce, takže vaše aplikace logiky vyžaduje Trigger, a to minimálně.

  V tomto rychlém startu se používá aktivační událost **při přijetí nového e-mailu** , která je k dispozici v [konektoru Office 365 Outlook](/connectors/office365/).

- Telefonní číslo s povoleným SMS nebo [získat telefonní číslo](./get-phone-number.md).

## <a name="add-an-sms-action"></a>Přidat akci SMS

Pokud chcete přidat akci **Odeslat SMS** jako nový krok pracovního postupu pomocí konektoru služby Azure Communications Services, postupujte podle těchto kroků v [Azure Portal](https://portal.azure.com) s vaším pracovním postupem aplikace logiky otevřeným v návrháři aplikace logiky:

1. V návrháři v kroku, kam chcete přidat novou akci, vyberte **Nový krok**. Pokud chcete přidat novou akci mezi kroky, přesuňte ukazatel myši na šipku mezi těmito kroky, vyberte znaménko plus ( **+** ) a vyberte **přidat akci**.

1. Do vyhledávacího pole **Vybrat operaci** zadejte `Azure Communication Services` . V seznamu akce vyberte **Odeslat SMS**.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Snímek obrazovky zobrazující Azure Portal, který je otevřený v návrháři aplikace logiky, a ukazuje ukázkovou aplikaci logiky, která používá akci odeslat SMS pro konektor Azure Communication Services.":::

1. Nyní vytvořte připojení ke zdroji komunikačních služeb.

   1. Zadejte název připojení.

   1. Vyberte prostředek služby Azure Communication Services.

   1. Vyberte **Vytvořit**.

1. V akci **Odeslat zprávu SMS** zadejte následující informace: 

   * Zdrojové a cílové telefonní číslo. Pro účely testování můžete jako cílové telefonní číslo použít vlastní telefonní číslo.

   * Obsah zprávy, který chcete odeslat, například "Hello from Logic Apps!".

   Tady je akce **odeslání zprávy SMS** s příklady informací:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Snímek obrazovky zobrazující Azure Portal, který je otevřený v návrháři aplikace logiky, a ukazuje ukázkovou aplikaci logiky, která používá akci odeslat SMS pro konektor Azure Communication Services.":::

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

Dále spusťte aplikaci logiky pro testování.

## <a name="test-your-logic-app"></a>Testování aplikace logiky

Pokud chcete aplikaci logiky spustit ručně, na panelu nástrojů návrháře vyberte **Spustit**. Nebo můžete počkat na aktivaci vaší aplikace logiky. V obou případech by aplikace logiky měla poslat zprávu SMS na zadané cílové telefonní číslo. Další informace o spuštění vaší aplikace logiky najdete v tématu [Jak spustit aplikaci logiky](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat předplatné služby Communications Services, odstraňte prostředek nebo skupinu prostředků služby Communications Services. Odstraněním skupiny prostředků dojde také k odstranění všech dalších prostředků v této skupině. Další informace najdete v tématu [Postup vyčištění prostředků služby Communications Services](../create-communication-resource.md#clean-up-resources).

Pokud chcete vyčistit pracovní postup aplikace logiky a související prostředky, přečtěte si téma [Jak vyčistit Logic Apps prostředky](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak odesílat zprávy SMS pomocí Azure Logic Apps a komunikačních služeb Azure. Pokud se chcete dozvědět víc, pokračujte s přihlášením k odběru událostí SMS:

> [!div class="nextstepaction"]
> [Přihlášení k odběru událostí SMS](./handle-sms-events.md)

Další informace o SMS v komunikačních službách Azure najdete v těchto článcích:

- [Koncepce SMS](../../concepts/telephony-sms/concepts.md)
- [Plánování telefonie a řešení pro SMS](../../concepts/telephony-sms/plan-solution.md)
- [SADA SMS SDK](../../concepts/telephony-sms/sdk-features.md)
