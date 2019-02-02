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
ms.openlocfilehash: f65463b534988e0a721a1a5f816183f8dd8ebcaf
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657650"
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
> * Konfigurace výchozí domovské stránky
> * Zobrazení náhledu výchozí domovské stránky jako operátor

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

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Zobrazení náhledu připojeného klimatizačního zařízení jako operátor

V režimu **Upravit šablonu** můžete přizpůsobit řídicí panel, stránku vlastností a stránku nastavení pro operátora. Pokud nejste v režimu **Upravit šablonu**, můžete aplikaci zobrazit jako operátor.

1. Pokud chcete zobrazit připojené klimatizační zařízení jako operátor, kliknutím na **Done** (Hotovo) ukončete úpravu šablony a vraťte se do zobrazení zařízení pro operátora.

2. Pokud chcete aktualizovat umístění tohoto zařízení, upravte hodnotu na dlaždici s umístěním a potom zvolte **Uložit**:

    ![Úprava nastavení vlastnosti](media/tutorial-customize-operator/editproperty.png)

3. Pokud chcete připojené klimatizaci odeslat nastavení, zvolte **Settings** (Nastavení),změňte hodnotu nastavení na dlaždici a potom zvolte **Update** (Aktualizovat):

    ![Odesílání nastavení do zařízení](media/tutorial-customize-operator/sendsetting.png)

    Jakmile zařízení rozpozná změnu nastavení, nastavení se na dlaždici zobrazí jako **synced** (Synchronizováno).

4. Jako operátor můžete zobrazit řídicí panel zařízení tak, jak ho nakonfiguroval tvůrce:

    ![Zobrazení řídicího panelu zařízení pro operátora](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Konfigurace výchozí domovské stránky

Když tvůrce nebo operátor přihlásí k aplikaci Azure IoT Central, uvidí domovskou stránku. Jako tvůrce můžete nakonfigurovat obsah této domovské stránky tak, aby zahrnovala obsah, který je pro operátory nejužitečnější a nejrelevantnější.

1. Pokud chcete přizpůsobit výchozí domovskou stránku, přejděte na stránku **Home** a v pravé horní části stránky vyberte **Upravit**. Po zvolení možnosti **Upravit** se zprava vysune seznam objektů, které můžete přidat na vaši domovskou stránku.

    ![Stránka Application Builder (Tvůrce aplikací)](media/tutorial-customize-operator/builderhome.png)

2. Pokud chcete přizpůsobit na domovskou stránku, přidejte dlaždice z **knihovny**. Zvolte **Link** (Odkaz) a přidejte podrobné informace o webu vaší organizace. Potom zvolte **Save** (Uložit):

    ![Přidání odkazu na domovskou stránku](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Můžete také přidat odkazy na stránky ve vaší aplikaci Azure IoT Central. Můžete třeba přidat odkaz na řídicí panel zařízení nebo na stránku nastavení.

3. Volitelně můžete zvolit **Image** (Obrázek) a nahrát obrázek, který se zobrazí na vaší domovské stránce. Obrázek může mít adresu URL, na kterou přejdete po kliknutí na něj:

    ![Přidání obrázku na domovskou stránku](media/tutorial-customize-operator/addimage.png)

    Další informace najdete v tématu věnovaném [postupu při přípravě a nahrávání obrázků do aplikace Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-default-home-page-as-an-operator"></a>Zobrazení náhledu výchozí domovské stránky jako operátor

Pokud chcete zobrazit náhled domovské stránky tak, jak ji uvidí operátor, a nechcete ji upravovat, zvolte **Hotovo** v pravé horní části stránky

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
> * Konfigurace výchozí domovské stránky
> * Zobrazení náhledu výchozí domovské stránky jako operátor

Teď když jste se naučili, jak přizpůsobit zobrazení aplikace pro operátora, můžete přejít k dalším navrhovaným krokům:

* [Monitorování zařízení (jako operátor)](tutorial-monitor-devices.md)
* [Přidání nového zařízení do aplikace (jako operátor a vývojář zařízení)](tutorial-add-device.md)
