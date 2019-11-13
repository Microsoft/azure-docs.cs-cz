---
title: Přidat zdroj události Event Hubs – Azure Time Series Insights | Microsoft Docs
description: Naučte se, jak do prostředí Time Series Insights přidat zdroj událostí Azure Event Hubs.
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
ms.openlocfilehash: 421a4635a80c5a7a45fb14bf900c205a06789279
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012626"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Přidání zdroje událostí centra událostí do prostředí Time Series Insights

Tento článek popisuje postup přidání zdroje událostí, která čte data z Azure Event Hubs do prostředí Azure Time Series Insights pomocí webu Azure portal.

> [!NOTE]
> Postup, který je popsaný v tomto článku, platí jak pro prostředí Time Series Insights GA a Time Series Insights ve verzi Preview.

## <a name="prerequisites"></a>Požadavky

- Vytvořte Time Series Insights prostředí, jak je popsáno v tématu [vytvoření Azure Time Series Insights prostředí](./time-series-insights-update-create-environment.md).
- Vytvořte centrum událostí. Přečtěte si téma [Vytvoření oboru názvů Event Hubs a centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md).
- Centrum událostí, musíte mít aktivní zprávě události do něj odesílají. Naučte se [odesílat události do Azure Event Hubs pomocí .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Vytvořte vyhrazenou skupinu spotřebitelů v Centru událostí, které využívají z prostředí Time Series Insights. Každý zdroj událostí Time Series Insights musí mít svůj vlastní vyhrazenou skupinu spotřebitelů, který není sdílený s další příjemce. Pokud více čtenářům přijímat události z stejnou skupinu uživatelů, jsou pravděpodobně uvidíte selhání všechny nástroje pro čtení. Platí omezení 20 skupin uživatelů na Centrum událostí. Podrobnosti najdete v tématu [programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Přidat skupinu uživatelů do vašeho centra událostí

Aplikace používat skupiny příjemců k získání dat z Azure Event Hubs. Pokud chcete spolehlivě číst data z centra událostí, poskytněte vyhrazenou skupinu uživatelů, kterou používá jenom toto Time Series Insights prostředí.

Chcete-li přidat novou skupinu uživatelů ve službě event hub:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete centrum událostí z oboru názvů centra událostí.

    [![otevřít obor názvů centra událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. V části **entity**vyberte **skupiny příjemců**a pak vyberte **skupinu příjemců**.

   [centrum událostí ![– přidat skupinu příjemců](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. Na **skupiny příjemců** stránky, zadejte novou jedinečnou hodnotu pro **název**.  Když vytvoříte nový zdroj událostí do prostředí Time Series Insights, použijte tento stejný název.

1. Vyberte **Vytvořit**.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).

1. Vyhledejte existující prostředí Time Series Insights. V nabídce vlevo vyberte **všechny prostředky**a pak vyberte vaše prostředí Time Series Insights.

1. V části **topologie prostředí**vyberte **zdroje událostí**a pak vyberte **přidat**.

   [![v části zdroje událostí vyberte tlačítko Přidat.](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. Zadejte hodnotu pro **název zdroje událostí** , který je jedinečný pro toto prostředí Time Series Insights, jako například **datového proudu událostí**.

1. Pro **zdroj**vyberte **centra událostí**.

1. Vyberte příslušné hodnoty pro **možnost importu**:

   * Pokud máte existující centrum událostí v jednom z vašich předplatných, vyberte **použijte Centrum událostí z dostupných předplatných**. Tato možnost je nejjednodušší přístup.

     [![výběr možnosti importu zdroje událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  V následující tabulce jsou popsány požadované vlastnosti pro **použijte Centrum událostí z dostupných předplatných** možnost:

       [Podrobnosti o ![předplatného a centra událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | Vlastnost | Popis |
       | --- | --- |
       | Předplatné | Předplatné, ke kterému patří požadovaná instance centra událostí a obor názvů. |
       | Obor názvů centra událostí | Obor názvů centra událostí, ke kterému patří požadovaná instance centra událostí |
       | Název centra událostí | Název požadované instance centra událostí. |
       | Hodnota zásad centra událostí | Vyberte požadované zásady sdíleného přístupu. Zásadu sdíleného přístupu můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **čtení** oprávnění. |
       | Klíč zásad centra událostí | Hodnota předem vyplněná z vybrané hodnoty zásad centra událostí |

    * Pokud centra událostí je externí ke svým předplatným, nebo pokud budete chtít Upřesnit možnosti, vyberte **nastavení zadejte centra událostí ručně**.

       V následující tabulce jsou popsány požadované vlastnosti pro **nastavení zadejte centra událostí ručně** možnost:
 
       | Vlastnost | Popis |
       | --- | --- |
       | ID předplatného | Předplatné, ke kterému patří požadovaná instance centra událostí a obor názvů. |
       | Skupina prostředků | Skupina prostředků, ke které patří požadovaná instance centra událostí a obor názvů |
       | Obor názvů centra událostí | Obor názvů centra událostí, ke kterému patří požadovaná instance centra událostí |
       | Název centra událostí | Název požadované instance centra událostí. |
       | Hodnota zásad centra událostí | Vyberte požadované zásady sdíleného přístupu. Zásadu sdíleného přístupu můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **čtení** oprávnění. |
       | Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů služby Service Bus. Zadejte primární a sekundární klíč tady. |

    * Obě možnosti sdílejí následující možnosti konfigurace:

       | Vlastnost | Popis |
       | --- | --- |
       | Skupina uživatelů centra událostí | Skupina uživatelů, která načítá události z centra událostí. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí. |
       | Formát serializace události | V současné době JSON je k dispozici pouze Serializační formát. Zprávy událostí musí být v tomto formátu nebo data nelze číst. |
       | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zpráva odeslaná do centra událostí. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud je ponecháno prázdné, **čas zařazení události** události zdroj se používá jako časové razítko události. |

1. Přidáte vyhrazenou Time Series Insights příjemce názvu skupiny, kterou jste přidali do vašeho centra událostí.

1. Vyberte **Vytvořit**.

   Po vytvoření zdroje události Time Series Insights automaticky zahájí streamování dat do vašeho prostředí.

## <a name="next-steps"></a>Další kroky

* [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit data.

* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.

* Přístup k prostředí na [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).
