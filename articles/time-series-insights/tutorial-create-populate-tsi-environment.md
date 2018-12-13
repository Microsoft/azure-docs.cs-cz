---
title: Vytvoření prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Přečtěte si, jak vytvořit prostředí Time Series Insights naplněné daty ze simulovaných zařízení.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 1a5f9901dfb3b08a068d1051b7ea434f1d5d5412
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278487"
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

V tomto videu si ukážeme, jak generovat data, která pomáhají v začátcích s Time Series Insights, pomocí akcelerátoru řešení Azure IoT.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

Váš přihlašovací účet Azure také musí mít v rámci předplatného roli Vlastník. Podrobnosti najdete v tématu věnovaném [správě přístupu pomocí RBAC a portálu Azure Portal](/azure/role-based-access-control/role-assignments-portal)

## <a name="overview"></a>Přehled

Prostředí TSI je místo, ve kterém se shromažďují a ukládají data ze zařízení. Po uložení zde můžete data dotazovat a analyzovat pomocí [Průzkumníka TSI](time-series-quickstart.md) a [rozhraní API pro dotazy TSI](/rest/api/time-series-insights/time-series-insights-reference-queryapi).

Stejně jako u všech zařízení (simulovaných nebo fyzických), IoT Hub představuje bod připojení, pomocí něhož se mohou zařízení bezpečně připojovat ke cloudu Azure a přenášet do něj data. Jak je popsáno v [přehledu služby TSI](time-series-insights-overview.md), IoT Hub také funguje jako zdroj události pro streamování dat do prostředí TSI. 

V tomto kurzu se dále využívá [akcelerátor řešení IoT](/azure/iot-accelerators/), který generuje a streamuje ukázková telemetrická data do IoT Hubu. Akcelerátory řešení IoT poskytují předkonfigurovaná řešení na úrovni podniků, jejichž účelem je zrychlit vývoj vlastních řešení IoT. 

## <a name="create-a-tsi-environment"></a>Vytvoření prostředí TSI

Nejprve vytvořte prostředí TSI v rámci předplatného Azure:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu předplatného Azure.  
2. Vlevo nahoře vyberte **+ Vytvořit prostředek**.  
3. Vyberte kategorii **Internet věcí** a potom zvolte **Time Series Insights**.  
   
   [![Výběr prostředku prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. Na stránce **Prostředí Time Series Insights** vyplňte požadované parametry:
   
   Parametr|Popis
   ---|---
   **Název prostředí** | Pro prostředí TSI vyberte jedinečný název. Název využívá průzkumník TSI a rozhraní API pro dotazy.
   **Předplatné** | Předplatná představují kontejnery prostředků Azure. Vyberte předplatné, ve kterém chcete prostředí TSI vytvořit.
   **Skupina prostředků** | Skupina prostředků představuje kontejner prostředků Azure. Pro prostředek vašeho prostředí TSI vyberte existující skupinu, nebo vytvořte novou.
   **Umístění** | Pro prostředí TSI vyberte oblast datového centra. Pokud se chcete vyhnout nákladům na šířku pásma a latenci, doporučuje se, aby se prostředí TSI nacházelo ve stejné oblasti jako další prostředky IoT.
   **Ceny skladových položek** | Vyberte potřebnou propustnost. Pokud chcete využít nejnižší náklady a počáteční kapacitu, zvolte S1.
   **Kapacita** | Kapacita představuje násobitel, který se aplikuje na rychlost příchozích přenosů, úložnou kapacitu a náklady spojené s vybranou skladovou položkou.  Kapacitu prostředí můžete po vytvoření změnit. Pokud preferujete nejnižší náklady, nastavte 1. 

   Po dokončení zahajte proces zřizování kliknutím na **Vytvořit**.

   ![Vytvoření prostředku prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. Dokončení nasazení by mělo trvat necelou minutu a můžete ho sledovat na panelu **Oznámení**:  

   ![Nasazení prostředí Time Series Insights bylo úspěšné.](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

Dalším krokem je vytvoření řešení simulace zařízení, které vygeneruje testovací data k naplnění prostředí TSI:

1. Otevřete samostatné okno nebo kartu, přejděte na https://www.azureiotsolutions.com, přihlaste se pomocí stejného účtu předplatného Azure a vyberte akcelerátor Simulace zařízení:

   ![Spuštění akcelerátoru Simulace zařízení](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. Na stránce **Vytvoření řešení Simulace zařízení** zadejte požadované parametry:

   Parametr|Popis
   ---|---
   **Název řešení** | Jedinečná hodnota sloužící k vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků.
   **Předplatné** | Vyberte stejné předplatné, jako jste použili k vytvoření prostředí TSI v předchozí části.
   **Oblast** | Vyberte stejnou oblast, jako jste použili k vytvoření prostředí TSI v předchozí části. 
   **Nasadit nepovinné prostředky Azure** | Položku **IoT Hub** nechte zaškrtnutou, protože bude sloužit k připojování a streamování dat simulovaných zařízení.

   Po dokončení klikněte na **Vytvořit řešení**. Do 6–7 minut se pro toto řešení zřídí prostředky Azure.

   ![Zřízení řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. Jakmile se zřizování dokončí, změní se text nad novým řešením ze „Zřizuje se...“ na „Připraveno“.

   >[!IMPORTANT]
   > Zatím neklikejte na tlačítko **Spustit**! Tuto stránku ale ještě nechce otevřenou, protože se sem vrátíte později.

   ![Zřizování řešení simulace zařízení dokončeno](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. Nyní se vraťte na web Azure Portal a prohlédněte si nově vytvořené prostředky v rámci vašeho předplatného. Na stránce **Skupiny prostředků** uvidíte novou skupinu prostředků vytvořenou na základě **názvu řešení** zadaného v předchozím kroku. Všimněte si i prostředků vytvořených pro podporu řešení simulace zařízení:

   [![Prostředky řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Připojení prostředí TSI k IoT Hubu 

V tuto chvíli už víte, jak vytvořit dvě sady prostředků, a to každou v rámci vlastní skupiny prostředků: 
- Prázdné prostředí TSI 
- Prostředky řešení simulace zařízení včetně IoT Hubu vygenerované akcelerátorem řešení

Nezapomeňte, že aby mohla simulovaná zařízení streamovat data zařízení, musí být k IoT Hubu připojená. Pro předávání dat do prostředí TSI je nutné změnit konfiguraci IoT Hubu i prostředí TSI. 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Konfigurace IoT Hubu: definice skupiny uživatelů

IoT Hub nabízí různé koncové body pro sdílení funkcí s jinými objekty actor. Koncový bod Události se streamuje do instance IoT Hubu a umožňuje tak ostatním aplikacím využívat data. Skupiny uživatelů konkrétně poskytují mechanismus, pomocí něhož mohou aplikace poslouchat a získávat data z IoT Hubu. 

Nyní v koncovém bodu Události v IoT Hubu vašeho řešení simulace zařízení definujete novou vlastnost Skupina uživatelů. 

1. Na webu Azure Portal přejděte na stránku **Přehled** skupiny prostředků, kterou jste vytvořili pro řešení simulace zařízení, a potom vyberte prostředek IoT Hubu:

   [![Skupina prostředků řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Poznamenejte si také **název** prostředku IoT Hubu vygenerovaného pro vaše řešení – později ho budete potřebovat.

2. Posuňte se dolů, vyberte stránku **Koncové body** a potom vyberte koncový bod **Události**. Na stránce **Vlastnosti** tohoto koncového bodu zadejte u skupiny uživatelů s označením „$Default“ jedinečný název koncového bodu a potom klikněte na **Uložit**:

   [![Koncové body IoT Hubu pro řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>Konfigurace prostředí TSI: definice zdroje události

Nyní připojte nový koncový bod událostí v IoT Hubu s označením Skupina uživatelů k prostředí TSI jako zdroj událostí.

1. Přejděte na stránku **Přehled** skupiny prostředků, kterou jste vytvořili pro vaše prostředí TSI, a potom prostředí vyberte:

   [![Skupina prostředků a prostředí pro prostředí TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. Na stránce prostředí TSI vyberte **Zdroje událostí** a potom klikněte na **+ Přidat**:

   ![Přehled prostředí TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. Na stránce **Nový zdroj událostí** zadejte požadované parametry: 

   Parametr|Popis
   ---|---
   **Název zdroje událostí** | Pro název zdroje událostí zadejte jedinečnou hodnotu.
   **Zdroj** | Vyberte „IoT Hub“.
   **Možnost importu** | Ponechejte výchozí možnost „Použít centrum IoT Hub z dostupných předplatných“. Další rozevírací seznam se naplní dostupnými předplatnými.
   **Předplatné** | Vyberte stejné předplatné, pomocí kterého jste vytvořili prostředí TSI a prostředky simulace zařízení.
   **Název centra IoT Hub** | Měl by být nastaven název IoT Hubu, který jste si poznamenali dříve. Pokud tomu tak není, zadejte správný název.
   **Název zásady centra IoT Hub** | Ponechejte výchozí hodnotu „iothubowner“.
   **Skupina uživatelů centra IoT Hub** | Měl by být nastaven název skupiny uživatelů IoT Hubu, kterou jste vytvořili dříve. Pokud tomu tak není, vyberte správný název skupiny uživatelů. 
   **Formát serializace události** | Nechejte nastavenou výchozí hodnotu JSON.
   **Název vlastnosti časového razítka** | Zadejte jako „časové razítko“.

   Jakmile budete mít hotovo, přidejte zdroj událostí kliknutím na **Vytvořit**. Když se vrátíte na stránku **Přehled** skupiny prostředků, zobrazí se společně s prostředkem prostředí TSI nový prostředek „Zdroj událostí Time Series Insights“.

   ![Nový zdroj událostí prostředí TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>Spuštění simulace zařízení pro streamování dat do prostředí TSI

Veškerá konfigurace je nyní hotová a je čas naplnit prostředí TSI ukázkovými daty ze simulovaných zařízení.

Možná si vzpomínáte, že jste v části [Vytvoření simulace zařízení](#create-a-device-simulation) vytvořili prostřednictvím akcelerátoru několik prostředků Azure pro podporu vašeho řešení. Kromě IoT Hubu popsaného výše se pro vytvoření a přenos telemetrie simulovaného zařízení vygenerovala webová aplikace služby Azure App Service.

1. Vraťte se zpět na [Řídicí panel akcelerátorů řešení](https://www.azureiotsolutions.com/Accelerators#dashboard). Podle potřeby se znovu přihlaste pomocí stejného účtu Azure, jako používáte v tomto kurzu. Teď můžete kliknout na tlačítko **Spustit** v části řešení Simulace zařízení:

     ![Řídicí panel akcelerátorů řešení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. Nyní se spustí webová aplikace simulace zařízení (při prvním načtení to může trvat několik sekund). Také se zobrazí výzva k vyjádření souhlasu, aby bylo webové aplikaci uděleno oprávnění „Vaše přihlášení a čtení vašeho profilu“. Toto oprávnění umožní aplikaci získat informace o profilu uživatele nutné pro její správné fungování:

     ![Souhlas webové aplikaci pro simulaci zařízení](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. Po načtení stránky **Simulation setup** („Nastavení simulace“) zadejte požadované parametry: 

   Parametr|Popis
   ---|---
   **Target IoT Hub** („Cílový IoT Hub“) | Použijte možnost Use pre-provisioned IoT Hub („Použít předem zřízený IoT Hub“).
   **Device model** („Model zařízení“) | Vyberte možnost Chiller („Chladič“).
   **Number of devices** („Počet zařízení“)  | V části **Amount** („Množství“) zadejte 1000.
   **Telemetry frequency** („Frekvence telemetrie“) | Zadejte 10 sekund.
   **Simulation duration** („Doba trvání simulace“) | Vyberte **End in:** („Skončit za:“) a zadejte 5 minut.

   Jakmile budete mít hotovo, klikněte na **Start simulation** („Spustit simulaci“). Simulace poběží celkem pět minut a každých deset sekund vygeneruje data z 1000 simulovaných zařízení.  

   ![Nastavení simulace zařízení](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. V průběhu simulace si můžete všimnout, že se přibližně každých deset sekund aktualizují pole **Total messages** („Celkem zpráv“) a **Messages per second** („Zpráv za sekundu“). Simulace se dokončí asi za pět minut a znovu se zobrazí stránka **Simulation setup** („Nastavení simulace“).

   ![Spuštěná simulace zařízení](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>Ověření telemetrických dat

V této poslední fázi ověříte, že se telemetrická data vygenerovala a uložila v prostředí TSI. K ověření dat použijete průzkumníka TSI, který slouží k dotazování a analyzování telemetrických dat.

1. Vraťte se na stránku **Přehled** skupiny prostředků prostředí TSI a znovu vyberte prostředí:

   [![Skupina prostředků a prostředí pro prostředí TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. Na stránce **Přehled** prostředí TSI otevřete průzkumníka TSI kliknutím na **URL průzkumníka služby Time Series Insights**.

   [![Průzkumník TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. Průzkumník TSI se načte a ověří pomocí vašeho účtu na webu Azure Portal. Při počátečním zobrazení si v oblasti grafu všimněte, že prostředí TSI se skutečně naplnilo simulovanými telemetrickými daty. Pokud chcete vyfiltrovat užší časový rozsah, vyberte rozevírací seznam v levé horní části stránky. Zadejte takový časový úsek, který pokryje dobu trvání simulace zařízení. Nakonec klikněte na ikonu lupy pro hledání:

   [![Filtrování časového rozsahu v průzkumníkovi TSI](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. Zúžení časového rozsahu umožňuje podrobně v grafu zobrazit výrazné nárůsty přenosů dat do IoT Hubu a prostředí TSI. Také si v pravém horním rohu všimněte textu **Streamování dokončeno**, u kterého se zobrazuje celkový počet nalezených událostí. Potáhnutím posuvníku **Velikost intervalu** můžete ovládat i rozlišení zobrazeného grafu:

   [![Zobrazení s filtrovaným časovým rozsahem v průzkumníku TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. A nakonec můžete kliknutím do oblasti levým tlačítkem myši vyfiltrovat rozsah, kliknout pravým tlačítkem a pomocí možnosti **Zkoumání událostí** zobrazit podrobnosti o událostech v tabulkovém zobrazení **Události**:

   [![Zobrazení s filtrovaným časovým rozsahem a událostmi v průzkumníku TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto kurzu se vytváří několik spouštěných služeb Azure pro podporu prostředí TSI a řešení simulace zařízení. Pokud chcete tuto sérii kurzů přerušit nebo dokončit později, doporučujeme odstranit všechny prostředky, aby se vám neúčtovaly zbytečné poplatky. 

V nabídce vlevo na webu Azure Portal:

1. Klikněte na ikonu **Skupiny prostředků** a vyberte skupinu prostředků, kterou jste vytvořili pro prostředí TSI. V horní části stránky klikněte na **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a potom klikněte na **Odstranit**. 
2. Klikněte na ikonu **Skupiny prostředků** a vyberte skupinu vytvořenou akcelerátorem řešení simulace zařízení. V horní části stránky klikněte na **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a potom klikněte na **Odstranit**. 

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


