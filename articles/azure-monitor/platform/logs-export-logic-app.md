---
title: Archivace dat z pracovního prostoru Log Analytics do Azure Storage pomocí aplikace logiky
description: Popisuje metodu použití Azure Logic Apps k dotazování dat z pracovního prostoru Log Analytics a odeslání do Azure Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 04f1eb0d9db00a2be1a4619cafe38aa18145fc78
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185993"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Archivace dat z pracovního prostoru Log Analytics do Azure Storage pomocí aplikace logiky
Tento článek popisuje způsob, jak použít [Azure Logic Apps](../../logic-apps/index.yml) k dotazování dat z pracovního prostoru Log Analytics v Azure monitor a odeslání do Azure Storage. Tento postup použijte v případě, že potřebujete exportovat data protokolu Azure Monitor pro scénáře auditování a dodržování předpisů nebo pokud chcete, aby mohla jiná služba načíst tato data.  

## <a name="other-export-methods"></a>Další metody exportu
Metoda popsaná v tomto článku popisuje plánovaný export z dotazu protokolu pomocí aplikace logiky. K dalším možnostem exportu dat pro konkrétní scénáře patří následující:

- Pokud chcete exportovat všechna data z vašeho pracovního prostoru Log Analytics do účtu služby Azure Storage nebo centra událostí, použijte funkci exportu dat Log Analytics pracovního prostoru Azure Monitor protokolů. Viz [Log Analytics exportu dat v pracovním prostoru v Azure monitor (Preview)](logs-data-export.md)
- Jednorázové export pomocí aplikace logiky [Logic Apps a automatické automatizaci najdete v tématu konektory protokolů Azure monitor](logicapp-flow-connector.md).
- Jednou při exportu do místního počítače pomocí skriptu PowerShellu. Viz [Invoke-AzOperationalInsightsQueryExport]] ( https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) .

## <a name="overview"></a>Přehled
Tento postup používá [konektor protokolů Azure monitor](/connectors/azuremonitorlogs/) , který umožňuje spustit dotaz protokolu z aplikace logiky a použít jeho výstup v jiných akcích v pracovním postupu. Pomocí [konektoru služby azure BLOB Storage](/connectors/azureblob/) se v tomto postupu pošle výstup dotazu do služby Azure Storage. Další akce jsou popsány v následujících částech.

![Přehled aplikace logiky](media/logs-export-logicapp/logic-app-overview.png)

Při exportu dat z Log Analytics pracovního prostoru byste měli filtrovat a agregovat data protokolu a optimalizovat dotaz tak, aby se omezilo množství dat zpracovaných pracovním postupem logiky vaší aplikace logiky na požadovaná data. Například pokud potřebujete Archivovat události přihlášení, můžete vyfiltrovat požadované události a projektovat pouze povinná pole s následujícím dotazem: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

Při exportu dat podle plánu použijte v dotazu funkci ingestion_time (), abyste se ujistili, že nebudete mít pozdě přijíždějící data. Pokud jsou data zpožděna kvůli problémům se sítí nebo platformou, bude při použití času příjmu zajištěno, že budou zahrnuty do dalšího spuštění aplikace logiky. Příklad najdete v tématu o [akci přidání protokolů Azure monitor](#add-azure-monitor-logs-action) .

## <a name="prerequisites"></a>Předpoklady
Níže jsou uvedené požadavky, které je nutné před dokončením tohoto postupu dokončit.

- Log Analytics pracovní prostor. Uživatel, který vytváří aplikaci logiky, musí mít k pracovnímu prostoru oprávnění alespoň pro čtení. 
- Účet služby Azure Storage. Účet úložiště nemusí být ve stejném předplatném jako váš pracovní prostor Log Analytics. Uživatel, který vytváří aplikaci logiky, musí mít oprávnění k zápisu do účtu úložiště. 


## <a name="connector-limits"></a>Omezení konektoru
Log Analytics pracovní prostor a dotazy protokolů v Azure Monitor jsou víceklientské služby, které zahrnují omezení, která chrání a izolují zákazníky a udržují kvalitu služby. Při dotazování na velké množství dat byste měli zvážit následující omezení, což může mít vliv na to, jak nakonfigurujete opakování aplikace logiky a dotaz na protokol:

- Dotazy protokolu nemohou vracet více než 500 000 řádků.
- Dotazy protokolu nemohou vracet více než 64 000 000 bajtů.
- Dotazy protokolu nemohou ve výchozím nastavení běžet déle než 10 minut. 
- Konektor Log Analytics je omezený na 100 volání za minutu.


## <a name="create-container-in-the-storage-account"></a>Vytvoření kontejneru v účtu úložiště
Pomocí postupu v části [vytvoření kontejneru](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) přidejte kontejner do svého účtu úložiště, do kterého se budou ukládat exportovaná data. Název, který se používá pro kontejner v tomto článku, je **loganalytics-data**, ale můžete použít libovolný název.


## <a name="create-logic-app"></a>Vytvoření aplikace logiky

V Azure Portal přejděte na **Logic Apps** a klikněte na **Přidat**. Vyberte **předplatné**, **skupinu prostředků** a **oblast** , do které chcete uložit novou aplikaci logiky, a pak jí udělte jedinečný název. Můžete zapnout **Log Analytics** nastavení a shromažďovat informace o běhových datech a událostech, jak je popsáno v tématu [nastavení protokolů Azure monitor a shromažďování diagnostických dat pro Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Toto nastavení není vyžadováno pro použití konektoru Azure Monitorch protokolů.

![Vytvoření aplikace logiky](media/logs-export-logicapp/create-logic-app.png)


Klikněte na tlačítko **zkontrolovat + vytvořit** a pak **vytvořit**. Po dokončení nasazení klikněte na **Přejít k prostředku** a otevřete **Návrháře Logic Apps**.

## <a name="create-a-trigger-for-the-logic-app"></a>Vytvoření triggeru pro aplikaci logiky
V části **začít se společným triggerem** vyberte **opakování**. Tím se vytvoří aplikace logiky, která se automaticky spustí v pravidelných intervalech. V poli **frekvence** akce vyberte **hodiny** a v poli **interval** zadejte **1** , pokud chcete pracovní postup spustit jednou za den.

![Akce opakování](media/logs-export-logicapp/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Akce přidání protokolů Azure Monitor
Kliknutím na **+ Nový krok** přidáte akci, která se spustí po akci opakování. V části **Zvolte akci** zadejte **Azure monitor** a pak vyberte **Azure monitor protokoly**.

![Akce protokolů Azure Monitor](media/logs-export-logicapp/select-azure-monitor-connector.png)

Klikněte na **Azure Log Analytics – spusťte dotaz a výsledky seznamu**.

![Snímek obrazovky s novou akcí, která se přidává do kroku v návrháři aplikace logiky Protokoly Azure Monitor jsou v části zvolit akci zvýrazněné.](media/logs-export-logicapp/select-query-action-list.png)

Zobrazí se výzva k výběru tenanta a udělení přístupu k Log Analyticsmu pracovnímu prostoru pomocí účtu, který bude pracovní postup používat ke spuštění dotazu.


## <a name="add-azure-monitor-logs-action"></a>Akce přidání protokolů Azure Monitor
Akce protokoly Azure Monitor umožňuje zadat dotaz, který se má spustit. Dotaz protokolu použitý v tomto příkladu je optimalizován na hodinové opakování a shromažďuje data ingestovaná pro konkrétní dobu provádění. Například pokud pracovní postup běží v 4:35, bude časový rozsah 4:00 až 5:00. Pokud změníte aplikaci logiky tak, aby běžela s jinou frekvencí, budete potřebovat také dotaz změnit. Například pokud nastavíte, aby se opakování spouštělo každý den, nastavili jste Čas_spuštění v dotazu na startofday (make_datetime (rok, měsíc, den, 0, 0)). 

Vyberte **předplatné** a **skupinu prostředků** pro pracovní prostor Log Analytics. Jako **typ prostředku** vyberte *Log Analytics pracovní prostor* a potom v části **název prostředku** vyberte název pracovního prostoru.

Do okna **dotazu** přidejte následující dotaz protokolu.  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

**Časový rozsah** určuje záznamy, které budou zahrnuty v dotazu na základě sloupce **TimeGenerated** . Tato hodnota by měla být nastavena na hodnotu větší nebo vyšší než časový rozsah vybraný v dotazu. Vzhledem k tomu, že tento dotaz nepoužívá sloupec **TimeGenerated** , **nastavení v možnosti dotaz** není k dispozici. Další podrobnosti o časovém rozsahu najdete v tématu [obor dotazů](../log-query/scope.md) . 

Vyberte **Poslední 4 hodiny** pro **časový rozsah**. Tím se zajistí, aby se do výsledků zahrnuly všechny záznamy s časem příjmu větším než **TimeGenerated** .
   
![Snímek obrazovky s nastavením pro novou Azure Monitor protokoluje akci s názvem spustit dotaz a vizualizovat výsledky.](media/logs-export-logicapp/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>Přidat aktivitu analýzy JSON (nepovinné)
Výstup akce **Spustit dotaz a výsledky seznamu** je ve formátu JSON. Tato data můžete analyzovat a manipulovat v rámci přípravy pro akci **psaní** . 

Můžete zadat schéma JSON, které popisuje datovou část, kterou očekáváte pro příjem. Návrhář analyzuje obsah JSON pomocí tohoto schématu a generuje uživatelsky přívětivé tokeny, které reprezentují vlastnosti v obsahu JSON. Pak můžete tyto vlastnosti snadno odkazovat a používat v rámci pracovního postupu vaší aplikace logiky. 


Klikněte na **+ Nový krok** a potom klikněte na **+ přidat akci**. V části **zvolit akci** zadejte **JSON** a pak vyberte **analyzovat JSON**.

![Vybrat aktivitu analyzovat JSON](media/logs-export-logicapp/select-parse-json.png)

Kliknutím do pole **obsah** zobrazíte seznam hodnot z předchozích aktivit. V akci **Spustit dotaz a výsledky seznamu** vyberte **tělo** . Toto je výstup dotazu protokolu.

[![Vybrat tělo](media/logs-export-logicapp/select-body.png)](media/logs-export-logicapp/select-body.png#lightbox)

5.  **Pro generování schématu klikněte na použít ukázkovou datovou část**. Spusťte dotaz protokolu a zkopírujte výstup, který se použije pro vzorovou datovou část.  Pro ukázkový dotaz zde můžete použít následující výstup:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![Analyzovat datovou část JSON](media/logs-export-logicapp/parse-json-payload.png)

## <a name="add-the-compose-action"></a>Přidat akci psaní
Akce **Vytvoření** převezme analyzovaný výstup JSON a vytvoří objekt, který je třeba uložit do objektu BLOB.

Klikněte na **+ Nový krok** a potom klikněte na **+ přidat akci**. V části **zvolit akci** napište **napsat** a pak vyberte akci **vytvořit** .

![Vybrat akci psaní](media/logs-export-logicapp/select-compose.png)


Kliknutím na pole **vstupy** zobrazíte seznam hodnot z předchozích aktivit. Z akce **analyzovat JSON** vyberte **tělo** . Toto je analyzovaný výstup z dotazu protokolu.

[![Výběr textu pro akci psaní](media/logs-export-logicapp/select-body-compose.png)](media/logs-export-logicapp/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Přidat akci vytvořit objekt BLOB
Akce vytvořit objekt BLOB zapisuje složený kód JSON do úložiště.

Klikněte na **+ Nový krok** a potom klikněte na **+ přidat akci**. V části **zvolit akci** zadejte **objekt BLOB** a potom vyberte akci **vytvořit objekt BLOB** .

![Vyberte vytvořit objekt BLOB](media/logs-export-logicapp/select-create-blob.png)

Do pole **název připojení** zadejte název připojení k vašemu účtu úložiště a potom kliknutím na ikonu složky v poli cesta ke **složce** vyberte kontejner v účtu úložiště. Kliknutím na **název objektu BLOB** zobrazíte seznam hodnot z předchozích aktivit. Klikněte na **výraz** a zadejte výraz, který odpovídá vašemu časovému intervalu. Pro tento dotaz, který se spouští každou hodinu, následující výraz nastaví název objektu blob za předchozí hodinu: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Výraz objektu BLOB](media/logs-export-logicapp/blob-expression.png)](media/logs-export-logicapp/blob-expression.png#lightbox)

Kliknutím na pole **obsah objektu BLOB** zobrazte seznam hodnot z předchozích aktivit a potom v části **psaní** vyberte **výstupy** .


![Vytvořit výraz objektu BLOB](media/logs-export-logicapp/create-blob.png)


## <a name="test-the-logic-app"></a>Otestování aplikace logiky
Otestujte pracovní postup kliknutím na tlačítko **Spustit**. Pokud má pracovní postup chyby, bude uveden v kroku s problémem. Můžete zobrazit spuštění a přejít k jednotlivým krokům, abyste zobrazili vstup a výstup pro prošetření selhání. V případě potřeby [se podívejte na téma řešení potíží a Diagnostika selhání pracovního postupu v Azure Logic Apps](../../logic-apps/logic-apps-diagnosing-failures.md) .

[![Historie spuštění](media/logs-export-logicapp/runs-history.png)](media/logs-export-logicapp/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Zobrazit protokoly v úložišti
V Azure Portal přejděte do nabídky **účty úložiště** a vyberte svůj účet úložiště. Klikněte na dlaždici **objekty blob** a vyberte kontejner, který jste zadali v akci vytvořit objekt BLOB. Vyberte jeden z objektů BLOB a pak **upravte objekt BLOB**.

[![Data objektu BLOB](media/logs-export-logicapp/blob-data.png)](media/logs-export-logicapp/blob-data.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [dotazech protokolu v Azure monitor](../log-query/log-query-overview.md).
- Další informace o [Logic Apps](../../logic-apps/index.yml)
- Přečtěte si další informace o [Power automatizuje](https://flow.microsoft.com).