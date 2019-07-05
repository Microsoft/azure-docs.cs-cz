---
title: Použití zařízení nastaví v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Jakožto Obsluha nastaví použití zařízení v aplikaci Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: e1e7b91e0808b9e23e653acd43b95f24a46c7d27
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503206"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Pomocí sady zařízení ve vaší aplikaci Azure IoT Central

Tento článek popisuje, jak, jakožto obsluha používat zařízení nastaví v aplikaci Azure IoT Central.

Sada zařízení je seznam zařízení, které jsou seskupeny vzhledem k tomu, aby odpovídaly některé zadaným kritériím. Nastaví zařízení pomáhají spravovat, vizualizovat a analyzovat zařízení ve velkém měřítku pomocí seskupení zařízení do menších logické skupiny. Můžete například vytvořit zařízení nastavit na seznam všech zařízení klimatizace v Praze umožňující technika najít zařízení, pro které jsou zodpovědné. V tomto článku se dozvíte, jak vytvořit a nakonfigurovat sady zařízení.

## <a name="create-a-device-set"></a>Vytvoření sady zařízení

Vytvořit sadu zařízení:

1. Zvolte **sady zařízení** v levé navigační nabídce.

1. Vyberte **+ nový**.

    ![Nová sada zařízení](media/howto-use-device-sets/image1.png)

1. Pojmenujte svou sadu zařízení, které jsou jedinečné v celé aplikaci. Můžete také přidat popis. Sada zařízení může obsahovat jenom zařízení ze šablony jedno zařízení. Výběr šablony zařízení pro tuto sadu.

1. Vytvořte dotaz pro účely identifikace zařízení pro zařízení nastavit tak, že vyberete vlastnost, relační operátor a hodnotu. Můžete přidat více dotazů a zařízení, která splňují **všechny** kritéria jsou umístěny do sady zařízení. Sadu zařízení, které vytvoříte je přístupný každému, kdo má přístup k aplikaci, aby všem uživatelům zobrazit, upravit nebo odstranit sadu zařízení.

    ![V zařízení nastavené dotazu](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Sada zařízení je dynamický dotaz. Pokaždé, když se podíváte seznam zařízení, může být různá zařízení v seznamu. Seznam závisí na zařízení, která aktuálně splňují kritéria dotazu.

1. Zvolte **Uložit**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurace řídicího panelu pro vaši sadu zařízení

Po vytvoření sady zařízení, můžete nakonfigurovat jeho **řídicí panel**. **Řídicí panel** je domovská stránka, kde umístit obrázky a odkazy. Můžete také přidat mřížky, které se seznam zařízení v sadě zařízení.

1. Zvolte **sady zařízení** v levé navigační nabídce.

1. Vyberte vaši sadu zařízení.

1. Vyberte kartu **Řídicí panel** .

1. Vyberte **Upravit**.

    ![Režim návrhu](media/howto-use-device-sets/image3.png)

1. Informace o přidání obrázku, naleznete v tématu [Prepare a nahrávání imagí do aplikace Azure IoT Central](howto-prepare-images.md).

1. Přidání dlaždice odkaz:
    1. Zvolte **odkaz** v pravém podokně.
    1. Zadejte odkaz **Title**.
    1. Zvolte adresu URL otevřít při výběru odkazu.
    1. Zadejte popis, který zobrazuje následující odkaz **Title**.
    1. Zvolte **Uložit**.

        ![Uložit odkaz](media/howto-use-device-sets/image7.png)

    1. Můžete přesunutí a změna velikosti dlaždice odkaz na **řídicí panel**.

1. Přidáte mřížku. Mřížka je tabulka zařízení na zařízení, nastavit pomocí sloupce, které zvolíte.
    1. Zvolte **mřížky** v pravém podokně.
    1. Poskytují vaší mřížkou **Title**.
    1. Vyberte sloupce, které chcete zobrazit výběrem **přidat nebo odebrat**. V panelu, která se otevře zvolte sloupec má a klikněte na šipku doprava a vyberte ji.
    1. Zvolte **OK**.
    1. Zvolte **Uložit**.

        ![Ukládat mřížka](media/howto-use-device-sets/image9.png)

    1. Přetáhnout myší na tabulku a umístěte ho na **řídicí panel**.

        > [!NOTE]
        > Můžete přidat více imagí, odkazy a mřížky.
  
    1. Vyberte **Done** (Hotovo).

Další informace o tom, jak používat dlaždice v Azure IoT Central, naleznete v tématu [pomocí dlaždice řídicího panelu](howto-use-tiles.md).

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>Na řídicím panelu sady zařízení nakonfigurovat Mapa umístění

Můžete přidat mapu, která bude vizualizovat umístění zařízení ve vaší sadě zařízení.

Pokud chcete přidat mapu, která vaše zařízení sady řídicí panel, musíte nakonfigurovat měření umístění nebo vlastnost umístění ve vaší šabloně zařízení. Další informace najdete v tématu [vytvořit umístění měření](howto-set-up-template.md) nebo [vytvoření vlastnosti umístění](howto-set-up-template.md).

1. Na vašem zařízení nastavit **řídicí panel**vyberte **mapy** z knihovny.
2. Přidejte název a zvolte umístění měření nebo vlastnost, kterou jste nakonfigurovali dříve.
3. Vyberte **Uložit** a mapovou dlaždici zobrazí poslední známé umístění zařízení ve vaší sadě zařízení.
4. Operátor zobrazení řídicího panelu sady zařízení, operátor, který se zobrazí všechny dlaždice, které jste nakonfigurovali, včetně Mapa umístění.

Mapovou dlaždici na řídicím panelu můžete změnit velikost. Výběr špendlíku na mapě zobrazí informace o zařízení, název a umístění. Vyberte automaticky otevírané okno, přejděte na stránku vlastností zařízení.

## <a name="configure-the-list-for-your-device-set"></a>Proveďte konfiguraci seznamu pro vaši sadu zařízení

Po vytvoření sady zařízení, můžete nakonfigurovat **seznamu**. **Seznamu** zobrazuje všechna zařízení na zařízení nastavit v tabulce se sloupci zvolíte.

1. Zvolte **sady zařízení** v levé navigační nabídce.

1. Zvolte **seznamu** kartu.

1. Zvolte **možnosti sloupce**.

    ![Možnosti sloupce](media/howto-use-device-sets/image11.png)

1. Vyberte sloupce, které má být zobrazen výběrem sloupce, který chcete zobrazit a klepnutím na šipku vpravo a vyberte ji.

    ![Volba sloupce](media/howto-use-device-sets/image12.png)

1. Zvolte **OK**.

## <a name="analytics"></a>Analýzy

Analýzy ve skupinách zařízení je stejný jako hlavní analytics kartu v levé navigační nabídce. Další informace o analýze v článku na [vytvoření analytics](howto-use-device-sets.md).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak pomocí sady zařízení ve vaší aplikaci Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Vytvoření pravidla telemetrie](howto-create-telemetry-rules.md)
