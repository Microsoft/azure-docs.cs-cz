---
title: Přizpůsobení zobrazení operátora v Azure IoT Central | Microsoft Docs
description: Jako tvůrce můžete přizpůsobit zobrazení operátora ve vaší aplikaci Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: aa5ff93b4f1f6392f753a88d57a072f542fc16e2
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877273"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Kurz: Přizpůsobení zobrazení operátora Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Tento kurz vám jako tvůrci ukáže, jak přizpůsobit zobrazení vaší aplikace pro operátora. Když měníte aplikaci jako tvůrce, můžete zobrazit náhled zobrazení operátor v aplikaci Microsoft Azure IoT Central.

V tomto kurzu přizpůsobíte aplikaci tak, aby operátorovi zobrazovala relevantní informace o připojeném klimatizačním zařízení. Vaše přizpůsobení umožní operátorovi spravovat klimatizační zařízení připojená k aplikaci.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace řídicího panelu zařízení
> * Konfigurace rozložení nastavení zařízení
> * Konfigurace rozložení vlastností zařízení
> * Zobrazení náhledu zařízení jako operátor
> * Konfigurace výchozího řídicího panelu aplikace
> * Náhled výchozího řídicího panelu aplikace jako operátoru

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit dva předchozí kurzy:

* [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type.md)
* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md)

## <a name="configure-your-device-dashboard"></a>Konfigurace řídicího panelu zařízení

Jako tvůrce můžete definovat, které informace se zobrazí na řídicím panelu zařízení. V kurzu [definování nového typu zařízení v rámci aplikace](tutorial-define-device-type.md) jste do připojeného řídicího panelu **klimatizace** přidali spojnicový graf a další informace.

1. Pokud chcete upravit připojenou šablonu zařízení klimatizačního **prostředí** , v levé navigační nabídce vyberte **šablony zařízení** :

    ![Stránka šablony zařízení](media/tutorial-customize-operator/devicetemplates.png)

2. Pokud chcete přizpůsobit řídicí panel zařízení, vyberte zařízení šablony **připojeného vzduchu (1.0.0)** , které jste vytvořili v kurzu [definování nového typu zařízení v rámci aplikace](tutorial-define-device-type.md) .

3. Řídicí panel upravíte tak, že vyberete kartu **řídicí panel** .

4. Pokud chcete na řídící panel přidat dlaždici Klíčový ukazatel výkonu, zvolte **KPI** (Klíčový ukazatel výkonu):

    Při definování klíčového ukazatele výkonu použijte informace v následující tabulce:

    | Nastavení     | Value |
    | ----------- | ----- |
    | Name        | Maximální teplota |
    | Časový rozsah  | Poslední 1 týden |
    | Typ měření | Telemetrie |
    | Měření | Teplota |
    | Agregace | Maximum |
    | Viditelnost  | Enabled |

    ![Přidání KPI](media/tutorial-customize-operator/addkpi.png)

5. Vyberte **Uložit**. Teď na řídicím panelu vidíte dlaždici KPI:

    ![Dlaždice KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Pokud chcete přesunout dlaždici na řídicím panelu nebo změnit její velikost, přesuňte ukazatel myši na dlaždici. Dlaždici můžete přetáhnout na nové místo nebo změnit její velikost.

## <a name="configure-your-settings-layout"></a>Konfigurace rozložení nastavení

Jako tvůrce můžete také nakonfigurovat zobrazení nastavení zařízení pro operátora. Operátor používá ke konfiguraci zařízení kartu nastavení zařízení. Například operátor používá kartu nastavení k nastavení cílové teploty připojeného klimatizace.

1. Pokud chcete upravit rozložení nastavení pro připojeného klimatizace, klikněte na kartu **Nastavení** .

2. Dlaždice nastavení můžete přesunout a můžete také upravit jejich velikost:

    ![Úprava rozložení nastavení](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Konfigurace rozložení vlastností

Kromě řídicího panelu a nastavení můžete zobrazení operátora nakonfigurovat také pro vlastnosti zařízení. Operátor používá kartu Vlastnosti zařízení ke správě metadat zařízení. Například operátor používá kartu Vlastnosti k zobrazení sériového čísla zařízení nebo k aktualizaci informací o kontaktech pro výrobce.

1. Pokud chcete upravit rozložení vlastností připojeného klimatizace, klikněte na kartu **vlastnosti** .

2. Pole vlastností můžete přesunout a můžete také upravit jejich velikost:

    ![Úprava rozložení vlastností](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Zobrazit náhled zařízení

Stránka **šablony zařízení** slouží k přizpůsobení karet řídicího panelu, nastavení a vlastností pro operátor. Pomocí stránky **Device Explorer** můžete zobrazit a používat šablonu zařízení.

1. Pokud chcete zobrazit a používat připojenou šablonu klimatizačního prostředí jako operátora, přejděte na stránku **Device Explorer** a vyberte simulované zařízení, které IoT Central vygenerovalo z vaší šablony:

    ![Zobrazení a použití šablony zařízení](media/tutorial-customize-operator/usetemplate.png)

2. Pokud chcete aktualizovat umístění tohoto zařízení, klikněte na **vlastnosti** a upravte hodnotu na dlaždici poloha. Pak vyberte **Uložit**:

    ![Úprava nastavení vlastnosti](media/tutorial-customize-operator/editproperty.png)

3. Pokud chcete připojené klimatizaci odeslat nastavení, zvolte **Settings** (Nastavení),změňte hodnotu nastavení na dlaždici a potom zvolte **Update** (Aktualizovat):

    ![Odesílání nastavení do zařízení](media/tutorial-customize-operator/sendsetting.png)

    Jakmile zařízení rozpozná změnu nastavení, nastavení se na dlaždici zobrazí jako **synced** (Synchronizováno).

4. Jako operátor můžete zobrazit řídicí panel zařízení tak, jak ho nakonfiguroval tvůrce:

    ![Zobrazení řídicího panelu zařízení pro operátora](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Konfigurace výchozího řídicího panelu

Když se tvůrce nebo operátor přihlásí k aplikaci IoT Central Azure, uvidí řídicí panel aplikace. Jako tvůrce můžete nakonfigurovat obsah výchozího řídicího panelu tak, aby zahrnoval nejužitečnější a relevantní obsah pro operátor.

> [!NOTE]
> Uživatelé také mohou vytvořit své vlastní osobní řídicí panely a zvolit je jako výchozí.

1. Pokud chcete přizpůsobit výchozí řídicí panel aplikace, přejděte na stránku **řídicí panel** a v pravém horním rohu stránky vyberte **Upravit** . Zobrazí se panel s knihovnou objektů, které můžete přidat na řídicí panel.

    ![Stránka řídicího panelu](media/tutorial-customize-operator/builderhome.png)

2. Řídicí panel můžete přizpůsobit přidáním dlaždic z **knihovny**. Zvolte **Link** (Odkaz) a přidejte podrobné informace o webu vaší organizace. Potom zvolte **Save** (Uložit):

    ![Přidat odkaz na řídicí panel](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Můžete také přidat odkazy na stránky ve vaší aplikaci Azure IoT Central. Můžete třeba přidat odkaz na řídicí panel zařízení nebo na stránku nastavení.

3. Volitelně můžete zvolit **Obrázek** a nahrát obrázek, který se zobrazí na řídicím panelu. Obrázek může mít adresu URL, na kterou při výběru přejdete:

    ![Přidání obrázku na řídicí panel](media/tutorial-customize-operator/addimage.png)

    Další informace najdete v tématu věnovaném [postupu při přípravě a nahrávání obrázků do aplikace Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Náhled řídicího panelu

Pokud chcete zobrazit náhled řídicího panelu aplikace jako operátora, vyberte **Hotovo** v pravém horním rohu stránky.

![Přepnutí režimu návrhu](media/tutorial-customize-operator/operatorviewhome.png)

Můžete vybrat dlaždice propojení a obrázek a přejít na adresy URL, které jste nastavili jako tvůrce.

## <a name="next-steps"></a>Další kroky

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