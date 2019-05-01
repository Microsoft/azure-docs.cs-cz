---
title: 'Kurz: Nastavení prostředí Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu'
description: Zjistěte, jak nastavit prostředí v Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: f83063a88207f51f9d481447923fd8a8498692a2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713913"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Kurz: Nastavení prostředí Azure čas Series Insights ve verzi Preview

Tento kurz vás provede procesem vytvoření prostředí Azure čas Series Insights ve verzi Preview průběžných plateb (PAYG). V tomto kurzu se naučíte:

* Vytvoření prostředí Azure čas Series Insights ve verzi Preview.
* Prostředí Azure čas Series Insights ve verzi Preview připojte do centra událostí ve službě Azure Event Hubs.
* Spuštění ukázky akcelerátor řešení pro streamování dat do prostředí Azure čas Series Insights ve verzi Preview.
* Proveďte základní analýzy na data.
* Definujte typ modelu časové řady a hierarchie a přidružte jej k instancím.

# <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

V této části vytvoříte tři Simulovaná zařízení, které budou posílat data do služby IoT hub.

1. Přejděte [stránka akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stránce se zobrazí předem připravených několik příkladů. Přihlaste se pomocí svého účtu Azure. Vyberte **simulace zařízení**.

   [![Stránka akcelerátory řešení Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Vyberte **vyzkoušet**.

1. Na stránce **Vytvoření řešení Simulace zařízení** zadejte požadované parametry:

   | Parametr | Popis |
   | --- | --- |
   | **Název řešení** |    Zadejte jedinečnou hodnotu pro vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků. |
   | **Předplatné** | Zadejte stejné předplatné použité k vytvoření prostředí Time Series Insights. |
   | **Oblast** |   Zadejte stejné oblasti používané pro vytvoření prostředí Time Series Insights. |
   | **Nasadit nepovinné prostředky Azure**    | Ponechte služby IoT Hub vybrána, protože Simulovaná zařízení bude používat pro připojení a Streamovat data. |

   Vyberte **vytvořit řešení**. Počkejte 10 až 15 minut pro vaše řešení nasadit.

   [![Vytvoření stránky řešení simulace zařízení](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Na řídicím panelu řešení akcelerátoru, vyberte **spuštění** tlačítka:

   [![Spuštění řešení simulace zařízení](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Budete přesměrováni na **simulace zařízení IoT Microsoft Azure** stránky. Vyberte **+ nové simulace** v pravém horním rohu stránky.

   [![Stránka simulace Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Vyplňte požadované parametry následujícím způsobem:

    [![Parametry k vyplnění](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    |||
    | --- | --- |
    | **Název** | Zadejte jedinečný název pro simulátor. |
    | **Popis** | Zadejte definici. |
    | **Simulation duration** („Doba trvání simulace“) | Nastavte na **běží hodně dlouho**. |
    | **Device model** („Model zařízení“) | **Název**: Zadejte `Chiller`. </br>**Velikost**: Zadejte `3`. |
    | **Target IoT Hub** („Cílový IoT Hub“) | Nastavte na **použití předem zřízené služby IoT Hub**. |

    Vyberte **spustit simulaci**.

1. Na řídicím panelu simulace zařízení najdete v článku **aktivních zařízení** a **zpráv za sekundu**.

    [![Simulace řídicí panel služby Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>Seznam vlastností simulace zařízení

Před vytvořením prostředí Azure Time Series Insights, musíte názvy centra IoT, předplatné a skupinu prostředků.

1. Přejít na řídicí panel akcelerátor řešení a přihlaste se pomocí stejného účtu předplatného Azure. Najdete simulace zařízení, kterou jste vytvořili v předchozích krocích.

1. Vyberte vaše simulátor zařízení a pak vyberte **spuštění**. Vyberte **portálu pro správu Azure** odkaz na pravé straně.

    [![Simulátor výpisy](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. Poznamenejte si IoT hub, předplatné a názvy skupin prostředků.

    [![Azure portal](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Vytvoření prostředí s průběžnými PLATBAMI čas Series Insights ve verzi Preview

Tato část popisuje, jak vytvořit prostředí Azure čas Series Insights ve verzi Preview s využitím [webu Azure portal](https://portal.azure.com/).

1. Přihlaste se k webu Azure portal pomocí svého účtu předplatného.

1. Vyberte **vytvořit prostředek**.

1. Vyberte **Internet of Things** kategorie a pak vyberte **Time Series Insights**.

   [![Vyberte Internet of Things a pak vyberte Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Vyplňte pole na stránce následujícím způsobem:

   | | |
   | --- | ---|
   | **Název prostředí** | Vyberte jedinečný název pro prostředí Azure čas Series Insights ve verzi Preview. |
   | **Předplatné** | Zadejte vaše předplatné, ve kterém chcete vytvořit prostředí Azure čas Series Insights ve verzi Preview. Je vhodné používat stejné předplatné jako v ostatních vašich IoT prostředků vytvořených v simulátoru zařízení. |
   | **Skupina prostředků** | Skupina prostředků představuje kontejner prostředků Azure. Zvolte existující skupinu prostředků nebo vytvořte novou pro prostředek prostředí Azure čas Series Insights ve verzi Preview. Je osvědčeným postupem použít stejnou skupinu prostředků jako v ostatních vašich IoT prostředků vytvořených v simulátoru zařízení. |
   | **Umístění** | Vyberte oblast datového centra pro prostředí Azure čas Series Insights ve verzi Preview. Aby se zabránilo náklady na přidání šířku pásma a čekací doba, je nejlepší mít prostředí Azure čas Series Insights ve verzi Preview ve stejné oblasti jako ostatní prostředky IoT. |
   | **Tier** |  Vyberte **PAYG**, což je zkratka pro plán průběžných plateb. Toto je SKU produktu Azure čas Series Insights ve verzi Preview. |
   | **ID vlastnosti** | Zadejte objekt, který jednoznačně identifikuje časové řady. Mějte na paměti, že toto pole je neměnná a není možné později změnit. Pro účely tohoto kurzu použijte `iothub-connection-device-id`. Další informace o ID řady času, [návodu k výběru ID řady času](./time-series-insights-update-how-to-id.md). |
   | **Název účtu úložiště** | Zadejte globálně jedinečný název pro nový účet úložiště, který se má vytvořit. |

   Vyberte **Další: Zdroj události**.

   [![Stránka pro vytvoření prostředí Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na stránce pro zdroj události vyplňte pole následujícím způsobem:

   | | |
   | --- | --- |
   | **Vytvoření zdroje událostí?** | Zadejte `Yes`.|
   | **Název** | Zadejte jedinečnou hodnotu, která se používá k pojmenování zdroji události.|
   | **Typ zdroje** | Vyberte **služby IoT Hub**. |
   | **Vyberte centrum?** | Vyberte **vybrat existující**. |
   | **Předplatné** | Vyberte předplatné, které jste použili pro simulátor zařízení. |
   | **Název centra IoT** | Vyberte název centra IoT, které jste vytvořili pro simulátor zařízení. |
   | **Zásady přístupu pro službu IoT Hub** | Vyberte **iothubowner**. |
   | **Skupina uživatelů centra IOT** | Budete potřebovat skupinu příjemců jedinečný pro Azure čas Series Insights ve verzi Preview. Vyberte **nový**, zadejte jedinečný název a potom vyberte **přidat**. |
   | **Vlastnost časového razítka** | Toto pole se používá k identifikaci názvu vlastnosti časového razítka v příchozí telemetrická data. Pro účely tohoto kurzu není vyplňte pole. Simulátor používá příchozí časové razítko ze služby IoT Hub, kde je použit výchozí Time Series Insights.|

   Vyberte **revize + vytvořit**.

   [![Konfigurace zdroje událostí](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Zkontrolujte všechna pole na stránce Zkontrolovat a vyberte **vytvořit**.

   [![Zkontrolovat a vytvořit stránku, pomocí tlačítka pro vytvoření](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

1. Zobrazí se stav nasazení.

   [![Oznámení, že je nasazení dokončeno](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Pokud vlastníte tenanta, měli byste obdržet přístup k prostředí Azure čas Series Insights ve verzi Preview. Abyste měli jistotu, že máte přístup:

   a. Vyhledejte skupinu prostředků a vyberte vaše prostředí Azure čas Series Insights ve verzi Preview:

      [![Vybrané prostředí](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   b. Na stránce Azure čas Series Insights ve verzi Preview, přejděte na **zásady přístupu k datům**.

     [![Zásady přístupu k datům](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   c. Ověřte, že vaše přihlašovací údaje jsou uvedeny.

     [![Uvedené pověření](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Pokud vaše přihlašovací údaje nejsou uvedené, budete muset sami sobě udělit oprávnění pro přístup k prostředí. Další informace o nastavení oprávnění najdete v článku [udělení přístupu k datům](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analýza dat ve vašem prostředí

V této části provedete základní analýzy ve vašich datech časových řad s využitím [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).

1. Přejděte do aplikace Azure čas Series Insights ve verzi Preview explorer tak, že vyberete adresu URL stránky prostředku na [webu Azure portal](https://portal.azure.com/).

   [![Adresa URL explorer čas Series Insights ve verzi Preview](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. V Průzkumníkovi, vyberte **instance řady času** uzlu pro zobrazení všech instancí Azure čas Series Insights ve verzi Preview v prostředí.

   [![Seznam instancí bez nadřazených položek](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. V časové řadě zobrazí vyberte první instanci. Vyberte **zobrazit Avg tlak**.

   [![Vybraná instance pomocí příkazu nabídky můžete zobrazit průměrné zatížení](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

   Na pravé straně by se zobrazit graf časové řady. Upravit **Interval** k `15s`.

   [![Graf časové řady](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Opakujte **kroku 3** k ostatním dvou časových řad. Pak můžete zobrazit všechny časové řady, jak je znázorněno v tomto grafu:

   [![Graf pro všechny časové řady](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Upravte časový rozsah zobrazíte čas řady trendy za poslední hodinu.

   a. Vyberte **časový rámec** možnost pole:

      [![Nastavte časové rozpětí za hodinu](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definování a použití modelu

V této části použijete model strukturovat vaše data. K dokončení modelu, můžete definovat typy, hierarchie a instance. Další informace o modelování dat, přejděte na [modelu časové řady](./time-series-insights-update-tsm.md).

1. V Průzkumníkovi, vyberte **modelu** kartu:

   [![Karta modelu v Průzkumníku](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Vyberte **+ přidat** přidání typu. Na pravé straně se otevře editor typů.

   [![Tlačítko Přidat pro typy](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Definujte proměnné, tři typ: přetížení, teploty a vlhkosti. Zadejte následující informace:

   | | |
   | --- | ---|
   | **Název** | Zadejte `Chiller`. |
   | **Popis** | Zadejte `This is a type definition of Chiller`. |

   * Definujte přetížení s tří proměnných:

      | | |
      | --- | ---|
      | **Název** | Zadejte `Avg Pressure`. |
      | **Hodnota** | Vyberte **tlak (Double)**. Všimněte si, že toto pole může trvat několik minut, který se má naplnit po Azure čas Series Insights ve verzi Preview začne přijímat události. |
      | **Agregační operace** | Vyberte **AVG**. |

      [![Výběry pro definování tlaku](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Vyberte **+ přidat proměnnou** přidáte další proměnné.

   * Definujte teploty:

      | | |
      | --- | ---|
      | **Název** | Zadejte `Avg Temperature`. |
      | **Hodnota** | Vyberte **teploty (Double)**. Všimněte si, že toto pole může trvat několik minut, který se má naplnit po Azure čas Series Insights ve verzi Preview začne přijímat události. |
      | **Agregační operace** | Vyberte **AVG**.|

      [![Výběry pro definování teploty](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

   * Definujte vlhkosti:

      | | |
      | --- | ---|
      | **Název** | Enter `Max Humidity` |
      | **Hodnota** | Vyberte **vlhkosti (Double)**. Všimněte si, že toto pole může trvat několik minut, který se má naplnit po Azure čas Series Insights ve verzi Preview začne přijímat události. |
      | **Agregační operace** | Vyberte **maximální**.|

      [![Výběry pro definování teploty](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

   Potom vyberte **Create** (Vytvořit).

1. Zobrazí typ přidán:

   [![Informace o přidání typu](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Dalším krokem je přidání hierarchie. V **hierarchie** vyberte **+ přidat**:

   [![Karta hierarchie s tlačítkem Přidat](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Definujte hierarchii. Vyplňte pole následujícím způsobem:

   | | |
   | --- | ---|
   | **Název** | Zadejte `Location Hierarchy`. |
   | **1. úrovně** | Zadejte `Country`. |
   | **Úroveň 2** | Zadejte `City`. |
   | **Úroveň 3** | Zadejte `Building`. |

   Potom vyberte **Create** (Vytvořit).

   [![Hierarchie pole pomocí tlačítka pro vytvoření](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

1. Můžete zobrazit hierarchii, kterou jste vytvořili:

   [![Informace o hierarchii](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Vyberte **instance** na levé straně. Po instance se zobrazí, vyberte první instanci a potom vyberte **upravit**:

   [![Vyberte tlačítko Upravit pro instance](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. Na pravé straně se zobrazí v textovém editoru. Přidejte následující informace:

   | | |
   | --- | --- |
   | **Typ** | Vyberte **chladič**. |
   | **Popis** | Zadejte `Instance for Chiller-01.1`. |
   | **Hierarchie** | Vyberte **umístění hierarchie**. |
   | **Země** | Zadejte `USA`. |
   | **Město** | Zadejte `Seattle`. |
   | **Sestavování** | Zadejte `Space Needle`. |

    Potom vyberte **Uložit**.

   [![Pole instance s tlačítko Uložit](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

1. Opakujte předchozí krok pro další senzory. Pomocí následujících polí:

   * Pro chladič 01.2:

     | | |
     | --- | --- |
     | **Typ** | Vyberte **chladič**. |
     | **Popis** | Zadejte `Instance for Chiller-01.2`. |
     | **Hierarchie** | Vyberte **umístění hierarchie**. |
     | **Země** | Zadejte `USA`. |
     | **Město** | Zadejte `Seattle`. |
     | **Sestavování** | Zadejte `Pacific Science Center`. |

   * Pro chladič 01.3:

     | | |
     | --- | --- |
     | **Typ** | Vyberte **chladič**. |
     | **Popis** | Zadejte `Instance for Chiller-01.3`. |
     | **Hierarchie** | Vyberte **umístění hierarchie**. |
     | **Země** | Zadejte `USA`. |
     | **Město** | Zadejte `New York`. |
     | **Sestavování** | Zadejte `Empire State Building`. |

1. Přejděte **analyzovat** kartu a aktualizujte stránku. Rozbalte všechny úrovně hierarchie k vyhledání časové řady.

   [![Karta Analyze](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Chcete-li prozkoumat časové řady za poslední hodinu, změňte **rychlé časy** k **za poslední hodinu**:

    [![Rychlé časy pole s vybrána poslední hodina](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Vyberte časové řady pod **Tichomoří vědy Center** a vyberte **zobrazit maximální vlhkost**.

    [![Vybrané časové řady s výběrem nabídky Zobrazit maximální vlhkost](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Časové řady pro **maximální vlhkost** interval velikosti **1 minuta** otevře. Vyberte oblast, kterou chcete filtrovat řady. Potom klikněte pravým tlačítkem a vyberte **přiblížení** analyzovat události v časovém rámci:

   [![Vybraný rozsah příkazem zvětšení v místní nabídce](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Můžete také vybrat oblast a potom klikněte pravým tlačítkem myši zobrazíte detaily události:

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