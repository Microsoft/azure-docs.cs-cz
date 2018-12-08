---
title: Nastavte si prostředí kurz Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Zjistěte, jak nastavit prostředí v Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: 20cec1305f84bd1ff7e01f2e1d38f374aa17bc6f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106668"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Kurz: Nastavení prostředí Azure čas Series Insights ve verzi Preview

Tento kurz vás provede procesem vytvoření prostředí Azure čas Series Insights ve verzi Preview, který je vyplněný daty ze simulovaných zařízení. V tomto kurzu se naučíte:

* Vytvoření prostředí čas Series Insights ve verzi Preview.
* Připojení prostředí čas Series Insights ve verzi Preview do centra událostí ve službě Azure Event Hubs.
* Spuštění simulace větrné farmy pro streamování dat do prostředí čas Series Insights ve verzi Preview.
* Proveďte základní analýzy na data.
* Definujte typ modelu časové řady a hierarchie a přidružte jej k instancím.

## <a name="create-a-time-series-insights-preview-environment"></a>Vytvoření prostředí čas Series Insights ve verzi Preview

Tato část popisuje, jak vytvořit čas Series Insights ve verzi Preview prostředí pomocí [webu Azure portal](https://portal.azure.com/).

1. Přihlaste se k webu Azure portal pomocí svého účtu předplatného.

1. Vyberte **vytvořit prostředek**.

1. Vyberte **Internet of Things** kategorie a pak vyberte **Time Series Insights**.

  ![Vyberte možnost vytvořit prostředek, pak vyberte Internet of Things a pak vyberte Time Series Insights][1]

1. Na **Základy** kartu, zadejte požadované parametry a pak vyberte **Další: Zdroj události**

  ![Na kartě Základy prostředí Time Series Insights a další: tlačítko Zdroj události][2]

1. Na **zdroj události** kartu, zadejte požadované parametry a pak vyberte **revize + vytvořit**.

  ![Na kartě Zdroj události a kontrola + tlačítko vytvořit.][3]

1. Na **Souhrn** kartu, projděte si všechny informace a vyberte **vytvořit** zahájíte zřizování prostředí.

  ![Karta Souhrn a tlačítka pro vytvoření][4]

1. Po úspěšné nasazení se zobrazí oznámení.

  ![Oznámení nasazení bylo úspěšné.][5]

## <a name="send-events-to-your-time-series-insights-environment"></a>Odesílání událostí do prostředí Time Series Insights

V této části použijete windmill simulátor zařízení k odesílání událostí do prostředí Time Series Insights prostřednictvím centra událostí.

  1. Na webu Azure Portal přejděte na váš prostředek centra událostí a připojení k prostředí Time Series Insights. Další postup najdete v tématu [připojit prostředek k existujícím Centru událostí](./time-series-insights-how-to-add-an-event-source-eventhub.md).

  1. Na stránce prostředků centra událostí, přejděte na **sdílené zásady přístupu** > **RootManageSharedAccessKey**. Zkopírujte hodnotu **připojovací řetězec – primární klíč**.

      ![Zkopírujte hodnotu primárního klíče připojovací řetězec][6]

  1. Přejděte do [ (Nastavení)https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html) (Integrace a služby). Tato webová aplikace na adrese URL simuluje windmill zařízení.

  1. V **připojovací řetězec centra událostí** pole na webové stránce, vložte připojovací řetězec, který jste zkopírovali v předchozím kroku.

      ![Vložte primární klíč připojovacího řetězce v poli připojovací řetězec centra událostí][7]

  1. Vyberte **klikněte na tlačítko Spustit** pro odesílání událostí do vašeho centra událostí. Soubor s názvem *instances.json* se stáhne do vašeho počítače. Uložte tento soubor pro pozdější použití.

  1. Vraťte se do vašeho centra událostí na webu Azure Portal. V Centru událostí **přehled** stránce se zobrazí nové události, které jsou přijímány v Centru událostí.

     ![Stránka Přehled centra událostí, který zobrazuje metriky pro Centrum událostí][8]

## <a name="analyze-data-in-your-environment"></a>Analýza dat ve vašem prostředí

V této části provedete základní analýzy na váš čas řady dat pomocí služby Time Series Insights aktualizovat Průzkumníka.

  1. Kliknutím na adresu URL na stránce prostředků na webu Azure Portal přejděte na vaše aktualizace Průzkumníka Time Series Insights.

      ![Adresa URL Průzkumníka Time Series Insights][9]

  1. V Průzkumníku pod **fyzická hierarchie**, vyberte **bez nadřazených položek instance** uzlů, čímž zobrazíte všechny instance řady času v prostředí.

     ![Seznam instancí bez nadřazených položek v podokně fyzická hierarchie][10]

  1. V tomto kurzu budeme analyzovat data, která byla odeslána za poslední den. Vyberte **rychlé časy**a pak vyberte **posledních 24 hodin**.

     ![V rozevíracím seznamu Rychlé časy vyberte posledních 24 hodin][11]

  1. Vyberte **Sensor_0**a pak vyberte **zobrazit průměrné hodnoty** k vizualizaci dat odesílaných z této instance služby Time Series Insights.

     ![Vyberte Zobrazit průměrné hodnoty pro Sensor_0][12]

  1. Podobně můžete zobrazit data, která pochází z jiné instance služby Time Series Insights provádět základní analýzy.

     ![Vykreslení dat služby Time Series Insights][13]

## <a name="define-a-type-and-hierarchy"></a>Definujte typ a hierarchie 

V této části vytváření typu a hierarchie a pak přidružit typ a hierarchie s vaší instancí služby Time Series Insights. Další informace o [čas řady modely](./time-series-insights-update-tsm.md).

  1. V Průzkumníkovi, vyberte **modelu** kartu.

     ![Na kartě modelu v nabídce Průzkumníka][14]

  1. V **typy** vyberte **přidat** a vytvořte nový typ modelu časové řady.

     ![Tlačítko Přidat na stránku typy][15]

  1. V Editoru typů, zadejte hodnoty pro **název** a **popis**. Vytváření proměnných pro **průměrné**, **Min**, a **maximální** hodnoty, jak je znázorněno na následujících obrázcích. Vyberte **vytvořit** uložte typ.

     ![Přidat typ podokno a tlačítka pro vytvoření][16]

     ![Typy Windmill vzorku][17]

  1. V **hierarchie** vyberte **přidat** vytvořit novou hierarchii modelu časové řady.

     ![Tlačítko Přidat na stránku hierarchie][18]

  1. V editoru hierarchie, zadejte hodnotu pro **název** a přidejte úrovní hierarchie. Vyberte **vytvořit** uložit hierarchii.

     ![Přidat hierarchii podokno a tlačítka pro vytvoření][19]

     ![Do pole fyzická hierarchie][20]

  1. V **instance** , vyberte výchozí instanci a pak vyberte **upravit** pro přidružení k této instanci typu a hierarchie.

     ![Seznam instancí][21]

  1. V editoru instance vyberte typ a hierarchie, která jste definovali v kroku 3 a 5.

     ![Upravit instanci podokno][22]

  1. Můžete také vybrat typ a hierarchie v rámci všech instancí současně, můžete upravit *instances.json* soubor, který byl předtím stáhli. V tomto souboru Nahradit vše **typeId** a **hierarchyId** pole s ID, kterou jste získali v kroku 3 a 5.

  1. V **instance** vyberte **nahrát JSON** a nahrát upravený *instances.json* souboru.

     ![Tlačítko Nahrát JSON][23]

  1. Vyberte **Analytics** kartu a aktualizujte svůj prohlížeč. Zobrazit všechny instance přidružené k typu a hierarchie, která jste definovali.

     ![Vykreslení dat služby Time Series Insights][24]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:  

* Vytvoření prostředí čas Series Insights ve verzi Preview.
* Připojení prostředí čas Series Insights ve verzi Preview do centra událostí.
* Spuštění simulace větrné farmy pro streamování dat do prostředí čas Series Insights ve verzi Preview.
* Proveďte analýzu základní data.
* Definujte typ modelu časové řady a hierarchie a přidružit vaše instance.

Teď, když víte, jak vytvořit vlastní prostředí Time Series Insights aktualizace, přečtěte si informace o klíčových konceptech v Time Series Insights.

Přečtěte si informace o konfiguraci úložiště Time Series Insights:

> [!div class="nextstepaction"]
> [Azure storage čas Series Insights ve verzi Preview a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md)

Další informace o modelech řady čas:

> [!div class="nextstepaction"]
> [Modelování dat Azure čas Series Insights ve verzi Preview](./time-series-insights-update-tsm.md)

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