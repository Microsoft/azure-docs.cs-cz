---
title: Použití šablon aplikací v Azure IoT Central | Microsoft Docs
description: Jako operátor, jak používat sady zařízení ve vaší aplikaci Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3cc6f82676f426240fba4cc4910246073aa9a556
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982456"
---
# <a name="use-application-templates"></a>Použití šablon aplikací

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako správce řešení vytvářet a používat šablony aplikací.

Když vytváříte aplikaci Azure IoT Central, máte možnost zvolit si předdefinované vzorové šablony. Můžete také vytvořit vlastní šablony aplikací z existujících aplikací IoT Central. Když vytváříte nové aplikace, můžete použít vlastní šablony aplikací.

Když vytváříte šablonu aplikace, zahrnuje následující položky z vaší existující aplikace:

- Výchozí řídicí panel aplikace, včetně rozložení řídicího panelu a všech dlaždic, které jste definovali.
- Šablony zařízení, včetně měření, nastavení, vlastností, příkazů a řídicího panelu.
- Pravidly. Jsou zahrnuty všechny definice pravidel. Akce, s výjimkou e-mailových akcí, ale nejsou zahrnuté.
- Sady zařízení, včetně jejich podmínek a řídicích panelů.

> [!WARNING]
> Pokud řídicí panel obsahuje dlaždice, které zobrazují informace o konkrétních zařízeních, pak tyto dlaždice ukázaly, že **požadovaný prostředek** nebyl v nové aplikaci nalezen. Aby bylo možné zobrazit informace o zařízeních v nové aplikaci, je nutné tyto dlaždice znovu nakonfigurovat.

Když vytvoříte šablonu aplikace, nezahrnuje následující položky:

- Zařízení
- Uživatelé
- Definice úloh
- Průběžné definice exportu dat

Přidejte tyto položky ručně do všech aplikací vytvořených z šablony aplikace.

## <a name="create-an-application-template"></a>Vytvoření šablony aplikace

Vytvoření šablony aplikace z existující aplikace IoT Central:

1. V aplikaci přejdete do části **Správa** .
1. Vyberte **Exportovat šablonu aplikace**.
1. Na stránce **Export šablony aplikace** zadejte název a popis šablony.
1. Chcete-li vytvořit šablonu aplikace, vyberte tlačítko **exportovat** . Nyní můžete zkopírovat odkaz umožňující **sdílení** , který někomu umožňuje vytvořit novou aplikaci ze šablony:

![Vytvoření šablony aplikace](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>Použití šablony aplikace

Chcete-li použít šablonu aplikace k vytvoření nové aplikace IoT Central, budete potřebovat dříve vytvořený **odkaz Shared**. Do adresního řádku prohlížeče vložte **odkaz Shared** . Zobrazí se stránka **vytvořit aplikaci** s vybranou šablonou vlastní aplikace:

![Vytvoření aplikace ze šablony](media/howto-use-app-templates/create-app.png)

Vyberte svůj Cenový tarif a vyplňte další pole ve formuláři. Pak vyberte **vytvořit** a vytvořte novou IoT Central aplikaci z šablony aplikace.

## <a name="manage-application-templates"></a>Správa šablon aplikací

Na stránce **Export šablony aplikace** můžete šablonu aplikace odstranit nebo aktualizovat.

Pokud šablonu aplikace odstraníte, nebudete už k vytváření nových aplikací používat dříve vygenerovaný odkaz s možnostmi sdílení.

Chcete-li aktualizovat šablonu aplikace, změňte název šablony nebo popis na stránce pro **Export šablony aplikace** . Pak znovu vyberte tlačítko **exportovat** . Tato akce vygeneruje nový **odkaz Shared** a zruší platnost všech předchozích adres URL **odkazu pro sdílení** .

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s použitím šablon aplikací, je navržený další krok, kde se dozvíte, jak [spravovat IoT Central z Azure Portal](howto-manage-iot-central-from-portal.md)
