---
title: Oznámení v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Umožňuje posílat oznámení uživatelům aplikací vytvořených na komunikačních službách Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a52188dc5058dbc74d3b03fba860b98540cd4a41
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608499"
---
# <a name="communication-services-notifications"></a>Oznámení služby Communication Services

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Chat komunikačních služeb Azure a volání klientských knihoven vytvoří kanál pro zasílání zpráv v reálném čase, který umožňuje efektivní a spolehlivé doručování zpráv do připojených klientů. Díky tomu můžete do svých aplikací vytvořit bohatou funkci komunikace v reálném čase, aniž byste museli implementovat složitou logiku dotazování HTTP. V mobilních aplikacích ale tento kanál signalizace zůstane připojený jenom v případě, že je vaše aplikace aktivní v popředí. Pokud chcete, aby uživatelé přijímali příchozí hovory nebo zprávy chatu, když je vaše aplikace na pozadí, měli byste použít nabízená oznámení.

Nabízená oznámení umožňují odesílat informace z vaší aplikace do mobilních zařízení uživatelů. Nabízená oznámení můžete použít k zobrazení dialogového okna, přehrání zvuku nebo zobrazení uživatelského rozhraní příchozího volání. Komunikační služby Azure poskytují integrace s [Azure Event Grid](../../event-grid/overview.md) a [Notification Hubs Azure](../../notification-hubs/notification-hubs-push-notification-overview.md) , které umožňují přidat do svých aplikací nabízená oznámení.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Aktivovat nabízená oznámení prostřednictvím Azure Event Grid

Komunikační služby Azure se integrují s [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) , aby poskytovaly oznámení událostí v reálném čase spolehlivým, škálovatelným a zabezpečeným způsobem. Tuto integraci můžete využít k vytvoření služby oznámení, která uživatelům poskytuje mobilní nabízená oznámení tím, že vytvoří odběr služby Event Grid, který aktivuje [funkci Azure Functions](../../azure-functions/functions-overview.md) nebo Webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagram znázorňující, jak se komunikační služby integrují s Event Grid.":::

Přečtěte si další informace o [zpracování událostí v komunikačních službách Azure](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Doručování nabízených oznámení prostřednictvím Azure Notification Hubs

Centrum oznámení Azure můžete připojit ke zdroji komunikačních služeb, aby bylo možné automaticky odesílat nabízená oznámení do mobilního zařízení uživatele, když obdrží příchozí volání. Tato nabízená oznámení byste měli použít k probuzení aplikace z uživatelského rozhraní na pozadí a zobrazovaného uživatelského rozhraní, které umožňuje uživateli přijmout nebo odmítnout volání. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagram znázorňující, jak se komunikační služby integrují do centra oznámení Azure.":::

Komunikační služby využívají Azure Notification hub jako předávací službu ke komunikaci s různými službami nabízených oznámení specifických pro konkrétní platformu pomocí rozhraní API pro [přímé odesílání](/rest/api/notificationhubs/direct-send) . Díky tomu můžete znovu použít stávající prostředky a konfigurace centra oznámení Azure pro zajištění nízké latence a spolehlivého volání oznámení vašim aplikacím.

> [!NOTE]
> V současné době je podporována pouze nabízená oznámení volání.

### <a name="notification-hub-provisioning"></a>Zřizování centra oznámení 

Pokud chcete odesílat nabízená oznámení do klientských zařízení pomocí Notification Hubs, [vytvořte centrum oznámení](../../notification-hubs/create-notification-hub-portal.md) v rámci stejného předplatného jako prostředek služby Communications. Pro službu oznámení platformy, kterou chcete použít, musí být služba Azure Notification Hubs nakonfigurovaná. Informace o tom, jak v klientské aplikaci získat nabízená oznámení z Notification Hubs, najdete v tématu [Začínáme s Notification Hubs](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) a v rozevíracím seznamu v horní části stránky vyberte cílovou klientskou platformu.

> [!NOTE]
> V současné době jsou podporovány platformy APNs a FCM.  
U platformy APNs je potřeba nakonfigurovat režim ověřování tokenu. Režim ověřování certifikátu není nyní podporován. 

Jakmile je vaše centrum oznámení nakonfigurované, můžete ho přidružit k vašemu prostředku komunikačních služeb zadáním připojovacího řetězce pro centrum pomocí klienta Azure Resource Manager nebo prostřednictvím Azure Portal. Připojovací řetězec by měl obsahovat oprávnění Send (Odeslat). Doporučujeme vytvořit další zásadu přístupu s oprávněním Send jenom pro vaše centrum. Další informace o [Notification Hubs zásadách zabezpečení a přístupu](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>Konfigurace centra oznámení pomocí klienta Azure Resource Manager

Pokud se chcete přihlásit Azure Resource Manager, spusťte následující příkaz a přihlaste se pomocí svých přihlašovacích údajů.

```console
armclient login
```

 Po úspěšném přihlášení ke zřízení Centra oznámení proveďte následující kroky:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>Konfigurace centra oznámení pomocí Azure Portal

Na portálu přejděte do svého prostředku služby Azure Communication Services. V rámci prostředku služby Communications Services vyberte nabízená oznámení v levé nabídce stránky komunikační služby a připojte centrum oznámení, které jste zřídili dříve. Sem budete muset zadat připojovací řetězec a ID prostředku:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Snímek obrazovky s nastavením nabízených oznámení na webu Azure Portal":::

> [!NOTE]
> Pokud je připojovací řetězec centra oznámení Azure aktualizovaný, je nutné aktualizovat také prostředek komunikačních služeb.  
Jakákoli změna způsobu propojení rozbočovače se projeví v rovině dat (tj. při posílání oznámení) během maximální doby v ``10`` minutách. To platí také v případě, že je centrum propojeno poprvé, **Pokud** se předtím poslala nějaká oznámení.

#### <a name="device-registration"></a>Registrace zařízení 

Další informace o registraci popisovače zařízení pomocí komunikačních služeb najdete v tématu [rychlý Start volajícího](../quickstarts/voice-video-calling/getting-started-with-calling.md) .

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](../../event-grid/overview.md)
* Další informace o konceptech centra oznámení Azure najdete v tématu [dokumentace k azure Notification Hubs](../../notification-hubs/index.yml) .