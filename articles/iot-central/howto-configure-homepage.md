---
title: Nakonfigurovat domovskou stránku aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako tvůrce zjistěte, jak nakonfigurovat domovskou stránku aplikace Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a03ac0ef66f4ffdce53d0bd2a35839bbe1615d0b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199081"
---
# <a name="configuring-homepage"></a>Konfigurace domovské stránky

Domovské stránce je stránka, která načte, když uživatelé, kteří mají přístup k aplikaci přejděte na adresu URL vaší aplikace. Pokud jste vybrali "Ukázkové společnosti Contoso" nebo "Ukázka Devkits" aplikace šablony při vytváření vaší aplikace, aplikace bude mít předdefinované domovské stránky. Pokud jste vybrali na druhé straně šablony aplikace "Aplikace vlastní", bude prázdné vaší domovské stránce.

Například tady je domovská stránka pro aplikace založené na šabloně "Ukázkové společnosti Contoso". Přizpůsobení domovské stránky pro vaši aplikaci, vyberte nejdřív **upravit** v pravém horním rohu. 

![Domovská stránka pro aplikace založené na šabloně "Ukázkové společnosti Contoso"](media/howto-configure-homepage/image1.png)

Výběr **upravit**, knihovna řídicího panelu se otevře do panelu na levé straně. Existuje mnoho typů dlaždice a řídicí panel primitiva, které mohou být přidány k přizpůsobení domovské stránky.

![Knihovna řídicího panelu](media/howto-configure-homepage/image2.png)

Například můžete přidat **nastavením a vlastnostem** dlaždici zobrazíte výběr aktuální hodnoty vlastností a nastavení. Uděláte to tak, nejdřív vyberte **šablona zařízení** vyberte **Instance zařízení**. Po na dlaždici, která poskytnout název a vyberte **nastavení** nebo **vlastnost** k zobrazení. V tomto případě jsme vybrali **nastavit teploty**. Kliknutím na **provádí** způsobí, že tato dlaždice se zobrazí na domovské stránce.

!["Konfigurace podrobnosti o zařízení" formulář s podrobnostmi o nastavení a vlastnosti](media/howto-configure-homepage/image3.png)

Nyní když operátor domovskou stránku, uvidí tuto dlaždici, které zobrazuje vlastnosti nebo nastavení zařízení:

![Karta "Řídicí panel" se zobrazené nastavení a vlastnosti pro dlaždici](media/howto-configure-homepage/image4.png)

Pohrajte si s různými Další dlaždice typy v knihovně se zjistit, jak můžete přizpůsobit domovskou stránku aplikace ještě více.

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak nakonfigurovat domovské stránce Azure IoT Central, můžete:

> [!div class="nextstepaction"]
> [Informace o přípravě a nahrávání imagí](howto-prepare-images.md)
