---
title: Zobrazit události protokolu aktivit Azure ve službě Azure Monitor
description: Zobrazení protokolu aktivit Azure ve službě Azure Monitor a načíst pomocí Powershellu, rozhraní příkazového řádku a rozhraní REST API.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248111"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Zobrazení a načíst události protokolu aktivit Azure

[Protokolu aktivit Azure](activity-logs-overview.md) poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo v Azure. Tento článek obsahuje podrobnosti o různých metodách pro zobrazení a načítání události protokolu aktivit.

## <a name="azure-portal"></a>portál Azure
Zobrazení protokolu aktivit pro všechny prostředky z **monitorování** nabídky na webu Azure Portal. Zobrazení protokolu aktivit pro určitý prostředek z **protokolu aktivit** v nabídce tohoto prostředku.

![Zobrazení protokolu aktivit](./media/activity-logs-overview/view-activity-log.png)

Můžete filtrovat události protokolu aktivit na základě následujících polí:

* **Časový interval**: Počáteční a koncový čas pro události.
* **Kategorie**: Kategorie události, jak je popsáno v [kategorií v protokolu aktivit](activity-logs-overview.md#categories-in-the-activity-log).
* **Předplatné**: Jeden nebo více názvů předplatného Azure.
* **Skupina prostředků**: Jeden nebo více skupinami prostředků v rámci vybrané odběry.
* **Zdroje (název)** :-název konkrétní prostředek.
* **Typ prostředku**: Typ prostředku, například _Microsoft.Compute/virtualmachines_.
* **Název operace** -název operace Azure Resource Manageru, třeba _Microsoft.SQL/servers/Write_.
* **Závažnost**: Úroveň závažnosti události. Dostupné jsou hodnoty _informativní_, _upozornění_, _chyba_, _kritický_.
* **Zahájit událost**: Uživatel, který provedl operaci.
* **Otevřít hledání**: Otevřít textové vyhledávací pole, která hledá pro tento řetězec ve všech polích ve všech událostí.

### <a name="view-change-history"></a>Zobrazení historie změn

Při kontrole protokolu aktivit, může pomoct podívejte se změny během této doby události. Můžete zobrazit tyto informace se **historii změn**. Vyberte událost od protokolů aktivit, které chcete podívat hlouběji do. Vyberte **změnit historii (Preview)** kartu, chcete-li zobrazit všechny přidružené změny této události.

![Seznam historie změn pro událost](media/activity-logs-overview/change-history-event.png)

Pokud jsou všechny související změny s událostí, zobrazí se vám seznam změn, které můžete vybrat. Otevře **změnit historii (Preview)** stránky. Na této stránce se zobrazí změny prostředku. Jak je vidět z následujícího příkladu máme nejen uvidíte, že virtuální počítač změně velikosti, ale předchozí velikost virtuálního počítače se před provedením změny a co bylo upraveno pro.

![Změnit stránku historie zobrazující rozdíly](media/activity-logs-overview/change-history-event-details.png)

Další informace o historii změn, naleznete v tématu [získat změn prostředků](../../governance/resource-graph/how-to/get-resource-changes.md).


## <a name="log-analytics-workspace"></a>Pracovní prostor Log Analytics
Klikněte na tlačítko **protokoly** v horní části **protokolu aktivit** otevřít stránku [Activity Log Analytics, řešení pro monitorování](activity-log-collect.md) pro předplatné. To vám umožní zobrazit analýzu protokolu aktivit a spuštění [protokolu dotazy](../log-query/log-query-overview.md) s **AzureActivity** tabulky. Pokud váš protokol aktivit není připojený k pracovnímu prostoru Log Analytics, budete vyzváni k provedení této konfigurace.



## <a name="powershell"></a>PowerShell
Použití [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) rutina pro načtení protokolu aktivit z Powershellu. Toto jsou některé běžné příklady.

> [!NOTE]
> `Get-AzLog` poskytuje pouze 15 dní historie. Použití **- MaxEvents** parametr poslední událostí N se po uplynutí 15 dnů. Pro přístup k události starší než 15 dnů, pomocí rozhraní REST API nebo sady SDK. Pokud není zadána **StartTime**, pak je výchozí hodnota **EndTime** mínus jedna hodina. Pokud není zadána **EndTime**, pak výchozí hodnotou je aktuální čas. Všechny časy jsou ve formátu UTC.


Získáte položky protokolu byly vytvořeny po uplynutí určité doby konkrétní datum:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Získáte položky protokolu mezi datem časový rozsah:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získáte položky protokolu v konkrétní skupině prostředků:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Získáte položky protokolu ze zprostředkovatele konkrétní prostředek mezi datem časový rozsah:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získáte položky protokolu s konkrétním volající:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Získání posledních 1000 událostí:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>Rozhraní příkazového řádku
Použití [az monitorování – protokol aktivit](cli-samples.md#view-activity-log-for-a-subscription) pro načtení protokolu aktivit z příkazového řádku. Toto jsou některé běžné příklady.


Zobrazte všechny dostupné možnosti.

```azurecli
az monitor activity-log list -h
```

Získáte položky protokolu v konkrétní skupině prostředků:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Získáte položky protokolu s konkrétním volající:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Získání protokolů volajícím na typ prostředku v rámci rozsah dat:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
Použití [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) pro načtení protokolu aktivit z klienta REST. Toto jsou některé běžné příklady.

Získání protokolů aktivit pomocí filtru:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Získáte protokoly aktivit se filtr a vyberte:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Získání protokolů aktivit s vybranými:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Získání protokolů aktivit bez filtru nebo vyberte:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Další kroky

* [Přečtěte si přehled protokolu aktivit](activity-logs-overview.md)
* [Archivace protokolu aktivit do úložiště nebo Streamovat do centra událostí](activity-log-export.md)
* [Stream protokolů aktivit Azure do služby Event Hubs](activity-logs-stream-event-hubs.md)
* [Archivace protokolu aktivit Azure do služby storage](archive-activity-log.md)

