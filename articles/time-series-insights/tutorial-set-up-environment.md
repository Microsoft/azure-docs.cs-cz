---
title: 'Kurz: nastavení prostředí Gen2 – Azure Time Series Insights Gen2 | Microsoft Docs'
description: 'Kurz: Naučte se, jak nastavit prostředí v Azure Time Series Insights Gen2.'
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/25/2021
ms.custom: seodec18
ms.openlocfilehash: 76a33bdb773645c9e8f97a47b1378d813b165631
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464575"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Kurz: nastavení prostředí Azure Time Series Insights Gen2

V tomto kurzu Vás provedeme procesem vytvoření prostředí Azure Time Series Insights Gen2 s průběžnými *platbami* (PAYG).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte prostředí Azure Time Series Insights Gen2.
> * Připojte prostředí Azure Time Series Insights Gen2 k IoT Hub.
> * Spusťte ukázku akcelerátoru řešení pro streamování dat do prostředí Azure Time Series Insights Gen2.
> * Proveďte základní analýzu dat.
> * Definujte typ modelu časové řady a hierarchii a přidružte je k vašim instancím.

>[!TIP]
> [Akcelerátory řešení IoT](https://www.azureiotsolutions.com/Accelerators) poskytují předkonfigurovaná řešení na podnikové úrovni, která můžete použít k urychlení vývoje vlastních řešení IoT.

Zaregistrujte si [bezplatné předplatné Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte.

## <a name="prerequisites"></a>Požadavky

* Minimálně musíte mít roli **přispěvatele** pro předplatné Azure. Pokud chcete získat další informace, přečtěte si téma [přiřazení rolí Azure pomocí Azure Portal](../role-based-access-control/role-assignments-portal.md).

* Vytvořte prostředí pomocí [Azure Portal](#create-an-azure-time-series-insights-gen2-environment) nebo [CLI](how-to-create-environment-using-cli.md).

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

V této části vytvoříte tři simulovaná zařízení, která odesílají data do instance služby Azure IoT Hub.

1. Přejít na [stránku akcelerátory řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Přihlaste se pomocí svého účtu Azure a pak vyberte **simulace zařízení**.

   [![Stránka akcelerátory řešení Azure IoT.](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Posuňte se dolů a přečtěte si části [Přehled](https://github.com/Azure/azure-iot-pcs-device-simulation#overview) a [Začínáme](https://github.com/Azure/azure-iot-pcs-device-simulation#getting-started) .

1. Postupujte podle [pokynů pro nasazení](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/deployment/README.md) v části Začínáme.

   Dokončení tohoto procesu může trvat až 20 minut.

1. Po dokončení nasazení se vám poskytne adresa URL simulace. Tuto stránku nechte otevřenou, protože se k ní vrátíte později.

   >[!IMPORTANT]
   > Ještě nezadávejte akcelerátor řešení. Tuto webovou stránku nechte otevřenou, protože se k ní vrátíte později.

   [![Zřizování řešení pro simulaci zařízení bylo dokončeno.](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png)](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png#lightbox)

1. Nyní zkontrolujte nově vytvořené prostředky v Azure Portal. Na stránce **skupiny prostředků** si všimněte, že se vytvořila nová skupina prostředků pomocí služby, `solutionName` kterou jste zadali v souboru parametrů šablony ARM. Poznamenejte si prostředky, které byly vytvořeny pro simulaci zařízení.

   [![Prostředky simulace zařízení.](media/tutorial-set-up-environment/device-sim-solution-resources.png)](media/tutorial-set-up-environment/device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Vytvoření prostředí Azure Time Series Insights Gen2

Tato část popisuje, jak vytvořit prostředí Azure Time Series Insights Gen2 a připojit ho ke službě IoT Hub vytvořené pomocí akcelerátoru řešení IoT pomocí [Azure Portal](https://portal.azure.com/).

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu předplatného Azure.
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**.
1. Vyberte kategorii **Internet věcí** a pak vyberte **Time Series Insights**.

   [![Vyberte prostředek prostředí Time Series Insights.](media/tutorial-set-up-environment/create-new-environment.png)](media/tutorial-set-up-environment/create-new-environment.png#lightbox)

1. V podokně **vytvořit Time Series Insights prostředí** na kartě **základy** nastavte následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název prostředí** | Zadejte jedinečný název prostředí Azure Time Series Insights Gen2. |
    | **Předplatné** | Zadejte předplatné, ve kterém chcete vytvořit prostředí Azure Time Series Insights Gen2. Osvědčeným postupem je použít stejné předplatné jako zbytek prostředků IoT vytvořených simulátorem zařízení. |
    | **Skupina prostředků** | Vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků pro prostředek prostředí Azure Time Series Insights Gen2. Skupina prostředků představuje kontejner prostředků Azure. Osvědčeným postupem je použití stejné skupiny prostředků jako u jiných prostředků IoT, které jsou vytvořené simulátorem zařízení. |
    | **Umístění** | Vyberte oblast datového centra pro prostředí Azure Time Series Insights Gen2. Abyste se vyhnuli další latenci, je nejlepší vytvořit prostředí Azure Time Series Insights Gen2 ve stejné oblasti, ve které se služba IoT Hub vytvořila simulátorem zařízení. |
    | **Úroveň** |  Vyberte **Gen2 (L1)**. Toto je SKU pro produkt Azure Time Series Insights Gen2. |
    | **Název vlastnosti Time Series ID** | Zadejte název vlastnosti, která obsahuje hodnoty, které jedinečně identifikují instance časových řad. Hodnota, kterou zadáte do pole **název vlastnosti** jako časový interval, se nedá později změnit. Pro tento kurz zadejte **_iothub-Connection-Device-ID_**. Pokud chcete získat další informace o ID časové řady včetně ID složené časové řady, přečtěte si [osvědčené postupy pro výběr ID časové řady](./how-to-select-tsid.md). |
    | **Název účtu úložiště** | Zadejte globálně jedinečný název nového účtu úložiště.|
    | **Druh účtu úložiště** | Vyberte druh úložiště pro nový účet úložiště. Doporučujeme StorageV2|
    | **Replikace účtu úložiště** | Vyberte druh úložiště pro nový účet úložiště. Na základě vašeho výběru polohy si můžete vybrat z LRS, GRS a ZRS. Pro tento kurz můžete vybrat LRS|
    | **Hierarchický obor názvů** |Tato možnost se dá vybrat, když vyberete druh úložiště, který se má StorageV2. Ve výchozím nastavení je zakázaný. V tomto kurzu ho můžete nechat ve výchozím stavu *zakázáno* .|
    |**Povolit teplé úložiště**|Vyberte **Ano** , pokud chcete povolit teplé úložiště. Toto nastavení se dá zakázat a po vytvoření prostředí taky znovu povolit. |
    |**Uchovávání dat (ve dnech)**|Vyberte výchozí možnost 7 dní. |

    [![Nová konfigurace Azure Time Series Insights prostředí.](media/tutorial-set-up-environment/environment-configuration.png)](media/tutorial-set-up-environment/environment-configuration.png#lightbox)
    [![Nová konfigurace Azure Time Series Insights prostředí pokračuje.](media/tutorial-set-up-environment/environment-configuration2.png)](media/tutorial-set-up-environment/environment-configuration2.png#lightbox)

1. Vyberte **Další: zdroj události**.

   [![Nakonfigurujte ID časových řad pro prostředí.](media/tutorial-set-up-environment/time-series-id-selection.png)](media/tutorial-set-up-environment/time-series-id-selection.png#lightbox)

1. Na kartě **zdroj události** nastavte následující parametry:

   | Parametr | Akce |
   | --- | --- |
   | **Vytvořit zdroj události?** | Vyberte **Ano**.|
   | **Typ zdroje** | Vyberte **IoT Hub**. |
   | **Název** | Zadejte jedinečnou hodnotu názvu zdroje události. |
   | **Výběr centra** | Zvolte **Vybrat existující**. |
   | **Předplatné** | Vyberte předplatné, které jste použili pro simulátor zařízení. |
   | **Název IoT Hub** | Vyberte název služby IoT Hub, který jste vytvořili pro simulátor zařízení. |
   | **Zásada přístupu IoT Hub** | Vyberte **iothubowner**. |
   | **Skupina uživatelů IoT Hub** | Vyberte **Nový**, zadejte jedinečný název a potom vyberte **+ Přidat**. Skupina příjemců musí být jedinečnou hodnotou v Azure Time Series Insights Gen2. |
   | **Vlastnost timestamp** | Tato hodnota se používá k identifikaci vlastnosti **časového razítka** ve příchozích datech telemetrie. V tomto kurzu ponechte toto pole prázdné. Simulátor používá příchozí časové razítko z IoT Hub, které Azure Time Series Insights Gen2 výchozí hodnoty. |

1. Vyberte **Zkontrolovat a vytvořit**.

   [![Nakonfigurujte vytvořené centrum IoT jako zdroj události.](media/tutorial-set-up-environment/configure-event-source.png)](media/tutorial-set-up-environment/configure-event-source.png#lightbox)

1. Vyberte **Zkontrolovat a vytvořit**.

    [![Klikněte na tlačítko Zobrazit a vytvořit stránku s tlačítkem vytvořit.](media/tutorial-set-up-environment/environment-confirmation.png)](media/tutorial-set-up-environment/environment-confirmation.png#lightbox)

    Můžete zkontrolovat stav nasazení:

    [![Oznámení o dokončení nasazení.](media/tutorial-set-up-environment/deployment-notification.png)](media/tutorial-set-up-environment/deployment-notification.png#lightbox)

1. Rozbalte podrobnosti nasazení.

## <a name="stream-data"></a>Streamování dat

Teď, když máte nasazené prostředí Azure Time Series Insights Gen2, začněte streamovat data pro analýzu.

1. Po dokončení nasazení akcelerátoru řešení budete mít adresu URL.

1. Kliknutím na adresu URL spusťte simulaci zařízení.

1. Vyberte **+ Nová simulace**.

    1. Po načtení stránky **Nastavení simulace** zadejte požadované parametry.

        | Parametr | Akce |
        | --- | --- |
        | **Název** | Zadejte jedinečný název simulátoru. |
        | **Popis** | Zadejte definici. |
        | **Doba trvání simulace** | Nastaveno na **neomezenou dobu**. |
        | **Model zařízení** | Klikněte na + **Přidat typ zařízení** . <br />**Název**: zadejte **výtah**. <br />**Částka**: zadejte **3**. <br /> Zbývající výchozí hodnoty ponechte. |
        | **Cílová služba IoT Hub** | Nastavte na **použít předem zřízené IoT Hub**. |

        [![Nakonfigurujte parametry a spusťte.](media/tutorial-set-up-environment/launch-solution-accelerator.png)](media/tutorial-set-up-environment/launch-solution-accelerator.png#lightbox)

    1. Vyberte **spustit simulaci**. V řídicím panelu pro simulaci zařízení se zobrazí **aktivní zařízení** a **Celkový počet zpráv** .

        [![Řídicí panel simulace Azure IoT.](media/tutorial-set-up-environment/see-active-devices-and-messages.png)](media/tutorial-set-up-environment/see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analýza dat

V této části provedete základní analýzy pro data časových řad pomocí [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

1. Vyberte adresu URL ze stránky prostředků v [Azure Portal](https://portal.azure.com/)a otevřete Azure Time Series Insights Gen2 Explorer.

    [![Adresa URL aplikace Azure Time Series Insights Gen2](media/tutorial-set-up-environment/select-explorer-url.png)](media/tutorial-set-up-environment/select-explorer-url.png#lightbox)

1. V Azure Time Series Insights Průzkumníku Gen2 se zobrazí pruh rozložený v horní části obrazovky. Toto je váš výběr vaší dostupnosti. Ujistěte se, že máte vybrané aspoň 2 2 m a v případě potřeby rozbalte časový rámec výběrem a přetažením úchytů pro výběr vlevo a vpravo.

1. **Instance časových řad** se zobrazí na levé straně.

    [![Seznam nenadřazených instancí.](media/tutorial-set-up-environment/explorer-unparented-instances.png)](media/tutorial-set-up-environment/explorer-unparented-instances.png#lightbox)

1. Vyberte instanci instance řady First-Time. Pak vyberte **Zobrazit teplotu**.

    [![Vybraná instance časové řady s příkazem nabídky zobrazí průměrnou teplotu.](media/tutorial-set-up-environment/select-instance-and-temperature.png)](media/tutorial-set-up-environment/select-instance-and-temperature.png#lightbox)

    Zobrazí se graf časové řady. Změňte **interval** na **30 s**.

1. Opakujte předchozí krok s ostatními dvěma instancemi časových řad, abyste zobrazili všechny tři, jak je znázorněno v tomto grafu:

    [![Graf pro všechny časové řady](media/tutorial-set-up-environment/explorer-add-three-instances.png)](media/tutorial-set-up-environment/explorer-add-three-instances.png#lightbox)

1. V pravém horním rohu vyberte časový rozsah pro výběr. Tady můžete vybrat konkrétní počáteční a koncové časy do milisekundy nebo zvolit předem nakonfigurované možnosti, například **posledních 30 minut**. Můžete také změnit výchozí časové pásmo.

    [![Nastavte časový rozsah na posledních 30 minut.](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png)](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png#lightbox)

    V Azure Time Series Insights Průzkumníku Gen2 se teď zobrazí průběh akcelerátoru řešení za **posledních 30 minut** .

## <a name="define-and-apply-a-model"></a>Definování a použití modelu

V této části použijete model ke strukturování dat. Pro dokončení modelu definujete typy, hierarchie a instance. Další informace o modelování dat získáte v [modelu časové řady](./concepts-model-overview.md)pro čtení.

1. V Průzkumníku vyberte kartu **model** :

   [![Zobrazte kartu model v Průzkumníkovi.](media/tutorial-set-up-environment/select-model-view.png)](media/tutorial-set-up-environment/select-model-view.png#lightbox)

   Na kartě **typy** vyberte **+ Přidat**.

1. Zadejte následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název** | Zadat **výtah** |
    | **Popis** | Zadejte **Toto je definice typu pro výtah** . |

1. V dalším kroku vyberte kartu **proměnné** .

    1. Vyberte **+ přidat proměnnou** a vyplňte následující hodnoty pro první proměnnou typu výtahu. Budete vytvářet tři proměnné celkem.

        | Parametr | Akce |
        | --- | --- |
        | **Název** | Zadejte **průměrnou teplotu**. |
        | **Druh** | Vybrat **číselné** |
        | **Hodnota** | Vyberte možnost z předvolby: vyberte **teplotu (Double)**. <br /> Poznámka: může trvat několik minut, než se **hodnota** automaticky vyplní po Azure Time Series Insights Gen2 spustí přijímání událostí.|
        | **Agregační operace** | Rozbalte položku **Pokročilá nastavení**. <br /> Vyberte **prům**. |

    1. Vyberte **Použít**. Pak **+ přidat proměnnou** znovu a nastavte následující hodnoty:

        | Parametr | Akce |
        | --- | --- |
        | **Název** | Zadejte **průměrné vibrace**. |
        | **Druh** | Vybrat **číselné** |
        | **Hodnota** | Vyberte možnost z předvolby: vyberte **vibrace (dvojitá)**. <br /> Poznámka: může trvat několik minut, než se **hodnota** automaticky vyplní po Azure Time Series Insights Gen2 spustí přijímání událostí.|
        | **Agregační operace** | Rozbalte položku **Pokročilá nastavení**. <br /> Vyberte **prům**. |

    1. Vyberte **Použít**. Pak **+ přidat proměnnou** znovu a nastavte následující hodnoty pro třetí a poslední proměnnou:

        | Parametr | Akce |
        | --- | --- |
        | **Název** | Zadejte **podlahovou základnu**. |
        | **Druh** | Vybrat **kategorií** |
        | **Hodnota** | Vyberte možnost z předvolby: vyberte **podlahová (dvojitá)**. <br /> Poznámka: může trvat několik minut, než se **hodnota** automaticky vyplní po Azure Time Series Insights Gen2 spustí přijímání událostí.|
        | **Kategorie** | <span style="text-decoration: underline">Popisek</span>   -  <span style="text-decoration: underline">Hodnoty</span> <br /> Nižší: 1, 2, 3, 4 <br /> Uprostřed: 5, 6, 7, 8, 9, 9 <br /> Horní: 10, 11, 12, 13, 14, 15 |
        | **Výchozí kategorie** | Zadejte **Neznámý** |

        [![Přidejte proměnné typu.](media/tutorial-set-up-environment/add-type-variables.png)](media/tutorial-set-up-environment/add-type-variables.png#lightbox)

    1. Vyberte **Použít**.
    1. Vyberte **Uložit**. Vytvoří a zobrazí tři proměnné.

        [![Po přidání typu si ho přečtěte v zobrazení modelu.](media/tutorial-set-up-environment/add-type-and-view.png)](media/tutorial-set-up-environment/add-type-and-view.png#lightbox)

1. Vyberte kartu **hierarchie** . Pak vyberte **+ Přidat**.

   1. V podokně **Upravit hierarchii** nastavte následující parametry:

        | Parametr | Akce |
        | --- | ---|
        | **Název** | Zadejte **hierarchii umístění**. |
        |**Úrovně**| Jako název první úrovně zadejte **Country (země** ).<br />Vybrat **+ Přidat úroveň**<br />Zadejte **město** druhé úrovně a pak vyberte **+ Přidat úroveň** .<br />Jako název třetí a poslední úrovně zadejte **sestavování** . |

   1. Vyberte **Uložit**.

        [![Zobrazit novou hierarchii v zobrazení modelu.](media/tutorial-set-up-environment/add-hierarchy-and-view.png)](media/tutorial-set-up-environment/add-hierarchy-and-view.png#lightbox)

1. Přejděte na **instance**.

    1. V části **Akce** úplně vpravo vyberte ikonu tužky a upravte první instanci s následujícími hodnotami:

        | Parametr | Akce |
        | --- | --- |
        | **Typ** | Vyberte **výtah**. |
        | **Název** | Zadejte **výtah 1** .|
        | **Popis** | Zadejte **instanci pro výtah 1** . |

    1. Přejděte do **polí instance** a zadejte následující hodnoty:

        | Parametr | Akce |
        | --- | --- |
        | **Hierarchie** | Vybrat **hierarchii umístění** |
        | **Země** | Zadejte **USA** . |
        | **City (Město)** | Zadejte **Seattle** |
        | **Kapacit** | Zadat **ručičku místa** |

    1. Vyberte **Uložit**.

1. Pomocí následujících hodnot opakujte předchozí krok s ostatními dvěma instancemi:

    **Pro výtah 2:**

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **výtah**. |
    | **Název** | Zadejte **výtah 2** .|
    | **Popis** | Zadejte **instanci pro výtah 2** . |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Země** | Zadejte **USA** . |
    | **City (Město)** | Zadejte **Seattle** |
    | **Kapacit** | Zadejte **střed pro Tichomoří – věda** |

    **Pro výtah 3:**

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **výtah**. |
    | **Název** | Zadejte **výtah 3**|
    | **Popis** | Zadejte **instanci pro výtah 3** . |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Země** | Zadejte **USA** . |
    | **City (Město)** | Zadejte **New York** |
    | **Kapacit** | Zadejte **sestavení stavu Empire** |

    [![Zobrazit aktualizované instance.](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png)](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png#lightbox)

1. Přejděte zpět na kartu **analyzovat** a zobrazte podokno grafů. V části **Hierarchie umístění** rozbalte všechny úrovně hierarchie, aby se zobrazily instance časových řad:

    [![Zobrazení všech hierarchií v zobrazení grafu.](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png)](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. V oblasti **Tichomoří – střed** vyberte instance časové řady **2** a pak vyberte **Zobrazit průměrnou teplotu**.

1. U stejné instance, **výtah 2**, vyberte **Zobrazit podlahovou základnu**.

    Pomocí proměnné kategorií můžete určit, kolik času vychází z výtahu na horní, dolní a prostřední podlahovou plochu.

    [![Vizualizujte si výtah 2 s hierarchií a daty.](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png)](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili kurz, vyčistěte prostředky, které jste vytvořili:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky** a vyhledejte vaši skupinu prostředků Azure Time Series Insights Gen2.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) tak, že vyberete možnost **Odstranit** nebo odebrat jednotlivé prostředky zvlášť.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvořte a použijte akcelerátor simulace zařízení.
* Vytvořte Azure Time Series Insights prostředí PAYG pro Gen2.
* Připojte prostředí Azure Time Series Insights Gen2 k centru IoT.
* Spusťte ukázku akcelerátoru řešení pro streamování dat do prostředí Azure Time Series Insights Gen2.
* Provede základní analýzu dat.
* Definujte typ modelu časové řady a hierarchii a přidružte je k vašim instancím.

Když teď víte, jak vytvořit vlastní prostředí Azure Time Series Insights Gen2, přečtěte si další informace o klíčových konceptech v Azure Time Series Insights Gen2.

Přečtěte si o Azure Time Series Insights ingestování Gen2:

> [!div class="nextstepaction"]
> [Přehled příjmu dat Azure Time Series Insights Gen2](./concepts-ingestion-overview.md)

Přečtěte si o Azure Time Series Insights Gen2 Storage:

> [!div class="nextstepaction"]
> [Azure Time Series Insights úložiště dat Gen2](./concepts-storage.md)

Další informace o modelech časových řad:

> [!div class="nextstepaction"]
> [Azure Time Series Insights modelování dat Gen2](./concepts-model-overview.md)

Přečtěte si další informace o připojení prostředí k Power BI:

> [!div class="nextstepaction"]
> [Vizualizace dat z Azure Time Series Insights Gen2 v Power BI](./how-to-connect-power-bi.md)
