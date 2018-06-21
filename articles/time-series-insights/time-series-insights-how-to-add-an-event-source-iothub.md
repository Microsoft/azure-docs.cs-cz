---
title: Postup přidání zdroje událostí služby IoT Hub ke statistice řady čas Azure | Microsoft Docs
description: Tento článek popisuje postup přidání připojeném do služby IoT Hub pro vaše prostředí časové řady Statistika zdroje událostí
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: edett
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: b970d01c586e016d47b0f0480d73f06211969814
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294876"
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Postup přidání zdroje událostí služby IoT Hub časové řady Přehled prostředí
Tento článek popisuje, jak pomocí portálu Azure přidejte zdroje událostí, který čte data ze služby IoT Hub do vašeho prostředí Statistika časové řady.

## <a name="prerequisites"></a>Požadavky
- Vytvořte prostředí Statistika časové řady. Další informace najdete v tématu [vytvoření prostředí, ve statistice řady čas Azure](time-series-insights-get-started.md) 
- Vytvoření služby IoT Hub. Další informace o centra IoT najdete v tématu [vytvoření služby IoT Hub pomocí portálu Azure](../iot-hub/iot-hub-create-through-portal.md)
- IoT Hub musí mít aktivní zpráva události odesílány ve.
- Vytvořte vyhrazenou skupinu spotřebitelů IoT hub pro prostředí časové řady přehledy využívat z. Každý zdroj události Statistika časové řady musí mít svůj vlastní vyhrazenou skupinu spotřebitelů která není sdílena s dalšími uživateli. Pokud více čtečky přijímat události z stejnou skupinu uživatelů, budou pravděpodobně najdete v části selhání všechny nástroje pro čtení. Podrobnosti najdete v tématu [Příručka vývojáře pro službu IoT Hub](../iot-hub/iot-hub-devguide.md).

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte existující časové řady Statistika prostředí. Klikněte na tlačítko **všechny prostředky** v nabídce na levé straně na portálu Azure. Vyberte vaše prostředí Time Series Insights.

3. V části **prostředí topologie** záhlaví, klikněte na tlačítko **zdroje událostí**.
   ![Zdroje událostí + přidat](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Klikněte na tlačítko **+ Přidat**.

5. Zadejte **název zdroje událostí** jedinečné pro toto časové řady Statistika prostředí, například **datového proudu událostí**.

   ![Zadejte první tři parametry ve tvaru.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Vyberte **zdroj** jako **služby IoT Hub**.

7. Vyberte odpovídající **importovat možnost**. 
   - Zvolte **použijte centrum IoT z dostupných předplatných** Pokud již máte stávající služby IoT Hub na jednom z vašich předplatných. Toto je nejjednodušší přístup.
   - Zvolte **nastavení zadejte centra IoT ručně** při IoT Hub je externí ke svým předplatným, nebo chcete vybrat rozšířené možnosti. 

8. Pokud jste vybrali **použijte centrum IoT z dostupných předplatných** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   ![Podrobnosti o předplatném a události rozbočovače](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Vyberte předplatné, ve kterém byla vytvořena toto centrum IoT.
   | Název centra IoT | Vyberte název služby IoT Hub.
   | Název zásady služby IoT Hub | Vyberte zásady sdíleného přístupu, které lze najít na kartě nastavení služby IoT Hub. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásada sdíleného přístupu ke zdroji událostí *musí* mít **služba připojit** oprávnění.
   | Klíč služby IoT Hub zásad | Klíč je předem.
   | Skupiny uživatelů služby IoT Hub | Skupiny příjemců číst události ze služby IoT Hub. Důrazně doporučujeme použít vyhrazenou skupinu spotřebitelů zdroje událostí.
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být ve formátu, nebo žádná data lze číst. |
   | Název vlastnosti časové razítko | Chcete-li tuto hodnotu určit, je potřeba pochopit formát zprávy zpráva dat odesílaných do služby IoT Hub. Tato hodnota je **název** vlastnosti konkrétní události v daty zprávy, kterou chcete použít jako události časové razítko. Hodnota je malá a velká písmena. Pokud je ponecháno prázdné, **zařazování čas události** v rámci události zdroj se používá jako časové razítko události. |

9. Pokud jste vybrali **nastavení zadejte centra IoT ručně** možnost, následující tabulka vysvětluje všechny požadované vlastnosti:

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Odběr, v němž je tato byla vytvořena IoT Hub.
   | Skupina prostředků | Název skupiny prostředků, ve které byla vytvořena IoT Hub.
   | Název centra IoT | Název služby IoT Hub. Když vytvoříte Centrum IoT, dáte mu taky určitý název.
   | Název zásady služby IoT Hub | Zásady sdíleného přístupu, které se dají vytvořit na kartě nastavení služby IoT Hub. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásada sdíleného přístupu ke zdroji událostí *musí* mít **služba připojit** oprávnění.
   | Klíč služby IoT Hub zásad | Sdílený přístupový klíč používaný k ověření přístupu k oboru názvů Service Bus. Primární nebo sekundární klíč zadejte sem.
   | Skupiny uživatelů služby IoT Hub | Skupiny příjemců číst události ze služby IoT Hub. Důrazně doporučujeme použít vyhrazenou skupinu spotřebitelů zdroje událostí.
   | Formát serializace události | JSON je k dispozici pouze serializace v současné době. Zprávy událostí musí být ve formátu, nebo žádná data lze číst. |
   | Název vlastnosti časové razítko | Chcete-li tuto hodnotu určit, je potřeba pochopit formát zprávy zpráva dat odesílaných do služby IoT Hub. Tato hodnota je **název** vlastnosti konkrétní události v daty zprávy, kterou chcete použít jako události časové razítko. Hodnota je malá a velká písmena. Pokud je ponecháno prázdné, **zařazování čas události** v rámci události zdroj se používá jako časové razítko události. |

10. Vyberte **vytvořit** přidat nový zdroj událostí.

   ![Kliknutí na Vytvořit](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   Po vytvoření zdroje událostí začne Time Series Insights automaticky streamovat data do vašeho prostředí.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidat skupinu uživatelů do služby IoT Hub
Skupiny uživatelů slouží aplikace k načítání dat z Azure IoT Hubs. Zadejte vyhrazenou skupinu spotřebitelů, použijte tento čas řady Statistika prostředí pouze spolehlivě číst data ze služby IoT Hub.

Chcete-li přidat novou skupinu uživatelů do služby IoT Hub, postupujte takto:
1. Na portálu Azure vyhledejte a otevřete své služby IoT Hub.

2. V části **zasílání zpráv** záhlaví, vyberte **koncové body**. 

   ![Přidat skupinu uživatelů](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Vyberte **události** koncovému bodu a **vlastnosti** otevře se stránka.

4. V části **skupiny příjemců** záhlaví, zadejte nový jedinečný název pro skupinu příjemců. Používejte tento stejný název v prostředí Statistika řady čas při vytváření nový zdroj událostí.

5. Vyberte **Uložit** uložit do nové skupiny příjemců.

## <a name="next-steps"></a>Další postup
- [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit data.
- [Odesílání událostí](time-series-insights-send-events.md) ke zdroji událostí.
- Přístup k prostředí v [explorer časové řady Insights](https://insights.timeseries.azure.com).
