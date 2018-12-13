---
title: Azure Time Series Insights přidat služby IoT hub – přidání zdroje událostí IoT hub do služby Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup přidání zdroje událostí, která je připojená do služby IoT hub pro vaše prostředí Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.custom: seodec18
ms.openlocfilehash: 3e370bd4cebb84d7ee9f607fc6640218ee202bb9
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321267"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Přidání zdroje událostí IoT hub do prostředí Time Series Insights

Tento článek popisuje postup přidání zdroje událostí, která čte data ze služby Azure IoT Hub do prostředí Azure Time Series Insights pomocí webu Azure portal.

> [!NOTE]
> Pokyny v tomto článku použít verzi GA Azure Insights řady, čas a čas Series Insights ve verzi Preview prostředí.

## <a name="prerequisites"></a>Požadavky

* Vytvoření [prostředí Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Vytvoření [služby IoT hub s využitím webu Azure portal](../iot-hub/iot-hub-create-through-portal.md).
* Centrum IoT musíte mít aktivní zprávě události odesílány ve.
* Vytvořte vyhrazenou skupinu spotřebitelů ve službě IoT hub pro využívání z prostředí Time Series Insights. Každý zdroj událostí Time Series Insights musí mít svůj vlastní vyhrazenou skupinu spotřebitelů, který není sdílený s další příjemce. Pokud více čtenářům přijímat události z stejnou skupinu uživatelů, jsou pravděpodobně uvidíte selhání všechny nástroje pro čtení. Podrobnosti najdete v tématu [– Příručka pro vývojáře Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Přidat skupinu uživatelů do služby IoT hub

Aplikace používat skupiny příjemců k získání dat ze služby Azure IoT Hub. Poskytují vyhrazenou skupinu spotřebitelů, který se používá pouze v tomto prostředí Time Series Insights spolehlivě číst data ze služby IoT hub.

Chcete-li přidat novou skupinu uživatelů do služby IoT hub:

1. Na webu Azure Portal vyhledejte a otevřete své Centrum IoT.

1. V nabídce v části **nastavení**vyberte **integrovaných koncových bodech**a pak vyberte **události** koncového bodu.

   ![Na stránce sestavení v koncových bodů vyberte tlačítko události][1]

1. V části **skupiny příjemců**, zadejte jedinečný název pro skupinu příjemců. Když vytvoříte nový zdroj událostí, použijte tento stejný název ve vašem prostředí Time Series Insights.

1. Vyberte **Uložit**.

## <a name="add-a-new-event-source"></a>Přidat nový zdroj událostí

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Všechny prostředky**. Vyberte vaše prostředí Time Series Insights.

1. V části **topologie prostředí**vyberte **zdroje událostí**a pak vyberte **přidat**.

   ![Vyberte zdroje událostí a pak vyberte tlačítko Přidat][2]

1. V **nový zdroj událostí** podokně pro **název zdroje událostí**, zadejte název, který je jedinečný pro toto prostředí Time Series Insights. Zadejte například **datového proudu událostí**.

1. Pro **zdroj**vyberte **služby IoT Hub**.

1. Vyberte hodnotu pro **možnost importu**:

   * Pokud už máte službu IoT hub v jednom z vašich předplatných, vyberte **pomocí služby IoT Hub z dostupných předplatných**. Tato možnost je nejjednodušší přístup.
   * Pokud je pro vaše předplatná externí služby IoT hub nebo pokud chcete zvolit Pokročilá nastavení, vyberte **nastavení poskytovat služby IoT Hub ručně**.

   ![Vyberte možnosti v podokně nový zdroj událostí][3]

1. Následující tabulka popisuje vlastnosti, které jsou požadovány pro **pomocí služby IoT Hub z dostupných předplatných** možnost:

   ![Podokno zdroj nové události – vlastnosti, které chcete nastavit ve službě IoT Hub pomocí z možnosti předplatné k dispozici][4]

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Vyberte předplatné, ve kterém byla vytvořena služby IoT hub.
   | Název IoT Hubu | Vyberte název centra IoT.
   | Název zásad centra IoT | Vyberte zásady sdíleného přístupu. Zásady sdíleného přístupu můžete najít na kartě Nastavení centra IoT. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **služba připojit** oprávnění.
   | Klíč zásad centra IoT | Klíč je předem.
   | Skupina uživatelů centra IoT | Skupina uživatelů, která načítá události ze služby IoT hub. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
   | Formát serializace události | V současné době JSON je k dispozici pouze Serializační formát. Zprávy událostí musí být v tomto formátu, nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zprávy odesílané do služby IoT hub. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud je ponecháno prázdné, **čas zařazení události** události zdroj se používá jako časové razítko události. |

1. V následující tabulce jsou popsány požadované vlastnosti pro **nastavení poskytovat služby IoT Hub ručně**:

   | Vlastnost | Popis |
   | --- | --- |
   | ID předplatného | Předplatné, ve kterém byla vytvořena služby IoT hub.
   | Skupina prostředků | Název skupiny prostředků, ve kterém byla vytvořena služby IoT hub.
   | Název IoT Hubu | název služby IoT hub. Pokud jste vytvořili službu IoT hub, jste zadali název služby IoT hub.
   | Název zásad centra IoT | Zásady sdíleného přístupu. Na kartě nastavení IoT hub můžete vytvořit zásady sdíleného přístupu. Každá zásada sdíleného přístupu má název, že je nastavená oprávnění a přístupové klíče. Zásady sdíleného přístupu pro váš zdroj událostí *musí* mají **služba připojit** oprávnění.
   | Klíč zásad centra IoT | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů služby Azure Service Bus. Zadejte primární a sekundární klíč tady.
   | Skupina uživatelů centra IoT | Skupina uživatelů, která načítá události ze služby IoT hub. Důrazně doporučujeme používat vyhrazenou skupinu spotřebitelů pro váš zdroj událostí.
   | Formát serializace události | V současné době JSON je k dispozici pouze Serializační formát. Zprávy událostí musí být v tomto formátu, nebo mohou být přečteny žádná data. |
   | Název vlastnosti časového razítka | Chcete-li tuto hodnotu určit pochopit formátu dat zprávy odesílané do služby IoT hub. Tato hodnota je **název** vlastnosti specifické události v datech zprávu, kterou chcete použít jako časové razítko události. Hodnota je velká a malá písmena. Pokud je ponecháno prázdné, **čas zařazení události** události zdroj se používá jako časové razítko události. |

1. Přidáte vyhrazenou Time Series Insights příjemce názvu skupiny, kterou jste přidali do služby IoT hub.

1. Vyberte **Vytvořit**.

   ![Tlačítka pro vytvoření][5]

1. Po vytvoření zdroje událostí Time Series Insights automaticky spustí, streamování dat do vašeho prostředí.

## <a name="next-steps"></a>Další postup

* [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit data.
* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.
* Přístup k prostředí na [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png
[2]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png
[3]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png
[4]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png
[5]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png