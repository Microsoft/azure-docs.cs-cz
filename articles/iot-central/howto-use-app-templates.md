---
title: Použití šablon aplikací v Azure IoT Central | Dokumentace Microsoftu
description: Jakožto Obsluha nastaví použití zařízení v aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499216"
---
# <a name="use-application-templates"></a>Použití šablon aplikací

Tento článek popisuje, jak jako správce řešení, vytváření a používání šablon aplikací.

Když vytvoříte aplikaci Azure IoT Central, máte celou řadu integrovaných ukázkové šablony. Můžete také vytvořit vlastní šablony aplikací z existujících aplikací IoT Central. Pak můžete použít vlastní šablony aplikací při vytváření nových aplikací.

Při vytváření šablony aplikace zahrnuje následující položky z existující aplikace:

- Výchozí řídicí panel aplikací, včetně rozložení řídicího panelu a všechny dlaždice, které jste definovali.
- Šablony zařízení, včetně měření, nastavení, vlastnosti, příkazy a řídicí panel.
- Pravidla. Všechny definice pravidla jsou zahrnuty. Akce s výjimkou e-mailových akcí, nejsou však zahrnuty.
- Zařízení se nastaví, včetně jejich podmínky a řídicí panely.

> [!WARNING]
> Pokud řídicí panel obsahuje dlaždice, které zobrazují informace o konkrétních zařízení a pak tyto dlaždice zobrazit **požadovaný prostředek nebyl nalezen** v nové aplikaci. Je nutné překonfigurovat tyto dlaždice k zobrazení informací o zařízeních v nové aplikaci.

Při vytváření šablony aplikace neobsahuje následující položky:

- Zařízení
- Uživatelé
- Definice úloh
- Souvislá datová exportovat definice

Tyto položky se ručně přidáte do jakékoli aplikace vytvořené z šablony aplikace.

## <a name="create-an-application-template"></a>Vytvoření šablony aplikace

Postup vytvoření šablony aplikace z existující aplikace IoT Central:

1. Přejděte **správu** oddílu ve vaší aplikaci.
1. Vyberte **Export šablony aplikace**.
1. Na **exportovat šablonu aplikace** stránky, zadejte název a popis šablony.
1. Vyberte **exportovat** pro vytvoření šablony aplikace. Nyní můžete zkopírovat **sdílet odkaz** uživatele k vytvoření nové aplikace ze šablony, která umožňuje:

![Vytvoření šablony aplikace](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Použít šablonu aplikace

Šablony aplikace použít k vytvoření nové aplikace IoT Central, musíte dříve vytvořeného **sdílet odkaz**. Vložit **sdílet odkaz** do panelu Adresa v prohlížeči. **Vytvořit aplikaci** stránka zobrazí vaše vlastní aplikace máte zvolenou šablonu:

![Vytvoření aplikace ze šablony](media/howto-use-app-templates/create-app.png)

Vyberte plán plateb a vyplnit i ostatní pole na formuláři. Potom vyberte **vytvořit** k vytvoření nové aplikace IoT Central ze šablony aplikace.

## <a name="manage-application-templates"></a>Správa šablon aplikací

Na **exportovat šablonu aplikace** stránky, můžete odstranit nebo aktualizovat šablonu aplikace.

Při odstranění šablony aplikace, už vám pomůže dříve vytvořený odkaz ke sdílení vytváření nových aplikací.

K aktualizaci šablony aplikace, změnit na šablony názvu nebo popisu **exportovat šablonu aplikace** stránky. Vyberte **exportovat** tlačítko znovu. Tato akce vytvoří novou **sdílet odkaz** a zruší platnost jakékoli předchozí **sdílet odkaz** adresy URL.

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak používat šablony aplikací, navrhované dalším krokem je další způsob [spravovat IoT Central z portálu Azure portal](howto-manage-iot-central-from-portal.md)
