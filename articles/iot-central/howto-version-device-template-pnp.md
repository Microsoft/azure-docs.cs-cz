---
title: Principy správy verzí šablon zařízení pro aplikace Azure IoT Central | Microsoft Docs
description: Iterujte na šablonách zařízení vytvořením nových verzí a bez dopadu na živá zařízení připojená k síti.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ac2af605859d9555cc1c165afac4ca5e7ba343cc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879653"
---
# <a name="create-a-new-device-template-version-preview-features"></a>Vytvořit novou verzi šablony zařízení (funkce verze Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central umožňuje rychlý vývoj aplikací IoT. Můžete rychle iterovat v návrzích šablon zařízení přidáním, úpravou nebo odstraněním možností zařízení, zobrazení a přizpůsobení. Po publikování šablony zařízení se v modelu schopností zařízení zobrazí jako **publikované** s ikonami zámku vedle modelu. Aby bylo možné provádět změny v modelu schopností zařízení, budete muset vytvořit novou verzi šablony zařízení. V současné době se vlastnosti cloudu, přizpůsobení a zobrazení dají kdykoli upravovat, aniž byste museli verzi šablony zařízení potřebovat. Po uložení kterékoli z těchto změn můžete šablonu zařízení publikovat, aby se k dispozici nejnovější změny pro obsluhu, aby bylo možné zobrazit v Device Explorer.

> [!NOTE]
> Další informace o tom, jak vytvořit šablonu zařízení, najdete v tématu [nastavení a Správa šablony zařízení](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Přidání přizpůsobení do šablony zařízení bez správy verzí

Některé prvky možností zařízení je možné upravovat, aniž byste museli mít k disverzi šablonu a rozhraní pro vaše zařízení. Mezi tato pole patří například zobrazované jméno, sémantický typ, minimální hodnota, maximální hodnota, desetinná místa, barva, jednotka, zobrazovaná jednotka, komentář a popis. Přidání jednoho z těchto přizpůsobení:

1. Přejít na stránku **šablony zařízení** .
1. Vyberte šablonu zařízení, kterou chcete přizpůsobit.
1. Klikněte na kartu **přizpůsobit** .
1. Tady jsou uvedené všechny funkce definované v modelu schopností zařízení. Všechna pole, která můžete upravit tady, se dají uložit a používat napříč vaší aplikací, aniž byste museli verzi šablony zařízení použít. Pokud existují pole, která chcete upravit, jsou jen pro čtení, budete muset verzi šablony zařízení změnit. Vyberte pole, které chcete upravit, a zadejte nové hodnoty.
1. Klikněte na **Uložit**. Tyto hodnoty teď přepíšou vše, co se původně uložilo v šabloně zařízení a použije se v rámci aplikace.

## <a name="versioning-a-device-template"></a>Správa verzí šablony zařízení

Vytvořením nové verze šablony zařízení se vytvoří koncept verze šablony, kde můžete upravit model schopností zařízení. Všechna publikovaná rozhraní zůstanou publikovaná, dokud nebudou samostatně nahlášená verze. Aby bylo možné upravit publikované rozhraní, je třeba nejprve vytvořit novou verzi šablony zařízení.

Při pokusu o úpravu části modelu schopností zařízení, kterou nemůžete upravit v části vlastní nastavení v šabloně zařízení, by měla být šablona zařízení jenom ve verzi. 

Chcete-li pořídit verzi šablony zařízení:

1. Přejít na stránku **šablony zařízení** .
1. Vyberte šablonu zařízení, u které se pokoušíte o verzi.
1. V horní části stránky klikněte na tlačítko **verze** a zadejte nový název šablony. Navrhli jsme nový název, který se dá upravit.
1. Klikněte na možnost **Vytvořit**.
1. Šablona zařízení je teď v režimu konceptu. Uvidíte, že vaše rozhraní jsou pořád uzamčená a je nutné je upravovat individuálně. 

### <a name="versioning-an-interface"></a>Správa verzí rozhraní

Správa verzí rozhraní umožňuje přidat, aktualizovat a odebrat funkce v rozhraní, které jste už vytvořili. 

Aby bylo možné verzi rozhraní:

1. Přejít na stránku **šablony zařízení** .
1. Vyberte šablonu zařízení, kterou máte v režimu konceptu.
1. Vyberte rozhraní, které je v publikovaném režimu, ve kterém chcete verzi upravit.
1. Klikněte na tlačítko **verze** v horní části stránky rozhraní. 
1. Klikněte na možnost **Vytvořit**.
1. Rozhraní je nyní v režimu konceptu. Do svého rozhraní budete moct přidávat nebo upravovat možnosti bez přerušení stávajících úprav a zobrazení. 

> [!NOTE]
> Standardní rozhraní publikovaná pomocí Azure IoT se nedají vyměnit verze ani upravovat. Tato standardní rozhraní se používají pro certifikaci zařízení.

> [!NOTE]
> Po publikování rozhraní nemůžete odstranit žádnou z jeho funkcí i v režimu konceptu. Možnosti lze upravovat nebo přidávat pouze do rozhraní v režimu konceptu.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrace zařízení napříč verzemi šablon zařízení

Můžete vytvořit několik verzí šablony zařízení. V průběhu času budete mít pomocí těchto šablon zařízení více připojených zařízení. Zařízení můžete migrovat z jedné verze šablony zařízení do jiné. Následující postup popisuje, jak migrovat zařízení:

1. Přejít na stránku **Device Explorer** .
1. Vyberte zařízení, které potřebujete migrovat na jinou verzi.
1. Klikněte na tlačítko **migrovat**.
1. Vyberte šablonu zařízení s číslem verze, na které chcete zařízení migrovat, a zvolte **migrovat**.

![Postup migrace zařízení](media/howto-version-device-template-pnp/pick-version.png)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili používat verze šablon zařízení ve vaší aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Jak vytvořit pravidla telemetrie](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
