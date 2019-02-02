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
ms.openlocfilehash: a5457299d0a0112685b194887802b4808a21d983
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666599"
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

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="use-the-left-navigation-menu"></a>Použití levé navigační nabídky

Pomocí levé navigační nabídky můžete získat přístup k různým oblastem aplikace:

| Nabídka | Popis |
| ---- | ----------- |
| ![Levá navigační nabídka](media/overview-iot-central-tour-experimental/navigationbar.png) | <ul><li>Tlačítko **Home** (Domů) zobrazí domovskou stránku vaší aplikace. Jako tvůrce můžete tuto domovskou stránku přizpůsobit pro operátory.</li><li>**Device Explorer** tlačítko seznamem skutečných a simulovaných zařízení přidružená k šabloně každé zařízení v aplikaci. Jako operátor můžete tlačítko **Device Explorer** (Průzkumník zařízení) použít ke správě připojených zařízení.</li><li>Tlačítko **Device Sets** (Sady zařízení) umožňuje zobrazit a nastavit sady zařízení. Jako operátor můžete vytvořit sady zařízení představující logické kolekce zařízení specifikovaných určitým dotazem.</li><li>Tlačítko **Analytics** (Analýzy) zobrazuje analýzy odvozené z telemetrických dat pro zařízení a sady zařízení. Jako operátor můžete vedle dat zařízení vytvářet vlastní zobrazení, která vám pomůžou získat z aplikace požadované informace.</li><li>Tlačítko **Jobs** (Úlohy) umožňuje hromadnou správu zařízení tím, že vás nechá vytvořit a spustit úlohy pro provedení aktualizací škálovaně.</li><li>**Šablon** zobrazovalo nástroje Tvůrce používá k vytváření a správě šablon zařízení.</li><li>**Průběžný Export dat** tlačítko správce ke konfiguraci průběžného exportu k jiným službám Azure, jako jsou úložiště a front.</li><li>Tlačítko **Administration** (Správa) zobrazí stránky správy aplikace, kde může správce spravovat nastavení aplikace, uživatele a role.</li></ul> |

## <a name="search-help-and-support"></a>Hledání, nápověda a podpora

Na každé stránce se zobrazuje horní nabídka:

![Panel nástrojů](media/overview-iot-central-tour-experimental/toolbar.png)

- Pokud chcete hledat zařízení a šablony zařízení, použijte ikonu **Hledání**.
- Chcete-li změnit jazyk uživatelského rozhraní, zvolte **jazyk** ikonu.
- Pokud potřebujete nápovědu a podporu, otevřete rozevírací nabídku **Nápověda**, která obsahuje seznam zdrojů.
- Chcete-li změnit motiv uživatelského rozhraní nebo odhlásit z aplikace, zvolte **účet** ikonu.

Můžete si vybrat mezi světlým a tmavým motivem uživatelského rozhraní:

![Volba motivu uživatelského rozhraní](media/overview-iot-central-tour-experimental/themes.png)

## <a name="home-page"></a>Domovská stránka

![Domovská stránka](media/overview-iot-central-tour-experimental/homepage.png)

Domovská stránka je první stránka, která se zobrazí, když se přihlásíte do aplikace Azure IoT Central. Jako tvůrce můžete domovskou stránku přizpůsobit pro jiné uživatele aplikace pomocí dlaždic. Další informace najdete v kurzu [Přizpůsobení zobrazení Azure IoT Central pro operátora](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="device-explorer"></a>Průzkumník zařízení

![Stránka Explorer (Průzkumník)](media/overview-iot-central-tour-experimental/explorer.png)

Zobrazí se stránka Průzkumník _zařízení_ v aplikaci Azure IoT Central, seskupené podle _šablona zařízení_.

* Šablona zařízení definuje typ zařízení, které se může připojit k vaší aplikaci. Další informace najdete v tématu [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* Zařízení představuje v aplikaci buď skutečné, nebo simulované zařízení. Další informace najdete v tématu [Přidání nového zařízení do aplikace Azure IoT Central](tutorial-add-device.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="device-sets"></a>Sady zařízení

![Stránka Device Sets (Sady zařízení)](media/overview-iot-central-tour-experimental/devicesets.png)

Stránka _Device Sets_ (Sady zařízení) obsahuje sady zařízení vytvořené tvůrcem. Sada zařízení je kolekce souvisejících zařízení. Tvůrce definuje dotaz, který určí zařízení obsažená v určité sadě zařízení. Sady zařízení se používají při přizpůsobování analýz ve vaší aplikaci. Další informace najdete v článku [Použití sad zařízení v aplikaci Azure IoT Central](howto-use-device-sets.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="analytics"></a>Analýzy

![Stránka Analytics (Analýzy)](media/overview-iot-central-tour-experimental/analytics.png)

Stránka analýz obsahuje grafy, které vám pomáhají pochopit chování zařízení připojených k aplikaci. Operátor pomocí této stránky monitoruje a zkoumá problémy s připojenými zařízeními. Tvůrce může určit, jaké grafy se mají na této stránce zobrazovat. Další informace najdete v článku [Vytvoření vlastních analýz pro aplikaci Azure IoT Central](howto-create-analytics.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="jobs"></a>Úlohy

![Stránka Jobs (Úlohy)](media/overview-iot-central-tour-experimental/jobs.png)

Stránka úloh vám umožňuje provádět operace hromadné správy vašeho zařízení. Tvůrce tuto stránku používá k aktualizaci vlastností zařízení, nastavení a příkazů. Další informace najdete v článku o [spuštění úlohy](howto-run-a-job.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="device-templates"></a>Šablony zařízení

![Stránka šablony zařízení](media/overview-iot-central-tour-experimental/templates.png)

Stránka zařízení šablony je společnost tvůrce vytvoří a spravuje zařízení šablony v aplikaci. Další informace najdete v kurzu [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="continuous-data-export"></a>Průběžný export dat

![Průběžný Export dat stránky](media/overview-iot-central-tour-experimental/export.png)

Stránka souvislá datová export je, kde správce definuje, jak exportovat data, jako je například telemetrických dat, z aplikace. Další služby můžete ukládat exportovaná data nebo ho použít k analýze. Další informace najdete v tématu [exportovat data v Azure IoT Central](howto-export-data.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) článku.

## <a name="administration"></a>Správa

![Stránka Administration (Správa)](media/overview-iot-central-tour-experimental/administration.png)

Stránka správy obsahuje odkazy na nástroje používané správcem, třeba k definování uživatelů a rolí v aplikaci. Další informace najdete v článku [Správa aplikace Azure IoT Central](howto-administer.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="next-steps"></a>Další postup

Přečetli jste si přehled Azure IoT Central a orientujete se v rozložení uživatelského rozhraní. Jako další krok vám doporučujeme rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).