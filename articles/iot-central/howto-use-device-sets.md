---
title: Použití zařízení nastaví v aplikaci Azure IoT centrální | Microsoft Docs
description: Jako operátor jak používat zařízení nastaví v Azure IoT centrální aplikaci.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: d27e687acf62e4a1e96f1bb49607618dff45512b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628500"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Pomocí sady zařízení ve vaší aplikaci Azure IoT centrální

Tento článek popisuje, jak, jakožto Obsluha k použití zařízení nastaví v aplikaci Microsoft Azure IoT centrální.

Sada zařízení je seznam zařízení, které jsou seskupeny dohromady, protože všechny odpovídají některé zadaná kritéria. Zařízení nastaví pomáhají spravovat, vizualizovat a analyzovat zařízení tím, že zařízení do menších, logických skupin. Například můžete vytvořit seznam všech zařízení klimatizace v Praze, chcete-li povolit technika Praha najít všechna zařízení, pro které je zodpovědná. Tento článek ukazuje, jak vytvořit a nakonfigurovat zařízení sady.

## <a name="create-a-device-set"></a>Vytvoření sady zařízení

Chcete-li vytvořit sadu zařízení:

1. Zvolte **sady zařízení** v levé navigační nabídce.

1. Klikněte na **+ Nový**.

    ![Nová sada zařízení](media/howto-use-device-sets/image1.png)

1. Zadejte název, který je jedinečný v rámci celé aplikace sada vaše zařízení. Můžete také přidat popis. Sada zařízení může obsahovat jenom zařízení ze šablony jedno zařízení. Vyberte šablonu použitou pro tuto sadu na zařízení.

1. Vytvořte dotaz k identifikaci zařízení pro zařízení nastavit tak, že vyberete vlastnost, relační operátor a hodnotu. Můžete přidat více dotazy a zařízení, která splňují **všechny** kritéria jsou umístěny v sadě zařízení. Sada zařízení, které vytvoříte je přístupný pro každý, kdo má přístup k aplikaci, takže každý, kdo může zobrazit, upravit nebo odstranit sadu zařízení.

    ![Zařízení nastavit dotazu](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > Sada zařízení je dynamická dotazu. Pokaždé, když se zobrazí seznam zařízení, může být různých zařízení v seznamu. V seznamu, závisí na zařízení, která aktuálně splňují kritéria dotazu.

1. Zvolte **Uložit**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Konfigurace řídicí panel pro vaše zařízení sadu

Po vytvoření sady zařízení, můžete nakonfigurovat jeho **řídicí panel**. **Řídicí panel** je na domovskou stránku, kde můžete umístit obrázky a odkazy. Můžete také přidat mřížky, které se zobrazí seznam zařízení v sadě zařízení.

1. Zvolte **sady zařízení** v levé navigační nabídce.

1. Vyberte **řídicí panel** kartě.

1. Zapnout **návrh režimu**.

    ![Režim návrhu](media/howto-use-device-sets/image3.png)

1. Informace o přidání obrázku najdete v tématu [Příprava a nahrajte Image aplikace Azure IoT centrální](howto-prepare-images.md).

1. Přidáte dlaždice odkaz:
    1. Zvolte **odkaz** v pravém podokně.

        ![Vyberte propojení](media/howto-use-device-sets/image6.png)

    1. Zadejte odkaz na vaši **název**.
    1. Vyberte adresu URL k otevření při kliknutí na odkaz.
    1. Zadejte popis, který ukazuje níže odkaz na vaši **název**.
    1. Zvolte **Uložit**.

        ![Uložit odkaz](media/howto-use-device-sets/image7.png)

    1. Můžete změnit umístění a velikost na dlaždici odkaz **řídicí panel**.

1. Přidáte mřížku. Mřížka je tabulka zařízení na zařízení s sloupce, které zvolíte.
    1. Zvolte **mřížky** v pravém podokně.

        ![Zvolte mřížky](media/howto-use-device-sets/image8.png)

    1. Poskytnout vaše mřížky **název**.
    1. Vyberte sloupce, které chcete zobrazit kliknutím na tlačítko nastavení. V panelu, která se objeví vyberte sloupec mají zobrazit a vybrat na šipku vpravo a vyberte ji.
    1. Vyberte **OK**.
    1. Zvolte **Uložit**.

        ![Uložit mřížka](media/howto-use-device-sets/image9.png)

    1. Přetáhnout myší na tabulku a umístěte ji na **řídicí panel**.

    > [!NOTE]
    > Můžete přidat více bitových kopií, odkazy a mřížky.

1. Vypnout **režimu návrhu**.

    ![Návrh režimu mimo](media/howto-use-device-sets/image10.png)

## <a name="configure-the-list-for-your-device-set"></a>Konfigurace seznamu pro vaše zařízení sadu

Po vytvoření sady zařízení, můžete nakonfigurovat **seznamu**. **Seznamu** zobrazí všechna zařízení v zařízení nastavit v tabulce s sloupce, které zvolíte.

1. Zvolte **sady zařízení** v levé navigační nabídce.

1. Vyberte **seznamu** kartě.

1. Zvolte **sloupec možnosti**.

    ![Možnosti sloupce](media/howto-use-device-sets/image11.png)

1. Vyberte sloupce, které chcete zobrazit výběrem sloupec, který chcete zobrazit a vybrat na šipku vpravo a vyberte ji.

    ![Vyberte sloupce](media/howto-use-device-sets/image12.png)

1. Vyberte **OK**.

## <a name="analytics"></a>Analýzy

Analýza v sadách zařízení je stejný jako kartě Hlavní analýzy v levé navigační nabídce. Další informace o analýzy v článku na [vytvoření analytics](howto-create-analytics.md).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili způsob použití sady zařízení v aplikaci Azure IoT centrální, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Postup vytvoření pravidla telemetrie](howto-create-telemetry-rules.md)
