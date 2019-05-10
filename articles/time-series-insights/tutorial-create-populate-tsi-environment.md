---
title: 'Kurz: Vytvoření prostředí Azure Time Series Insights | Dokumentace Microsoftu'
description: Přečtěte si, jak vytvořit prostředí Time Series Insights naplněné daty ze simulovaných zařízení.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 42a7ba0c66bd603b19d60c7b3407ae5ca80db28e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209850"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Kurz: Vytvoření prostředí Azure Time Series Insights

Tento kurz vás provede procesem vytvoření prostředí Time Series Insight (TSI) naplněného daty ze simulovaných zařízení. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prostředí TSI 
> * Vytvořit řešení simulace zařízení obsahující IoT Hub
> * Připojit prostředí TSI k IoT Hubu
> * Spustit simulaci zařízení pro streamování dat do prostředí TSI
> * Ověřit simulovaná telemetrická data

## <a name="video"></a>Video

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Další informace o použití akcelerátor řešení Azure IoT pro generování dat a začít pracovat s Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/).

* Účtu Azure přihlášení musí být také členem předplatného **vlastníka** role. Zobrazit [správě přístupu pomocí RBAC a webu Azure portal](/azure/role-based-access-control/role-assignments-portal) další podrobnosti.

## <a name="overview"></a>Přehled

Prostředí TSI je místo, ve kterém se shromažďují a ukládají data ze zařízení. Po uložení v prostředí služby TSI [Průzkumníku TSI](time-series-quickstart.md) a [rozhraní API pro dotazy služby TSI](/rest/api/time-series-insights/ga-query-api) slouží k dotazování a analyzovat data. IoT Hub je spojovací bod používá všechna zařízení (simulovaná nebo fyzická) k zabezpečenému připojení a přenášet data do cloudu Azure. [TSI přehled](time-series-insights-overview.md) zaznamená, že Azure IoT Hub také slouží jako zdroj událostí pro streamování dat do prostředí TSI. Tento kurz používá [akcelerátorů řešení IoT](/azure/iot-accelerators/) ke generování a streamování ukázková telemetrická data do služby IoT Hub.

>[!TIP]
> Akcelerátory řešení IoT poskytovat podniková předkonfigurované řešení, které vám umožní zrychlit vývoj vlastních řešení IoT.

## <a name="create-a-tsi-environment"></a>Vytvoření prostředí TSI

Nejprve vytvořte prostředí TSI v rámci předplatného Azure:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu předplatného Azure.  
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**.  
1. Vyberte kategorii **Internet věcí** a potom zvolte **Time Series Insights**.  

   [![Výběr prostředku prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na stránce **Prostředí Time Series Insights** vyplňte požadované parametry:

   Parametr|Popis
   ---|---
   **Název prostředí** | Pro prostředí TSI vyberte jedinečný název. Název společnosti používají Průzkumníku TSI a API pro dotazy.
   **Předplatné** | Předplatná představují kontejnery prostředků Azure. Vyberte předplatné, k vytvoření prostředí služby TSI.
   **Skupina prostředků** | Skupina prostředků představuje kontejner prostředků Azure. Zvolte existující skupinu prostředků nebo vytvořte novou pro prostředek prostředí TSI.
   **Umístění** | Pro prostředí TSI vyberte oblast datového centra. Pokud se chcete vyhnout nákladům na šířku pásma a latenci, doporučuje se, aby se prostředí TSI nacházelo ve stejné oblasti jako další prostředky IoT.
   **Ceny skladových položek** | Vyberte potřebnou propustnost. Na nejnižší náklady a počáteční kapacitu, vyberte `S1`.
   **Kapacita** | Kapacita představuje násobitel, který se aplikuje na rychlost příchozích přenosů, úložnou kapacitu a náklady spojené s vybranou skladovou položkou.  Kapacitu můžete změnit po vytvoření. Nejnižší náklady vyberte kapacitou 1.

   Po dokončení zahajte proces zřizování kliknutím na **Vytvořit**.

   [![Vytvořit prostředek prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Zkontrolujte **oznámení** panelu ke sledování dokončení nasazení.  

   [![Čas Series Insights prostředí nasazení bylo úspěšné](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

Dalším krokem je vytvoření řešení simulace zařízení, které vygeneruje testovací data k naplnění prostředí TSI:

1. V samostatné okna nebo karty, přejděte na [azureiotsolutions.com](https://www.azureiotsolutions.com). Přihlaste se pomocí stejného účtu předplatného Azure a vyberte **simulace zařízení** akcelerátoru.

   [![Spustit simulaci zařízení akcelerátoru](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Zadejte požadované parametry na **simulace zařízení vytvořit řešení** stránky.

   Parametr|Popis
   ---|---
   **Název řešení** | Jedinečná hodnota sloužící k vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků.
   **Předplatné** | Vyberte stejné předplatné, jako jste použili k vytvoření prostředí TSI v předchozí části.
   **Oblast** | Vyberte stejnou oblast, jako jste použili k vytvoření prostředí TSI v předchozí části.
   **Nasadit nepovinné prostředky Azure** | Položku **IoT Hub** nechte zaškrtnutou, protože bude sloužit k připojování a streamování dat simulovaných zařízení.

   Až budete hotovi, klikněte na tlačítko **vytvořit řešení** ke zřízení prostředků Azure řešení. Může trvat 6 až 7 minut na dokončení tohoto procesu.

   [![Zřízení řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Po dokončení zřizování text nad nové řešení se změní z **zřizování...**  k **připravené**:

   >[!IMPORTANT]
   > Zatím neklikejte na tlačítko **Spustit**! Tuto stránku ale ještě nechce otevřenou, protože se sem vrátíte později.

   [![Řešení pro simulaci zařízení zřizování dokončeno](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Nyní se vraťte na web Azure Portal a prohlédněte si nově vytvořené prostředky v rámci vašeho předplatného. Na stránce **Skupiny prostředků** uvidíte novou skupinu prostředků vytvořenou na základě **názvu řešení** zadaného v předchozím kroku. Všimněte si i prostředků vytvořených pro podporu řešení simulace zařízení:

   [![Prostředky řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Připojení prostředí TSI k IoT Hubu

V tuto chvíli už víte, jak vytvořit dvě sady prostředků, a to každou v rámci vlastní skupiny prostředků:

- Prázdné prostředí TSI.
- Simulace řešení prostředky zařízení, včetně služby IoT hub, vygenerovaná akcelerátoru řešení.

Nezapomeňte, že aby mohla simulovaná zařízení streamovat data zařízení, musí být k IoT Hubu připojená. Pro předávání dat do prostředí TSI je nutné změnit konfiguraci IoT Hubu i prostředí TSI.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Konfigurace IoT Hubu: definice skupiny uživatelů

IoT Hub nabízí různé koncové body pro sdílení funkcí s jinými objekty actor. Koncový bod Události se streamuje do instance IoT Hubu a umožňuje tak ostatním aplikacím využívat data. Skupiny uživatelů konkrétně poskytují mechanismus, pomocí něhož mohou aplikace poslouchat a získávat data z IoT Hubu.

V dalším kroku definujete nový **skupinu příjemců** vlastnost na řešení simulace zařízení IoT hub **koncového bodu Events**.

1. Na webu Azure Portal přejděte na stránku **Přehled** skupiny prostředků, kterou jste vytvořili pro řešení simulace zařízení, a potom vyberte prostředek IoT Hubu:

   [![Skupina prostředků řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Poznamenejte si také **název** prostředku IoT Hubu vygenerovaného pro vaše řešení – později ho budete potřebovat.

1. Posuňte se dolů, vyberte stránku **Koncové body** a potom vyberte koncový bod **Události**. Na stránce **Vlastnosti** tohoto koncového bodu zadejte u skupiny uživatelů s označením „$Default“ jedinečný název koncového bodu a potom klikněte na **Uložit**:

   [![Koncové body IoT Hubu pro řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-configuration-define-an-event-source"></a>Konfigurace služby TSI: definování zdroje událostí

Nyní může připojit novou službu IoT hub **skupinu příjemců** koncový bod událostí do prostředí TSI jako **zdroj události**.

1. Přejděte na stránku **Přehled** skupiny prostředků, kterou jste vytvořili pro vaše prostředí TSI, a potom prostředí vyberte:

   [![Skupina prostředků a prostředí pro prostředí TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Na stránce TSI prostředí vyberte **zdroje událostí**, pak klikněte na tlačítko **+ přidat**.

   [![Přehled prostředí TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Zadejte požadované parametry na **nový zdroj událostí** stránky.

   Parametr|Popis
   ---|---
   **Název zdroje událostí** | Pro název zdroje událostí zadejte jedinečnou hodnotu.
   **Zdroj** | Vyberte **služby IoT Hub**.
   **Možnost importu** | Vyberte výchozí `Use IoT hub from available subscriptions`. Další rozevírací seznam se naplní dostupnými předplatnými.
   **Předplatné** | Vyberte stejné předplatné, pomocí kterého jste vytvořili prostředí TSI a prostředky simulace zařízení.
   **Název centra IoT Hub** | Měl by být nastaven název IoT Hubu, který jste si poznamenali dříve. Pokud tomu tak není, zadejte správný název.
   **Název zásady centra IoT Hub** | Vyberte **iothubowner**.
   **Skupina uživatelů centra IoT Hub** | Měl by být nastaven název skupiny uživatelů IoT Hubu, kterou jste vytvořili dříve. Pokud tomu tak není, vyberte správný název skupiny uživatelů.
   **Formát serializace události** | Ponechte výchozí hodnotu z `JSON`.
   **Název vlastnosti časového razítka** | Zadejte jako `timestamp`.

   Jakmile budete mít hotovo, přidejte zdroj událostí kliknutím na **Vytvořit**. Když se vrátíte na stránku **Přehled** skupiny prostředků, zobrazí se společně s prostředkem prostředí TSI nový prostředek „Zdroj událostí Time Series Insights“.

   [![Nový zdroj událostí TSI prostředí](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data-into-tsi"></a>Spustit simulaci zařízení pro streamování dat do služby TSI

Veškerá konfigurace je nyní hotová a je čas naplnit prostředí TSI ukázkovými daty ze simulovaných zařízení.

Možná si vzpomínáte, že jste v části [Vytvoření simulace zařízení](#create-a-device-simulation) vytvořili prostřednictvím akcelerátoru několik prostředků Azure pro podporu vašeho řešení. Kromě IoT Hubu popsaného výše se pro vytvoření a přenos telemetrie simulovaného zařízení vygenerovala webová aplikace služby Azure App Service.

1. Vraťte se zpět na [Řídicí panel akcelerátorů řešení](https://www.azureiotsolutions.com/Accelerators#dashboard). Podle potřeby se znovu přihlaste pomocí stejného účtu Azure, jako používáte v tomto kurzu. Nyní můžete kliknout **spuštění** tlačítko v rámci řešení "Simulace zařízení".

     [![Řídicí panel akcelerátory řešení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Nyní se spustí webová aplikace simulace zařízení (při prvním načtení to může trvat několik sekund). Také se zobrazí výzva k vyjádření souhlasu, aby bylo webové aplikaci uděleno oprávnění „Vaše přihlášení a čtení vašeho profilu“. Toto oprávnění uděluje aplikací, aby získal informace profilu uživatele, která je nezbytná pro podporu fungování aplikace.

     [![Simulace zařízení, web souhlasu s aplikací](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Jednou **nastavení** stránce načítání, zadejte požadované parametry.

   Parametr|Popis
   ---|---
   **Target IoT Hub** („Cílový IoT Hub“) | Vyberte **použití předem zřízené služby IoT Hub**.
   **Device model** („Model zařízení“) | Vyberte **chladič**.
   **Number of devices** („Počet zařízení“)  | Zadejte `1000` pod **částka**.
   **Telemetry frequency** („Frekvence telemetrie“) | Zadejte `10` sekund.
   **Simulation duration** („Doba trvání simulace“) | Vyberte **končit:** a zadejte `5` minut.

   Jakmile budete mít hotovo, klikněte na **Start simulation** („Spustit simulaci“). Simulace poběží celkem pět minut a každých deset sekund vygeneruje data z 1000 simulovaných zařízení.  

   [![Instalace simulace zařízení](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. V průběhu simulace si můžete všimnout, že se přibližně každých deset sekund aktualizují pole **Total messages** („Celkem zpráv“) a **Messages per second** („Zpráv za sekundu“). Simulace se dokončí asi za pět minut a znovu se zobrazí stránka **Simulation setup** („Nastavení simulace“).

   [![Simulace zařízení se systémem](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Ověření telemetrických dat

V této poslední fázi ověříte, že se telemetrická data vygenerovala a uložila v prostředí TSI. K ověření dat použijete průzkumníka TSI, který slouží k dotazování a analyzování telemetrických dat.

1. Vraťte se do skupiny prostředků prostředí TSI **přehled** stránky. Vyberte prostředí TSI.

   [![Skupina prostředků a prostředí pro prostředí TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. V prostředí služby TSI **přehled** stránky, klikněte na tlačítko **URL Průzkumníka Time Series Insights** otevřít v Průzkumníku TSI.

   [![Průzkumník TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Průzkumník TSI se načte a ověří pomocí vašeho účtu na webu Azure Portal. Při počátečním zobrazení si v oblasti grafu všimněte, že prostředí TSI se skutečně naplnilo simulovanými telemetrickými daty. Pokud chcete vyfiltrovat užší časový rozsah, vyberte rozevírací seznam v levé horní části stránky. Zadejte takový časový úsek, který pokryje dobu trvání simulace zařízení. Pak klikněte na tlačítko Hledat zvětšení třídy.

   [![Filtrování časového rozsahu v průzkumníkovi TSI](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zúžení časového rozsahu umožňuje podrobně v grafu zobrazit výrazné nárůsty přenosů dat do IoT Hubu a prostředí TSI. Také si v pravém horním rohu všimněte textu **Streamování dokončeno**, u kterého se zobrazuje celkový počet nalezených událostí. Můžete také přetáhnout **velikost intervalu** posuvníku pod kontrolou granularitu vykreslení v grafu.

   [![Zobrazení s filtrovaným časovým rozsahem v průzkumníku TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. A konečně, můžete taky levým tlačítkem myši oblast, kterou chcete filtrovat řady, klikněte pravým tlačítkem a použít **zkoumat události** zobrazíte podrobnosti o události v tabulkovém **události** zobrazení.

   [![Zobrazení s filtrovaným časovým rozsahem a událostmi v průzkumníku TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto kurzu se vytváří několik spouštěných služeb Azure pro podporu prostředí TSI a řešení simulace zařízení. Pokud chcete tuto sérii kurzů přerušit nebo dokončit později, doporučujeme odstranit všechny prostředky, aby se vám neúčtovaly zbytečné poplatky.

V nabídce vlevo na webu Azure Portal:

1. Klikněte na ikonu **Skupiny prostředků** a vyberte skupinu prostředků, kterou jste vytvořili pro prostředí TSI. V horní části stránky klikněte na **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a potom klikněte na **Odstranit**.

1. Klikněte na ikonu **Skupiny prostředků** a vyberte skupinu vytvořenou akcelerátorem řešení simulace zařízení. V horní části stránky klikněte na tlačítko **odstranit skupinu prostředků**, zadejte název skupiny prostředků a pak klikněte na tlačítko **odstranit**

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření prostředí TSI 
> * Vytvořit řešení simulace zařízení obsahující IoT Hub
> * Připojit prostředí TSI k IoT Hubu
> * Spustit simulaci zařízení pro streamování dat do prostředí TSI
> * Ověřit simulovaná telemetrická data

Když teď víte, jak vytvořit vlastní prostředí TSI, naučte se vytvořit webovou aplikaci, která využívá data z prostředí TSI:

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)