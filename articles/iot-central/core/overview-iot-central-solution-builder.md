---
title: Sestavování řešení pro Azure IoT Central | Microsoft Docs
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT. Tento článek poskytuje přehled o vytváření integrovaných řešení pomocí IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e762b8c2e2d7f72b89629c520560b205cedcd036
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728551"
---
# <a name="iot-central-solution-builder-guide"></a>Průvodce IoT Centralm tvůrcem řešení

*Tento článek se týká tvůrců řešení.*

IoT Central aplikace umožňuje monitorovat a spravovat miliony zařízení během jejich životního cyklu. Tato příručka je určená pro sestavovatele řešení, kteří používají IoT Central k vytváření integrovaných řešení. Aplikace IoT Central umožňuje spravovat zařízení, analyzovat telemetrii zařízení a integrovat s jinými back-endové služby.

Tvůrce řešení:

- Konfiguruje řídicí panely a zobrazení ve webovém uživatelském rozhraní IoT Central.
- Používá předdefinovaná pravidla a analytické nástroje k odvození obchodních přehledů z připojených zařízení.
- Pomocí možností exportu a pravidel pro data můžete integrovat IoT Central s dalšími back-endové služby.

## <a name="configure-dashboards-and-views"></a>Konfigurace řídicích panelů a zobrazení

Aplikace IoT Central může mít jeden nebo více řídicích panelů, které operátory používají k zobrazení a interakci s aplikací. Jako tvůrce řešení můžete přizpůsobit výchozí řídicí panel a vytvořit specializované řídicí panely:

- Některé příklady přizpůsobených řídicích panelů najdete v tématu věnovaném [průmyslovým šablonám](concepts-app-templates.md#industry-focused-templates).
- Další informace o řídicích panelech najdete v tématu [Vytvoření a Správa více řídicích panelů](howto-create-personal-dashboards.md) a [Konfigurace řídicího panelu aplikace](howto-add-tiles-to-your-dashboard.md).

Když se zařízení připojí k IoT Central, zařízení je přidružené k šabloně zařízení pro daný typ zařízení. Šablona zařízení má přizpůsobitelné zobrazení, které operátor používá ke správě jednotlivých zařízení. Jako vývojář řešení můžete vytvořit a přizpůsobit dostupná zobrazení pro typ zařízení. Další informace najdete v tématu věnovaném [Přidání zobrazení](howto-set-up-template.md#add-views).

## <a name="use-built-in-rules-and-analytics"></a>Použití předdefinovaných pravidel a analýz

Vývojář řešení může přidat pravidla do aplikace IoT Central, která spouští přizpůsobitelné akce. Pravidla vyhodnotí podmínky na základě dat přicházejících ze zařízení, aby bylo možné určit, kdy spustit akci. Další informace o pravidlech najdete v těchto tématech:

- [Kurz: Vytvoření pravidla a nastavení oznámení v aplikaci Azure IoT Central](tutorial-create-telemetry-rules.md)
- [Vytváření akcí webhooků u pravidel v Azure IoT Central](howto-create-webhooks.md)
- [Seskupit více akcí ke spuštění z jednoho nebo více pravidel](howto-use-action-groups.md)

IoT Central má integrované analytické možnosti, které operátor může použít k analýze toku dat z připojených zařízení. Další informace najdete v tématu [jak pomocí analýzy analyzovat data zařízení](howto-create-analytics.md).

## <a name="integrate-with-other-services"></a>Integrace s ostatními službami

Jako tvůrce řešení můžete použít možnosti exportu dat a pravidel v IoT Central pro integraci s jinými službami. Další informace najdete v následujících tématech:

- [Export dat IoT do cloudových cílů pomocí exportu dat](howto-export-data.md)
- [Transformovat data pro IoT Central](howto-transform-data.md)
- [Použití pracovních postupů k integraci aplikace IoT Central Azure s dalšími Cloud Services](howto-configure-rules-advanced.md)
- [Rozšíření Azure IoT Central o vlastní pravidla s využitím služeb Stream Analytics, Azure Functions a SendGrid](howto-create-custom-rules.md)
- [Rozšíření Azure IoT Central o vlastní analýzy s využitím služby Azure Databricks](howto-create-custom-analytics.md)
- [Vizualizace a analýza dat IoT Central Azure v řídicím panelu Power BI](howto-connect-powerbi.md)

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o použití IoT Central, je doporučeným dalším postupem vyzkoušet rychlé starty, počínaje [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md).
