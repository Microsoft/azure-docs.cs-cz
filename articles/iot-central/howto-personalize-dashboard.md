---
title: Vytváření osobních řídicích panelů Azure IoT Central | Dokumentace Microsoftu
description: Jako uživatel zjistěte, jak vytvářet a spravovat vaše osobní řídicí panely.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: fb74669dcaa802ad06a9c4dff3ffdf25726f518c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316052"
---
# <a name="create-and-manage-personal-dashboards"></a>Vytvoření a Správa osobních řídicích panelů

**Řídicí panel** je stránka, která načte při první návštěvě do vaší aplikace. A **Tvůrce** ve vaší aplikaci definuje výchozí řídicí panel aplikací pro všechny uživatele. Tento výchozí řídicí panel můžete nahradit vlastní, přizpůsobené aplikace řídicí panel. Můžete mít několik řídicích panelů, které zobrazují různá data a mezi nimi přepínat.

## <a name="create-dashboard"></a>Vytvoření řídicího panelu

Následující snímek obrazovky ukazuje řídicí panel v aplikaci vytvořenou z **ukázka Contoso** šablony. Řídicí panel můžete nahradit výchozí řídicí panel aplikací. Chcete-li to provést, vyberte **+ nová** v horní části stránky.

![Řídicí panel pro aplikace založené na šabloně "Ukázkové společnosti Contoso"](media/howto-personalize-dashboard/defaultdashboard.png)

Výběr **+ nová**, otevře se editor řídicích panelů. V editoru můžete pojmenovat řídicí panel a zvolit položky z knihovny. Knihovna obsahuje dlaždice a řídicí panel primitiv, můžete přizpůsobit řídicí panel.

![Knihovna řídicího panelu](media/howto-personalize-dashboard/dashboardeditor.png)

Například můžete přidat **nastavení a vlastnosti** dlaždici zobrazíte vlastnosti a nastavení hodnoty pro jedno zařízení, která spravujete. Uděláte to tak, nejdřív vyberte **šablona zařízení** vyberte **Instance zařízení**. Název a vyberte dlaždici dejte **nastavení** nebo **vlastnost** k zobrazení. Následující snímek obrazovky ukazuje **ventilátor rychlost** nastavení vybrané pro přidání k dlaždici. Vyberte **provádí** uložte změnu do řídicího panelu.

!["Konfigurace podrobnosti o zařízení" formulář s podrobnostmi o nastavení a vlastnosti](media/howto-personalize-dashboard/dashboardsetting.png)

Teď když zobrazíte řídicí panel, se zobrazí nová dlaždice s **ventilátor rychlost** nastavení pro zařízení:

![Karta "Řídicí panel" se zobrazené nastavení a vlastnosti pro dlaždici](media/howto-personalize-dashboard/personaldashboard.png)

Můžete prozkoumat další typy dlaždic v knihovně chcete zjistit, jak dále přizpůsobit osobních řídicích panelů.

## <a name="manage-dashboards"></a>Spravovat řídicí panely

Může mít několik osobních řídicích panelů a mezi nimi přepínat, nebo zvolte výchozí řídicí panel aplikací:

![Řídicí panel přepínače](media/howto-personalize-dashboard/switchdashboards.png)

Můžete upravit osobních řídicích panelů a ty, které už nepotřebujete odstranit:

![Odstranit řídicí panel](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak vytvářet a spravovat osobní řídicí panely, můžete:

> [!div class="nextstepaction"]
> [Další informace o správě vašich předvoleb aplikace](howto-manage-preferences.md)
