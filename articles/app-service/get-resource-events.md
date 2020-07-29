---
title: Získat události prostředků v Azure App Service
description: Naučte se, jak získat události prostředku prostřednictvím protokolů aktivit a Event Grid v aplikaci App Service.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 7075e3eacc85198e22a9aa0e53f67a22416b2678
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83649068"
---
# <a name="get-resource-events-in-azure-app-service"></a>Získat události prostředků v Azure App Service

Azure App Service poskytuje integrované nástroje pro sledování stavu a stavu vašich prostředků. Události prostředků vám pomůžou pochopit všechny změny provedené v prostředcích webových aplikací a provádět v případě potřeby akci. Mezi příklady událostí patří: škálování instancí, aktualizace nastavení aplikace, restartování webové aplikace a spousta dalších. V tomto článku se dozvíte, jak zobrazit [protokoly aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) a povolit [Event Grid](https://docs.microsoft.com/azure/event-grid/) monitorování událostí prostředků souvisejících s vaší App Service webovou aplikací.

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

[Zobrazit a načíst události protokolu aktivit Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>Odeslání protokolů aktivit do Event Grid

I když jsou protokoly aktivit založené na uživatelích, je k dispozici nová [Event Grid](https://docs.microsoft.com/azure/event-grid/) integrace s App Service (Preview), která protokoluje akce uživatelů a automatizované události. Pomocí Event Grid můžete nakonfigurovat obslužnou rutinu, aby reagovala na tyto události. Pomocí služby Event Grid třeba můžete pokaždé, když se do kontejneru úložiště objektů blob přidá nová fotka, okamžitě aktivovat funkci bez serveru, která spustí analýzu obrázku.

Nebo můžete pomocí služeb Event Grid a Logic Apps zpracovávat data kdekoli bez psaní kódu. Event Grid propojuje zdroje dat a obslužné rutiny událostí. Pomocí služby Event Grid třeba můžete pokaždé, když se do kontejneru úložiště objektů blob přidá nová fotka, okamžitě aktivovat funkci bez serveru, která spustí analýzu obrázku.

[Zobrazení vlastností a schématu pro události Azure App Service.](https://docs.microsoft.com/azure/event-grid/event-schema-app-service)

## <a name="next-steps"></a><a name="nextsteps"></a>Další kroky
* [Dotazování protokolů pomocí Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [Jak monitorovat Azure App Service](web-sites-monitor.md)
* [Řešení potíží s Azure App Service v aplikaci Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analýza protokolů aplikací v HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
