---
title: Export aplikace Azure IoT Central | Dokumenty společnosti Microsoft
description: Jako správce řešení chci exportovat šablonu aplikace, abych ji mohl znovu použít.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f50c7e8dcb33fd2ed95829286aaf815926d9fb3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157597"
---
# <a name="export-your-application"></a>Export vaší aplikace



Tento článek popisuje, jak jako správce řešení exportovat aplikaci IoT Central, abyste ji mohli znovu použít.

Máte dvě možnosti:

- Můžete vytvořit kopii aplikace, pokud potřebujete vytvořit duplicitní kopii aplikace.
- Šablonu aplikace můžete vytvořit z aplikace, pokud plánujete vytvořit více kopií.

## <a name="copy-your-application"></a>Kopírování aplikace

Můžete vytvořit kopii libovolné aplikace bez všech instancí zařízení, historie dat zařízení a uživatelských dat. Kopie používá standardní cenový plán, který vám bude účtován. Nelze vytvořit aplikaci, která používá bezplatný cenový plán zkopírováním aplikace.

Vyberte **Kopírovat**. V dialogovém okně zadejte podrobnosti o nové aplikaci. Pak vyberte **Kopírovat,** chcete-li potvrdit, že chcete pokračovat. Další informace o polích ve formuláři najdete v [tématu Vytvoření rychlého](quick-deploy-iot-central.md) spuštění aplikace.

![Stránka nastavení aplikace](media/howto-use-app-templates/appcopy2.png)

Po úspěšné operaci kopírování aplikace můžete přejít do nové aplikace pomocí odkazu.

![Stránka nastavení aplikace](media/howto-use-app-templates/appcopy3a.png)

Kopírování aplikace také zkopíruje definici pravidel a e-mailové akce. Některé akce, jako je například Flow a Logic Apps, jsou vázány na konkrétní pravidla prostřednictvím ID pravidla. Když je pravidlo zkopírováno do jiné aplikace, získá vlastní ID pravidla. V takovém případě budou uživatelé muset vytvořit novou akci a pak k ní přidružit nové pravidlo. Obecně je vhodné zkontrolovat pravidla a akce, abyste se ujistili, že jsou v nové aplikaci aktuální.

> [!WARNING]
> Pokud řídicí panel obsahuje dlaždice, které zobrazují informace o konkrétních zařízeních, zobrazí se tyto dlaždice Požadovaný prostředek nebyl v nové aplikaci **nalezen.** Tyto dlaždice je nutné překonfigurovat tak, aby zobrazovaly informace o zařízeních v nové aplikaci.

## <a name="create-an-application-template"></a>Vytvoření šablony aplikace

Když vytvoříte aplikaci Azure IoT Central, máte na výběr z předdefinovaných ukázkových šablon. Můžete také vytvořit vlastní šablony aplikací z existujících aplikací IoT Central. Při vytváření nových aplikací pak můžete použít vlastní šablony aplikací.

Když vytvoříte šablonu aplikace, obsahuje následující položky z existující aplikace:

- Výchozí řídicí panel aplikace, včetně rozložení řídicího panelu a všech dlaždic, které jste definovali.
- Šablony zařízení, včetně měření, nastavení, vlastností, příkazů a řídicího panelu.
- Pravidla. Jsou zahrnuty všechny definice pravidel. Akce, s výjimkou e-mailových akcí, však nejsou zahrnuty.
- Sady zařízení, včetně jejich podmínek a řídicích panelů.

> [!WARNING]
> Pokud řídicí panel obsahuje dlaždice, které zobrazují informace o konkrétních zařízeních, zobrazí se tyto dlaždice Požadovaný prostředek nebyl v nové aplikaci **nalezen.** Tyto dlaždice je nutné překonfigurovat tak, aby zobrazovaly informace o zařízeních v nové aplikaci.

Když vytvoříte šablonu aplikace, neobsahuje následující položky:

- Zařízení
- Uživatelé
- Definice zakázek
- Definice průběžného exportu dat

Tyto položky přidejte ručně do všech aplikací vytvořených ze šablony aplikace.

Vytvoření šablony aplikace z existující aplikace IoT Central:

1. V aplikaci přejděte do části **Správa.**
1. Vyberte **export šablony aplikace**.
1. Na stránce **Export šablony aplikace** zadejte název a popis šablony.
1. Chcete-li vytvořit šablonu aplikace, vyberte tlačítko **Exportovat.** Nyní můžete zkopírovat **odkaz s možnosti sdílení,** který umožňuje někomu vytvořit novou aplikaci ze šablony:

![Vytvoření šablony aplikace](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Použití šablony aplikace

Chcete-li k vytvoření nové aplikace IoT Central použít šablonu aplikace, potřebujete dříve vytvořený **odkaz Sypitelný link**. Vložte **odkaz na sdílení** do adresního řádku prohlížeče. Zobrazí se stránka **Vytvořit aplikaci** s vybranou vlastní šablonou aplikace:

![Vytvoření aplikace ze šablony](media/howto-use-app-templates/create-app.png)

Vyberte cenový plán a vyplňte ostatní pole ve formuláři. Pak vyberte **Vytvořit** a vytvořte novou aplikaci IoT Central ze šablony aplikace.

### <a name="manage-application-templates"></a>Správa šablon aplikací

Na stránce **Export šablony aplikace** můžete šablonu aplikace odstranit nebo aktualizovat.

Pokud odstraníte šablonu aplikace, nebudete již k vytváření nových aplikací moci používat dříve generovaný odkaz s odkazem na sdílení.

Chcete-li aktualizovat šablonu aplikace, změňte název nebo popis šablony na stránce **Export šablony aplikace.** Pak znovu vyberte tlačítko **Exportovat.** Tato akce vygeneruje nový **odkaz s odkazem s odkazem, který lze sdílet,** a zruší platnost všech předchozích adres URL **odkazu s odkazem, které lze sdílet.**

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili používat šablony aplikací, je navrhovaným dalším krokem naučit se [spravovat IoT Central z webu Azure Portal.](howto-manage-iot-central-from-portal.md)
