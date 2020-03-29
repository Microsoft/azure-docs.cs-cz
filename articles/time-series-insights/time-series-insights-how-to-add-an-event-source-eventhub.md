---
title: Přidání zdroje událostí Centra událostí – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Přečtěte si, jak přidat zdroj událostí centra Azure Event Hubs do prostředí Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: c3b06289ba6ce98d4307a8255981ecdba069fdfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905405"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Přidání zdroje událostí centra událostí do prostředí Time Series Insights

Tento článek popisuje, jak pomocí portálu Azure přidat zdroj událostí, který čte data z Centra událostí Azure do prostředí Azure Time Series Insights.

> [!NOTE]
> Kroky popsané v tomto článku platí jak pro prostředí Time Series Insights GA, tak pro prostředí Náhled přehledů časové řady.

## <a name="prerequisites"></a>Požadavky

- Vytvořte prostředí Time Series Insights, jak je popsáno v [prostředí Create a Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Vytvořte centrum událostí. Přečtěte si [vytvoření oboru názvů Event Hubs a centra událostí pomocí portálu Azure](../event-hubs/event-hubs-create.md).
- Centrum událostí musí mít aktivní události zprávy odeslané do něj. Zjistěte, jak [odesílat události do centra událostí Azure pomocí rozhraní .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Vytvořte vyhrazenou skupinu spotřebitelů v centru událostí, ze kterého může prostředí Time Series Insights využívat. Každý zdroj událostí Time Series Insights musí mít vlastní vyhrazenou skupinu spotřebitelů, která není sdílena s žádným jiným spotřebitelem. Pokud více čtenářů spotřebovávají události ze stejné skupiny spotřebitelů, všechny čtenáři pravděpodobně vykazují chyby. V centru událostí je limit 20 skupin spotřebitelů. Podrobnosti naleznete v [průvodci programováním centra událostí](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Přidání skupiny spotřebitelů do centra událostí

Aplikace používají skupiny spotřebitelů k vytahování dat z Centra událostí Azure. Chcete-li spolehlivě číst data z centra událostí, zadejte vyhrazenou skupinu spotřebitelů, která se používá pouze v tomto prostředí Time Series Insights.

Přidání nové skupiny spotřebitelů do centra událostí:

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a otevřete instanci centra událostí v podokně **Přehled** oboru názvů centra událostí. Vyberte **Entity > centra událostí** nebo vyhledejte svou instanci v části **Název**.

    [![Otevření oboru názvů centra událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. V instanci centra událostí vyberte **Entity > skupiny spotřebitelů**. Potom vyberte **+ Skupina spotřebitelů** a přidejte novou skupinu spotřebitelů. 

   [![Centrum událostí – přidání skupiny spotřebitelů](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   V opačném případě vyberte existující skupinu spotřebitelů a přejděte k další části.

1. Na stránce **Skupiny spotřebitelů** zadejte novou jedinečnou hodnotu pro **Název**.  Tento stejný název použijte při vytváření nového zdroje událostí v prostředí Time Series Insights.

1. Vyberte **Vytvořit**.

## <a name="add-a-new-event-source"></a>Přidání nového zdroje událostí

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyhledejte stávající prostředí Time Series Insights. V levé nabídce vyberte **Všechny prostředky**a pak vyberte prostředí Time Series Insights.

1. Vyberte **Zdroje událostí**a pak vyberte **Přidat**.

   [![V části Zdroje událostí vyberte tlačítko Přidat.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Zadejte hodnotu názvu **zdroje událostí,** která je jedinečná `Contoso-TSI-GA-Event-Hub-ES`pro toto prostředí Time Series Insights, například .

1. V **pouzdřovaném zdroji**vyberte **Centrum událostí**.

1. Vyberte příslušné hodnoty pro **volbu Import**:

   * Pokud máte v jednom z předplatných existující centrum událostí, vyberte **Použít centrum událostí z dostupných předplatných**. Tato možnost je nejjednodušší přístup.

     [![Vybrat možnost importu zdroje událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  Následující tabulka popisuje požadované vlastnosti centra **Use Event Hub z dostupných předplatných:**

       [![Podrobnosti o předplatném a centru událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Vlastnost | Popis |
       | --- | --- |
       | Předplatné | Předplatné patří k požadované instanci centra událostí a oboru názvů. |
       | Obor názvů centra událostí | Obor názvů centra událostí, do který patří požadovaná instance centra událostí. |
       | Název centra událostí | Název instance centra událostí požadované události. |
       | Hodnota zásad centra událostí | Vyberte požadovanou zásadu sdíleného přístupu. Zásady sdíleného přístupu můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro zdroj událostí *musí* mít oprávnění **ke čtení.** |
       | Klíč zásad Centra událostí | Předem vyplněno z vybrané hodnoty zásad centra událostí. |

    * Pokud je centrum událostí externí pro vaše předplatná nebo chcete vybrat upřesňující možnosti, vyberte **ručně poskytněte nastavení Centra událostí**.

       Následující tabulka popisuje požadované vlastnosti pro možnost **Poskytnout nastavení centra událostí ručně:**
 
       | Vlastnost | Popis |
       | --- | --- |
       | ID předplatného | Předplatné patří k požadované instanci centra událostí a oboru názvů. |
       | Skupina prostředků | Skupina prostředků, do které patří požadovaná instance centra událostí a obor názvů. |
       | Obor názvů centra událostí | Obor názvů centra událostí, do který patří požadovaná instance centra událostí. |
       | Název centra událostí | Název instance centra událostí požadované události. |
       | Hodnota zásad centra událostí | Vyberte požadovanou zásadu sdíleného přístupu. Zásady sdíleného přístupu můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro zdroj událostí *musí* mít oprávnění **ke čtení.** |
       | Klíč zásad Centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Service Bus. Zde zadejte primární nebo sekundární klíč. |

    * Obě možnosti sdílejí následující možnosti konfigurace:

       | Vlastnost | Popis |
       | --- | --- |
       | Skupina uživatelů centra událostí | Skupina spotřebitelů, která čte události z centra událostí. Důrazně doporučujeme použít vyhrazenou skupinu spotřebitelů pro zdroj událostí. |
       | Formát serializace události | V současné době json je pouze k dispozici serializace formát. Zprávy událostí musí být v tomto formátu nebo data nelze číst. |
       | Název vlastnosti časového razítka | Chcete-li zjistit tuto hodnotu, musíte pochopit formát zprávy dat zpráv, která je odeslána do centra událostí. Tato hodnota je **název** vlastnosti konkrétní události v datech zprávy, která chcete použít jako časové razítko události. Hodnota rozlišuje malá a velká písmena. Pokud je ponecháno prázdné, **čas zařazení události do fronty** ve zdroji událostí se používá jako časové razítko události. |

1. Přidejte název skupiny zákazníků Time Series Insights, který jste přidali do centra událostí.

1. Vyberte **Vytvořit**.

   Po vytvoření zdroje událostí Time Series Insights automaticky spustí streamování dat do vašeho prostředí.

## <a name="next-steps"></a>Další kroky

* [Definujte zásady přístupu k datům](time-series-insights-data-access.md) pro zabezpečení dat.

* [Odešlete události](time-series-insights-send-events.md) do zdroje událostí.

* Přístup k vašemu prostředí v [průzkumníku Time Series Insights](https://insights.timeseries.azure.com).
