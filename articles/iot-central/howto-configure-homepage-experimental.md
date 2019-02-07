---
title: Nakonfigurovat domovskou stránku aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako tvůrce zjistěte, jak nakonfigurovat domovskou stránku aplikace Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 599616f046b2eccee009e8a08e8a2f51cf968644
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773224"
---
# <a name="configuring-homepage"></a>Konfigurace domovské stránky

Domovské stránce je stránka, která načte, když uživatelé, kteří mají přístup k aplikaci přejděte na adresu URL vaší aplikace. Pokud jste vybrali "Ukázkové společnosti Contoso" nebo "Ukázka Devkits" aplikace šablony při vytváření vaší aplikace, aplikace bude mít předdefinované domovské stránky. Pokud jste vybrali na druhé straně šablony aplikace "Aplikace vlastní", bude prázdné vaší domovské stránce.

## <a name="add-tiles"></a>Přidat dlaždice

Například tady je domovská stránka pro aplikace založené na šabloně "Ukázkové společnosti Contoso". Přizpůsobení domovské stránky pro vaši aplikaci, vyberte nejdřív **upravit** v pravém horním rohu. 

![Domovská stránka pro aplikace založené na šabloně "Ukázkové společnosti Contoso"](media/howto-configure-homepage-experimental/image1.png)

Výběr **upravit**, knihovna řídicího panelu se otevře do panelu na levé straně. Existuje mnoho typů dlaždice a řídicí panel primitiva, které mohou být přidány k přizpůsobení domovské stránky.

![Knihovna řídicího panelu](media/howto-configure-homepage-experimental/image2.png)

Například můžete přidat **nastavením a vlastnostem** dlaždici zobrazíte výběr aktuální hodnoty vlastností a nastavení. Uděláte to tak, nejdřív vyberte **šablona zařízení** vyberte **Instance zařízení**. Po na dlaždici, která poskytnout název a vyberte **nastavení** nebo **vlastnost** k zobrazení. V tomto případě jsme vybrali **nastavit teploty**. Kliknutím na **provádí** způsobí, že tato dlaždice se zobrazí na domovské stránce.

!["Konfigurace podrobnosti o zařízení" formulář s podrobnostmi o nastavení a vlastnosti](media/howto-configure-homepage-experimental/image3.png)

Nyní když operátor domovskou stránku, uvidí tuto dlaždici, které zobrazuje vlastnosti nebo nastavení zařízení:

![Karta "Řídicí panel" se zobrazené nastavení a vlastnosti pro dlaždici](media/howto-configure-homepage-experimental/image4.png)

Pohrajte si s různými Další dlaždice typy v knihovně se zjistit, jak můžete přizpůsobit domovskou stránku aplikace ještě více.

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak nakonfigurovat domovské stránce Azure IoT Central, můžete:

> [!div class="nextstepaction"]
> [Informace o přípravě a nahrávání imagí](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
