---
title: Prohlídka uživatelského rozhraní Azure IoT Central | Microsoft Docs
description: Seznamte se s klíčovými oblastmi centrálního uznaného Azure IoT, které používáte k vytváření, správě a používání řešení IoT.
author: lmasieri
ms.author: lmasieri
ms.date: 12/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: b905b1e86810b25c4c94072d6cd414b993e2a883
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77425991"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Prohlídka uživatelského rozhraní Azure IoT Central



Tento článek vás seznámí s uživatelským rozhraním Microsoft Azure IoT Central. Pomocí uživatelského rozhraní můžete vytvořit, spravovat a používat řešení Azure IoT Central a připojená zařízení.

Jako _tvůrce řešení_používáte centrální uznané azure ioT k definování vašeho řešení Azure IoT Central. V uživatelském rozhraní můžete provést tyto kroky:

* Definovat typy zařízení, která se budou k řešení připojovat
* Nakonfigurovat pravidla a akce pro vaše zařízení 
* Přizpůsobit uživatelské rozhraní pro _operátora_, který vaše řešení používá

Jako _operátor_ můžete pomocí uživatelského rozhraní Azure IoT Central spravovat řešení Azure IoT Central. V uživatelském rozhraní můžete provést tyto kroky:

* Monitorovat zařízení
* Konfigurovat zařízení
* Řešit potíže a odstraňovat problémy se zařízeními
* Zřizovat nová zařízení.

## <a name="iot-central-homepage"></a>Domovská stránka IoT Central

Domovská [stránka IoT Central](https://aka.ms/iotcentral-get-started) je místo, kde se můžete dozvědět více o nejnovějších zprávách a funkcích dostupných na IoT Central, vytvořit nové aplikace a zobrazit a spustit stávající aplikaci.

> [!div class="mx-imgBorder"]
> ![Domovská stránka IoT Central](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Vytvoření aplikace

V části Sestavení můžete procházet seznam šablon IoT Central relevantní pro odvětví, které vám pomůžou rychle začít, nebo začít od začátku pomocí vlastní šablony aplikace.  
> [!div class="mx-imgBorder"]
> ![Stránka sestavení IoT Central](media/overview-iot-central-tour/iot-central-build-pnp.png)

Další informace najdete v [tématu Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md) rychlý start.

### <a name="launch-your-application"></a>Spuštění aplikace

Aplikaci IoT Central můžete spustit tak, že přejdete na adresu URL, kterou vy nebo váš tvůrce řešení vyberete při vytváření aplikací. Seznam všech aplikací, ke kterých máte přístup, najdete taky ve [Správci aplikací IoT Central](https://aka.ms/iotcentral-apps).

> [!div class="mx-imgBorder"]
> ![Správce aplikací IoT Central](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navigace v aplikaci

Jakmile jste uvnitř aplikace IoT, použijte levé podokno pro přístup k různým oblastem. Levé podokno můžete rozbalit nebo sbalit výběrem ikony se třemi podšívkou v horní části podokna:

> [!NOTE]
> Položky, které vidíte v levém podokně, závisí na roli uživatele. Přečtěte si další informace o [správě uživatelů a rolí](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![levé podokno](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Řídicí panel** zobrazuje řídicí panel aplikace. Jako *tvůrce řešení*můžete přizpůsobit globální řídicí panel pro vaše operátory. V závislosti na roli uživatele mohou operátoři také vytvářet vlastní osobní řídicí panely.
     
     **Zařízení** vám umožní spravovat připojená zařízení - skutečná a simulovaná.

     **Skupiny zařízení** umožňuje zobrazit a vytvořit logické kolekce zařízení určených dotazem. Tento dotaz můžete uložit a použít skupiny zařízení prostřednictvím aplikace k provádění hromadných operací.

     **Pravidla** umožňují vytvářet a upravovat pravidla pro sledování vašich zařízení. Pravidla jsou vyhodnocována na základě telemetrie zařízení a spouštějí přizpůsobitelné akce.

     **Služba Analytics** umožňuje vytvářet vlastní zobrazení nad daty zařízení, která odvozují přehledy z vaší aplikace.

     **Úlohy** umožňují spravovat vaše zařízení ve velkém měřítku spuštěním hromadných operací.

     **Šablony zařízení** je místo, kde můžete vytvořit a spravovat vlastnosti zařízení, která se připojují k vaší aplikaci.

     **Export dat** umožňuje konfigurovat nepřetržitý export do externích služeb , jako je například úložiště a fronty.

     **Správa** je místo, kde můžete spravovat nastavení aplikace, přizpůsobení, fakturaci, uživatele a role.

     **IoT Central** umožňuje *správcům* přejít zpět na Správce aplikací ioT Central.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Vyhledávání, nápověda, motiv a podpora

Na každé stránce se zobrazuje horní nabídka:

> [!div class="mx-imgBorder"]
> ![Panel nástrojů](media/overview-iot-central-tour/toolbar-pnp.png)

* Chcete-li vyhledat šablony zařízení a zařízení, zadejte hodnotu **Hledat.**
* Chcete-li změnit jazyk nebo motiv ui, zvolte ikonu **Nastavení.** Další informace o [správě předvoleb aplikací](howto-manage-preferences.md)
* Chcete-li se z aplikace odhlásit, zvolte ikonu **Účet.**
* Pokud potřebujete nápovědu a podporu, otevřete rozevírací nabídku **Nápověda**, která obsahuje seznam zdrojů. V aplikaci o volném cenovém plánu zahrnují zdroje podpory přístup k [živému chatu](howto-show-hide-chat.md).

Můžete si vybrat mezi světlým a tmavým motivem uživatelského rozhraní:

> [!NOTE]
> Možnost volby mezi světlá a tmavá témata není k dispozici, pokud správce nakonfiguroval vlastní motiv pro aplikaci.

> [!div class="mx-imgBorder"]
> ![Volba motivu uživatelského rozhraní](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Řídicí panel
> [!div class="mx-imgBorder"]
> ![Řídicí panel](media/overview-iot-central-tour/dashboard-pnp.png)

* Řídicí panel je první stránka, která se zobrazí při přihlášení k aplikaci Azure IoT Central. Jako *tvůrce řešení*můžete vytvářet a přizpůsobovat více řídicích panelů globálních aplikací pro ostatní uživatele. Další informace o [přidávání dlaždic na řídicí panel](howto-add-tiles-to-your-dashboard.md)

* Jako *operátor*, pokud to vaše role uživatele umožňuje, můžete vytvořit osobní řídicí panely pro sledování toho, co vás zajímá. Další informace najdete v [článku vytvoření osobních řídicích panelů Azure IoT Central.](howto-create-personal-dashboards.md)

### <a name="devices"></a>Zařízení

> [!div class="mx-imgBorder"]
> ![Stránka Zařízení](media/overview-iot-central-tour/devices-pnp.png)

Na stránce průzkumníka se zobrazují _zařízení_ v aplikaci Azure IoT Central seskupená podle _šablony zařízení_. 

* Šablona zařízení definuje typ zařízení, které se může připojit k vaší aplikaci.
* Zařízení představuje v aplikaci buď skutečné, nebo simulované zařízení.

Další informace najdete v tématu Rychlý start [sledování zařízení.](./quick-monitor-devices.md) 

### <a name="device-groups"></a>skupiny zařízení.

> [!div class="mx-imgBorder"]
> ![Stránka Skupiny zařízení](media/overview-iot-central-tour/device-groups-pnp.png)

Skupina zařízení je kolekce souvisejících zařízení. *Tvůrce řešení* definuje dotaz k identifikaci zařízení, která jsou součástí skupiny zařízení. Skupiny zařízení se používají k provádění hromadných operací v aplikaci. Další informace najdete v tématu Použití skupin zařízení v článku [aplikace Azure IoT Central.](tutorial-use-device-groups.md)

### <a name="rules"></a>Pravidla
> [!div class="mx-imgBorder"]
> ![Stránka Pravidla](media/overview-iot-central-tour/rules-pnp.png)

Stránka pravidla umožňuje definovat pravidla na základě telemetrie, stavu nebo událostí zařízení. Když se pravidlo spustí, může spustit jednu nebo více akcí - například odeslání e-mailu, upozornění externího systému prostřednictvím upozornění webhooku atd. Další informace naleznete v kurzu [Konfigurace pravidel.](tutorial-create-telemetry-rules.md) 

### <a name="analytics"></a>Analýza

> [!div class="mx-imgBorder"]
> ![Stránka Analytics (Analýzy)](media/overview-iot-central-tour/analytics-pnp.png)

Analýza umožňuje vytvářet vlastní zobrazení nad daty zařízení, abyste získali přehledy z vaší aplikace. Další informace najdete v článku [Vytvoření analýzy pro vaši aplikaci Azure IoT Central.](howto-create-analytics.md)

### <a name="jobs"></a>Úlohy

> [!div class="mx-imgBorder"]
> ![Stránka Jobs (Úlohy)](media/overview-iot-central-tour/jobs-pnp.png)

Stránka úlohy umožňuje spouštět hromadné operace správy zařízení na vašich zařízeních. Vlastnosti zařízení, nastavení a spouštění příkazů můžete aktualizovat ve skupinách zařízení. Další informace najdete v článku o [spuštění úlohy](howto-run-a-job.md).

### <a name="device-templates"></a>Šablony zařízení

> [!div class="mx-imgBorder"]
> ![Stránka předlohy zařízení](media/overview-iot-central-tour/templates-pnp.png)

Stránka šablon zařízení je místo, kde tvůrce vytváří a spravuje šablony zařízení v aplikaci. Šablona zařízení určuje charakteristiky zařízení, jako jsou:

* Telemetrie, měření stavu a událostí
* Vlastnosti
* Příkazy
* Zobrazení

*Tvůrce řešení* můžete také vytvářet formuláře a řídicí panely pro operátory pro správu zařízení.

Další informace najdete v kurzu [Definování nového typu zařízení v aplikaci Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Export dat
> [!div class="mx-imgBorder"]
> ![Stránka exportu dat](media/overview-iot-central-tour/export-pnp.png)

Export dat umožňuje nastavit proudy dat, jako je například telemetrie, z aplikace do externích systémů. Další informace najdete [v článku Export dat v azure ioT central.](./howto-export-data.md)

### <a name="administration"></a>Správa
> [!div class="mx-imgBorder"]
> ![Stránka Administration (Správa)](media/overview-iot-central-tour/administration-pnp.png)

Stránka správy umožňuje konfigurovat a přizpůsobovat aplikaci IoT Central. Zde můžete změnit název aplikace, adresu URL, šablony, spravovat uživatele a role, vytvářet tokeny rozhraní API a exportovat aplikaci. Další informace najdete v článku [Správa aplikace Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled Azure IoT Central a orientujete se v rozložení uživatelského rozhraní. Jako další krok vám doporučujeme rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md).
