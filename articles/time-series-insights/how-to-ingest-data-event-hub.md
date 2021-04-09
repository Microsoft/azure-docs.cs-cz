---
title: Přidat zdroj události Event Hubs – Azure Time Series Insights | Microsoft Docs
description: Naučte se, jak do prostředí Azure Time Series Insights přidat zdroj událostí Azure Event Hubs.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: ee66e68216933c410092865a1cdb781476a944c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461129"
---
# <a name="add-an-event-hub-event-source-to-your-azure-time-series-insights-environment"></a>Přidání zdroje událostí centra událostí do prostředí Azure Time Series Insights

Tento článek popisuje, jak pomocí Azure Portal přidat zdroj událostí, který čte data z Azure Event Hubs do vašeho Azure Time Series Insights prostředí.

> [!NOTE]
> Postup, který je popsaný v tomto článku, platí jak pro prostředí Azure Time Series Insights Gen 1 a Azure Time Series Insights Gen 2.

## <a name="prerequisites"></a>Požadavky

- Vytvořte Azure Time Series Insights prostředí, jak je popsáno v tématu [vytvoření Azure Time Series Insights prostředí](./tutorial-set-up-environment.md).
- Vytvořte centrum událostí. Přečtěte si téma [Vytvoření oboru názvů Event Hubs a centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md).
- Centrum událostí musí mít odeslané aktivní události zpráv. Naučte se [odesílat události do Azure Event Hubs pomocí .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Vytvořte vyhrazenou skupinu uživatelů v centru událostí, ze které může prostředí Azure Time Series Insights spotřebovat. Každý Azure Time Series Insights zdroj události musí mít svou vlastní vyhrazenou skupinu uživatelů, která není sdílená s žádným jiným příjemcem. Pokud více čtenářů spotřebovává události ze stejné skupiny příjemců, můžou se všechny čtenáři projevit při selhání. V každém centru událostí je limit 20 skupin uživatelů. Podrobnosti najdete v tématu [Průvodce programováním pro Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Přidání skupiny příjemců do centra událostí

Aplikace používají skupiny uživatelů k vyžádání dat z Azure Event Hubs. Pokud chcete spolehlivě číst data z centra událostí, poskytněte vyhrazenou skupinu uživatelů, kterou používá jenom toto Azure Time Series Insights prostředí.

Přidání nové skupiny příjemců do centra událostí:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete instanci centra událostí z podokna **přehledu** oboru názvů centra událostí. Vyberte **entity > Event Hubs** nebo Najděte instanci pod **názvem**.

    [![Otevřete obor názvů centra událostí.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. V instanci centra událostí vyberte **entity > skupiny uživatelů**. Pak vyberte **+ Skupina uživatelů** a přidejte novou skupinu příjemců.

   [![Centrum událostí – přidat skupinu příjemců](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   V opačném případě vyberte existující skupinu uživatelů a přejděte k další části.

1. Na stránce **skupiny příjemců** zadejte novou jedinečnou hodnotu pro **název**.  Stejný název použijte při vytváření nového zdroje událostí v prostředí Azure Time Series Insights.

1. Vyberte **Vytvořit**.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj události

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující Azure Time Series Insights prostředí. V nabídce vlevo vyberte **všechny prostředky** a pak vyberte prostředí Azure Time Series Insights.

1. Vyberte **zdroje událostí** a pak vyberte **Přidat**.

   [![V části zdroje událostí vyberte tlačítko Přidat.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Zadejte hodnotu pro **název zdroje události** , která je jedinečná pro toto Azure Time Series Insights prostředí, například `Contoso-TSI-Gen 1-Event-Hub-ES` .

1. V případě **zdroje** vyberte **centrum událostí**.

1. Vyberte odpovídající hodnoty pro **možnost importovat**:

   - Pokud máte existující centrum událostí v jednom z vašich předplatných, vyberte **použít centrum událostí z dostupných předplatných**. Tato možnost představuje nejjednodušší přístup.

     [![Vyberte možnost importu zdroje událostí.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

   - V následující tabulce jsou popsány požadované vlastnosti pro možnost **použít centrum událostí z dostupných předplatných** :

       [![Podrobnosti o předplatném a centru událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Vlastnost | Popis |
       | --- | --- |
       | Předplatné | Předplatné, ke kterému patří požadovaná instance centra událostí a obor názvů. |
       | Obor názvů centra událostí | Obor názvů centra událostí, ke kterému patří požadovaná instance centra událostí |
       | Název centra událostí | Název požadované instance centra událostí. |
       | Hodnota zásad centra událostí | Vyberte požadované zásady sdíleného přístupu. Zásadu sdíleného přístupu můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj události *musí* mít oprávnění **ke čtení** . |
       | Klíč zásad centra událostí | Hodnota předem vyplněná z vybrané hodnoty zásad centra událostí |

   - Pokud je centrum událostí externí pro vaše předplatná nebo pokud chcete vybrat rozšířené možnosti, vyberte **zadat nastavení centra událostí ručně**.

       V následující tabulce jsou popsány požadované vlastnosti pro možnost **zadat nastavení centra událostí ručně** :

       | Vlastnost | Popis |
       | --- | --- |
       | ID předplatného | Předplatné, ke kterému patří požadovaná instance centra událostí a obor názvů. |
       | Skupina prostředků | Skupina prostředků, ke které patří požadovaná instance centra událostí a obor názvů |
       | Obor názvů centra událostí | Obor názvů centra událostí, ke kterému patří požadovaná instance centra událostí |
       | Název centra událostí | Název požadované instance centra událostí. |
       | Hodnota zásad centra událostí | Vyberte požadované zásady sdíleného přístupu. Zásadu sdíleného přístupu můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj události *musí* mít oprávnění **ke čtení** . |
       | Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Service Bus. Sem zadejte primární nebo sekundární klíč. |

   - Obě možnosti sdílejí následující možnosti konfigurace:

       | Vlastnost | Popis |
       | --- | --- |
       | Skupina uživatelů centra událostí | Skupina příjemců, která čte události z centra událostí. Důrazně doporučujeme, abyste pro zdroj událostí používali vyhrazenou skupinu uživatelů. |
       | Formát serializace události | V současné době je JSON jediným dostupným formátem serializace. Zprávy událostí musí být v tomto formátu nebo data nelze číst. |
       | Název vlastnosti časového razítka | Chcete-li zjistit tuto hodnotu, je třeba porozumět formátu zprávy pro data zprávy, která jsou odeslána do centra událostí. Tato hodnota je **název** konkrétní vlastnosti události v datech zprávy, kterou chcete použít jako časové razítko události. V hodnotě se rozlišují malá a velká písmena. Pokud je ponecháno prázdné, použije se čas zařazení do **fronty události** ve zdroji události jako časové razítko události. |

1. Přidejte název vyhrazené Azure Time Series Insights skupiny uživatelů, který jste přidali do centra událostí.

1. Vyberte **Vytvořit**.

   Po vytvoření zdroje události Azure Time Series Insights automaticky zahájí streamování dat do vašeho prostředí.

## <a name="next-steps"></a>Další kroky

- [Definováním zásad přístupu k datům](./concepts-access-policies.md) Zabezpečte data.

- [Odešle události](time-series-insights-send-events.md) do zdroje událostí.

- Přístup k prostředí v [průzkumníkovi Azure Time Series Insights](https://insights.timeseries.azure.com).
