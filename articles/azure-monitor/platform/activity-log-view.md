---
title: Zobrazení událostí protokolu aktivit Azure v Azure Monitor
description: Podívejte se na protokol aktivit Azure v Azure Monitor a načtěte ho pomocí PowerShellu, rozhraní příkazového řádku a REST API.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d2423d04ead9040cce53d847d24efe75be680d94
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80397310"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Zobrazení a načtení událostí protokolu aktivit Azure

[Protokol aktivit Azure](platform-logs-overview.md) poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Tento článek poskytuje podrobné informace o různých metodách pro zobrazení a načítání událostí protokolu aktivit.

## <a name="azure-portal"></a>portál Azure
Zobrazení protokolu aktivit pro všechny prostředky z nabídky **monitorování** v Azure Portal. Podívejte se na protokol aktivit pro konkrétní prostředek z možnosti **Protokol aktivit** v nabídce příslušného prostředku.

![Zobrazit protokol aktivit](./media/activity-logs-overview/view-activity-log.png)

Události protokolu aktivit můžete filtrovat podle následujících polí:

* **TimeSpan**: počáteční a koncový čas pro události.
* **Kategorie**: Kategorie události, jak je popsáno v [části kategorie v protokolu aktivit](activity-log-view.md#categories-in-the-activity-log).
* **Předplatné**: jeden nebo víc názvů předplatných Azure.
* **Skupina prostředků**: jedna nebo více skupin prostředků v rámci vybraných předplatných.
* **Prostředek (název)**: – název konkrétního prostředku.
* **Typ prostředku**: typ prostředku, například _Microsoft. COMPUTE/VirtualMachines_.
* **Název operace** – název operace Azure Resource Manager, například _Microsoft. SQL/servery/Write_.
* **Závažnost**: úroveň závažnosti události. Dostupné hodnoty jsou _informativní_, _Upozornění_, _Chyba_, _kritická_.
* **Událost iniciovaná**uživatelem: uživatel, který operaci provedl.
* **Otevřít hledání**: Otevřete vyhledávací pole pro text, ve kterém se tento řetězec vyhledá napříč všemi poli ve všech událostech.

## <a name="categories-in-the-activity-log"></a>Kategorie v protokolu aktivit
Každá událost v protokolu aktivit má konkrétní kategorii, která je popsána v následující tabulce. Úplné podrobnosti o schémat těchto kategorií najdete v tématu [schéma událostí protokolu aktivit Azure](activity-log-schema.md). 

| Kategorie | Popis |
|:---|:---|
| Správa | Obsahuje záznam všech operací vytvoření, aktualizace, odstranění a akcí provedených prostřednictvím Správce prostředků. Mezi příklady událostí správy patří _vytvořit virtuální počítač_ a _Odstranit skupinu zabezpečení sítě_.<br><br>Každá akce prováděná uživatelem nebo aplikací pomocí Správce prostředků je modelována jako operace pro konkrétní typ prostředku. Pokud je typ operace _zápis_, _odstranění_nebo _Akce_, záznamy o zahájení i úspěchu nebo selhání této operace se zaznamenávají do administrativní kategorie. Události správy také zahrnují všechny změny řízení přístupu na základě role v rámci předplatného. |
| Service Health | Obsahuje záznam o všech incidentech služby Health Service, ke kterým došlo v Azure. Příkladem události Service Health _SQL Azure v východní USA dochází k výpadkům_. <br><br>Service Health události přicházejí v šesti variantách _: vyžaduje se akce_, _pomocná obnovení_, _incident_, _Údržba_, _informace_nebo _zabezpečení_. Tyto události se vytvoří jenom v případě, že máte prostředek v předplatném, který by to ovlivnila událost.
| Resource Health | Obsahuje záznam o všech událostech stavu prostředku, ke kterým došlo u vašich prostředků Azure. Příkladem události Resource Health je _stav virtuálního počítače, který není k dispozici_.<br><br>Události Resource Health mohou představovat jeden ze čtyř stavů: _dostupné_, _nedostupné_, _degradované_a _neznámé_. Resource Health události se navíc dají zařadit do kategorií jako _iniciované platformou_ nebo _uživatelem iniciované_. |
| Výstrahy | Obsahuje záznam o aktivaci pro Azure Alerts. Příkladem události výstrahy je, že _procesor% v myVM byl za posledních 5 minut vyšší než 80_.|
| Automatické škálování | Obsahuje záznam všech událostí souvisejících s provozem modulu automatického škálování na základě všech nastavení automatického škálování, které jste definovali v předplatném. Příkladem události automatického škálování se _nepovedlo provést akci automatického škálování horizontálního_škálování. |
| Doporučení | Obsahuje události doporučení z Azure Advisor. |
| Zabezpečení | Obsahuje záznam všech výstrah vygenerovaných nástrojem Azure Security Center. Příkladem události zabezpečení je _podezřelý soubor s dvojitou příponou_. |
| Zásada | Obsahuje záznamy všech operací akce účinku prováděných Azure Policy. Příklady událostí zásad zahrnují _audit_ a _Deny_. Každá akce prováděná zásadami je modelovaná jako operace na prostředku. |

## <a name="view-change-history"></a>Zobrazit historii změn

Při kontrole protokolu aktivit vám může pomáhat zjistit, k jakým změnám došlo během této události. Tyto informace můžete zobrazit v **historii změn**. Vyberte událost z protokolu aktivit, na kterou chcete hledat hlouběji. Vyberte kartu **historie změn (Preview)** a zobrazte tak související změny s touto událostí.

![Seznam historie změn pro událost](media/activity-logs-overview/change-history-event.png)

Pokud se s událostí nacházejí nějaké přidružené změny, zobrazí se seznam změn, které můžete vybrat. Tím otevřete stránku **historie změn (Preview)** . Na této stránce se zobrazí změny prostředku. Jak vidíte v následujícím příkladu, je možné, že se zobrazí nejen to, že virtuální počítač změnil velikosti, ale předchozí velikost virtuálního počítače byla předtím, než se změna a změnila na.

![Stránka historie změn zobrazující rozdíly](media/activity-logs-overview/change-history-event-details.png)

Další informace o historii změn najdete v tématu [získání změn prostředků](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Pomocí rutiny [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) načtěte protokol aktivit z PowerShellu. Níže jsou uvedeny některé běžné příklady.

> [!NOTE]
> `Get-AzLog`poskytuje jenom 15 dní historie. Použijte parametr **-MaxEvents** k dotazování posledních N událostí mimo 15 dní. Pro přístup k událostem starším než 15 dní použijte REST API nebo SDK. Pokud neuvedete **čas_spuštění**, výchozí hodnota je **čas_ukončení** minus jedna hodina. Pokud nezahrnete do pole **čas_ukončení**, výchozí hodnota je aktuální čas. Všechny časy jsou v UTC.


Získat položky protokolu vytvořené po určitém datu a čase:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Získat položky protokolu mezi rozsahem data a času:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získat položky protokolu z konkrétní skupiny prostředků:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Získat položky protokolu od určitého poskytovatele prostředků mezi rozsahem data a času:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Získat položky protokolu pro určitého volajícího:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Získat poslední 1000 události:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
Pomocí [AZ monitor Activity-log](cli-samples.md#view-activity-log-for-a-subscription) načtěte protokol aktivit z CLI. Níže jsou uvedeny některé běžné příklady.


Zobrazit všechny dostupné možnosti.

```azurecli
az monitor activity-log list -h
```

Získat položky protokolu z konkrétní skupiny prostředků:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Získat položky protokolu pro určitého volajícího:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Získání protokolů volajícím na typ prostředku v rámci rozsahu dat:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST API
K načtení protokolu aktivit z klienta REST použijte [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) . Níže jsou uvedeny některé běžné příklady.

Získání protokolů aktivit pomocí filtru:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Získejte protokoly aktivit pomocí filtru a vyberte:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Získání protokolů aktivit pomocí výběru:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Získat protokoly aktivit bez filtru nebo vybrat:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Další kroky

* [Přečtěte si přehled protokolů platforem](platform-logs-overview.md)
* [Vytvoření nastavení diagnostiky pro odesílání protokolů aktivit do jiných cílů](diagnostic-settings.md)
