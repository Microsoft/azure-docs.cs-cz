---
title: Kurz služby Azure Time Series Insights (Preview) | Dokumentace Microsoftu
description: Seznamte se s Azure Time Series Insights (Preview)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: d4f69533a68e11b3e171963429b141cf0736472d
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014629"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Kurz služby Azure Time Series Insights (Preview)

Tento kurz vás provede procesem vytvoření prostředí Azure Time Series Insights (TSI) ve verzi Preview, naplněný daty ze simulovaných zařízení. V tomto kurzu se naučíte:

* Vytvoření prostředí služby TSI (Preview).
* Připojení prostředí TSI (Preview) v Centru událostí.
* Spuštění simulace větrné farmy pro streamování dat do prostředí TSI Preview.
* Proveďte základní analýzy na data.
* Definujte typ modelu časové řady a hierarchie a přidružte jej k instancím.

## <a name="create-a-time-series-insights-preview-environment"></a>Vytvoření prostředí Time Series Insights (Preview)

Tato část popisuje, jak vytvořit prostředí Azure TSI (Preview) pomocí [webu Azure Portal](https://portal.azure.com/).

1. Přihlaste se pomocí svého účtu předplatného webu Azure Portal
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**.
1. Vyberte kategorii **Internet věcí** a potom zvolte **Time Series Insights**.

   ![kurz: 1][1]

1. Na stránce prostředí Time Series Insights, vyplňte požadované parametry a klikněte na **Další: zdroj událostí**. Pro účely tohoto kurzu nastavte **ID řady času** jako `Id`. Další informace o **čas řady ID**, najdete v tématu [čas řady ID](./time-series-insights-update-how-to-id.md).

   ![kurz 2][2]

1. Na **zdroj události** stránce, vyplňte požadované parametry a klikněte na **revize + vytvořit**. Pro účely tohoto kurzu nastavte **časové razítko** vlastnost pole jako `Timestamp`.

   ![kurz 3][3]

1. Zkontrolujte všechny informace a klikněte na **vytvořit** zahájíte zřizování prostředí.

   ![kurz 4][4]

1. Až se nasazení dokončí úspěšně, dostanete oznámení.

   ![kurz – pět][5]

## <a name="send-events-to-your-tsi-environment"></a>Odesílání událostí do prostředí TSI

V této části použijete windmill simulátor zařízení k odesílání událostí do prostředí TSI prostřednictvím centra událostí.

  1. Na webu Azure Portal přejděte k prostředku centra událostí a připojení k prostředí TSI. Přečtěte si [jak se připojit k existující centrum událostí váš prostředek](./time-series-insights-how-to-add-an-event-source-eventhub.md).

  1. Na stránce prostředků centra událostí, přejděte na **sdílené zásady přístupu** a potom **RootManageSharedAccessKey**. Kopírovat **připojovací řetězec – primární klíč** tady zobrazí.

      ![kurz – 6][6]

  1. Přejděte do [ (Nastavení)https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html) (Integrace a služby). Tato webová aplikace simuluje windmill zařízení.
  1. Vložit připojovací řetězec zkopíruje z kroku 3 v **připojovací řetězec centra událostí**

      ![kurz – sedm][7]

  1. Klikněte na **klikněte na tlačítko Start** odesílání událostí do vašeho centra událostí. V této fázi se soubor s názvem `instances.json` bude stažena do vašeho počítače. Tento soubor uložte, protože jsme budete potřebovat později.

  1. Vraťte se do vašeho centra událostí. Teď byste měli vidět nové události přijímá hub.d

      ![kurz – 8][8]

## <a name="analyze-data-in-your-environment"></a>Analýza dat ve vašem prostředí

V této části provedete základní analýzy na dobu aktualizace řady dat Time Series Insights pomocí Průzkumníka.

  1. Kliknutím na adresu URL na stránce prostředků na webu Azure Portal přejděte do vaší aktualizace Průzkumníka Time Series Insights.

      ![kurz devět][9]

  1. V Průzkumníkovi, klikněte na **bez nadřazených položek instance** uzlů, čímž zobrazíte všechny časové řady instance v prostředí.

      ![kurz – deset][10]

  1. V tomto kurzu vytvoříme analýza dat odeslaných za poslední den. Pokud chcete udělat, klikněte na **rychlé časy** a vyberte **posledních 24 hodin** možnost.

      ![kurz jedenáct][11]

  1. Vyberte **Sensor_0** a zvolte **zobrazit průměrné hodnoty** k vizualizaci dat odesílaných z této instance řady čas.

      ![kurz – 12][12]

  1. Podobně můžete zobrazit data přicházející z jiné instance řady času provádět základní analýzy.

      ![kurz třináct][13]

## <a name="define-a-type-and-hierarchy"></a>Definujte typ a hierarchie

V této části se vytvořit typ, hierarchie a přidružit k vaší instance řady času. Další informace o [čas řady modely](./time-series-insights-update-tsm.md).

  1. V Průzkumníkovi, klikněte na **modelu** kartu na panelu aplikací.

      ![kurz čtrnáct][14]

  1. V oddílu typy, klikněte na **+ přidat**. To vám umožní vytvořit nový typ modelu řady čas.

      ![kurz patnáct][15]

  1. V Editoru typů, zadejte **název**, **popis**, vytvářet proměnné pro **průměrné**, **Min**, a **maximální** hodnoty, jak je znázorněno níže. Klikněte na **vytvořit** uložte typ.

      ![kurz – šestnáct][16]

      ![kurz sedmnáct][17]

  1. V **hierarchie** části, klikněte na **+ přidat**. To vám umožní vytvořit nový Model řady časovou hierarchii.

     ![kurz osmnáct][18]

  1. V editoru hierarchie, zadejte **název** a přidat hierarchie úrovní, jak je znázorněno níže. Klikněte na **vytvořit** uložit hierarchii.

     ![kurz devatenáct][19]

     ![dvacet kurz][20]

  1. V **instance** , vyberte výchozí instanci a klikněte na **upravit**. To vám umožní přidružit tuto instanci typu a hierarchie.

     ![kurz: dvacet 1][21]

  1. V editoru instance zvolte typ a hierarchii definovaný v krocích 3, 5 výše, jak je znázorněno.

     ![kurz dvacet dvě][22]

  1. Provedete to pro všechny instance najednou, případně můžete upravit `instances.json` soubor, který byl předtím stáhli. V tomto souboru Nahradit vše **typeId** a **hierarchyId** pole s ID získané z kroky 3, 5 výše.

  1. V **instance** části, klikněte na **nahrát JSON** a nahrát upravený `instances.json` sdílené, jak je znázorněno níže.

     ![kurz dvacet tři][23]

  1. Přejděte **Analytics** kartu a aktualizujte svůj prohlížeč. Teď byste měli vidět všechny instance přidružené k typu a hierarchii výše.

     ![kurz – 24][24]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:  

* Vytvoření prostředí služby TSI (Preview).
* Připojení prostředí TSI (Preview) v Centru událostí.
* Spuštění simulace větrné farmy pro streamování dat do prostředí TSI (Preview).
* Proveďte základní analýzy na data.
* Definování typu modelu časové řady, hierarchie a přidružte jej k instancím.

Teď už víte, jak vytvořit vlastní prostředí TSI aktualizace, přečtěte si informace o klíčových konceptech ve službě TSI.

Přečtěte si informace o konfiguraci úložiště TSI:

> [!div class="nextstepaction"]
> [Úložiště Azure TSI (Preview) a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md)

Další informace o modelech řady čas:

> [!div class="nextstepaction"]
> [Azure modelování dat TSI (Preview)](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png