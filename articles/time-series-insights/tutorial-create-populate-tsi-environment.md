---
title: 'Kurz: Vytvoření prostředí Azure Time Series Insights | Dokumentace Microsoftu'
description: Zjistěte, jak vytvořit prostředí Time Series Insights, který je naplněn daty ze simulovaných zařízení.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 06a450c47c7264bdecb663c9f71e3a9753df5e1e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273550"
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

Prostředí Time Series Insights je zařízení se data shromažďují a ukládají. Po uložení [Průzkumníka služby Azure Time Series Insights](time-series-quickstart.md) a [rozhraní API pro čas Series Insights dotazy](/rest/api/time-series-insights/ga-query-api) slouží k dotazování a analýze dat.

Azure IoT Hub je zdroj událostí, který se používá ve všech zařízeních (simulovaná nebo fyzická) v kurzu k zabezpečenému připojení a přenášet data do cloudu Azure.

V tomto kurzu se používá také [akcelerátorů řešení IoT](https://www.azureiotsolutions.com) ke generování a streamování ukázková telemetrická data do služby IoT Hub.

>[!TIP]
> [Akcelerátory řešení IoT](https://www.azureiotsolutions.com) poskytují podnikové předkonfigurované řešení, která vám pomůže zrychlit vývoj vlastních řešení IoT.

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

Nejprve vytvořte řešení pro simulaci zařízení, která generuje testovací data pro naplnění prostředí Time Series Insights.

1. V samostatném okně nebo záložce, přejděte na [azureiotsolutions.com](https://www.azureiotsolutions.com). Přihlaste se pomocí stejného účtu předplatného Azure a vyberte **simulace zařízení** akcelerátoru.

   [![Spustit simulaci zařízení akcelerátoru](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Zadejte požadované parametry na **simulace zařízení vytvořit řešení** stránky.

   Parametr|Popis
   ---|---
   **Název nasazení** | Tato jedinečná hodnota se používá k vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků.
   **Předplatné Azure** | Zadejte předplatným, která byla použita k vytvoření prostředí Time Series Insights v předchozí části.
   **Možnosti nasazení** | Vyberte **zřídit novou službu IoT Hub** vytvořit novou službu IoT hub specifické pro tento kurz.
   **Umístění Azure** | Zadejte stejné oblasti, která byla použita k vytvoření prostředí Time Series Insights v předchozí části.

   Jakmile budete hotovi, vyberte **vytvořit řešení** ke zřízení prostředků Azure řešení. Může trvat až 20 minut, než k dokončení tohoto procesu.

   [![Zřízení řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Po dokončení zřizování text nad nové řešení se změní z **zřizování** k **připravené**.

   >[!IMPORTANT]
   > Nevybírejte **spuštění** ještě! Tato webová stránka nechte otevřené, protože k němu budete později vrátit.

   [![Řešení pro simulaci zařízení zřizování dokončeno](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Nyní vyhledejte nově vytvořené prostředky na webu Azure Portal. Na **skupiny prostředků** stránky si všimněte, že nová skupina prostředků se vytvořila pomocí **název řešení** uvedené v předchozím kroku. Poznamenejte si prostředky, které byly vytvořeny pro simulaci zařízení.

   [![Prostředky simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Vytvoření prostředí

Za druhé vytvořte prostředí Time Series Insights ve vašem předplatném Azure.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí svého účtu předplatného Azure. 
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**. 
1. Vyberte **Internet of Things** kategorie a pak vyberte **Time Series Insights**. 

   [![Výběr prostředku prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na **prostředí Time Series Insights** stránce, vyplňte požadované parametry.

   Parametr|Popis
   ---|---
   **Název prostředí** | Vyberte jedinečný název pro prostředí Time Series Insights. Názvy jsou použity pomocí Průzkumníka služby Time Series Insights a [API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Předplatné** | Předplatná představují kontejnery prostředků Azure. Vyberte předplatné, k vytvoření prostředí Time Series Insights.
   **Skupina prostředků** | Skupina prostředků představuje kontejner prostředků Azure. Zvolte existující skupinu prostředků nebo vytvořte novou pro prostředek prostředí Time Series Insights.
   **Location** | Zvolte oblast System center data pro vaše prostředí Time Series Insights. Aby se zabránilo další latence, vytvořte prostředí Time Series Insights ve stejné oblasti jako ostatní prostředky IoT.
   **Tier** | Vyberte potřebnou propustnost. Vyberte **S1**.
   **Kapacita** | Kapacita je multiplikátor použitý pro rychlost příchozího přenosu dat a kapacity úložiště spojené s vybraná skladová položka. Kapacitu můžete změnit po vytvoření. Vyberte kapacitu **1**.

   Až budete hotovi, vyberte **revize + vytvořit** pokračujte k dalšímu kroku.

   [![Vytvořit prostředek prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Prostředí Time Series Insights teď připojte k vytvořené akcelerátor řešení služby IoT hub. Nastavte **vyberte centrum** k `Select existing`. Poté vyberte položku vytvořil Solution Accelerator při nastavování služby IoT hub **název centra IoT**.

   [![Připojení prostředí Time Series Insights do vytvořeného služby IoT hub](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

1. Zkontrolujte **oznámení** panelu ke sledování dokončení nasazení. 

   [![Čas Series Insights prostředí nasazení bylo úspěšné](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Spustit simulaci zařízení pro streamování dat

Teď, když je kompletní nasazení a počáteční konfiguraci, naplnění prostředí Time Series Insights s ukázkovými daty z [Simulovaná zařízení, vytvořené akcelerátoru](#create-a-device-simulation).

Spolu s IoT hub se vygeneroval webovou aplikaci Azure App Service k vytvoření a odesílání telemetrie simulované zařízení.

1. Vraťte se zpět na [Řídicí panel akcelerátorů řešení](https://www.azureiotsolutions.com/Accelerators#dashboard). Přihlaste se znovu, v případě potřeby pomocí stejného účtu Azure, které jste používali v tomto kurzu. Teď můžete vybrat **spuštění** v rámci řešení "Simulace zařízení".

     [![Řídicí panel akcelerátory řešení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Webové aplikace pro simulaci zařízení začíná s výzvou, abyste udělili webové aplikace "přihlášení a čtení vašeho profilu" oprávnění. Toto oprávnění uděluje aplikací, aby získal informace profilu uživatele, která je nezbytná pro podporu fungování aplikace.

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

Tento kurz vytvoří několik spuštění Azure services pro podporu řešení simulace zařízení a prostředí Time Series Insights. Chcete-li je odebrat, přejděte zpět na webu Azure portal.

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
