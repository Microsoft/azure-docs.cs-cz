---
title: Získat události prostředků v Azure App Service
description: Naučte se, jak získat události prostředku prostřednictvím protokolů aktivit a Event Grid v aplikaci App Service.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c20028a4f84dae9d292cf855a1e164bd69864909
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574031"
---
# <a name="get-resource-events-in-azure-app-service"></a>Získat události prostředků v Azure App Service

Azure App Service poskytuje integrované nástroje pro sledování stavu a stavu vašich prostředků. Události prostředků vám pomůžou pochopit všechny změny provedené v prostředcích webových aplikací a provádět v případě potřeby akci. Mezi příklady událostí patří: škálování instancí, aktualizace nastavení aplikace, restartování webové aplikace a spousta dalších. V tomto článku se dozvíte, jak zobrazit [protokoly aktivit Azure](../azure-monitor/essentials/activity-log.md#view-the-activity-log) a povolit [Event Grid](../event-grid/index.yml) monitorování událostí prostředků souvisejících s vaší App Service webovou aplikací.

> [!NOTE]
> App Service integrace s Event Grid je ve **verzi Preview**. [Další podrobnosti najdete v oznámení.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Zobrazení protokolů aktivit Azure
Protokoly aktivit Azure obsahují události prostředků emitované operacemi provedenými u prostředků v rámci vašeho předplatného. Akce uživatelů v šablonách Azure Portal a Azure Resource Manager přispívají k událostem zachyceným protokolem aktivit. 

Protokoly aktivit Azure pro App Service podrobnosti, jako například:
- jaké operace byly provedeny u prostředků (např. App Service plánů)
- Kdo operaci zahájil
- Při výskytu operace
- stav operace
- hodnoty vlastností, které vám pomůžou s výzkumem operace

### <a name="what-can-you-do-with-azure-activity-logs"></a>Co se dá dělat s protokoly aktivit Azure?

K protokolům aktivit Azure se dá dotázat pomocí Azure Portal, PowerShellu, REST API nebo rozhraní příkazového řádku. Protokoly můžete odeslat do účtu úložiště, centra událostí a Log Analytics. Můžete je také analyzovat v Power BI nebo vytvářet výstrahy, abyste si mohli zůstat aktualizovat události prostředků.

[Zobrazit a načíst události protokolu aktivit Azure.](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>Odeslání protokolů aktivit do Event Grid

I když jsou protokoly aktivit založené na uživatelích, je k dispozici nová [Event Grid](../event-grid/index.yml) integrace s App Service (Preview), která protokoluje akce uživatelů a automatizované události. Pomocí Event Grid můžete nakonfigurovat obslužnou rutinu, aby reagovala na tyto události. Pomocí služby Event Grid třeba můžete pokaždé, když se do kontejneru úložiště objektů blob přidá nová fotka, okamžitě aktivovat funkci bez serveru, která spustí analýzu obrázku.

Nebo můžete pomocí služeb Event Grid a Logic Apps zpracovávat data kdekoli bez psaní kódu. Event Grid propojuje zdroje dat a obslužné rutiny událostí. Pomocí služby Event Grid třeba můžete pokaždé, když se do kontejneru úložiště objektů blob přidá nová fotka, okamžitě aktivovat funkci bez serveru, která spustí analýzu obrázku.

[Zobrazení vlastností a schématu pro události Azure App Service.](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> Další kroky
* [Dotazování protokolů pomocí Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Jak monitorovat Azure App Service](web-sites-monitor.md)
* [Řešení potíží s Azure App Service v aplikaci Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analýza protokolů aplikací v HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)