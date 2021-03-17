---
title: Kurz – vytvoření řídicího panelu pro výběr dat o stavu pomocí Azure IoT Central | Microsoft Docs
description: Kurz – Naučte se vytvářet řídicí panely pro vytváření dat o stavu pomocí šablon aplikací Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: d227d934eedd31342ce419576fffe7cea17efb1d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748027"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Kurz: sestavení řídicího panelu poskytovatele Power BI

Při sestavování řešení pro monitorování nepřetržitého pacienta můžete také vytvořit řídicí panel pro tým ústavní péče, který bude vizualizovat data pacienta. V tomto kurzu se naučíte, jak vytvořit řídicí panel Power BI pro streamování v reálném čase ze šablony vaší IoT Central nepřetržité aplikace pro monitorování. Pokud váš případ použití nevyžaduje přístup k datům v reálném čase, můžete použít [řídicí panel IoT Central Power BI](../core/howto-connect-powerbi.md), který má zjednodušený proces nasazení. 

:::image type="content" source="media/dashboard-gif-3.gif" alt-text="Obrázek řídicího panelu GIF":::

Základní architektura bude postupovat podle této struktury:

:::image type="content" source="media/dashboard-architecture.png" alt-text="Řídicí panel třídění poskytovatele":::

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Export dat z Azure IoT Central do Azure Event Hubs
> * Nastavení Power BI datové sady streamování
> * Připojení aplikace logiky k Azure Event Hubs
> * Streamování dat do Power BI z aplikace logiky
> * Vytvoření řídicího panelu v reálném čase pro důležité vlastnosti pacienta


## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Šablona aplikace pro monitorování nepřetržitého používání služby Azure IoT Central. Pokud ho ještě nemáte, můžete [šablonu aplikace nasadit](overview-iot-central-healthcare.md)pomocí postupu.

* [Obor názvů a centrum událostí](../../event-hubs/event-hubs-create.md)pro Azure Event Hubs.

* Aplikace logiky, ke které chcete získat přístup k centru událostí Chcete-li spustit aplikaci logiky pomocí triggeru Event Hubs služby Azure, budete potřebovat [prázdnou aplikaci logiky](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Účet služba Power BI. Pokud ho ještě nemáte, můžete [pro služba Power BI vytvořit bezplatný zkušební účet](https://app.powerbi.com/). Pokud jste už Power BI ještě nepoužili, může být užitečné projít [si Začínáme s Power BI](/power-bi/service-get-started).


## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Nastavení průběžného exportu dat do Azure Event Hubs
Nejdřív budete muset nastavit průběžný export dat ze šablony aplikace Azure IoT Central do centra událostí Azure v rámci vašeho předplatného. Můžete to udělat podle kroků v tomto kurzu pro Azure IoT Central pro [Export do Event Hubs](../core/howto-export-data.md). Pro účely tohoto kurzu budete muset jenom vyexportovat pro telemetrii.


## <a name="create-a-power-bi-streaming-dataset"></a>Vytvoření datové sady streamování Power BI

1. Přihlaste se ke svému účtu Power BI.

1. V upřednostňovaném pracovním prostoru vytvořte novou datovou sadu streamování tak, že vyberete tlačítko **+ vytvořit** v pravém horním rohu panelu nástrojů. Pro každého pacienta, který chcete mít na řídicím panelu, bude nutné vytvořit samostatnou datovou sadu.

   :::image type="content" source="media/create-streaming-dataset.png" alt-text="Vytvořit datovou sadu streamování":::


1. Vyberte **rozhraní API** pro zdroj datové sady.

1. Zadejte **název** datové sady (například název pacienta) a potom vyplňte hodnoty z datového proudu. Níže uvedený příklad můžete zobrazit na základě hodnot přicházejících z simulovaných zařízení v šabloně aplikace pro monitorování nepřetržitého pacienta. Příklad obsahuje dva pacienty:

   * Teddy stříbrná, která má data z inteligentní složené závorky.
   * Yesenia Sanford, který obsahuje data z opravy s inteligentním zabezpečením.

   :::image type="content" source="media/enter-dataset-values.png" alt-text="Zadat hodnoty datové sady":::

Další informace o datových sadách streamování v Power BI najdete v článku o [streamování v reálném čase v Power BI](/power-bi/service-real-time-streaming).


## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Připojení aplikace logiky k Azure Event Hubs

Pokud chcete aplikaci logiky připojit k Azure Event Hubs, můžete postupovat podle pokynů popsaných v tomto dokumentu při [posílání událostí pomocí Azure Event Hubs a Azure Logic Apps](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action). Tady je několik navrhovaných parametrů:

|Parametr|Hodnota|
|---|---|
|Typ obsahu|application/json|
|Interval|3|
|Frekvence|Second|

Na konci tohoto kroku by měl návrhář aplikace logiky vypadat takto:

>[!div class="mx-imgBorder"] 
>![Logic Apps se připojuje Event Hubs](media/eh-logic-app.png)

:::image type="content" source="media/enter-dataset-values.png" alt-text="Zadat hodnoty datové sady":::


## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Streamování dat do Power BI z aplikace logiky

V dalším kroku budete analyzovat data přicházející z centra událostí, abyste je mohli streamovat do Power BIch datových sad, které jste vytvořili dříve.

Než to uděláte, budete muset pochopit datovou část JSON, která se posílá ze zařízení do vašeho centra událostí. Můžete to udělat tak, že si prohlédněte toto [ukázkové schéma](../core/howto-export-data.md#telemetry-format) a upravíte ho tak, aby odpovídalo vašemu schématu, nebo pomocí [Průzkumníka Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer) zkontrolovat zprávy. Pokud používáte aplikace pro monitorování nepřetržitého pacienta, vaše zprávy budou vypadat takto:

**Inteligentní telemetrie opravy – nedůležité**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Telemetrie inteligentních kolenních závorek**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Vlastnosti**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

1. Teď, když jste zkontrolovali datové části JSON, vraťte se zpátky do svého návrháře aplikace logiky a vyberte **+ Nový krok**. V dalším kroku vyhledejte a přidejte **proměnnou Initialize** a zadejte následující parametry:

   |Parametr|Hodnota|
   |---|---|
   |Název|Název rozhraní|
   |Typ|Řetězec|

   Vyberte **Uložit**. 

1. Přidejte další proměnnou s názvem **text** jako **řetězec**. Vaše aplikace logiky bude mít přidané tyto akce:

   :::image type="content" source="media/initialize-string-variables.png" alt-text="Inicializovat proměnné":::
    
1. Vyberte **+ Nový krok** a přidejte akci **analyzovat JSON** . Přejmenujte tuto **vlastnost pro analýzu vlastností**. V části Obsah vyberte **vlastnosti** přicházející z centra událostí. Vyberte možnost **použít ukázkovou datovou část k vygenerování schématu** v dolní části a vložte ukázkovou datovou část z výše uvedeného oddílu vlastností.

1. Dále vyberte akci **nastavit proměnnou** a aktualizujte proměnnou **název rozhraní** pomocí **iothub-Interface-Name** z analyzovaných vlastností JSON.

1. Jako další akci přidejte ovládací prvek **rozdělení** a jako parametr on vyberte proměnnou **název rozhraní** . Použijete ho k trychtýři dat na správnou datovou sadu.

1. V aplikaci Azure IoT Central vyhledejte v zobrazení **šablony zařízení** název rozhraní pro data o stavu opravy s automatickými opravami a základní data. Vytvořte dva různé případy pro ovládací prvek **přepínač** pro každý název rozhraní a odpovídajícím způsobem tento ovládací prvek přejmenujte. Můžete nastavit výchozí případ pro použití ovládacího prvku **ukončit** a zvolit, jaký stav chcete zobrazit.

   :::image type="content" source="media/split-by-interface.png" alt-text="Rozdělit ovládací prvek":::

1. V případě **oprav inteligentních** zásad, které je potřeba provést, přidejte akci **analyzovat JSON** . V části Obsah vyberte **obsah** přicházející z centra událostí. Zkopírujte a vložte ukázkovou datovou část pro opravu s inteligentními opravami, abyste mohli schéma vytvořit.

1. Přidejte akci **nastavit proměnnou** a aktualizujte proměnnou **body** s **textem** z analyzovaného formátu JSON v kroku 7.

1. Jako další akci přidejte ovládací prvek **Podmínka** a nastavte podmínku na **tělo**, **obsahuje**, **HeartRate**. Tím zajistíte, že máte správnou sadu dat, která přichází z opravy s inteligentními opravami před naplněním Power BI datové sady. Kroky 7-9 budou vypadat takto:

   :::image type="content" source="media/smart-vitals-pbi.png" alt-text="Chytry důležité přidat podmínku":::

1. Pro **skutečný** případ podmínky přidejte akci, která volá **Přidání řádků do datové sady** Power BI funkcionalita. K tomu se budete muset přihlašovat Power BI. Váš **falešný** případ může znovu použít ovládací prvek **ukončení** .

1. Vyberte vhodný **pracovní prostor**, **datovou sadu** a **tabulku**. Namapujte parametry, které jste zadali při vytváření datové sady streamování v Power BI, do analyzovaných hodnot JSON, které pocházejí z vašeho centra událostí. Vaše vyplněné akce by měly vypadat takto:
 
   :::image type="content" source="media/add-rows-yesenia.png" alt-text="Přidat řádky do Power BI":::

1. Pro případ přepínače **inteligentní složené závorky** , přidejte akci **analýzy JSON** pro analýzu obsahu, podobně jako v kroku 7. Pak **přidejte řádky do datové sady** , abyste aktualizovali datovou sadu Teddy stříbrt v Power BI.

   :::image type="content" source="media/knee-brace-pbi.png" alt-text="Snímek obrazovky, který ukazuje, jak přidat řádky do datových sad":::

1. Stiskněte **Uložit** a pak spusťte aplikaci logiky.


## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Vytvoření řídicího panelu v reálném čase pro důležité vlastnosti pacienta

Nyní se vraťte na Power BI a vyberte **+ vytvořit** pro vytvoření nového **řídicího panelu**. Dejte řídicímu panelu název a stiskněte **vytvořit**.

V horním navigačním panelu vyberte tři tečky a pak vyberte **+ Přidat dlaždici**.

:::image type="content" source="media/add-tile.png" alt-text="Přidat dlaždici na řídicí panel":::

Vyberte typ dlaždice, kterou chcete přidat, a upravte aplikaci, kterou chcete.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte prostředky pomocí následujících kroků:

1. Z Azure Portal můžete odstranit centrum událostí a prostředky Logic Apps, které jste vytvořili.

1. Pro aplikaci IoT Central otevřete kartu Správa a vyberte **Odstranit**.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Doprovodné materiály k architektuře monitorování nepřetržitého pacienta](concept-continuous-patient-monitoring-architecture.md)