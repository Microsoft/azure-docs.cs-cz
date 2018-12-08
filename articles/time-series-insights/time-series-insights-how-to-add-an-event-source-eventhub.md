---
title: Přidání zdroje událostí služby Event Hubs do služby Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup přidání zdroje událostí, který je připojený k Azure Event Hubs do prostředí Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: ce76a9dcd8b717b6ab0aef4c3c12c2116ec4f237
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104692"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Přidání zdroje událostí centra událostí do prostředí Time Series Insights

Tento článek popisuje postup přidání zdroje událostí, která čte data z Azure Event Hubs do prostředí Azure Time Series Insights pomocí webu Azure portal.

> [!NOTE]
> Podle kroků popsaných v tomto článku se vztahují jak na GA Insights řady čas a čas Series Insights ve verzi Preview prostředí.

## <a name="prerequisites"></a>Požadavky

- Vytvoření prostředí Time Series Insights. Další informace najdete v tématu [vytvořit prostředí Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Vytvořte centrum událostí. Další informace o službě Event Hubs najdete v tématu [vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal](../event-hubs/event-hubs-create.md).
- Centrum událostí, musíte mít aktivní zprávě události do něj odesílají. Další informace najdete v tématu [odesílání událostí do služby Azure Event Hubs pomocí rozhraní .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Vytvořte vyhrazenou skupinu spotřebitelů v Centru událostí, které využívají z prostředí Time Series Insights. Každý zdroj událostí Time Series Insights musí mít svůj vlastní vyhrazenou skupinu spotřebitelů, který není sdílený s další příjemce. Pokud více čtenářům přijímat události z stejnou skupinu uživatelů, jsou pravděpodobně uvidíte selhání všechny nástroje pro čtení. Platí omezení 20 skupin uživatelů na Centrum událostí. Podrobnosti najdete v tématu [programováním pro službu Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Přidat skupinu uživatelů do vašeho centra událostí

Aplikace používat skupiny příjemců k získání dat z Azure Event Hubs. Poskytují vyhrazenou skupinu spotřebitelů, pro použití pouze u tohoto prostředí Time Series Insights, spolehlivě přečíst data z vašeho centra událostí.

Chcete-li přidat novou skupinu uživatelů ve službě event hub:

1. Na webu Azure Portal vyhledejte a otevřete vaše Centrum událostí.

1. V části **entity**vyberte **skupiny příjemců**a pak vyberte **skupinu příjemců**.

   ![Centrum událostí – přidat skupinu uživatelů](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

1. Na **skupiny příjemců** stránky, zadejte novou jedinečnou hodnotu pro **název**.  Když vytvoříte nový zdroj událostí do prostředí Time Series Insights, použijte tento stejný název.

1. Vyberte **Vytvořit**.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující prostředí Time Series Insights. V nabídce vlevo vyberte **všechny prostředky**a pak vyberte vaše prostředí Time Series Insights.

1. V části **topologie prostředí**vyberte **zdroje událostí**a pak vyberte **přidat**.

   ![V části zdroje událostí vyberte tlačítko Přidat](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

1. Zadejte hodnotu pro **název zdroje událostí** , který je jedinečný pro toto prostředí Time Series Insights, jako například **datového proudu událostí**.

1. Pro **zdroj**vyberte **centra událostí**.

1. Vyberte příslušné hodnoty pro **možnost importu**:
   - Pokud máte existující centrum událostí v jednom z vašich předplatných, vyberte **použijte Centrum událostí z dostupných předplatných**. Tato možnost je nejjednodušší přístup.
   - Pokud centra událostí je externí ke svým předplatným, nebo pokud budete chtít Upřesnit možnosti, vyberte **nastavení zadejte centra událostí ručně**.

   ![V podokně nový zdroj událostí zadejte hodnoty pro první tři parametry](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

1. V následující tabulce jsou popsány požadované vlastnosti pro **použijte Centrum událostí z dostupných předplatných** možnost:

   ![Podrobnosti o Centru předplatného a události](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Vyberte předplatné, ve kterém byla vytvořena centra událostí.
   | Názvový prostor služby Service Bus | Vyberte obor názvů služby Azure Service Bus, která obsahuje centra událostí.
   | Název centra událostí | Vyberte název centra událostí.
   | Název zásady centra událostí | Vyberte zásady sdíleného přístupu. Můžete vytvořit zásady sdíleného přístupu v Centru událostí **konfigurovat** kartu. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **čtení** oprávnění.
   | Klíč zásad centra událostí | Hodnota klíče může být předem.
   | Skupina uživatelů centra událostí | Skupina uživatelů, která načítá události z centra událostí. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí. |
   | Formát serializace události | V současné době JSON je k dispozici pouze Serializační formát. Zprávy o událostech musí být v tomto formátu nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zpráva odeslaná do centra událostí. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud je ponecháno prázdné, **čas zařazení události** události zdroj se používá jako časové razítko události. |

1. V následující tabulce jsou popsány požadované vlastnosti pro **nastavení zadejte centra událostí ručně** možnost:

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Předplatné, ve kterém byla vytvořena centra událostí.
   | Skupina prostředků | Skupina prostředků, ve kterém byla vytvořena centra událostí.
   | Názvový prostor služby Service Bus | Obor názvů služby Service Bus je kontejner sady entit pro zasílání zpráv. Když vytvoříte nové Centrum událostí, vytvoříte tím taky obor názvů Service Bus.
   | Název centra událostí | Název vašeho centra událostí. Když vytvoříte Centrum událostí, dáte mu taky určitý název.
   | Název zásady centra událostí | Zásady sdíleného přístupu. Můžete vytvořit zásady sdíleného přístupu v Centru událostí **konfigurovat** kartu. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **čtení** oprávnění.
   | Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů služby Service Bus. Zadejte primární a sekundární klíč tady.
   | Skupina uživatelů centra událostí | Skupina uživatelů, která načítá události z centra událostí. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
   | Formát serializace události | V současné době JSON je k dispozici pouze Serializační formát. Zprávy o událostech musí být v tomto formátu nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zpráva odeslaná do centra událostí. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud je ponecháno prázdné, **čas zařazení události** události zdroj se používá jako časové razítko události. |

1. Přidáte vyhrazenou Time Series Insights příjemce názvu skupiny, kterou jste přidali do vašeho centra událostí.

1. Vyberte **Vytvořit**.

   ![Výběr možnosti Vytvořit](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Po vytvoření zdroje událostí Time Series Insights automaticky spustí, streamování dat do vašeho prostředí.

## <a name="next-steps"></a>Další postup

* [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit data.
* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.
* Přístup k prostředí na [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).
