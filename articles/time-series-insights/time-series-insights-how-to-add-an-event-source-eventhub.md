---
title: Postup přidání zdroje událostí centra událostí Azure časové řady insights | Microsoft Docs
description: Tento článek popisuje postup přidání zdroje událostí, která je připojena k Centru událostí pro vaše prostředí časové řady Statistika.
ms.service: time-series-insights
services: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: ed151160bd8bd0f0241e1a728fab53570e33a201
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656013"
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Postup přidání zdroje událostí centra událostí do časové řady Přehled prostředí

Tento článek popisuje, jak pomocí portálu Azure přidejte zdroje událostí, který čte data z centra událostí do vašeho prostředí Statistika časové řady.

## <a name="prerequisites"></a>Požadavky
- Vytvořte prostředí Statistika časové řady. Další informace najdete v tématu [vytvoření prostředí, ve statistice řady čas Azure](time-series-insights-get-started.md) 
- Vytvoření centra událostí Další informace o službě Event Hubs naleznete v tématu [vytvořit obor názvů Event Hubs a centra událostí pomocí portálu Azure](../event-hubs/event-hubs-create.md)
- Centra událostí musí mít aktivní zpráva události odesílány ve. Další informace najdete v tématu [odesílat události do centra událostí Azure pomocí rozhraní .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- V Centru událostí pro prostředí časové řady přehledy využívat z vytvořte vyhrazenou skupinu spotřebitelů. Každý zdroj události Statistika časové řady musí mít svůj vlastní vyhrazenou skupinu spotřebitelů která není sdílena s dalšími uživateli. Pokud více čtečky přijímat události z stejnou skupinu uživatelů, budou pravděpodobně najdete v části selhání všechny nástroje pro čtení. Všimněte si, že také maximální počet 20 skupiny příjemců za centra událostí. Podrobnosti najdete v tématu [Průvodce programováním centra událostí](../event-hubs/event-hubs-programming-guide.md).

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte existující časové řady Statistika prostředí. Klikněte na tlačítko **všechny prostředky** v nabídce na levé straně na portálu Azure. Vyberte vaše prostředí Time Series Insights.

3. V části **prostředí topologie** záhlaví, klikněte na tlačítko **zdroje událostí**.

   ![Zdroje událostí + přidat](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Klikněte na tlačítko **+ Přidat**.

5. Zadejte **název zdroje událostí** jedinečné pro toto časové řady Statistika prostředí, například **datového proudu událostí**.

   ![Zadejte první tři parametry ve tvaru.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. Vyberte **zdroj** jako **centra událostí**.

7. Vyberte odpovídající **importovat možnost**. 
   - Zvolte **použijte Centrum událostí z dostupných předplatných** Pokud již máte existující centra událostí na jednom z vašich předplatných. Toto je nejjednodušší přístup.
   - Zvolte **nastavení zadejte centra událostí ručně** při centra událostí je externí ke svým předplatným, nebo chcete vybrat rozšířené možnosti. 

8. Pokud jste vybrali **použijte Centrum událostí z dostupných předplatných** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   ![Podrobnosti o předplatném a události rozbočovače](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Vyberte předplatné, ve kterém byla vytvořena toto Centrum událostí.
   | Obor názvů Sběrnice | Vyberte obor názvů Service Bus, který obsahuje centra událostí.
   | Název centra událostí | Vyberte název centra událostí.
   | Název zásady centra událostí | Vyberte zásady sdíleného přístupu, které se dají vytvořit na kartě Konfigurace centra událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásada sdíleného přístupu ke zdroji událostí *musí* mít **číst** oprávnění.
   | Klíč události rozbočovače zásad | Hodnota klíče může předběžně vyplní.
   | Skupina uživatelů centra událostí | Skupina uživatelů mají události načítat z centra událostí. Důrazně doporučujeme použít vyhrazenou skupinu spotřebitelů zdroje událostí. |
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být ve formátu, nebo žádná data lze číst. |
   | Název vlastnosti časové razítko | Chcete-li tuto hodnotu určit, je potřeba pochopit formát zprávy zpráva dat odesílaných do centra událostí. Tato hodnota je **název** vlastnosti konkrétní události v daty zprávy, kterou chcete použít jako události časové razítko. Hodnota je malá a velká písmena. Pokud je ponecháno prázdné, **zařazování čas události** v rámci události zdroj se používá jako časové razítko události. |


9. Pokud jste vybrali **nastavení zadejte centra událostí ručně** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Předplatné, ve kterém byla vytvořena toto Centrum událostí.
   | Skupina prostředků | Skupinu prostředků, ve kterém byla vytvořena toto Centrum událostí.
   | Obor názvů Sběrnice | Obor názvů sběrnice je kontejner sady entit pro zasílání zpráv. Při vytváření nového centra událostí, vytvoříte tím taky obor názvů sběrnice.
   | Název centra událostí | Název vašeho centra událostí. Když vytvoříte Centrum událostí, dáte mu taky určitý název.
   | Název zásady centra událostí | Zásady sdíleného přístupu, které se dají vytvořit na kartě Konfigurace centra událostí. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásada sdíleného přístupu ke zdroji událostí *musí* mít **číst** oprávnění.
   | Klíč události rozbočovače zásad | Sdílený přístupový klíč použitý k ověření přístupu k oboru názvů Service Bus. Primární nebo sekundární klíč zadejte sem.
   | Skupina uživatelů centra událostí | Skupina uživatelů mají události načítat z centra událostí. Důrazně doporučujeme použít vyhrazenou skupinu spotřebitelů zdroje událostí.
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být ve formátu, nebo žádná data lze číst. |
   | Název vlastnosti časové razítko | Chcete-li tuto hodnotu určit, je potřeba pochopit formát zprávy zpráva dat odesílaných do centra událostí. Tato hodnota je **název** vlastnosti konkrétní události v daty zprávy, kterou chcete použít jako události časové razítko. Hodnota je malá a velká písmena. Pokud je ponecháno prázdné, **zařazování čas události** v rámci události zdroj se používá jako časové razítko události. |


10. Vyberte **vytvořit** přidat nový zdroj událostí.
   
   ![Kliknutí na Vytvořit](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Po vytvoření zdroje událostí začne Time Series Insights automaticky streamovat data do vašeho prostředí.


### <a name="add-a-consumer-group-to-your-event-hub"></a>Přidat skupinu uživatelů do vašeho centra událostí
Skupiny uživatelů slouží aplikace k načítání dat z Azure Event Hubs. Zadejte vyhrazenou skupinu spotřebitelů, použijte tento čas řady Statistika prostředí pouze spolehlivě číst data z vašeho centra událostí.

Chcete-li přidat novou skupinu uživatelů ve službě Event Hub, postupujte takto:
1. Na portálu Azure vyhledejte a otevřete Centrum událostí.

2. V části **entity** záhlaví, vyberte **skupiny příjemců**.

   ![Centra událostí - přidat skupinu uživatelů](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Vyberte **+ skupiny příjemců** přidat nové skupiny příjemců. 

4. Na **skupiny příjemců** zadejte nový jedinečný **název**.  Při vytváření nový zdroj událostí v prostředí časové řady Insights, použijte tento stejný název.

5. Vyberte **vytvořit** k vytvoření nové skupiny příjemců.

## <a name="next-steps"></a>Další postup
- [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit data.
- [Odesílání událostí](time-series-insights-send-events.md) ke zdroji událostí.
- Přístup k prostředí v [explorer časové řady Insights](https://insights.timeseries.azure.com).
