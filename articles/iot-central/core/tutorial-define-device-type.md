---
title: 'Kurz: definování nového typu zařízení v Azure IoT Central'
description: Tento kurz vám jako tvůrci ukáže, jak definovat nový typ zařízení v aplikaci Azure IoT Central. Definujete telemetrii, stav, vlastnosti a nastavení pro svůj typ.
author: dominicbetts
ms.author: dobett
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 3d79e710f2c71d96179ab8774ee89c90cb462ca4
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112353"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Kurz: Definování nového typu zařízení v aplikaci Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Tento kurz vám jako tvůrci ukáže, jak pomocí šablony zařízení definovat nový typ zařízení v aplikaci Microsoft Azure IoT Central. Šablona zařízení definuje telemetrická data, stav, vlastnosti a nastavení pro váš typ zařízení.

Aby bylo možné otestovat aplikaci před připojením skutečného zařízení, IoT Central při vytváření šablony zařízení vygeneruje simulované zařízení.

V tomto kurzu vytvoříte šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace). Připojené klimatizační zařízení:

* Odesílá telemetrická data, jako je teplota a vlhkost.
* Hlásí stav, například zda je zapnutý nebo vypnutý.
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

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central vytvořenou v šabloně **vlastní aplikace** . Pokud nemáte aplikaci, dokončete průvodce [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central.md) a ujistěte se, že jste vybrali šablonu **vlastní aplikace** .

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

Jako tvůrce můžete ve vaší aplikaci vytvářet a upravovat šablony zařízení. Když vytvoříte šablonu zařízení, Azure IoT Central z této šablony vygeneruje simulované zařízení. Simulované zařízení vygeneruje telemetrii, která umožňuje otestovat chování aplikace před připojením reálného zařízení.

Chcete-li do aplikace přidat novou šablonu zařízení, je třeba přejít na stránku **šablony zařízení** . Pokud to chcete udělat, vyberte v levém podokně **šablony zařízení** .

![Stránka šablony zařízení](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Přidat šablonu zařízení

Následující kroky ukazují, jak vytvořit novou šablonu zařízení **Connected Air Conditioner** (Připojená klimatizace) pro zařízení, která do vaší aplikace odesílají teplotní telemetrická data:

1. Na stránce **šablony zařízení** vyberte **+ Nová**:

    ![Stránka šablony zařízení, vytvořit šablonu zařízení](./media/tutorial-define-device-type/newtemplate.png)

2. Stránka zobrazuje šablony, ze kterých můžete vybírat.

    ![Knihovna šablon zařízení](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Vyberte **vlastní**, jako název šablony zařízení zadejte **připojeného leteckého klimatizace** a pak vyberte **vytvořit**. Můžete také nahrát obrázek zařízení, který vidí operátoři v Device Exploreru:

    ![Vlastní zařízení](./media/tutorial-define-device-type/createcustomdevice.png)

4. V šabloně zařízení s **připojenou** klimatizace se ujistěte, že jste na kartě **měření** , kde můžete definovat telemetrii. Každá šablona zařízení, kterou definujete, obsahuje samostatné karty:

   * Určete _měření_, jako je telemetrie, událost a stav, odesílané zařízením.

   * Zadejte _Nastavení_ , která se použijí pro řízení zařízení.

   * Definujte _vlastnosti_ , které jsou v metadatech zařízení.

   * Definujte _příkazy_ , které se mají spustit přímo na zařízení.

   * Definujte _pravidla_ přidružená k zařízení.

   * Přizpůsobte _řídicí panel_ zařízení pro vaše operátory.

     ![Měření klimatizace](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Chcete-li změnit název šablony zařízení, vyberte název šablony v horní části stránky.

5. Pokud chcete přidat měření telemetrie na teplotu, vyberte **+ nové měření**. Potom jako typ měření zvolte **Telemetry** (Telemetrická data):

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

     Můžete také zvolit barvu pro zobrazení telemetrických dat. Pokud chcete uložit definici telemetrie, vyberte **Uložit**:

     ![Konfigurace simulace teploty](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Po krátké době se na kartě **měření** zobrazuje graf telemetrie teploty ze zařízení simulovaného připojeného vzduchu. Pomocí ovládacích prvků můžete spravovat viditelnost a agregaci nebo můžete upravit definici telemetrických dat:
 
    > [!NOTE]
    > V případě telemetrie je jako výchozí agregace nastavena hodnota **průměr** . 

    ![Zobrazení simulace teploty](./media/tutorial-define-device-type/viewsimulation.png)

8. K přizpůsobení grafu se také dají využít ovládací prvky **Line** (Čárový), **Stacked** (Skládaný) a **Edit Time Range** (Upravit časový rozsah):

    ![Přizpůsobení grafu](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Přidat měření události

Události použijte k definování data v čase, které zařízení odesílá, když dojde k chybě nebo selhání součásti. Azure IoT Central může simulovat události zařízení, aby bylo možné před připojením reálného zařízení otestovat chování vaší aplikace. V zobrazení **měření** definujte měření událostí pro šablonu zařízení.

1. Chcete-li přidat měření **chybové události motoru ventilátoru** , vyberte **+ nové měření**. Potom jako typ měření zvolte **Event** (Událost):

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

     Pokud chcete definici události uložit, vyberte **Uložit**:

     ![Konfigurace měření událostí](./media/tutorial-define-device-type/eventconfiguration.png)

3. Po krátké době se na kartě **měření** zobrazuje graf událostí náhodně generovaných ze simulovaného zařízení s pneumatickým připojením. Pomocí ovládacích prvků můžete spravovat viditelnost nebo můžete upravit definici události:

    ![Zobrazení simulace události](./media/tutorial-define-device-type/eventview.png)

1. Pokud chcete zobrazit další podrobnosti o události, vyberte událost v grafu:

    ![Zobrazení podrobnosti události](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definování měření stavu

Stav můžete použít k definování a vizualizaci stavu zařízení nebo jeho komponenty v časovém intervalu. Azure IoT Central může simulovat stav zařízení, aby bylo možné před připojením reálného zařízení otestovat chování vaší aplikace. K definování měření stavu pro příslušný typ zařízení se použije zobrazení **Measurements** (Měření).

1. Chcete-li přidat měření stavu **režimu ventilátoru** , vyberte **+ Nová měření**. Potom jako typ měření zvolte **State** (Stav):

    ![Měření stavu připojené klimatizace](./media/tutorial-define-device-type/statenew.png)

2. Každý typ stavu, který definujete pro šablonu zařízení, zahrnuje [Možnosti konfigurace](howto-set-up-template.md) , jako například:

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

     Pokud chcete uložit definici měření stavu, vyberte **Uložit**:

     ![Konfigurace měření stavu](./media/tutorial-define-device-type/stateconfiguration.png)

3. Po krátké době se na kartě **měření** zobrazuje graf stavů náhodně generovaných ze simulovaného zařízení s pneumatickým připojením. Pomocí ovládacích prvků můžete spravovat viditelnost nebo můžete upravit definici stavu:

    ![Zobrazení simulace stavu](./media/tutorial-define-device-type/stateview.png)

4. Pokud je v rámci malé doby trvání zařízení odesíláno příliš mnoho datových bodů, zobrazuje se u měření stavu jiný vizuál. Výběrem grafu zobrazíte všechny datové body v daném časovém období v chronologickém pořadí. Časový rozsah můžete také zúžit a zobrazit podrobnější měření.

## <a name="settings-properties-and-commands"></a>Nastavení, vlastnosti a příkazy

Nastavení, vlastnosti a příkazy jsou různé hodnoty definované v šabloně zařízení a přidružené k jednotlivým zařízením:

* _Nastavení_ použijete k odeslání konfiguračních dat ze zařízení do aplikace. Operátor může například použít nastavení, pokud chce změnit interval telemetrie zařízení ze dvou na pět sekund. Když operátor změní nastavení, bude toto nastavení označeno jako čeká v uživatelském rozhraní, dokud zařízení neodpoví potvrzením.

* _Vlastnosti_ použijete k definování metadat přidružených k vašemu zařízení. Vlastnosti se dělí do dvou kategorií:
    
  * _Vlastnosti aplikace_ použijete k zaznamenání informací o zařízení ve vaší aplikaci. Vlastnosti aplikace můžete použít například k zaznamenání polohy zařízení a data jeho poslední údržby. Tyto vlastnosti se ukládají v aplikaci a nesynchronizují se se zařízením. Operátor může vlastnostem přiřadit hodnotu.

  * _Vlastnosti zařízení_ použijete k tomu, abyste zařízení umožnili odeslat hodnoty vlastností do vaší aplikace. Tyto vlastnosti může měnit jenom zařízení. Pro operátora jsou vlastnosti zařízení jen pro čtení. V tomto scénáři připojené klimatizace jsou verze firmwaru a sériové číslo zařízení vlastnosti zařízení odesílané zařízením.
    
    Další informace najdete v tématu [vlastnosti](howto-set-up-template.md#properties) v příručce k nastavení šablony zařízení.

* _Příkazy_ použijete ke vzdálené správě zařízení z aplikace. Příkazy můžete spustit přímo na zařízení z cloudu a zařízení tak ovládat. Operátor například může spouštět příkazy, jako je třeba příkaz restartování, a okamžitě zařízení restartovat.

## <a name="use-settings"></a>Použití nastavení

*Nastavení* použijete k tomu, abyste operátorovi umožnili odeslání konfiguračních dat do zařízení. V této části do šablony zařízení **Connected Air Conditioner** přidáte nastavení, které operátorovi umožňuje nastavit cílovou teplotu připojené klimatizace.

1. Přejděte na kartu **Nastavení** pro **připojenou** šablonu zařízení klimatizačního modulu.

2. Můžete vytvořit nastavení různých typů, například čísla nebo text. Vyberte **číslo** pro přidání nastavení čísla do zařízení.

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

    Pak vyberte **Uložit**:

    ![Konfigurace nastavení Set Temperature (Nastavená teplota)](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Jakmile zařízení rozpozná změnu nastavení, stav nastavení se změní na **synced** (Synchronizováno).

4. Rozložení karty **Nastavení** můžete přizpůsobit přesunutím a změnou velikosti dlaždic nastavení:

    ![Přizpůsobení rozložení nastavení](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Použití vlastností

*Vlastnosti aplikace* použijete k uložení informací o vašem zařízení v aplikaci. V této části do šablony zařízení **Connected Air Conditioner** (Připojená klimatizace) přidáte vlastnosti aplikace pro uložení polohy zařízení a data jeho poslední údržby. Tyto vlastnosti lze v aplikaci upravovat. Zařízení také hlásí vlastnosti, jako je sériové číslo a verze firmwaru, které jsou v aplikaci jen pro čtení.

1. Přejděte na kartu **vlastnosti** **připojené** šablony zařízení klimatizačního modulu.

1. Můžete vytvořit vlastnosti zařízení různých typů, například čísla nebo text. Pokud chcete do šablony zařízení přidat vlastnost umístění, zvolte **Location** (Umístění). Ke konfiguraci vlastnosti umístění použijte informace v následující tabulce:

    | Pole                | Hodnota                |
    | -------------------- | -------------------- |
    | Zobrazovaný název         | Umístění             |
    | Název pole           | location             |
    | Počáteční hodnota        | Seattle, WA          |
    | Popis          | Device location (Umístění zařízení)      |

    Ostatní pole nechte nastavená na výchozí hodnoty.

    ![Konfigurace vlastností zařízení](./media/tutorial-define-device-type/configureproperties.png)

    Vyberte **Uložit**.

1. Pokud chcete do šablony zařízení přidat vlastnost data poslední údržby, zvolte **Date** (Datum).

1. Ke konfiguraci vlastnosti data poslední údržby použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Last Service Date (Datum poslední údržby)       |
    | Název pole           | serviceDate             |
    | Počáteční hodnota        | 1/1/2019                |
    | Popis          | Last serviced (Poslední údržba)           |

    ![Konfigurace vlastností zařízení](./media/tutorial-define-device-type/configureproperties2.png)

    Vyberte **Uložit**.

1. Rozložení karty **vlastnosti** můžete přizpůsobit přesunutím a změnou velikosti dlaždic vlastností.

1. Pokud chcete do šablony zařízení přidat vlastnost zařízení, jako je verze firmwaru, zvolte **Device Property** (Vlastnost zařízení).

1. Ke konfiguraci verze firmwaru použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Verze firmwaru        |
    | Název pole           | firmwareVersion         |
    | Typ dat            | text                    |
    | Popis          | Verze firmwaru klimatizace |

    ![Konfigurace verze firmwaru](./media/tutorial-define-device-type/configureproperties3.png)

    Vyberte **Uložit**.

1. Pokud chcete do šablony zařízení přidat vlastnost zařízení, jako je sériové číslo, zvolte **Device Property** (Vlastnost zařízení).

1. Ke konfiguraci sériového čísla použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazovaný název         | Sériové číslo           |
    | Název pole           | serialNumber            |
    | Typ dat            | text                    |
    | Popis          | Sériové číslo klimatizace  |

    ![Konfigurace sériového čísla](./media/tutorial-define-device-type/configureproperties4.png)

    Vyberte **Uložit**.

    > [!NOTE]
    > Vlastnost zařízení se odešle ze zařízení do aplikace. Hodnoty verze firmwaru a sériového čísla se aktualizují při připojení skutečného zařízení k IoT Central.

## <a name="use-commands"></a>Použití příkazů

_Příkazy_ použijete k tomu, abyste operátorovi umožnili spouštět příkazy přímo na zařízení. V této části přidáte do šablony zařízení **Connected Air Conditioner** příkaz, který operátorovi umožní vypsat na připojené klimatizaci určitou zprávu.

1. Pokud chcete šablonu upravit, přejděte na kartu **příkazy** pro **připojenou** šablonu zařízení klimatizace.

1. Vyberte **+ Nový příkaz** a přidejte do zařízení příkaz a začněte konfigurovat nový příkaz.

1. Ke konfiguraci nového příkazu použijte informace v následující tabulce:

    | Pole                | Hodnota           |
    | -------------------- | -----------     |
    | Zobrazovaný název         | Příkaz pro zobrazení výsledků    |
    | Název pole           | echo            |
    | Výchozí časový limit      | 30              |
    | Typ dat         | text            |
    | Popis          | Příkaz zařízení  |  

    Výběrem **+** pro **vstupní pole**můžete do příkazu přidat další vstupy.

    ![Příprava pro přidání nastavení](./media/tutorial-define-device-type/commandsecho1.png)

     Vyberte **Uložit**.

1. Rozložení karty **příkazy** můžete přizpůsobit přesunutím a změnou velikosti dlaždic příkazů.

## <a name="view-your-simulated-device"></a>Zobrazení simulovaného zařízení

Teď jste definovali **připojenou** šablonu zařízení klimatizace pro klimatizace, můžete přizpůsobit její **řídicí panel** tak, aby zahrnovala měření, nastavení a vlastnosti, které jste definovali. Potom si můžete zobrazit náhled řídicího panelu jako operátor:

1. Vyberte kartu **řídicí panel** pro **připojenou** šablonu zařízení klimatizačního modulu.

1. Vyberte **Spojnicový graf** a přidejte komponentu do **řídicího panelu**.

1. Ke konfiguraci komponenty **Line Chart** (Čárový graf) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Teplota |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření     | Teplota (výběr **viditelnosti** vedle **teploty**) |

    ![Nastavení čárového grafu](./media/tutorial-define-device-type/linechartsettings.png)

    Potom vyberte **Uložit**.

1. Vyberte součást **historie událostí** s použitím informací v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Ventilátory motorových událostí |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření     | Chyba ventilátoru (vyberte **viditelnost** vedle **ventilátoru chyba motoru**) |

    ![Nastavení grafu událostí](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Potom vyberte **Uložit**.

1. Ke konfiguraci komponenty **State History** (Historie stavu) použijte informace v následující tabulce:

    | Nastavení      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Režim ventilátoru |
    | Časové rozmezí   | Posledních 30 minut |
    | Měření | Režim ventilátoru (vyberte **viditelnost** vedle **režimu ventilátoru**) |

    ![Nastavení čárového grafu](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Potom vyberte **Uložit**.

1. Chcete-li přidat nastavení a vlastnosti zařízení na řídicí panel, vyberte možnost **nastavení a vlastnosti**. Vyberte **Přidat nebo odebrat** a přidejte tak nastavení nebo vlastnosti, které byste chtěli zobrazit na řídicím panelu.

1. Ke konfiguraci komponenty **Settings and Properties** (Nastavení a vlastnosti) použijte informace v následující tabulce:

    | Nastavení                 | Hodnota         |
    | ----------------------- | ------------- |
    | Název                   | Vlastnosti zařízení |
    | Nastavení a vlastností | Nastavená teplota<br/>Sériové číslo<br/>Verze firmwaru |

    Nastavení a vlastnosti, které jste dříve definovali na stránkách **nastavení a vlastnosti** , jsou zobrazeny v části **Dostupné sloupce**.

    ![Nastavení vlastností Set Temperature (Nastavená teplota)](./media/tutorial-define-device-type/propertysettings4.png)

    Potom vyberte **Uložit**.

1. Na řídicím panelu teď můžete vidět Simulovaná data pro připojeného klimatizace. Dlaždice a rozložení řídicího panelu můžete upravit:

    ![Zobrazení řídicího panelu](./media/tutorial-define-device-type/dashboard.png)

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
> * Použití nastavení a vlastností
> * Použití příkazů
> * Zobrazení simulovaného zařízení na řídicím panelu

Teď, když jste definovali šablonu zařízení v aplikaci Azure IoT Central, tady jsou doporučené další kroky:

* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md)
* [Přizpůsobení zobrazení operátora](tutorial-customize-operator.md)