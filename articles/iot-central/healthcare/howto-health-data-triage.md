---
title: Vytvoření řídicího panelu třídění dat stavu pomocí Azure IoT Central | Dokumenty společnosti Microsoft
description: Naučte se vytvářet řídicí panel třídění dat stavu pomocí šablon aplikací Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 99b27ec53d955079b5f73986408e698955c0969b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021640"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Kurz: Vytvoření řídicího panelu poskytovatele Power BI



Při vytváření řešení nepřetržitého monitorování pacientů můžete také vytvořit řídicí panel pro tým nemocniční péče pro vizualizaci dat pacientů. V tomto kurzu se dozvíte, jak vytvořit řídicí panel streamování Power BI v reálném čase z šablony aplikace průběžného monitorování pacientů ioT Central.

>[!div class="mx-imgBorder"]
>![Řídicí panel GIF](media/dashboard-gif-3.gif)

Základní architektura bude následovat tuto strukturu:

>[!div class="mx-imgBorder"] 
>![Řídicí panel třídění zprostředkovatele](media/dashboard-architecture.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Export dat z Azure IoT Central do Azure Event Hubs
> * Nastavení datové sady streamování Power BI
> * Připojení aplikace logiky k Centru událostí Azure
> * Streamování dat do Power BI z aplikace Logika
> * Vytvoření řídicího panelu v reálném čase pro životní funkce pacientů

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Šablona aplikace průběžného monitorování pacientů Azure IoT Central. Pokud ji ještě nemáte, můžete postupovat podle pokynů k [nasazení šablony aplikace](overview-iot-central-healthcare.md).

* Obor [názvů Centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)Azure a Centrum událostí .

* Aplikace logiky, které chcete získat přístup k centru událostí. Chcete-li spustit aplikaci logiky s aktivační událostí Azure, budete potřebovat [prázdnou aplikaci logiky](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow).

* Účet služby Power BI. Pokud ho ještě nemáte, můžete [si vytvořit bezplatný zkušební účet pro službu Power BI](https://app.powerbi.com/). Pokud jste Power BI ještě nepoužívali, může být užitečné jít přes [Začínáme s Power BI](https://docs.microsoft.com/power-bi/service-get-started).

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Nastavení průběžného exportu dat do Centra událostí Azure
Nejdřív budete muset nastavit nepřetržitý export dat ze šablony aplikace Azure IoT Central do Centra událostí Azure ve vašem předplatném. Můžete tak učinit podle kroků v tomto kurzu Azure IoT Central pro [export do centra událostí](https://docs.microsoft.com/azure/iot-central/core/howto-export-data). Budete potřebovat pouze export pro telemetrii pro účely tohoto kurzu.

## <a name="create-a-power-bi-streaming-dataset"></a>Vytvoření datové sady streamování Power BI

1. Přihlaste se ke svému účtu Power BI.

2. V upřednostňovaném pracovním prostoru vytvořte novou datovou sadu datových proudů výběrem tlačítka **+ Vytvořit** v pravém horním rohu panelu nástrojů. Budete muset vytvořit samostatnou datovou sadu pro každého pacienta, který chcete mít na řídicím panelu.

    >[!div class="mx-imgBorder"] 
    >![Vytvoření datové sady datových proudů](media/create-streaming-dataset.png)

3. Pro zdroj datové sady zvolte **rozhraní API.**

4. Zadejte **jméno** (například jméno pacienta) pro vaši datovou sadu a vyplňte hodnoty z datového proudu. Níže zobrazený příklad založený na hodnotách pocházejících ze simulovaných zařízení v šabloně aplikace průběžného monitorování pacientů. Příklad má dva pacienty:

    * Teddy Silvers, který má data z Smart Knee Brace
    * Yesenia Sanford, která má data z opravy Smart Vitals

    >[!div class="mx-imgBorder"] 
    >![Zadání hodnot datové sady](media/enter-dataset-values.png)

Další informace o datových sadách datových datových datových vysílání v Power BI najdete v tomto dokumentu ve [streamovacím vysílání v Power BI](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Připojení aplikace logiky k Centru událostí Azure
Chcete-li připojit aplikaci logiky k centru událostí Azure, můžete postupovat podle pokynů uvedených v tomto dokumentu při [odesílání událostí pomocí Azure Event Hubs a Azure Logic Apps](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Zde jsou některé navrhované parametry:

|Parametr|Hodnota|
|---|---|
|Typ obsahu|application/json|
|Interval|3|
|Frequency|1 sekunda|

Na konci tohoto kroku by měl návrhář aplikace logiky vypadat takto:

>[!div class="mx-imgBorder"] 
>![Aplikace logiky se připojují k centru událostí](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Streamování dat do Power BI z aplikace Logika
Dalším krokem bude analýza dat přicházejících z centra událostí a jejich vysílání do datových sad Power BI, které jste dříve vytvořili.

1. Než to budete moci provést, budete muset pochopit datovou část JSON, která se odesílá z vašeho zařízení do centra událostí. Můžete tak učinit tak, že se podíváte na toto [ukázkové schéma](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) a upravíte ho tak, aby odpovídalo schématu, nebo pomocí [průzkumníka service bus](https://github.com/paolosalvatori/ServiceBusExplorer) ke kontrole zpráv. Pokud používáte aplikace průběžného monitorování pacientů, budou vaše zprávy vypadat takto:

**Telemetrie smart Vitals Patch**

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

**Telemetrie inteligentní kolenní ortézy**

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

2. Nyní, když jste zkontrolovali datové části JSON, vraťte se do návrháře aplikací logiky a vyberte **+ Nový krok**. Jako další krok vyhledejte a přidejte **proměnnou Initialize** a zadejte následující parametry:

    |Parametr|Hodnota|
    |---|---|
    |Name (Název)|Název rozhraní|
    |Typ|Řetězec|

    Klikněte na **Uložit**. 

3. Přidejte další proměnnou nazvanou **Text** s typem jako **řetězec**. Vaše aplikace logiky bude mít tyto akce přidány:

    >[!div class="mx-imgBorder"]
    >![Inicializovat proměnné](media/initialize-string-variables.png)
    
4. Vyberte **+ Nový krok** a přidejte akci Analyzovat **JSON.** Přejmenujte to na **Analyzovat vlastnosti**. Pro obsah zvolte **Vlastnosti** pocházející z centra událostí. Vyberte **Použít ukázkovou datovou část ke generování schématu** v dolní části a vložte datovou část vzorku z výše uvedené části Vlastnosti.

5. Dále zvolte akci **Nastavit proměnnou** a aktualizujte proměnnou **Název rozhraní** s **názvem iothub-interface** z analyzovaných vlastností JSON.

6. Přidejte jako další akci **rozdělit** ovládací prvek a jako parametr On zvolte proměnnou **Název rozhraní.** Tato možnost slouží k přelévání dat do správné datové sady.

7. V aplikaci Azure IoT Central najděte název rozhraní pro data o stavu oprav inteligentních životních funkcí a data o stavu inteligentní kolenní ortézy v zobrazení **Šablony zařízení.** Vytvořte dva různé případy pro **ovládací prvek přepínače** pro každý název rozhraní a odpovídajícím způsobem přejmenujte ovládací prvek. Můžete nastavit výchozí případ pro použití **ovládacího prvku Ukončit** a zvolit, jaký stav chcete zobrazit.

    >[!div class="mx-imgBorder"] 
    >![Rozdělit ovládací prvek](media/split-by-interface.png)

8. V případě **opravy Smart Vitals** přidejte akci **Parse JSON.** Pro obsah zvolte **Obsah** pocházející z Centra událostí. Zkopírujte a vložte ukázkové datové části pro výše uvedenou opravu Smart Vitals, abyste vygenerovali schéma.

9. Přidejte akci **Nastavit proměnnou** a aktualizujte proměnnou **Body** s **tělem** z analyzovaného jsonu v kroku 7.

10. Přidejte funkci Řízení **stavu** jako další akci a nastavte podmínku na **hodnotu Tělo**, **obsahuje** **hodnotu HeartRate**. Tím zajistíte, že budete mít správnou sadu dat pocházejících z opravy Inteligentní vitals před vyplněním datové sady Power BI. Kroky 7-9 budou vypadat takto:

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals přidat podmínku](media/smart-vitals-pbi.png)

11. Pro **skutečný** případ podmínky přidejte akci, která volá **Přidat řádky do** funkce Datové sady Power BI. Za tímto účelem se budete muset přihlásit k Power BI. Váš **případ False** můžete znovu použít **Terminate** ovládací prvek.

12. Zvolte příslušný **pracovní prostor**, **datovou sadu**a **tabulku**. Namapujte parametry, které jste zadali při vytváření datové sady datových proudů v Power BI, na analyzované hodnoty JSON, které přicházejí z centra událostí. Vaše vyplněné akce by měly vypadat takto:

    >[!div class="mx-imgBorder"] 
    >![Přidání řádků do Power BI](media/add-rows-yesenia.png)

13. Pro **případ přepínače smart kolenní ortézy** přidejte akci **Parse JSON,** abyste obsah analyzovat, podobně jako krok 7. Pak **přidejte řádky do datové sady** a aktualizujte datovou sadu Teddy Silvers v Power BI.

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals přidat podmínku](media/knee-brace-pbi.png)

14. Stiskněte **Save** a spusťte aplikaci Logika.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Vytvoření řídicího panelu v reálném čase pro životní funkce pacientů
Teď se vraťte k Power BI a vyberte **+ Vytvořit** a vytvořte nový **řídicí panel**. Pojmenujte řídicí panel a stiskněte **tlačítko Vytvořit**.

Vyberte tři tečky v horním navigačním panelu a pak vyberte **+ Přidat dlaždici**.

>[!div class="mx-imgBorder"] 
>![Přidání dlaždice na řídicí panel](media/add-tile.png)

Vyberte typ dlaždice, kterou chcete přidat a přizpůsobit aplikaci, jak chcete.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tuto aplikaci, odstraňte prostředky pomocí následujících kroků:

1. Z webu Azure Portal můžete odstranit centrum událostí a prostředky Logic Apps, které jste vytvořili.

2. Pro vaši aplikaci IoT Central přejděte na kartu Správa a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

* Projděte si [pokyny k průběžnému monitorování architektury pacientů](concept-continuous-patient-monitoring-architecture.md).
