---
title: 'Kurz: Vytvoření prostředí Azure Time Series Insights | Dokumentace Microsoftu'
description: Naučte se vytvořit prostředí Time Series Insights, které je naplněné daty z simulovaných zařízení.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 39226498316de9359394ca7953ab34012b595116
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638898"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Kurz: Vytvoření prostředí Azure Time Series Insights

Tento kurz vás provede procesem vytvoření Azure Time Series Insights prostředí, které je naplněné daty z simulovaných zařízení. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření prostředí Time Series Insights.
> * Vytvořte řešení pro simulaci zařízení, které obsahuje centrum IoT.
> * Připojte prostředí Time Series Insights ke službě IoT Hub.
> * Spusťte simulaci zařízení pro streamování dat do prostředí Time Series Insights.
> * Ověřte Simulovaná data telemetrie.

## <a name="video"></a>Video

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Naučte se, jak pomocí akcelerátoru řešení Azure IoT vygenerovat data a začít s Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Účet pro přihlášení do Azure musí být také členem role **vlastníka** předplatného. Další informace najdete v tématu [Správa přístupu pomocí řízení přístupu na základě role a Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="overview"></a>Přehled

Prostředí Time Series Insights je místo, kde se shromažďují a ukládají data zařízení. Po uložení je možné použít k dotazování a analýze dat [rozhraní API](/rest/api/time-series-insights/ga-query-api) pro [Azure Time Series Insights](time-series-quickstart.md) a dotazování Time Series Insights.

Azure IoT Hub je zdroj událostí, který se používá pro všechna zařízení (simulované nebo fyzické) v tomto kurzu k bezpečnému připojení a přenosu dat do cloudu Azure.

V tomto kurzu se používá také [akcelerátor řešení IoT](https://www.azureiotsolutions.com) k vygenerování a streamování ukázkových dat telemetrie pro IoT Hub.

>[!TIP]
> [Akcelerátory řešení IoT](https://www.azureiotsolutions.com) poskytují předkonfigurovaná řešení na podnikové úrovni, která můžete použít k urychlení vývoje vlastních řešení IoT.

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

Nejprve vytvořte řešení pro simulaci zařízení, které generuje testovací data pro naplnění Time Series Insightsho prostředí.

1. V samostatném okně nebo na kartě přejdete na [azureiotsolutions.com](https://www.azureiotsolutions.com). Přihlaste se pomocí stejného účtu předplatného Azure a vyberte akcelerátor **simulace zařízení** .

   [![Spustit akcelerátor simulace zařízení](media/tutorial-create-populate-tsi-environment/sa-main.png)](media/tutorial-create-populate-tsi-environment/sa-main.png#lightbox)

1. Na stránce **vytvoření řešení simulace zařízení** zadejte požadované parametry.

   Parametr|Popis
   ---|---
   **Název nasazení** | Tato jedinečná hodnota se používá k vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků.
   **Předplatné Azure** | Zadejte stejné předplatné, které bylo použito k vytvoření prostředí Time Series Insights v předchozí části.
   **Možnosti nasazení** | Pokud chcete vytvořit nový IoT Hub specifický pro tento kurz, vyberte **zřídit nový IoT Hub** .
   **Umístění Azure** | Zadejte stejnou oblast, která byla použita k vytvoření prostředí Time Series Insights v předchozí části.

   Až budete hotovi, vyberte **vytvořit řešení** a zřiďte prostředky Azure řešení. Dokončení tohoto procesu může trvat až 20 minut.

   [![Zřídit řešení pro simulaci zařízení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png#lightbox)

1. Po dokončení zřizování se text nad novým řešením změní ze **zřizování** na **připraveno**.

   >[!IMPORTANT]
   > Ještě nevybírejte možnost **Spustit** . Tuto webovou stránku nechte otevřenou, protože se k ní vrátíte později.

   [![Zřizování řešení pro simulaci zařízení bylo dokončeno.](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png#lightbox)

1. Nyní zkontrolujte nově vytvořené prostředky v Azure Portal. Na stránce **skupiny prostředků** si všimněte, že se vytvořila nová skupina prostředků pomocí **názvu řešení** , který jste zadali v posledním kroku. Poznamenejte si prostředky, které byly vytvořeny pro simulaci zařízení.

   [![Prostředky simulace zařízení](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Vytvoření prostředí

Za druhé vytvořte v předplatném Azure Time Series Insights prostředí.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu předplatného Azure. 
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**. 
1. Vyberte **Internet of Things** kategorie a pak vyberte **Time Series Insights**. 

   [![Výběr prostředku prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

1. Na stránce **Time Series Insights prostředí** vyplňte požadované parametry.

   Parametr|Popis
   ---|---
   **Název prostředí** | Vyberte jedinečný název prostředí Time Series Insights. Názvy používá Průzkumník Time Series Insights a [rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Předplatné** | Předplatná představují kontejnery prostředků Azure. Vyberte předplatné, ve kterém chcete vytvořit prostředí Time Series Insights.
   **Skupina prostředků** | Skupina prostředků představuje kontejner prostředků Azure. Vyberte existující skupinu prostředků nebo vytvořte novou pro prostředek prostředí Time Series Insights.
   **Location** | Vyberte oblast datového centra pro prostředí Time Series Insights. Aby nedošlo k další latenci, vytvořte Time Series Insights prostředí ve stejné oblasti jako jiné prostředky IoT.
   **Vrstva** | Vyberte potřebnou propustnost. Vyberte **S1**.
   **Kapacita** | Kapacita je násobitel, který se používá pro míru příchozího přenosu dat a kapacitu úložiště přidruženou k vybrané SKU. Kapacitu můžete po vytvoření změnit. Vyberte kapacitu **1**.

   Až budete hotovi, vyberte **zkontrolovat + vytvořit** a pokračujte dalším krokem.

   [![Vytvoření prostředku prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png#lightbox)

1. Nyní připojte prostředí Time Series Insights ke službě IoT Hub vytvořené pomocí akcelerátoru řešení. Nastavte **Vyberte rozbočovač** na `Select existing`. Pak při nastavení **IoT Hubho názvu**vyberte Centrum IoT vytvořené pomocí akcelerátoru řešení.

   [![Připojení prostředí Time Series Insights k vytvořenému centru IoT Hub](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-iot-hub.png#lightbox)

1. Projděte si panel **oznámení** a sledujte dokončení nasazení. 

   [![Nasazení prostředí Time Series Insights bylo úspěšné.](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation-to-stream-data"></a>Spustit simulaci zařízení pro streamování dat

Po dokončení nasazení a počáteční konfigurace naplňte Time Series Insights prostředí pomocí ukázkových dat z [simulovaných zařízení vytvořených akcelerátorem](#create-a-device-simulation).

Společně se službou IoT Hub se vygenerovala Azure App Service webová aplikace pro vytvoření a přenos telemetrie simulovaného zařízení.

1. Vraťte se zpět na [Řídicí panel akcelerátorů řešení](https://www.azureiotsolutions.com/Accelerators#dashboard). V případě potřeby se znovu přihlaste pomocí stejného účtu Azure, který jste používali v tomto kurzu. Nyní můžete v řešení simulace zařízení vybrat možnost **Spustit** .

     [![Řídicí panel akcelerátorů řešení](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png#lightbox)

1. Webová aplikace pro simulaci zařízení začíná výzvou k udělení oprávnění "přihlášení a čtení vašeho profilu". Toto oprávnění umožňuje aplikaci načíst informace o profilu uživatele, které jsou nezbytné k podpoře fungování aplikace.

     [![Souhlas webové aplikace pro simulaci zařízení](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Po načtení stránky **Nastavení simulace** zadejte požadované parametry.

   Parametr|Popis
   ---|---
   **Target IoT Hub** („Cílový IoT Hub“) | Vyberte možnost **použít předem zřízené IoT Hub**.
   **Device model** („Model zařízení“) | Vyberte **chladicíer**.
   **Number of devices** („Počet zařízení“)  | Zadejte `1000` hodnotu v poli **množství**.
   **Telemetry frequency** („Frekvence telemetrie“) | Zadejte `10` sekundy.
   **Simulation duration** („Doba trvání simulace“) | Vyberte **Konec v:** a zadejte `5` minuty.

   Až budete hotovi, vyberte **spustit simulaci**. Simulace se spustí celkem po dobu 5 minut. Vygeneruje data z 1 000 simulovaných zařízení každých 10 sekund. 

   [![Nastavení simulace zařízení](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Zatímco se simulace spouští, Všimněte si, že **Celkový počet zpráv** a **zpráv za sekundu** se aktualizuje přibližně každých 10 sekund. Simulace skončí po přibližně 5 minutách a vrátí vás k **Nastavení simulace**.

   [![Simulace zařízení je spuštěná.](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Ověření telemetrických dat

V této poslední části ověříte, že se data telemetrie vygenerovala a uložila v prostředí Time Series Insights. K ověření dat použijete průzkumníka TSI, který slouží k dotazování a analyzování telemetrických dat.

1. Vraťte se na stránku **Přehled** skupiny prostředků Time Series Insightsho prostředí. Vyberte prostředí Time Series Insights.

   [![Skupina prostředků prostředí Time Series Insights a prostředí](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na stránce **Přehled** prostředí Time Series Insights vyberte **adresu URL Time Series Insights Explorer** a otevřete tak Průzkumník Time Series Insights.

   [![Průzkumník Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Průzkumník Time Series Insights načítá a ověřuje pomocí účtu Azure Portal. Při počátečním zobrazení se můžete podívat v oblasti grafu, ve které Time Series Insights prostředí naplněno pomocí simulovaných dat telemetrie. Pokud chcete filtrovat užší časový rozsah, vyberte rozevírací nabídku v levém horním rohu. Zadejte časový rozsah, který je dostatečně velký pro rozsah trvání simulace zařízení. Pak vyberte lupu hledání.

   [![Filtr časového rozsahu Time Series Insights Exploreru](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zúžení časového rozsahu umožňuje grafu přiblížit se k různým nárůstům přenosu dat do centra IoT a Time Series Insightsho prostředí. Všimněte si také **kompletního** textu v pravém horním rohu, které zobrazuje celkový počet nalezených událostí. Můžete také přetáhnout posuvník **velikost intervalu** pro řízení členitosti grafu v grafu.

   [![Filtrované zobrazení časového rozsahu Time Series Insights Exploreru](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Nakonec můžete také kliknutím levým na oblast filtrovat rozsah. Potom klikněte pravým tlačítkem a pomocí možnosti **prozkoumat události** zobrazte podrobnosti události v zobrazení tabulkových **událostí** .

   [![Zobrazení a události filtrovaného časového rozsahu aplikace Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto kurzu se vytvoří několik spuštěných služeb Azure, které podporují řešení pro simulaci Time Series Insights prostředí a zařízení. Pokud je chcete odebrat, přejděte zpět na Azure Portal.

Z nabídky na levé straně Azure Portal:

1. Vyberte ikonu **skupiny prostředků** . Pak vyberte skupinu prostředků, kterou jste vytvořili pro prostředí Time Series Insights. V horní části stránky vyberte **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a vyberte **Odstranit**.

1. Vyberte ikonu **skupiny prostředků** . Pak vyberte skupinu prostředků, kterou vytvořil akcelerátor řešení pro simulaci zařízení. V horní části stránky vyberte **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření prostředí Time Series Insights.
> * Vytvořte řešení pro simulaci zařízení, které obsahuje centrum IoT.
> * Připojte prostředí Time Series Insights ke službě IoT Hub.
> * Spusťte simulaci zařízení pro streamování dat do prostředí Time Series Insights.
> * Ověřte Simulovaná data telemetrie.

Teď, když víte, jak vytvořit vlastní prostředí Time Series Insights, se naučíte, jak vytvořit webovou aplikaci, která využívá data z prostředí Time Series Insights:

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)
