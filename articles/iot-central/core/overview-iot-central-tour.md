---
title: Prohlídka uživatelského rozhraní Azure IoT Central | Microsoft Docs
description: Seznamte se s klíčovými oblastmi uživatelského rozhraní Azure IoT Central, které můžete použít k vytvoření, správě a používání řešení IoT.
author: lmasieri
ms.author: lmasieri
ms.date: 12/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: c07d247358d980152648fdf7f702c8efcb72bab9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90020069"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Prohlídka uživatelského rozhraní Azure IoT Central

Tento článek vás seznámí s uživatelským rozhraním Microsoft Azure IoT Central. Pomocí uživatelského rozhraní můžete vytvořit, spravovat a používat řešení Azure IoT Central a připojená zařízení.

Jako _Tvůrce řešení_použijete uživatelské rozhraní Azure IoT Central k definování řešení Azure IoT Central. V uživatelském rozhraní můžete provést tyto kroky:

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

> [!div class="mx-imgBorder"]
> ![IoT Central domovskou stránku](media/overview-iot-central-tour/iot-central-homepage-pnp.png)

### <a name="create-an-application"></a>Vytvoření aplikace

V části sestavení můžete procházet seznam relevantních šablon IoT Central, které vám pomůžou rychle začít, nebo začít úplně od začátku pomocí šablony vlastní aplikace.  
> [!div class="mx-imgBorder"]
> ![Stránka buildu IoT Central](media/overview-iot-central-tour/iot-central-build-pnp.png)

Další informace najdete v tématu věnovaném rychlému startu [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md) .

### <a name="launch-your-application"></a>Spuštění aplikace

Aplikaci IoT Central můžete spustit tak, že přejdete na adresu URL, kterou vy nebo váš tvůrce řešení zvolíte během vytváření aplikace. Seznam všech aplikací, ke kterým máte přístup, můžete zobrazit také ve [správci aplikací IoT Central](https://aka.ms/iotcentral-apps).

> [!div class="mx-imgBorder"]
> ![Správce aplikací IoT Central](media/overview-iot-central-tour/app-manager-pnp.png)

## <a name="navigate-your-application"></a>Navigace v aplikaci

Až budete v aplikaci IoT, můžete k různým oblastem přistupovat v levém podokně. V levém podokně můžete rozbalit nebo sbalit tak, že v horní části podokna vyberete ikonu se třemi řádky:

> [!NOTE]
> Položky, které vidíte v levém podokně, závisí na vaší roli uživatele. Přečtěte si další informace o [správě uživatelů a rolí](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      > [!div class="mx-imgBorder"]
      > ![levé podokno](media/overview-iot-central-tour/navigationbar-pnp.png)
  :::column-end:::
  :::column span="2":::
     **Řídicí** panel zobrazuje řídicí panel aplikace. Jako *Tvůrce řešení*můžete přizpůsobit globální řídicí panel pro vaše operátory. V závislosti na své roli uživatele mohou operátoři vytvářet také vlastní osobní řídicí panely.
     
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

> [!div class="mx-imgBorder"]
> ![Panel nástrojů](media/overview-iot-central-tour/toolbar-pnp.png)

* Pokud chcete vyhledat šablony zařízení a zařízení, zadejte **hledanou** hodnotu.
* Chcete-li změnit jazyk uživatelského rozhraní nebo motiv, klikněte na ikonu **Nastavení** . Další informace o [správě předvoleb vaší aplikace](howto-manage-preferences.md)
* Pokud se chcete odhlásit z aplikace, vyberte ikonu **účtu** .
* Pokud potřebujete nápovědu a podporu, otevřete rozevírací nabídku **Nápověda**, která obsahuje seznam zdrojů. [Informace o aplikaci můžete získat](./howto-get-app-info.md) z odkazu **o aplikaci** . V aplikaci podle cenového plánu zdarma zahrnuje prostředky podpory přístup k [živému chatu](howto-show-hide-chat.md).

Můžete si vybrat mezi světlým a tmavým motivem uživatelského rozhraní:

> [!NOTE]
> Možnost výběru mezi světlými a tmavými motivy není k dispozici, pokud správce nakonfiguroval vlastní motiv pro aplikaci.

> [!div class="mx-imgBorder"]
> ![Volba motivu uživatelského rozhraní](media/overview-iot-central-tour/themes-pnp.png)

### <a name="dashboard"></a>Řídicí panel
> [!div class="mx-imgBorder"]
> ![Řídicí panel](media/overview-iot-central-tour/dashboard-pnp.png)

* Řídicí panel je první stránkou, která se zobrazí při přihlášení k aplikaci Azure IoT Central. Jako *Tvůrce řešení*můžete vytvořit a přizpůsobit několik globálních řídicích panelů aplikace pro ostatní uživatele. Další informace o [přidávání dlaždic do řídicího panelu](howto-add-tiles-to-your-dashboard.md)

* Pokud to vaše uživatelská role umožňuje, můžete jako *operátor*vytvořit osobní řídicí panely, abyste mohli monitorovat, co vás zajímá. Další informace najdete v článku o postupu [Vytvoření osobního řídicího panelu pro Azure IoT Central](howto-create-personal-dashboards.md) .

### <a name="devices"></a>Zařízení

> [!div class="mx-imgBorder"]
> ![Stránka zařízení](media/overview-iot-central-tour/devices-pnp.png)

Stránka Průzkumník zobrazuje _zařízení_ v aplikaci Azure IoT Central seskupená podle _šablony zařízení_. 

* Šablona zařízení definuje typ zařízení, které se může připojit k vaší aplikaci.
* Zařízení představuje v aplikaci buď skutečné, nebo simulované zařízení.

Další informace najdete v rychlém startu pro [monitorování vašich zařízení](./quick-monitor-devices.md) . 

### <a name="device-groups"></a>skupiny zařízení.

> [!div class="mx-imgBorder"]
> ![Stránka skupin zařízení](media/overview-iot-central-tour/device-groups-pnp.png)

Skupina zařízení je kolekce souvisejících zařízení. *Tvůrce řešení* definuje dotaz k identifikaci zařízení, která jsou obsažena ve skupině zařízení. Skupiny zařízení slouží k provádění hromadných operací ve vaší aplikaci. Další informace najdete v článku [použití skupin zařízení v aplikaci Azure IoT Central](tutorial-use-device-groups.md) .

### <a name="rules"></a>Pravidla
> [!div class="mx-imgBorder"]
> ![Stránka Pravidla](media/overview-iot-central-tour/rules-pnp.png)

Stránka pravidla umožňuje definovat pravidla založená na telemetrie, stavu nebo událostech zařízení. Když se pravidlo aktivuje, může aktivovat jednu nebo více akcí – například odeslání e-mailu, upozornění externího systému prostřednictvím výstrah Webhooku atd. Další informace najdete v kurzu [Konfigurace pravidel](tutorial-create-telemetry-rules.md) . 

### <a name="analytics"></a>Analýzy

> [!div class="mx-imgBorder"]
> ![Stránka Analytics (Analýzy)](media/overview-iot-central-tour/analytics-pnp.png)

Analýza vám umožní vytvořit vlastní zobrazení nad daty zařízení a odvodit z ní přehledy. Další informace najdete v článku [Vytvoření analýzy pro Azure IoT Central aplikace](howto-create-analytics.md) .

### <a name="jobs"></a>Úlohy

> [!div class="mx-imgBorder"]
> ![Stránka Jobs (Úlohy)](media/overview-iot-central-tour/jobs-pnp.png)

Stránka úlohy umožňuje spuštění operací správy hromadných zařízení v zařízeních. Můžete aktualizovat vlastnosti zařízení, nastavení a spustit příkazy pro skupiny zařízení. Další informace najdete v článku o [spuštění úlohy](howto-run-a-job.md).

### <a name="device-templates"></a>Šablony zařízení

> [!div class="mx-imgBorder"]
> ![Stránka šablony zařízení](media/overview-iot-central-tour/templates-pnp.png)

Stránka šablony zařízení je tam, kde tvůrce vytvoří a spravuje šablony zařízení v aplikaci. Šablona zařízení Určuje charakteristiky zařízení, jako například:

* Měření telemetrie, stavu a událostí
* Vlastnosti
* Příkazy
* Zobrazení

*Tvůrce řešení* také může vytvořit formuláře a řídicí panely pro operátory, které slouží ke správě zařízení.

Další informace najdete v kurzu [Definování nového typu zařízení v aplikaci Azure IoT Central](howto-set-up-template.md). 

### <a name="data-export"></a>Export dat
> [!div class="mx-imgBorder"]
> ![Stránka pro export dat](media/overview-iot-central-tour/export-pnp.png)

Export dat umožňuje nastavit proudy dat, jako je telemetrie, z aplikace až po externí systémy. Další informace najdete v článku věnovaném [exportu dat do Azure IoT Central](./howto-export-data.md) .

### <a name="administration"></a>Správa
> [!div class="mx-imgBorder"]
> ![Stránka Administration (Správa)](media/overview-iot-central-tour/administration-pnp.png)

Stránka Správa umožňuje nakonfigurovat a přizpůsobit svou IoT Central aplikaci. Tady můžete změnit název aplikace, adresu URL, možnosti správy, spravovat uživatele a role, vytvářet tokeny rozhraní API a exportovat aplikaci. Další informace najdete v článku [Správa aplikace Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled Azure IoT Central a orientujete se v rozložení uživatelského rozhraní. Jako další krok vám doporučujeme rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md).
