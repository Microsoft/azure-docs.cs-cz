---
title: 'Kurz: Nastavení prostředí Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu'
description: Zjistěte, jak nastavit prostředí v Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: 824d24b97f192583a42192b3bb90eb1818e1aa18
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273059"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Kurz: Nastavení prostředí Azure čas Series Insights ve verzi Preview

Tento kurz vás provede procesem vytvoření prostředí Azure čas Series Insights ve verzi Preview průběžných plateb (PAYG). 

V tomto kurzu se naučíte:

* Vytvoření prostředí Azure čas Series Insights ve verzi Preview.
* Prostředí Azure čas Series Insights ve verzi Preview připojte do centra událostí ve službě Azure Event Hubs.
* Spuštění ukázky akcelerátor řešení pro streamování dat do prostředí Azure čas Series Insights ve verzi Preview.
* Proveďte základní analýzy na data.
* Definujte typ modelu časové řady a hierarchie a přidružte jej k instancím.

>[!TIP]
> [Akcelerátory řešení IoT](https://www.azureiotsolutions.com/Accelerators) poskytují podnikové předkonfigurované řešení, která vám pomůže zrychlit vývoj vlastních řešení IoT.

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

V této části vytvoříte tři Simulovaná zařízení, které posílají data do instance služby Azure IoT Hub.

1. Přejděte [stránka akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stránce se zobrazí předem připravených několik příkladů. Přihlaste se pomocí svého účtu Azure. Vyberte **simulace zařízení**.

   [![Stránka akcelerátory řešení Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Vyberte **vyzkoušet**.

1. Na **simulace zařízení vytvořit řešení** nastavte následující parametry:

    | Parametr | Akce |
    | --- | --- |
    | **Název nasazení** | Zadejte jedinečnou hodnotu pro novou skupinu prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků. |
    | **Předplatné Azure** | Vyberte předplatné, které jste použili k vytvoření prostředí Time Series Insights. |
    | **Umístění Azure** | Vyberte oblast, který jste použili k vytvoření prostředí Time Series Insights. |
    | **Možnosti nasazení** | Vyberte **zřídit novou službu IoT Hub**. |
 
    Vyberte **vytvořit řešení**. Může trvat až 20 minut, než se řešení pro dokončení nasazení.

    [![Vytvoření stránky řešení simulace zařízení](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Vytvoření prostředí s průběžnými PLATBAMI čas Series Insights ve verzi Preview

Tato část popisuje, jak vytvořit prostředí Azure čas Series Insights ve verzi Preview a připojte ho ke službě IoT hub, který je vytvořen s využitím akcelerátorů řešení IoT [webu Azure portal](https://portal.azure.com/).

1. Přihlaste se k webu Azure portal pomocí svého účtu předplatného.

1. Vyberte **vytvořit prostředek** > **Internet of Things** > **Time Series Insights**.

   [![Vyberte Internet of Things a pak vyberte Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. V **prostředí vytvořit Time Series Insights** podokně na **Základy** kartu, nastavit následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název prostředí** | Zadejte jedinečný název pro prostředí Azure čas Series Insights ve verzi Preview. |
    | **Předplatné** | Zadejte předplatné, ve kterém chcete vytvořit prostředí Azure čas Series Insights ve verzi Preview. Osvědčeným postupem je používat stejné předplatné jako v ostatních prostředků IoT, které jsou vytvořené v simulátoru zařízení. |
    | **Skupina prostředků** | Vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků pro prostředek prostředí Azure čas Series Insights ve verzi Preview. Skupina prostředků představuje kontejner prostředků Azure. Osvědčeným postupem je použít stejnou skupinu prostředků jako ostatní prostředky IoT, které jsou vytvořené v simulátoru zařízení. |
    | **Location** | Vyberte oblast datového centra pro prostředí Azure čas Series Insights ve verzi Preview. Aby se zabránilo další latenci, je nejlepší vytvořit prostředí Azure čas Series Insights ve verzi Preview ve stejné oblasti jako vašich IoT prostředků. |
    | **Tier** |  Vyberte **PAYG** (*s průběžnými platbami*). Toto je SKU produktu Azure čas Series Insights ve verzi Preview. |
    | **ID vlastnosti** | Zadejte hodnotu, která jednoznačně identifikuje instanci času řady. Hodnotu, kterou zadáte v **ID vlastnosti** pole je neměnný. Později ho nelze změnit. Pro účely tohoto kurzu zadejte **iothub připojení zařízení id**. Další informace o ID řady času, naleznete v tématu [osvědčené postupy pro výběr ID řady času](./time-series-insights-update-how-to-id.md). |
    | **Název účtu úložiště** | Zadejte globálně jedinečný název pro nový účet úložiště vytvořit. |
   
   Vyberte **Další: Zdroj události**.

   [![Podokno pro vytvoření prostředí Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na **zdroj události** kartu, nastavit následující parametry:

   | Parametr | Akce |
   | --- | --- |
   | **Vytvoření zdroje událostí?** | Vyberte **Ano**.|
   | **Název** | Zadejte jedinečnou hodnotu pro název zdroje událostí. |
   | **Typ zdroje** | Vyberte **služby IoT Hub**. |
   | **Vyberte centrum** | Zvolte **vybrat existující**. |
   | **Předplatné** | Vyberte předplatné, které jste použili pro simulátor zařízení. |
   | **Název centra IoT** | Vyberte název centra IoT, které jste vytvořili pro simulátor zařízení. |
   | **Zásady přístupu pro službu IoT Hub** | Vyberte **iothubowner**. |
   | **IoT Hub consumer group** | Vyberte **nový**, zadejte jedinečný název a potom vyberte **přidat**. Skupina uživatelů musí být jedinečná hodnota v Azure čas Series Insights ve verzi Preview. |
   | **Vlastnost časového razítka** | Tato hodnota se používá k identifikaci **časové razítko** vlastnost v příchozí telemetrická data. Pro účely tohoto kurzu ponechte toto pole prázdné. Simulátor používá příchozí časové razítko ze služby IoT Hub, kde je použit výchozí Time Series Insights. |

   Vyberte **Zkontrolovat a vytvořit**.

   [![Konfigurace zdroje událostí](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Na **zkontrolovat a vytvořit** kartu zkontrolujte vybrané možnosti a pak vyberte **vytvořit**.

    [![Zkontrolovat a vytvořit stránku, pomocí tlačítka pro vytvoření](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Můžete zobrazit stav nasazení:

    [![Oznámení, že je nasazení dokončeno](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Pokud jste vlastníkem tenanta máte přístup k prostředí Azure čas Series Insights ve verzi Preview. Abyste měli jistotu, že máte přístup:

   1. Vyhledejte skupinu prostředků a poté vyberte vaše prostředí Azure čas Series Insights ve verzi Preview:

      [![Vybrané prostředí](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na stránce Azure čas Series Insights ve verzi Preview vyberte **zásady přístupu k datům**:

      [![Zásady přístupu k datům](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Ověřte, že vaše přihlašovací údaje patří:

      [![Uvedené pověření](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Pokud vaše přihlašovací údaje nejsou uvedené, je nutné si udělit oprávnění pro přístup k prostředí. Další informace o nastavení oprávnění najdete v článku [udělení přístupu k datům](./time-series-insights-data-access.md).

## <a name="stream-data-into-your-environment"></a>Datový Stream do vašeho prostředí

1. Přejděte zpět [stránka akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Vyhledejte řešení v řídicím panelu řešení akcelerátoru. Vyberte **spuštění**:

    [![Spuštění řešení simulace zařízení](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Budete přesměrováni na **simulace zařízení IoT Microsoft Azure** stránky. V pravém horním rohu stránky vyberte **nové simulace**.

    [![Stránka simulace Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. V **nastavení** podokně nastavit následující parametry:

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte jedinečný název pro simulátor. |
    | **Popis** | Zadejte definici. |
    | **Simulation duration** („Doba trvání simulace“) | Nastavte na **běží hodně dlouho**. |
    | **Device model** („Model zařízení“) | **Název**: Zadejte **chladič**. <br />**Velikost**: Zadejte **3**. |
    | **Target IoT Hub** („Cílový IoT Hub“) | Nastavte na **použití předem zřízené služby IoT Hub**. |

    [![Parametry pro nastavení](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Vyberte **spustit simulaci**.

    Na řídicím panelu zařízení simulace, poznamenejte si informace zobrazené pro **aktivních zařízení** a **zpráv za sekundu**.

    [![Simulace řídicí panel služby Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data-in-your-environment"></a>Analýza dat ve vašem prostředí

V této části provedete základní analýzy ve vašich datech časových řad s využitím [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).

1. Přejděte do aplikace Azure čas Series Insights ve verzi Preview explorer tak, že vyberete adresu URL stránky prostředku na [webu Azure portal](https://portal.azure.com/).

    [![Adresa URL explorer čas Series Insights ve verzi Preview](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. V Průzkumníkovi, vyberte **instance řady času** uzlu pro zobrazení všech instancí Azure čas Series Insights ve verzi Preview v prostředí.

    [![Seznam instancí bez nadřazených položek](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Vyberte první instance řady čas. Vyberte **zobrazit tlak**.

    [![Vybraná instance řady čas pomocí příkazu nabídky můžete zobrazit průměrné zatížení](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Zobrazí se graf časové řady. Změnit **Interval** k **15s**.

    [![Graf časové řady](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Opakováním kroků 3 s další dva časové řady instancí. Všechny instance řady času, můžete zobrazit, jak je znázorněno v tomto grafu:

    [![Graf pro všechny časové řady](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. V **časový rámec** možnost pole, změnit časový rozsah, pokud chcete zobrazit trendy řady čas za poslední hodinu:

    [![Nastavte časové rozpětí za hodinu](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definování a použití modelu

V této části použijete model strukturovat vaše data. K dokončení modelu, můžete definovat typy, hierarchie a instance. Další informace o modelování dat, naleznete v tématu [modelu časové řady](./time-series-insights-update-tsm.md).

1. V Průzkumníkovi, vyberte **modelu** kartu:

   [![Karta modelu v Průzkumníku](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Vyberte **přidat** přidejte typ:

   [![Tlačítko Přidat pro typy](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. V dalším kroku definujete tří proměnných pro typ: *tlak*, *teploty*, a *vlhkosti*. V **přidat typ** podokně nastavit následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název** | Zadejte **chladič**. |
    | **Popis** | Zadejte **jde definici typu chladič**. |

   * Chcete-li definovat *tlak*v části **proměnné**, nastavit následující parametry:

     | Parametr | Akce |
     | --- | ---|
     | **Název** | Zadejte **Avg tlak**. |
     | **Hodnota** | Vyberte **tlak (Double)** . Může trvat několik minut, než **hodnotu** automaticky vyplní se po Azure čas Series Insights ve verzi Preview začne přijímat události. |
     | **Agregační operace** | Vyberte **AVG**. |

      [![Výběry pro definování tlaku](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Chcete-li přidat další proměnné, **přidat proměnnou**.

   * Definování *teploty*:

     | Parametr | Akce |
     | --- | ---|
     | **Název** | Zadejte **průměrná teplota**. |
     | **Hodnota** | Vyberte **teploty (Double)** . Může trvat několik minut, než **hodnotu** automaticky vyplní se po Azure čas Series Insights ve verzi Preview začne přijímat události. |
     | **Agregační operace** | Vyberte **AVG**.|

      [![Výběry pro definování teploty](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Chcete-li přidat další proměnné, **přidat proměnnou**.

   * Definování *vlhkosti*:

      | | |
      | --- | ---|
      | **Název** | Zadejte **maximální vlhkost** |
      | **Hodnota** | Vyberte **vlhkosti (Double)** . Může trvat několik minut, než **hodnotu** automaticky vyplní se po Azure čas Series Insights ve verzi Preview začne přijímat události. |
      | **Agregační operace** | Vyberte **maximální**.|

      [![Výběry pro definování teploty](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Vyberte **Vytvořit**.

    Zobrazí se na typ, který jste přidali:

    [![Informace o přidání typu](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Dalším krokem je přidání hierarchie. V části **hierarchie**vyberte **přidat**:

    [![Karta hierarchie s tlačítkem Přidat](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. V **Upravit hierarchii** podokně nastavit následující parametry:

   | Parametr | Akce |
   | --- | ---|
   | **Název** | Zadejte **umístění hierarchie**. |
   | **1. úrovně** | Zadejte **země**. |
   | **Úroveň 2** | Zadejte **Město**. |
   | **Úroveň 3** | Zadejte **vytváření**. |

   Vyberte **Uložit**.

    [![Hierarchie pole pomocí tlačítka pro vytvoření](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Můžete zobrazit hierarchii, kterou jste vytvořili:

    [![Informace o hierarchii](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Vyberte **instance**. Vyberte první instanci a pak vyberte **upravit**:

    [![Vyberte tlačítko Upravit pro instance](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. V **upravit instance** podokně nastavit následující parametry:

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **chladič**. |
    | **Popis** | Zadejte **instanci chladič 01.1**. |
    | **Hierarchie** | Vyberte **umístění hierarchie**. |
    | **Země** | Zadejte **USA**. |
    | **Město** | Zadejte **Seattle**. |
    | **Sestavování** | Zadejte **místo ručička**. |

    [![Pole instance s uložit tlačítkem](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Vyberte **Uložit**.

1. Opakujte předchozí krok pro další senzory. Aktualizací následujících hodnot:

   * Pro chladič 01.2:

     | Parametr | Akce |
     | --- | --- |
     | **Typ** | Vyberte **chladič**. |
     | **Popis** | Zadejte **instanci chladič 01.2**. |
     | **Hierarchie** | Vyberte **umístění hierarchie**. |
     | **Země** | Zadejte **USA**. |
     | **Město** | Zadejte **Seattle**. |
     | **Sestavování** | Zadejte **Tichomoří vědy Center**. |

   * Pro chladič 01.3:

     | Parametr | Akce |
     | --- | --- |
     | **Typ** | Vyberte **chladič**. |
     | **Popis** | Zadejte **instanci chladič 01.3**. |
     | **Hierarchie** | Vyberte **umístění hierarchie**. |
     | **Země** | Zadejte **USA**. |
     | **Město** | Zadejte **New York**. |
     | **Sestavování** | Zadejte **Empire stav vytváření**. |

1. Vyberte **analyzovat** kartu a potom aktualizujte zobrazení stránky. V části **umístění hierarchie**, Rozbalit všechny úrovně hierarchie k zobrazení instance řady čas:

   [![Karta Analyze](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Chcete-li prozkoumat instance řady času za poslední hodinu, změňte **rychlé časy** k **za poslední hodinu**:

    [![Rychlé časy pole s vybrána poslední hodina](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. V části **Tichomoří vědy Center**, vyberte instance řady čas a pak vyberte **zobrazit maximální vlhkost**.

    [![Instance řady vybraný čas a výběr nabídky Zobrazit maximální vlhkost](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Časové řady pro **maximální vlhkost** interval velikosti **1 minuta** otevře. Filtrovat řady, vyberte oblast. Analýza událostí v časovém rámci, klikněte pravým tlačítkem na graf a potom vyberte **přiblížení**:

   [![Vybraný rozsah příkazem zvětšení v místní nabídce](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Pokud chcete zobrazit podrobnosti o události, vyberte oblast a klikněte pravým tlačítkem na graf:

   [![Podrobný seznam událostí](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:  

> [!div class="checklist"]
> * Vytváření a používání akcelerátoru simulaci zařízení.
> * Vytvoření prostředí Azure čas Series Insights ve verzi Preview s průběžnými PLATBAMI.
> * Prostředí Azure čas Series Insights ve verzi Preview připojte do centra událostí.
> * Spuštění ukázky akcelerátor řešení pro streamování dat do prostředí Azure čas Series Insights ve verzi Preview.
> * Proveďte analýzu základní data.
> * Definujte typ modelu časové řady a hierarchie a přidružit vaše instance.

Teď, když víte, jak vytvořit prostředí Azure čas Series Insights ve verzi Preview, přečtěte si další informace o klíčových konceptech Azure Time Series Insights.

Přečtěte si informace o konfiguraci úložiště Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Azure storage čas Series Insights ve verzi Preview a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md)

Další informace o modelech řady čas:

> [!div class="nextstepaction"]
> [Modelování dat Azure čas Series Insights ve verzi Preview](./time-series-insights-update-tsm.md)
