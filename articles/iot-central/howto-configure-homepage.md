---
title: Konfigurace Azure IoT Central řídicí panel aplikací | Dokumentace Microsoftu
description: Jako tvůrce zjistěte, jak nakonfigurovat výchozí Azure IoT Central řídicí panel aplikací.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3168bbbf70c1ffeb3827482459febbcea256eda6
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773142"
---
# <a name="configure-the-application-dashboard"></a>Konfigurace řídicí panel aplikací

**Řídicí panel** je stránka, která načte, když uživatelé, kteří mají přístup k aplikaci přejděte na adresu URL vaší aplikace. Pokud jste vybrali, buď **ukázka Contoso** nebo **ukázka Devkits** aplikace šablony k vytvoření aplikace, vaše aplikace má předdefinovaného řídicího panelu. Pokud jste zvolili **vlastní aplikace** šablonu aplikace, řídicí panel je prázdný.

> [!NOTE]
> Uživatelé mohou také [vytváření vlastních osobních řídicích panelů](howto-personalize-dashboard.md) použít místo výchozí řídicí panel aplikací.

## <a name="add-tiles"></a>Přidat dlaždice

Následující snímek obrazovky ukazuje řídicí panel v aplikaci vytvořenou z **ukázka Contoso** šablony. Chcete-li přizpůsobit výchozí řídicí panel pro vaši aplikaci, vyberte **upravit** v horní části stránky.

![Řídicí panel pro aplikace založené na šabloně "Ukázkové společnosti Contoso"](media/howto-configure-homepage/image1.png)

Výběr **upravit**, otevře se panel Knihovna řídicího panelu. Knihovna obsahuje dlaždice a řídicí panel primitiv, můžete přizpůsobit řídicí panel.

![Knihovna řídicího panelu](media/howto-configure-homepage/image2.png)

Například můžete přidat **nastavení a vlastnosti** dlaždici zobrazíte výběr aktuální hodnoty vlastností a nastavení pro zařízení. Uděláte to tak, nejdřív vyberte **šablona zařízení** vyberte **Instance zařízení**. Po na dlaždici, která poskytnout název a vyberte **nastavení** nebo **vlastnost** k zobrazení. Následující snímek obrazovky ukazuje nastavení a vlastnosti, které se rozhodli přidat dlaždici. Vyberte **provádí** se uložit změnu na řídicí panel.

!["Konfigurace podrobnosti o zařízení" formulář s podrobnostmi o nastavení a vlastnosti](media/howto-configure-homepage/image3.png)

Teď když operátor zobrazí výchozí řídicí panel aplikací, se zobrazí nová dlaždice s **nastavit teploty** nastavení pro zařízení:

![Karta "Řídicí panel" se zobrazené nastavení a vlastnosti pro dlaždici](media/howto-configure-homepage/image4.png)

Můžete prozkoumat další typy dlaždic v knihovně chcete zjistit, jak dále přizpůsobit výchozí řídicí panel aplikací.

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak Azure IoT Central řídicí panel aplikací výchozí konfigurace, můžete:

> [!div class="nextstepaction"]
> [Informace o přípravě a nahrávání imagí](howto-prepare-images.md)
