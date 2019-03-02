---
title: Přizpůsobení zobrazení operátora v Azure IoT Central | Microsoft Docs
description: Jako tvůrce můžete přizpůsobit zobrazení operátora ve vaší aplikaci Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: d10e0b48da497dafe9bb841c68746328e8334c91
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215723"
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

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit dva předchozí kurzy:

* [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type.md)
* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md)

## <a name="configure-your-device-dashboard"></a>Konfigurace řídicího panelu zařízení

Jako tvůrce můžete definovat, které informace se zobrazí na řídicím panelu zařízení. V kurzu [Definování nového typu zařízení ve vaší aplikaci](tutorial-define-device-type.md) jste na řídicí panel **Connected Air Conditioner-1** přidali čárový graf a další informace.

1. Pokud chcete upravit šablonu zařízení **Connected Air Conditioner**, v levé navigační nabídce zvolte **Explorer** (Průzkumník):

    ![Stránka Explorer (Průzkumník)](media/tutorial-customize-operator/explorer.png)

2. Pokud chcete začít s přizpůsobováním řídicího panelu připojeného klimatizačního zařízení, vyberte šablonu zařízení **Connected Air Conditioner (1.0.0)**. Zvolte zařízení **Connected Air Conditioner-1**, které jste vytvořili v kurzu [Definování nového typu zařízení ve vaší aplikaci](tutorial-define-device-type.md):

    ![Výběr připojeného klimatizačního zařízení](media/tutorial-customize-operator/selectdevice.png)

    Když jste uvnitř zařízení, jako je třeba **Connected Air Conditioner-1**, můžete měnit příslušnou šablonu pomocí možnosti **Upravit šablonu**. Další informace najdete v tématu [Vytvoření nové verze šablony zařízení](howto-version-devicetemplate.md).

3. Pokud chcete upravit řídicí panel, zvolte **řídicí panel** a vyberte možnost **Upravit šablonu**:

    ![Stránka řídicího panelu šablony zařízení](media/tutorial-customize-operator/dashboard.png)

4. Pokud chcete na řídící panel přidat dlaždici Klíčový ukazatel výkonu, zvolte **KPI** (Klíčový ukazatel výkonu):

    ![Přidání KPI](media/tutorial-customize-operator/addkpi.png)

    Při definování klíčového ukazatele výkonu použijte informace v následující tabulce:

    | Nastavení     | Hodnota |
    | ----------- | ----- |
    | Název        | Maximální teplota |
    | Měření | Teplota |
    | Agregace | Maximum |
    | Časové rozmezí  | Poslední 1 týden |

5. Zvolte **Uložit**. Teď na řídicím panelu vidíte dlaždici KPI:

    ![Dlaždice KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Pokud chcete přesunout dlaždici na řídicím panelu nebo změnit její velikost, přesuňte ukazatel myši na dlaždici. Dlaždici můžete přetáhnout na nové umístění nebo změnit její velikost:

    ![Úprava rozložení řídicího panelu](media/tutorial-customize-operator/dashboardlayout.png)

7. Po dokončení změn klikněte na tlačítko **Hotovo**.

## <a name="configure-your-settings-layout"></a>Konfigurace rozložení nastavení

Jako tvůrce můžete také nakonfigurovat zobrazení nastavení zařízení pro operátora. Operátor používá stránku nastavení zařízení ke konfiguraci zařízení. Operátor použije stránku nastavení například k nastavení cílové teploty pro připojenou klimatizaci.

1. Pokud chcete upravit rozložení nastavení pro připojenou klimatizaci, zvolte **Nastavení** a vyberte možnost **Upravit šablonu**:

    ![Stránka Nastavení](media/tutorial-customize-operator/settings.png)

2. Dlaždice nastavení můžete přesunout a můžete také upravit jejich velikost:

    ![Úprava rozložení nastavení](media/tutorial-customize-operator/settingslayout.png)

3. Po dokončení změn klikněte na tlačítko **Hotovo**.

> [!NOTE]
> V režimu **Upravit šablonu** nemůžete upravovat hodnoty nastavení.

## <a name="configure-your-properties-layout"></a>Konfigurace rozložení vlastností

Kromě řídicího panelu a nastavení můžete zobrazení operátora nakonfigurovat také pro vlastnosti zařízení. Operátor používá stránku vlastností zařízení ke správě metadat zařízení. Operátor použije stránku vlastností například k zobrazení sériového čísla zařízení nebo aktualizaci kontaktních údajů pro výrobce.

1. Pokud chcete upravit rozložení vlastností pro připojenou klimatizaci, zvolte **Vlastnosti** a poté vyberte možnost **Upravit šablonu**:

    ![Stránka Vlastnosti](media/tutorial-customize-operator/properties.png)

2. Pole vlastností můžete přesunout a můžete také upravit jejich velikost:

    ![Úprava rozložení vlastností](media/tutorial-customize-operator/propertieslayout.png)

3. Po dokončení změn klikněte na tlačítko **Hotovo**.

> [!NOTE]
> V režimu **Upravit šablonu** nemůžete upravovat hodnoty vlastností.

## <a name="preview-the-device"></a>Zařízení ve verzi Preview

V režimu **Upravit šablonu** můžete přizpůsobit řídicí panel, stránku vlastností a stránku nastavení pro operátora. Pokud nejste v režimu **Upravit šablonu**, můžete aplikaci zobrazit jako operátor.

1. Pokud chcete zobrazit připojené klimatizační zařízení jako operátor, kliknutím na **Done** (Hotovo) ukončete úpravu šablony a vraťte se do zobrazení zařízení pro operátora.

2. Pokud chcete aktualizovat umístění tohoto zařízení, upravte hodnotu na dlaždici s umístěním a potom zvolte **Uložit**:

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

1. Chcete-li přizpůsobit výchozí řídicí panel aplikace, přejděte na **řídicí panel** stránky a klikněte na tlačítko **upravit** v horní části stránky. Knihovna objektů, které lze přidat na řídicí panel se zobrazí panel.

    ![Stránka řídicího panelu](media/tutorial-customize-operator/builderhome.png)

2. Chcete-li přizpůsobit řídicí panel, přidejte dlaždice z **knihovny**. Zvolte **Link** (Odkaz) a přidejte podrobné informace o webu vaší organizace. Potom zvolte **Save** (Uložit):

    ![Přidat odkaz na řídicí panel](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Můžete také přidat odkazy na stránky ve vaší aplikaci Azure IoT Central. Můžete třeba přidat odkaz na řídicí panel zařízení nebo na stránku nastavení.

3. Volitelně můžete zvolit **Image** a nahrát obrázek se zobrazí na řídicím panelu. Obrázek může mít adresu URL, na který můžete procházet po kliknutí:

    ![Přidání obrázku na řídicí panel](media/tutorial-customize-operator/addimage.png)

    Další informace najdete v tématu věnovaném [postupu při přípravě a nahrávání obrázků do aplikace Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Ve verzi Preview řídicí panel

Chcete-li zobrazit řídicí panel aplikací jako operátor náhled, vyberte **provádí** v horní části stránky

![Přepnutí režimu návrhu](media/tutorial-customize-operator/operatorviewhome.png)

Když kliknete na dlaždice s obrázky a odkazy, přejdete na adresy URL, které jste nastavili jako tvůrce.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili, jak přizpůsobit zobrazení aplikace pro operátora.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Konfigurace řídicího panelu zařízení
> * Konfigurace rozložení nastavení zařízení
> * Konfigurace rozložení vlastností zařízení
> * Zobrazení náhledu zařízení jako operátor
> * Konfigurovat výchozí řídicí panel aplikací
> * Ve verzi Preview. výchozí řídicí panel aplikací jako operátor

Teď když jste se naučili, jak přizpůsobit zobrazení aplikace pro operátora, můžete přejít k dalším navrhovaným krokům:

* [Monitorování zařízení (jako operátor)](tutorial-monitor-devices.md)
* [Přidání nového zařízení do aplikace (jako operátor a vývojář zařízení)](tutorial-add-device.md)
