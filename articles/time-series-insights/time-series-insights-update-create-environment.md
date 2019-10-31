---
title: 'Kurz: nastavení prostředí pro náhled Azure Time Series Insights | Microsoft Docs'
description: Přečtěte si, jak nastavit prostředí v Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: 840280eecce60ac74514404497b857cd5362c9a0
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171649"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Kurz: nastavení prostředí pro Azure Time Series Insights Preview

Tento kurz vás provede procesem vytvoření náhledu prostředí s průběžnými platbami v Azure Time Series Insights Preview (PAYG). 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte prostředí Azure Time Series Insights Preview.
> * Připojte prostředí Azure Time Series Insights Preview k centru událostí ve službě Azure Event Hubs.
> * Spusťte ukázku akcelerátoru řešení pro streamování dat do prostředí Azure Time Series Insights Preview.
> * Proveďte základní analýzu dat.
> * Definujte typ modelu časové řady a hierarchii a přidružte je k vašim instancím.

>[!TIP]
> [Akcelerátory řešení IoT](https://www.azureiotsolutions.com/Accelerators) poskytují předkonfigurovaná řešení na podnikové úrovni, která můžete použít k urychlení vývoje vlastních řešení IoT.

Zaregistrujte si [bezplatné předplatné Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte.

## <a name="prerequisites"></a>Předpoklady

* Účet pro přihlášení do Azure musí být také členem role **vlastníka** předplatného. Další informace najdete v tématu [Správa přístupu pomocí řízení přístupu na základě role a Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

V této části vytvoříte tři simulovaná zařízení, která odesílají data do instance služby Azure IoT Hub.

1. Přejít na [stránku akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stránce se zobrazí několik předem sestavených příkladů. Přihlaste se pomocí svého účtu Azure. Pak vyberte **simulace zařízení**.

   [Stránka akcelerátorů řešení Azure IoT ![](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Vyberte **zkusit nyní**.

1. Na stránce **vytvoření řešení simulace zařízení** nastavte následující parametry:

    | Parametr | Akce |
    | --- | --- |
    | **Název nasazení** | Zadejte jedinečnou hodnotu pro novou skupinu prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků. |
    | **Předplatné Azure** | Vyberte předplatné, ve kterém budete vytvářet prostředí Time Series Insights. |
    | **Umístění Azure** | Vyberte oblast, kterou jste použili k vytvoření prostředí Time Series Insights. |
    | **Možnosti nasazení** | Vyberte možnost **zřídit nové IoT Hub**. |
 
    Vyberte **vytvořit řešení**. Může trvat až 20 minut, než se řešení dokončí nasazováním.

    [Stránka ![vytvoření řešení simulace zařízení](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Vytvoření verze Preview prostředí PAYG

Tato část popisuje, jak vytvořit prostředí Azure Time Series Insights Preview a připojit ho ke službě IoT Hub vytvořené pomocí akcelerátoru řešení IoT pomocí [Azure Portal](https://portal.azure.com/).

1. Přihlaste se k Azure Portal pomocí svého účtu předplatného.

1. Vyberte **vytvořit prostředek**  > **Internet věcí**  > **Time Series Insights**.

   [![vyberte Internet věcí a pak vyberte Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. V podokně **vytvořit Time Series Insights prostředí** na kartě **základy** nastavte následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název prostředí** | Zadejte jedinečný název prostředí Azure Time Series Insights Preview. |
    | **Předplatné** | Zadejte předplatné, ve kterém chcete vytvořit prostředí Azure Time Series Insights Preview. Osvědčeným postupem je použít stejné předplatné jako zbytek prostředků IoT vytvořených simulátorem zařízení. |
    | **Skupina prostředků** | Vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků pro prostředek prostředí Azure Time Series Insights Preview. Skupina prostředků představuje kontejner prostředků Azure. Osvědčeným postupem je použití stejné skupiny prostředků jako u jiných prostředků IoT, které jsou vytvořené simulátorem zařízení. |
    | **Umístění** | Vyberte oblast datového centra pro prostředí Azure Time Series Insights Preview. Abyste se vyhnuli další latenci, je nejlepší vytvořit prostředí Azure Time Series Insights Preview ve stejné oblasti jako vaše jiné prostředky IoT. |
    | **Vrstva** |  Vyberte **PAYG** (průběžné*platby*). Toto je SKU produktu Azure Time Series Insights Preview. |
    | **ID vlastnosti** | Zadejte hodnotu, která jedinečně identifikuje vaši instanci časové řady. Hodnota, kterou zadáte v poli **ID vlastnosti** , je neměnná a nelze ji později změnit. Pro tento kurz zadejte **iothub-Connection-Device-ID**. Další informace o ID časových řad najdete v tématu [osvědčené postupy pro výběr ID časové řady](./time-series-insights-update-how-to-id.md). |
    | **Název účtu úložiště** | Zadejte globálně jedinečný název pro nový účet úložiště pro chladírenské úložiště.|
    |**Replikace účtu úložiště**|Vyberte strategii replikace, která bude fungovat pro vaši firmu. Další informace o různých strategiích replikace najdete v tématu [redundance Azure Storage](../storage/common/storage-redundancy.md). |
    |**Povolit teplé úložiště**|Vytvořte volitelný záložní obchod. Dá se kdykoli odstranit. Záložní úložiště umožňuje rychlejší a neomezený počet dotazů přes nejaktuálnější data (až do 31 dnů)  |
    |**Uchovávání dat (ve dnech)**|Vyberte si uchovávání dat v teplém úložišti. Výchozí hodnota je 7 dní a je možné ji prodloužit až 31 dní. |
   
   Vyberte **Další: zdroj události**.

   [Podokno ![pro vytvoření prostředí Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na kartě **zdroj události** nastavte následující parametry:

   | Parametr | Akce |
   | --- | --- |
   | **Vytvořit zdroj události?** | Vyberte **Ano**.|
   | **Název** | Zadejte jedinečnou hodnotu názvu zdroje události. |
   | **Typ zdroje** | Vyberte **IoT Hub**. |
   | **Výběr centra** | Zvolte **Vybrat existující**. |
   | **Předplatné** | Vyberte předplatné, které jste použili pro simulátor zařízení. |
   | **Název IoT Hub** | Vyberte název služby IoT Hub, který jste vytvořili pro simulátor zařízení. |
   | **Zásada přístupu IoT Hub** | Vyberte **iothubowner**. |
   | **Skupina uživatelů IoT Hub** | Vyberte **Nový**, zadejte jedinečný název a pak vyberte **Přidat**. Skupina příjemců musí být ve verzi Azure Time Series Insights Preview jedinečná. |
   | **Vlastnost timestamp** | Tato hodnota se používá k identifikaci vlastnosti **časového razítka** ve příchozích datech telemetrie. V tomto kurzu ponechte toto pole prázdné. Simulátor používá příchozí časové razítko z IoT Hub, které Time Series Insights výchozím nastavením. |

   Vyberte **Zkontrolovat a vytvořit**.

   [![konfigurace zdroje události](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Na kartě **Revize + vytvořit** zkontrolujte výběry a pak vyberte **vytvořit**.

    [![stránka pro kontrolu a vytvoření stránky s tlačítkem vytvořit](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Můžete zobrazit stav nasazení:

    [![oznámení o dokončení nasazení](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Pokud vlastníte tenanta, máte přístup k prostředí Azure Time Series Insights ve verzi Preview. Ujistěte se, že máte přístup:

   1. Vyhledejte skupinu prostředků a potom vyberte prostředí Azure Time Series Insights Preview:

      [![vybrané prostředí](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na stránce Náhled Azure Time Series Insights vyberte **zásady přístupu k datům**:

      [![zásady přístupu k datům](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Ověřte, že jsou uvedené přihlašovací údaje:

      [![uvedené přihlašovací údaje](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Pokud vaše přihlašovací údaje nejsou uvedené, musíte sami udělit oprávnění k přístupu k prostředí. Pokud se chcete dozvědět víc o nastavení oprávnění, přečtěte si téma [udělení přístupu k datům](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Streamování dat

Teď, když jste nasadili Time Series Insights prostředí, streamujte do něj data pro účely analýzy.

1. Přejděte zpátky na [stránku akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Vyhledejte řešení na řídicím panelu akcelerátoru řešení. Pak vyberte **Spustit**:

    [![spustit řešení pro simulaci zařízení](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Budete přesměrováni na stránku **Microsoft Azure pro simulaci zařízení IoT** . V pravém horním rohu stránky vyberte **Nová simulace**.

    [Stránka simulace ![Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. V podokně **Nastavení simulace** nastavte následující parametry:

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte jedinečný název simulátoru. |
    | **Popis** | Zadejte definici. |
    | **Simulation duration** („Doba trvání simulace“) | Nastaveno na **neomezenou dobu**. |
    | **Device model** („Model zařízení“) | **Název**: zadejte **chladicíer**. <br />**Částka**: zadejte **3**. |
    | **Target IoT Hub** („Cílový IoT Hub“) | Nastavte na **použít předem zřízené IoT Hub**. |

    [![parametrů, které se mají nastavit](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Vyberte **spustit simulaci**.

    Na řídicím panelu pro simulaci zařízení si všimněte informací zobrazených pro **aktivní zařízení** a **zprávy za sekundu**.

    [řídicí panel simulace Azure IoT ![](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analyzujte data

V této části provedete základní analýzy dat časových řad pomocí [Průzkumníka služby Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

1. Pokud chcete přejít do Průzkumníka služby Azure Time Series Insights Preview, vyberte adresu URL ze stránky prostředků v [Azure Portal](https://portal.azure.com/).

    [![URL aplikace Time Series Insights Preview](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. V Průzkumníkovi vyberte uzel **instance časové řady** , aby se zobrazily všechny instance Azure Time Series Insights Preview v prostředí.

    [![seznam nenadřazených instancí](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Vyberte instanci první časové řady. Pak vyberte **Zobrazit tlak**.

    [![vybrala instance časové řady s příkazem nabídky k zobrazení průměrného tlaku.](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Zobrazí se graf časové řady. Změňte **interval** na **15s**.

    [graf časové řady ![](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Opakujte krok 3 s ostatními dvěma instancemi časových řad. Můžete zobrazit všechny instance časových řad, jak je znázorněno v tomto grafu:

    [![grafu pro všechny časové řady](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. V poli **časový rámec** změňte časový rozsah, abyste viděli trendy časových řad za poslední hodinu:

    [![nastavit časový rozsah na hodinu](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definování a použití modelu

V této části použijete model ke strukturování dat. Pro dokončení modelu definujete typy, hierarchie a instance. Další informace o modelování dat najdete v tématu [model časové řady](./time-series-insights-update-tsm.md).

1. V Průzkumníku vyberte kartu **model** :

   [karta model ![v Průzkumníkovi](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Vyberte **Přidat** a přidejte typ:

   [![tlačítko Přidat pro typy](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Dále definujete tři proměnné pro typ: *tlak*, *teplota*a *vlhkost*. V podokně **Přidat typ** nastavte následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název** | Zadejte **chladicíer**. |
    | **Popis** | Zadejte **Toto je definice typu chladicího programu**. |

   * Chcete-li definovat *tlak*, v části **proměnné**nastavte následující parametry:

     | Parametr | Akce |
     | --- | ---|
     | **Název** | Zadejte **průměrný tlak**. |
     | **Hodnota** | Vyberte možnost **přítlak (Double)** . Automatické naplnění **hodnoty** po Azure Time Series Insights ve verzi Preview může trvat několik minut, než začnou přijímat události. |
     | **Agregační operace** | Vyberte **prům**. |

      [Výběry ![pro definování tlaku](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Chcete-li přidat další proměnnou, vyberte možnost **přidat proměnnou**.

   * Definovat *teplotu*:

     | Parametr | Akce |
     | --- | ---|
     | **Název** | Zadejte **průměrnou teplotu**. |
     | **Hodnota** | Vyberte možnost **teplota (Double)** . Automatické naplnění **hodnoty** po Azure Time Series Insights ve verzi Preview může trvat několik minut, než začnou přijímat události. |
     | **Agregační operace** | Vyberte **prům**.|

      [Výběry ![pro definování teploty](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Chcete-li přidat další proměnnou, vyberte možnost **přidat proměnnou**.

   * Definovat *vlhkost*:

      | | |
      | --- | ---|
      | **Název** | Zadejte **maximální vlhkost** . |
      | **Hodnota** | Vyberte **vlhkost (dvojitá přesnost)** . Automatické naplnění **hodnoty** po Azure Time Series Insights ve verzi Preview může trvat několik minut, než začnou přijímat události. |
      | **Agregační operace** | Vyberte **Max**.|

      [Výběry ![pro definování teploty](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Vyberte **Create** (Vytvořit).

    Můžete si prohlédnout typ, který jste přidali:

    [![informace o přidaném typu](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Dalším krokem je přidání hierarchie. V části **hierarchie**vyberte **Přidat**:

    [karta ![hierarchie s tlačítkem Přidat](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. V podokně **Upravit hierarchii** nastavte následující parametry:

   | Parametr | Akce |
   | --- | ---|
   | **Název** | Zadejte **hierarchii umístění**. |
   | **Úroveň 1** | Zadejte **Country (země**). |
   | **Úroveň 2** | Zadejte **City**. |
   | **Úroveň 3** | Zadejte **sestavování**. |

   Vyberte **Save** (Uložit).

    [pole ![hierarchie s tlačítkem vytvořit](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Můžete zobrazit hierarchii, kterou jste vytvořili:

    [![informace o hierarchii](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Vyberte **instance**. Vyberte první instanci a pak vyberte **Upravit**:

    [![výběru tlačítka pro úpravy instance](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. V podokně **upravit instance** nastavte následující parametry:

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **chladicíer**. |
    | **Popis** | Zadejte **instanci pro chladicího a 01,1**. |
    | **Hierarchie** | Vyberte možnost **Hierarchie umístění**. |
    | **Krajin** | Zadejte **USA**. |
    | **Vatikán** | Zadejte **Seattle**. |
    | **Kapacit** | Zadejte **ručičku místa**. |

    [![polí instance pomocí tlačítka Uložit](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Vyberte **Save** (Uložit).

1. Předchozí krok opakujte pro ostatní senzory. Aktualizujte následující hodnoty:

   * Pro chlazení 01,2:

     | Parametr | Akce |
     | --- | --- |
     | **Typ** | Vyberte **chladicíer**. |
     | **Popis** | Zadejte **instanci pro chladicího a 01,2**. |
     | **Hierarchie** | Vyberte možnost **Hierarchie umístění**. |
     | **Krajin** | Zadejte **USA**. |
     | **Vatikán** | Zadejte **Seattle**. |
     | **Kapacit** | Zadejte **centrum tichomořské vědy**. |

   * Pro chlazení 01,3:

     | Parametr | Akce |
     | --- | --- |
     | **Typ** | Vyberte **chladicíer**. |
     | **Popis** | Zadejte **instanci pro chladicího a 01,3**. |
     | **Hierarchie** | Vyberte možnost **Hierarchie umístění**. |
     | **Krajin** | Zadejte **USA**. |
     | **Vatikán** | Zadejte **New York**. |
     | **Kapacit** | Jako stav zadejte **Empire**. |

1. Vyberte kartu **analyzovat** a aktualizujte stránku. V části **Hierarchie umístění**rozbalte všechny úrovně hierarchie, aby se zobrazily instance časových řad:

   [![kartě analyzovat](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Chcete-li prozkoumat instance časových řad za poslední hodinu, změňte **rychlé časy** na **Poslední hodinu**:

    [![pole rychlé časy s vybranou poslední hodinou.](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. V **centru pro Tichomoří – vědu**vyberte instanci časové řady a pak vyberte **Zobrazit maximální vlhkost**.

    [![vybrané instance časové řady a výběr nabídky Zobrazit maximální vlhkost](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Otevře se časová řada pro **maximální vlhkost** s velikostí intervalu **1 minuta** . Chcete-li filtrovat rozsah, vyberte oblast. Chcete-li analyzovat události v časovém rámci, klikněte pravým tlačítkem na graf a vyberte možnost **Zvětšit**:

   [![vybraný rozsah s příkazem Lupa v místní nabídce](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Pokud chcete zobrazit podrobnosti události, vyberte oblast a potom klikněte pravým tlačítkem myši na graf:

   [![podrobný seznam událostí](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili kurz, vyčistěte prostředky, které jste vytvořili:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**a vyhledejte svoji Azure Time Series Insights skupinu prostředků.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) tak, že vyberete možnost **Odstranit** nebo odebrat jednotlivé prostředky zvlášť.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:  

> [!div class="checklist"]
> * Vytvořte a použijte akcelerátor simulace zařízení.
> * Vytvořte PAYG prostředí Azure Time Series Insights ve verzi Preview.
> * Připojte prostředí Azure Time Series Insights Preview ke službě IoT Hub.
> * Spusťte ukázku akcelerátoru řešení pro streamování dat do prostředí Azure Time Series Insights Preview.
> * Provede základní analýzu dat.
> * Definujte typ modelu časové řady a hierarchii a přidružte je k vašim instancím.

Teď, když víte, jak vytvořit vlastní prostředí pro Azure Time Series Insights Preview, získáte další informace o klíčových konceptech v Azure Time Series Insights.

Přečtěte si o konfiguraci úložiště Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Azure Time Series Insights a příchozí úložiště ve verzi Preview](./time-series-insights-update-storage-ingress.md)

Další informace o modelech časových řad:

> [!div class="nextstepaction"]
> [Modelování dat ve verzi Preview Azure Time Series Insights](./time-series-insights-update-tsm.md)
