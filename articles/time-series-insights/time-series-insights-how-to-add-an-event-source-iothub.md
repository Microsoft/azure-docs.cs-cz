---
title: Přidání zdroje událostí IoT Hub do služby Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup přidání zdroje událostí, která je připojená do služby IoT Hub pro vaše prostředí Time Series Insights
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: b6beecbf64cee925f62ac4c82919926fcb79940a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627368"
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Přidání zdroje událostí IoT Hub do prostředí Time Series Insights
Tento článek popisuje postup přidání zdroje událostí, která čte data ze služby IoT Hub do prostředí Time Series Insights pomocí webu Azure portal.

## <a name="prerequisites"></a>Požadavky
- Vytvoření prostředí Time Series Insights. Další informace najdete v tématu [vytvořit prostředí Azure Time Series Insights](time-series-insights-get-started.md) 
- Vytvořit IoT Hub. Další informace o IoT hub najdete v tématu [vytvoření IoT Hubu pomocí webu Azure portal](../iot-hub/iot-hub-create-through-portal.md)
- IoT Hub musí mít aktivní zprávě události odesílány ve.
- Vytvořte vyhrazenou skupinu spotřebitelů ve službě IoT Hub pro prostředí Time Series Insights k využívání z. Každý zdroj událostí Time Series Insights musí mít svůj vlastní vyhrazenou skupinu spotřebitelů, kterou nebude sdílet s dalšími uživateli. Pokud více čtenářům přijímat události z stejnou skupinu uživatelů, jsou pravděpodobně uvidíte selhání všechny nástroje pro čtení. Podrobnosti najdete v tématu [Příručka vývojáře pro IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidat skupinu uživatelů do služby IoT Hub
Skupiny příjemců používají aplikace k načítání dat ze služby Azure IoT Hubs. Poskytují vyhrazenou skupinu spotřebitelů, pro použití u tohoto prostředí Time Series Insights, spolehlivě číst data ze služby IoT Hub.

Chcete-li přidat novou skupinu uživatelů do služby IoT Hub, postupujte podle těchto kroků:
1. Na webu Azure Portal najděte a otevřete své Centrum IoT.

2. V části **zasílání zpráv** záhlaví, vyberte **koncové body**. 

   ![Přidat skupinu uživatelů](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Vyberte **události** koncového bodu a **vlastnosti** otevře se stránka.

4. V části **skupiny příjemců** záhlaví, zadejte nový jedinečný název pro skupinu příjemců. Při vytváření nového zdroje událostí, použijte tento stejný název v prostředí Time Series Insights.

5. Vyberte **Uložit** uložte novou skupinu uživatelů.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte existující prostředí Time Series Insights. Klikněte na tlačítko **všechny prostředky** v nabídce na levé straně webu Azure portal. Vyberte vaše prostředí Time Series Insights.

3. V části **topologie prostředí** záhlaví, klikněte na tlačítko **zdroje událostí**.
   ![Zdroje událostí + přidat](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Klikněte na tlačítko **+ Přidat**.

5. Poskytují **název zdroje událostí** jedinečné pro toto prostředí Time Series Insights, jako **datového proudu událostí**.

   ![Vyplňte první tři parametry ve formě.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Vyberte **zdroj** jako **služby IoT Hub**.

7. Vyberte příslušné **možnost importu**. 
   - Zvolte **pomocí služby IoT Hub z dostupných předplatných** Pokud už máte existující centrum IoT Hub na jednom z vašich předplatných. Toto je nejjednodušší přístup.
   - Zvolte **nastavení poskytovat služby IoT Hub ručně** při služby IoT Hub je externí ke svým předplatným, nebo chcete zvolit Pokročilá nastavení. 

8. Pokud jste vybrali **pomocí služby IoT Hub z dostupných předplatných** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   ![Podrobnosti o Centru předplatného a události](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Vyberte předplatné, ve kterém byla vytvořena centra IoT Hub.
   | Název centra IoT | Vyberte název centra IoT.
   | Název zásad centra IoT | Vyberte zásady sdíleného přístupu, který se nachází na kartě nastavení služby IoT Hub. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **služba připojit** oprávnění.
   | Klíč zásad centra IoT | Klíč je předem.
   | Skupina uživatelů centra IoT | Skupina uživatelů ke čtení události ze služby IoT Hub. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být v tomto formátu, nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zprávy odeslané do služby IoT Hub. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud nevyplníte, **čas zařazení události** v rámci události zdroj se používá jako časové razítko události. |

9. Pokud jste vybrali **nastavení poskytovat služby IoT Hub ručně** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Předplatné, ve které byla vytvořena služby IoT Hub.
   | Skupina prostředků | Název skupiny prostředků, ve které byla vytvořena služby IoT Hub.
   | Název centra IoT | Název služby IoT Hub. Pokud jste vytvořili službu IoT Hub, dáte mu taky určitý název.
   | Název zásad centra IoT | Zásady sdíleného přístupu, které se dají vytvořit na kartě nastavení služby IoT Hub. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **služba připojit** oprávnění.
   | Klíč zásad centra IoT | Sdílený přístupový klíč používaný k ověření přístupu k oboru názvů služby Service Bus. Primární nebo sekundární klíč zadejte sem.
   | Skupina uživatelů centra IoT | Skupina uživatelů ke čtení události ze služby IoT Hub. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být v tomto formátu, nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zprávy odeslané do služby IoT Hub. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud nevyplníte, **čas zařazení události** v rámci události zdroj se používá jako časové razítko události. |

10. Přidáte vyhrazenou TSI příjemce názvu skupiny, kterou jste přidali do služby IoT Hub.

11. Vyberte **vytvořit** přidat nový zdroj událostí.

   ![Kliknutí na Vytvořit](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   Po vytvoření zdroje událostí začne Time Series Insights automaticky streamovat data do vašeho prostředí.

## <a name="next-steps"></a>Další postup
- [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit data.
- [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.
- Přístup k prostředí na [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).
