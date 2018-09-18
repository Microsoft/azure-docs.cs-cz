---
title: Použití zařízení nastaví v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Jakožto Obsluha nastaví použití zařízení v aplikaci Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 28706ad77f48ae826b621ebdd920d26f3b87178a
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731970"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Pomocí sady zařízení ve vaší aplikaci Azure IoT Central

Tento článek popisuje, jak, jakožto obsluha používat zařízení nastaví v aplikaci Microsoft Azure IoT Central.

Sada zařízení je seznam zařízení, které jsou seskupeny vzhledem k tomu, aby všechny odpovídaly některé zadaným kritériím. Nastaví zařízení pomáhají spravovat, vizualizovat a analyzovat zařízení ve velkém měřítku pomocí seskupení zařízení do menších logické skupiny. Například v Praze umožňující technika Seattle najít všechna zařízení, pro které je zodpovědný vytvořit seznam všech zařízení klimatizace. V tomto článku se dozvíte, jak vytvořit a nakonfigurovat sady zařízení.

## <a name="create-a-device-set"></a>Vytvoření sady zařízení

Vytvořit sadu zařízení:

1. Zvolte **sady zařízení** v levé navigační nabídce.

1. Klikněte na **+ Nový**.

    ![Nová sada zařízení](media/howto-use-device-sets/image1.png)

1. Pojmenujte svou sadu zařízení, které jsou jedinečné v celé aplikaci. Můžete také přidat popis. Sada zařízení může obsahovat jenom zařízení ze šablony jedno zařízení. Výběr šablony zařízení pro tuto sadu.

1. Vytvořte dotaz pro účely identifikace zařízení pro zařízení nastavit tak, že vyberete vlastnost, relační operátor a hodnotu. Můžete přidat více dotazů a zařízení, která splňují **všechny** kritéria jsou umístěny do sady zařízení. Sadu zařízení, které vytvoříte je přístupný každému, kdo má přístup k aplikaci, aby všem uživatelům zobrazit, upravit nebo odstranit sadu zařízení.

    ![V zařízení nastavené dotazu](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Sada zařízení je dynamický dotaz. Pokaždé, když se podíváte seznam zařízení, může být různá zařízení v seznamu. Seznam závisí na zařízení, která aktuálně splňují kritéria dotazu.

1. Zvolte **Uložit**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurace řídicího panelu pro vaši sadu zařízení

Po vytvoření sady zařízení, můžete nakonfigurovat jeho **řídicí panel**. **Řídicí panel** je domovská stránka, kde můžete umístit obrázky a odkazy. Můžete také přidat mřížky, které se seznam zařízení v sadě zařízení.

1. Zvolte **sady zařízení** v levé navigační nabídce.

1. Zvolte **řídicí panel** kartu.

1. Klikněte na tlačítko **upravit šablonu**.

    ![Režim návrhu](media/howto-use-device-sets/image3.png)

1. Informace o přidání obrázku, naleznete v tématu [Prepare a nahrávání imagí do aplikace Azure IoT Central](howto-prepare-images.md).

1. Přidání dlaždice odkaz:
    1. Zvolte **odkaz** v pravém podokně.
    1. Zadejte odkaz **Title**.
    1. Zvolte adresu URL otevřít po kliknutí na odkaz.
    1. Zadejte popis, který zobrazuje následující odkaz **Title**.
    1. Zvolte **Uložit**.

        ![Uložit odkaz](media/howto-use-device-sets/image7.png)

    1. Můžete přesunutí a změna velikosti dlaždice odkaz na **řídicí panel**.

1. Přidáte mřížku. Mřížka je tabulka zařízení na zařízení, nastavit pomocí sloupce, které zvolíte.
    1. Zvolte **mřížky** v pravém podokně.

        ![Zvolte mřížky](media/howto-use-device-sets/image8.png)

    1. Poskytují vaší mřížkou **Title**.
    1. Vyberte sloupce, které chcete zobrazit kliknutím na tlačítko nastavení. V panelu, která se otevře zvolte sloupec má a klikněte na šipku doprava a vyberte ji.
    1. Vyberte **OK**.
    1. Zvolte **Uložit**.

        ![Ukládat mřížka](media/howto-use-device-sets/image9.png)

    1. Přetáhnout myší na tabulku a umístěte ho na **řídicí panel**.

    > [!NOTE]
    > Můžete přidat více imagí, odkazy a mřížky.
  
    1. Klikněte na **Done** (Hotovo).

    ![Návrh režimu mimo](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Konfigurace mapování umístění vašeho zařízení nastaví řídicí panel 
Můžete přidat mapu umístění, která bude vizualizovat umístění zařízení nastaví v objektu Map.

Pokud chcete přidat mapu umístění, která bude zařízení nastaví řídicí panel, musíte mít vlastnost nakonfigurovaného umístění ve vaší šabloně zařízení, najdete v článku [vytvoření vlastnosti umístění používá technologii Azure Maps](howto-set-up-template.md).


1. Na zařízení nastavit řídicím panelu vyberte mapování z knihovny.

    ![Nastaví mapy řídicího panelu zařízení](media/howto-use-device-sets/LocationMaps1.png)

2. Zadejte název a vyberte vlastnost umístění, které jste dříve nakonfigurovali v rámci vlastnosti zařízení.
3. Uložit a můžete se zobrazí na mapě dlaždici zobrazení umístění zařízení v nastavení zařízení.
4. Teď když operátor řídicí panel sady zařízení, tak může vidět všechny dlaždice jste nakonfigurovali, včetně umístění mapu, která bude vizualizovat všechna umístění zařízení na první pohled! 
    
[!NOTE] Budete moct změnit velikost mapování na požadovanou velikost. Kliknutím na kód pin na mapě zobrazíte informace o zařízení, název a umístění. Můžete kliknout na automaticky otevírané okno, přejděte na stránku vlastností zařízení.  


## <a name="configure-the-list-for-your-device-set"></a>Proveďte konfiguraci seznamu pro vaši sadu zařízení

Po vytvoření sady zařízení, můžete nakonfigurovat **seznamu**. **Seznamu** zobrazuje všechna zařízení na zařízení nastavit v tabulce se sloupci zvolíte.

1. Zvolte **sady zařízení** v levé navigační nabídce.

1. Zvolte **seznamu** kartu.

1. Zvolte **možnosti sloupce**.

    ![Možnosti sloupce](media/howto-use-device-sets/image11.png)

1. Vyberte sloupce, které má být zobrazen výběrem sloupce, který chcete zobrazit a klepnutím na šipku vpravo a vyberte ji.

    ![Volba sloupce](media/howto-use-device-sets/image12.png)

1. Vyberte **OK**.

## <a name="analytics"></a>Analýzy

Analýzy ve skupinách zařízení je stejný jako hlavní analytics kartu v levé navigační nabídce. Další informace o analýze v článku na [vytvoření analytics](howto-create-analytics.md).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak pomocí sady zařízení ve vaší aplikaci Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Vytvoření pravidla telemetrie](howto-create-telemetry-rules.md)
