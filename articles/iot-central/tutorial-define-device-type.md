---
title: Definování nového typu zařízení v Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte jako tvůrce, jak v aplikaci IoT Central Azure definovat nový typ zařízení. Definujete telemetrii, stav, vlastnosti a nastavení typu.
author: dominicbetts
ms.author: dobett
ms.date: 06/07/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a3faa76c1506664a075648edc7d57fbba542b011
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960532"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Kurz: definování nového typu zařízení v aplikaci Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

V tomto kurzu se dozvíte jako tvůrce, jak použít šablonu zařízení k definování nového typu zařízení v aplikaci Microsoft Azure IoT Central. Šablona zařízení definuje telemetrie, stav, vlastnosti a nastavení pro typ zařízení.

Aby bylo možné otestovat aplikaci před připojením reálného zařízení, IoT Central při vytváření z šablony zařízení vygeneruje simulované zařízení.

V tomto kurzu vytvoříte **připojenou** šablonu zařízení klimatizace klimatizace. Připojené zařízení klimatizačního zařízení:

* Odesílá telemetrii, jako je teplota a vlhkost.
* Hlásí stav, například zda je zapnutý nebo vypnutý.
* Má vlastnosti zařízení, jako je například verze firmwaru a sériové číslo.
* Obsahuje nastavení jako cílovou teplotu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit novou šablonu zařízení
> * Přidání telemetrie do zařízení
> * Zobrazit simulovanou telemetrii
> * Definovat měření události
> * Zobrazit simulované události
> * Definovat měření stavu
> * Zobrazit simulovaný stav
> * Použití nastavení a vlastností
> * Použití příkazů
> * Zobrazení simulovaného zařízení na řídicím panelu

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central. Pokud jste dokončili rychlé spuštění [aplikace pro vytváření IoT Central Azure](quick-deploy-iot-central.md) , můžete znovu použít aplikaci, kterou jste vytvořili v rychlém startu. V opačném případě proveďte následující kroky k vytvoření prázdné aplikace IoT Central Azure:

1. Přejděte na web [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) .

2. Zadejte e-mailovou adresu a heslo, které používáte pro přístup k předplatnému Azure:

    ![Zadejte účet organizace.](./media/tutorial-define-device-type/sign-in.png)

3. Pokud chcete začít vytvářet novou aplikaci IoT Central Azure, vyberte **Nová aplikace**:

    ![Stránka Správce aplikací Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Vytvoření nové aplikace IoT Central v Azure:
    
   * Vyberte **zkušební verzi**. K vytvoření zkušební aplikace nepotřebujete předplatné Azure.
    
      Další informace o adresářích a předplatných najdete v tématu [rychlý Start k vytvoření aplikace](quick-deploy-iot-central.md).
    
   * Vyberte **vlastní aplikace**.
    
   * Volitelně můžete zvolit popisný název aplikace, například **klimatizace společnosti Contoso**. Azure IoT Central pro vás vygeneruje jedinečnou předponu adresy URL. Tuto předponu adresy URL můžete změnit na něco snadněji.
    
   * Vyberte **vytvořit**.

     ![Stránka pro vytvoření aplikace v Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

     Další informace najdete v tématu [rychlý Start k vytvoření aplikace](quick-deploy-iot-central.md).

## <a name="create-a-device-template"></a>Vytvoření šablony zařízení

V rámci Tvůrce můžete v aplikaci vytvořit a upravit šablony zařízení. Když vytvoříte šablonu zařízení, Azure IoT Central ze šablony vygeneruje simulované zařízení. Simulované zařízení vygeneruje telemetrii, která umožňuje otestovat chování aplikace před připojením reálného zařízení.

Chcete-li do aplikace přidat novou šablonu zařízení, je třeba přejít na stránku **šablony zařízení** . Pokud to chcete udělat, vyberte v levé navigační nabídce **šablony zařízení** .

![Stránka šablony zařízení](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Přidat šablonu zařízení

Následující kroky ukazují, jak vytvořit novou **připojenou** šablonu zařízení klimatizačního prostředí pro zařízení, která odesílají do vaší aplikace telemetrii teploty:

1. Na stránce **šablony zařízení** vyberte **+ Nová**:

    ![Stránka šablony zařízení, vytvořit šablonu zařízení](./media/tutorial-define-device-type/newtemplate.png)

2. Stránka zobrazuje šablony, ze kterých můžete vybírat.

    ![Knihovna šablon zařízení](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Vyberte **vlastní**, jako název šablony zařízení zadejte **připojeného leteckého klimatizace** a pak vyberte **vytvořit**. Můžete také nahrát obrázek zařízení, který je viditelný pro operátory v Průzkumníkovi zařízení:

    ![Vlastní zařízení](./media/tutorial-define-device-type/createcustomdevice.png)

4. V šabloně zařízení s **připojenou** klimatizace se ujistěte, že jste na kartě **měření** , kde můžete definovat telemetrii. Každá šablona zařízení, kterou definujete, obsahuje samostatné karty:

   * Určete _měření_, jako je telemetrie, událost a stav, odesílané zařízením.

   * Zadejte _Nastavení_ , která se použijí pro řízení zařízení.

   * Definujte _vlastnosti_ , které jsou v metadatech zařízení.

   * Definujte _příkazy_ , které se mají spustit přímo na zařízení.

   * Definujte _pravidla_ přidružená k zařízení.

   * Přizpůsobte _řídicí panel_ zařízení pro vaše operátory.

     ![Měření klimatizačního prostředí](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Chcete-li změnit název šablony zařízení, vyberte název šablony v horní části stránky.

5. Pokud chcete přidat měření telemetrie na teplotu, vyberte **+ nové měření**. Pak jako typ měření zvolte **telemetrie** :

    ![Měření vzduchem připojeného klimatizace](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Každý typ telemetrie, který definujete pro šablonu zařízení, zahrnuje [Možnosti konfigurace](howto-set-up-template.md) , jako například:

   * Možnosti zobrazení.

   * Podrobnosti telemetrie.

   * Parametry simulace.

     Ke konfiguraci telemetrie **teploty** použijte informace v následující tabulce:

     | Nastavením              | Hodnota         |
     | -------------------- | -----------   |
     | Zobrazované jméno         | Tepelné   |
     | Název pole           | tepelné   |
     | Podíl                | F             |
     | Dlouhé                  | 60            |
     | Počet                  | 110           |
     | Desetinná místa       | 0,8             |

     Můžete také zvolit barvu zobrazení telemetrie. Pokud chcete uložit definici telemetrie, vyberte **Uložit**:

     ![Konfigurace simulace teploty](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Po krátké době se na kartě **měření** zobrazuje graf telemetrie teploty ze zařízení simulovaného připojeného vzduchu. Pomocí ovládacích prvků můžete spravovat viditelnost, agregaci nebo úpravu definice telemetrie:
 
    > [!NOTE]
    > V případě telemetrie je jako výchozí agregace nastavena hodnota **průměr** . 

    ![Zobrazit simulaci teploty](./media/tutorial-define-device-type/viewsimulation.png)

8. Můžete také přizpůsobit graf pomocí ovládacích prvků rozsah **,** **Skládaný**a **Upravit časový rozsah** :

    ![Přizpůsobení grafu](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Přidat měření události

Události použijte k definování data v čase, které zařízení odesílá, když dojde k chybě nebo selhání součásti. Azure IoT Central může simulovat události zařízení, aby bylo možné před připojením reálného zařízení otestovat chování vaší aplikace. V zobrazení **měření** definujte měření událostí pro šablonu zařízení.

1. Chcete-li přidat měření **chybové události motoru ventilátoru** , vyberte **+ nové měření**. Pak zvolte **událost** jako typ měření:

    ![Měření vzduchem připojeného klimatizace](./media/tutorial-define-device-type/eventnew.png)

2. Každý typ události, kterou definujete pro šablonu zařízení, zahrnuje [Možnosti konfigurace](howto-set-up-template.md) , jako například:

   * Zobrazovaný název

   * Název pole

   * Závažnost.

     K nakonfigurování **chybové události ventilátoru** použijte informace v následující tabulce:

     | Nastavením              | Hodnota             |
     | -------------------- | -----------       |
     | Zobrazované jméno         | Chyba ventilátoru   |
     | Název pole           | fanmotorerr       |
     | Závažnost             | Chyba             |

     Pokud chcete definici události uložit, vyberte **Uložit**:

     ![Konfigurovat měření událostí](./media/tutorial-define-device-type/eventconfiguration.png)

3. Po krátké době se na kartě **měření** zobrazuje graf událostí náhodně generovaných ze simulovaného zařízení s pneumatickým připojením. Použijte ovládací prvky pro správu viditelnosti nebo pro úpravu definice události:

    ![Zobrazit simulaci události](./media/tutorial-define-device-type/eventview.png)

1. Pokud chcete zobrazit další podrobnosti o události, vyberte událost v grafu:

    ![Zobrazit podrobnosti události](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definování měření stavu

Stav můžete použít k definování a vizualizaci stavu zařízení nebo jeho komponenty v časovém intervalu. Azure IoT Central může simulovat stav zařízení, aby bylo možné před připojením reálného zařízení otestovat chování vaší aplikace. V zobrazení **měření** definujte měření stavu pro typ zařízení.

1. Chcete-li přidat měření stavu **režimu ventilátoru** , vyberte **+ Nová měření**. Pak jako typ míry zvolte možnost **stav** :

    ![Měření stavu připojeného klimatizace](./media/tutorial-define-device-type/statenew.png)

2. Každý typ stavu, který definujete pro šablonu zařízení, zahrnuje [Možnosti konfigurace](howto-set-up-template.md) , jako například:

   * Zobrazovaný název

   * Název pole

   * Hodnoty s volitelnými popisky zobrazení.

   * Barva pro každou hodnotu

     Chcete-li konfigurovat stav **režimu ventilátoru** , použijte informace v následující tabulce:

     | Nastavením              | Hodnota             |
     | -------------------- | -----------       |
     | Zobrazované jméno         | Režim ventilátoru          |
     | Název pole           | fanmode           |
     | Hodnota                | první                 |
     | Zobrazit popisek        | Operačního         |
     | Hodnota                | 0,8                 |
     | Zobrazit popisek        | Ukončen           |

     Pokud chcete uložit definici měření stavu, vyberte **Uložit**:

     ![Konfigurovat měření stavu](./media/tutorial-define-device-type/stateconfiguration.png)

3. Po krátké době se na kartě **měření** zobrazuje graf stavů náhodně generovaných ze simulovaného zařízení s pneumatickým připojením. Použijte ovládací prvky pro správu viditelnosti nebo pro úpravu definice stavu:

    ![Simulace stavu zobrazení](./media/tutorial-define-device-type/stateview.png)

4. Pokud je v rámci malé doby trvání zařízení odesíláno příliš mnoho datových bodů, zobrazuje se u měření stavu jiný vizuál. Výběrem grafu zobrazíte všechny datové body v daném časovém období v chronologickém pořadí. Můžete také zúžit časový rozsah, aby se měření zobrazilo podrobněji.

## <a name="settings-properties-and-commands"></a>Nastavení, vlastnosti a příkazy

Nastavení, vlastnosti a příkazy jsou jiné hodnoty definované v šabloně zařízení a spojené s jednotlivými zařízeními:

* Pomocí _Nastavení_ můžete odesílat konfigurační data do zařízení z vaší aplikace. Například operátor může použít nastavení ke změně intervalu telemetrie zařízení ze dvou sekund na pět sekund. Když operátor změní nastavení, bude toto nastavení označeno jako čeká v uživatelském rozhraní, dokud zařízení neodpoví potvrzením.

* Pomocí _vlastností_ můžete definovat metadata, která jsou přidružená k vašemu zařízení. Existují dvě kategorie vlastností:
    
  * _Vlastnosti aplikace_ můžete použít k zaznamenání informací o zařízení do aplikace. Můžete například použít vlastnosti aplikace k zaznamenání umístění zařízení a jeho poslední datum služby. Tyto vlastnosti se ukládají v aplikaci a nesynchronizují se se zařízením. Operátor může přiřadit hodnoty k vlastnostem.

  * Pomocí _vlastností zařízení_ povolíte, aby zařízení odesílalo hodnoty vlastností do vaší aplikace. Tyto vlastnosti může změnit jenom zařízení. U operátoru jsou vlastnosti zařízení jen pro čtení. V tomto scénáři připojeného klimatizačního modulu se ve verzi firmwaru a sériové číslo zařízení zobrazí vlastnosti zařízení hlášené zařízením.
    
    Další informace najdete v tématu [vlastnosti](howto-set-up-template.md#properties) v příručce k nastavení šablony zařízení.

* Pomocí _příkazů_ můžete vzdáleně spravovat zařízení z vaší aplikace. Můžete přímo spouštět příkazy na zařízení z cloudu a řídit tak zařízení. Například operátor může spustit příkazy, jako je třeba restart, pro okamžité restartování zařízení.

## <a name="use-settings"></a>Použít nastavení

Pomocí *Nastavení* můžete povolit operátorovi odesílat konfigurační data do zařízení. V této části přidáte nastavení do připojené šablony zařízení **klimatizačního** modulu, která umožňuje operátorovi nastavit cílovou teplotu připojeného klimatizace.

1. Přejděte na kartu **Nastavení** pro **připojenou** šablonu zařízení klimatizačního modulu.

2. Můžete vytvořit nastavení různých typů, například čísla nebo text. Vyberte **číslo** pro přidání nastavení čísla do zařízení.

3. Pokud chcete konfigurovat nastavení **teploty** , použijte informace v následující tabulce:

    | Pole                | Hodnota           |
    | -------------------- | -----------     |
    | Zobrazované jméno         | Nastavit teplotu |
    | Název pole           | setTemperature  |
    | Měrná jednotka      | F               |
    | Desetinná místa       | první               |
    | Minimální hodnota        | 20o              |
    | Maximální hodnota        | 200             |
    | Počáteční hodnota        | 80              |
    | Popis          | Nastavit cílovou teplotu pro klimatizační modul |

    Pak vyberte **Uložit**:

    ![Konfigurovat nastavení teploty](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Když zařízení potvrdí změnu nastavení, stav nastavení se změní na **Synchronizovaný**.

4. Rozložení karty **Nastavení** můžete přizpůsobit přesunutím a změnou velikosti dlaždic nastavení:

    ![Přizpůsobení rozložení nastavení](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Použití vlastností

*Vlastnosti aplikace* můžete použít k ukládání informací o zařízení do aplikace. V této části přidáte vlastnosti aplikace do **připojené** šablony zařízení klimatizačního modulu, kde můžete ukládat umístění zařízení a poslední datum služby. Tyto vlastnosti lze v aplikaci upravovat. Zařízení také hlásí vlastnosti, jako je sériové číslo a verze firmwaru, které jsou v aplikaci jen pro čtení.

1. Přejděte na kartu **vlastnosti** **připojené** šablony zařízení klimatizačního modulu.

1. Můžete vytvořit vlastnosti zařízení různých typů, například čísla nebo text. Chcete-li do šablony zařízení přidat vlastnost umístění, vyberte možnost **umístění**. Chcete-li nakonfigurovat vlastnost Location, použijte informace v následující tabulce:

    | Pole                | Hodnota                |
    | -------------------- | -------------------- |
    | Zobrazované jméno         | Umístění             |
    | Název pole           | umístění             |
    | Počáteční hodnota        | Praha, WA          |
    | Popis          | Umístění zařízení      |

    Ponechte ostatní pole výchozími hodnotami.

    ![Konfigurace vlastností zařízení](./media/tutorial-define-device-type/configureproperties.png)

    Vyberte **Uložit**.

1. Chcete-li do šablony zařízení přidat poslední vlastnost data služby, vyberte položku **Datum**.

1. Chcete-li konfigurovat poslední vlastnost data služby, použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazované jméno         | Datum poslední služby       |
    | Název pole           | serviceDate             |
    | Počáteční hodnota        | 1/1/2019                |
    | Popis          | Poslední služba           |

    ![Konfigurace vlastností zařízení](./media/tutorial-define-device-type/configureproperties2.png)

    Vyberte **Uložit**.

1. Rozložení karty **vlastnosti** můžete přizpůsobit přesunutím a změnou velikosti dlaždic vlastností.

1. Chcete-li do šablony zařízení přidat vlastnost zařízení, jako je například firmware verze, vyberte **vlastnost zařízení**.

1. Ke konfiguraci verze firmwaru použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazované jméno         | Verze firmwaru        |
    | Název pole           | firmwareVersion         |
    | Datový typ            | text                    |
    | Popis          | Verze firmwaru klimatizace |

    ![Konfigurovat verzi firmwaru](./media/tutorial-define-device-type/configureproperties3.png)

    Vyberte **Uložit**.

1. Chcete-li do šablony zařízení přidat vlastnost zařízení, například sériové číslo, vyberte **vlastnost zařízení**.

1. Chcete-li konfigurovat sériové číslo, použijte informace v následující tabulce:

    | Pole                | Hodnota                   |
    | -------------------- | ----------------------- |
    | Zobrazované jméno         | Sériové číslo           |
    | Název pole           | Sériové            |
    | Datový typ            | text                    |
    | Popis          | Pořadové číslo klimatizace  |

    ![Konfigurovat sériové číslo](./media/tutorial-define-device-type/configureproperties4.png)

    Vyberte **Uložit**.

    > [!NOTE]
    > Vlastnost zařízení je odeslána ze zařízení do aplikace. Hodnoty verze firmwaru a sériové číslo se aktualizují, když se skutečné zařízení připojí k IoT Central.

## <a name="use-commands"></a>Použití příkazů

Pomocí _příkazů_ můžete operátorovi povolit spouštění příkazů přímo na zařízení. V této části přidáte příkaz do připojené šablony zařízení **klimatizačního** modulu, který umožňuje operátorovi vypsat určitou zprávu na připojeném klimatizace.

1. Pokud chcete šablonu upravit, přejděte na kartu **příkazy** pro **připojenou** šablonu zařízení klimatizace.

1. Vyberte **+ Nový příkaz** a přidejte do zařízení příkaz a začněte konfigurovat nový příkaz.

1. K nakonfigurování nového příkazu použijte informace v následující tabulce:

    | Pole                | Hodnota           |
    | -------------------- | -----------     |
    | Zobrazované jméno         | Echo příkaz    |
    | Název pole           | echo            |
    | Výchozí časový limit      | 30              |
    | Datový typ         | text            |
    | Popis          | Příkaz zařízení  |  

    K příkazu můžete přidat další vstupy výběrem možnosti **+** pro **vstupní pole**.

    ![Příprava na přidání nastavení](./media/tutorial-define-device-type/commandsecho1.png)

     Vyberte **Uložit**.

1. Rozložení karty **příkazy** můžete přizpůsobit přesunutím a změnou velikosti dlaždic příkazů.

## <a name="view-your-simulated-device"></a>Zobrazení simulovaného zařízení

Teď jste definovali **připojenou** šablonu zařízení klimatizace pro klimatizace, můžete přizpůsobit její **řídicí panel** tak, aby zahrnovala měření, nastavení a vlastnosti, které jste definovali. Pak můžete řídicí panel zobrazit jako operátor:

1. Vyberte kartu **řídicí panel** pro **připojenou** šablonu zařízení klimatizačního modulu.

1. Vyberte **Spojnicový graf** a přidejte komponentu do **řídicího panelu**.

1. Pomocí informací v následující tabulce Nakonfigurujte komponentu **spojnicového grafu** :

    | Nastavením      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Tepelné |
    | Časový rozsah   | Posledních 30 minut |
    | Předpisů     | Teplota (výběr **viditelnosti** vedle **teploty**) |

    ![Nastavení spojnicového grafu](./media/tutorial-define-device-type/linechartsettings.png)

    Pak vyberte **Save (Uložit**).

1. Vyberte součást **historie událostí** s použitím informací v následující tabulce:

    | Nastavením      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Ventilátory motorových událostí |
    | Časový rozsah   | Posledních 30 minut |
    | Předpisů     | Chyba ventilátoru (vyberte **viditelnost** vedle **ventilátoru chyba motoru**) |

    ![Nastavení grafu událostí](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Pak vyberte **Save (Uložit**).

1. Nakonfigurujte komponentu **historie stavu** pomocí informací v následující tabulce:

    | Nastavením      | Hodnota       |
    | ------------ | ----------- |
    | Název        | Režim ventilátoru |
    | Časový rozsah   | Posledních 30 minut |
    | Předpisů | Režim ventilátoru (vyberte **viditelnost** vedle **režimu ventilátoru**) |

    ![Nastavení spojnicového grafu](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Pak vyberte **Save (Uložit**).

1. Chcete-li přidat nastavení a vlastnosti zařízení na řídicí panel, vyberte možnost **nastavení a vlastnosti**. Vyberte **Přidat nebo odebrat** a přidejte tak nastavení nebo vlastnosti, které byste chtěli zobrazit na řídicím panelu.

1. Nakonfigurujte komponentu **nastavení a vlastnosti** pomocí informací v následující tabulce:

    | Nastavením                 | Hodnota         |
    | ----------------------- | ------------- |
    | Název                   | Vlastnosti zařízení |
    | Nastavení a vlastnosti | Nastavit teplotu<br/>Sériové číslo<br/>Verze firmwaru |

    Nastavení a vlastnosti, které jste dříve definovali na stránkách **nastavení a vlastnosti** , jsou zobrazeny v části **Dostupné sloupce**.

    ![Nastavení vlastností teploty](./media/tutorial-define-device-type/propertysettings4.png)

    Pak vyberte **Save (Uložit**).

1. Na řídicím panelu teď můžete vidět Simulovaná data pro připojeného klimatizace. Dlaždice a rozložení řídicího panelu můžete upravit:

    ![Zobrazit řídicí panel](./media/tutorial-define-device-type/dashboard.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Vytvořit novou šablonu zařízení
> * Přidání telemetrie do zařízení
> * Zobrazit simulovanou telemetrii
> * Definování událostí zařízení
> * Zobrazit simulované události
> * Definujte svůj stav.
> * Zobrazit simulovaný stav
> * Použití nastavení a vlastností
> * Použití příkazů
> * Zobrazení simulovaného zařízení na řídicím panelu

Teď, když jste definovali šablonu zařízení v aplikaci Azure IoT Central, tady jsou doporučené další kroky:

* [Konfigurace pravidel a akcí pro vaše zařízení](tutorial-configure-rules.md)
* [Přizpůsobení zobrazení operátoru](tutorial-customize-operator.md)