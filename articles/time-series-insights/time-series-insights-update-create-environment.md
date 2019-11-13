---
title: 'Kurz: nastavení prostředí verze Preview – Azure Time Series Insights | Microsoft Docs'
description: Přečtěte si, jak nastavit prostředí pro Azure Time Series Insights ve verzi Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: 2dd08af518be9b3fec84e756edf9532ef669f22f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012663"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Kurz: Nastavení prostředí Azure čas Series Insights ve verzi Preview

Tento kurz vás provede procesem vytvoření náhledu prostředí s průběžnými platbami v Azure Time Series Insights Preview (PAYG).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte prostředí Azure Time Series Insights Preview.
> * Připojte prostředí Azure Time Series Insights Preview k IoT Hub.
> * Spusťte ukázku akcelerátoru řešení pro streamování dat do prostředí Azure Time Series Insights Preview.
> * Proveďte základní analýzy na data.
> * Definujte typ modelu časové řady a hierarchii a přidružte je k vašim instancím.
> * Použijte konektor Power BI a Vizualizujte data v Power BI.

>[!TIP]
> [Akcelerátory řešení IoT](https://www.azureiotsolutions.com/Accelerators) poskytují předkonfigurovaná řešení na podnikové úrovni, která můžete použít k urychlení vývoje vlastních řešení IoT.

Zaregistrujte si [bezplatné předplatné Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte.

## <a name="prerequisites"></a>Požadavky

* Minimálně musíte mít roli **přispěvatele** pro předplatné Azure. Další informace najdete v tématu [Správa přístupu pomocí řízení přístupu na základě role a Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

V této části vytvoříte tři simulovaná zařízení, která odesílají data do instance služby Azure IoT Hub.

1. Přejít na [stránku akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stránce se zobrazí několik předem sestavených příkladů. Přihlaste se pomocí svého účtu Azure. Pak vyberte **simulace zařízení**.

   [Stránka akcelerátorů řešení Azure IoT ![](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. Na další stránce vyberte **vyzkoušet nyní**.

   [Stránka simulace zařízení ![](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. Na stránce **vytvoření řešení simulace zařízení** nastavte následující parametry:

    | Parametr | Akce |
    | --- | --- |
    | **Název nasazení** | Zadejte jedinečnou hodnotu pro novou skupinu prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků. |
    | **Předplatné Azure** | Vyberte předplatné, ve kterém budete vytvářet prostředí Time Series Insights. |
    | **Umístění Azure** | Vyberte oblast, do které chcete uložit Time Series Insights prostředí. Všimněte si, že simulátor zařízení se nabízí jenom v omezeném počtu oblastí, takže Pokud nevidíte požadovanou oblast, můžete vybrat umístění výhradně pro tento kurz a pak vytvořit nové prostředí TSI, až budete připraveni přejít k další fázi na-b. oarding.  |
    | **Možnosti nasazení** | Vyberte možnost **zřídit nové IoT Hub**. |

    1. Vyberte **Vytvořit**.
    [Stránka simulace zařízení ![](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Po přibližně 20 minutách bude akcelerátor řešení připravený.

    [Stránka simulace zařízení ![](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Vytvoření verze Preview prostředí PAYG

Tato část popisuje, jak vytvořit prostředí Azure Time Series Insights Preview a připojit ho ke službě IoT Hub vytvořené pomocí akcelerátoru řešení IoT pomocí [Azure Portal](https://portal.azure.com/).

1. Přihlaste se k webu Azure portal pomocí svého účtu předplatného.

1. Vyberte **vytvořit prostředek** > **Internet věcí** > **Time Series Insights**.

   [![vyberte Internet věcí a pak vyberte Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. V podokně **vytvořit Time Series Insights prostředí** na kartě **základy** nastavte následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název prostředí** | Zadejte jedinečný název prostředí Azure Time Series Insights Preview. |
    | **Předplatné** | Zadejte předplatné, ve kterém chcete vytvořit prostředí Azure Time Series Insights Preview. Osvědčeným postupem je použít stejné předplatné jako zbytek prostředků IoT vytvořených simulátorem zařízení. |
    | **Skupina prostředků** | Vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků pro prostředek prostředí Azure Time Series Insights Preview. Skupina prostředků představuje kontejner prostředků Azure. Osvědčeným postupem je použití stejné skupiny prostředků jako u jiných prostředků IoT, které jsou vytvořené simulátorem zařízení. |
    | **Umístění** | Vyberte oblast datového centra pro prostředí Azure Time Series Insights Preview. Abyste se vyhnuli další latenci, je nejlepší vytvořit prostředí Azure Time Series Insights Preview ve stejné oblasti, ve které je služba IoT Hub vytvořená simulátorem zařízení. |
    | **Vrstva** |  Vyberte **PAYG** (průběžné*platby*). Toto je SKU produktu Azure Time Series Insights Preview. |
    | **ID vlastnosti** | Zadejte hodnotu, která jedinečně identifikuje vaši instanci časové řady. Hodnotu, kterou zadáte v poli **ID vlastnosti** , nelze později změnit. Pro tento kurz zadejte **iothub-Connection-Device-ID**. Další informace o ID časových řad najdete v tématu [osvědčené postupy pro výběr ID časové řady](./time-series-insights-update-how-to-id.md). |
    | **Název účtu úložiště** | Zadejte globálně jedinečný název nového účtu úložiště.|
    |**Povolit teplé úložiště**|Vyberte **Ano** , pokud chcete povolit teplé úložiště.|
    |**Uchovávání dat (ve dnech)**|Vyberte výchozí možnost 7 dní. |

    Vyberte **Další: zdroj události**.

   [![podokno pro vytvoření prostředí Time Series Insights](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [podokno![pro vytvoření prostředí Time Series Insights](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

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

1. Vyberte **Vytvořit**.

    [![stránka pro kontrolu a vytvoření stránky s tlačítkem vytvořit](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Můžete zobrazit stav nasazení:

    [![oznámení o dokončení nasazení](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Pokud jste vlastníkem předplatného Azure, máte ve výchozím nastavení přístup k prostředí Azure Time Series Insights Preview. Ověřte, že máte přístup:

   1. Vyhledejte skupinu prostředků a potom vyberte své nově vytvořené prostředí Azure Time Series Insights Preview. 
      [![vybrané prostředí](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na stránce Náhled Azure Time Series Insights vyberte **zásady přístupu k datům**: [![zásady přístupu k datům](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox) .

   1. Ověřte, že jsou uvedené přihlašovací údaje:

      [![uvedené přihlašovací údaje](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Pokud vaše přihlašovací údaje nejsou uvedené, musíte sami udělit oprávnění k přístupu k prostředí, a to tak, že vyberete Přidat a vyhledat svoje přihlašovací údaje. Pokud se chcete dozvědět víc o nastavení oprávnění, přečtěte si téma [udělení přístupu k datům](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Streamování dat

Teď, když jste nasadili Time Series Insights prostředí, začněte streamovat data k analýze.

1. Přejděte zpátky na [stránku akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Vyhledejte řešení na řídicím panelu akcelerátoru řešení a pak vyberte **Spustit**:

    [![spustit řešení pro simulaci zařízení](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Vyberte **Přejít do akcelerátoru řešení**.

    [![spustit řešení pro simulaci zařízení](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. Budete přesměrováni na stránku **Microsoft Azure pro simulaci zařízení IoT** . V pravém horním rohu stránky vyberte **Nová simulace**.

    [Stránka simulace ![Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. V podokně **Nastavení simulace** nastavte následující parametry:

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte jedinečný název simulátoru. |
    | **Popis** | Zadejte definici. |
    | **Simulation duration** („Doba trvání simulace“) | Nastaveno na **neomezenou dobu**. |
    | **Device model** („Model zařízení“) | Klikněte na + **Přidat typ zařízení** . <br />**Název**: zadejte **výtah**. <br />**Částka**: zadejte **3**. <br /> Zbývající výchozí hodnoty ponechte. |
    | **Target IoT Hub** („Cílový IoT Hub“) | Nastavte na **použít předem zřízené IoT Hub**. |

    [![parametrů, které se mají nastavit](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Vyberte **spustit simulaci**.

    V řídicím panelu pro simulaci zařízení se zobrazí **aktivní zařízení** a **Celkový počet zpráv**.

    [řídicí panel simulace Azure IoT ![](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analýza dat

V této části provedete základní analýzy dat časových řad pomocí [Průzkumníka služby Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

1. Pokud chcete přejít do Průzkumníka služby Azure Time Series Insights Preview, vyberte adresu URL ze stránky prostředků v [Azure Portal](https://portal.azure.com/).

    [![URL aplikace Time Series Insights Preview](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. V Průzkumníku Time Series Insights se zobrazí pruh v horní části obrazovky. Toto je váš výběr vaší dostupnosti. Ujistěte se, že jste vybrali alespoň dvě 2 min, a v případě potřeby rozbalte časový rámec tím, že vyberete a přetáhnete úchyty pro výběr doleva a doprava.

1. Na levé straně budete moct vidět **instance časových řad** .


    [![seznam nenadřazených instancí](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Vyberte instanci první časové řady. Pak vyberte **Zobrazit tlak**.

    [![vybrala instance časové řady s příkazem nabídky k zobrazení průměrného tlaku.](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Zobrazí se graf časové řady. Změňte **interval** na **30 s**.

    [graf časové řady ![](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Opakujte krok 3 s ostatními dvěma instancemi časových řad, abyste zobrazili všechny tři, jak je znázorněno v tomto grafu:

    [![grafu pro všechny časové řady](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. V pravém horním rohu vyberte časový rozsah pro výběr. Tady můžete vybrat konkrétní počáteční a koncové časy do milisekundy nebo zvolit z předem nakonfigurovaných možností, jako je například poslední hodina. Můžete také změnit výchozí časové pásmo.

    [![nastavit časový rozsah na hodinu](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Níže je snímek obrazovky podokna grafů po spuštění simulace po určitou hodinu:

    [podokno ![grafů](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definování a použití modelu

V této části použijete model ke strukturování dat. Pro dokončení modelu definujete typy, hierarchie a instance. Další informace o modelování dat najdete v tématu [model časové řady](./time-series-insights-update-tsm.md).

1. V Průzkumníku vyberte kartu **model** :

   [karta model ![v Průzkumníkovi](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Na kartě **typy** vyberte **Přidat**.

   [![tlačítko Přidat pro typy](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Zadejte následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název** | Zadat **výtah** |
    | **Popis** | Zadejte **Toto je definice typu pro výtah** . |

    [![tlačítko Přidat pro typy](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Potom vyberte kartu **proměnné** . [![kartu vybrat proměnné](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. Vyberte **+ přidat proměnnou** a vyplňte následující hodnoty pro první proměnnou typu výtahu. Budete vytvářet tři proměnné celkem.

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte **průměrnou teplotu**. |
    | **Plnění** | Vybrat **číselné** |
    | **Hodnota** | Vyberte možnost z předvolby: vyberte **teplotu (Double)** . <br /> Poznámka: když Azure Time Series Insights Preview začne přijímat události, může trvat několik minut, než se **hodnota** automaticky naplní.|
    | **Agregační operace** | Rozbalte položku **Pokročilá nastavení**. <br /> Vyberte **prům**. |

    [Výběry ![pro definování teploty](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    Vyberte **Použít**.

    Znovu vyberte **+ přidat proměnnou** a nastavte následující hodnoty:

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte **průměrné vibrace**. |
    | **Plnění** | Vybrat **číselné** |
    | **Hodnota** | Vyberte možnost z předvolby: vyberte **vibrace (dvojitá)** . <br /> Poznámka: když Azure Time Series Insights Preview začne přijímat události, může trvat několik minut, než se **hodnota** automaticky naplní.|
    | **Agregační operace** | Rozbalte položku **Pokročilá nastavení**. <br /> Vyberte **prům**. |

    [Výběry ![pro definování vibrací](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    Vyberte **Použít**.

    Znovu vyberte **+ přidat proměnnou** a nastavte následující hodnoty pro třetí a poslední proměnnou:

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte **podlahovou základnu**. |
    | **Plnění** | Vybrat **kategorií** |
    | **Hodnota** | Vyberte možnost z předvolby: vyberte **podlahová (dvojitá)** . <br /> Poznámka: když Azure Time Series Insights Preview začne přijímat události, může trvat několik minut, než se **hodnota** automaticky naplní.|
    | **Kategorie** |   - <span style="text-decoration: underline">hodnoty</span> <span style="text-decoration: underline">popisku</span> <br /> Nižší: 1, 2, 3, 4 <br /> Uprostřed: 5, 6, 7, 8, 9, 9 <br /> Horní: 10, 11, 12, 13, 14, 15 |
    | **Výchozí kategorie** | Zadejte **Neznámý** |

    [Výběry ![pro definování vibrací](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    Vyberte **Použít**. Měli byste vidět tři proměnné, které byly vytvořeny:

    [Výběry ![pro definování vibrací](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   Vyberte **Uložit**. Zobrazí se **typ** vytvořený:

    [Výběry ![pro definování vibrací](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. Vyberte kartu **hierarchie** . Vyberte **+ Přidat**.

    [karta ![hierarchie s tlačítkem Přidat](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. V podokně **Upravit hierarchii** nastavte následující parametry:

   | Parametr | Akce |
   | --- | ---|
   | **Název** | Zadejte **hierarchii umístění**. |
   |**Hladin**| Jako název první úrovně zadejte **Country (země** ). <br> Vybrat **+ Přidat úroveň** <br> Zadejte **město** druhé úrovně a pak vyberte **+ Přidat úroveň** . <br> Jako název třetí a poslední úrovně zadejte **sestavování** . |

   Vyberte **Uložit**.

    [pole ![hierarchie s tlačítkem vytvořit](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Můžete zobrazit hierarchii, kterou jste vytvořili:

    [![informace o hierarchii](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Přejděte na **instance**. V části **Akce** úplně vpravo vyberte ikonu tužky a upravte první instanci s následujícími hodnotami:

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **výtah**. |
    | **Název** | Zadejte **výtah 1** .|
    | **Popis** | Zadejte **instanci pro výtah 1** . |

    [![výběru tlačítka pro úpravy instance](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    Přejděte do **polí instance** a zadejte následující:

    | Parametr | Akce |
    | --- | --- |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Krajin** | Zadejte **USA** . |
    | **Vatikán** | Zadejte **Seattle** |
    | **Kapacit** | Zadat **ručičku místa** |

    [![výběru tlačítka pro úpravy instance](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    Vyberte **Uložit**.

1. Opakujte krok 8 s ostatními dvěma instancemi s následujícími hodnotami:

    <span style="text-decoration: underline;">Pro výtah 2</span>:

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **výtah**. |
    | **Název** | Zadejte **výtah 2** .|
    | **Popis** | Zadejte **instanci pro výtah 2** . |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Krajin** | Zadejte **USA** . |
    | **Vatikán** | Zadejte **Seattle** |
    | **Kapacit** | Zadejte **střed pro Tichomoří – věda** |

    <span style="text-decoration: underline;">Pro výtah 3</span>:

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **výtah**. |
    | **Název** | Zadejte **výtah 3**|
    | **Popis** | Zadejte **instanci pro výtah 3** . |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Krajin** | Zadejte **USA** . |
    | **Vatikán** | Zadejte **New York** |
    | **Kapacit** | Zadejte **sestavení stavu Empire** |

1. Přejděte zpět na kartu **analyzovat** a zobrazte podokno grafů. V části **Hierarchie umístění**rozbalte všechny úrovně hierarchie, aby se zobrazily instance časových řad:

   [![kartě analyzovat](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. V oblasti **Tichomoří – střed**vyberte instance časové řady **2**a pak vyberte **Zobrazit průměrnou teplotu**.

    [![instance časové řady zobrazit průměrnou teplotu](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. U stejné instance, **výtah 2**, vyberte **Zobrazit podlahovou základnu**.

    [Instance ![časových řad – zobrazit podlahu](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    Pomocí proměnné kategorií můžete určit, kolik času vychází z výtahu na horní, dolní a prostřední podlahovou plochu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili kurz, vyčistěte prostředky, které jste vytvořili:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**a vyhledejte svoji Azure Time Series Insights skupinu prostředků.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) tak, že vyberete možnost **Odstranit** nebo odebrat jednotlivé prostředky zvlášť.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:  

* Vytvořte a použijte akcelerátor simulace zařízení.
* Vytvořte PAYG prostředí Azure Time Series Insights ve verzi Preview.
* Připojte prostředí Azure Time Series Insights Preview ke službě IoT Hub.
* Spusťte ukázku akcelerátoru řešení pro streamování dat do prostředí Azure Time Series Insights Preview.
* Proveďte analýzu základní data.
* Definujte typ modelu časové řady a hierarchii a přidružte je k vašim instancím.

Teď, když víte, jak vytvořit vlastní prostředí pro Azure Time Series Insights Preview, získáte další informace o klíčových konceptech v Azure Time Series Insights.

Přečtěte si o konfiguraci úložiště Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Azure storage čas Series Insights ve verzi Preview a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md)

Další informace o modelech řady čas:

> [!div class="nextstepaction"]
> [Modelování dat Azure čas Series Insights ve verzi Preview](./time-series-insights-update-tsm.md)
