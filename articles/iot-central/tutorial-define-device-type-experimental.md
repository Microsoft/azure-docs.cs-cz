---
title: Definování nového typu zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Tento kurz vám jako tvůrci ukáže, jak definovat nový typ zařízení v aplikaci Azure IoT Central. Pro váš typ definujete telemetrická data, stav, vlastnosti a nastavení.
author: dominicbetts
ms.author: dobett
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 02f09363e9f9a014ceaf63f375c4e417f8c09232
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666407"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-new-ui-design"></a>Kurz: Definujte nový typ zařízení v aplikaci Azure IoT Central (návrh nové uživatelské rozhraní)

Tento kurz vám jako tvůrci ukáže, jak pomocí šablony zařízení definovat nový typ zařízení v aplikaci Microsoft Azure IoT Central. Šablona zařízení definuje telemetrická data, stav, vlastnosti a nastavení pro váš typ zařízení.

Aby bylo možné otestovat aplikaci před připojením skutečného zařízení, IoT Central při vytváření šablony zařízení vygeneruje simulované zařízení.

V tomto kurzu vytvoříte šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace). Připojené klimatizační zařízení:

* Odesílá telemetrická data, jako je teplota a vlhkost.
* Sestavy stav například určuje, zda jsou zapnuty či vypnuty.
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

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central. Pokud jste dokončili rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), můžete znovu využít aplikaci, kterou jste v tomto rychlém startu vytvořili. Jinak použijte následující postup a vytvořte prázdnou aplikaci Azure IoT Central:

1. Přejděte na stránku [správce aplikací](https://aka.ms/iotcentral) Azure IoT Central.

2. Zadejte e-mailovou adresu a heslo, které používáte pro přístup k vašemu předplatnému Azure:

    ![Zadání účtu organizace](./media/tutorial-define-device-type-experimental/sign-in.png)

3. Chcete-li vytvořit novou aplikaci Azure IoT Central, klikněte na tlačítko **novou aplikaci**:

    ![Stránka správce aplikací Azure IoT Central](./media/tutorial-define-device-type-experimental/iotcentralhome.png)

4. Vytvoření nové aplikace Azure IoT Central:
    
    * Zvolte **Trial** (Zkušební verze). K vytvoření zkušební verze aplikace nepotřebujete předplatné Azure.
    
       Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
    
    * Zvolte **Vlastní aplikace**.
    
    * Volitelně můžete zvolit popisný název aplikace, jako je třeba **Klimatizace Contoso**. Azure IoT Central pro vás vygeneruje jedinečnou předponu URL. Tuto předponu URL můžete změnit, aby byla snáze zapamatovatelná.
    
    * Klikněte na možnost **Vytvořit**.

    ![Stránka vytvoření aplikace Azure IoT Central](./media/tutorial-define-device-type-experimental/iotcentralcreate.png)

    Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="create-a-new-custom-device-template"></a>Vytvoření nové vlastní šablony zařízení

Jako tvůrce můžete ve vaší aplikaci vytvářet a upravovat šablony zařízení. Když vytvoříte šablonu zařízení, Azure IoT Central z této šablony vygeneruje simulované zařízení. Simulované zařízení generuje telemetrická data, která umožňuje testovat chování aplikace před připojit skutečné zařízení.

Chcete-li přidat novou šablonu zařízení pro vaši aplikaci, budete muset přejít do **šablon** stránky. Klepněte na tlačítko **šablon** v levé navigační nabídce.

![Stránka šablony zařízení](./media/tutorial-define-device-type-experimental/devicetemplates.png)

## <a name="add-a-device-template"></a>Přidání šablony zařízení

Následující kroky ukazují, jak vytvořit novou šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace) pro zařízení, která do vaší aplikace odesílají teplotní telemetrická data:

1. Na **šablon** klikněte na **+ nová**:

    ![Stránka zařízení šablon, vytvořit šablonu zařízení](./media/tutorial-define-device-type-experimental/newtemplate.png)

3. Na **vlastní šablonu zařízení** zadejte **připojené klimatizace** jako název zařízení a pak klikněte na tlačítko **vytvořit**. Můžete také nahrát obrázek zařízení, který vidí operátoři v Device Exploreru:

    ![Vlastní zařízení](./media/tutorial-define-device-type-experimental/createcustomdevice.png)

4. V **připojené klimatizace** zařízení šablonu, ujistěte se, že jste na **měření** kartu, kde můžete definovat telemetrická data. Každé zařízení šablony, které definujete má samostatných kartách můžete:

    * Zadejte _měření_, jako je například telemetrie, události a stav, odeslané ze zařízení.

    * Definovat _nastavení_ používat k ovládání zařízení.

    * Definovat _vlastnosti_ , které jsou metadata zařízení.

    * Definovat _příkazy_ mohl být spuštěn přímo na zařízení.

    * Definovat _pravidla_ přidružené k zařízení.

    * Vlastní nastavení zařízení _řídicí panel_ pro operátory.

    ![Měření klimatizace](./media/tutorial-define-device-type-experimental/airconmeasurements.png)

    > [!NOTE]
    > Chcete-li změnit název zařízení šablony, klikněte na název šablony v horní části stránky.

5. Přidání telemetrické měření teploty, klikněte na tlačítko **a nové měření**. Potom jako typ měření zvolte **Telemetry** (Telemetrická data):

    ![Měření připojené klimatizace](./media/tutorial-define-device-type-experimental/airconmeasurementsnew.png)

6. Každý typ telemetrických dat, který definujete pro šablonu zařízení, zahrnuje [možnosti konfigurace](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), jako jsou:

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

    Můžete také zvolit barvu pro zobrazení telemetrických dat. Chcete-li uložit definici telemetrická data, klikněte na tlačítko **Uložit**:

    ![Konfigurace simulace teploty](./media/tutorial-define-device-type-experimental/temperaturesimulation.png)

7. Po nějakou dobu **měření** karta ukazuje graf teplotní telemetrie ze zařízení s Simulovaná připojených klimatizace. Pomocí ovládacích prvků můžete spravovat viditelnost a agregaci nebo můžete upravit definici telemetrických dat:

    ![Zobrazení simulace teploty](./media/tutorial-define-device-type-experimental/viewsimulation.png)

8. K přizpůsobení grafu se také dají využít ovládací prvky **Line** (Čárový), **Stacked** (Skládaný) a **Edit Time Range** (Upravit časový rozsah):

    ![Přizpůsobení grafu](./media/tutorial-define-device-type-experimental/customizechart.png)

## <a name="add-an-event-measurement"></a>Přidat měření událostí

Události použijte k definování dat bodu v čase, který zařízení odešle, když dojde události, například k chybě nebo selhání součásti. Azure IoT Central můžete simulovat zařízení událostí umožňuje testovat chování aplikace před připojit skutečné zařízení. Definovat událost měření v šabloně zařízení **měření** zobrazení.

1. Chcete-li přidat **Motor chyba ventilátor** měření událostí, klikněte na tlačítko **a nové měření**. Potom jako typ měření zvolte **Event** (Událost):

    ![Měření připojené klimatizace](./media/tutorial-define-device-type-experimental/eventnew.png)

2. Každý typ události, který definujete pro šablonu zařízení, zahrnuje [možnosti konfigurace](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), jako jsou:

   * Zobrazovaný název

   * Název pole

   * Závažnost

    Ke konfiguraci události **Fan Motor Error** (Chyba motoru ventilátoru) použijte informace v následující tabulce:

    | Nastavení              | Hodnota             |
    | -------------------- | -----------       |
    | Zobrazovaný název         | Chyba motoru ventilátoru   |
    | Název pole           | fanmotorerr       |
    | Severity             | Chyba             |

    Chcete-li uložit definici události, klikněte na tlačítko **Uložit**:

    ![Konfigurace měření událostí](./media/tutorial-define-device-type-experimental/eventconfiguration.png)

3. Po nějakou dobu **měření** karta ukazuje graf náhodně vygenerované ze zařízení s Simulovaná připojených vzduchovod události. Pomocí ovládacích prvků můžete spravovat viditelnost nebo můžete upravit definici události:

    ![Zobrazení simulace události](./media/tutorial-define-device-type-experimental/eventview.png)

1. Pokud chcete o události zobrazit další podrobnosti, klikněte na ni v grafu:

    ![Zobrazení podrobnosti události](./media/tutorial-define-device-type-experimental/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definování měření stavu

Stav můžete použít k definování a vizualizovat stav zařízení nebo jeho součást po určitou dobu. Azure IoT Central můžete simulovat stavu zařízení umožňuje testovat chování aplikace před připojit skutečné zařízení. K definování měření stavu pro příslušný typ zařízení se použije zobrazení **Measurements** (Měření).

1. Chcete-li přidat **ventilátor režimu** měření stavu, klikněte na tlačítko **a nové měření**. Potom jako typ měření zvolte **State** (Stav):

    ![Měření stavu připojené klimatizace](./media/tutorial-define-device-type-experimental/statenew.png)

2. Každý typ stavu definujete pro šablonu zařízení obsahuje [možnosti konfigurace](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) jako například:

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

    Chcete-li uložit definici měření stavu, klikněte na tlačítko **Uložit**:

    ![Konfigurace měření stavu](./media/tutorial-define-device-type-experimental/stateconfiguration.png)

3. Po nějakou dobu **měření** karta ukazuje graf stavů náhodně generované ze zařízení s Simulovaná připojených klimatizace. Pomocí ovládacích prvků můžete spravovat viditelnost nebo můžete upravit definici stavu:

    ![Zobrazení simulace stavu](./media/tutorial-define-device-type-experimental/stateview.png)

4. Pokud existuje příliš mnoho datových bodů, které zařízení odeslalo do malé dobu trvání, zobrazí se s jiný vizuál měření stavu. Klikněte na graf zobrazíte všechny datové body v rámci tohoto časového období se zobrazují v chronologickém pořadí. Časový rozsah můžete také zúžit a zobrazit podrobnější měření.

## <a name="settings-properties-and-commands"></a>Nastavení, vlastnosti a příkazy

Nastavení, vlastnosti a příkazy jsou různé hodnoty definované v šabloně zařízení a přidružené k jednotlivým zařízením:

* _Nastavení_ použijete k odeslání konfiguračních dat ze zařízení do aplikace. Operátor může například použít nastavení, pokud chce změnit interval telemetrie zařízení ze dvou na pět sekund. Pokud se operátor změní nastavení, nastavení je čekající na vyřízení v uživatelském rozhraní, dokud se zařízení odpoví na potvrzení.

* _Vlastnosti_ použijete k definování metadat přidružených k vašemu zařízení. Vlastnosti se dělí do dvou kategorií:
    
    * _Vlastnosti aplikace_ použijete k zaznamenání informací o zařízení ve vaší aplikaci. Vlastnosti aplikace můžete použít například k zaznamenání polohy zařízení a data jeho poslední údržby. Tyto vlastnosti se ukládají v aplikaci a nechcete synchronizovat s zařízení. Operátor může vlastnostem přiřadit hodnotu.

    * _Vlastnosti zařízení_ použijete k tomu, abyste zařízení umožnili odeslat hodnoty vlastností do vaší aplikace. Tyto vlastnosti může měnit jenom zařízení. Pro operátora jsou vlastnosti zařízení jen pro čtení. V tomto scénáři připojené klimatizace jsou verze firmwaru a sériové číslo zařízení vlastnosti zařízení odesílané zařízením.
    
    Další informace najdete v tématu [vlastnosti](howto-set-up-template.md#properties) v Průvodci s postupy o nastavení zařízení šablony.

* _Příkazy_ použijete ke vzdálené správě zařízení z aplikace. Příkazy můžete spustit přímo na zařízení z cloudu a zařízení tak ovládat. Operátor například může spouštět příkazy, jako je třeba příkaz restartování, a okamžitě zařízení restartovat.

## <a name="use-settings"></a>Použití nastavení

*Nastavení* použijete k tomu, abyste operátorovi umožnili odeslání konfiguračních dat do zařízení. V této části do šablony zařízení **Connected Air Conditioner** přidáte nastavení, které operátorovi umožňuje nastavit cílovou teplotu připojené klimatizace.

1. Přejděte na **nastavení** kartu pro vaše **připojené Vzduchovod** šablona zařízení.

2. Můžete vytvořit nastavení různých typů, například čísla nebo text. Klikněte na tlačítko **číslo** k zadejte číslo pro vaše zařízení.

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

    Pak klikněte na tlačítko **Uložit**:

    ![Konfigurace nastavení Set Temperature (Nastavená teplota)](./media/tutorial-define-device-type-experimental/configuresetting.png)

    > [!NOTE]
    > Jakmile zařízení rozpozná změnu nastavení, stav nastavení se změní na **synced** (Synchronizováno).

4. Můžete upravit rozložení **nastavení** kartu tak, že přesunutí a změna velikosti dlaždice nastavení:

    ![Přizpůsobení rozložení nastavení](./media/tutorial-define-device-type-experimental/settingslayout.png)

## <a name="use-properties"></a>Použití vlastností

*Vlastnosti aplikace* použijete k uložení informací o vašem zařízení v aplikaci. V této části do šablony zařízení **Connected Air Conditioner** (Připojená klimatizace) přidáte vlastnosti aplikace pro uložení polohy zařízení a data jeho poslední údržby. Tyto vlastnosti lze upravit v aplikaci. Zařízení odesílá také vlastnosti, jako je sériové číslo a verze firmwaru, které jsou v aplikaci jen pro čtení.

1. Přejděte na **vlastnosti** kartu pro vaše **připojené Vzduchovod** šablona zařízení.

1. Můžete vytvořit vlastnosti zařízení různých typů, například čísla nebo text. Pokud chcete do šablony zařízení přidat vlastnost umístění, zvolte **Location** (Umístění). Ke konfiguraci vlastnosti umístění použijte informace v následující tabulce:

    | Pole                | Hodnota                |
    | -------------------- | -------------------- |
    | Zobrazovaný název         | Umístění             |
    | Název pole           | location             |
    | Počáteční hodnota        | Seattle, WA          |
    | Popis          | Device location (Umístění zařízení)      |

    Ostatní pole nechte nastavená na výchozí hodnoty.

    ![Konfigurace vlastností zařízení](./media/tutorial-define-device-type-experimental/configureproperties.png)

    Klikněte na **Uložit**.

1. Pokud chcete do šablony zařízení přidat vlastnost data poslední údržby, zvolte **Date** (Datum).

1. Ke konfiguraci vlastnosti data poslední údržby použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Last Service Date (Datum poslední údržby)       |
    | Název pole           | serviceDate             |
    | Počáteční hodnota        | 1/1/2019                |
    | Popis          | Last serviced (Poslední údržba)           |

    ![Konfigurace vlastností zařízení](./media/tutorial-define-device-type-experimental/configureproperties2.png)

    Klikněte na **Uložit**.

1. Můžete upravit rozložení **vlastnosti** kartu tak, že přesunutí a změna velikosti dlaždice vlastnosti.

1. Pokud chcete do šablony zařízení přidat vlastnost zařízení, jako je verze firmwaru, zvolte **Device Property** (Vlastnost zařízení).

1. Ke konfiguraci verze firmwaru použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Verze firmwaru        |
    | Název pole           | firmwareVersion         |
    | Typ dat            | text                    |
    | Popis          | Verze firmwaru klimatizace |

    ![Konfigurace verze firmwaru](./media/tutorial-define-device-type-experimental/configureproperties3.png)

    Klikněte na **Uložit**.

1. Pokud chcete do šablony zařízení přidat vlastnost zařízení, jako je sériové číslo, zvolte **Device Property** (Vlastnost zařízení).

1. Ke konfiguraci sériového čísla použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Sériové číslo           |
    | Název pole           | serialNumber            |
    | Typ dat            | text                    |
    | Popis          | Sériové číslo klimatizace  |

    ![Konfigurace sériového čísla](./media/tutorial-define-device-type-experimental/configureproperties4.png)

    Klikněte na **Uložit**.

    > [!NOTE]
    > Vlastnost zařízení se odešle ze zařízení do aplikace. Hodnoty verze firmwaru a sériového čísla se aktualizují při připojení skutečného zařízení k IoT Central.

## <a name="use-commands"></a>Použití příkazů

_Příkazy_ použijete k tomu, abyste operátorovi umožnili spouštět příkazy přímo na zařízení. V této části přidáte do šablony zařízení **Connected Air Conditioner** příkaz, který operátorovi umožní vypsat na připojené klimatizaci určitou zprávu.

1. Přejděte na **příkazy** kartu pro vaše **připojené Vzduchovod** zařízení šablonu upravit šablonu.

1. Klikněte na tlačítko **+ nový příkaz** začít konfigurovat nový příkaz a přidejte příkaz do svého zařízení.

1. Ke konfiguraci nového příkazu použijte informace v následující tabulce:

    | Pole                | Hodnota           |
    | -------------------- | -----------     |
    | Zobrazovaný název         | Příkaz pro zobrazení výsledků    |
    | Název pole           | echo            |
    | Výchozí časový limit      | 30              |
    | Zobrazovaný typ         | text            |
    | Popis          | Příkaz zařízení  |  

    Kliknutím na **+** v části **Input Fields** (Vstupní pole) můžete do příkazu přidat další vstupy.

    ![Příprava pro přidání nastavení](./media/tutorial-define-device-type-experimental/commandsecho1.png)

     Klikněte na **Uložit**.

1. Můžete upravit rozložení **příkazy** kartu tak, že přesunutí a změna velikosti dlaždice příkazu.

## <a name="view-your-simulated-device"></a>Zobrazení simulovaného zařízení

Nyní jste definovali vaše **připojené klimatizace** šablona zařízení, si můžete přizpůsobit jeho **řídicí panel** měření, nastavení a vlastnosti, které jste definovali. Potom si můžete zobrazit náhled řídicího panelu jako operátor:

1. Zvolte **řídicí panel** kartu pro vaše **připojené Vzduchovod** šablona zařízení.

1. Klikněte na tlačítko **spojnicový graf** přidat součást do **řídicí panel**.

1. Ke konfiguraci komponenty **Line Chart** (Čárový graf) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Teplota |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření     | Teplota (klikněte na tlačítko **viditelnost** vedle **teploty**) |

    ![Nastavení čárového grafu](./media/tutorial-define-device-type-experimental/linechartsettings.png)

    Potom klikněte na **Uložit**.

1. Klikněte na tlačítko **Historie událostí** komponenty s použitím informací v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Ventilátor Motor události |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření     | Ventilátor Motor chyby (klikněte na tlačítko **viditelnost** vedle **Motor chyba ventilátor**) |

    ![Nastavení čárového grafu](./media/tutorial-define-device-type-experimental/dashboardeventchartsetting.png)

    Potom klikněte na **Uložit**.

1. Ke konfiguraci komponenty **State History** (Historie stavu) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Režim ventilátoru |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření | Ventilátor režimu (klikněte na tlačítko **viditelnost** vedle **ventilátor režimu**) |

    ![Nastavení čárového grafu](./media/tutorial-define-device-type-experimental/dashboardstatechartsetting.png)

    Potom klikněte na **Uložit**.

1. Chcete-li přidat na řídicí panel nastavení a vlastnosti, zvolte **nastavením a vlastnostem**. Kliknutím na **Add/Remove** (Přidat nebo odebrat) přidejte nastavení nebo vlastnosti, které chcete zobrazit na řídicím panelu.

1. Ke konfiguraci komponenty **Settings and Properties** (Nastavení a vlastnosti) použijte informace v následující tabulce:

    | Nastavení                 | Hodnota         |
    | ----------------------- | ------------- |
    | Název                   | Vlastnosti zařízení |
    | Nastavení a vlastností | Nastavená teplota<br/>Sériové číslo<br/>Verze firmwaru |

    Nastavení a vlastnosti, které jste dříve definovali v **nastavením a vlastnostem** stránky jsou uvedeny v **dostupných sloupců**.

    ![Nastavení vlastností Set Temperature (Nastavená teplota)](./media/tutorial-define-device-type-experimental/propertysettings4.png)

    Potom klikněte na **Uložit**.

1. Nyní je vidět Simulovaná data pro vaše připojení Vzduchovod na řídicím panelu. Můžete upravit dlaždice a rozložení řídicího panelu:

    ![Zobrazení řídicího panelu](./media/tutorial-define-device-type-experimental/dashboard.png)

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

Teď, když jste definovali šablonu zařízení v aplikaci Azure IoT Central, tady jsou další navrhované kroky:

* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Přizpůsobení zobrazení operátora](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)