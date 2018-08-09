---
title: Přidání zdroje událostí centra událostí do služby Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup přidání zdroje událostí, který je připojený do centra událostí do prostředí Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: ce4bf1ab74e4203f0deb7b2984ffa6a66d5efd4a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627106"
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Přidání zdroje událostí centra událostí do prostředí Time Series Insights

Tento článek popisuje postup přidání zdroje událostí, která čte data z centra událostí do prostředí Time Series Insights pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky
- Vytvoření prostředí Time Series Insights. Další informace najdete v tématu [vytvořit prostředí Azure Time Series Insights](time-series-insights-get-started.md) 
- Vytvoření centra událostí Další informace o Event Hubs najdete v tématu [vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal](../event-hubs/event-hubs-create.md)
- Centrum událostí musí mít aktivní zprávě události odesílány ve. Další informace najdete v tématu [odesílání událostí do služby Azure Event Hubs pomocí rozhraní .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Vytvořte vyhrazenou skupinu spotřebitelů v Centru událostí pro prostředí Time Series Insights k využívání z. Každý zdroj událostí Time Series Insights musí mít svůj vlastní vyhrazenou skupinu spotřebitelů, kterou nebude sdílet s dalšími uživateli. Pokud více čtenářům přijímat události z stejnou skupinu uživatelů, jsou pravděpodobně uvidíte selhání všechny nástroje pro čtení. Všimněte si, že také limit 20 skupiny uživatelů na Centrum událostí. Podrobnosti najdete v tématu [Průvodce programováním pro Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Přidat skupinu uživatelů do vašeho centra událostí
Skupiny uživatelů se aplikace používají k načítání dat z Azure Event Hubs. Poskytují vyhrazenou skupinu spotřebitelů, pro použití u tohoto prostředí Time Series Insights, spolehlivě přečíst data z vašeho centra událostí.

Přidat novou skupinu uživatelů centra událostí, postupujte podle těchto kroků:
1. Na webu Azure Portal vyhledejte a otevřete vaše Centrum událostí.

2. V části **entity** záhlaví, vyberte **skupiny příjemců**.

   ![Centrum událostí – přidat skupinu uživatelů](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Vyberte **+ skupina uživatelů** se přidat novou skupinu uživatelů. 

4. Na **skupiny příjemců** zadejte nový jedinečný **název**.  Při vytváření nového zdroje událostí do prostředí Time Series Insights, použijte tento stejný název.

5. Vyberte **vytvořit** se vytvořit novou skupinu uživatelů.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte existující prostředí Time Series Insights. Klikněte na tlačítko **všechny prostředky** v nabídce na levé straně webu Azure portal. Vyberte vaše prostředí Time Series Insights.

3. V části **topologie prostředí** záhlaví, klikněte na tlačítko **zdroje událostí**.

   ![Zdroje událostí + přidat](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Klikněte na tlačítko **+ Přidat**.

5. Poskytují **název zdroje událostí** jedinečné pro toto prostředí Time Series Insights, jako **datového proudu událostí**.

   ![Vyplňte první tři parametry ve formě.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. Vyberte **zdroj** jako **centra událostí**.

7. Vyberte příslušné **možnost importu**. 
   - Zvolte **použijte Centrum událostí z dostupných předplatných** Pokud již máte existující centrum událostí v jednom z vašich předplatných. Toto je nejjednodušší přístup.
   - Zvolte **nastavení zadejte centra událostí ručně** při centra událostí je externí ke svým předplatným, nebo chcete zvolit Pokročilá nastavení. 

8. Pokud jste vybrali **použijte Centrum událostí z dostupných předplatných** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   ![Podrobnosti o Centru předplatného a události](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Vyberte předplatné, ve kterém byla vytvořena centra událostí.
   | Obor názvů Sběrnice | Vyberte obor názvů služby Service Bus, která obsahuje centra událostí.
   | Název centra událostí | Vyberte název centra událostí.
   | Název zásady centra událostí | Vyberte zásady sdíleného přístupu, které se dají vytvořit na kartě Konfigurace centra událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **čtení** oprávnění.
   | Klíč zásad centra událostí | Hodnota klíče může být předem.
   | Skupina uživatelů centra událostí | Skupina uživatelů číst události z centra událostí. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí. |
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být v tomto formátu, nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zprávy odeslané do centra událostí. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud nevyplníte, **čas zařazení události** v rámci události zdroj se používá jako časové razítko události. |


9. Pokud jste vybrali **nastavení zadejte centra událostí ručně** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Předplatné, ve kterém byla vytvořena centra událostí.
   | Skupina prostředků | Skupina prostředků, ve kterém byla vytvořena centra událostí.
   | Obor názvů Sběrnice | Obor názvů služby Service Bus je kontejner sady entit pro zasílání zpráv. Když vytvoříte nové Centrum událostí, vytvoříte tím taky obor názvů Service Bus.
   | Název centra událostí | Název vašeho centra událostí. Když vytvoříte Centrum událostí, dáte mu taky určitý název.
   | Název zásady centra událostí | Zásady sdíleného přístupu, které se dají vytvořit na kartě Konfigurace centra událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **čtení** oprávnění.
   | Klíč zásad centra událostí | Sdílený přístupový klíč používá k ověření přístupu k oboru názvů služby Service Bus. Primární nebo sekundární klíč zadejte sem.
   | Skupina uživatelů centra událostí | Skupina uživatelů číst události z centra událostí. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být v tomto formátu, nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zprávy odeslané do centra událostí. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud nevyplníte, **čas zařazení události** v rámci události zdroj se používá jako časové razítko události. |

10. Přidáte vyhrazenou TSI příjemce názvu skupiny, kterou jste přidali do vašeho centra událostí.

11. Vyberte **vytvořit** přidat nový zdroj událostí.
   
   ![Kliknutí na Vytvořit](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Po vytvoření zdroje událostí začne Time Series Insights automaticky streamovat data do vašeho prostředí.


## <a name="next-steps"></a>Další postup
- [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit data.
- [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.
- Přístup k prostředí na [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).
