---
title: Migrace aplikace IoT Central Azure pro v2 na V3 | Microsoft Docs
description: Jako správce se dozvíte, jak migrovat aplikaci IoT Central Azure Azure do verze v3.
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 735ad7ad9ded6baded59ab3f08e239d1c8376b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702721"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Migrace aplikace IoT Central v2 na V3

*Tento článek se týká správců.*

V současné době se při vytváření nové aplikace IoT Central jedná o aplikaci v3. Pokud jste dříve vytvořili aplikaci, může být v závislosti na době jejího vytvoření v2. Tento článek popisuje, jak migrovat v2 do aplikace v3, abyste měli jistotu, že používáte nejnovější funkce IoT Central.

Informace o tom, jak identifikovat verzi IoT Central aplikace, najdete v tématu [o vaší aplikaci](howto-get-app-info.md).

Postup migrace aplikace z verze V2 na v3 je následující:

1. Vytvoří novou aplikaci V3 z aplikace V2.
1. Nakonfigurujte aplikaci v3.
1. Odstranit do aplikace V2.

## <a name="create-a-new-v3-application"></a>Vytvoření nové aplikace V3

Pomocí Průvodce migrací vytvořte novou aplikaci v3.

IoT Central nepodporuje migraci do existující aplikace v3. Pokud chcete automaticky přesunout existující zařízení, použijte Průvodce migrací k vytvoření aplikace v3.

Průvodce migrací:

- Vytvoří novou aplikaci v3.
- Zkontroluje, jestli jsou v šablonách zařízení kompatibilní se systémem v3.
- Zkopíruje všechny šablony zařízení do nové aplikace v3.
- Zkopíruje všechny uživatele a přiřazení rolí do nové aplikace v3.

> [!NOTE]
> Aby byla zajištěna kompatibilita zařízení se v3, primitivní typy v šabloně zařízení se stanou vlastnostmi objektu. Nemusíte dělat žádné změny kódu zařízení.

Chcete-li migrovat aplikaci na verzi 3, musíte být správcem.

1. V nabídce **Správa** vyberte možnost **migrovat do aplikace V3**:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Snímek obrazovky, který zobrazuje Průvodce migrací aplikace":::

1. Zadejte nový **název aplikace** a volitelně změňte automaticky vygenerovanou  **adresu URL**. Adresa URL nemůže být stejná jako vaše aktuální adresa URL aplikace V2. Adresu URL můžete ale později změnit i po odstranění aplikace V2.

1. Vyberte **vytvořit novou aplikaci V3**.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Snímek obrazovky, který zobrazuje možnosti v Průvodci migrací aplikace":::

    V závislosti na počtu a složitosti šablon zařízení může dokončení tohoto procesu trvat několik minut.

    > [!Warning]
    > Vytvoření aplikace V3 selže, pokud má libovolná šablona zařízení pole, která začínají číslem nebo obsahují některý z následujících znaků ( `+` , `*` , `?` , `^` , `$` , `(` , `)` , `[` , `]` , `{` , `}` , `|` , `\` ). Schéma šablony zařízení DTDL, které používají aplikace v3, nepovoluje tyto znaky. Než začnete migrovat na v3, aktualizujte šablonu zařízení, aby se tento problém vyřešil.

1. Když je nová aplikace V3 připravena, vyberte **otevřít novou aplikaci** a otevřete ji.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Snímek obrazovky, který po migraci aplikace zobrazuje Průvodce migrací aplikace":::

## <a name="configure-the-v3-application"></a>Konfigurace aplikace V3

Po vytvoření nové aplikace V3 proveďte všechny změny konfigurace před tím, než zařízení přesunete z aplikace V2 do aplikace v3.

> [!TIP]
> Počkejte prosím, než se [seznámíte se systémem V3](overview-iot-central-tour.md#navigate-your-application) , protože obsahuje několik rozdílů oproti předchozí verzi.

Tady je několik doporučených kroků konfigurace, které je potřeba vzít v úvahu:

- [Konfigurace řídicích panelů](howto-add-tiles-to-your-dashboard.md)
- [Konfigurace exportu dat](howto-export-data.md)
- [Konfigurace pravidel a akcí](quick-configure-rules.md)
- [Přizpůsobení uživatelského rozhraní aplikace](howto-customize-ui.md)

Když je vaše aplikace V3 nakonfigurovaná tak, aby vyhovovala vašim potřebám, jste připraveni přesunout zařízení z aplikace V2 do vaší aplikace v3.

## <a name="move-your-devices-to-the-v3-application"></a>Přesunutí zařízení do aplikace V3

Po dokončení tohoto kroku budou všechna vaše zařízení komunikovat pouze s vaší novou aplikací v3.

> [!IMPORTANT]
> Před přesunutím zařízení do aplikace V3 odstraňte všechna zařízení, která jste vytvořili v aplikaci v3.

Tento krok přesune všechna vaše stávající zařízení do vaší nové aplikace v3. Data zařízení se nekopírují.

Vyberte **přesunout všechna zařízení** a začněte přesouvat zařízení. Dokončení tohoto kroku může trvat několik minut.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Snímek obrazovky, který zobrazuje Průvodce migrací aplikace s možností přesunout zařízení":::

Po dokončení přesunu restartujte všechna vaše zařízení, abyste se ujistili, že se připojí k nové aplikaci v3.

> [!WARNING]
> Aplikaci V3 neodstraňujte, protože vaše aplikace V2 teď není funkční.

## <a name="delete-your-old-v2-application"></a>Odstranění staré aplikace V2

Po ověření, že vše funguje v nové aplikaci V3 podle očekávání, odstraňte starou aplikaci v2. Tento krok zajistí, že se nebudete účtovat za aplikaci, kterou už nepoužíváte.

> [!Note]
> Pokud chcete aplikaci odstranit, musíte mít oprávnění k odstraňování prostředků v předplatném Azure, které jste si zvolili při vytváření aplikace. Další informace najdete v tématu [použití řízení přístupu na základě rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).

1. V aplikaci v2 vyberte v nabídce kartu **Správa** .
2. Vyberte **Odstranit** pro trvalé odstranění IoT Central aplikace. Tato možnost trvale odstraní všechna data přidružená k dané aplikaci.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak migrovat aplikaci, je doporučeným dalším krokem kontrola [uživatelského rozhraní Azure IoT Central](overview-iot-central-tour.md) , kde zjistíte, co se změnilo v v3.