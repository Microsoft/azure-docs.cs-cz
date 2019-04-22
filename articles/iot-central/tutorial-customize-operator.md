---
title: Přizpůsobení zobrazení operátora v Azure IoT Central | Microsoft Docs
description: Jako tvůrce můžete přizpůsobit zobrazení operátora ve vaší aplikaci Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 7fc42b24090b5fc68176fea2c7b993643ea24200
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678220"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Kurz: Přizpůsobení zobrazení operátora Azure IoT Central

Tento kurz vám jako tvůrci ukáže, jak přizpůsobit zobrazení vaší aplikace pro operátora. Když měníte aplikaci jako tvůrce, můžete zobrazit náhled zobrazení operátor v aplikaci Microsoft Azure IoT Central.

V tomto kurzu přizpůsobíte aplikaci tak, aby operátorovi zobrazovala relevantní informace o připojeném klimatizačním zařízení. Vaše přizpůsobení umožní operátorovi spravovat klimatizační zařízení připojená k aplikaci.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace řídicího panelu zařízení
> * Konfigurace rozložení nastavení zařízení
> * Konfigurace rozložení vlastností zařízení
> * Zobrazení náhledu zařízení jako operátor
> * Konfigurovat výchozí řídicí panel aplikací
> * Ve verzi Preview. výchozí řídicí panel aplikací jako operátor

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit dva předchozí kurzy:

* [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type.md)
* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md)

## <a name="configure-your-device-dashboard"></a>Konfigurace řídicího panelu zařízení

Jako tvůrce můžete definovat, které informace se zobrazí na řídicím panelu zařízení. V [definujte nový typ zařízení ve vaší aplikaci](tutorial-define-device-type.md) kurzu přidáte spojnicový graf a dalších informací **připojené Vzduchovod** řídicího panelu.

1. Chcete-li upravit **připojené Vzduchovod** šablona zařízení, zvolte **šablon** v levé navigační nabídce:

    ![Stránka šablony zařízení](media/tutorial-customize-operator/devicetemplates.png)

2. Chcete-li přizpůsobit řídicí panel zařízení, vyberte **připojené Vzduchovod (1.0.0)** šablony zařízení, které jste vytvořili v [definujte nový typ zařízení ve vaší aplikaci](tutorial-define-device-type.md) kurzu.

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

    ![Přidání KPI](media/tutorial-customize-operator/addkpi.png)

5. Vyberte **Uložit**. Teď na řídicím panelu vidíte dlaždici KPI:

    ![Dlaždice KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Pokud chcete přesunout dlaždici na řídicím panelu nebo změnit její velikost, přesuňte ukazatel myši na dlaždici. Můžete přetáhnout na dlaždici na nové umístění nebo změňte jeho velikost.

## <a name="configure-your-settings-layout"></a>Konfigurace rozložení nastavení

Jako tvůrce můžete také nakonfigurovat zobrazení nastavení zařízení pro operátora. Operátor používá ke konfiguraci zařízení na kartě Nastavení zařízení. Například operátor používá na kartě nastavení nastavit cílové teploty pro připojené klimatizace.

1. Chcete-li upravit nastavení rozložení pro vaše připojených klimatizace, zvolte **nastavení** kartu.

2. Dlaždice nastavení můžete přesunout a můžete také upravit jejich velikost:

    ![Úprava rozložení nastavení](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Konfigurace rozložení vlastností

Kromě řídicího panelu a nastavení můžete zobrazení operátora nakonfigurovat také pro vlastnosti zařízení. Operátor používá ke správě metadat zařízení na kartě Vlastnosti zařízení. Například operátor používá na kartě Vlastnosti Zobrazit sériové číslo zařízení ani aktualizovat kontaktní údaje pro výrobce.

1. Chcete-li upravit vlastnosti rozložení pro vaše připojených klimatizace, zvolte **vlastnosti** kartu.

2. Pole vlastností můžete přesunout a můžete také upravit jejich velikost:

    ![Úprava rozložení vlastností](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Zařízení ve verzi Preview

Můžete použít **šablon** stránky k přizpůsobení karty řídicí panel, nastavení a vlastnosti pro operátor. Můžete použít **Device Explorer** stránky zobrazení a použití šablony zařízení.

1. Zobrazení a použití šablony vzduchovod připojené jako operátor, přejděte **Device Explorer** stránky a zvolte simulovaného zařízení tento IoT Central generován ze šablony:

    ![Zobrazit a používat šablonu zařízení](media/tutorial-customize-operator/usetemplate.png)

2. Chcete-li aktualizovat umístění tohoto zařízení, zvolte **vlastnosti** a upravit její hodnotu v dlaždici umístění. Potom vyberte **Uložit**:

    ![Úprava nastavení vlastnosti](media/tutorial-customize-operator/editproperty.png)

3. Pokud chcete připojené klimatizaci odeslat nastavení, zvolte **Settings** (Nastavení),změňte hodnotu nastavení na dlaždici a potom zvolte **Update** (Aktualizovat):

    ![Odesílání nastavení do zařízení](media/tutorial-customize-operator/sendsetting.png)

    Jakmile zařízení rozpozná změnu nastavení, nastavení se na dlaždici zobrazí jako **synced** (Synchronizováno).

4. Jako operátor můžete zobrazit řídicí panel zařízení tak, jak ho nakonfiguroval tvůrce:

    ![Zobrazení řídicího panelu zařízení pro operátora](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Konfigurovat výchozí řídicí panel

Když tvůrce nebo operátor přihlásí k aplikaci Azure IoT Central, zobrazí se jim řídicí panel aplikací. Jako tvůrce můžete nakonfigurovat obsah výchozí řídicí panel chcete zahrnout obsah nejvíce a vhodnost pro operátor.

> [!NOTE]
> Uživatelé mohou také vytvářet své vlastní osobní řídicí panely a zvolte jednu z nich jako výchozí.

1. Chcete-li přizpůsobit výchozí řídicí panel aplikace, přejděte na **řídicí panel** stránku a vybrat **upravit** v horní části stránky. Knihovna objektů, které lze přidat na řídicí panel se zobrazí panel.

    ![Stránka řídicího panelu](media/tutorial-customize-operator/builderhome.png)

2. Chcete-li přizpůsobit řídicí panel, přidejte dlaždice z **knihovny**. Zvolte **Link** (Odkaz) a přidejte podrobné informace o webu vaší organizace. Potom zvolte **Save** (Uložit):

    ![Přidat odkaz na řídicí panel](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Můžete také přidat odkazy na stránky ve vaší aplikaci Azure IoT Central. Můžete třeba přidat odkaz na řídicí panel zařízení nebo na stránku nastavení.

3. Volitelně můžete zvolit **Image** a nahrát obrázek se zobrazí na řídicím panelu. Obrázek může mít adresu URL, na který můžete procházet, když ji vyberete:

    ![Přidání obrázku na řídicí panel](media/tutorial-customize-operator/addimage.png)

    Další informace najdete v tématu věnovaném [postupu při přípravě a nahrávání obrázků do aplikace Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Ve verzi Preview řídicí panel

Chcete-li zobrazit řídicí panel aplikací jako operátor náhled, vyberte **provádí** v horní části stránky.

![Přepnutí režimu návrhu](media/tutorial-customize-operator/operatorviewhome.png)

Můžete vybrat dlaždice odkazů a obrázků přejděte k adresám URL, které jste nastavili jako tvůrce.

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

* [Monitorování zařízení (jako operátor)](tutorial-monitor-devices.md)
* [Přidání nového zařízení do aplikace (jako operátor a vývojář zařízení)](tutorial-add-device.md)