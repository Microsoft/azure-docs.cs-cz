---
title: Prohlídka uživatelského rozhraní Azure IoT Central | Microsoft Docs
description: Seznamte se s klíčovými oblastmi uživatelského rozhraní Azure IoT Central, které můžete použít k vytvoření, správě a používání řešení IoT.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: ff795281b3471d5c6f57397edf5a52e0df9bbd32
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589111"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Prohlídka uživatelského rozhraní Azure IoT Central

Tento článek vás seznámí s uživatelským rozhraním Microsoft Azure IoT Central. Pomocí uživatelského rozhraní můžete vytvořit, spravovat a používat řešení Azure IoT Central a připojená zařízení.

## <a name="iot-central-homepage"></a>IoT Central domovskou stránku

Stránka [domovské stránky IoT Central](https://aka.ms/iotcentral-get-started) je místo, kde najdete další informace o nejnovějších novinkách a funkcích, které jsou k dispozici v IoT Central, vytváření nových aplikací a zobrazení a spuštění stávajících aplikací.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central domovskou stránku":::

### <a name="create-an-application"></a>Vytvoření aplikace

V části sestavení můžete procházet seznam relevantních šablon IoT Central, nebo začít od začátku pomocí šablony vlastní aplikace.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="Stránka buildu IoT Central":::

Další informace najdete v tématu věnovaném rychlému startu [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md) .

### <a name="launch-your-application"></a>Spuštění aplikace

Aplikaci IoT Central spustíte tak, že přejdete na adresu URL, kterou jste si zvolili během vytváření aplikace. Seznam všech aplikací, ke kterým máte přístup, můžete zobrazit také ve [správci aplikací IoT Central](https://aka.ms/iotcentral-apps).

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="Správce aplikací IoT Central":::

## <a name="navigate-your-application"></a>Navigace v aplikaci

Až budete v aplikaci IoT, můžete k různým oblastem přistupovat v levém podokně. V levém podokně můžete rozbalit nebo sbalit tak, že v horní části podokna vyberete ikonu se třemi řádky:

> [!NOTE]
> Položky, které vidíte v levém podokně, závisí na vaší roli uživatele. Přečtěte si další informace o [správě uživatelů a rolí](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="levé podokno":::

  :::column-end:::
  :::column span="2":::
     **Řídicí panely** zobrazují všechny aplikace a osobní řídicí panely. 
     
     **Zařízení** umožňují spravovat připojená zařízení – reálné a simulované.

     **Skupiny zařízení** vám umožní zobrazit a vytvořit kolekce zařízení, která jsou určená dotazem. Skupiny zařízení se používají prostřednictvím aplikace k provádění hromadných operací.

     **Pravidla** umožňují vytvářet a upravovat pravidla pro monitorování vašich zařízení. Pravidla se vyhodnocují na základě telemetrie zařízení a triggerů přizpůsobitelných akcí.

     **Analýza** vám umožní zobrazit telemetrii ze zařízení graficky.

     **Úlohy** umožňují spravovat zařízení ve velkém měřítku tím, že se spouštějí hromadné operace.

     **Šablony zařízení** jsou místo, kde můžete vytvářet a spravovat charakteristiky zařízení, která se připojují k vaší aplikaci.

     **Export dat** umožňuje nakonfigurovat průběžný export na externí služby – například úložiště a fronty.

     **Správa** je místo, kde můžete spravovat nastavení aplikace, přizpůsobení, fakturaci, uživatele a role.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Hledání, pomoc, motiv a podpora

Na každé stránce se zobrazuje horní nabídka:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="Panel nástrojů IoT Central":::

* Pokud chcete vyhledat zařízení, zadejte **hledanou** hodnotu.
* Chcete-li změnit jazyk uživatelského rozhraní nebo motiv, klikněte na ikonu **Nastavení** . Další informace o [správě předvoleb vaší aplikace](howto-manage-preferences.md)
* Pokud potřebujete nápovědu a podporu, otevřete rozevírací nabídku **Nápověda**, která obsahuje seznam zdrojů. [Informace o aplikaci můžete získat](./howto-get-app-info.md) z odkazu **o aplikaci** . V aplikaci podle cenového plánu zdarma zahrnuje prostředky podpory přístup k [živému chatu](howto-show-hide-chat.md).
* Pokud se chcete odhlásit z aplikace, vyberte ikonu **účtu** .

Můžete si vybrat mezi světlým a tmavým motivem uživatelského rozhraní:

> [!NOTE]
> Možnost výběru mezi světlými a tmavými motivy není k dispozici, pokud správce nakonfiguroval vlastní motiv pro aplikaci.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Snímek obrazovky IoT Central vyberte motiv.":::

### <a name="dashboard"></a>Řídicí panel

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Snímek obrazovky s řídicím panelem IoT Central.":::

* Řídicí panel je první stránkou, která se zobrazí při přihlášení k aplikaci Azure IoT Central. Můžete vytvořit a přizpůsobit více řídicích panelů aplikace. Další informace o [přidávání dlaždic do řídicího panelu](howto-add-tiles-to-your-dashboard.md)

* Je také možné vytvořit osobní řídicí panely, které vám umožní monitorovat, co se vám zajímá. Další informace najdete v článku o postupu [Vytvoření osobního řídicího panelu pro Azure IoT Central](howto-create-personal-dashboards.md) .

### <a name="devices"></a>Zařízení

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Obrazovka stránky zařízení.":::

Stránka Průzkumník zobrazuje _zařízení_ v aplikaci Azure IoT Central seskupená podle _šablony zařízení_. 

* Šablona zařízení definuje typ zařízení, které se může připojit k vaší aplikaci.
* Zařízení představuje v aplikaci buď skutečné, nebo simulované zařízení.

Další informace najdete v rychlém startu pro [monitorování vašich zařízení](./quick-monitor-devices.md) . 

### <a name="device-groups"></a>Skupiny zařízení

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Stránka skupiny zařízení":::

Skupina zařízení je kolekce souvisejících zařízení. Skupiny zařízení slouží k provádění hromadných operací ve vaší aplikaci. Další informace najdete v článku [použití skupin zařízení v aplikaci Azure IoT Central](tutorial-use-device-groups.md) .

### <a name="rules"></a>Pravidla
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Snímek obrazovky se stránkou pravidel":::

Stránka pravidla umožňuje definovat pravidla založená na telemetrie, stavu nebo událostech zařízení. Když se pravidlo aktivuje, může aktivovat jednu nebo více akcí – například odeslání e-mailu, upozornění externího systému prostřednictvím výstrah Webhooku atd. Další informace najdete v kurzu [Konfigurace pravidel](tutorial-create-telemetry-rules.md) . 

### <a name="analytics"></a>Analýzy

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Snímek stránky Analytics":::

Stránka Analytics umožňuje zobrazit telemetrii ze zařízení graficky v rámci časových řad. Další informace najdete v článku [Vytvoření analýzy pro Azure IoT Central aplikace](howto-create-analytics.md) .

### <a name="jobs"></a>Úlohy

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Stránka úlohy":::

Stránka úlohy umožňuje spustit hromadnou operaci na vašich zařízeních. Můžete aktualizovat vlastnosti zařízení, nastavení a spustit příkazy pro skupiny zařízení. Další informace najdete v článku o [spuštění úlohy](howto-run-a-job.md).

### <a name="device-templates"></a>Šablony zařízení

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Snímek obrazovky s šablonami zařízení":::

Stránka šablony zařízení je místo, kde můžete vytvářet a spravovat šablony zařízení v aplikaci. Šablona zařízení Určuje charakteristiky zařízení, jako například:

* Měření telemetrie, stavu a událostí
* Vlastnosti
* Příkazy
* Zobrazení

Další informace najdete v kurzu [Definování nového typu zařízení v aplikaci Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Export dat

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Export dat":::

Export dat umožňuje nastavit streamy dat na externí systémy. Další informace najdete v článku věnovaném [exportu dat do Azure IoT Central](./howto-export-data.md) .

### <a name="administration"></a>Správa

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Snímek správy IoT":::

Stránka Správa umožňuje nakonfigurovat a přizpůsobit svou IoT Central aplikaci. Tady můžete změnit název aplikace, adresu URL, možnosti správy, spravovat uživatele a role, vytvářet tokeny rozhraní API a exportovat aplikaci. Další informace najdete v článku [Správa aplikace Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled Azure IoT Central a orientujete se v rozložení uživatelského rozhraní. Jako další krok vám doporučujeme rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md).
