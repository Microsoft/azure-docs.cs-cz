---
title: Volání aplikací logiky z Power Automate a Power Apps
description: Vyvolejte aplikace logiky od Microsoftu pro automatizaci toků tím, že budete exportovat aplikace logiky jako konektory.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91762455"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Volání aplikací logiky z Power Automate a Power Apps

Chcete-li volat aplikace logiky od společnosti Microsoft pro automatizaci a aplikace Microsoft Power Apps, můžete exportovat aplikace logiky jako konektory. Když vystavíte aplikaci logiky jako vlastní konektor v prostředí Power automat nebo Power Apps, můžete aplikaci logiky volat z toků.

Pokud chcete migrovat tok z Power Automate nebo Power Logic Apps, přečtěte si téma [Export toků z automatizace napájení a nasazení do Azure Logic Apps](export-from-microsoft-flow-logic-app-template.md).

> [!NOTE]
> V Azure Logic Apps nejsou k dispozici všechny konektory Power Automate. Můžete migrovat pouze toky Power Automate, které mají ekvivalentní konektory v Azure Logic Apps. Například Trigger tlačítka, konektor pro schvalování a konektor oznámení jsou specifické pro automatizaci. V současné době se toky založené na OpenAPI v Power automatu nepodporují pro export a nasazení jako šablony aplikace logiky.
>
> * Pokud chcete zjistit, které napájecí konektory nemají Logic Apps ekvivalenty, přečtěte si téma [Automatizace konektorů](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Pokud chcete zjistit, které Logic Apps konektory nemají ekvivalenty vypnutí napájení, přečtěte si téma [Logic Apps konektory](/connectors/connector-reference/connector-reference-powerautomate-connectors).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Licence Power automatizuje nebo Power Apps.

* Aplikace logiky s triggerem žádosti pro export.

* Tok v Power automatu nebo Power, ze kterého chcete zavolat aplikaci logiky.

## <a name="export-your-logic-app-as-a-custom-connector"></a>Export aplikace logiky jako vlastního konektoru

Než budete moct aplikaci logiky volat z Power Automata nebo Power Apps, musíte nejdřív exportovat aplikaci logiky jako vlastní konektor.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Do vyhledávacího pole Azure Portal zadejte `Logic Apps` . Ve výsledcích vyberte v části **služby**možnost **Logic Apps**.

1. Vyberte aplikaci logiky, kterou chcete exportovat.

1. V nabídce vaší aplikace logiky vyberte **exportovat**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Snímek stránky aplikace logiky v Azure Portal, zobrazení nabídky s vybraným tlačítkem exportovat":::

1. V podokně **Export** pro **název**zadejte název vlastního konektoru do vaší aplikace logiky. V seznamu **prostředí** vyberte prostředí Power automatizuje nebo Power Apps, ze kterého chcete zavolat aplikaci logiky. Až to bude hotové, vyberte **OK**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Snímek stránky aplikace logiky v Azure Portal, zobrazení nabídky s vybraným tlačítkem exportovat":::

1. Pokud chcete potvrdit, že se aplikace logiky úspěšně exportovali, zkontrolujte podokno oznámení.

### <a name="exporting-errors"></a>Exportování chyb

Tady jsou chyby, ke kterým může dojít při exportování aplikace logiky jako vlastního konektoru a jejich navrhovaných řešení:

* **Nepovedlo se získat prostředí. Ujistěte se, že je váš účet nakonfigurovaný pro Power automat, a zkuste to znovu.** zkontrolujte, jestli má váš účet Azure plán automatizovaného automatizace.

* **Aktuální aplikaci logiky nejde exportovat. Pokud chcete exportovat, vyberte aplikaci logiky, která má aktivační událost žádosti.**: Ověřte, že vaše aplikace logiky začíná [triggerem požadavku](./logic-apps-workflow-actions-triggers.md#request-trigger).

## <a name="connect-to-your-logic-app-from-power-automate"></a>Připojte se k vaší aplikaci logiky z automatizace napájení

Pokud se chcete připojit k aplikaci logiky, kterou jste exportovali s vaším tokem Power Automate:

1. Přihlaste se k [Power](https://flow.microsoft.com)automatu.

1. V nabídce domovské stránky **Automatizace napájení** vyberte **Moje toky**.

1. Na stránce **toky** vyberte tok, který chcete připojit k aplikaci logiky.

1. V nabídce stránky Flow vyberte **Upravit**.

1. V editoru toků vyberte **&#43; nový krok**.

1. V části **zvolit akci**zadejte do vyhledávacího pole název konektoru aplikace logiky. Pokud chcete ve svém prostředí zobrazit jenom vlastní konektory, vyfiltrujte výsledky tak, že vyberete **vlastní** kartu.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Snímek stránky aplikace logiky v Azure Portal, zobrazení nabídky s vybraným tlačítkem exportovat":::

1. Vyberte akci, kterou chcete provést s vaším konektorem aplikace logiky. 

1. Zadejte informace, které akce předá do konektoru aplikace logiky.

1. Pokud chcete změny uložit, vyberte v nabídce editor automatizace napájení možnost **Uložit**.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Ve službě Logic Apps Najděte aplikaci logiky, kterou jste exportovali.

1. Ujistěte se, že vaše aplikace logiky funguje způsobem, který jste očekávali v toku automatizace napájení.

## <a name="delete-logic-app-connector-from-power-automate"></a>Odstranění konektoru aplikace logiky z Power Automate

1. Přihlaste se k [Power](https://flow.microsoft.com)automatu.

1. Na domovské stránce **Power** Automata vyberte **data** &gt; **vlastní konektory** v nabídce.

1. V seznamu najděte vlastní konektor a vyberte tlačítko se třemi tečkami (**...**) &gt; **Odstranit**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Snímek stránky aplikace logiky v Azure Portal, zobrazení nabídky s vybraným tlačítkem exportovat":::

1. Odstranění potvrďte tak, že vyberete **OK**.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Připojení k aplikaci logiky z Power Apps

Pokud se chcete připojit k aplikaci logiky, kterou jste exportovali s vaším tokem Power Apps:

1. Přihlaste se k [Power Apps](https://powerapps.microsoft.com/).

1. Na domovské stránce **Power Apps** vyberte v nabídce **toky** .

1. Na stránce **toky** vyberte tok, který chcete připojit k aplikaci logiky.

1. Na stránce flow v nabídce toku vyberte **Upravit** .

1. V editoru toků vyberte tlačítko **&#43; nový krok** .

1. V části **zvolit akci** v novém kroku zadejte do vyhledávacího pole název vašeho konektoru aplikace logiky. Podle potřeby můžete filtrovat výsledky podle **vlastní** karty, aby se zobrazily pouze vlastní konektory ve vašem prostředí.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Snímek stránky aplikace logiky v Azure Portal, zobrazení nabídky s vybraným tlačítkem exportovat":::

1. Vyberte akci, kterou chcete s konektorem provést. 

1. Nakonfigurujte, jaké informace vaše akce předá do konektoru aplikace logiky.

1. V nabídce editor Power Apps vyberte **Uložit** a uložte provedené změny. 

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Ve službě Logic Apps Najděte aplikaci logiky, kterou jste exportovali.

1. Ověřte, že vaše aplikace logiky funguje tak, jak má váš tok Power Apps.

## <a name="delete-logic-app-connector-from-power-apps"></a>Odstranění konektoru aplikace logiky z Power Apps

1. Přihlaste se k [Power Apps](https://powerapps.microsoft.com).

1. Na domovské stránce **Power Apps** vyberte **Data** &gt; v nabídce **vlastní konektory** dat.

1. V seznamu najděte vlastní konektor a vyberte tlačítko se třemi tečkami (**...**) &gt; **Odstranit**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Snímek stránky aplikace logiky v Azure Portal, zobrazení nabídky s vybraným tlačítkem exportovat":::

1. Odstranění potvrďte tak, že vyberete **OK**.

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech pro Azure Logic Apps](../connectors/apis-list.md)
* Další informace o [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
