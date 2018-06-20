---
title: Definování nového typu zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Tento kurz vám jako tvůrci ukáže, jak definovat nový typ zařízení v aplikaci Azure IoT Central. Pro váš typ definujete telemetrická data, stav, vlastnosti a nastavení.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 71ccae1951020a522fbbdddcdce0bbeeea5f1fb9
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235786"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Kurz: Definování nového typu zařízení v aplikaci Azure IoT Central

Tento kurz vám jako tvůrci ukáže, jak pomocí šablony zařízení definovat nový typ zařízení v aplikaci Microsoft Azure IoT Central. Šablona zařízení definuje telemetrická data, stav, vlastnosti a nastavení pro váš typ zařízení.

Aby bylo možné otestovat aplikaci před připojením skutečného zařízení, Azure IoT Central při vytvoření šablony zařízení vygeneruje simulované zařízení.

V tomto kurzu vytvoříte šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace). Připojené klimatizační zařízení:

* Odesílá telemetrická data, jako je teplota a vlhkost.
* Hlásí stav, třeba jestli je vypnuté nebo zapnuté.
* Má vlastnosti, jako je sériové číslo a verze firmwaru.
* Má nastavení, jako je cílová teplota a rychlost ventilátoru.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nové šablony zařízení
> * Přidání telemetrických dat do zařízení
> * Zobrazení simulovaných telemetrických dat
> * Definování měření událostí
> * Zobrazení simulovaných událostí
> * Definování měření stavu
> * Zobrazení simulovaného stavu
> * Použití vlastností zařízení
> * Použití nastavení zařízení

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central. Pokud jste dokončili rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md), můžete znovu využít aplikaci, kterou jste v tomto rychlém startu vytvořili. Jinak použijte následující postup a vytvořte prázdnou aplikaci Azure IoT Central:

1. Přejděte na stránku [správce aplikací](https://aka.ms/iotcentral) Azure IoT Central.

2. Zadejte e-mailovou adresu a heslo, které používáte pro přístup k vašemu předplatnému Azure:

   ![Zadání účtu organizace](media/tutorial-define-device-type/sign-in.png)

3. Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, zvolte **New Application** (Nová aplikace):

    ![Stránka správce aplikací Azure IoT Central](media/tutorial-define-device-type/iotcentralhome.png)

4. Vytvoření nové aplikace Azure IoT Central:

    * Zvolte popisný název, jako je třeba **Klimatizace Contoso**. Azure IoT Central pro vás vygeneruje jedinečnou předponu URL. Tuto předponu URL můžete změnit, aby byla snáze zapamatovatelná.
    * Zvolte předplatné Azure a Azure Active Directory, které chcete použít. Další informace o adresářích a předplatných najdete v tématu [Vytvoření aplikace Azure IoT Central](howto-create-application.md).
    * Buď použijte existující skupinu prostředků, nebo vytvořte novou skupinu prostředků s názvem podle vašeho výběru. Příklad: **contoso-rg**.
    * Vyberte oblast geograficky nejblíž k vám.
    * Zvolte aplikační šablonu **Custom Application** (Vlastní aplikace).
    * Zvolte platební plán **Free 30 Day Trial Application**.
    * Zvolte **Vytvořit**.

    ![Stránka vytvoření aplikace Azure IoT Central](media/tutorial-define-device-type/iotcentralcreate.png)

Další informace najdete v tématu popisujícím [postup vytvoření aplikace Azure IoT Central](howto-create-application.md).

## <a name="create-a-new-custom-device-template"></a>Vytvoření nové vlastní šablony zařízení

Jako tvůrce můžete ve vaší aplikaci vytvářet a upravovat šablony zařízení. Když vytvoříte šablonu zařízení, Azure IoT Central z této šablony vygeneruje simulované zařízení. Toto simulované zařízení generuje telemetrická data, která umožňují otestovat chování vaší aplikace, než připojíte fyzické zařízení.

Pokud chcete do vaší aplikace přidat novou šablonu zařízení, musíte přejít na stránku **Application Builder** (Tvůrce aplikací). K tomuto účelu zvolte **Application builder** (Tvůrce aplikací) v levé navigační nabídce:

    ![Application Builder page](media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Přidání zařízení a definování telemetrických dat

Následující kroky ukazují, jak vytvořit novou šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace) pro zařízení, která do vaší aplikace odesílají teplotní telemetrická data:

1. Na stránce **Application Builder** (Tvůrce zařízení) zvolte **Create Device Template** (Vytvořit šablonu zařízení):

    ![Stránka Application Builder (Tvůrce aplikací), vytvoření šablony zařízení](media/tutorial-define-device-type/builderhomedevices.png)

2. Na stránce **Device Templates** (Šablony zařízení) zvolte **Custom** (Vlastní). **Vlastní** šablona zařízení umožňuje definovat všechny charakteristiky a chování připojené klimatizace:

    ![Zařízení](media/tutorial-define-device-type/builderhomedevicescustom.png)

3. Na stránce **New Device Template** (Nová šablona zařízení) jako název vašeho zařízení zadejte **Connected Air Conditioner** (Připojená klimatizace) a potom zvolte **Create** (Vytvořit). Můžete také nahrát obrázek zařízení, který vidí operátoři v Device Exploreru:

    ![Vlastní zařízení](media/tutorial-define-device-type/createcustomdevice.png)

4. V šabloně zařízení **Connected Air Conditioner** (Připojená klimatizace) zkontrolujte, že při definování telemetrických dat jste na stránce **Measurements** (Měření). Každá šablona zařízení, kterou definujete, má samostatné stránky, které umožňují:

    * Zadat měření odesílaná zařízením, jako jsou telemetrická data, události a stav
    * Definovat nastavení používaná k ovládání příslušného zařízení
    * Definovat vlastnosti používané k zaznamenávání informací o zařízení
    * Definovat pravidla přidružená k zařízení
    * Přizpůsobit řídicí panel zařízení pro vaše operátory

    ![Měření klimatizace](media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Pokud chcete změnit název zařízení nebo šablony zařízení, klikněte na text v horní části stránky.

5. Pokud chcete přidat měření telemetrických teplotních dat, zvolte **New Measurement** (Nové měření). Potom jako typ měření zvolte **Telemetry** (Telemetrická data):

    ![Měření připojené klimatizace](media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Každý typ telemetrických dat, který definujete pro šablonu zařízení, zahrnuje [možnosti konfigurace](howto-set-up-template.md), jako jsou:

    * Možnosti zobrazení
    * Podrobnosti o telemetrických datech
    * Parametry simulace

    Ke konfiguraci telemetrických dat **Temperature** (Teplota) použijte informace v následující tabulce:

    | Nastavení              | Hodnota         |
    | -------------------- | -----------   |
    | Zobrazovaný název         | Teplota   |
    | Název pole           | Teplota   |
    | Jednotky                | F             |
    | Minimum                  | 60            |
    | Maximum                  | 110           |
    | Desetinná místa       | 0             |

    Můžete také zvolit barvu pro zobrazení telemetrických dat. Pokud chcete definici telemetrických dat uložit, zvolte **Save** (Uložit):

    ![Konfigurace simulace teploty](media/tutorial-define-device-type/temperaturesimulation.png)

7. Po chvíli se na stránce **Measurements** (Měření) zobrazí graf teplotních telemetrických dat z vašeho simulovaného připojeného klimatizačního zařízení. Pomocí ovládacích prvků můžete spravovat viditelnost a agregaci nebo můžete upravit definici telemetrických dat:

    ![Zobrazení simulace teploty](media/tutorial-define-device-type/viewsimulation.png)

8. K přizpůsobení grafu se také dají využít ovládací prvky **Line** (Čárový), **Stacked** (Skládaný) a **Edit Time Range** (Upravit časový rozsah):

    ![Přizpůsobení grafu](media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Definování měření událostí

Událost můžete využít k definování dat v určitém časovém bodu, která se zařízení odesílají jako indikátor něčeho důležitého, například chyby nebo selhání komponenty. Podobně jako u měření telemetrických dat může Azure IoT Central simulovat události zařízení, aby bylo možné otestovat chování aplikace před připojením skutečného zařízení. K definování měření událostí pro příslušný typ zařízení se použije zobrazení **Measurements** (Měření).

1. Pokud chcete přidat měření události **Fan Motor Error** (Chyba motoru ventilátoru), zvolte **New Measurement** (Nové měření). Potom jako typ měření zvolte **Event** (Událost):

    ![Měření připojené klimatizace](media/tutorial-define-device-type/eventnew.png)

2. Každý typ události, který definujete pro šablonu zařízení, zahrnuje [možnosti konfigurace](howto-set-up-template.md), jako jsou:

    * Zobrazovaný název
    * Název pole
    * Závažnost

    Ke konfiguraci události **Fan Motor Error** (Chyba motoru ventilátoru) použijte informace v následující tabulce:

    | Nastavení              | Hodnota             |
    | -------------------- | -----------       |
    | Zobrazovaný název         | Chyba motoru ventilátoru   |
    | Název pole           | fanmotorerr       |
    | Severity             | Chyba             |

    Pokud chcete definici události uložit, zvolte **Save** (Uložit):

    ![Konfigurace měření událostí](media/tutorial-define-device-type/eventconfiguration.png)

3. Po chvíli se na stránce **Measurements** (Měření) zobrazí graf událostí náhodně vygenerovaných z vašeho simulovaného připojeného klimatizačního zařízení. Pomocí ovládacích prvků můžete spravovat viditelnost nebo můžete upravit definici události:

    ![Zobrazení simulace události](media/tutorial-define-device-type/eventview.png)

1. Pokud chcete o události zobrazit další podrobnosti, klikněte na ni v grafu:

    ![Zobrazení podrobnosti události](media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>Definování měření stavu

Pomocí měření stavu můžete definovat a vizualizovat stav zařízení nebo jeho komponent za časové období. Podobně jako u měření telemetrických dat může Azure IoT Central simulovat stav zařízení, aby bylo možné otestovat chování aplikace před připojením skutečného zařízení. K definování měření stavu pro příslušný typ zařízení se použije zobrazení **Measurements** (Měření).

1. Pokud chcete přidat měření **Fan Mode** (Režim ventilátoru), zvolte **New Measurement** (Nové měření). Potom jako typ měření zvolte **State** (Stav):

    ![Měření stavu připojené klimatizace](media/tutorial-define-device-type/statenew.png)

2. Každý typ stavu, který definujete pro šablonu zařízení, zahrnuje [možnosti konfigurace](howto-set-up-template.md), jako jsou:

    * Zobrazovaný název
    * Název pole
    * Hodnoty s volitelnými popisky
    * Barva pro jednotlivé hodnoty

    Ke konfiguraci **Fan Mode** (Režim ventilátoru) použijte informace v následující tabulce:

    | Nastavení              | Hodnota             |
    | -------------------- | -----------       |
    | Zobrazovaný název         | Režim ventilátoru          |
    | Název pole           | fanmode           |
    | Hodnota                | 1                 |
    | Popisek        | Funguje         |
    | Hodnota                | 0                 |
    | Popisek        | Zastaveno           |

    Pokud chcete definici stavu uložit, zvolte **Save** (Uložit):

    ![Konfigurace měření stavu](media/tutorial-define-device-type/stateconfiguration.png)

3. Po chvíli se na stránce **Measurements** (Měření) zobrazí graf stavů náhodně vygenerovaných z vašeho simulovaného připojeného klimatizačního zařízení. Pomocí ovládacích prvků můžete spravovat viditelnost nebo můžete upravit definici stavu:

    ![Zobrazení simulace stavu](media/tutorial-define-device-type/stateview.png)

4. V případě, že zařízení v krátkém čase pošle příliš mnoho datových bodů, měření stavu se zobrazí s jiným vizuálem, jak je uvedeno níže. Pokud kliknete na graf, zobrazí se v chronologickém pořadí všechny datové body v daném časovém období. Časový rozsah můžete také zúžit a projít si měření znázorněná v grafu.

    ![Zobrazení podrobností o stavu](media/tutorial-define-device-type/stateviewdetail.png)

## <a name="properties-device-properties-and-settings"></a>Vlastnosti, vlastnosti zařízení a nastavení

Vlastnosti, vlastnosti zařízení a nastavení jsou různé hodnoty definované v šabloně zařízení a přidružené k jednotlivým zařízením:

* _Nastavení_ použijete k odeslání konfiguračních dat ze zařízení do aplikace. Operátor může například použít nastavení, pokud chce změnit interval telemetrie zařízení ze dvou na pět sekund. Když operátor změní nastavení, bude toto nastavení v uživatelském rozhraní označené jako čekající, dokud zařízení nepotvrdí, že realizovalo změnu nastavení.

* _Vlastnosti_ použijete k zaznamenání informací o zařízení ve vaší aplikaci. Vlastnosti můžete použít například k zaznamenání sériového čísla zařízení nebo telefonního čísla výrobce zařízení. Vlastnosti se ukládají v aplikaci a nesynchronizují se se zařízením. Operátor může vlastnostem přiřadit hodnotu.

* _Vlastnosti zařízení_ použijete k tomu, abyste zařízení umožnili odeslat hodnoty vlastností do vaší aplikace. Tyto vlastnosti může měnit jenom zařízení. Pro operátora jsou vlastnosti zařízení jen pro čtení.

## <a name="use-settings"></a>Použití nastavení

*Nastavení* použijete k tomu, abyste operátorovi umožnili odeslání konfiguračních dat do zařízení. V této části do šablony zařízení **Connected Air Conditioner** přidáte nastavení, které operátorovi umožňuje nastavit cílovou teplotu připojené klimatizace.

1. Přejděte ke stránce **Settings** (Nastavení) vaší šablony **Connected Air Conditioner**:

    ![Příprava pro přidání nastavení](media/tutorial-define-device-type/deviceaddsetting.png)

    Můžete vytvořit nastavení různých typů, například čísla nebo text.

2. Pokud chcete k zařízení přidat číselné nastavení, zvolte **Number**.

3. Ke konfiguraci nastavení **Set Temperature** (Nastavená teplota) použijte informace v následující tabulce:

    | Pole                | Hodnota           |
    | -------------------- | -----------     |
    | Zobrazovaný název         | Nastavená teplota |
    | Název pole           | setTemperature  |
    | Jednotka měření  | F               |
    | Desetinná místa       | 1               |
    | Minimální hodnota        | 20              |
    | Maximální hodnota        | 200             |
    | Počáteční hodnota        | 80              |
    | Popis          | Nastavení cílové teploty pro klimatizaci |

    Potom zvolte **Save** (Uložit):

    ![Konfigurace nastavení Set Temperature (Nastavená teplota)](media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Jakmile zařízení rozpozná změnu nastavení, stav nastavení se změní na **synced** (Synchronizováno).

4. Rozložení stránky **Settings** (Nastavení) můžete přizpůsobit přesunutím dlaždic nastavení a změnou jejich velikosti:

    ![Přizpůsobení rozložení nastavení](media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Použití vlastností

*Vlastnosti* použijete k uložení informací o vašem zařízení v aplikaci. V této části do šablony zařízení **Connected Air Conditioner** přidáte vlastnosti pro uložení sériového čísla a verze firmwaru jednotlivých zařízení.

1. Přejděte ke stránce **Properties** (Vlastnosti) vaší šablony **Connected Air Conditioner**:

    ![Příprava pro přidání vlastnosti](media/tutorial-define-device-type/deviceaddproperty.png)

    Můžete vytvořit vlastnosti různých typů, například čísla nebo text. Pokud chcete do šablony zařízení přidat vlastnost sériového čísla, zvolte **Text**.

2. Ke konfiguraci vlastnosti sériového čísla použijte informace v následující tabulce:

    | Pole                | Hodnota                |
    | -------------------- | -------------------- |
    | Zobrazovaný název         | Sériové číslo        |
    | Název pole           | serialNumber         |
    | Počáteční hodnota        | cac00001             |
    | Popis          | Sériové číslo zařízení |

    Ostatní pole nechte nastavená na výchozí hodnoty.

    ![Konfigurace vlastností zařízení](media/tutorial-define-device-type/configureproperties.png)

    Potom zvolte **Save** (Uložit).

3. Pokud chcete do šablony zařízení přidat vlastnost verze firmwaru, zvolte **Text**.

4. Ke konfiguraci vlastnosti verze firmwaru použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Verze firmwaru        |
    | Název pole           | firmwareVersion         |
    | Počáteční hodnota        | 0.1                     |
    | Popis          | Verze firmwaru zařízení |

    ![Konfigurace vlastností zařízení](media/tutorial-define-device-type/configureproperties2.png)

    Potom zvolte **Save** (Uložit).

5. Rozložení stránky **Properties** (Vlastnosti) můžete přizpůsobit přesunutím dlaždic vlastností a změnou jejich velikosti:

    ![Přizpůsobení rozložení vlastností](media/tutorial-define-device-type/propertieslayout.png)

## <a name="view-your-simulated-device"></a>Zobrazení simulovaného zařízení

Teď máte šablonu **Connected Air Conditioner** definovanou a můžete přizpůsobit její **řídicí panel**, aby zahrnoval měření, nastavení a vlastnosti, které jste definovali. Potom si můžete zobrazit náhled řídicího panelu jako operátor:

1. Zvolte stránku **Dashboard** (Řídicí panel) vaší šablony **Connected Air Conditioner**:

    ![Řídicí panely připojeného klimatizačního zařízení](media/tutorial-define-device-type/aircondashboards.png)

2. Zvolte **Line Chart** (Čárový graf) a přidejte tuto komponentu na **řídicí panel**:

    ![Komponenty řídicího panelu](media/tutorial-define-device-type/dashboardcomponents1.png)

3. Ke konfiguraci komponenty **Line Chart** (Čárový graf) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Teplota |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření | Teplota (zvolte **Visibility** (Viditelnost) vedle **temperature**) |

    ![Nastavení čárového grafu](media/tutorial-define-device-type/linechartsettings.png)

    Potom zvolte **Save** (Uložit).

4. Ke konfiguraci komponenty **Event Chart** (Graf událostí) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Události |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření | Chyba motoru ventilátoru (zvolte **Visibility** (Viditelnost) vedle **Fan Motor Error**) |

    ![Nastavení čárového grafu](media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Potom zvolte **Save** (Uložit).

5. Ke konfiguraci komponenty **State Chart** (Graf stavu) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Režim ventilátoru |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření | Režim ventilátoru (zvolte **Visibility** (Viditelnost) vedle **Fan Mode**) |

    ![Nastavení čárového grafu](media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Potom zvolte **Save** (Uložit).

6. Pokud chcete přidat nastavení teploty na řídicí panel, zvolte **Settings and Properties** (Nastavení a vlastnosti):

    ![Komponenty řídicího panelu](media/tutorial-define-device-type/dashboardcomponents4.png)

7. Ke konfiguraci komponenty **Settings and Properties** (Nastavení a vlastnosti) použijte informace v následující tabulce:

    | Nastavení                 | Hodnota         |
    | ----------------------- | ------------- |
    | Název                   | Nastavení cílové teploty |
    | Nastavení a vlastností | Nastavená teplota |

    ![Nastavení vlastnosti sériového čísla](media/tutorial-define-device-type/propertysettings3.png)

    Potom zvolte **Save** (Uložit).

8. Pokud chcete přidat sériové číslo zařízení na řídicí panel, zvolte **Settings and Properties** (Nastavení a vlastnosti):

    ![Komponenty řídicího panelu](media/tutorial-define-device-type/dashboardcomponents3.png)

9. Ke konfiguraci komponenty **Settings and Properties** (Nastavení a vlastnosti) použijte informace v následující tabulce:

    | Nastavení                 | Hodnota         |
    | ----------------------- | ------------- |
    | Název                   | Sériové číslo |
    | Nastavení a vlastností | Sériové číslo |

    ![Nastavení vlastnosti sériového čísla](media/tutorial-define-device-type/propertysettings1.png)

    Potom zvolte **Save** (Uložit).

10. Pokud chcete přidat verzi firmwaru zařízení na řídicí panel, zvolte **Settings and Properties** (Nastavení a vlastnosti):

    ![Komponenty řídicího panelu](media/tutorial-define-device-type/dashboardcomponents4.png)

11. Ke konfiguraci komponenty **Settings and Properties** (Nastavení a vlastnosti) použijte informace v následující tabulce:

    | Nastavení                 | Hodnota            |
    | ----------------------- | ---------------- |
    | Název                   | Verze firmwaru |
    | Nastavení a vlastností | Verze firmwaru |

    ![Nastavení vlastnosti sériového čísla](media/tutorial-define-device-type/propertysettings2.png)

    Potom zvolte **Save** (Uložit).

12. Pokud chcete řídicí panel zobrazit jako operátor, vypněte **Design Mode** (Režim návrhu) v pravé horní části stránky.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Vytvoření nové šablony zařízení
> * Přidání telemetrických dat do zařízení
> * Zobrazení simulovaných telemetrických dat
> * Definování události zařízení
> * Zobrazení simulovaných událostí
> * Definování stavu
> * Zobrazení simulovaného stavu
> * Použití vlastností zařízení
> * Použití nastavení zařízení

Teď máte definovanou šablonu zařízení v aplikaci Azure IoT Central a můžete přejít k dalším navrhovaným krokům:

* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md)
* [Přizpůsobení zobrazení operátora](tutorial-customize-operator.md)