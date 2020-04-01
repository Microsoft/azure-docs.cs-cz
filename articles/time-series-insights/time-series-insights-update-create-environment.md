---
title: 'Kurz: Nastavení prostředí Preview – Přehledy azure time series | Dokumenty společnosti Microsoft'
description: 'Kurz: Naučte se, jak nastavit prostředí v Azure Time Series Insights Preview.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: af15a7366fd07cecb376ff76ad383f784202a887
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77526806"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Kurz: Nastavení prostředí Azure Time Series Insights Preview

Tento kurz vás provede procesem vytváření prostředí Azure Time Series Insights Preview *s průběžným platbou* za čas (PAYG).

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte prostředí Azure Time Series Insights Preview.
> * Připojte prostředí Azure Time Series Insights Preview k centru IoT Hub.
> * Spusťte ukázku akcelerátoru řešení pro streamování dat do prostředí Azure Time Series Insights Preview.
> * Proveďte základní analýzu dat.
> * Definujte typ a hierarchii modelu časové řady a přidružte je k instancím.
> * Použijte konektor Power BI a vizualizujte data v Power BI.

>[!TIP]
> [Akcelerátory řešení IoT](https://www.azureiotsolutions.com/Accelerators) poskytují předkonfigurovaná řešení na podnikové úrovni, která můžete použít k urychlení vývoje vlastních řešení IoT.

Pokud ještě nemáte předplatné Azure, zaregistrujte si předplatné [Azure.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Požadavky

* Minimálně musíte mít roli **přispěvatele** pro předplatné Azure. Další informace [načtete spravovat přístup pomocí řízení přístupu na základě rolí a portálu Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Vytvoření simulace zařízení

V této části vytvoříte tři simulovaná zařízení, která odesílají data do instance služby Azure IoT Hub.

1. Přejděte na [stránku akcelerátorů řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators). Stránka zobrazí několik předem sestavených příkladů. Přihlaste se pomocí svého účtu Azure. Potom vyberte **Simulace zařízení**.

   [![Stránka akcelerátorů řešení Azure IoT.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Na další stránce vyberte **Try Now**. Potom zadejte požadované parametry na stránce **Řešení Vytvořit simulační zařízení.**

   Parametr|Popis
   ---|---
   **Název nasazení** | Tato jedinečná hodnota se používá k vytvoření nové skupiny prostředků. Uvedené prostředky Azure se vytvoří a přiřadí do skupiny prostředků.
   **Předplatné Azure** | Zadejte stejné předplatné, které bylo použito k vytvoření prostředí Time Series Insights v předchozí části.
   **Možnosti nasazení** | Vyberte **Zřídit nové Centrum IoT Hub** a vytvořte nové centrum IoT specifické pro tento kurz.
   **Umístění Azure** | Zadejte stejnou oblast, která byla použita k vytvoření prostředí Time Series Insights v předchozí části.

   Až budete hotovi, vyberte **Vytvořit,** chcete-li zřídit prostředky Azure řešení. Dokončení tohoto procesu může trvat až 20 minut.

   [![Zřizte simulační řešení zařízení.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Po dokončení zřizování se zobrazí dvě oznámení oznamující, že stav nasazení se přesunul z **zřizování** na **Připraveno**. 

   >[!IMPORTANT]
   > Ještě nevstupujte do akcelerátoru řešení! Ponechte tuto webovou stránku otevřenou, protože se k ní později vrátíte.

   [![Zřizování řešení simulace zařízení dokončeno.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Teď zkontrolujte nově vytvořené prostředky na webu Azure Portal. Na stránce **Skupiny prostředků** si všimněte, že nová skupina prostředků byla vytvořena pomocí **názvu řešení** uvedeného v posledním kroku. Poznamenejte si prostředky, které byly vytvořeny pro simulaci zařízení.

   [![Prostředky simulace zařízení.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Vytvoření prostředí Preview PAYG

Tato část popisuje, jak vytvořit prostředí Azure Time Series Insights Preview a připojit ho k centru IoT vytvořenému akcelerátorem řešení IoT pomocí [portálu Azure](https://portal.azure.com/).

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu předplatného Azure. 
1. Vlevo nahoře vyberte **+ Vytvořit prostředek**. 
1. Vyberte kategorii **Internet věcí** a pak vyberte **Přehledy časových řad**. 

   [![Vyberte prostředek prostředí Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. V podokně **prostředí Vytvořit přehledy časových řad** nastavte na kartě **Základy** následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název prostředí** | Zadejte jedinečný název prostředí Azure Time Series Insights Preview. |
    | **Předplatné** | Zadejte předplatné, ve kterém chcete vytvořit prostředí Azure Time Series Insights Preview. Osvědčeným postupem je použití stejného předplatného jako ostatní prostředky IoT, které jsou vytvořeny simulátoru zařízení. |
    | **Skupina prostředků** | Vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků pro prostředek prostředí Azure Time Series Insights Preview. Skupina prostředků představuje kontejner prostředků Azure. Osvědčeným postupem je použití stejné skupiny prostředků jako ostatní prostředky IoT, které jsou vytvořeny simulátoru zařízení. |
    | **Umístění** | Vyberte oblast datového centra pro prostředí Azure Time Series Insights Preview. Chcete-li se vyhnout další latenci, je nejlepší vytvořit prostředí Azure Time Series Insights Preview ve stejné oblasti jako vaše centrum IoT vytvořené simulátorem zařízení. |
    | **Úroveň** |  Vyberte **PAYG** *(průběžně zdatný).* Toto je skladová položka produktu Azure Time Series Insights Preview. |
    | **Název vlastnosti** | Zadejte hodnotu, která jednoznačně identifikuje instanci časové řady. Hodnotu, kterou zadáte do pole **ID vlastnosti,** nelze později změnit. V tomto kurzu zadejte ***iothub-connection-device-id***. Další informace o ID časové řady najdete v [doporučených postupech pro výběr ID časové řady](./time-series-insights-update-how-to-id.md). |
    | **Název účtu úložiště** | Zadejte globálně jedinečný název nového účtu úložiště.|
    |**Povolit teplý obchod**|Chcete-li povolit teplý obchod, vyberte **možnost Ano.** Můžete se vrátit později a povolit toto nastavení. |
    |**Uchovávání dat (ve dnech)**|Zvolte výchozí možnost 7 dní. |

    Vyberte **další: Zdroj události**.

   [![Nová konfigurace prostředí Time Series Insights.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![Konfigurace ID časové řady pro prostředí.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Na kartě **Zdroj událostí** nastavte následující parametry:

   | Parametr | Akce |
   | --- | --- |
   | **Chcete vytvořit zdroj události?** | Vyberte **ano**.|
   | **Název** | Zadejte jedinečnou hodnotu pro název zdroje události. |
   | **Typ zdroje** | Vyberte **IoT Hub**. |
   | **Výběr rozbočovače** | Zvolte **Vybrat existující**. |
   | **Předplatné** | Vyberte předplatné, které jste použili pro simulátor zařízení. |
   | **Název ioT hubu** | Vyberte název centra IoT, který jste vytvořili pro simulátor zařízení. |
   | **Zásady přístupu k centru IoT Hub** | Vyberte **iothubowner**. |
   | **Skupina spotřebitelů IoT Hub** | Vyberte **Nový**, zadejte jedinečný název a pak vyberte **+ Přidat**. Skupina spotřebitelů musí být jedinečnou hodnotou ve verzi Azure Time Series Insights Preview. |
   | **Vlastnost časového razítka** | Tato hodnota se používá k identifikaci vlastnosti **Timestamp** v příchozích telemetrických datech. V tomto kurzu ponechte toto pole prázdné. Tento simulátor používá příchozí časové razítko z ioT hubu, které Time Series Insights výchozí. |

   Vyberte **zkontrolovat + vytvořit**.

   [![Nakonfigurujte vytvořený portál IoT hub jako zdroj událostí.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Vyberte **Vytvořit**.

    [![Recenze + Vytvořit stránku pomocí tlačítka Vytvořit.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Můžete zkontrolovat stav nasazení:

    [![Oznámení, že nasazení je dokončeno.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Pokud jste vlastníkem předplatného Azure, máte ve výchozím nastavení přístup k prostředí Azure Time Series Insights Preview. Ověřte, zda máte přístup:

   1. Vyhledejte svou skupinu prostředků a vyberte nově vytvořené prostředí Azure Time Series Insights Preview. 

      [![Vyberte a zobrazte své prostředí.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Na stránce Azure Time Series Insights Preview vyberte **Zásady přístupu k datům**:

      [![Ověřte zásady přístupu k datům.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Ověřte, zda jsou vaše přihlašovací údaje uvedeny:

      Pokud vaše přihlašovací údaje nejsou uvedeny, musíte si udělit oprávnění k přístupu k prostředí výběrem přidat a hledat vaše přihlašovací údaje. Další informace o nastavení oprávnění najdete v článku [Udělení přístupu k datům](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Streamování dat

Teď, když jste nasadili prostředí Time Series Insights, začněte streamovat data pro analýzu.

1. Vraťte se zpět na [Řídicí panel akcelerátorů řešení](https://www.azureiotsolutions.com/Accelerators#dashboard). V případě potřeby se znovu přihlaste pomocí stejného účtu Azure, který jste používali v tomto kurzu. Vyberte "Řešení zařízení" a pak **přejděte na akcelerátor řešení** a spusťte nasazené řešení.

   [![Řídicí panel akcelerátorů řešení.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Webová aplikace pro simulaci zařízení začíná výzvou k udělení webové aplikace **Přihlásit se k vám a přečíst si** oprávnění k profilu. Toto oprávnění umožňuje aplikaci načíst informace o profilu uživatele nezbytné pro podporu fungování aplikace.

   [![Souhlas webové aplikace pro simulaci zařízení.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Vyberte **+ Nová simulace**. Po načtení stránky **Nastavení simulace** zadejte požadované parametry.

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte jedinečný název simulátoru. |
    | **Popis** | Zadejte definici. |
    | **Doba trvání simulace** | Nastaveno na **neomezenou dobu**. |
    | **Model zařízení** | Klikněte na + **Přidat typ zařízení.** <br />**Název**: Zadejte **výtah**. <br />**Částka**: Zadejte **3**. <br /> Ponechat zbývající výchozí hodnoty |
    | **Cílová služba IoT Hub** | Nastavte **na použití předem zřízeného centra IoT Hub**. |

    [![Nakonfigurujte parametry a spusťte.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Vyberte **Spustit simulaci**.

    Na řídicím panelu simulace zařízení se zobrazují **aktivní zařízení** a **celkový počet zpráv.**

    [![Řídicí panel simulace Azure IoT.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analýza dat

V této části provádíte základní analýzy dat časových řad pomocí [průzkumníka Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

1. Přejděte do průzkumníka Náhled přehledů Azure Time Series výběrem adresy URL ze stránky prostředků na [webu Azure Portal](https://portal.azure.com/).

    [![Adresa URL průzkumníka náhledu přehledů časové řady.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. V průzkumníku Time Series Insights se zobrazí pruh v horní části obrazovky. Toto je váš výběr dostupnosti. Ujistěte se, že máte vybrány alespoň dva 2 m, a v případě potřeby rozbalte časový rámec výběrem a přetažením úchytů pro výběr doleva a doprava.

1. **Instance časových řad** se zobrazí na levé straně.

    [![Seznam nenadřazených instancí.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Vyberte instanci první série. Potom vyberte **Zobrazit teplotu**.

    [![Vybraná instance časové řady s příkazem nabídky pro zobrazení průměrné teploty.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Zobrazí se graf časových řad. Změňte **interval** na **30s**.

1. Opakujte předchozí krok s dalšími dvěma instancemi časových řad, abyste si prohlíželi všechny tři, jak je znázorněno v tomto grafu:

    [![Graf pro všechny časové řady.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. V pravém horním rohu vyberte výběr časového rozpětí. Zde můžete vybrat konkrétní počáteční a koncový čas až do milisekundy, nebo si vybrat z předem nakonfigurovaných možností, jako je **posledních 30 minut**. Můžete také změnit výchozí časové pásmo.

    [![Nastavte časový rozsah na posledních 30 minut.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    Průběh akcelerátoru řešení za **posledních 30 minut** se nyní zobrazí v průzkumníku Time Series Insights.

## <a name="define-and-apply-a-model"></a>Definování a použití modelu

V této části použijete model pro strukturu dat. Chcete-li model dokončit, definujte typy, hierarchie a instance. Další informace o modelování dat najdete v textu [Model časových řad](./time-series-insights-update-tsm.md).

1. V průzkumníku vyberte kartu **Model:**

   [![Zobrazení karty Model v průzkumníku.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Na kartě **Typy** vyberte **+ Přidat**.

1. Zadejte následující parametry:

    | Parametr | Akce |
    | --- | ---|
    | **Název** | Zadejte **výtah** |
    | **Popis** | **Zadejte: Toto je definice typu pro výtah** |

1. Dále vyberte kartu **Proměnné.** 

   Vyberte **+ Přidat proměnnou** a vyplňte následující hodnoty pro první proměnnou typu Výtah. Vytvoříte celkem tři proměnné.

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte **Avg Teplota**. |
    | **Druhu** | Vybrat **číselné** |
    | **Hodnotu** | Vyberte si z předvolby: Zvolte **teplotu (Double).** <br /> Poznámka: Může trvat několik **minut,** než se hodnota automaticky naplní, jakmile Azure Time Series Insights Preview začne přijímat události.|
    | **Operace agregace** | Rozbalte **rozšířené možnosti**. <br /> Vyberte **Možnost AVG**. |

    Vyberte **Použít**. Potom **+ Přidat proměnnou** znovu a nastavte následující hodnoty:

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte **Avg Vibration**. |
    | **Druhu** | Vybrat **číselné** |
    | **Hodnotu** | Vyberte si z předvolby: Vyberte **vibrace (Double).** <br /> Poznámka: Může trvat několik **minut,** než se hodnota automaticky naplní, jakmile Azure Time Series Insights Preview začne přijímat události.|
    | **Operace agregace** | Rozbalte **rozšířené možnosti**. <br /> Vyberte **Možnost AVG**. |

    Vyberte **Použít**. Potom **+ Přidat proměnnou** znovu a nastavte následující hodnoty pro třetí a konečnou proměnnou:

    | Parametr | Akce |
    | --- | --- |
    | **Název** | Zadejte **podlahu**. |
    | **Druhu** | Vybrat **kategorické** |
    | **Hodnotu** | Vyberte z předvolby: Vyberte **podlahu (dvojitou)**. <br /> Poznámka: Může trvat několik **minut,** než se hodnota automaticky naplní, jakmile Azure Time Series Insights Preview začne přijímat události.|
    | **Kategorie** | <span style="text-decoration: underline">Hodnoty popisků </span>   -  <span style="text-decoration: underline">Values</span> <br /> Nižší: 1,2,3,4 <br /> Střední: 5,6,7,8,9 <br /> Svršek: 10,11,12,13,14,15 |
    | **Výchozí kategorie** | Zadejte **neznámý** |

    [![Přidejte proměnné typu.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Vyberte **Použít**.

1. Vyberte **Uložit**. Jsou vytvořeny a zobrazeny tři proměnné.

    [![Po přidání typu jej zkontrolujte v pohledu model.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Vyberte kartu **Hierarchie.** Potom vyberte **+ Přidat**.
   
   V podokně **Upravit hierarchii** nastavte následující parametry:

   | Parametr | Akce |
   | --- | ---|
   | **Název** | Zadejte **hierarchii umístění**. |
   |**Úrovně**| Zadejte **zemi** jako název první úrovně. <br> Vybrat **+ Přidat úroveň** <br> Zadejte **město** pro druhou úroveň a pak vyberte **+ Přidat úroveň** <br> Zadejte **budovu** jako název třetí a konečné úrovně. |

   Vyberte **Uložit**.

   [![Zobrazí novou hierarchii v zobrazení model.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Přejděte na **Instance**. V části **Akce** zcela vpravo a vyberte ikonu tužky, chcete-li upravit první instanci s následujícími hodnotami:

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **možnost Výtah**. |
    | **Název** | Zadejte **výtah 1**|
    | **Popis** | Zadat **instanci výtahu 1** |

    Přejděte do **polí instance** a zadejte následující hodnoty:

    | Parametr | Akce |
    | --- | --- |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Země** | Zadejte **USA** |
    | **Město** | Zadejte **Seattle** |
    | **Sestavování** | Zadejte **mezeru jehlu** |

    Vyberte **Uložit**.

1. Opakujte předchozí krok s ostatními dvěma instancemi při použití následujících hodnot:

    **Pro výtah 2:**

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **možnost Výtah**. |
    | **Název** | Zadejte **výtah 2**|
    | **Popis** | Zadat **instanci výtahu 2** |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Země** | Zadejte **USA** |
    | **Město** | Zadejte **Seattle** |
    | **Sestavování** | Zadejte **Pacific Science Center** |

    **Pro výtah 3:**

    | Parametr | Akce |
    | --- | --- |
    | **Typ** | Vyberte **možnost Výtah**. |
    | **Název** | Zadejte **výtah 3**|
    | **Popis** | Zadat **instanci výtahu 3** |
    | **Hierarchie** | Vybrat **hierarchii umístění** |
    | **Země** | Zadejte **USA** |
    | **Město** | Zadejte **New York** |
    | **Sestavování** | Zadejte **Empire State Building** |

    [![Zobrazení aktualizovaných instancí.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Přejděte zpět na kartu **Analyzovat** a zobrazte podokno grafů. V části **Hierarchie umístění**rozbalte všechny úrovně hierarchie a zobrazte instance časových řad:

    [![Zobrazení všech hierarchií v zobrazení grafu](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. V části **Pacific Science Center**vyberte možnost **Výškovka**instance časové řady 2 a pak vyberte **Zobrazit průměrnou teplotu**.

1. Pro stejnou **instanci, Výtah 2**, vyberte **Zobrazit podlahu**.

    Pomocí kategorické proměnné můžete určit, kolik času výtah strávil v horním, dolním a středním patře.

    [![Vizualizujte výtah 2 s hierarchií a daty.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili kurz, vyčistěte prostředky, které jste vytvořili:

1. V levé nabídce na [webu Azure Portal](https://portal.azure.com)vyberte Všechny **prostředky**, vyhledejte skupinu prostředků Azure Time Series Insights.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) výběrem **možnosti Odstranit** nebo odebrat jednotlivé prostředky jednotlivě.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:  

* Vytvořte a použijte akcelerátor simulace zařízení.
* Vytvořte prostředí Azure Time Series Insights Preview PAYG.
* Připojte prostředí Azure Time Series Insights Preview k iot hubu.
* Spusťte ukázku akcelerátoru řešení pro streamování dat do prostředí Azure Time Series Insights Preview.
* Proveďte základní analýzu dat.
* Definujte typ a hierarchii modelu časové řady a přidružte je k instancím.

Teď, když víte, jak vytvořit vlastní prostředí Azure Time Series Insights Preview, přečtěte si další informace o klíčových konceptech v Azure Time Series Insights.

Přečtěte si o konfiguraci úložiště Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Úložiště a příchozí přenos dat přehledů Azure Time Series](./time-series-insights-update-storage-ingress.md)

Další informace o modelech časových řad:

> [!div class="nextstepaction"]
> [Modelování dat Azure Time Series Insights Preview](./time-series-insights-update-tsm.md)
