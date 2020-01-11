---
title: 'Kurz: nastavení prostředí verze Preview – Azure Time Series Insights | Microsoft Docs'
description: 'Kurz: Naučte se, jak nastavit prostředí v Azure Time Series Insights Preview.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/26/2019
ms.custom: seodec18
ms.openlocfilehash: bebc7dde30dad57157d0abee7f2294d9da58fd5c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861808"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Kurz: Nastavení prostředí Azure čas Series Insights ve verzi Preview

Tento kurz vás provede procesem vytvoření náhledu prostředí s průběžnými *platbami* v Azure Time Series Insights Preview (PAYG).

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

* Minimálně musíte mít roli **přispěvatele** pro předplatné Azure. Další informace najdete v článku [Správa přístupu pomocí řízení přístupu na základě role a Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

V této části vytvoříte tři simulovaná zařízení, která odesílají data do instance služby Azure IoT Hub.

1. Přejít na [stránku akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stránce se zobrazí několik předem sestavených příkladů. Přihlaste se pomocí svého účtu Azure. Pak vyberte **simulace zařízení**.

   [![stránce akcelerátory řešení Azure IoT.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Na další stránce vyberte **vyzkoušet nyní**. Pak na stránce **řešení pro vytvoření simulace zařízení** zadejte požadované parametry.

   Parametr|Popis
   ---|---
   **Název nasazení** | Tato jedinečná hodnota se používá k vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků.
   **Předplatné Azure** | Zadejte stejné předplatné, které bylo použito k vytvoření prostředí Time Series Insights v předchozí části.
   **Možnosti nasazení** | Pokud chcete vytvořit nový IoT Hub specifický pro tento kurz, vyberte **zřídit nový IoT Hub** .
   **Umístění Azure** | Zadejte stejnou oblast, která byla použita k vytvoření prostředí Time Series Insights v předchozí části.

   Až budete hotovi, vyberte **vytvořit** a zřiďte prostředky Azure v řešení. Dokončení tohoto procesu může trvat až 20 minut.

   [![zřízení řešení pro simulaci zařízení.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Po dokončení zřizování se zobrazí dvě oznámení oznamující, že stav nasazení se přesunul ze **zřizování** na **připraveno**. 

   >[!IMPORTANT]
   > Ještě nezadávejte akcelerátor řešení. Tuto webovou stránku nechte otevřenou, protože se k ní vrátíte později.

   [![se zřizování řešení pro simulaci zařízení dokončila.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Nyní zkontrolujte nově vytvořené prostředky v Azure Portal. Na stránce **skupiny prostředků** si všimněte, že se vytvořila nová skupina prostředků pomocí **názvu řešení** , který jste zadali v posledním kroku. Poznamenejte si prostředky, které byly vytvořeny pro simulaci zařízení.

   [![prostředky simulace zařízení.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Vytvoření verze Preview prostředí PAYG

Tato část popisuje, jak vytvořit prostředí Azure Time Series Insights Preview a připojit ho ke službě IoT Hub vytvořené pomocí akcelerátoru řešení IoT pomocí [Azure Portal](https://portal.azure.com/).

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu předplatného Azure. 
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**. 
1. Vyberte **Internet of Things** kategorie a pak vyberte **Time Series Insights**. 

   [![vyberte prostředek prostředí Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. V podokně **vytvořit Time Series Insights prostředí** na kartě **základy** nastavte následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název prostředí** | Zadejte jedinečný název prostředí Azure Time Series Insights Preview. |
    | **Předplatné** | Zadejte předplatné, ve kterém chcete vytvořit prostředí Azure Time Series Insights Preview. Osvědčeným postupem je použít stejné předplatné jako zbytek prostředků IoT vytvořených simulátorem zařízení. |
    | **Skupina prostředků** | Vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků pro prostředek prostředí Azure Time Series Insights Preview. Skupina prostředků představuje kontejner prostředků Azure. Osvědčeným postupem je použití stejné skupiny prostředků jako u jiných prostředků IoT, které jsou vytvořené simulátorem zařízení. |
    | **Umístění** | Vyberte oblast datového centra pro prostředí Azure Time Series Insights Preview. Abyste se vyhnuli další latenci, je nejlepší vytvořit prostředí Azure Time Series Insights Preview ve stejné oblasti, ve které je služba IoT Hub vytvořená simulátorem zařízení. |
    | **Vrstva** |  Vyberte **PAYG** (průběžné*platby*). Toto je SKU produktu Azure Time Series Insights Preview. |
    | **Název vlastnosti** | Zadejte hodnotu, která jedinečně identifikuje vaši instanci časové řady. Hodnotu, kterou zadáte v poli **ID vlastnosti** , nelze později změnit. Pro tento kurz zadejte ***iothub-Connection-Device-ID***. Pokud se chcete dozvědět víc o ID časových řad, přečtěte si [osvědčené postupy pro výběr ID časové řady](./time-series-insights-update-how-to-id.md). |
    | **Název účtu úložiště** | Zadejte globálně jedinečný název nového účtu úložiště.|
    |**Povolit teplé úložiště**|Vyberte **Ano** , pokud chcete povolit teplé úložiště. Později se můžete vrátit a povolit toto nastavení. |
    |**Uchovávání dat (ve dnech)**|Vyberte výchozí možnost 7 dní. |

    Vyberte **Další: zdroj události**.

   [![novou konfiguraci Time Series Insights prostředí.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![nakonfigurovat ID časových řad pro prostředí.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

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
   | **Skupina uživatelů IoT Hub** | Vyberte **Nový**, zadejte jedinečný název a potom vyberte **+ Přidat**. Skupina příjemců musí být ve verzi Azure Time Series Insights Preview jedinečná. |
   | **Vlastnost timestamp** | Tato hodnota se používá k identifikaci vlastnosti **časového razítka** ve příchozích datech telemetrie. V tomto kurzu ponechte toto pole prázdné. Simulátor používá příchozí časové razítko z IoT Hub, které Time Series Insights výchozím nastavením. |

   Vyberte **zkontrolovat + vytvořit**.

   [![nakonfigurovat vytvořené centrum IoT jako zdroj události.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Vyberte **Vytvořit**.

    [pomocí tlačítka vytvořit ![stránku revize a vytvořit.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Můžete zkontrolovat stav nasazení:

    [![oznámení, že nasazení bylo dokončeno.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Pokud jste vlastníkem předplatného Azure, máte ve výchozím nastavení přístup k prostředí Azure Time Series Insights Preview. Ověřte, že máte přístup:

   1. Vyhledejte skupinu prostředků a potom vyberte své nově vytvořené prostředí Azure Time Series Insights Preview. 

      [![výběr a zobrazení prostředí.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Na stránce Náhled Azure Time Series Insights vyberte **zásady přístupu k datům**:

      [![ověřit zásady přístupu k datům.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Ověřte, že jsou uvedené přihlašovací údaje:

      Pokud vaše přihlašovací údaje nejsou uvedené, musíte sami udělit oprávnění k přístupu k prostředí, a to tak, že vyberete Přidat a vyhledat svoje přihlašovací údaje. Pokud se chcete dozvědět víc o nastavení oprávnění, přečtěte si téma [udělení přístupu k datům](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Streamování dat

Teď, když jste nasadili Time Series Insights prostředí, začněte streamovat data k analýze.

1. Vraťte se zpět na [Řídicí panel akcelerátorů řešení](https://www.azureiotsolutions.com/Accelerators#dashboard). V případě potřeby se znovu přihlaste pomocí stejného účtu Azure, který jste používali v tomto kurzu. Vyberte své řešení zařízení a potom v **rámci akcelerátoru řešení** spusťte nasazené řešení.

   [řídicí panel pro ![akcelerátory řešení.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Webová aplikace simulace zařízení začíná výzvou k udělení webové aplikace tak, aby **vás přihlásila a četla oprávnění k vašemu profilu** . Toto oprávnění umožňuje aplikaci načíst informace o profilu uživatele, které jsou nezbytné k podpoře fungování aplikace.

   [![souhlasu webové aplikace o simulaci zařízení.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Vyberte **+ Nová simulace**. Po načtení stránky **Nastavení simulace** zadejte požadované parametry.

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte jedinečný název simulátoru. |
    | **Popis** | Zadejte definici. |
    | **Simulation duration** („Doba trvání simulace“) | Nastaveno na **neomezenou dobu**. |
    | **Device model** („Model zařízení“) | Klikněte na + **Přidat typ zařízení** . <br />**Název**: zadejte **výtah**. <br />**Částka**: zadejte **3**. <br /> Zbývající výchozí hodnoty ponechte. |
    | **Target IoT Hub** („Cílový IoT Hub“) | Nastavte na **použít předem zřízené IoT Hub**. |

    [![nakonfigurovat parametry a spustit.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Vyberte **spustit simulaci**.

    V řídicím panelu pro simulaci zařízení se zobrazí **aktivní zařízení** a **Celkový počet zpráv** .

    [![řídicí panel simulace Azure IoT.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analyzujte data

V této části provedete základní analýzy dat časových řad pomocí [Průzkumníka služby Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

1. Pokud chcete přejít do Průzkumníka služby Azure Time Series Insights Preview, vyberte adresu URL ze stránky prostředků v [Azure Portal](https://portal.azure.com/).

    [![URL aplikace Time Series Insights Preview.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. V Průzkumníkovi Time Series Insights se zobrazí pruh rozložený na horní části obrazovky. Toto je váš výběr vaší dostupnosti. Ujistěte se, že máte vybrané aspoň dvě 2 m, a v případě potřeby rozbalte časový rámec výběrem a přetažením úchytů pro výběr vlevo a vpravo.

1. **Instance časových řad** se zobrazí na levé straně.

    [![seznam nenadřazených instancí.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Vyberte instanci instance řady First-Time. Pak vyberte **Zobrazit teplotu**.

    [![vybrala instance časové řady s příkazem nabídky k zobrazení průměrné teploty.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Zobrazí se graf časové řady. Změňte **interval** na **30 s**.

1. Opakujte předchozí krok s ostatními dvěma instancemi časových řad, abyste zobrazili všechny tři, jak je znázorněno v tomto grafu:

    [![graf pro všechny časové řady.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. V pravém horním rohu vyberte časový rozsah pro výběr. Tady můžete vybrat konkrétní počáteční a koncové časy do milisekundy nebo zvolit předem nakonfigurované možnosti, například **posledních 30 minut**. Můžete také změnit výchozí časové pásmo.

    [![nastavit časový rozsah na posledních 30 minut.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    V Průzkumníkovi Time Series Insights se teď zobrazí průběh akcelerátoru řešení za **posledních 30 minut** .

## <a name="define-and-apply-a-model"></a>Definování a použití modelu

V této části použijete model ke strukturování dat. Pro dokončení modelu definujete typy, hierarchie a instance. Další informace o modelování dat získáte v [modelu časové řady](./time-series-insights-update-tsm.md)pro čtení.

1. V Průzkumníku vyberte kartu **model** :

   [![zobrazit kartu model v Průzkumníkovi.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Na kartě **typy** vyberte **+ Přidat**.

1. Zadejte následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název** | Zadat **výtah** |
    | **Popis** | Zadejte **Toto je definice typu pro výtah** . |

1. V dalším kroku vyberte kartu **proměnné** . 

   Vyberte **+ přidat proměnnou** a vyplňte následující hodnoty pro první proměnnou typu výtahu. Budete vytvářet tři proměnné celkem.

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte **průměrnou teplotu**. |
    | **Plnění** | Vybrat **číselné** |
    | **Hodnota** | Vyberte možnost z předvolby: vyberte **teplotu (Double)** . <br /> Poznámka: když Azure Time Series Insights Preview začne přijímat události, může trvat několik minut, než se **hodnota** automaticky naplní.|
    | **Agregační operace** | Rozbalte položku **Pokročilá nastavení**. <br /> Vyberte **prům**. |

    Vyberte **Použít**. Pak **+ přidat proměnnou** znovu a nastavte následující hodnoty:

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte **průměrné vibrace**. |
    | **Plnění** | Vybrat **číselné** |
    | **Hodnota** | Vyberte možnost z předvolby: vyberte **vibrace (dvojitá)** . <br /> Poznámka: když Azure Time Series Insights Preview začne přijímat události, může trvat několik minut, než se **hodnota** automaticky naplní.|
    | **Agregační operace** | Rozbalte položku **Pokročilá nastavení**. <br /> Vyberte **prům**. |

    Vyberte **Použít**. Pak **+ přidat proměnnou** znovu a nastavte následující hodnoty pro třetí a poslední proměnnou:

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte **podlahovou základnu**. |
    | **Plnění** | Vybrat **kategorií** |
    | **Hodnota** | Vyberte možnost z předvolby: vyberte **podlahová (dvojitá)** . <br /> Poznámka: když Azure Time Series Insights Preview začne přijímat události, může trvat několik minut, než se **hodnota** automaticky naplní.|
    | **Kategorie** |   - <span style="text-decoration: underline">hodnoty</span> <span style="text-decoration: underline">popisku</span> <br /> Nižší: 1, 2, 3, 4 <br /> Uprostřed: 5, 6, 7, 8, 9, 9 <br /> Horní: 10, 11, 12, 13, 14, 15 |
    | **Výchozí kategorie** | Zadejte **Neznámý** |

    [![přidat proměnné typu.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Vyberte **Použít**.

1. Vyberte **Uložit**. Vytvoří a zobrazí tři proměnné.

    [![po přidání typu, přečtěte si ho v zobrazení modelu.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Vyberte kartu **hierarchie** . Pak vyberte **+ Přidat**.
   
   V podokně **Upravit hierarchii** nastavte následující parametry:

   | Parametr | Akce |
   | --- | ---|
   | **Název** | Zadejte **hierarchii umístění**. |
   |**Hladin**| Jako název první úrovně zadejte **Country (země** ). <br> Vybrat **+ Přidat úroveň** <br> Zadejte **město** druhé úrovně a pak vyberte **+ Přidat úroveň** . <br> Jako název třetí a poslední úrovně zadejte **sestavování** . |

   Vyberte **Uložit**.

   [![v zobrazení modelu zobrazit novou hierarchii.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Přejděte na **instance**. V části **Akce** úplně vpravo vyberte ikonu tužky a upravte první instanci s následujícími hodnotami:

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **výtah**. |
    | **Název** | Zadejte **výtah 1** .|
    | **Popis** | Zadejte **instanci pro výtah 1** . |

    Přejděte do **polí instance** a zadejte následující hodnoty:

    | Parametr | Akce |
    | --- | --- |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Krajin** | Zadejte **USA** . |
    | **Město** | Zadejte **Seattle** |
    | **Kapacit** | Zadat **ručičku místa** |

    Vyberte **Uložit**.

1. Pomocí následujících hodnot opakujte předchozí krok s ostatními dvěma instancemi:

    **Pro výtah 2:**

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **výtah**. |
    | **Název** | Zadejte **výtah 2** .|
    | **Popis** | Zadejte **instanci pro výtah 2** . |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Krajin** | Zadejte **USA** . |
    | **Město** | Zadejte **Seattle** |
    | **Kapacit** | Zadejte **střed pro Tichomoří – věda** |

    **Pro výtah 3:**

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **výtah**. |
    | **Název** | Zadejte **výtah 3**|
    | **Popis** | Zadejte **instanci pro výtah 3** . |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Krajin** | Zadejte **USA** . |
    | **Město** | Zadejte **New York** |
    | **Kapacit** | Zadejte **sestavení stavu Empire** |

    [![zobrazit aktualizované instance.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Přejděte zpět na kartu **analyzovat** a zobrazte podokno grafů. V části **Hierarchie umístění**rozbalte všechny úrovně hierarchie, aby se zobrazily instance časových řad:

    [![zobrazení všech hierarchií v zobrazení grafu.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. V oblasti **Tichomoří – střed**vyberte instance časové řady **2**a pak vyberte **Zobrazit průměrnou teplotu**.

1. U stejné instance, **výtah 2**, vyberte **Zobrazit podlahovou základnu**.

    Pomocí proměnné kategorií můžete určit, kolik času vychází z výtahu na horní, dolní a prostřední podlahovou plochu.

    [![Vizualizujte výtah 2 s hierarchií a daty.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

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
