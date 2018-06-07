---
title: Vytvoření a Správa pravidel událostí v aplikaci Azure IoT centrální | Microsoft Docs
description: Pravidla událostí ve službě Azure IoT střed umožňují sledovat vaše zařízení skoro v reálném čase a automaticky vyvolání akce, například odeslání e-mailu, když se pravidlo spustí.
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 30223fdca9d848ddc407981bf4a3ca683a10575a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628364"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Vytvořit pravidlo události a vytvořit oznámení v aplikaci Azure IoT centrální

Microsoft Azure IoT centrální můžete použít pro vzdálené monitorování připojená zařízení. Pravidla ve službě Azure IoT střed umožňují sledovat vaše zařízení skoro v reálném čase a automaticky vyvolání akce, například odeslání e-mailu, když se pravidlo spustí. V několika kliknutí můžete definovat podmínku, která má monitorovat data zařízení a nakonfigurujte akci, která má být vyvolána. Tento článek vysvětluje událostí pravidlo podrobně monitorování.

Střed Azure IoT používá [měření událostí](howto-set-up-template.md) k zaznamenání dat o zařízení. Každý typ měření má klíčové atributy, které definují měření. Můžete vytvořit pravidla pro každý typ měření zařízení monitorování a generování výstrah v případě, že pravidlo spustí. Pravidlo událost se aktivuje při události vybrané zařízení je hlášen zařízení.

## <a name="create-an-event-rule"></a>Vytvoření pravidla událostí

V této části se dozvíte, jak vytvořit pravidlo události. Tento příklad používá chladicí prodejních strojové zařízení této sestavy ventilátor motor chybová událost. Pravidlo sleduje události údajů ze zařízení a odešle e-mail, vždy, když se použije v hlášení události.

1. Přejděte na stránku podrobností zařízení pro zařízení, který chcete přidat pravidlo.

1. Pokud ještě jste nevytvořili všechna pravidla, zobrazí se následující obrazovka:

    ![Zatím žádná pravidla](media\howto-create-event-rules\image1.png)

1. Na **pravidla** , zvolte **+ nové pravidlo** zobrazíte typy pravidel, můžete vytvořit.

    ![Typy pravidel](media\howto-create-event-rules\image2.png)

1. Klikněte na **událostí** otevřete formulář vytvoření pravidla.

    ![Pravidlo události](media\howto-create-event-rules\image3.png)

1. Zvolte název, který umožňuje identifikovat pravidla v této šabloně zařízení.

1. Postup při povolení okamžitě pravidla pro všechna zařízení, které jsou vytvořené z této šablony, přepněte **Povolit pravidlo**.

### <a name="configure-the-rule-condition"></a>Konfigurace podmínka pro pravidlo

V této části se dozvíte, jak přidat podmínku, kterou chcete monitorovat měření událostí ventilátor motor chyb.

1. Vyberte **+** vedle **podmínku**.

1. Zvolte měření událostí z rozevíracího seznamu, kterou chcete monitorovat. V tomto příkladu **Motor chyba ventilátor** událost nebyla vybrána.

1. Volitelně můžete zadat také hodnotu v případě, že chcete monitorovat konkrétní hodnotu události hlášených zařízení. Například pokud zařízení hlásí stejnou událost s jinou chybové kódy pak zadají kód chyby jako hodnota v podmínce pravidla zajistí, že pravidlo aktivačních událostí jenom v případě, že zařízení odesílá tuto konkrétní hodnotu jako datové části události. Tento prázdné bude znamenat, že pravidlo se aktivuje vždy, když zařízení odesílá události bez ohledu na hodnotu událostí.

    ![Přidejte podmínku událostí](media\howto-create-event-rules\image4.png)

    > [!NOTE]
    > Při definování podmínku pravidla událostí, musíte vybrat aspoň jednu událost měření.

### <a name="configure-the-action"></a>Konfigurace akce

V této části se dozvíte, jak určit, co pravidlo dělá, když podmínku odpovídá přidáním akce.

1. Vyberte **+** vedle **akce**. Zde se zobrazí seznam akcí, k dispozici. Chcete-li verzi public Preview **e-mailu** je jedinou podporovanou akcí.

    ![Akce pro přidání](media\howto-create-event-rules\image5.png)

1. Vyberte **e-mailu** akce, zadejte platnou e-mailovou adresu do **k** pole a zadejte poznámku k vložit do těla e-mailu, když se pravidlo spustí.

    > [!NOTE]
    > E-maily jsou odesílány pouze uživatele, kteří byly přidány k aplikaci a alespoň jednou přihlásí. Další informace o [Správa uživatelů](howto-administer.md) v centrální Azure IoT.

   ![Konfigurace akce](media\howto-create-event-rules\image6.png)

1. Chcete-li uložit pravidlo, zvolte **Uložit**. Pravidlo přejde během několika minut za provozu a spustí monitorování události odesílány do vaší aplikace. Pokud je podmínka uvedená v pravidle odpovídá, pravidlo spustí akci nakonfigurovanou e-mailu.

## <a name="parameterize-the-rule"></a>Parametrizace pravidlo

Akce je také možné nakonfigurovat pomocí **vlastnosti zařízení** jako parametr. Pokud jako vlastnost zařízení je uložený e-mailovou adresu, pak můžete použít, když definujete **k** adresu.

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud pravidlo již nepotřebujete, odstraňte jej otevřením pravidlo a zvolením **odstranit**. Odstraňuje se pravidlo odebere z šablony zařízení a všechna související zařízení.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Povolí nebo zakáže pravidlo pro šablonu zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnutím **Povolit pravidlo pro všechna zařízení této šablony** tlačítka na pravidlo povolí nebo zakáže pravidlo pro všechny zařízení přidružená k šabloně zařízení.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolí nebo zakáže pravidlo pro zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnutí **pravidlo povolení pro toto zařízení** tlačítko k povolení nebo zakázání pravidla pro toto zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili postup vytvoření pravidla v aplikaci Azure IoT centrální, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Jak spravovat zařízení](howto-manage-devices.md).