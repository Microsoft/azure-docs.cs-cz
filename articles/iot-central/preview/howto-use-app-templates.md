---
title: Export aplikace Azure IoT Central | Microsoft Docs
description: Jako správce řešení bych chtěl Exportovat šablonu aplikace, aby ji bylo možné znovu použít.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 9870b18be9fb75e25941a7e1d05e4164239aacbb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977518"
---
# <a name="export-your-application-preview-features"></a>Export aplikace (funkce verze Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Tento článek popisuje, jak jako správce řešení exportovat aplikaci IoT Central, aby ji bylo možné znovu použít.

Máte dvě možnosti:

- Můžete vytvořit kopii aplikace, pokud potřebujete pouze vytvořit duplicitní kopii vaší aplikace.
- Můžete vytvořit šablonu aplikace z aplikace, pokud plánujete vytvořit více kopií.

## <a name="copy-your-application"></a>Zkopírování aplikace

Můžete vytvořit kopii libovolné aplikace, mínus instance zařízení, historii dat zařízení a uživatelská data. Kopie je aplikace s průběžnými platbami, za kterou se vám bude účtovat. Nemůžete vytvořit zkušební aplikaci zkopírováním aplikace.

Vyberte **Kopírovat**. Do dialogového okna zadejte podrobnosti nové aplikace s průběžnými platbami. Pak vyberte **Kopírovat** a potvrďte, že chcete pokračovat. Další informace o polích ve formuláři najdete v rychlém startu [Vytvoření aplikace](quick-deploy-iot-central.md) .

> [!NOTE]
> Aplikaci můžete **Kopírovat** pouze do **Evropy** a umístění **USA** .

![Stránka nastavení aplikace](media/howto-use-app-templates/appcopy2.png)

Po úspěšném dokončení operace kopírování aplikace můžete přejít k nové aplikaci pomocí odkazu.

![Stránka nastavení aplikace](media/howto-use-app-templates/appcopy3a.png)

Zkopírováním aplikace se také zkopírují definice pravidel a e-mailové akce. Některé akce, jako je například Flow a Logic Apps, jsou vázány na konkrétní pravidla prostřednictvím ID pravidla. Když se pravidlo zkopíruje do jiné aplikace, získá ID vlastního pravidla. V takovém případě budou muset uživatelé vytvořit novou akci a pak k ní přidružit nové pravidlo. Obecně je vhodné zkontrolovat pravidla a akce, abyste se ujistili, že jsou v nové aplikaci aktuální.

> [!WARNING]
> Pokud řídicí panel obsahuje dlaždice, které zobrazují informace o konkrétních zařízeních, pak tyto dlaždice ukázaly, že **požadovaný prostředek** nebyl v nové aplikaci nalezen. Aby bylo možné zobrazit informace o zařízeních v nové aplikaci, je nutné tyto dlaždice znovu nakonfigurovat.

## <a name="create-an-application-template"></a>Vytvoření šablony aplikace

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

Vytvoření šablony aplikace z existující aplikace IoT Central:

1. V aplikaci přejdete do části **Správa** .
1. Vyberte **Exportovat šablonu aplikace**.
1. Na stránce **Export šablony aplikace** zadejte název a popis šablony.
1. Chcete-li vytvořit šablonu aplikace, vyberte tlačítko **exportovat** . Nyní můžete zkopírovat odkaz umožňující **sdílení** , který někomu umožňuje vytvořit novou aplikaci ze šablony:

![Vytvoření šablony aplikace](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Použití šablony aplikace

Chcete-li použít šablonu aplikace k vytvoření nové aplikace IoT Central, budete potřebovat dříve vytvořený **odkaz Shared**. Do adresního řádku prohlížeče vložte **odkaz Shared** . Zobrazí se stránka **vytvořit aplikaci** s vybranou šablonou vlastní aplikace:

![Vytvoření aplikace ze šablony](media/howto-use-app-templates/create-app.png)

Vyberte svůj platební plán a vyplňte další pole ve formuláři. Pak vyberte **vytvořit** a vytvořte novou IoT Central aplikaci z šablony aplikace.

> [!NOTE]
> V případě vytvoření aplikace pomocí **odkazu ke sdílení** je dostupná umístění **Evropa** a **USA**.

### <a name="manage-application-templates"></a>Správa šablon aplikací

Na stránce **Export šablony aplikace** můžete šablonu aplikace odstranit nebo aktualizovat.

Pokud šablonu aplikace odstraníte, nebudete už k vytváření nových aplikací používat dříve vygenerovaný odkaz s možnostmi sdílení.

Chcete-li aktualizovat šablonu aplikace, změňte název šablony nebo popis na stránce pro **Export šablony aplikace** . Pak znovu vyberte tlačítko **exportovat** . Tato akce vygeneruje nový **odkaz Shared** a zruší platnost všech předchozích adres URL **odkazu pro sdílení** .

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s použitím šablon aplikací, je navržený další krok, kde se dozvíte, jak [spravovat IoT Central z Azure Portal](../core/howto-manage-iot-central-from-portal.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
