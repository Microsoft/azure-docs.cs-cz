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
ms.openlocfilehash: 569a1365e73acbc2fdaf351f2e2cff21181241e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523452"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Prohlídka uživatelského rozhraní Azure IoT Central

Tento článek vás seznámí s uživatelským rozhraním Microsoft Azure IoT Central. Pomocí uživatelského rozhraní můžete vytvořit, spravovat a používat řešení Azure IoT Central a připojená zařízení.

Jako _Tvůrce řešení_ použijete uživatelské rozhraní Azure IoT Central k definování řešení Azure IoT Central. V uživatelském rozhraní můžete provést tyto kroky:

* Definovat typy zařízení, která se budou k řešení připojovat
* Nakonfigurovat pravidla a akce pro vaše zařízení 
* Přizpůsobit uživatelské rozhraní pro _operátora_, který vaše řešení používá

Jako _operátor_ můžete pomocí uživatelského rozhraní Azure IoT Central spravovat řešení Azure IoT Central. V uživatelském rozhraní můžete provést tyto kroky:

* Monitorovat zařízení
* Konfigurovat zařízení
* Řešit potíže a odstraňovat problémy se zařízeními
* Zřizovat nová zařízení.

## <a name="iot-central-homepage"></a>IoT Central domovskou stránku

Stránka [domovské stránky IoT Central](https://aka.ms/iotcentral-get-started) je místo, kde se můžete dozvědět víc o nejnovějších novinkách a funkcích, které jsou k dispozici v IoT Central, vytváření nových aplikací a zobrazení a spuštění stávající aplikace.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="IoT Central domovskou stránku":::

### <a name="create-an-application"></a>Vytvoření aplikace

V části sestavení můžete procházet seznam relevantních šablon IoT Central, které vám pomůžou rychle začít, nebo začít úplně od začátku pomocí šablony vlastní aplikace.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="Stránka buildu IoT Central":::

Další informace najdete v tématu věnovaném rychlému startu [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md) .

### <a name="launch-your-application"></a>Spuštění aplikace

Aplikaci IoT Central můžete spustit tak, že přejdete na adresu URL, kterou vy nebo váš tvůrce řešení zvolíte během vytváření aplikace. Seznam všech aplikací, ke kterým máte přístup, můžete zobrazit také ve [správci aplikací IoT Central](https://aka.ms/iotcentral-apps).

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
     **Řídicí** panel zobrazuje řídicí panel aplikace. Jako *Tvůrce řešení* můžete přizpůsobit globální řídicí panel pro vaše operátory. V závislosti na své roli uživatele mohou operátoři vytvářet také vlastní osobní řídicí panely.
     
     **Zařízení** umožňují spravovat připojená zařízení – reálné a simulované.

     **Skupiny zařízení** umožňují zobrazení a vytváření logických kolekcí zařízení určených dotazem. Můžete uložit tento dotaz a použít skupiny zařízení v aplikaci k provádění hromadných operací.

     **Pravidla** umožňují vytvářet a upravovat pravidla pro monitorování vašich zařízení. Pravidla se vyhodnocují na základě telemetrie zařízení a triggerů přizpůsobitelných akcí.

     **Analýza** vám umožní vytvořit vlastní zobrazení nad daty zařízení a odvodit z aplikace přehledy.

     **Úlohy** umožňují spravovat zařízení ve velkém měřítku tím, že se spouštějí hromadné operace.

     **Šablony zařízení** jsou místo, kde můžete vytvářet a spravovat charakteristiky zařízení, která se připojují k vaší aplikaci.

     **Export dat** umožňuje nakonfigurovat průběžný export na externí služby – například úložiště a fronty.

     **Správa** je místo, kde můžete spravovat nastavení aplikace, přizpůsobení, fakturaci, uživatele a role.

     **IoT Central** umožňuje *správcům* přejít zpátky na IoT Central App Manager.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Hledání, pomoc, motiv a podpora

Na každé stránce se zobrazuje horní nabídka:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="Panel nástrojů IoT Central":::

* Pokud chcete vyhledat šablony zařízení a zařízení, zadejte **hledanou** hodnotu.
* Chcete-li změnit jazyk uživatelského rozhraní nebo motiv, klikněte na ikonu **Nastavení** . Další informace o [správě předvoleb vaší aplikace](howto-manage-preferences.md)
* Pokud potřebujete nápovědu a podporu, otevřete rozevírací nabídku **Nápověda**, která obsahuje seznam zdrojů. [Informace o aplikaci můžete získat](./howto-get-app-info.md) z odkazu **o aplikaci** . V aplikaci podle cenového plánu zdarma zahrnuje prostředky podpory přístup k [živému chatu](howto-show-hide-chat.md).
* Pokud se chcete odhlásit z aplikace, vyberte ikonu **účtu** .

Můžete si vybrat mezi světlým a tmavým motivem uživatelského rozhraní:

> [!NOTE]
> Možnost výběru mezi světlými a tmavými motivy není k dispozici, pokud správce nakonfiguroval vlastní motiv pro aplikaci.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Snímek obrazovky IoT Central vyberte motiv.":::

### <a name="dashboard"></a>Řídicí panel

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Snímek obrazovky s řídicím panelem IoT Central.":::

* Řídicí panel je první stránkou, která se zobrazí při přihlášení k aplikaci Azure IoT Central. Jako *Tvůrce řešení* můžete vytvořit a přizpůsobit několik globálních řídicích panelů aplikace pro ostatní uživatele. Další informace o [přidávání dlaždic do řídicího panelu](howto-add-tiles-to-your-dashboard.md)

* Pokud to vaše uživatelská role umožňuje, můžete jako *operátor* vytvořit osobní řídicí panely, abyste mohli monitorovat, co vás zajímá. Další informace najdete v článku o postupu [Vytvoření osobního řídicího panelu pro Azure IoT Central](howto-create-personal-dashboards.md) .

### <a name="devices"></a>Zařízení

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Obrazovka stránky zařízení.":::

Stránka Průzkumník zobrazuje _zařízení_ v aplikaci Azure IoT Central seskupená podle _šablony zařízení_. 

* Šablona zařízení definuje typ zařízení, které se může připojit k vaší aplikaci.
* Zařízení představuje v aplikaci buď skutečné, nebo simulované zařízení.

Další informace najdete v rychlém startu pro [monitorování vašich zařízení](./quick-monitor-devices.md) . 

### <a name="device-groups"></a>Skupiny zařízení

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Stránka skupiny zařízení":::

Skupina zařízení je kolekce souvisejících zařízení. *Tvůrce řešení* definuje dotaz k identifikaci zařízení, která jsou obsažena ve skupině zařízení. Skupiny zařízení slouží k provádění hromadných operací ve vaší aplikaci. Další informace najdete v článku [použití skupin zařízení v aplikaci Azure IoT Central](tutorial-use-device-groups.md) .

### <a name="rules"></a>Pravidla
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Snímek obrazovky se stránkou pravidel":::

Stránka pravidla umožňuje definovat pravidla založená na telemetrie, stavu nebo událostech zařízení. Když se pravidlo aktivuje, může aktivovat jednu nebo více akcí – například odeslání e-mailu, upozornění externího systému prostřednictvím výstrah Webhooku atd. Další informace najdete v kurzu [Konfigurace pravidel](tutorial-create-telemetry-rules.md) . 

### <a name="analytics"></a>Analýzy

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Snímek stránky Analytics":::

Analýza vám umožní vytvořit vlastní zobrazení nad daty zařízení a odvodit z ní přehledy. Další informace najdete v článku [Vytvoření analýzy pro Azure IoT Central aplikace](howto-create-analytics.md) .

### <a name="jobs"></a>Úlohy

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Stránka úlohy":::

Stránka úlohy umožňuje spuštění operací správy hromadných zařízení v zařízeních. Můžete aktualizovat vlastnosti zařízení, nastavení a spustit příkazy pro skupiny zařízení. Další informace najdete v článku o [spuštění úlohy](howto-run-a-job.md).

### <a name="device-templates"></a>Šablony zařízení

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Snímek obrazovky s šablonami zařízení":::

Stránka šablony zařízení je tam, kde tvůrce vytvoří a spravuje šablony zařízení v aplikaci. Šablona zařízení Určuje charakteristiky zařízení, jako například:

* Měření telemetrie, stavu a událostí
* Vlastnosti
* Příkazy
* Zobrazení

*Tvůrce řešení* také může vytvořit formuláře a řídicí panely pro operátory, které slouží ke správě zařízení.

Další informace najdete v kurzu [Definování nového typu zařízení v aplikaci Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Export dat

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Export dat":::

Export dat umožňuje nastavit proudy dat, jako je telemetrie, z aplikace až po externí systémy. Další informace najdete v článku věnovaném [exportu dat do Azure IoT Central](./howto-export-data.md) .

### <a name="administration"></a>Správa

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Snímek správy IoT":::

Stránka Správa umožňuje nakonfigurovat a přizpůsobit svou IoT Central aplikaci. Tady můžete změnit název aplikace, adresu URL, možnosti správy, spravovat uživatele a role, vytvářet tokeny rozhraní API a exportovat aplikaci. Další informace najdete v článku [Správa aplikace Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled Azure IoT Central a orientujete se v rozložení uživatelského rozhraní. Jako další krok vám doporučujeme rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md).
