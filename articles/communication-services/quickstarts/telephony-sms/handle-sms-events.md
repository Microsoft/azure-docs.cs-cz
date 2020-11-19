---
title: Rychlý Start – zpracování událostí SMS
titleSuffix: An Azure Communication Services quickstart
description: Naučte se zpracovávat události SMS pomocí komunikačních služeb Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e8decbe22f869573bf7a2221099d2bc30c00fc8a
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888840"
---
# <a name="quickstart-handle-sms-events"></a>Rychlý Start: zpracování událostí serveru SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Začínáme s komunikačními službami Azure pomocí Azure Event Grid k obsluze událostí služby SMS pro komunikaci. 

## <a name="about-azure-event-grid"></a>O Azure Event Grid

[Azure Event Grid](../../../event-grid/overview.md) je cloudová služba pro pokládání událostí. V tomto článku se dozvíte, jak se přihlásit k odběru událostí [služby Communication Service](../../concepts/event-handling.md)a aktivovat událost pro zobrazení výsledku. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. V tomto článku pošleme události do webové aplikace, která shromažďuje a zobrazuje zprávy.

## <a name="prerequisites"></a>Předpoklady
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Prostředek služby Azure Communication Service. Další podrobnosti najdete v rychlém startu [Vytvoření zdroje komunikace Azure](../create-communication-resource.md) .
- Telefonní číslo s povoleným SMS. [Získejte telefonní číslo](./get-phone-number.md).

## <a name="setting-up"></a>Nastavení

### <a name="enable-event-grid-resource-provider"></a>Povolit poskytovatele prostředků služby Event Grid

Pokud jste ve svém předplatném Azure dřív nepoužívali Event Grid, možná budete muset zaregistrovat Event Grid poskytovatele prostředků podle následujících kroků:

Na webu Azure Portal:

1. V nabídce vlevo vyberte **odběry** .
2. Vyberte předplatné, které používáte pro službu Event Grid.
3. V nabídce vlevo v části **Nastavení** vyberte **poskytovatelé prostředků**.
4. Vyhledejte **Microsoft.EventGrid**.
5. Pokud prostředek není zaregistrovaný, vyberte **Zaregistrovat**. 

Dokončení registrace může chvíli trvat. Stav můžete aktualizovat kliknutím na **Aktualizovat**. Jakmile se **Stav** změní na **Zaregistrováno**, můžete pokračovat.

### <a name="event-grid-viewer-deployment"></a>Nasazení Event Grid Vieweru

V tomto rychlém startu použijeme [ukázku Azure Event Grid Viewer](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) k zobrazení událostí téměř v reálném čase. Uživatel tak bude mít zkušenosti s informačním kanálem v reálném čase. Kromě toho by měly být k dispozici také datové části každé události pro kontrolu.  

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Přihlášení k odběru událostí SMS pomocí webhooků

Na portálu přejděte na prostředek služby Azure Communications, který jste vytvořili. V rámci prostředku služby komunikace vyberte **události** v levé nabídce stránky **komunikační služby** .

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="Snímek obrazovky s výběrem tlačítka pro odběr události v rámci stránky události prostředku":::

Klikněte na **přidat odběr události** a zadejte Průvodce vytvořením.

Na stránce **vytvořit odběr události** zadejte **název** odběru události.

Můžete se přihlásit k odběru konkrétních událostí a sdělit Event Grid, které události serveru SMS chcete sledovat a kam se mají události odesílat. Z rozevírací nabídky vyberte události, k jejichž odběru se chcete přihlásit. V případě serveru SMS máte možnost zvolit možnosti `SMS Received` a `SMS Delivery Report Received` . 

Pokud budete vyzváni k zadání **názvu systémového tématu**, můžete zadat jedinečný řetězec. Toto pole nemá žádný vliv na vaše prostředí a používá se pro účely interní telemetrie.

Podívejte se na úplný seznam událostí, které [podporuje komunikační služby Azure](../../concepts/event-handling.md).

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="Snímek obrazovky s informacemi o přijetí služby SMS a oznámením o doručení SMS přijal vybrané typy událostí.":::

Vyberte **webový Hook** pro **Typ koncového bodu**. 

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="Snímek obrazovky s polem typu koncového bodu nastaveným na webový Hook":::

V části **koncový bod** klikněte na **Vybrat koncový bod** a zadejte adresu URL vaší webové aplikace.

V tomto případě použijeme adresu URL z [ukázky Azure Event Grid Viewer](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) , kterou jsme nastavili dříve v rychlém startu. Adresa URL pro ukázku bude ve formátu: `https://{{site-name}}.azurewebsites.net/api/updates`

Pak vyberte **potvrdit výběr**.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Snímek obrazovky, který ukazuje potvrzení koncového bodu webového zavěšení":::

## <a name="viewing-sms-events"></a>Zobrazení událostí serveru SMS

### <a name="triggering-sms-events"></a>Aktivují se události SMS.

Chcete-li zobrazit triggery událostí, je nutné generovat události na prvním místě.

- `SMS Received` události jsou generovány, když telefonní číslo komunikační služby obdrží textovou zprávu. Pokud chcete aktivovat událost, stačí poslat zprávu z telefonu na telefonní číslo připojené k vašemu prostředku komunikační služby.
- `SMS Delivery Report Received` události jsou generovány při odeslání serveru SMS uživateli pomocí telefonního čísla komunikační služby. Chcete-li aktivovat událost, je nutné povolit možnost `Delivery Report` v možnostech [odeslaného serveru SMS](../telephony-sms/send.md). Zkuste poslat zprávu na telefon s `Delivery Report` . Po dokončení této akce dojde v účtu Azure k malým nákladům na několik centů nebo méně.

Podívejte se na úplný seznam událostí, které [podporuje komunikační služby Azure](../../concepts/event-handling.md).

### <a name="receiving-sms-events"></a>Příjem událostí serveru SMS

Jakmile dokončíte jednu akci výše, všimnete si, že `SMS Received` se `SMS Delivery Report Received` do vašeho koncového bodu odesílají události a. Tyto události se zobrazí v [ukázce Azure Event Grid Vieweru](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) , kterou jsme nastavili na začátku. Můžete stisknout ikonu oka vedle události a zobrazit celou datovou část. Události budou vypadat takto:

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="Snímek obrazovky znázorňující Event Grid schéma pro událost přijatou serverem SMS":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="Snímek obrazovky znázorňující Event Grid schéma pro událost sestavy doručení SMS.":::

Přečtěte si další informace o [schématech událostí a dalších konceptech událostí](../../concepts/event-handling.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak spotřebovávat události serveru SMS. Zprávy SMS můžete přijímat vytvořením předplatného Event Grid.

> [!div class="nextstepaction"] 
> [Odeslat SMS](../telephony-sms/send.md)

Můžete také chtít:

 - [Další informace o konceptech zpracování událostí](../../concepts/event-handling.md)
 - [Informace o Event Grid](../../../event-grid/overview.md)