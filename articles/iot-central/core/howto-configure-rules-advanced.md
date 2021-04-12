---
title: Použití pracovních postupů k integraci aplikace IoT Central Azure s dalšími Cloud Services | Microsoft Docs
description: V tomto článku se dozvíte, jak můžete jako tvůrce nakonfigurovat pravidla a akce, které integrují vaši aplikaci IoT Central s dalšími Cloud Services. Chcete-li vytvořit rozšířené pravidlo, použijte IoT Central konektor buď v Power automatu, nebo v Azure Logic Apps.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: af6adcf408c9790b09830e986cd1cac00774a2ec
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491929"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Použití pracovních postupů k integraci aplikace IoT Central Azure s dalšími Cloud Services

*Tento článek se týká tvůrců řešení.*

Můžete vytvořit pravidla v IoT Central, která spouštějí akce, jako je například odeslání e-mailu, v reakci na podmínky založené na telemetrie, jako je například teplota zařízení překračující prahovou hodnotu.

Konektor Azure IoT Central v3 pro automatické automatizace a Azure Logic Apps umožňuje vytvořit pokročilejší pravidla pro automatizaci operací v IoT Central:

- Když pravidlo v aplikaci Azure IoT Central vyvolá, může aktivovat pracovní postup v Power Automate nebo Azure Logic Apps. Tyto pracovní postupy můžou spouštět akce v jiných cloudových službách, jako je Microsoft 365 nebo služba třetí strany.
- Událost v jiné cloudové službě, jako je například Microsoft 365, může aktivovat pracovní postup v Power Automate nebo Azure Logic Apps. Tyto pracovní postupy mohou spouštět akce nebo načítat data z aplikace IoT Central.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Nastavení řešení vyžaduje verzi 3 IoT Central aplikaci. Informace o tom, jak zjistit verzi vaší aplikace, najdete v tématu [o vaší aplikaci](./howto-get-app-info.md). Informace o tom, jak vytvořit aplikaci IoT Central, najdete v tématu [Vytvoření aplikace Azure IoT Central](./quick-deploy-iot-central.md).

> [!NOTE]
> Pokud používáte aplikaci IoT Central verze 2, přečtěte si téma [vytvoření pracovních postupů pomocí konektoru IoT Central v Azure Logic Apps](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) na webu dokumentace k předchozím verzím a použití konektoru Azure IoT Central v2.

## <a name="trigger-a-workflow-from-a-rule"></a>Aktivovat pracovní postup z pravidla

Než budete moci aktivovat pracovní postup v Power Automate nebo Azure Logic Apps, budete potřebovat pravidlo ve vaší aplikaci IoT Central. Další informace najdete v tématu [Konfigurace pravidel a akcí v Azure IoT Central](./howto-configure-rules.md).

Postup přidání konektoru **Azure IoT Central V3-Preview** jako triggeru v Power Automate:

1. V Power automatu vyberte **+ vytvořit** a vyberte **vlastní** karta.
1. Vyhledejte *IoT Central* a vyberte konektor **Azure IoT Central V3-Preview** .
1. V seznamu triggerů vyberte, **kdy se má pravidlo aktivovat (Preview)**.
1. V kroku **při vyvolání pravidla** vyberte svou aplikaci IoT Central a pravidlo, které používáte.

Přidání konektoru **Azure IoT Central V3-Preview** jako triggeru v Azure Logic Apps:

1. V **návrháři Logic Apps** vyberte šablonu **prázdná aplikace logiky** .
1. V návrháři vyberte **vlastní** kartu.
1. Vyhledejte *IoT Central* a vyberte konektor **Azure IoT Central V3-Preview** .
1. V seznamu triggerů vyberte, **kdy se má pravidlo aktivovat (Preview)**.
1. V kroku **při vyvolání pravidla** vyberte svou aplikaci IoT Central a pravidlo, které používáte.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Najděte konektor Azure IoT Central-Preview a vyberte Trigger.":::

Teď můžete do pracovního postupu přidat další kroky a vytvořit tak scénář integrace.

## <a name="run-an-action"></a>Spustit akci

V IoT Central aplikaci můžete spouštět akce z Power Automata a Azure Logic Apps pracovní postupy. Nejdřív vytvořte pracovní postup a pomocí konektoru definujte aktivační událost, která spustí pracovní postup. Pak jako akci použijte konektor **Azure IoT Central V3-Preview** .

Postup přidání konektoru služby **Azure IoT Central V3-Preview** jako akce v Power Automate:

1. V Power automatu na panelu **Zvolte akci** vyberte **vlastní** kartu.
1. Vyhledejte *IoT Central* a vyberte konektor **Azure IoT Central V3-Preview** .
1. V seznamu akcí vyberte akci IoT Central, kterou chcete použít.
1. V kroku akce dokončete konfiguraci pro akci, kterou jste zvolili. Pak vyberte **Uložit**.

Postup přidání konektoru služby **Azure IoT Central V3-Preview** jako akce v Azure Logic Apps:

1. V **návrháři Logic Apps** v panelu **Zvolte akci** vyberte **vlastní** kartu.
1. Vyhledejte *IoT Central* a vyberte konektor **Azure IoT Central V3-Preview** .
1. V seznamu akcí vyberte akci IoT Central, kterou chcete použít.
1. V kroku akce dokončete konfiguraci pro akci, kterou jste zvolili. Pak vyberte **Uložit**.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Najděte konektor Azure IoT Central v3 a vyberte akci.":::

## <a name="list-of-actions"></a>Seznam akcí

Následující seznam obsahuje všechny dostupné IoT Central akce v konektoru **Azure IoT Central V3-Preview** a jejich možnosti konfigurace. Mnoho polí může mít dynamicky generovaný obsah. Například předchozí krok může určit ID zařízení, na kterém aktuální krok funguje.

### <a name="create-or-update-a-device"></a>Vytvoření nebo aktualizace zařízení

Pomocí této akce můžete v aplikaci IoT Central vytvořit nebo aktualizovat zařízení.

| Pole | Description |
| ----- | ----------- |
| Aplikace | Vyberte si ze seznamu IoT Central aplikací. |
| Zařízení | Jedinečné ID zařízení, které se má vytvořit nebo aktualizovat |
| Schválené | Vyberte, jestli je zařízení schválené pro připojení k IoT Central. |
| Popis zařízení | Podrobný popis zařízení. |
| Název zařízení | Zobrazovaný název zařízení. |
| Šablona zařízení | Vyberte ze seznamu šablon zařízení v aplikaci IoT Central. |
| Simulované | Vyberte, jestli se zařízení simuluje. |

### <a name="delete-a-device"></a>Odstranit zařízení

Pomocí této akce můžete z aplikace IoT Central odstranit zařízení.

| Pole | Description |
| ----- | ----------- |
| Aplikace | Vyberte si ze seznamu IoT Central aplikací. |
| Zařízení | Jedinečné ID zařízení, které se má odstranit |

### <a name="execute-a-device-command"></a>Spustit příkaz pro zařízení

Tuto akci použijte ke spuštění příkazu definovaného v jednom z rozhraní zařízení.

| Pole | Description |
| ----- | ----------- |
| Aplikace | Vyberte si ze seznamu IoT Central aplikací. |
| Zařízení | Jedinečné ID zařízení, které se má odstranit |
| Součást zařízení | Rozhraní v šabloně zařízení, které obsahuje příkaz. |
| Příkaz zařízení | Vyberte jeden z příkazů ve vybraném rozhraní. |
| Šablona zařízení | Vyberte ze seznamu šablon zařízení v aplikaci IoT Central. |
| Datová část požadavku na zařízení | Pokud příkaz vyžaduje datovou část požadavku, přidejte ji sem.  |

> [!NOTE]
> Nemůžete zvolit součást zařízení, dokud nezvolíte šablonu zařízení.

### <a name="get-a-device-by-id"></a>Získat zařízení podle ID

Pomocí této akce načtete podrobnosti o zařízení.

| Pole | Description |
| ----- | ----------- |
| Aplikace | Vyberte si ze seznamu IoT Central aplikací. |
| Zařízení | Jedinečné ID zařízení, které se má odstranit |

Můžete použít vrácené podrobnosti v dynamických výrazech v jiných akcích. Mezi vracené podrobnosti o zařízení patří: **schváleno**, **text**, **Popis zařízení**, **název zařízení**, **Šablona zařízení**, **zřízené** a **simulované**.

### <a name="get-device-cloud-properties"></a>Získat vlastnosti cloudu zařízení

Tuto akci použijte, pokud chcete načíst hodnoty vlastností cloudu pro konkrétní zařízení.

| Pole | Description |
| ----- | ----------- |
| Aplikace | Vyberte si ze seznamu IoT Central aplikací. |
| Zařízení | Jedinečné ID zařízení, které se má odstranit |
| Šablona zařízení | Vyberte ze seznamu šablon zařízení v aplikaci IoT Central. |

V jiných akcích můžete použít vrácené hodnoty vlastností cloudu v dynamických výrazech.

### <a name="get-device-properties"></a>Získat vlastnosti zařízení

Tuto akci použijte, pokud chcete načíst hodnoty vlastností pro konkrétní zařízení.

| Pole | Description |
| ----- | ----------- |
| Aplikace | Vyberte si ze seznamu IoT Central aplikací. |
| Zařízení | Jedinečné ID zařízení, které se má odstranit |
| Šablona zařízení | Vyberte ze seznamu šablon zařízení v aplikaci IoT Central. |

Můžete použít hodnoty vrácených vlastností v dynamických výrazech v jiných akcích.

### <a name="get-device-telemetry-value"></a>Získání hodnoty telemetrie zařízení

Tuto akci použijte, pokud chcete načíst hodnoty telemetrie pro konkrétní zařízení.

| Pole | Description |
| ----- | ----------- |
| Aplikace | Vyberte si ze seznamu IoT Central aplikací. |
| Zařízení | Jedinečné ID zařízení, které se má odstranit |
| Šablona zařízení | Vyberte ze seznamu šablon zařízení v aplikaci IoT Central. |

Můžete použít vrácené hodnoty telemetrie v dynamických výrazech v jiných akcích.

### <a name="update-device-cloud-properties"></a>Aktualizovat vlastnosti cloudu zařízení

Tuto akci použijte, pokud chcete aktualizovat hodnoty vlastností cloudu pro konkrétní zařízení.

| Pole | Description |
| ----- | ----------- |
| Aplikace | Vyberte si ze seznamu IoT Central aplikací. |
| Zařízení | Jedinečné ID zařízení, které se má odstranit |
| Šablona zařízení | Vyberte ze seznamu šablon zařízení v aplikaci IoT Central. |
| Vlastnosti cloudu | Po výběru šablony zařízení se přidá pole pro každou vlastnost cloudu definovanou v šabloně. |

### <a name="update-device-properties"></a>Aktualizace vlastností zařízení

Tuto akci použijte, pokud chcete aktualizovat hodnoty vlastností s možností zápisu pro konkrétní zařízení.

| Pole | Description |
| ----- | ----------- |
| Aplikace | Vyberte si ze seznamu IoT Central aplikací. |
| Zařízení | Jedinečné ID zařízení, které se má odstranit |
| Šablona zařízení | Vyberte ze seznamu šablon zařízení v aplikaci IoT Central. |
| Vlastnosti s možností zápisu | Po výběru šablony zařízení se přidá pole pro každou vlastnost s možností zápisu definovanou v šabloně. |

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s postupem vytvoření pokročilého pravidla v aplikaci Azure IoT Central, se můžete dozvědět, jak [analyzovat data zařízení v aplikaci azure IoT Central](howto-create-analytics.md) .