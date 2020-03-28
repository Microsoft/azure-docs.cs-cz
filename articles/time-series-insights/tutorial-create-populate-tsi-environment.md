---
title: 'Kurz: Vytvoření prostředí – Přehledy azure time series | Dokumenty společnosti Microsoft'
description: Zjistěte, jak vytvořit prostředí Time Series Insights, které je naplněno daty ze simulovaných zařízení.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 7bebc9e682f5156fa235b77ff020e502695a28be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76981187"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Kurz: Vytvoření prostředí Azure Time Series Insights

Tento kurz vás provede procesem vytváření prostředí Azure Time Series Insights, které je naplněno daty ze simulovaných zařízení. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte prostředí Time Series Insights.
> * Vytvořte simulační řešení zařízení, které obsahuje centrum IoT.
> * Připojte prostředí Time Series Insights k centru IoT.
> * Spusťte simulaci zařízení pro streamování dat do prostředí Time Series Insights.
> * Ověřte simulovaná telemetrická data.

> [!IMPORTANT]
> Pokud ještě nemáte předplatné Azure, zaregistrujte si předplatné [Azure.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Požadavky

* Váš účet pro přihlášení do Azure musí být také členem role **vlastníka** předplatného. Další informace [načtete spravovat přístup pomocí řízení přístupu na základě rolí a portálu Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Projděte si video

### <a name="learn-how-to-use-an-azure-iot-solution-accelerator-to-generate-data-and-get-started-with-time-series-insights-br"></a>Zjistěte, jak pomocí akcelerátoru řešení Azure IoT generovat data a začít s Time Series Insights. </br>

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Přehled

Prostředí Time Series Insights je místo, kde se shromažďují a ukládají data zařízení. Po uložení lze [průzkumník Azure Time Series Insights](time-series-quickstart.md) a časové rozhraní API pro dotazy [přehledy přehledů time series](/rest/api/time-series-insights/ga-query-api) insights použít k dotazování a analýze dat.

Azure IoT Hub je zdroj událostí, který používají všechna zařízení (simulovaná nebo fyzická) v kurzu k bezpečnému připojení a přenosu dat do cloudu Azure.

Tento kurz také používá [akcelerátor řešení IoT](https://www.azureiotsolutions.com) ke generování a streamování ukázkových telemetrických dat do služby IoT Hub.

>[!TIP]
> [Akcelerátory řešení IoT](https://www.azureiotsolutions.com) poskytují předkonfigurovaná řešení na podnikové úrovni, která můžete použít k urychlení vývoje vlastních řešení IoT.

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

Nejprve vytvořte simulační řešení zařízení, které generuje testovací data pro naplnění prostředí Time Series Insights.

1. V samostatném okně nebo na kartě přejděte na [azureiotsolutions.com](https://www.azureiotsolutions.com). Přihlaste se pomocí stejného účtu předplatného Azure a vyberte akcelerátor **simulace zařízení.**

   [![Spuštění akcelerátoru Simulace zařízení](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1.  Vyberte **Možnost Vyzkoušet .** Potom zadejte požadované parametry na stránce **Řešení Vytvořit simulační zařízení.**

   Parametr|Popis
   ---|---
   **Název nasazení** | Tato jedinečná hodnota se používá k vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků.
   **Předplatné Azure** | Zadejte stejné předplatné, které bylo použito k vytvoření prostředí Time Series Insights v předchozí části.
   **Možnosti nasazení** | Vyberte **Zřídit nové Centrum IoT Hub** a vytvořte nové centrum IoT specifické pro tento kurz.
   **Umístění Azure** | Zadejte stejnou oblast, která byla použita k vytvoření prostředí Time Series Insights v předchozí části.

   Až budete hotovi, vyberte **Vytvořit,** chcete-li zřídit prostředky Azure řešení. Dokončení tohoto procesu může trvat až 20 minut.

   [![Zřízení řešení simulace zařízení](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Po dokončení zřizování se zobrazí dvě aktualizace s upozorněním, že stav nasazení se přesunul z **zřizování** na **Připraveno**. 

   >[!IMPORTANT]
   > Ještě nevstupujte do akcelerátoru řešení! Ponechte tuto webovou stránku otevřenou, protože se k ní později vrátíte.

   [![Zřizování řešení simulace zařízení dokončeno](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Teď zkontrolujte nově vytvořené prostředky na webu Azure Portal. Na stránce **Skupiny prostředků** si všimněte, že nová skupina prostředků byla vytvořena pomocí **názvu řešení** uvedeného v posledním kroku. Poznamenejte si prostředky, které byly vytvořeny pro simulaci zařízení.

   [![Prostředky pro simulaci zařízení](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Vytvoření prostředí

Za druhé, vytvořte prostředí Time Series Insights ve vašem předplatném Azure.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu předplatného Azure. 
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**. 
1. Vyberte kategorii **Internet věcí** a pak vyberte **Přehledy časových řad**. 

   [![Vyberte zdroj prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Na stránce **prostředí Time Series Insights** vyplňte požadované parametry.

   Parametr|Popis
   ---|---
   **Název prostředí** | Zvolte jedinečný název prostředí Time Series Insights. Názvy jsou používány průzkumníkem Time Series Insights a [rozhraníMI API dotazu](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).
   **Předplatné** | Předplatná představují kontejnery prostředků Azure. Zvolte předplatné a vytvořte prostředí Time Series Insights.
   **Skupina prostředků** | Skupina prostředků představuje kontejner prostředků Azure. Zvolte existující skupinu prostředků nebo vytvořte novou pro prostředek prostředí Time Series Insights.
   **Umístění** | Vyberte oblast datového centra pro prostředí Time Series Insights. Chcete-li se vyhnout další latenci, vytvořte prostředí Time Series Insights ve stejné oblasti jako ostatní prostředky IoT.
   **Úroveň** | Vyberte potřebnou propustnost. Vyberte **Možnost S1**.
   **Kapacita** | Kapacita je násobitel použitý pro rychlost příchozího přenosu dat a kapacitu úložiště přidruženou k vybrané skladové jednotce. Kapacitu můžete po vytvoření změnit. Vyberte kapacitu **1**.

   Po dokončení vyberte **Další: Zdroj události,** abyste přešeli k dalšímu kroku.

   [![Vytvoření prostředku prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Teď připojte prostředí Time Series Insights k centru IoT vytvořenému akcelerátorem řešení. Nastavit Možnost Vybrat `Select existing` **rozbočovač** na . Potom zvolte centrum IoT vytvořené akcelerátorem řešení při nastavování **názvu centra IoT Hub**.

   [![Připojení prostředí Time Series Insights k vytvořenému centru IoT hub](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Nakonec vyberte **Zkontrolovat + vytvořit**.

1. Zkontrolujte panel **Oznámení** a sledujte dokončení nasazení. 

   [![Nasazení prostředí Time Series Insights bylo úspěšné.](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Spustit simulaci zařízení

Nyní, když je nasazení a počáteční konfigurace dokončena, naplňte prostředí Time Series Insights ukázkovými daty ze [simulovaných zařízení vytvořených akcelerátorem](#create-a-device-simulation).

Spolu s centrem IoT byla vygenerována webová aplikace Azure App Service, která vytvářela a přenášela simulovanou telemetrii zařízení.

1. Vraťte se zpět na [Řídicí panel akcelerátorů řešení](https://www.azureiotsolutions.com/Accelerators#dashboard). V případě potřeby se znovu přihlaste pomocí stejného účtu Azure, který jste používali v tomto kurzu. Vyberte "Řešení zařízení" a pak **přejděte na akcelerátor řešení** a spusťte nasazené řešení.

   [![Řídicí panel akcelerátorů řešení](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Webová aplikace pro simulaci zařízení začíná výzvou k udělení webové aplikace **Přihlásit se k vám a přečíst si** oprávnění k profilu. Toto oprávnění umožňuje aplikaci načíst informace o profilu uživatele nezbytné pro podporu fungování aplikace.

   [![Souhlas webové aplikaci pro simulaci zařízení](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Vyberte **+ Nová simulace**. Po načtení stránky **Nastavení simulace** zadejte požadované parametry.

   Parametr|Popis
   ---|---
   **Cílová služba IoT Hub** | Vyberte **Použít předem zřízené centrum IoT Hub**.
   **Model zařízení** | Vyberte **chladicí jednotka**.
   **Počet zařízení**  | Zadejte do `10` části **Částka**.
   **Frekvence telemetrie** | Zadejte `10` sekundy.
   **Doba trvání simulace** | Vyberte **Zakončit:** a zadejte `5` minuty.

   Po dokončení vyberte **Spustit simulaci**. Simulace probíhá celkem 5 minut. Generuje data z 1000 simulovaných zařízení každých 10 sekund. 

   [![Nastavení simulace zařízení](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Při spuštění simulace si všimněte, že se aktualizuje **pole Celkem zpráv** a **zpráv za sekundu,** přibližně každých 10 sekund. Simulace končí přibližně po 5 minutách a vrátí vás do **nastavení simulace**.

   [![Spuštěná simulace zařízení](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Ověření telemetrických dat

V této poslední části ověříte, že telemetrická data byla generována a uložena v prostředí Time Series Insights. K ověření dat použijete průzkumníka TSI, který slouží k dotazování a analyzování telemetrických dat.

1. Vraťte se na stránku **Přehled** skupiny prostředků prostředí Time Series Insights. Vyberte prostředí Time Series Insights.

   [![Skupina a prostředí prostředí Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Na stránce **Přehled** prostředí Time Series Insights vyberte **adresu URL průzkumníka Time Series Insights** a otevřete průzkumníka Time Series Insights.

   [![Průzkumník Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Průzkumník Time Series Insights se načítá a ověřuje pomocí účtu portálu Azure. Zpočátku se zobrazí oblast grafu, se kterou bylo naplněné prostředí Time Series Insights spolu s simulovanými telemetrickými daty. Chcete-li filtrovat užší časový rozsah, vyberte rozevírací seznam v levém horním rohu. Zadejte dostatečně velký časový rozsah, který pokrčil dobu trvání simulace zařízení. Pak vyberte vyhledávací lupu.

   [![Filtr časového rozsahu průzkumníka Přehledy časových řad](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zúžení časového rozsahu umožňuje grafu přiblížit na zřetelné shluky přenosu dat do centra IoT hub a prostředí Time Series Insights. Všimněte si **také úplného** textu streamování v pravém horním rohu, který zobrazuje celkový počet nalezených událostí. Můžete také přetáhnout jezdec **Velikost intervalu** a ovládat rozlišovací schopnost vykreslování v grafu.

   [![Zobrazení filtrované časové rozsahprůzkumníku Přehledy časových řad Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Nakonec můžete také klepnutím levým tlačítkem myši na oblast filtrovat oblast. Potom klikněte pravým tlačítkem myši a použijte **prozkoumat události** k zobrazení podrobností o události v zobrazení tabulkových **událostí.**

   [![Filtrované zobrazení a události časového rozsahu průzkumníka Time Series Insights](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz vytvoří několik spuštěných služeb Azure pro podporu prostředí Time Series Insights a řešení simulace zařízení. Pokud je chcete odebrat, přejděte zpět na portál Azure.

Z nabídky vlevo na webu Azure Portal:

1. Vyberte ikonu **Skupiny prostředků.** Pak vyberte skupinu prostředků, kterou jste vytvořili pro prostředí Time Series Insights. V horní části stránky vyberte **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a vyberte **Odstranit**.

1. Vyberte ikonu **Skupiny prostředků.** Pak vyberte skupinu prostředků, která byla vytvořena akcelerátorem řešení simulace zařízení. V horní části stránky vyberte **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořte prostředí Time Series Insights.
> * Vytvořte simulační řešení zařízení, které obsahuje centrum IoT.
> * Připojte prostředí Time Series Insights k centru IoT.
> * Spusťte simulaci zařízení pro streamování dat do prostředí Time Series Insights.
> * Ověřte simulovaná telemetrická data.

Teď, když víte, jak vytvořit vlastní prostředí Time Series Insights, zjistěte, jak vytvořit webovou aplikaci, která spotřebovává data z prostředí Time Series Insights:

> [!div class="nextstepaction"]
> [Čtení vzorků vizualizace sady Hosted Client SDK](https://tsiclientsample.azurewebsites.net/)
