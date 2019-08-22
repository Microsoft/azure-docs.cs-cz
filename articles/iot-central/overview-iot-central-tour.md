---
title: Prohlídka uživatelského rozhraní Azure IoT Central | Microsoft Docs
description: Jako tvůrce se seznamte s klíčovými oblastmi uživatelského rozhraní Azure IoT Central, které umožňují vytvořit řešení IoT.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6c06d789e6a005c0079f889cead6a75f4d0baf19
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875702"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Prohlídka uživatelského rozhraní Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

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

Pro přístup k různým oblastem aplikace použijte levou navigační nabídku. Navigační panel můžete rozbalit nebo sbalit výběrem možnosti **<** nebo: **>**

| Nabídka | Popis |
| ---- | ----------- |
| ![Levá navigační nabídka](media/overview-iot-central-tour/navigationbar.png) | <ul><li>Na tlačítku **řídicího panelu** se zobrazí řídicí panel aplikace. Jako tvůrce můžete řídicí panel přizpůsobit svým operátorům. Uživatelé také mohou vytvořit vlastní řídicí panely.</li><li>Tlačítko **Device Explorer** uvádí simulovaná a skutečná zařízení přidružená ke každé šabloně zařízení v aplikaci. Jako operátor můžete tlačítko **Device Explorer** (Průzkumník zařízení) použít ke správě připojených zařízení.</li><li>Tlačítko **Device Sets** (Sady zařízení) umožňuje zobrazit a nastavit sady zařízení. Jako operátor můžete vytvořit sady zařízení představující logické kolekce zařízení specifikovaných určitým dotazem.</li><li>Tlačítko **Analytics** (Analýzy) zobrazuje analýzy odvozené z telemetrických dat pro zařízení a sady zařízení. Jako operátor můžete vedle dat zařízení vytvářet vlastní zobrazení, která vám pomůžou získat z aplikace požadované informace.</li><li>Tlačítko **Jobs** (Úlohy) umožňuje hromadnou správu zařízení tím, že vás nechá vytvořit a spustit úlohy pro provedení aktualizací škálovaně.</li><li>Tlačítko **šablony zařízení** zobrazuje nástroje, které tvůrce používá k vytváření a správě šablon zařízení.</li><li>Tlačítko pro **Export průběžných dat** správce může nakonfigurovat průběžný export na jiné služby Azure, jako je například úložiště a fronty.</li><li>Tlačítko **Administration** (Správa) zobrazí stránky správy aplikace, kde může správce spravovat nastavení aplikace, uživatele a role.</li></ul> |

## <a name="search-help-and-support"></a>Hledání, nápověda a podpora

Na každé stránce se zobrazuje horní nabídka:

![Panel nástrojů](media/overview-iot-central-tour/toolbar.png)

- Pokud chcete vyhledat šablony zařízení a zařízení, zadejte **hledanou** hodnotu.
- Chcete-li změnit jazyk uživatelského rozhraní nebo motiv, klikněte na ikonu **Nastavení** .
- Pokud se chcete odhlásit z aplikace, vyberte ikonu **účtu** .
- Pokud potřebujete nápovědu a podporu, otevřete rozevírací nabídku **Nápověda**, která obsahuje seznam zdrojů. V aplikaci zkušební verze zahrnují prostředky podpory přístup k živému [chatu](howto-show-hide-chat.md).

Můžete si vybrat mezi světlým a tmavým motivem uživatelského rozhraní:

![Volba motivu uživatelského rozhraní](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> Možnost výběru mezi světlými a tmavými motivy není k dispozici, pokud správce nakonfiguroval vlastní motiv pro aplikaci.

## <a name="dashboard"></a>Řídicí panel

![Řídicí panel](media/overview-iot-central-tour/homepage.png)

* Řídicí panel je první stránkou, která se zobrazí při přihlášení k aplikaci Azure IoT Central. Jako tvůrce můžete řídicí panel aplikace přizpůsobit ostatním uživatelům přidáním dlaždic. Další informace najdete v kurzu [Přizpůsobení zobrazení Azure IoT Central pro operátora](tutorial-customize-operator.md).

* Jako operátor můžete vytvářet přizpůsobené řídicí panely a přepínat mezi nimi a výchozím řídicím panelem. Další informace najdete v článku o tom, jak [vytvořit a spravovat osobní řídicí panely](howto-personalize-dashboard.md) .

## <a name="device-explorer"></a>Device Explorer

![Stránka Explorer (Průzkumník)](media/overview-iot-central-tour/explorer.png)

Stránka Průzkumník zobrazuje _zařízení_ v aplikaci Azure IoT Central seskupená podle _šablony zařízení_.

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

Stránka úlohy umožňuje spuštění operací správy hromadných zařízení v zařízeních. Tvůrce tuto stránku používá k aktualizaci vlastností zařízení, nastavení a příkazů. Další informace najdete v článku o [spuštění úlohy](howto-run-a-job.md).

## <a name="device-templates"></a>Šablony zařízení

![Stránka šablony zařízení](media/overview-iot-central-tour/templates.png)

Stránka šablony zařízení je tam, kde tvůrce vytvoří a spravuje šablony zařízení v aplikaci. Šablona zařízení Určuje charakteristiky zařízení, například:

- Měření telemetrie, stavu a událostí.
- Nastavení a vlastnosti.
- Příkaz.
- Pravidla založená na událostech nebo hodnotách telemetrie.

Další informace najdete v kurzu [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Nepřetržitý export dat

![Stránka průběžného exportu dat](media/overview-iot-central-tour/export.png)

Stránka průběžného exportu dat je tam, kde správce definuje způsob, jakým se z aplikace streamují data, jako je telemetrie. Další služby mohou ukládat exportovaná data nebo je použít k analýze. Další informace najdete v článku věnovaném [exportu dat do Azure IoT Central](howto-export-data.md) .

## <a name="administration"></a>Správa

![Stránka Správa](media/overview-iot-central-tour/administration.png)

Stránka Správa obsahuje odkazy na nástroje, které správce používá, jako je definování uživatelů a rolí v aplikaci, a přizpůsobení uživatelského rozhraní. Další informace najdete v článku [Správa aplikace Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Další postup

Přečetli jste si přehled Azure IoT Central a orientujete se v rozložení uživatelského rozhraní. Jako další krok vám doporučujeme rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central.md).