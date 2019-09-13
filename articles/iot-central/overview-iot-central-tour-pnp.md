---
title: Prohlídka uživatelského rozhraní Azure IoT Central | Microsoft Docs
description: Jako tvůrce se seznamte s klíčovými oblastmi uživatelského rozhraní Azure IoT Central, které umožňují vytvořit řešení IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 82996db232fde0424ccc8e3e478a70a5892231e6
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884825"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui-preview-features"></a>Prohlídka uživatelského rozhraní Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Tento článek vás seznámí s uživatelským rozhraním Microsoft Azure IoT Central. Pomocí uživatelského rozhraní můžete vytvořit, spravovat a používat řešení Azure IoT Central a připojená zařízení.

Jako _tvůrce_ můžete pomocí uživatelského rozhraní Azure IoT Central definovat své řešení Azure IoT Central. V uživatelském rozhraní můžete provést tyto kroky:

* Definovat typy zařízení, která se budou k řešení připojovat
* Nakonfigurovat pravidla a akce pro vaše zařízení
* Přizpůsobit uživatelské rozhraní pro _operátora_, který vaše řešení používá

Jako _operátor_ můžete pomocí uživatelského rozhraní Azure IoT Central spravovat řešení Azure IoT Central. V uživatelském rozhraní můžete provést tyto kroky:

* Monitorovat zařízení
* Konfigurovat zařízení
* Řešit potíže a odstraňovat problémy se zařízeními
* Zřizovat nová zařízení.

## <a name="use-the-left-navigation-menu"></a>Použití levé navigační nabídky

Pro přístup k různým oblastem aplikace použijte levou navigační nabídku. Navigační panel můžete rozbalit nebo sbalit výběrem možnosti **<** nebo: **>**

:::row:::
  :::column span="":::
      ![Left navigation menu](media/overview-iot-central-tour-pnp/navigationbar.png)
  :::column-end:::
  :::column span="2":::
     **Řídicí** panel zobrazuje řídicí panel aplikace. Jako tvůrce můžete řídicí panel přizpůsobit svým operátorům. Uživatelé také mohou vytvořit vlastní řídicí panely.
     
     **Zařízení** vypisuje simulovaná a skutečná zařízení přidružená ke každé šabloně zařízení v aplikaci. Jako operátor můžete tlačítko **Device Explorer** (Průzkumník zařízení) použít ke správě připojených zařízení.

     **Skupiny zařízení** vám umožní zobrazit a vytvořit skupiny zařízení. Jako operátor můžete vytvořit skupiny zařízení jako logické kolekce zařízení, která jsou určená dotazem.

     **Pravidla** umožňují upravovat pravidla, která se aktivují v závislosti na telemetrie zařízení a triggerech přizpůsobitelných akcí.

     **Analytics** zobrazuje analýzy odvozené ze telemetrie zařízení pro zařízení a skupiny zařízení. Jako operátor můžete vedle dat zařízení vytvářet vlastní zobrazení, která vám pomůžou získat z aplikace požadované informace.

     **Úlohy** umožňují hromadnou správu zařízení s vytvářením a spouštěním úloh, které aktualizují vaše zařízení ve velkém měřítku.

     **Šablony zařízení** zobrazí nástroje, které tvůrce používá k vytváření a správě šablon zařízení.

     Při **exportu dat** může správce nakonfigurovat průběžný export na jiné služby Azure, jako je například úložiště a fronty.

     **Správa** zobrazuje stránky pro správu aplikací, kde správce může spravovat nastavení aplikací, uživatele a role.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Hledání, nápověda a podpora

Na každé stránce se zobrazuje horní nabídka:

![Panel nástrojů](media/overview-iot-central-tour-pnp/toolbar.png)

* Pokud chcete vyhledat šablony zařízení a zařízení, zadejte **hledanou** hodnotu.
* Chcete-li změnit jazyk uživatelského rozhraní nebo motiv, klikněte na ikonu **Nastavení** .
* Pokud se chcete odhlásit z aplikace, vyberte ikonu **účtu** .
* Pokud potřebujete nápovědu a podporu, otevřete rozevírací nabídku **Nápověda**, která obsahuje seznam zdrojů. V aplikaci zkušební verze zahrnují prostředky podpory přístup k [živému chatu](howto-show-hide-chat.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Můžete si vybrat mezi světlým a tmavým motivem uživatelského rozhraní:

![Volba motivu uživatelského rozhraní](media/overview-iot-central-tour-pnp/themes.png)

> [!NOTE]
> Možnost výběru mezi světlými a tmavými motivy není k dispozici, pokud správce nakonfiguroval vlastní motiv pro aplikaci.

## <a name="dashboard"></a>Řídicí panel

![Řídicí panel](media/overview-iot-central-tour-pnp/homepage.png)

* Řídicí panel je první stránkou, která se zobrazí při přihlášení k aplikaci Azure IoT Central. Jako tvůrce můžete řídicí panel aplikace přizpůsobit ostatním uživatelům přidáním dlaždic. Další informace najdete v kurzu [Vytvoření šablony zařízení](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

* Jako operátor můžete vytvářet přizpůsobené řídicí panely a přepínat mezi nimi a výchozím řídicím panelem. Další informace najdete v článku o tom, jak [vytvořit a spravovat osobní řídicí panely](howto-personalize-dashboard.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="devices"></a>Zařízení

![Stránka zařízení](media/overview-iot-central-tour-pnp/explorer.png)

Stránka Průzkumník zobrazuje _zařízení_ v aplikaci Azure IoT Central seskupená podle _šablony zařízení_.

* Šablona zařízení definuje typ zařízení, které se může připojit k vaší aplikaci. Další informace najdete v tématu [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
* Zařízení představuje v aplikaci buď skutečné, nebo simulované zařízení. Další informace najdete v tématu [Přidání nového zařízení do aplikace Azure IoT Central](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-groups"></a>Skupiny zařízení

![Stránka skupin zařízení](media/overview-iot-central-tour-pnp/devicesets.png)

Stránka _skupiny zařízení_ zobrazuje skupiny zařízení vytvořené tvůrcem. Skupina zařízení je kolekce souvisejících zařízení. Tvůrce definuje dotaz k identifikaci zařízení, která jsou obsažena ve skupině zařízení. Skupiny zařízení používáte při přizpůsobování analýz ve vaší aplikaci. Další informace najdete v článku [použití skupin zařízení v aplikaci Azure IoT Central](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="rules"></a>Pravidla

![Stránka pravidla](media/overview-iot-central-tour-pnp/rules.png)

Stránka pravidla umožňuje definovat pravidla založená na událostech telemetrie, stavu zařízení nebo zařízení. Když je pravidlo aktivováno, může aktivovat akci, jako je například odeslání e-mailu operátoru. Tvůrce používá tuto stránku k vytváření a správě pravidel. Další informace najdete v kurzu [Konfigurace pravidel a akcí pro vaše zařízení v Azure IoT Central](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) kurzu.

## <a name="analytics"></a>Analýzy

![Stránka Analytics (Analýzy)](media/overview-iot-central-tour-pnp/analytics.png)

Stránka analýz obsahuje grafy, které vám pomáhají pochopit chování zařízení připojených k aplikaci. Operátor pomocí této stránky monitoruje a zkoumá problémy s připojenými zařízeními. Tvůrce může určit, jaké grafy se mají na této stránce zobrazovat. Další informace najdete v článku [Vytvoření vlastních analýz pro aplikaci Azure IoT Central](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="jobs"></a>Úlohy

![Stránka Jobs (Úlohy)](media/overview-iot-central-tour-pnp/jobs.png)

Stránka úlohy umožňuje spuštění operací správy hromadných zařízení v zařízeních. Tvůrce tuto stránku používá k aktualizaci vlastností zařízení, nastavení a příkazů. Další informace najdete v článku o [spuštění úlohy](howto-run-a-job.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="device-templates"></a>Šablony zařízení

![Stránka šablony zařízení](media/overview-iot-central-tour-pnp/templates.png)

Stránka šablony zařízení je tam, kde tvůrce vytvoří a spravuje šablony zařízení v aplikaci. Šablona zařízení Určuje charakteristiky zařízení, například:

* Měření telemetrie, stavu a událostí.
* Vlastnosti.
* Příkaz.

Tvůrce také může vytvořit formuláře a řídicí panely pro operátory, které slouží ke správě zařízení.

Další informace najdete v kurzu [Definování nového typu zařízení v aplikaci Azure IoT Central](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="data-export"></a>Export dat

![Stránka pro export dat](media/overview-iot-central-tour-pnp/export.png)

Stránka pro export dat je tam, kde správce definuje způsob streamování dat, jako je telemetrie, z aplikace. Další služby mohou ukládat exportovaná data nebo je použít k analýze. Další informace najdete v článku věnovaném [exportu dat do Azure IoT Central](howto-export-data.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

## <a name="administration"></a>Správa

![Stránka Správa](media/overview-iot-central-tour-pnp/administration.png)

Stránka Správa obsahuje odkazy na nástroje, které správce používá, jako je definování uživatelů a rolí v aplikaci, a přizpůsobení uživatelského rozhraní. Další informace najdete v článku [Správa aplikace Azure IoT Central](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled Azure IoT Central a orientujete se v rozložení uživatelského rozhraní. Jako další krok vám doporučujeme rychlý start [Vytvoření aplikace Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
