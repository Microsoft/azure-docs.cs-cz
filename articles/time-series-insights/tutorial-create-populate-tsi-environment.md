---
title: 'Kurz: vytvoření prostředí – Azure Time Series Insights | Microsoft Docs'
description: Naučte se vytvořit prostředí Azure Time Series Insights, které je naplněné daty z simulovaných zařízení.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 982ca04237d16979d65260a040f5aed783822ac7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023220"
---
# <a name="tutorial-create-an-azure-time-series-insights-gen1-environment"></a>Kurz: vytvoření prostředí Azure Time Series Insights Gen1

> [!CAUTION]
> Toto je Gen1 článek.

Tento kurz vás provede procesem vytvoření Azure Time Series Insights prostředí, které je naplněné daty z simulovaných zařízení. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte Azure Time Series Insights prostředí.
> * Vytvořte řešení pro simulaci zařízení, které obsahuje centrum IoT.
> * Připojte prostředí Azure Time Series Insights ke službě IoT Hub.
> * Spusťte simulaci zařízení pro streamování dat do prostředí Azure Time Series Insights.
> * Ověřte Simulovaná data telemetrie.

> [!IMPORTANT]
> Zaregistrujte si [bezplatné předplatné Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte.

## <a name="prerequisites"></a>Požadavky

* Účet pro přihlášení do Azure musí být také členem role **vlastníka** předplatného. Další informace najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Zkontrolovat video

Naučte se, jak pomocí akcelerátoru řešení Azure IoT vygenerovat data a začít s Azure Time Series Insights.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Přehled

Prostředí Azure Time Series Insights je místo, kde se shromažďují a ukládají data zařízení. Po uložení je možné použít k dotazování a analýze dat [rozhraní API](/rest/api/time-series-insights/gen1-query-api) pro [Azure Time Series Insights](time-series-quickstart.md) a dotazování Azure Time Series Insights.

Azure IoT Hub je zdroj událostí, který se používá pro všechna zařízení (simulované nebo fyzické) v tomto kurzu k bezpečnému připojení a přenosu dat do cloudu Azure.

V tomto kurzu se používá také [akcelerátor řešení IoT](https://www.azureiotsolutions.com) k vygenerování a streamování ukázkových dat telemetrie pro IoT Hub.

>[!TIP]
> [Akcelerátory řešení IoT](https://www.azureiotsolutions.com) poskytují předkonfigurovaná řešení na podnikové úrovni, která můžete použít k urychlení vývoje vlastních řešení IoT.

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

Nejprve vytvořte řešení pro simulaci zařízení, které generuje testovací data pro naplnění Azure Time Series Insightsho prostředí.

1. V samostatném okně nebo na kartě přejdete na [azureiotsolutions.com](https://www.azureiotsolutions.com). Přihlaste se pomocí stejného účtu předplatného Azure a vyberte akcelerátor **simulace zařízení** .

   [![Spuštění akcelerátoru Simulace zařízení](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Vyberte **zkusit nyní**. Pak na stránce **řešení pro vytvoření simulace zařízení** zadejte požadované parametry.

   Parametr|Popis
   ---|---
   **Název nasazení** | Tato jedinečná hodnota se používá k vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků.
   **Předplatné Azure** | Zadejte stejné předplatné, které bylo použito k vytvoření prostředí Azure Time Series Insights v předchozí části.
   **Možnosti nasazení** | Pokud chcete vytvořit nový IoT Hub specifický pro tento kurz, vyberte **zřídit nový IoT Hub** .
   **Umístění Azure** | Zadejte stejnou oblast, která byla použita k vytvoření prostředí Azure Time Series Insights v předchozí části.

   Až budete hotovi, vyberte **vytvořit** a zřiďte prostředky Azure v řešení. Dokončení tohoto procesu může trvat až 20 minut.

   [![Zřízení řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Po dokončení zřizování se zobrazí dvě aktualizace oznamující, že stav nasazení se přesunul ze **zřizování** na **připraveno**.

   >[!IMPORTANT]
   > Ještě nezadávejte akcelerátor řešení. Tuto webovou stránku nechte otevřenou, protože se k ní vrátíte později.

   [![Zřizování řešení simulace zařízení dokončeno](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Nyní zkontrolujte nově vytvořené prostředky v Azure Portal. Na stránce **skupiny prostředků** si všimněte, že se vytvořila nová skupina prostředků pomocí **názvu řešení** , který jste zadali v posledním kroku. Poznamenejte si prostředky, které byly vytvořeny pro simulaci zařízení.

   [![Prostředky simulace zařízení](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Vytvoření prostředí

Za druhé vytvořte ve svém předplatném Azure Azure Time Series Insights prostředí.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu předplatného Azure.
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**.
1. Vyberte kategorii **Internet věcí** a pak vyberte **Time Series Insights**.

   [![Vyberte prostředek prostředí Azure Time Series Insights.](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Na stránce **Time Series Insights prostředí** vyplňte požadované parametry.

   Parametr|Popis
   ---|---
   **Název prostředí** | Vyberte jedinečný název prostředí Azure Time Series Insights. Názvy používá Průzkumník Azure Time Series Insights a [rozhraní API pro dotazy](/rest/api/time-series-insights/gen1-query).
   **Předplatné** | Předplatná představují kontejnery prostředků Azure. Vyberte předplatné, ve kterém chcete vytvořit prostředí Azure Time Series Insights.
   **Skupina prostředků** | Skupina prostředků představuje kontejner prostředků Azure. Vyberte existující skupinu prostředků nebo vytvořte novou pro prostředek prostředí Azure Time Series Insights.
   **Umístění** | Vyberte oblast datového centra pro prostředí Azure Time Series Insights. Aby nedošlo k další latenci, vytvořte Azure Time Series Insights prostředí ve stejné oblasti jako jiné prostředky IoT.
   **Úroveň** | Vyberte potřebnou propustnost. Vyberte **S1**.
   **Kapacita** | Kapacita je násobitel, který se používá pro míru příchozího přenosu dat a kapacitu úložiště přidruženou k vybrané SKU. Kapacitu můžete po vytvoření změnit. Vyberte kapacitu **1**.

   Po dokončení vyberte **Další: zdroj události** , abyste mohli pokračovat k dalšímu kroku.

   [![Vytvoření prostředku prostředí Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Nyní připojte prostředí Azure Time Series Insights ke službě IoT Hub vytvořené pomocí akcelerátoru řešení. Nastavte **Vyberte rozbočovač** na `Select existing` . Pak při nastavení **IoT Hubho názvu** vyberte Centrum IoT vytvořené pomocí akcelerátoru řešení.

   [![Připojení prostředí Azure Time Series Insights k vytvořenému centru IoT Hub](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Nakonec vyberte **zkontrolovat + vytvořit**.

1. Projděte si panel **oznámení** a sledujte dokončení nasazení.

   [![Nasazení prostředí Azure Time Series Insights bylo úspěšné.](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Spustit simulaci zařízení

Po dokončení nasazení a počáteční konfigurace naplňte Azure Time Series Insights prostředí pomocí ukázkových dat z [simulovaných zařízení vytvořených akcelerátorem](#create-a-device-simulation).

Společně se službou IoT Hub se vygenerovala Azure App Service webová aplikace pro vytvoření a přenos telemetrie simulovaného zařízení.

1. Vraťte se zpět na [Řídicí panel akcelerátorů řešení](https://www.azureiotsolutions.com/Accelerators#dashboard). V případě potřeby se znovu přihlaste pomocí stejného účtu Azure, který jste používali v tomto kurzu. Vyberte své řešení zařízení a potom v **rámci akcelerátoru řešení** spusťte nasazené řešení.

   [![Řídicí panel akcelerátorů řešení](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Webová aplikace simulace zařízení začíná výzvou k udělení webové aplikace tak, aby **vás přihlásila a četla oprávnění k vašemu profilu** . Toto oprávnění umožňuje aplikaci načíst informace o profilu uživatele, které jsou nezbytné k podpoře fungování aplikace.

   [![Souhlas webové aplikaci pro simulaci zařízení](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Vyberte **+ Nová simulace**. Po načtení stránky **Nastavení simulace** zadejte požadované parametry.

   Parametr|Popis
   ---|---
   **Cílová služba IoT Hub** | Vyberte možnost **použít předem zřízené IoT Hub**.
   **Model zařízení** | Vyberte **chladicíer**.
   **Počet zařízení**  | Zadejte `10` **hodnotu** v poli množství.
   **Frekvence telemetrie** | Zadejte `10` sekundy.
   **Doba trvání simulace** | Vyberte **Konec v:** a zadejte `5` minuty.

   Až budete hotovi, vyberte **spustit simulaci**. Simulace se spustí celkem po dobu 5 minut. Vygeneruje data z 1 000 simulovaných zařízení každých 10 sekund.

   [![Nastavení simulace zařízení](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Zatímco se simulace spouští, Všimněte si, že **Celkový počet zpráv** a **zpráv za sekundu** se aktualizuje přibližně každých 10 sekund. Simulace skončí po přibližně 5 minutách a vrátí vás k **Nastavení simulace**.

   [![Spuštěná simulace zařízení](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Ověření telemetrických dat

V této poslední části ověříte, že se data telemetrie vygenerovala a uložila v prostředí Azure Time Series Insights. Chcete-li ověřit data, použijte Průzkumníka Azure Time Series Insights, který se používá k dotazování a analýze dat telemetrie.

1. Vraťte se na stránku **Přehled** skupiny prostředků Azure Time Series Insightsho prostředí. Vyberte prostředí Azure Time Series Insights.

   [![Skupina prostředků prostředí Azure Time Series Insights a prostředí](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na stránce **Přehled** prostředí Azure Time Series Insights vyberte **adresu URL Time Series Insights Explorer** a otevřete tak Průzkumník Azure Time Series Insights.

   [![Průzkumník Azure Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Průzkumník Azure Time Series Insights načítá a ověřuje pomocí účtu Azure Portal. Zpočátku se zobrazí oblast grafu, ve které Azure Time Series Insights prostředí naplnilo společně s jejich simulovanými daty telemetrie. Pokud chcete filtrovat užší časový rozsah, vyberte rozevírací nabídku v levém horním rohu. Zadejte časový rozsah, který je dostatečně velký pro rozsah trvání simulace zařízení. Pak vyberte lupu hledání.

   [![Filtr časového rozsahu Azure Time Series Insights Exploreru](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zúžení časového rozsahu umožňuje grafu přiblížit se k různým nárůstům přenosu dat do centra IoT a Azure Time Series Insightsho prostředí. Všimněte si také **kompletního** textu v pravém horním rohu, které zobrazuje celkový počet nalezených událostí. Můžete také přetáhnout posuvník **velikost intervalu** pro řízení členitosti grafu v grafu.

   [![Filtrované zobrazení časového rozsahu Azure Time Series Insights Exploreru](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Nakonec můžete také kliknutím levým na oblast filtrovat rozsah. Potom klikněte pravým tlačítkem a pomocí možnosti **prozkoumat události** zobrazte podrobnosti události v zobrazení tabulkových **událostí** .

   [![Zobrazení a události filtrovaného časového rozsahu aplikace Azure Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto kurzu se vytvoří několik spuštěných služeb Azure, které podporují řešení pro simulaci Azure Time Series Insights prostředí a zařízení. Pokud je chcete odebrat, přejděte zpět na Azure Portal.

Z nabídky na levé straně Azure Portal:

1. Vyberte ikonu **skupiny prostředků** . Pak vyberte skupinu prostředků, kterou jste vytvořili pro prostředí Azure Time Series Insights. V horní části stránky vyberte **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a vyberte **Odstranit**.

1. Vyberte ikonu **skupiny prostředků** . Pak vyberte skupinu prostředků, kterou vytvořil akcelerátor řešení pro simulaci zařízení. V horní části stránky vyberte **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Vytvořte Azure Time Series Insights prostředí.
> * Vytvořte řešení pro simulaci zařízení, které obsahuje centrum IoT.
> * Připojte prostředí Azure Time Series Insights ke službě IoT Hub.
> * Spusťte simulaci zařízení pro streamování dat do prostředí Azure Time Series Insights.
> * Ověřte Simulovaná data telemetrie.

Teď, když víte, jak vytvořit vlastní prostředí Azure Time Series Insights, se naučíte, jak vytvořit webovou aplikaci, která využívá data z prostředí Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Čtení ukázek pro vizualizaci hostovaného klienta sady SDK](https://tsiclientsample.azurewebsites.net/)