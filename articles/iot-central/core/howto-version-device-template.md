---
title: Principy správy verzí šablon zařízení pro vaše aplikace Azure IoT Central | Dokumenty společnosti Microsoft
description: Itererate přes vaše zařízení šablony vytvořením nových verzí a bez dopadu na vaše live připojená zařízení
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 37c7bc99881c8d1106c8464cfe18c9e63b8a1b02
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756733"
---
# <a name="create-a-new-device-template-version"></a>Vytvoření nové verze šablony zařízení

*Tento článek se vztahuje na tvůrce řešení a vývojáře zařízení.*

Azure IoT Central umožňuje rychlý vývoj aplikací IoT. Návrhy šablon zařízení můžete rychle itorovat přidáním, úpravami nebo odstraněním možností, zobrazení a vlastních nastavení zařízení. Po publikování šablony zařízení se model schopností zařízení zobrazí jako **Publikováno** s ikonami zámku vedle modelu. Chcete-li provést změny v modelu schopností zařízení, budete muset vytvořit novou verzi šablony zařízení. Mezitím vlastnosti cloudu, vlastní nastavení a zobrazení lze kdykoli upravit bez nutnosti verze šablony zařízení. Po uložení některé z těchto změn můžete publikovat šablonu zařízení a zpřístupnit tak nejnovější změny, které může operátor zobrazit v Průzkumníkovi zařízení.

> [!NOTE]
> Další informace o tom, jak vytvořit šablonu zařízení, najdete v [tématu Nastavení a správa šablony zařízení](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>Přidání vlastního nastavení do šablony zařízení bez správy verzí

Některé prvky funkcí vašeho zařízení lze upravovat bez nutnosti verzí šablony zařízení a rozhraní. Některá z těchto polí například zahrnují zobrazovaný název, sémantický typ, minimální hodnotu, maximální hodnotu, desetinná místa, barvu, jednotku, zobrazovací jednotku, komentář a popis. Přidání jednoho z těchto úprav:

1. Přejděte na stránku **Šablony zařízení.**
1. Vyberte šablonu zařízení, kterou chcete přizpůsobit.
1. Zvolte kartu **Přizpůsobit.**
1. Zde budou uvedeny všechny funkce definované v modelu schopností zařízení. Všechna pole, která zde můžete upravit, lze uložit a použít v celé aplikaci, aniž byste museli používat šablonu zařízení. Pokud existují pole, která chcete upravit a která jsou jen pro čtení, budete muset šablonu zařízení použít k jejich změně. Vyberte pole, které chcete upravit, a zadejte nové hodnoty.
1. Klikněte na **Uložit**. Nyní tyto hodnoty přepíší vše, co bylo původně uloženo v šabloně zařízení a bude použito v celé aplikaci.

## <a name="versioning-a-device-template"></a>Správa verzí šablony zařízení

Vytvořením nové verze šablony zařízení vytvoříte koncept šablony, ve které lze upravit model schopností zařízení. Všechna publikovaná rozhraní zůstanou publikována, dokud nebudou individuálně vydána verze. Chcete-li upravit publikované rozhraní, musíte nejprve vytvořit novou verzi šablony zařízení.

Šablona zařízení by měla být verzí pouze v případě, že se pokoušíte upravit část modelu schopností zařízení, kterou nelze upravit v části vlastního nastavení šablony zařízení. 

Chcete-li verzi šablony zařízení:

1. Přejděte na stránku **Šablony zařízení.**
1. Vyberte šablonu zařízení, kterou se pokoušíte vytvořit verzi.
1. Klikněte na tlačítko **Verze** v horní části stránky a dejte šabloně nový název. Navrhli jsme pro vás nový název, který lze upravit.
1. Klikněte na **Vytvořit**.
1. Nyní je šablona zařízení v režimu konceptu. Uvidíte, že vaše rozhraní jsou stále uzamčena a musí být individuálně verzí, aby byla upravena. 

### <a name="versioning-an-interface"></a>Správa verzí rozhraní

Správa verzí rozhraní umožňuje přidávat, aktualizovat a odebírat možnosti uvnitř rozhraní, které jste již vytvořili. 

Aby bylo možné vytvořit rozhraní:

1. Přejděte na stránku **Šablony zařízení.**
1. Vyberte šablonu zařízení, kterou máte v režimu konceptu.
1. Vyberte rozhraní, které je v publikovaném režimu, které chcete verzi a upravit.
1. Klikněte na tlačítko **Verze** v horní části stránky rozhraní. 
1. Klikněte na **Vytvořit**.
1. Nyní je rozhraní v režimu konceptu. Budete moci přidávat nebo upravovat možnosti rozhraní bez porušení stávajících vlastních nastavení a zobrazení. 

> [!NOTE]
> Standardní rozhraní publikovaná Azure IoT nelze verzí nebo upravit. Tato standardní rozhraní se používají pro certifikaci zařízení.

> [!NOTE]
> Jakmile je rozhraní bylo publikováno, nelze odstranit žádné z jeho schopností i v režimu konceptu. Možnosti lze upravovat nebo přidávat do rozhraní pouze v režimu konceptu.


## <a name="migrate-a-device-across-device-template-versions"></a>Migrace zařízení mezi verzemi předloh zařízení

Můžete vytvořit více verzí šablony zařízení. V průběhu času budete mít více připojených zařízení pomocí těchto šablon zařízení. Zařízení můžete migrovat z jedné verze šablony zařízení do jiné. Následující kroky popisují, jak migrovat zařízení:

1. Přejděte na stránku **Průzkumník zařízení.**
1. Vyberte zařízení, které potřebujete k migraci na jinou verzi.
1. Zvolte **Migrovat**.
1. Vyberte šablonu zařízení s číslem verze, do které chcete zařízení migrovat, a zvolte **Migrovat**.

![Jak migrovat zařízení](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Další kroky

Pokud jste vývojář zařízení, dalším krokem je přečíst si o [zařízeních Azure IoT Edge a Azure IoT Central](./concepts-iot-edge.md).
