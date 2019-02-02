---
title: Definování nového typu zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Tento kurz vám jako tvůrci ukáže, jak definovat nový typ zařízení v aplikaci Azure IoT Central. Pro váš typ definujete telemetrická data, stav, vlastnosti a nastavení.
author: dominicbetts
ms.author: dobett
ms.date: 10/30/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 1ed1790d9fe1cdaa8d00b45e0684531984906c7f
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55661815"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Kurz: Definujte nový typ zařízení v aplikaci Azure IoT Central

Tento kurz vám jako tvůrci ukáže, jak pomocí šablony zařízení definovat nový typ zařízení v aplikaci Microsoft Azure IoT Central. Šablona zařízení definuje telemetrická data, stav, vlastnosti a nastavení pro váš typ zařízení.

Aby bylo možné otestovat aplikaci před připojením skutečného zařízení, IoT Central při vytváření šablony zařízení vygeneruje simulované zařízení.

V tomto kurzu vytvoříte šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace). Připojené klimatizační zařízení:

* Odesílá telemetrická data, jako je teplota a vlhkost.
* Hlásí stav, třeba jestli je vypnuté nebo zapnuté.
* Má vlastnosti zařízení, jako je sériové číslo a verze firmwaru.
* Má nastavení, jako je cílová teplota.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nové šablony zařízení
> * Přidání telemetrických dat do zařízení
> * Zobrazení simulovaných telemetrických dat
> * Definování měření událostí
> * Zobrazení simulovaných událostí
> * Definování měření stavu
> * Zobrazení simulovaného stavu
> * Použití nastavení a vlastností
> * Použití příkazů
> * Zobrazení simulovaného zařízení na řídicím panelu

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central. Pokud jste dokončili rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md), můžete znovu využít aplikaci, kterou jste v tomto rychlém startu vytvořili. Jinak použijte následující postup a vytvořte prázdnou aplikaci Azure IoT Central:

1. Přejděte na stránku [správce aplikací](https://aka.ms/iotcentral) Azure IoT Central.

2. Zadejte e-mailovou adresu a heslo, které používáte pro přístup k vašemu předplatnému Azure:

   ![Zadání účtu organizace](./media/tutorial-define-device-type/sign-in.png)

3. Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, zvolte **New Application** (Nová aplikace):

    ![Stránka správce aplikací Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Vytvoření nové aplikace Azure IoT Central:
    
    * Zvolte **Trial** (Zkušební verze). K vytvoření zkušební verze aplikace nepotřebujete předplatné Azure.
    
       Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
    
    * Zvolte **Vlastní aplikace**.
    
    * Volitelně můžete zvolit popisný název aplikace, jako je třeba **Klimatizace Contoso**. Azure IoT Central pro vás vygeneruje jedinečnou předponu URL. Tuto předponu URL můžete změnit, aby byla snáze zapamatovatelná.
    
    * Zvolte **Vytvořit**.

    ![Stránka vytvoření aplikace Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

    Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).

## <a name="create-a-new-custom-device-template"></a>Vytvoření nové vlastní šablony zařízení

Jako tvůrce můžete ve vaší aplikaci vytvářet a upravovat šablony zařízení. Když vytvoříte šablonu zařízení, Azure IoT Central z této šablony vygeneruje simulované zařízení. Simulované zařízení generuje telemetrická data, která umožňuje testovat chování aplikace před připojit skutečné zařízení.

Pokud chcete do vaší aplikace přidat novou šablonu zařízení, musíte přejít na stránku **Application Builder** (Tvůrce aplikací). K tomuto účelu zvolte **Application builder** (Tvůrce aplikací) v levé navigační nabídce.

![Stránka Application Builder (Tvůrce aplikací)](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Přidání zařízení a definování telemetrických dat

Následující kroky ukazují, jak vytvořit novou šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace) pro zařízení, která do vaší aplikace odesílají teplotní telemetrická data:

1. Na stránce **Application Builder** (Tvůrce zařízení) zvolte **Create Device Template** (Vytvořit šablonu zařízení):

    ![Stránka Application Builder (Tvůrce aplikací), vytvoření šablony zařízení](./media/tutorial-define-device-type/builderhomedevices.png)

2. Na stránce **Device Templates** (Šablony zařízení) zvolte **Custom** (Vlastní). **Vlastní** šablona zařízení umožňuje definovat všechny charakteristiky a chování připojené klimatizace:

    ![Zařízení](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. Na stránce **New Device Template** (Nová šablona zařízení) jako název vašeho zařízení zadejte **Connected Air Conditioner** (Připojená klimatizace) a potom zvolte **Create** (Vytvořit). Můžete také nahrát obrázek zařízení, který vidí operátoři v Device Exploreru:

    ![Vlastní zařízení](./media/tutorial-define-device-type/createcustomdevice.png)

4. V šabloně zařízení **Connected Air Conditioner** (Připojená klimatizace) zkontrolujte, že při definování telemetrických dat jste na stránce **Measurements** (Měření). Každá šablona zařízení, kterou definujete, má samostatné stránky, které umožňují:

    * Zadat měření odesílaná zařízením, jako jsou telemetrická data, události a stav
    
    * Definovat nastavení používaná k ovládání příslušného zařízení
    
    * Definovat vlastnosti, což jsou metadata zařízení.

    * Definovat příkazy, které se mají spustit přímo na zařízení.
    
    * Definovat pravidla přidružená k zařízení
    
    * Přizpůsobit řídicí panel zařízení pro vaše operátory

    Vždy, když definujete šablonu zařízení, nejprve zvolte **Edit Template** (Upravit šablonu), abyste mohli šablonu upravit. Jakmile budete hotovi, zvolte **Done** (Hotovo). 

    ![Měření klimatizace](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Pokud chcete změnit název zařízení nebo šablony zařízení, klikněte na text v horní části stránky.

5. Pokud chcete přidat měření telemetrických teplotních dat, zvolte **New Measurement** (Nové měření). Potom jako typ měření zvolte **Telemetry** (Telemetrická data):

    ![Měření připojené klimatizace](./media/tutorial-define-device-type/airconmeasurementsnew.png)

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

    ![Konfigurace simulace teploty](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Po chvíli se na stránce **Measurements** (Měření) zobrazí graf teplotních telemetrických dat z vašeho simulovaného připojeného klimatizačního zařízení. Pomocí ovládacích prvků můžete spravovat viditelnost a agregaci nebo můžete upravit definici telemetrických dat:

    ![Zobrazení simulace teploty](./media/tutorial-define-device-type/viewsimulation.png)

8. K přizpůsobení grafu se také dají využít ovládací prvky **Line** (Čárový), **Stacked** (Skládaný) a **Edit Time Range** (Upravit časový rozsah):

    ![Přizpůsobení grafu](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Definování měření událostí

Událost můžete využít k definování dat v určitém časovém bodu, která se zařízení odesílají jako indikátor něčeho důležitého, například chyby nebo selhání komponenty. Stejně jako měření telemetrická data Azure IoT Central můžete simulovat zařízení událostí umožňuje testovat chování aplikace před připojit skutečné zařízení. K definování měření událostí pro příslušný typ zařízení se použije zobrazení **Measurements** (Měření).

1. Pokud chcete přidat měření události **Fan Motor Error** (Chyba motoru ventilátoru), zvolte **New Measurement** (Nové měření). Potom jako typ měření zvolte **Event** (Událost):

    ![Měření připojené klimatizace](./media/tutorial-define-device-type/eventnew.png)

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

    ![Konfigurace měření událostí](./media/tutorial-define-device-type/eventconfiguration.png)

3. Po chvíli se na stránce **Measurements** (Měření) zobrazí graf událostí náhodně vygenerovaných z vašeho simulovaného připojeného klimatizačního zařízení. Pomocí ovládacích prvků můžete spravovat viditelnost nebo můžete upravit definici události:

    ![Zobrazení simulace události](./media/tutorial-define-device-type/eventview.png)

1. Pokud chcete o události zobrazit další podrobnosti, klikněte na ni v grafu:

    ![Zobrazení podrobnosti události](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>Definování měření stavu

Pomocí měření stavu můžete definovat a vizualizovat stav zařízení nebo jeho komponent za časové období. Stejně jako měření telemetrická data Azure IoT Central můžete simulovat stavu zařízení umožňuje testovat chování aplikace před připojit skutečné zařízení. K definování měření stavu pro příslušný typ zařízení se použije zobrazení **Measurements** (Měření).

1. Pokud chcete přidat měření **Fan Mode** (Režim ventilátoru), zvolte **New Measurement** (Nové měření). Potom jako typ měření zvolte **State** (Stav):

    ![Měření stavu připojené klimatizace](./media/tutorial-define-device-type/statenew.png)

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

    ![Konfigurace měření stavu](./media/tutorial-define-device-type/stateconfiguration.png)

3. Po chvíli se na stránce **Measurements** (Měření) zobrazí graf stavů náhodně vygenerovaných z vašeho simulovaného připojeného klimatizačního zařízení. Pomocí ovládacích prvků můžete spravovat viditelnost nebo můžete upravit definici stavu:

    ![Zobrazení simulace stavu](./media/tutorial-define-device-type/stateview.png)

4. V případě, že zařízení v krátkém čase pošle příliš mnoho datových bodů, měření stavu se zobrazí s jiným vizuálem, jak je uvedeno níže. Pokud kliknete na graf, zobrazí se v chronologickém pořadí všechny datové body v daném časovém období. Časový rozsah můžete také zúžit a zobrazit podrobnější měření.

    ![Zobrazení podrobností o stavu](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>Nastavení, vlastnosti a příkazy

Nastavení, vlastnosti a příkazy jsou různé hodnoty definované v šabloně zařízení a přidružené k jednotlivým zařízením:

* _Nastavení_ použijete k odeslání konfiguračních dat ze zařízení do aplikace. Operátor může například použít nastavení, pokud chce změnit interval telemetrie zařízení ze dvou na pět sekund. Když operátor změní nastavení, bude toto nastavení v uživatelském rozhraní označené jako čekající, dokud zařízení nepotvrdí, že realizovalo změnu nastavení.

* _Vlastnosti_ použijete k definování metadat přidružených k vašemu zařízení. Vlastnosti se dělí do dvou kategorií:
    
    * _Vlastnosti aplikace_ použijete k zaznamenání informací o zařízení ve vaší aplikaci. Vlastnosti aplikace můžete použít například k zaznamenání polohy zařízení a data jeho poslední údržby. Tyto vlastnosti se ukládají v aplikaci a nesynchronizují se se zařízením. Operátor může vlastnostem přiřadit hodnotu.

    * _Vlastnosti zařízení_ použijete k tomu, abyste zařízení umožnili odeslat hodnoty vlastností do vaší aplikace. Tyto vlastnosti může měnit jenom zařízení. Pro operátora jsou vlastnosti zařízení jen pro čtení. V tomto scénáři připojené klimatizace jsou verze firmwaru a sériové číslo zařízení vlastnosti zařízení odesílané zařízením. 
    
    Další informace najdete v části [Vlastnosti][lnk-define-template] v praktickém průvodci nastavením šablony zařízení.

* _Příkazy_ použijete ke vzdálené správě zařízení z aplikace. Příkazy můžete spustit přímo na zařízení z cloudu a zařízení tak ovládat. Operátor například může spouštět příkazy, jako je třeba příkaz restartování, a okamžitě zařízení restartovat.

## <a name="use-settings"></a>Použití nastavení

*Nastavení* použijete k tomu, abyste operátorovi umožnili odeslání konfiguračních dat do zařízení. V této části do šablony zařízení **Connected Air Conditioner** přidáte nastavení, které operátorovi umožňuje nastavit cílovou teplotu připojené klimatizace.

1. Přejděte ke stránce **Settings** (Nastavení) vaší šablony **Connected Air Conditioner**:

    ![Příprava pro přidání nastavení](./media/tutorial-define-device-type/deviceaddsetting.png)

    Můžete vytvořit nastavení různých typů, například čísla nebo text.

2. Pokud chcete k zařízení přidat číselné nastavení, zvolte **Number**.

3. Ke konfiguraci nastavení **Set Temperature** (Nastavená teplota) použijte informace v následující tabulce:

    | Pole                | Hodnota           |
    | -------------------- | -----------     |
    | Zobrazovaný název         | Nastavená teplota |
    | Název pole           | setTemperature  |
    | Jednotka měření      | F               |
    | Desetinná místa       | 1               |
    | Minimální hodnota        | 20              |
    | Maximální hodnota        | 200             |
    | Počáteční hodnota        | 80              |
    | Popis          | Nastavení cílové teploty pro klimatizaci |

    Potom zvolte **Save** (Uložit):

    ![Konfigurace nastavení Set Temperature (Nastavená teplota)](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Jakmile zařízení rozpozná změnu nastavení, stav nastavení se změní na **synced** (Synchronizováno).

4. Rozložení stránky **Settings** (Nastavení) můžete přizpůsobit přesunutím dlaždic nastavení a změnou jejich velikosti:

    ![Přizpůsobení rozložení nastavení](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Použití vlastností 

*Vlastnosti aplikace* použijete k uložení informací o vašem zařízení v aplikaci. V této části do šablony zařízení **Connected Air Conditioner** (Připojená klimatizace) přidáte vlastnosti aplikace pro uložení polohy zařízení a data jeho poslední údržby. Všimněte si, že obě tyto vlastnosti zařízení jsou upravitelné. Zařízení odesílá i vlastnosti zařízení jen pro čtení, které nelze měnit, jako je sériové číslo a verze firmwaru zařízení.
 
1. Přejděte ke stránce **Properties** (Vlastnosti) vaší šablony **Connected Air Conditioner**:

    ![Příprava pro přidání vlastnosti](./media/tutorial-define-device-type/deviceaddproperty.png)

    Můžete vytvořit vlastnosti zařízení různých typů, například čísla nebo text. Pokud chcete do šablony zařízení přidat vlastnost umístění, zvolte **Location** (Umístění).

1. Ke konfiguraci vlastnosti umístění použijte informace v následující tabulce:

    | Pole                | Hodnota                |
    | -------------------- | -------------------- |
    | Zobrazovaný název         | Umístění             |
    | Název pole           | location             |
    | Počáteční hodnota        | Seattle, WA          |
    | Popis          | Device location (Umístění zařízení)      |

    Ostatní pole nechte nastavená na výchozí hodnoty.

    ![Konfigurace vlastností zařízení](./media/tutorial-define-device-type/configureproperties.png)

    Zvolte **Uložit**.

1. Pokud chcete do šablony zařízení přidat vlastnost data poslední údržby, zvolte **Date** (Datum).

1. Ke konfiguraci vlastnosti data poslední údržby použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Last Service Date (Datum poslední údržby)       |
    | Název pole           | serviceDate             |
    | Počáteční hodnota        | 1. 1. 2018                |
    | Popis          | Last serviced (Poslední údržba)           |

    ![Konfigurace vlastností zařízení](./media/tutorial-define-device-type/configureproperties2.png)

    Zvolte **Uložit**.

5. Rozložení stránky **Properties** (Vlastnosti) můžete přizpůsobit přesunutím dlaždic vlastností a změnou jejich velikosti:

    ![Přizpůsobení rozložení vlastností](./media/tutorial-define-device-type/propertieslayout.png)

1. Pokud chcete do šablony zařízení přidat vlastnost zařízení, jako je verze firmwaru, zvolte **Device Property** (Vlastnost zařízení).

1.  Ke konfiguraci verze firmwaru použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Verze firmwaru        |
    | Název pole           | firmwareVersion         |
    | Typ dat            | text                    |
    | Popis          | Verze firmwaru klimatizace |

    ![Konfigurace verze firmwaru](./media/tutorial-define-device-type/configureproperties3.png)
    
    Zvolte **Uložit**.

1. Pokud chcete do šablony zařízení přidat vlastnost zařízení, jako je sériové číslo, zvolte **Device Property** (Vlastnost zařízení).

1. Ke konfiguraci sériového čísla použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Sériové číslo           |
    | Název pole           | serialNumber            |
    | Typ dat            | text                    |
    | Popis          | Sériové číslo klimatizace  |

    ![Konfigurace sériového čísla](./media/tutorial-define-device-type/configureproperties4.png)
    
    Zvolte **Uložit**.
    
    > [!NOTE]
    > Vlastnost zařízení se odešle ze zařízení do aplikace. Hodnoty verze firmwaru a sériového čísla se aktualizují při připojení skutečného zařízení k IoT Central.

## <a name="use-commands"></a>Použití příkazů

_Příkazy_ použijete k tomu, abyste operátorovi umožnili spouštět příkazy přímo na zařízení. V této části přidáte do šablony zařízení **Connected Air Conditioner** příkaz, který operátorovi umožní vypsat na připojené klimatizaci určitou zprávu.

1. Přejděte na stránku **Commands** (Příkazy) šablony zařízení **Connected Air Conditioner** a upravte šablonu. 

1. Kliknutím na **New Command** (Nový příkaz) přidejte do zařízení nový příkaz a začněte ho konfigurovat.

   Na základě svých požadavků můžete vytvářet příkazy různých typů. 

1. Ke konfiguraci nového příkazu použijte informace v následující tabulce:

    | Pole                | Hodnota           |
    | -------------------- | -----------     |
    | Zobrazovaný název         | Příkaz pro zobrazení výsledků    |
    | Název pole           | echo            |
    | Výchozí časový limit      | 30              |
    | Zobrazovaný typ         | text            |
    | Popis          | Příkaz zařízení  |  

    Kliknutím na **+** v části **Input Fields** (Vstupní pole) můžete do příkazu přidat další vstupy.

    ![Příprava pro přidání nastavení](media/tutorial-define-device-type/commandsecho1.png)

     Zvolte **Uložit**.

1. Rozložení stránky **Commands** (Příkazy) můžete přizpůsobit přesunutím dlaždic příkazů a změnou jejich velikosti:

    ![Přizpůsobení rozložení nastavení](media/tutorial-define-device-type/commandstileresize1.png)

## <a name="view-your-simulated-device"></a>Zobrazení simulovaného zařízení

Teď máte šablonu **Connected Air Conditioner** definovanou a můžete přizpůsobit její **řídicí panel**, aby zahrnoval měření, nastavení a vlastnosti, které jste definovali. Potom si můžete zobrazit náhled řídicího panelu jako operátor:

1. Zvolte stránku **Dashboard** (Řídicí panel) vaší šablony **Connected Air Conditioner**:

    ![Řídicí panely připojeného klimatizačního zařízení](./media/tutorial-define-device-type/aircondashboards.png)

1. Zvolte **Line Chart** (Čárový graf) a přidejte tuto komponentu na **řídicí panel**:

    ![Komponenty řídicího panelu](./media/tutorial-define-device-type/dashboardcomponents1.png)

1. Ke konfiguraci komponenty **Line Chart** (Čárový graf) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Teplota |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření     | Teplota (zvolte **Visibility** (Viditelnost) vedle **temperature**) |

    ![Nastavení čárového grafu](./media/tutorial-define-device-type/linechartsettings.png)

    Potom zvolte **Save** (Uložit).

1. Ke konfiguraci komponenty **Event History** (Historie událostí) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Události |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření     | Chyba motoru ventilátoru (zvolte **Visibility** (Viditelnost) vedle **Fan Motor Error**) |

    ![Nastavení čárového grafu](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Potom zvolte **Save** (Uložit).

1. Ke konfiguraci komponenty **State History** (Historie stavu) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Režim ventilátoru |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření | Režim ventilátoru (zvolte **Visibility** (Viditelnost) vedle **Fan Mode**) |

    ![Nastavení čárového grafu](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Potom zvolte **Save** (Uložit).

1. Pokud chcete přidat nastavení teploty na řídicí panel, zvolte **Settings and Properties** (Nastavení a vlastnosti). Kliknutím na **Add/Remove** (Přidat nebo odebrat) přidejte nastavení nebo vlastnosti, které chcete zobrazit na řídicím panelu. 

    ![Komponenty řídicího panelu](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Ke konfiguraci komponenty **Settings and Properties** (Nastavení a vlastnosti) použijte informace v následující tabulce:

    | Nastavení                 | Hodnota         |
    | ----------------------- | ------------- |
    | Název                   | Nastavení cílové teploty |
    | Nastavení a vlastností | Nastavená teplota |

    V části Available Columns (Dostupné sloupce) se zobrazí nastavení a vlastnosti, které jste definovali dříve na stránkách Settings (Nastavení) a Properties (Vlastnosti). 

    ![Nastavení vlastností Set Temperature (Nastavená teplota)](./media/tutorial-define-device-type/propertysettings4.png)

    Pak zvolte **OK**.

1. Pokud chcete přidat sériové číslo zařízení na řídicí panel, zvolte **Settings and Properties** (Nastavení a vlastnosti):

    ![Komponenty řídicího panelu](./media/tutorial-define-device-type/dashboardcomponents3.png)

1. Ke konfiguraci komponenty **Settings and Properties** (Nastavení a vlastnosti) použijte informace v následující tabulce:

    | Nastavení                 | Hodnota         |
    | ----------------------- | ------------- |
    | Název                   | Sériové číslo |
    | Nastavení a vlastností | Sériové číslo |

    ![Nastavení vlastnosti sériového čísla](./media/tutorial-define-device-type/propertysettings5.png)

    Pak zvolte **OK**.

1. Pokud chcete přidat verzi firmwaru zařízení na řídicí panel, zvolte **Settings and Properties** (Nastavení a vlastnosti):

    ![Komponenty řídicího panelu](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Ke konfiguraci komponenty **Settings and Properties** (Nastavení a vlastnosti) použijte informace v následující tabulce:

    | Nastavení                 | Hodnota            |
    | ----------------------- | ---------------- |
    | Název                   | Verze firmwaru |
    | Nastavení a vlastností | Verze firmwaru |

    ![Nastavení vlastnosti sériového čísla](./media/tutorial-define-device-type/propertysettings6.png)

    Pak zvolte **OK**.

1. Pokud chcete řídicí panel zobrazit jako operátor, vypněte **Edit Template** (Upravit šablonu) v pravé horní části stránky.

## <a name="next-steps"></a>Další postup

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
> * Použití nastavení a vlastností
> * Použití příkazů
> * Zobrazení simulovaného zařízení na řídicím panelu

Teď máte definovanou šablonu zařízení v aplikaci Azure IoT Central a můžete přejít k dalším navrhovaným krokům:

* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md)
* [Přizpůsobení zobrazení operátora](tutorial-customize-operator.md)

[lnk-define-template]: /azure/iot-central/howto-set-up-template#properties