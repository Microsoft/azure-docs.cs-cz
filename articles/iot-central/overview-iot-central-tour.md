---
title: Prohlídka uživatelského rozhraní Azure IoT Central | Microsoft Docs
description: Jako tvůrce se seznamte s klíčovými oblastmi uživatelského rozhraní Azure IoT Central, které umožňují vytvořit řešení IoT.
author: dominicbetts
ms.author: dobett
ms.date: 01/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 4908130cc0d59e42adb4f7ab2239e5b57dc08b89
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774298"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-new-ui-design"></a>Projděte si Azure IoT Central uživatelského rozhraní (návrh nové uživatelské rozhraní)

Tento článek vás seznámí s uživatelským rozhraním Microsoft Azure IoT Central. Pomocí uživatelského rozhraní můžete vytvořit, spravovat a používat řešení Azure IoT Central a připojená zařízení.

Jako _tvůrce_ můžete pomocí uživatelského rozhraní Azure IoT Central definovat své řešení Azure IoT Central. V uživatelském rozhraní můžete provést tyto kroky:

- Definovat typy zařízení, která se budou k řešení připojovat
- Nakonfigurovat pravidla a akce pro vaše zařízení
- Přizpůsobit uživatelské rozhraní pro _operátora_, který vaše řešení používá

Jako _operátor_ můžete pomocí uživatelského rozhraní Azure IoT Central spravovat řešení Azure IoT Central. V uživatelském rozhraní můžete provést tyto kroky:

- Monitorovat zařízení
- Konfigurovat zařízení
- Řešit potíže a odstraňovat problémy se zařízeními
- Zřizovat nová zařízení.


## <a name="use-the-left-navigation-menu"></a>Použití levé navigační nabídky

Levé navigační nabídce použijte pro přístup k oblasti jiné aplikace. Můžete rozbalit nebo Sbalit navigační panel tak, že vyberete **<** nebo **>**:

| Nabídka | Popis |
| ---- | ----------- |
| ![Levá navigační nabídka](media/overview-iot-central-tour/navigationbar.png) | <ul><li>**Řídicí panel** tlačítku zobrazí řídicí panel aplikací. Jako tvůrce můžete přizpůsobit řídicí panel pro operátory. Uživatelé mohou také vytvářet vlastní řídicí panely.</li><li>**Device Explorer** tlačítko seznamem skutečných a simulovaných zařízení přidružená k šabloně každé zařízení v aplikaci. Jako operátor můžete tlačítko **Device Explorer** (Průzkumník zařízení) použít ke správě připojených zařízení.</li><li>Tlačítko **Device Sets** (Sady zařízení) umožňuje zobrazit a nastavit sady zařízení. Jako operátor můžete vytvořit sady zařízení představující logické kolekce zařízení specifikovaných určitým dotazem.</li><li>Tlačítko **Analytics** (Analýzy) zobrazuje analýzy odvozené z telemetrických dat pro zařízení a sady zařízení. Jako operátor můžete vedle dat zařízení vytvářet vlastní zobrazení, která vám pomůžou získat z aplikace požadované informace.</li><li>Tlačítko **Jobs** (Úlohy) umožňuje hromadnou správu zařízení tím, že vás nechá vytvořit a spustit úlohy pro provedení aktualizací škálovaně.</li><li>**Šablon** zobrazovalo nástroje Tvůrce používá k vytváření a správě šablon zařízení.</li><li>**Průběžný Export dat** tlačítko správce ke konfiguraci průběžného exportu k jiným službám Azure, jako jsou úložiště a front.</li><li>Tlačítko **Administration** (Správa) zobrazí stránky správy aplikace, kde může správce spravovat nastavení aplikace, uživatele a role.</li></ul> |

## <a name="search-help-and-support"></a>Hledání, nápověda a podpora

Na každé stránce se zobrazuje horní nabídka:

![Panel nástrojů](media/overview-iot-central-tour/toolbar.png)

- Chcete-li hledat šablony zařízení a zařízení, zadejte **hledání** hodnotu.
- Chcete-li změnit jazyk uživatelského rozhraní nebo motivu, zvolte **nastavení** ikonu.
- Chcete-li odhlásit z aplikace, zvolte **účet** ikonu.
- Pokud potřebujete nápovědu a podporu, otevřete rozevírací nabídku **Nápověda**, která obsahuje seznam zdrojů.

Můžete si vybrat mezi světlým a tmavým motivem uživatelského rozhraní:

![Volba motivu uživatelského rozhraní](media/overview-iot-central-tour/themes.png)

## <a name="dashboard"></a>Řídicí panel

![Řídicí panel](media/overview-iot-central-tour/homepage.png)

Řídicí panel je první stránky, které se zobrazí při přihlášení do aplikace Azure IoT Central. Jako tvůrce můžete přizpůsobit řídicí panel aplikací pro ostatní uživatele tak, že přidáte dlaždice. Další informace najdete v kurzu [Přizpůsobení zobrazení Azure IoT Central pro operátora](tutorial-customize-operator.md). Uživatelé mohou také [vytváření vlastních osobních řídicích panelů](howto-personalize-dashboard.md).

## <a name="device-explorer"></a>Device Explorer

![Stránka Explorer (Průzkumník)](media/overview-iot-central-tour/explorer.png)

Zobrazí se stránka Průzkumník _zařízení_ v aplikaci Azure IoT Central, seskupené podle _šablona zařízení_.

* Šablona zařízení definuje typ zařízení, které se může připojit k vaší aplikaci. Další informace najdete v tématu [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type.md).
* Zařízení představuje v aplikaci buď skutečné, nebo simulované zařízení. Další informace najdete v tématu [Přidání nového zařízení do aplikace Azure IoT Central](tutorial-add-device.md).

## <a name="device-sets"></a>Sady zařízení

![Stránka Device Sets (Sady zařízení)](media/overview-iot-central-tour/devicesets.png)

Stránka _Device Sets_ (Sady zařízení) obsahuje sady zařízení vytvořené tvůrcem. Sada zařízení je kolekce souvisejících zařízení. Tvůrce definuje dotaz, který určí zařízení obsažená v určité sadě zařízení. Sady zařízení se používají při přizpůsobování analýz ve vaší aplikaci. Další informace najdete v článku [Použití sad zařízení v aplikaci Azure IoT Central](howto-use-device-sets.md).

## <a name="analytics"></a>Analýzy

![Stránka Analytics (Analýzy)](media/overview-iot-central-tour/analytics.png)

Stránka analýz obsahuje grafy, které vám pomáhají pochopit chování zařízení připojených k aplikaci. Operátor pomocí této stránky monitoruje a zkoumá problémy s připojenými zařízeními. Tvůrce může určit, jaké grafy se mají na této stránce zobrazovat. Další informace najdete v článku [Vytvoření vlastních analýz pro aplikaci Azure IoT Central](howto-use-device-sets.md).

## <a name="jobs"></a>Úlohy

![Stránka Jobs (Úlohy)](media/overview-iot-central-tour/jobs.png)

Stránka úloh vám umožňuje provádět operace hromadné správy vašeho zařízení. Tvůrce tuto stránku používá k aktualizaci vlastností zařízení, nastavení a příkazů. Další informace najdete v článku o [spuštění úlohy](howto-run-a-job.md).

## <a name="device-templates"></a>Šablony zařízení

![Stránka šablony zařízení](media/overview-iot-central-tour/templates.png)

Stránka zařízení šablony je společnost tvůrce vytvoří a spravuje zařízení šablony v aplikaci. Další informace najdete v kurzu [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Průběžný export dat

![Průběžný Export dat stránky](media/overview-iot-central-tour/export.png)

Stránka souvislá datová export je, kde správce definuje, jak exportovat data, jako je například telemetrických dat, z aplikace. Další služby můžete ukládat exportovaná data nebo ho použít k analýze. Další informace najdete v tématu [exportovat data v Azure IoT Central](howto-export-data.md) článku.

## <a name="administration"></a>Správa

![Stránka Administration (Správa)](media/overview-iot-central-tour/administration.png)

Stránka správy obsahuje odkazy na nástroje používané správcem, třeba k definování uživatelů a rolí v aplikaci. Další informace najdete v článku [Správa aplikace Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Další postup

Přečetli jste si přehled Azure IoT Central a orientujete se v rozložení uživatelského rozhraní. Jako další krok vám doporučujeme rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md).