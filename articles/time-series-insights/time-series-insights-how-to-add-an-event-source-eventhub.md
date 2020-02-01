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
ms.date: 01/30/2020
ms.custom: seodec18
ms.openlocfilehash: c3b06289ba6ce98d4307a8255981ecdba069fdfa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905405"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Přidání zdroje událostí centra událostí do prostředí Time Series Insights

Tento článek popisuje postup přidání zdroje událostí, která čte data z Azure Event Hubs do prostředí Azure Time Series Insights pomocí webu Azure portal.

> [!NOTE]
> Postup, který je popsaný v tomto článku, platí jak pro prostředí Time Series Insights GA a Time Series Insights ve verzi Preview.

## <a name="prerequisites"></a>Požadavky

- Vytvořte Time Series Insights prostředí, jak je popsáno v tématu [vytvoření Azure Time Series Insights prostředí](./time-series-insights-update-create-environment.md).
- Vytvořte centrum událostí. Přečtěte si téma [Vytvoření oboru názvů Event Hubs a centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md).
- Centrum událostí, musíte mít aktivní zprávě události do něj odesílají. Naučte se [odesílat události do Azure Event Hubs pomocí .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Vytvořte vyhrazenou skupinu spotřebitelů v Centru událostí, které využívají z prostředí Time Series Insights. Každý zdroj událostí Time Series Insights musí mít svůj vlastní vyhrazenou skupinu spotřebitelů, který není sdílený s další příjemce. Pokud více čtenářů spotřebovává události ze stejné skupiny příjemců, můžou se všechny čtenáři projevit při selhání. Platí omezení 20 skupin uživatelů na Centrum událostí. Podrobnosti najdete v tématu [Průvodce programováním pro Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Přidat skupinu uživatelů do vašeho centra událostí

Aplikace používat skupiny příjemců k získání dat z Azure Event Hubs. Pokud chcete spolehlivě číst data z centra událostí, poskytněte vyhrazenou skupinu uživatelů, kterou používá jenom toto Time Series Insights prostředí.

Chcete-li přidat novou skupinu uživatelů ve službě event hub:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete instanci centra událostí z podokna **přehledu** oboru názvů centra událostí. Vyberte **entity > Event Hubs** nebo Najděte instanci pod **názvem**.

    [![otevřít obor názvů centra událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. V instanci centra událostí vyberte **entity > skupiny uživatelů**. Pak vyberte **+ Skupina uživatelů** a přidejte novou skupinu příjemců. 

   [centrum událostí ![– přidat skupinu příjemců](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   V opačném případě vyberte existující skupinu uživatelů a přejděte k další části.

1. Na **skupiny příjemců** stránky, zadejte novou jedinečnou hodnotu pro **název**.  Když vytvoříte nový zdroj událostí do prostředí Time Series Insights, použijte tento stejný název.

1. Vyberte **Vytvořit**.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující prostředí Time Series Insights. V nabídce vlevo vyberte **všechny prostředky**a pak vyberte vaše prostředí Time Series Insights.

1. Vyberte **zdroje událostí**a pak vyberte **Přidat**.

   [![v části zdroje událostí vyberte tlačítko Přidat.](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Zadejte hodnotu pro **název zdroje události** , která je pro toto Time Series Insights prostředí jedinečná, například `Contoso-TSI-GA-Event-Hub-ES`.

1. Pro **zdroj**vyberte **centra událostí**.

1. Vyberte příslušné hodnoty pro **možnost importu**:

   * Pokud máte existující centrum událostí v jednom z vašich předplatných, vyberte **použijte Centrum událostí z dostupných předplatných**. Tato možnost je nejjednodušší přístup.

     [![výběr možnosti importu zdroje událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  V následující tabulce jsou popsány požadované vlastnosti pro **použijte Centrum událostí z dostupných předplatných** možnost:

       [Podrobnosti o ![předplatného a centra událostí](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

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
