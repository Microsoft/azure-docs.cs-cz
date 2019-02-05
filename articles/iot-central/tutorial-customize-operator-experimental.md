---
title: Přizpůsobení zobrazení operátora v Azure IoT Central | Microsoft Docs
description: Jako tvůrce můžete přizpůsobit zobrazení operátora ve vaší aplikaci Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/29/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: ba91bafbdb53269ef622feeceda7dc48359716de
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734687"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view-new-ui-design"></a>Kurz: Přizpůsobení zobrazení Azure IoT Central – operátor (návrh nové uživatelské rozhraní)

Tento kurz vám jako tvůrci ukáže, jak přizpůsobit zobrazení vaší aplikace pro operátora. Když měníte aplikaci jako tvůrce, můžete zobrazit náhled zobrazení operátor v aplikaci Microsoft Azure IoT Central.

V tomto kurzu přizpůsobíte aplikaci tak, aby operátorovi zobrazovala relevantní informace o připojeném klimatizačním zařízení. Vaše přizpůsobení umožní operátorovi spravovat klimatizační zařízení připojená k aplikaci.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace řídicího panelu zařízení
> * Konfigurace rozložení nastavení zařízení
> * Konfigurace rozložení vlastností zařízení
> * Zobrazení náhledu zařízení jako operátor
> * Konfigurace výchozí domovské stránky
> * Zobrazení náhledu výchozí domovské stránky jako operátor

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit dva předchozí kurzy:

* [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

## <a name="configure-your-device-dashboard"></a>Konfigurace řídicího panelu zařízení

Jako tvůrce můžete definovat, které informace se zobrazí na řídicím panelu zařízení. V [definujte nový typ zařízení ve vaší aplikaci](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) kurzu přidáte spojnicový graf a dalších informací **připojené Vzduchovod** řídicího panelu.

1. Chcete-li upravit **připojené Vzduchovod** šablona zařízení, zvolte **šablon** v levé navigační nabídce:

    ![Stránka šablony zařízení](media/tutorial-customize-operator-experimental/devicetemplates.png)

2. Přizpůsobte si řídicí panel zařízení, klikněte na tlačítko **připojené Vzduchovod (1.0.0)** šablony zařízení, které jste vytvořili v [definujte nový typ zařízení ve vaší aplikaci](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) kurzu.

3. Chcete-li upravit řídicí panel, vyberte **řídicí panel** kartu.

4. Pokud chcete na řídící panel přidat dlaždici Klíčový ukazatel výkonu, zvolte **KPI** (Klíčový ukazatel výkonu):

    Při definování klíčového ukazatele výkonu použijte informace v následující tabulce:

    | Nastavení     | Hodnota |
    | ----------- | ----- |
    | Název        | Maximální teplota |
    | Časové rozmezí  | Poslední 1 týden |
    | Typ měření | Telemetrická data |
    | Měření | Teplota |
    | Agregace | Maximum |
    | Viditelnost  | Povoleno |

    ![Přidání KPI](media/tutorial-customize-operator-experimental/addkpi.png)

5. Klikněte na **Uložit**. Teď na řídicím panelu vidíte dlaždici KPI:

    ![Dlaždice KPI](media/tutorial-customize-operator-experimental/temperaturekpi.png)

6. Pokud chcete přesunout dlaždici na řídicím panelu nebo změnit její velikost, přesuňte ukazatel myši na dlaždici. Můžete přetáhnout na dlaždici na nové umístění nebo změňte jeho velikost.

## <a name="configure-your-settings-layout"></a>Konfigurace rozložení nastavení

Jako tvůrce můžete také nakonfigurovat zobrazení nastavení zařízení pro operátora. Operátor používá ke konfiguraci zařízení na kartě Nastavení zařízení. Například operátor používá na kartě nastavení nastavit cílové teploty pro připojené klimatizace.

1. Chcete-li upravit nastavení rozložení pro vaše připojených klimatizace, zvolte **nastavení** kartu.

2. Dlaždice nastavení můžete přesunout a můžete také upravit jejich velikost:

    ![Úprava rozložení nastavení](media/tutorial-customize-operator-experimental/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Konfigurace rozložení vlastností

Kromě řídicího panelu a nastavení můžete zobrazení operátora nakonfigurovat také pro vlastnosti zařízení. Operátor používá ke správě metadat zařízení na kartě Vlastnosti zařízení. Například operátor používá na kartě Vlastnosti Zobrazit sériové číslo zařízení ani aktualizovat kontaktní údaje pro výrobce.

1. Chcete-li upravit vlastnosti rozložení pro vaše připojených klimatizace, zvolte **vlastnosti** kartu.

2. Pole vlastností můžete přesunout a můžete také upravit jejich velikost:

    ![Úprava rozložení vlastností](media/tutorial-customize-operator-experimental/propertieslayout.png)

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Zobrazení náhledu připojeného klimatizačního zařízení jako operátor

Můžete použít **šablon** stránky k přizpůsobení karty řídicí panel, nastavení a vlastnosti pro operátor. Můžete použít **Device Explorer** stránky zobrazení a použití šablony zařízení.

1. Zobrazení a použití šablony vzduchovod připojené jako operátor, přejděte **Device Explorer** stránky a zvolte simulovaného zařízení tento IoT Central generován ze šablony:

    ![Zobrazit a používat šablonu zařízení](media/tutorial-customize-operator-experimental/usetemplate.png)

2. Chcete-li aktualizovat umístění tohoto zařízení, zvolte **vlastnosti** a upravit její hodnotu v dlaždici umístění. Pak klikněte na tlačítko **Uložit**:

    ![Úprava nastavení vlastnosti](media/tutorial-customize-operator-experimental/editproperty.png)

3. Pokud chcete připojené klimatizaci odeslat nastavení, zvolte **Settings** (Nastavení),změňte hodnotu nastavení na dlaždici a potom zvolte **Update** (Aktualizovat):

    ![Odesílání nastavení do zařízení](media/tutorial-customize-operator-experimental/sendsetting.png)

    Jakmile zařízení rozpozná změnu nastavení, nastavení se na dlaždici zobrazí jako **synced** (Synchronizováno).

4. Jako operátor můžete zobrazit řídicí panel zařízení tak, jak ho nakonfiguroval tvůrce:

    ![Zobrazení řídicího panelu zařízení pro operátora](media/tutorial-customize-operator-experimental/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Konfigurace výchozí domovské stránky

Když tvůrce nebo operátor přihlásí k aplikaci Azure IoT Central, uvidí domovskou stránku. Jako tvůrce můžete nakonfigurovat obsah této domovské stránky tak, aby zahrnovala obsah, který je pro operátory nejužitečnější a nejrelevantnější.

1. Chcete-li přizpůsobit výchozí domovskou stránku, přejděte na **domácí** stránku a upravit **upravit** v horní části stránky. Vysune zleva se seznamem objektů můžete přidat **Domů** stránky:

    ![Stránka Application Builder (Tvůrce aplikací)](media/tutorial-customize-operator-experimental/builderhome.png)

2. Přizpůsobení **Domů** stránce, přidání dlaždice z **knihovny**. Zvolte **Link** (Odkaz) a přidejte podrobné informace o webu vaší organizace. Pak klikněte na tlačítko **Uložit**:

    ![Přidání odkazu na domovskou stránku](media/tutorial-customize-operator-experimental/addlink.png)

    > [!NOTE]
    > Můžete také přidat odkazy na stránky ve vaší aplikaci Azure IoT Central. Můžete třeba přidat odkaz na řídicí panel zařízení nebo na stránku nastavení.

3. Volitelně můžete zvolit **Image** (Obrázek) a nahrát obrázek, který se zobrazí na vaší domovské stránce. Obrázek může mít adresu URL, na kterou přejdete po kliknutí na něj:

    ![Přidání obrázku na domovskou stránku](media/tutorial-customize-operator-experimental/addimage.png)

    Další informace najdete v tématu věnovaném [postupu při přípravě a nahrávání obrázků do aplikace Azure IoT Central](howto-prepare-images.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="preview-the-default-home-page-as-an-operator"></a>Zobrazení náhledu výchozí domovské stránky jako operátor

Chcete-li zobrazit náhled na domovské stránce jako operátor, klikněte na tlačítko **provádí** v horní části stránky:

![Přepnutí režimu návrhu](media/tutorial-customize-operator-experimental/operatorviewhome.png)

Když kliknete na dlaždice s obrázky a odkazy, přejdete na adresy URL, které jste nastavili jako tvůrce.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili, jak přizpůsobit zobrazení aplikace pro operátora.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Konfigurace řídicího panelu zařízení
> * Konfigurace rozložení nastavení zařízení
> * Konfigurace rozložení vlastností zařízení
> * Zobrazení náhledu zařízení jako operátor
> * Konfigurace výchozí domovské stránky
> * Zobrazení náhledu výchozí domovské stránky jako operátor

Teď když jste se naučili, jak přizpůsobit zobrazení aplikace pro operátora, můžete přejít k dalším navrhovaným krokům:

* [Monitorování zařízení (jako operátor)](tutorial-monitor-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Přidání nového zařízení do aplikace (jako operátor a vývojář zařízení)](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
