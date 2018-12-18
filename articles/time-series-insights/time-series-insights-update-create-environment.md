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
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 965fd4b0ca5defc2b1bf633af72ac250942d838f
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554805"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Kurz: Nastavení prostředí Azure čas Series Insights ve verzi Preview

Tento kurz vás provede procesem vytvoření prostředí Azure Time Series Insights průběžných plateb (PAYG) ve verzi Preview. V tomto kurzu se naučíte:

* Vytvoření prostředí Azure čas Series Insights ve verzi Preview.
* Prostředí Azure čas Series Insights ve verzi Preview připojte do centra událostí ve službě Azure Event Hubs.
* Spuštění simulace větrné farmy pro streamování dat do prostředí Azure čas Series Insights ve verzi Preview.
* Proveďte základní analýzy na data.
* Definujte Time Series Model typu a hierarchie a přidružte jej k instancím.

# <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

V této části vytvoříte tři Simulovaná zařízení, které budou posílat data do služby IoT Hub.

1. Přejděte [stránka domovské akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na domovské stránce akcelerátory řešení Azure IoT zobrazí několik předem vytvořené příklady. Přihlaste se pomocí svého účtu Azure. Vyberte **simulace zařízení**.

   ![Azure IoT řešení akcelerátory domovskou stránku][1]

   A konečně, klikněte na tlačítko **vyzkoušet**.

1. Zadejte požadované parametry na **vytvořit simulace zařízení** stránka řešení:

   | Parametr | Popis |
   | --- | --- |
   | Název řešení |    Jedinečná hodnota sloužící k vytvoření nové skupiny prostředků. Všechny uvedené prostředky Azure | Vytvoření a přiřazení do skupiny prostředků. |
   | Předplatné | Zadejte stejné předplatné používá pro vytvoření prostředí služby TSI |
   | Oblast |   Zadejte stejné oblasti používané pro vytvoření vaší služby TSI. |
   | Nasadit nepovinné prostředky Azure    | Nechte IoT Hub zaškrtnuto, jako Simulovaná zařízení bude používat pro připojení/stream data. |

   Po zadání požadovaných parametrů, klikněte na **vytvořit řešení**. Počkejte přibližně 10 – 15 minut, než se vaše řešení se nasadí.

   ![Vytvoření řešení simulace zařízení][2]

1. Ve vaší **řídicí panel akcelerátor řešení**, klikněte na tlačítko **spuštění** tlačítko:

   ![Spuštění řešení simulace zařízení][3]

1. Budete přesměrováni na **simulace zařízení IoT Microsoft Azure** stránky. Klikněte na tlačítko **+ nové simulace** nachází v pravém horním rohu obrazovky.

   ![Stránka simulace Azure IoT][4]

1.  Vyplňte požadované parametry následujícím způsobem:

    ![Parametry k vyplnění][5]

    |||
    | --- | --- |
    | **Název** | Zadejte jedinečný název pro simulátor |
    | **Popis** | Zadejte definici |
    | **Doba trvání simulace** | Nastavte na `Run indefinitely` |
    | **Model zařízení** | **Název**: Zadejte `Chiller` **částka**: Zadejte `3` |
    | **Target IoT Hub** („Cílový IoT Hub“) | Nastavte na `Use pre-provisioned IoT Hub` |

    Po vyplnění požadovaných parametrů, klikněte na **Start simulace**.

1. Na řídicím panelu simulace zařízení najdete v článku **aktivních zařízení** a **zpráv za sekundu**.

    ![Simulace řídicí panel služby Azure IoT][6]

## <a name="list-device-simulation-properties"></a>Seznam vlastností simulace zařízení

Před vytvořením prostředí Azure Time Series Insights, budete potřebovat názvy služby IoT Hub, předplatné a název skupiny prostředků.

1. Přejděte **řídicí panel akcelerátor řešení** a přihlaste se pomocí stejného účtu předplatného Azure. Najdete simulace zařízení, kterou jste vytvořili v předchozích krocích.

1. Klikněte na vaše simulátor zařízení a klikněte na tlačítko **spuštění**. Klikněte na **portálu pro správu Azure** odkazu se zobrazí na pravé straně.

    ![Simulátor výpisy][7]

1. Poznamenejte si IoT Hub, předplatné a názvy skupin prostředků.

    ![portál Azure][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Vytvoření prostředí s průběžnými PLATBAMI čas Series Insights ve verzi Preview

Tato část popisuje, jak vytvořit prostředí Azure čas Series Insights ve verzi Preview s využitím [webu Azure portal](https://portal.azure.com/).

1. Přihlaste se k webu Azure portal pomocí svého účtu předplatného.

1. Vyberte **vytvořit prostředek**.

1. Vyberte **Internet of Things** kategorie a pak vyberte **Time Series Insights**.

   ![Vyberte možnost vytvořit prostředek, pak vyberte Internet of Things a pak vyberte Time Series Insights][9]

1. Vyplnění polí na stránce následujícím způsobem:

   | | |
   | --- | ---|
   | **Název prostředí** | Vyberte jedinečný název pro prostředí Azure čas Series Insights ve verzi Preview. |
   | **Předplatné** | Zadejte vaše předplatné, ve kterém chcete vytvořit prostředí Azure čas Series Insights ve verzi Preview. Je vhodné používat stejné předplatné jako v ostatních vašich IoT prostředků vytvořených v simulátoru zařízení. |
   | **Skupina prostředků** | Skupina prostředků představuje kontejner prostředků Azure. Zvolte existující skupinu prostředků nebo vytvořte novou pro prostředek prostředí Azure čas Series Insights ve verzi Preview. Je osvědčeným postupem použít stejnou skupinu prostředků jako v ostatních vašich IoT prostředků vytvořených v simulátoru zařízení. |
   | **Umístění** | Zvolte oblast System center data pro vaše prostředí Azure čas Series Insights ve verzi Preview. Aby se zabránilo náklady na přidání šířku pásma a čekací doba, je nejlepší mít prostředí Azure čas Series Insights ve verzi Preview ve stejné oblasti jako ostatní prostředky IoT. |
   | **Úroveň** |  Vyberte `PAYG` což je zkratka pro plán průběžných plateb. Toto je SKU pro produkt Azure čas Series Insights ve verzi Preview. |
   | **ID vlastnosti** | Jednoznačně identifikuje časové řady. Mějte na paměti, že toto pole je neměnná a není možné později změnit. V tomto kurzu nastavte pole na `iothub-connection-device-id`. Další informace o ID řady času, [návodu k výběru ID řady času](./time-series-insights-update-how-to-id.md). |
   | **Název účtu úložiště** | Zadejte globální jedinečný název pro nový účet úložiště, který se má vytvořit. |

   Po vyplnění polí výše, klikněte na tlačítko **Další: Zdroj události**.

   ![Klikněte na tlačítko Další: Zdroj události][10]

1. Na stránce vyplňte pole následujícím způsobem:

   | | |
   | --- | --- |
   | **Vytvoření zdroje událostí?** | Zadejte `Yes`|
   | **Název** | Pro název zdroje událostí zadejte jedinečnou hodnotu.|
   | **Typ zdroje** | Zadejte `IoT Hub` |
   | **Vyberte centrum?** | Zadejte `Select Existing` |
   | **Předplatné** | Zadejte předplatné, které jste použili pro simulátor zařízení. |
   | **Název centra IoT** | Zadejte název centra IoT, který jste vytvořili pro simulátor zařízení. |
   | **Zásady přístupu pro službu IoT Hub** | Zadejte `iothubowner` |
   | **Skupina uživatelů centra IOT** | Budete potřebovat skupinu příjemců jedinečný pro Azure čas Series Insights ve verzi Preview. |
   | **Časové razítko** | Toto pole se používá k identifikaci názvu vlastnosti časového razítka v příchozí telemetrická data. Pro účely tohoto kurzu nevyplní pole. Simulátor používá příchozí časové razítko ze služby IoT Hub, kde je použit výchozí Time Series Insights.|

   Chcete-li vytvořit skupinu příjemců jedinečný:

   1. Klikněte na tlačítko **nový** vedle **skupinu příjemců IoT Hub** pole:

      ![Klikněte na tlačítko Další: Zdroj události][11]

   1. Zadejte skupinu příjemců jedinečný název a klikněte na tlačítko **přidat**:

      ![Klikněte na tlačítko Přidat.][12]

   Po vyplnění polí výše, klikněte na tlačítko **revize + vytvořit**.

      ![Zkontrolovat a vytvořit][13]

1. Zkontrolujte všechna pole na stránce kontroly a klikněte na **vytvořit**.

   ![Vytvořit][14]

1. Zobrazí se stav nasazení.

   ![Nasazení dokončeno][15]

1. Pokud jste vlastníkem tenanta, měli byste obdržet přístup pro vaše prostředí time Series insights. Abyste měli jistotu, že máte přístup:

   * Přejděte do nově vytvořeného prostředí Azure čas Series Insights ve verzi Preview. Provedete to tak, že vaše skupina prostředků. Potom klikněte na vaše prostředí time Series insights:

      ![Nasazení dokončeno][16]

   * Na stránce Azure čas Series Insights ve verzi Preview, přejděte na **zásady přístupu k datům**.

     ![Zásady přístupu k datům][17]

   * Ověřte, že vaše přihlašovací údaje jsou uvedeny.

     ![Ověřit vaše přihlašovací údaje][18]

   Pokud vaše přihlašovací údaje nejsou uvedené, budete muset sami sobě udělit oprávnění pro přístup k prostředí. Čtení [udělit přístup k datům](./time-series-insights-data-access.md) Další informace o nastavení oprávnění.

## <a name="analyze-data-in-your-environment"></a>Analýza dat ve vašem prostředí

V této části provedete základní analýzy ve vašich datech časových řad s využitím [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).

1. Přejděte do aplikace Azure čas Series Insights ve verzi Preview explorer kliknutím na adresu URL ze stránky prostředku [webu Azure portal](https://portal.azure.com/).

   ![Adresa URL Průzkumníka Time Series Insights][19]

1. V Průzkumníkovi, vyberte **bez nadřazených položek instance** uzlů, čímž zobrazíte všechny Azure čas Series Insights ve verzi Preview v prostředí.

   ![Seznam instancí bez nadřazených položek][20]

1. V časové řadě zobrazí klikněte na první instanci. Potom klikněte na **zobrazit Avg tlak**.

   ![Zobrazit průměrnou tlaku][21]

1. Graf časové řady by se měla objevit na pravé straně:

   ![Graf časové řady][22]

1. Opakujte **kroku 3** k ostatním dvou časových řad. Všechny časové řady může prohlížet jak je znázorněno níže:

   ![Graf všechny časové řady][23]

1. Upravit **časový rozsah** zobrazíte čas řady trendy za poslední hodinu. Vyberte **z** políčko možnost, jak je znázorněno níže:

   ![Vyberte možnost From][24]

1. Změnit čas v rámci **z** možnost pole se zobrazí události od poslední hodiny:

   ![Vyberte možnost From][25]

1. Pak můžete porovnat tlak na všech třech zařízeních za poslední hodinu:

   ![Vyberte možnost From][26]

## <a name="define-and-apply-a-model"></a>Definování a použití modelu

V této části použijete model strukturovat vaše data. Dokončete modelu budou definovat typy, hierarchie a instance. Další informace o modelování dat, přejděte na [čas řady modely](./time-series-insights-update-tsm.md).

1. V Průzkumníkovi, vyberte **modelu** kartu:

   ![Vyberte kartu modelu][27]

1. Pak klikněte na **+ přidat** přidání typu. Na pravé straně se otevře editor typů.

   ![Klikněte na tlačítko Přidat.][28]

1. Dále definujte tří proměnných: Přetížení, teploty a vlhkosti v typu. Zadejte následující pole:

   | | |
   | --- | ---|
   | **Název** | Zadejte `Chiller` |
   | **Popis** | Zadejte `This is a type definition of Chiller` |

   * Nyní definujte přetížení s tří proměnných:

      | | |
      | --- | ---|
      | **Název** | Zadejte `Avg Pressure` |
      | **Hodnota** | Vyberte **tlak (Double)**. Mějte na paměti, může trvat několik minut, než toto pole k naplnění po Azure Time Series Insights začne přijímat události |
      | **Agregační operace** | Vyberte `AVG` |

      ![Přidat proměnnou][29]

      Klikněte na **+ proměnné** přidáte další proměnné.

   * Nyní definujte teploty:

      | | |
      | --- | ---|
      | **Název** | Zadejte `Avg Temperature` |
      | **Hodnota** | Vyberte **teploty (Double)**. Mějte na paměti, může trvat několik minut, než toto pole k naplnění po Azure Time Series Insights začne přijímat události |
      | **Agregační operace** | Vyberte `AVG`|

      ![Definování teploty][30]

   * Nyní definujte vlhkosti:

      | | |
      | --- | ---|
      | **Název** | Zadejte `Max Humidity` |
      | **Hodnota** | Vyberte **vlhkosti (Double)**. Mějte na paměti, může trvat několik minut, než toto pole k naplnění po Azure Time Series Insights začne přijímat události |
      | **Agregační operace** | Vyberte `MAX`|

      ![Definování teploty][31]

   Po definování proměnných, klikněte na tlačítko **vytvořit**.

1. Zobrazí typ přidán:

   ![Zobrazit typ přidán][32]

1. Dalším krokem je přidání hierarchie. V **hierarchie** vyberte **+ přidat** k vytvoření nové hierarchie:

   ![Přidat hierarchii][33]

1. Definujte hierarchii. Zadejte pole následujícím způsobem:

   | | |
   | --- | ---|
   | **Název** | Zadejte `Location Hierarchy` |
   | **1. úrovně** | Zadejte `Country` |
   | **Úroveň 2** | Zadejte `City` |
   | **Úroveň 3** | Zadejte `Building` |

   Po vyplnění polí výše, klikněte na **vytvořit**.

   ![Definujte hierarchii][34]

1. Zobrazí se hierarchie vytvořili:

   ![Zobrazit hierarchii][35]

1. Po definování hierarchie, klikněte na tlačítko **instance** na levé straně. Poté, co instance se zobrazí, klikněte na první instanci a vyberte **upravit**:

   ![Úprava instance][36]

1. Na pravé straně se zobrazí v textovém editoru. Přidejte následující pole:

   | | |
   | --- | --- |
   | **Typ** | Vyberte `Chiller` |
   | **Popis** | Zadejte `Instance for Chiller-01.1` |
   | **Hierarchie** | Povolit `Location Hierarchy` |
   | **Země** | Zadejte `USA` |
   | **Město** | Zadejte `Seattle` |
   | **Sestavování** | Zadejte `Space Needle` |

    Po vyplnění polí výše, klikněte na tlačítko **Uložit**.

   ![Uložit chladič][37]

1. Opakujte předchozí krok pro další senzory. Pomocí následujících polí:

   * Pro chladič 01.2:

     | | |
     | --- | --- |
     | **Typ** | Vyberte `Chiller` |
     | **Popis** | Zadejte `Instance for Chiller-01.2` |
     | **Hierarchie** | Povolit `Location Hierarchy` |
     | **Země** | Zadejte `USA` |
     | **Město** | Zadejte `Seattle` |
     | **Sestavování** | Zadejte `Pacific Science Center` |

   * Pro chladič 01.3:

     | | |
     | --- | --- |
     | **Typ** | Vyberte `Chiller` |
     | **Popis** | Zadejte `Instance for Chiller-01.1` |
     | **Hierarchie** | Povolit `Location Hierarchy` |
     | **Země** | Zadejte `USA` |
     | **Město** | Zadejte `New York` |
     | **Sestavování** | Zadejte `Empire State Building` |

1. Přejděte na **analyzovat** kartu a aktualizujte stránku. Rozbalte všechny úrovně hierarchie k vyhledání časové řady.

   ![Zobrazit kartu analýza][38]

1. Chcete-li prozkoumat časové řady za poslední hodinu, změňte **rychlé časy** do za poslední hodinu:

   ![Prozkoumat poslední hodina][39]

1. Klikněte na časy řady pod **Tichomoří vědy Center** a klikněte na tlačítko **zobrazit maximální vlhkost**.

   ![Zobrazit maximální vlhkost][40]

1. Časové řady pro **maximální vlhkost** s intervalem se otevře velikost 1 minuta. Kliknutím levého tlačítka myši oblast, kterou chcete filtrovat řady. Potom klikněte pravým tlačítkem a zvětšit analýza událostí v rámci čas:

   ![Zobrazit, filtrovat a přiblížení][41]

   ![Zobrazit, filtrovat a přiblížení][42]

1. Můžete také kliknutím levého tlačítka myši do oblasti a klepněte pravým tlačítkem myši zobrazíte detaily události:

   ![Zobrazit, filtrovat a přiblížení][43]

   ![Zobrazit, filtrovat a přiblížení][44]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:  

* Vytváření a používání akcelerátoru simulaci zařízení.
* Vytvoření prostředí Azure čas Series Insights ve verzi Preview s průběžnými PLATBAMI.
* Prostředí Azure čas Series Insights ve verzi Preview připojte do centra událostí.
* Spuštění simulace větrné farmy pro streamování dat do prostředí Azure čas Series Insights ve verzi Preview.
* Proveďte analýzu základní data.
* Definujte typ modelu časové řady a hierarchie a přidružit vaše instance.

Teď, když víte, jak vytvořit prostředí Azure čas Series Insights ve verzi Preview, přečtěte si další informace o klíčových konceptech Azure Time Series Insights.

Přečtěte si informace o konfiguraci úložiště Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Azure storage čas Series Insights ve verzi Preview a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md)

Další informace o modelech řady čas:

> [!div class="nextstepaction"]
> [Modelování dat Azure čas Series Insights ve verzi Preview](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png