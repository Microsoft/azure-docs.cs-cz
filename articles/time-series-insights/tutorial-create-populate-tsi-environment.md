---
title: 'Kurz: Vytvoření prostředí Azure Time Series Insights | Dokumentace Microsoftu'
description: Zjistěte, jak vytvořit prostředí Time Series Insights, který je naplněn daty ze simulovaných zařízení.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: b8b46db043113f29f559ad44855d19f0d6ca73c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244164"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Kurz: Vytvoření prostředí Azure Time Series Insights

Tento kurz vás provede procesem vytvoření prostředí Azure Time Series Insights, který je vyplněný daty ze simulovaných zařízení. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prostředí Time Series Insights.
> * Vytvoření řešení pro simulaci zařízení, která obsahuje službu IoT hub.
> * Prostředí Time Series Insights připojte ke službě IoT hub.
> * Spuštění simulace zařízení pro streamování dat do prostředí Time Series Insights.
> * Ověřte Simulovaná telemetrická data.

## <a name="video"></a>Video

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Další informace o použití akcelerátor řešení Azure IoT pro generování dat a začít pracovat s Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/).
* Účtu Azure přihlášení musí být také členem předplatného **vlastníka** role. Další informace najdete v tématu [správě přístupu pomocí řízení přístupu na základě rolí a webu Azure portal](/azure/role-based-access-control/role-assignments-portal).

## <a name="overview"></a>Přehled

Prostředí Time Series Insights je zařízení se data shromažďují a ukládají. Po uložení dat [Průzkumníka služby Azure Time Series Insights](time-series-quickstart.md) a [čas Series Insights dotazu API](/rest/api/time-series-insights/ga-query-api) slouží k dotazování a analyzovat data. Azure IoT Hub je spojovací bod používá všechna zařízení (simulovaná nebo fyzická) k zabezpečenému připojení a přenášet data do cloudu Azure. [Přehled služby Time Series Insights](time-series-insights-overview.md) zaznamená, že Azure IoT Hub také slouží jako zdroj událostí pro streamování dat do prostředí Time Series Insights. Tento kurz používá [akcelerátorů řešení IoT](/azure/iot-accelerators/) ke generování a streamování ukázková telemetrická data do služby IoT Hub.

>[!TIP]
> Akcelerátory řešení IoT poskytovat podnikové předkonfigurované řešení, která vám pomůže zrychlit vývoj vlastních řešení IoT.

## <a name="create-an-environment"></a>Vytvoření prostředí

Nejprve vytvořte prostředí Time Series Insights ve vašem předplatném Azure.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí svého účtu předplatného Azure. 
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**. 
1. Vyberte **Internet of Things** kategorie a pak vyberte **Time Series Insights**. 

   [![Výběr prostředku prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na **prostředí Time Series Insights** stránce, vyplňte požadované parametry.

   Parametr|Popis
   ---|---
   **Název prostředí** | Vyberte jedinečný název pro prostředí Time Series Insights. Názvy jsou použity v Průzkumníku Time Series Insights a API pro dotazy.
   **Předplatné** | Předplatná představují kontejnery prostředků Azure. Vyberte předplatné, k vytvoření prostředí Time Series Insights.
   **Skupina prostředků** | Skupina prostředků představuje kontejner prostředků Azure. Zvolte existující skupinu prostředků nebo vytvořte novou pro prostředek prostředí Time Series Insights.
   **Umístění** | Zvolte oblast System center data pro vaše prostředí Time Series Insights. Aby se zabránilo náklady na přidání šířku pásma a čekací doba, zachovat prostředí Time Series Insights ve stejné oblasti jako ostatní prostředky IoT.
   **Ceny skladových položek** | Vyberte potřebnou propustnost. Na nejnižší náklady a počáteční kapacitu, vyberte `S1`.
   **Kapacita** | Kapacita představuje násobitel, který se aplikuje na rychlost příchozích přenosů, úložnou kapacitu a náklady spojené s vybranou skladovou položkou. Kapacitu můžete změnit po vytvoření. Nejnižší náklady vyberte kapacitou 1.

   Až budete hotovi, vyberte **vytvořit** zahájíte proces zřizování.

   [![Vytvořit prostředek prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Zkontrolujte **oznámení** panelu ke sledování dokončení nasazení. 

   [![Čas Series Insights prostředí nasazení bylo úspěšné](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

Dále vytvořte řešení pro simulaci zařízení, která generuje testovací data pro naplnění prostředí Time Series Insights.

1. V samostatném okně nebo záložce, přejděte na [azureiotsolutions.com](https://www.azureiotsolutions.com). Přihlaste se pomocí stejného účtu předplatného Azure a vyberte **simulace zařízení** akcelerátoru.

   [![Spustit simulaci zařízení akcelerátoru](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Zadejte požadované parametry na **simulace zařízení vytvořit řešení** stránky.

   Parametr|Popis
   ---|---
   **Název řešení** | Tato jedinečná hodnota se používá k vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků.
   **Předplatné** | Zadejte předplatným, která byla použita k vytvoření prostředí Time Series Insights v předchozí části.
   **Oblast** | Zadejte stejné oblasti, která byla použita k vytvoření prostředí Time Series Insights v předchozí části.
   **Nasadit nepovinné prostředky Azure** | Ponechte **služby IoT Hub** zaškrtnuto. Simulovaná zařízení ji použít k připojení nebo Streamovat data.

   Jakmile budete hotovi, vyberte **vytvořit řešení** ke zřízení prostředků Azure řešení. Může trvat 6 až 7 minut na dokončení tohoto procesu.

   [![Zřízení řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Po dokončení zřizování text nad nové řešení se změní z **zřizování** k **připravené**.

   >[!IMPORTANT]
   > Nevybírejte **spuštění** ještě! Tato webová stránka nechte otevřené, protože k němu budete později vrátit.

   [![Řešení pro simulaci zařízení zřizování dokončeno](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Nyní přejděte zpět na web Azure Portal a zkontrolujte nově vytvořené prostředky ve vašem předplatném. Na portálu **skupiny prostředků** stránky si všimněte, že nová skupina prostředků se vytvořila pomocí **název řešení** uvedené v předchozím kroku. Všimněte si také všechny prostředky, které byly vytvořeny pro podporu řešení pro simulaci zařízení.

   [![Prostředky řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-environment-to-the-iot-hub"></a>Připojení prostředí k centru IoT

V tuto chvíli už víte, jak vytvořit dvě sady prostředků, a to každou v rámci vlastní skupiny prostředků:

- Prázdné prostředí Time Series Insights.
- Prostředky řešení simulace zařízení, které zahrnuje službu IoT hub, vygenerovaná akcelerátoru řešení.

Nezapomeňte, že aby mohla simulovaná zařízení streamovat data zařízení, musí být k IoT Hubu připojená. Tok dat do prostředí Time Series Insights, budete muset provést změny konfigurace služby IoT hub a prostředí Time Series Insights.

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Konfigurace centra IoT: Definujte skupinu příjemců

IoT Hub poskytuje různé koncové body ke sdílení funkcí na jiné objekty actor. Koncový bod "Události" poskytuje způsob, jak další aplikace využívají data, jako je Streamovat do centra IoT instance. Konkrétně "skupiny uživatelů" poskytují mechanismus pro aplikace, které chcete naslouchat a získání dat ze služby IoT hub.

Potom definujte nový **skupinu příjemců** vlastnost u řešení pro simulaci zařízení IoT hub **koncového bodu Events**.

1. Na webu Azure Portal, přejděte **přehled** stránce skupiny prostředků, kterou jste vytvořili pro řešení pro simulaci zařízení. Vyberte prostředek centra IoT.

   [![Skupina prostředků řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Poznamenejte si **název** vygenerovaný pro řešení služby IoT Hub prostředku. Budete odkazovat na ni později.

1. Přejděte dolů a vyberte možnost **koncové body** stránce a pak vyberte **události** koncového bodu. Na koncovém bodu **vlastnosti** stránky, zadejte jedinečný název pro koncový bod služby v rámci skupiny příjemců "$Default". Vyberte **Uložit**.

   [![Koncové body IoT Hubu pro řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="environment-configuration-define-an-event-source"></a>Konfigurace prostředí: definování zdroje událostí

Nyní, připojit novou službu IoT hub **skupinu příjemců** koncový bod událostí do prostředí Time Series Insights jako **zdroj události**.

1. Přejděte **přehled** stránce skupiny prostředků, kterou jste vytvořili pro prostředí Time Series Insights. Vyberte prostředí Time Series Insights.

   [![Skupina prostředků prostředí času Series Insights a prostředí](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

1. Na stránce prostředí Time Series Insights, vyberte možnost **zdroje událostí**. Potom vyberte **+ přidat**.

   [![Přehled prostředí čas Series Insights](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png#lightbox)

1. Zadejte požadované parametry na **nový zdroj událostí** stránky.

   Parametr|Popis
   ---|---
   **Název zdroje událostí** | Pro název zdroje událostí zadejte jedinečnou hodnotu.
   **Zdroj** | Vyberte **služby IoT Hub**.
   **Možnost importu** | Vyberte výchozí `Use IoT hub from available subscriptions`. Tato možnost způsobí, že další rozevíracího seznamu, který se má naplnit dostupná předplatná.
   **Předplatné** | Vyberte stejné předplatné, ve které jste vytvořili prostředí Time Series Insights a prostředky pro simulaci zařízení.
   **Název centra IoT Hub** | Měl by být nastaven název IoT Hubu, který jste si poznamenali dříve. Pokud tomu tak není, zadejte správný název.
   **Název zásady centra IoT Hub** | Vyberte **iothubowner**.
   **Skupina uživatelů centra IoT Hub** | Měl by být nastaven název skupiny uživatelů IoT Hubu, kterou jste vytvořili dříve. Pokud tomu tak není, vyberte správný název skupiny uživatelů.
   **Formát serializace události** | Ponechte výchozí hodnotu z `JSON`.
   **Název vlastnosti časového razítka** | Zadejte jako `timestamp`.

   Jakmile budete hotovi, vyberte **vytvořit** přidání zdroje událostí. Po návratu do skupiny prostředků **přehled** stránka, spolu s váš prostředek prostředí Time Series Insights se zobrazí nový prostředek "Zdroje událostí Time Series Insights".

   [![Series Insights prostředí nový zdroj událostí Time](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Spustit simulaci zařízení pro streamování dat

Teď, když všechnu práci konfigurace dokončí, je čas k naplnění prostředí Time Series Insights s ukázkovými daty ze simulovaných zařízení.

Možná pamatujete z [vytvořte oddíl simulace zařízení](#create-a-device-simulation), několik prostředků Azure, které byly vytvořeny akcelerátoru pro podporu řešení. Kromě IoT Hubu popsaného výše se pro vytvoření a přenos telemetrie simulovaného zařízení vygenerovala webová aplikace služby Azure App Service.

1. Vraťte se zpět na [Řídicí panel akcelerátorů řešení](https://www.azureiotsolutions.com/Accelerators#dashboard). Přihlaste se znovu, v případě potřeby pomocí stejného účtu Azure, které jste používali v tomto kurzu. Teď můžete vybrat **spuštění** v rámci řešení "Simulace zařízení".

     [![Řídicí panel akcelerátory řešení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Zařízení simulace webové aplikace se spustí v tomto bodě a může trvat několik sekund po počátečním načtení. Můžete se také výzva k zadání souhlas udělit webové aplikace "přihlášení a čtení vašeho profilu" oprávnění. Toto oprávnění uděluje aplikací, aby získal informace profilu uživatele, která je nezbytná pro podporu fungování aplikace.

     [![Simulace zařízení, web souhlasu s aplikací](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Po **nastavení** stránce načítání, zadejte požadované parametry.

   Parametr|Popis
   ---|---
   **Target IoT Hub** („Cílový IoT Hub“) | Vyberte **použití předem zřízené služby IoT Hub**.
   **Device model** („Model zařízení“) | Vyberte **chladič**.
   **Number of devices** („Počet zařízení“)  | Zadejte `1000` pod **částka**.
   **Telemetry frequency** („Frekvence telemetrie“) | Zadejte `10` sekund.
   **Simulation duration** („Doba trvání simulace“) | Vyberte **končit:** a zadejte `5` minut.

   Jakmile budete hotovi, vyberte **Start simulace**. Simulace spustí celkem 5 minut. Vygeneruje data ze simulovaných zařízení 1 000 každých 10 sekund. 

   [![Instalace simulace zařízení](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Při spuštění simulace, Všimněte si, že **celkový počet zpráv** a **zpráv za sekundu** pole aktualizovat, přibližně každých 10 sekund. Simulace končí po přibližně 5 minut a vrátíte se do **nastavení**.

   [![Simulace zařízení se systémem](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Ověření telemetrických dat

V této závěrečné sekci ověřte, že telemetrická data byla vygenerována a uložená v prostředí Time Series Insights. K ověření dat použijete průzkumníka TSI, který slouží k dotazování a analyzování telemetrických dat.

1. Vraťte se do skupiny prostředků prostředí Time Series Insights **přehled** stránky. Vyberte prostředí Time Series Insights.

   [![Skupina prostředků prostředí času Series Insights a prostředí](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na prostředí Time Series Insights **přehled** stránky, vyberte **URL Průzkumníka Time Series Insights** otevřete Průzkumníka služby Time Series Insights.

   [![Průzkumník Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. V Průzkumníku Time Series Insights načte a ověří pomocí účtu Azure portal. Po počáteční zobrazení zobrazí se v oblasti grafu, že do prostředí Time Series Insights Section.Selectable s Simulovaná telemetrická data. Pokud chcete filtrovat užší časový rozsah, vyberte v rozevíracím seznamu v levém horním rohu. Zadejte časový rozsah, který je dostatečně velký, aby span trvání simulaci zařízení. Vyberte ikonu lupy vyhledávání.

   [![Filtr času rozsah času Series Insights Průzkumníka](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zúžení časový rozsah umožňuje grafu přiblížit odlišné nárůstům přenos dat do služby IoT hub a prostředí Time Series Insights. Všimněte si také **streamování dokončeno** textu v pravém horním rohu, která zobrazuje celkový počet nalezených událostí. Můžete také přetáhnout **velikost intervalu** posuvníku pod kontrolou granularitu vykreslení v grafu.

   [![Čas Series Insights explorer časový rozsah filtrované zobrazení](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. A konečně můžete taky levým tlačítkem myši oblast, kterou chcete filtrovat řady. Klikněte pravým tlačítkem a použít **zkoumat události** zobrazíte podrobnosti o události v tabulkovém **události** zobrazení.

   [![Čas Series Insights explorer časový rozsah filtrovat události a zobrazení](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz vytvoří několik spuštění Azure services pro podporu řešení simulace zařízení a prostředí Time Series Insights. Pokud chcete opustit nebo odložit pracovat v této sérii kurzů, odstraňte všechny prostředky, aby vám neúčtovaly zbytečné poplatky.

Z nabídky na levé straně webu Azure Portal:

1. Vyberte **skupiny prostředků** ikonu. Vyberte skupinu prostředků, kterou jste vytvořili pro prostředí Time Series Insights. V horní části stránky vyberte **odstranit skupinu prostředků**, zadejte název skupiny prostředků a vyberte **odstranit**.

1. Vyberte **skupiny prostředků** ikonu. Vyberte skupinu prostředků, který byl vytvořen akcelerátor řešení simulaci zařízení. V horní části stránky vyberte **odstranit skupinu prostředků**, zadejte název skupiny prostředků a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření prostředí Time Series Insights.
> * Vytvoření řešení pro simulaci zařízení, která obsahuje službu IoT hub.
> * Prostředí Time Series Insights připojte ke službě IoT hub.
> * Spuštění simulace zařízení pro streamování dat do prostředí Time Series Insights.
> * Ověřte Simulovaná telemetrická data.

Teď už víte, jak vytvořit vlastní prostředí Time Series Insights, přečtěte si, jak vytvořit webovou aplikaci, která využívá data z prostředí Time Series Insights:

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)
