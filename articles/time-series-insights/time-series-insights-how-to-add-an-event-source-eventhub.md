---
title: Přidat zdroj události Event Hubs do Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak do prostředí Time Series Insights přidat zdroj událostí, který je připojený k Azure Event Hubs.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: 3adf8b71d264b01f13f5aac7002b7ec455a31d60
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990068"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Přidání zdroje událostí centra událostí do prostředí Time Series Insights

Tento článek popisuje, jak pomocí Azure Portal přidat zdroj událostí, který čte data z Azure Event Hubs do vašeho Azure Time Series Insights prostředí.

> [!NOTE]
> Postup, který je popsaný v tomto článku, platí jak pro prostředí Time Series Insights GA a Time Series Insights ve verzi Preview.

## <a name="prerequisites"></a>Požadavky

- Vytvořte Time Series Insights prostředí, jak je popsáno v tématu [vytvoření Azure Time Series Insights prostředí](./time-series-insights-update-create-environment.md).
- Vytvořte centrum událostí. Přečtěte si téma [Vytvoření oboru názvů Event Hubs a centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md).
- Centrum událostí musí mít odeslané aktivní události zpráv. Naučte se [odesílat události do Azure Event Hubs pomocí .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Vytvořte vyhrazenou skupinu uživatelů v centru událostí, ze které může prostředí Time Series Insights spotřebovat. Každý Time Series Insights zdroj události musí mít svou vlastní vyhrazenou skupinu uživatelů, která není sdílená s žádným jiným příjemcem. Pokud více čtenářů spotřebovává události ze stejné skupiny příjemců, budou se jim nejspíš zobrazovat chyby. V každém centru událostí je limit 20 skupin uživatelů. Podrobnosti najdete v tématu [Průvodce programováním v Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Přidání skupiny příjemců do centra událostí

Aplikace používají skupiny uživatelů k vyžádání dat z Azure Event Hubs. Pokud chcete spolehlivě číst data z centra událostí, poskytněte vyhrazenou skupinu uživatelů, kterou používá jenom toto Time Series Insights prostředí.

Přidání nové skupiny příjemců do centra událostí:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete centrum událostí z oboru názvů centra událostí.

    [![otevřít obor názvů centra událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. V části **entity**vyberte **skupiny uživatelů**a pak vyberte **Skupina uživatelů**.

   [centrum událostí ![– přidat skupinu příjemců](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. Na stránce **skupiny příjemců** zadejte novou jedinečnou hodnotu pro **název**.  Stejný název použijte při vytváření nového zdroje událostí v prostředí Time Series Insights.

1. Vyberte **Vytvořit**.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj události

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující Time Series Insights prostředí. V nabídce vlevo vyberte **všechny prostředky**a pak vyberte prostředí Time Series Insights.

1. V části **topologie prostředí**vyberte **zdroje událostí**a pak vyberte **Přidat**.

   [![v části zdroje událostí vyberte tlačítko Přidat.](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. Zadejte hodnotu pro **název zdroje události** , která je jedinečná pro toto Time Series Insights prostředí, jako je například **Event-Stream**.

1. V případě **zdroje**vyberte **centrum událostí**.

1. Vyberte odpovídající hodnoty pro **možnost importovat**:

   * Pokud máte existující centrum událostí v jednom z vašich předplatných, vyberte **použít centrum událostí z dostupných předplatných**. Tato možnost představuje nejjednodušší přístup.

     [![výběr možnosti importu zdroje událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  V následující tabulce jsou popsány požadované vlastnosti pro možnost **použít centrum událostí z dostupných předplatných** :

       [Podrobnosti o ![předplatného a centra událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | Vlastnost | Popis |
       | --- | --- |
       | Předplatné | Předplatné, ke kterému patří požadovaná instance centra událostí a obor názvů. |
       | Obor názvů centra událostí | Obor názvů centra událostí, ke kterému patří požadovaná instance centra událostí |
       | Název centra událostí | Název požadované instance centra událostí. |
       | Hodnota zásad centra událostí | Vyberte požadované zásady sdíleného přístupu. Zásadu sdíleného přístupu můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj události *musí* mít oprávnění **ke čtení** . |
       | Klíč zásad centra událostí | Hodnota předem vyplněná z vybrané hodnoty zásad centra událostí |

    * Pokud je centrum událostí externí pro vaše předplatná nebo pokud chcete vybrat rozšířené možnosti, vyberte **zadat nastavení centra událostí ručně**.

       V následující tabulce jsou popsány požadované vlastnosti pro možnost **zadat nastavení centra událostí ručně** :
 
       | Vlastnost | Popis |
       | --- | --- |
       | ID předplatného | Předplatné, ke kterému patří požadovaná instance centra událostí a obor názvů. |
       | Skupina prostředků | Skupina prostředků, ke které patří požadovaná instance centra událostí a obor názvů |
       | Obor názvů centra událostí | Obor názvů centra událostí, ke kterému patří požadovaná instance centra událostí |
       | Název centra událostí | Název požadované instance centra událostí. |
       | Hodnota zásad centra událostí | Vyberte požadované zásady sdíleného přístupu. Zásadu sdíleného přístupu můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj události *musí* mít oprávnění **ke čtení** . |
       | Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Service Bus. Sem zadejte primární nebo sekundární klíč. |

    * Obě možnosti sdílejí následující možnosti konfigurace:

       | Vlastnost | Popis |
       | --- | --- |
       | Skupina uživatelů centra událostí | Skupina příjemců, která čte události z centra událostí. Důrazně doporučujeme, abyste pro zdroj událostí používali vyhrazenou skupinu uživatelů. |
       | Formát serializace události | V současné době je JSON jediným dostupným formátem serializace. Zprávy událostí musí být v tomto formátu nebo data nelze číst. |
       | Název vlastnosti časového razítka | Chcete-li zjistit tuto hodnotu, je třeba porozumět formátu zprávy pro data zprávy, která jsou odeslána do centra událostí. Tato hodnota je **název** konkrétní vlastnosti události v datech zprávy, kterou chcete použít jako časové razítko události. V hodnotě se rozlišují malá a velká písmena. Pokud je ponecháno prázdné, použije se čas zařazení do **fronty události** ve zdroji události jako časové razítko události. |

1. Přidejte název vyhrazené Time Series Insights skupiny uživatelů, který jste přidali do centra událostí.

1. Vyberte **Vytvořit**.

   Po vytvoření zdroje události Time Series Insights automaticky zahájí streamování dat do vašeho prostředí.

## <a name="next-steps"></a>Další kroky

* [Definováním zásad přístupu k datům](time-series-insights-data-access.md) Zabezpečte data.

* [Odešle události](time-series-insights-send-events.md) do zdroje událostí.

* Přístup k prostředí v [průzkumníkovi Time Series Insights](https://insights.timeseries.azure.com).
