---
title: Zobrazení událostí protokolu aktivit Azure v Azure Monitoru
description: Zobrazení protokolu aktivit Azure v Azure Monitor u načíst pomocí PowerShell, CLI a REST API.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d2423d04ead9040cce53d847d24efe75be680d94
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397310"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Zobrazení a načtení událostí protokolu aktivit Azure

[Protokol aktivit Azure](platform-logs-overview.md) poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Tento článek obsahuje podrobnosti o různých metodách pro zobrazení a načítání událostí protokolu aktivit.

## <a name="azure-portal"></a>portál Azure
Zobrazení protokolu aktivit pro všechny prostředky z nabídky **Monitorování** na webu Azure Portal. Zobrazení protokolu aktivit pro konkrétní prostředek z **možnosti Protokol aktivit** v nabídce daného zdroje.

![Zobrazit protokol aktivit](./media/activity-logs-overview/view-activity-log.png)

Události protokolu aktivit můžete filtrovat podle následujících polí:

* **Timespan**: Počáteční a koncový čas událostí.
* **Kategorie**: Kategorie událostí popsaná v [části Kategorie v protokolu aktivit](activity-log-view.md#categories-in-the-activity-log).
* **Předplatné**: Jeden nebo více názvů předplatného Azure.
* **Skupina prostředků**: Jedna nebo více skupin prostředků v rámci vybraných předplatných.
* **Zdroj (název)**: - Název konkrétního zdroje.
* **Typ prostředku**: Typ prostředku, například _Microsoft.Compute/virtualmachines_.
* **Název operace** – název operace Azure Resource Manager, například _Microsoft.SQL/servers/Write_.
* **Závažnost**: Úroveň závažnosti události. Dostupné hodnoty jsou _Informační_, _Upozornění_, _Chyba_, _Kritická_.
* **Událost iniciovaná uživatelem**: Uživatel, který operaci provedl.
* **Otevřít hledání**: Otevřete textové vyhledávací pole, které hledá tento řetězec ve všech polích ve všech událostech.

## <a name="categories-in-the-activity-log"></a>Kategorie v protokolu aktivit
Každá událost v protokolu aktivit má určitou kategorii, která je popsána v následující tabulce. Podrobné informace o schématu těchto kategorií naleznete v [tématu Schéma událostí protokolu aktivit Azure](activity-log-schema.md). 

| Kategorie | Popis |
|:---|:---|
| Správa | Obsahuje záznam všech operací vytvoření, aktualizace, odstranění a akce prováděných prostřednictvím Správce prostředků. Mezi příklady událostí správy patří _vytvoření virtuálního počítače_ a _odstranění skupiny zabezpečení sítě_.<br><br>Každá akce provedená uživatelem nebo aplikací pomocí Správce prostředků je modelována jako operace pro určitý typ prostředku. Pokud je typem operace _Write_, _Delete_nebo _Action_, budou záznamy zahájení i úspěchu nebo selhání této operace zaznamenány v kategorii Správa. Události správy také zahrnují všechny změny řízení přístupu na základě rolí v předplatném. |
| Service Health | Obsahuje záznam všech incidentů stavu služby, ke kterým došlo v Azure. Příklad události stavu služby _SQL Azure ve východní USA dochází k prostoj- výtky_. <br><br>Události stavu služby se domýšlijí v šesti variantách: _požadovaná akce_, _asistované zotavení_, _incidenty_, _údržba_, _informace_nebo _zabezpečení_. Tyto události jsou vytvořeny pouze v případě, že máte prostředek v předplatném, které by byly ovlivněny událostí.
| Resource Health | Obsahuje záznam všech událostí stavu prostředků, ke kterým došlo k prostředkům Azure. Příkladem události stavu prostředků je _stav virtuálního počítače změněn na nedostupný_.<br><br>Události stavu prostředků mohou představovat jeden ze čtyř stavů: _K dispozici_, _Není k dispozici_, _Degradován_a _Neznámý_. Události stavu prostředků lze navíc kategorizovat jako _spuštěné platformy_ nebo _inicializovány uživatelem_. |
| Výstrahy | Obsahuje záznam aktivací pro výstrahy Azure. Příkladem události Alert je _CPU % na myVM bylo více než 80 za posledních 5 minut_.|
| Automatické škálování | Obsahuje záznam všech událostí souvisejících s provozem modulu automatického škálování na základě všech nastavení automatického škálování, které jste definovali v předplatném. Příkladem události automatického škálování je _akce automatického škálování se nezdařilo_. |
| Doporučení | Obsahuje události doporučení z Azure Advisor. |
| Zabezpečení | Obsahuje záznam všech výstrah generovaných službou Azure Security Center. Příkladem události zabezpečení je _spuštění podezřelého souboru dvojité přípony_. |
| Zásada | Obsahuje záznamy o všech operacích akce efekt uprováděných zásady Azure. Příklady událostí zásad zahrnují _audit_ a _odepřít_. Každá akce přijatá zásadami je modelována jako operace s prostředkem. |

## <a name="view-change-history"></a>Zobrazit historii změn

Při kontrole protokolu aktivit může pomoci zjistit, jaké změny se během této události odehrály. Tyto informace můžete zobrazit pomocí **položky Historie změn**. Vyberte událost z protokolu aktivit, do kterého se chcete podívat hlouběji. Výběrem karty **Historie změn (Náhled)** zobrazíte všechny přidružené změny s tou událostí.

![Změna seznamu historie pro událost](media/activity-logs-overview/change-history-event.png)

Pokud k události dojde ke změnám přidruženým, zobrazí se seznam změn, které můžete vybrat. Otevře se stránka **Historie změn (náhled).** Na této stránce se zobrazí změny prostředku. Jak můžete vidět z následujícího příkladu, můžeme vidět nejen to, že virtuální virtuální počítače změnily velikosti, ale jaká byla předchozí velikost virtuálního počítače před změnou a na co byla změněna.

![Stránka Změnit historii zobrazující rozdíly](media/activity-logs-overview/change-history-event-details.png)

Další informace o historii změn najdete v [tématu Získání změn zdrojů](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Pomocí rutiny [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) načtěte protokol aktivit z prostředí PowerShell. Níže jsou uvedeny některé běžné příklady.

> [!NOTE]
> `Get-AzLog`poskytuje pouze 15 dní historie. Pomocí parametru **-MaxEvents** můžete dotazovat na poslední události N za 15 dní. Chcete-li získat přístup k událostem starším než 15 dní, použijte rozhraní REST API nebo sadu SDK. Pokud nezahrnete **starttime**, pak je výchozí hodnota **EndTime** mínus jedna hodina. Pokud nezahrnete **EndTime**, bude výchozí hodnota aktuální čas. Všechny časy jsou v UTC.


Získejte položky protokolu vytvořené po určitém časovém čase data:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Získejte položky protokolu mezi časovým obdobím data:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získejte položky protokolu z určité skupiny prostředků:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Získejte položky protokolu od určitého poskytovatele prostředků mezi časovým rozsahem data:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získejte položky protokolu s konkrétním volajícím:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Získejte posledních 1000 událostí:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>Rozhraní příkazového řádku
Pomocí [protokolu aktivity sledování az](cli-samples.md#view-activity-log-for-a-subscription) načíst protokol aktivit z CLI. Níže jsou uvedeny některé běžné příklady.


Zobrazení všech dostupných možností.

```azurecli
az monitor activity-log list -h
```

Získejte položky protokolu z určité skupiny prostředků:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Získejte položky protokolu s konkrétním volajícím:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Získejte protokoly volajícího na typu prostředku v rámci období:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
Pomocí [rozhraní API azure monitoru REST](https://docs.microsoft.com/rest/api/monitor/) načtěte protokol aktivit z klienta REST. Níže jsou uvedeny některé běžné příklady.

Získat protokoly aktivit s filtrem:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Získejte protokoly aktivit s filtrem a vyberte:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Získat protokoly aktivit s výběrem:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Získejte protokoly aktivit bez filtru nebo vyberte:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Další kroky

* [Přečtěte si přehled protokolů platformy](platform-logs-overview.md)
* [Vytvoření diagnostického nastavení pro odesílání protokolů aktivit do jiných cílů](diagnostic-settings.md)
