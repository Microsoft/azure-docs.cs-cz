---
title: Metriky Azure Storage Analytics (klasické)
description: Naučte se používat metriky v Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268402"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metriky Azure Storage Analytics (klasické)

Analýza úložiště může ukládat metriky, které zahrnují agregovanou statistiku transakcí a data kapacity týkající se požadavků do služby úložiště. Transakce jsou hlášeny jak na úrovni operace rozhraní API, tak i na úrovni služby úložiště a kapacita se oznamuje na úrovni služby úložiště. Data metriky se dají použít k analýze využití služby úložiště, diagnostice problémů s požadavky provedenými na službu úložiště a ke zlepšení výkonu aplikací, které používají službu.  

 Pro nové účty úložiště jsou ve výchozím nastavení povolené metriky Analýza úložiště. V [Azure Portal](https://portal.azure.com/)můžete nakonfigurovat metriky. Podrobnosti najdete v tématu [monitorování účtu úložiště v Azure Portal](/azure/storage/storage-monitor-storage-account). Analýza úložiště můžete také povolit programově prostřednictvím REST API nebo klientské knihovny. K povolení Analýza úložiště pro každou službu použijte operace set Service Properties.  

> [!NOTE]
> Pro služby blob, Queue, Table a File jsou k dispozici Analýza úložiště metriky.
> Metriky Analýza úložiště jsou teď klasickými metrikami. Microsoft doporučuje použití [metrik úložiště v Azure monitor](storage-metrics-in-azure-monitor.md) místo analýza úložištěch metrik.

## <a name="transaction-metrics"></a>Metriky transakcí  
 Robustní sada dat je zaznamenána v hodinovém nebo minutovém intervalu pro každou službu úložiště a požadovanou operaci rozhraní API, včetně příchozích a odchozích dat, dostupnosti, chyb a procentuálních požadavků na požadavky zařazených do kategorií. Úplný seznam podrobností o transakcích najdete v tématu [schéma tabulky analýza úložiště metrik](/rest/api/storageservices/storage-analytics-metrics-table-schema) .  

 Data transakcí se zaznamenávají na dvou úrovních – na úrovni služby a na úrovni provozu rozhraní API. Na úrovni služby jsou statistiky o všech požadovaných operacích rozhraní API zapisovány do entity tabulky každou hodinu, a to i v případě, že služba nevytvořila žádné požadavky. Na úrovni operace rozhraní API se Statistika zapisuje jenom na entitu, pokud se operace v této hodině vyžádala.  

 Například pokud provedete operaci **getblob** na svém Blob service, analýza úložiště metriky protokolují požadavek a zahrne je do agregovaných dat jak pro BLOB Service, tak i do operace **getblob** . Pokud se ale během hodiny nepožaduje žádná operace **getblob** , entita se pro tuto operaci nezapíše do *$MetricsTransactionsBlob* .  

 Metriky transakcí se zaznamenávají pro žádosti uživatelů a požadavky vytvořené Analýza úložiště sám sebou. Například požadavky, které Analýza úložiště k zápisu protokolů a entit tabulek, se zaznamenávají.

## <a name="capacity-metrics"></a>Metriky kapacity  

> [!NOTE]
>  V současné době jsou metriky kapacity k dispozici pouze pro Blob service.

 Data kapacity se zaznamenávají denně pro Blob service účtu úložiště a napíší se dvě entity tabulky. Jedna entita poskytuje statistiku pro uživatelská data a druhá poskytuje statistiku o `$logs` kontejneru objektů blob, který používá Analýza úložiště. Tabulka *$MetricsCapacityBlob* obsahuje následující statistiky:  

- **Kapacita**: velikost úložiště využitá BLOB Service účtu úložiště v bajtech.  
- **ContainerCount**: počet kontejnerů objektů blob v BLOB Service účtu úložiště.  
- **ObjectCount**: počet potvrzených a nepotvrzených objektů blob bloku nebo stránky v účtu úložiště BLOB Service.  

  Další informace o metrikách kapacity najdete v tématu [Analýza úložiště schéma tabulky metrik](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Jak jsou uloženy metriky  

 Všechna data metrik pro každou ze služeb úložiště se ukládají ve třech tabulkách rezervovaných pro tuto službu: jedna tabulka pro informace o transakcích, jedna tabulka pro informace o minutových transakcích a další tabulka pro informace o kapacitě. Informace o transakcích a minutách transakce se skládají z dat požadavků a odpovědí a informace o kapacitě se skládají z dat využití úložiště. Hodinové metriky, minutové metriky a kapacita Blob service účtu úložiště jsou dostupné v tabulkách, které jsou pojmenovány, jak je popsáno v následující tabulce.  

|Úroveň metrik|Názvy tabulek|Podporováno pro verze|  
|-------------------|-----------------|----------------------------|  
|Hodinové metriky, primární umístění|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Jenom verze starší než 2013-08-15. I když jsou tyto názvy stále podporovány, doporučujeme přepnout na použití níže uvedených tabulek.|  
|Hodinové metriky, primární umístění|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Všechny verze. Podpora pro metriky souborové služby je k dispozici pouze ve verzi 2015-04-05 a novější.|  
|Minutové metriky, primární umístění|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Všechny verze. Podpora pro metriky souborové služby je k dispozici pouze ve verzi 2015-04-05 a novější.|  
|Hodinové metriky, sekundární umístění|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Všechny verze. Musí být povolená geograficky redundantní replikace s přístupem pro čtení.|  
|Minutové metriky, sekundární umístění|-   $MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Všechny verze. Musí být povolená geograficky redundantní replikace s přístupem pro čtení.|  
|Kapacita (jenom Blob service)|$MetricsCapacityBlob|Všechny verze.|  

 Tyto tabulky se automaticky vytvoří, když je povolený Analýza úložiště pro koncový bod služby úložiště. Jsou dostupné přes obor názvů účtu úložiště, například: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Tabulky metrik se nezobrazí v operaci výpisu a je nutné k ní získat přímý odkaz pomocí názvu tabulky.  

## <a name="enable-metrics-using-the-azure-portal"></a>Povolit metriky pomocí Azure Portal
Pomocí těchto kroků povolíte metriky v [Azure Portal](https://portal.azure.com):

1. Přejděte na svůj účet úložiště.
1. V podokně **nabídky** vyberte **nastavení diagnostiky (Classic)** .
1. Zajistěte, aby byl **stav** nastaven **na zapnuto**.
1. Vyberte metriky služeb, které chcete monitorovat.
1. Zadejte zásady uchovávání informací, které určují, jak dlouho se mají uchovávat metriky a data protokolu.
1. Vyberte **Save** (Uložit).

[Azure Portal](https://portal.azure.com) v současné době neumožňuje nakonfigurovat minuty metrik v účtu úložiště. minutové metriky je nutné povolit pomocí prostředí PowerShell nebo prostřednictvím kódu programu.

## <a name="enable-storage-metrics-using-powershell"></a>Povolení metrik úložiště pomocí prostředí PowerShell  
Pomocí PowerShellu na místním počítači můžete nakonfigurovat metriky úložiště ve vašem účtu úložiště pomocí rutiny Azure PowerShell **Get-AzStorageServiceMetricsProperty** pro načtení aktuálních nastavení a rutinou **set-AzStorageServiceMetricsProperty** pro změnu aktuálního nastavení.  

Rutiny, které řídí metriky úložiště, používají následující parametry:  

* **ServiceType**, možná value je **BLOB**, **Queue**, **Table**a **File**.
* **MetricsType**, možné hodnoty jsou **Hour** a **minute**.  
* **MetricsLevel**jsou možné hodnoty:
* **Žádné**: vypne monitorování.
* **Služba**: shromažďuje metriky, jako je příchozí/odchozí, dostupnost, latence a procento úspěšnosti, které jsou agregované pro objekty blob, front, tabulek a souborové služby.
* **ServiceAndApi**: Kromě metrik služby shromažďuje stejnou sadu metrik pro každou operaci úložiště v rozhraní API služby Azure Storage.

Například následující příkaz přepne na minuty pro službu BLOB Service ve vašem účtu úložiště s dobou uchování nastavenou na pět dní: 

> [!NOTE]
> Tento příkaz předpokládá, že jste se k předplatnému Azure přihlásili pomocí příkazu `Connect-AzAccount`.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Nahraďte hodnotu zástupného symbolu `<resource-group-name>` názvem vaší skupiny prostředků.
        
* Nahraďte hodnotu zástupného symbolu `<storage-account-name>` názvem svého účtu úložiště.



Následující příkaz načte aktuální hodinovou metriku a dny uchování pro službu BLOB Service ve vašem výchozím účtu úložiště:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Informace o tom, jak nakonfigurovat rutiny Azure PowerShell pro práci s předplatným Azure a jak vybrat výchozí účet úložiště, který se má použít, najdete v tématu: [Jak nainstalovat a nakonfigurovat Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Povolení metrik úložiště prostřednictvím kódu programu  
Kromě použití Azure Portal nebo rutin Azure PowerShell k řízení metrik úložiště můžete použít také jedno z rozhraní API pro Azure Storage. Pokud například používáte jazyk .NET, můžete použít knihovnu klienta úložiště.  

Třídy **CloudBlobClient**, **CloudQueueClient**, **cloudtableclient vám**a **CloudFileClient** mají metody jako **SetServiceProperties** a **SetServicePropertiesAsync** , které přijímají objekt **ServiceProperties** jako parametr. Pomocí objektu **ServiceProperties** můžete nakonfigurovat metriky úložiště. Například následující C# fragment kódu ukazuje, jak změnit úroveň metrik a dny uchování pro hodinové metriky fronty:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Další informace o tom, jak pomocí jazyka .NET nakonfigurovat metriky úložiště, najdete v článku [Klientská knihovna pro úložiště pro .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Obecné informace o konfiguraci metrik úložiště pomocí REST API najdete v tématu [povolení a konfigurace analýza úložiště](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Zobrazení metrik úložiště  
Když nakonfigurujete metriky Analýza úložiště, abyste mohli monitorovat svůj účet úložiště, Analýza úložiště zaznamenává metriky v sadě dobře známých tabulek v účtu úložiště. Můžete nakonfigurovat grafy pro zobrazení hodinových metrik v [Azure Portal](https://portal.azure.com):

1. V [Azure Portal](https://portal.azure.com)přejděte na svůj účet úložiště.
1. V okně **nabídky** pro službu, jejíž metriky chcete zobrazit, vyberte **metriky (Classic)** .
1. Klikněte na graf, který chcete konfigurovat.
1. V okně **Upravit graf** vyberte **časový rozsah**, **typ grafu**a metriky, které chcete zobrazit v grafu.

V části **monitorování (Classic)** v okně nabídky účtu úložiště v Azure Portal můžete nakonfigurovat [pravidla upozornění](#metrics-alerts), jako je odesílání e-mailových upozornění, která vás upozorní, když konkrétní metrika dosáhne určité hodnoty.

Pokud chcete stáhnout metriky pro dlouhodobé ukládání nebo je analyzovat místně, je nutné použít nástroj nebo napsat kód pro čtení tabulek. Je nutné stáhnout minutové metriky pro analýzu. Tabulky se nezobrazí, pokud vypíšete všechny tabulky v účtu úložiště, ale můžete k nim přistupovat přímo podle názvu. Mnoho nástrojů pro procházení úložiště ví o těchto tabulkách a umožňuje vám jejich přímé zobrazení (viz téma [Azure Storage klientských nástrojů](/azure/storage/storage-explorers) pro seznam dostupných nástrojů).

||||  
|-|-|-|  
|**Metriky**|**Názvy tabulek**|**Poznámky**|  
|Hodinové metriky|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Ve verzích starších než 2013-08-15 byly tyto tabulky známé jako:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metriky pro souborovou službu jsou k dispozici od verze 2015-04-05.|  
|Minutové metriky|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Dá se povolit jenom pomocí PowerShellu nebo programově.<br /><br /> Metriky pro souborovou službu jsou k dispozici od verze 2015-04-05.|  
|Kapacita|$MetricsCapacityBlob|Pouze Blob service.|  

Všechny podrobnosti o schématech pro tyto tabulky najdete [Analýza úložiště schématu tabulky metrik](/rest/api/storageservices/storage-analytics-metrics-table-schema). Následující ukázkové řádky obsahují pouze podmnožinu dostupných sloupců, ale ilustrují některé důležité funkce, kterými metrika úložiště ukládá tyto metriky:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Časové razítko**|**TotalRequests**|**TotalBillableRequests**|**Totalbillablerequests**|**TotalEgress**|**Dostupnost**|**Hodnotu averagee2elatency**|**Hodnotu averageserverlatency**|**PercentSuccess**|  
|20140522T1100|uživatelský Všem|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|uživatelský QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|uživatelský QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|uživatelský UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

V tomto příkladu data metriky, klíč oddílu používá dobu v minutách. Klíč řádku identifikuje typ informací, které jsou uloženy na řádku a skládá se ze dvou částí informací, typu přístupu a typu požadavku:  

-   Typ přístupu je buď **uživatel** , nebo **systém**, kde **uživatel** odkazuje na všechny požadavky uživatelů na službu úložiště a **systém** odkazuje na požadavky vytvořené analýza úložiště.  

-   Typ žádosti **je buď v** takovém případě se jedná o souhrnný řádek, nebo identifikuje konkrétní rozhraní API, jako je například **QueryEntity** nebo **UpdateEntity**.  

Výše uvedená ukázková data zobrazí všechny záznamy za jednu minutu (počínaje 11.10:00), takže počet požadavků **QueryEntities** plus počet požadavků **QueryEntity** plus počet požadavků **UpdateEntity** přidaných na hodnotu 7, což je celková hodnota zobrazená na **uživateli: všechny** řádky. Podobně můžete odvodit průměrnou koncovou latenci 104,4286 u **uživatele: všechny** řádky vypočítané ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Výstrahy metrik
Měli byste zvážit nastavení výstrah v [Azure Portal](https://portal.azure.com) , takže budete automaticky upozorňováni na důležité změny v chování vašich služeb úložiště. Použijete-li nástroj Průzkumník úložišť ke stažení dat metrik ve formátu s oddělovači, můžete data analyzovat pomocí aplikace Microsoft Excel. Seznam dostupných nástrojů pro Průzkumník úložišť najdete v tématu [Azure Storage klientských nástrojů](/azure/storage/storage-explorers) . Výstrahy můžete nakonfigurovat v okně **Výstraha (klasické)** , které je dostupné v části **monitorování (Classic)** v okně nabídky účtu úložiště.

> [!IMPORTANT]
> Může dojít ke zpoždění mezi událostí úložiště a při nahrávání odpovídajících hodinových dat metriky nebo minut. V případě minutové metriky je možné zapsat několik minut dat najednou. To může vést k tomu, že se transakce z předchozích minut agreguje do transakce pro aktuální minutu. Pokud k tomu dojde, služba Alert nemusí mít všechna dostupná data metriky pro nakonfigurovaný interval upozornění, což může vést k neočekávanému vyvolávání výstrah.
>

## <a name="accessing-metrics-data-programmatically"></a>Programové přistupování k datům metrik  
Následující výpis zobrazuje vzorový C# kód, který přistupuje k minutovým metrikám po dobu celé řady minut a zobrazuje výsledky v okně konzoly. Ukázka kódu používá Azure Storage klientskou knihovnu verze 4. x nebo novější, která obsahuje třídu **CloudAnalyticsClient** , která zjednodušuje přístup k tabulkám metrik v úložišti.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>Fakturace v metrikách úložiště  
Požadavky na zápis pro vytváření entit tabulek pro metriky se účtují podle standardních sazeb platných pro všechny operace Azure Storage.  

Žádosti o čtení a odstranění požadavků na metriky klienta jsou také Fakturovatelné se standardními sazbami. Pokud jste nakonfigurovali zásady uchovávání dat, neúčtují se vám Azure Storage odstraní stará data metrik. Pokud ale data analýzy odstraníte, účtují se za operace odstranění účtu.  

Kapacita využívaná tabulkami metriky je také fakturovatelná. K odhadu kapacity využité pro ukládání dat metrik můžete použít následující:  

-   Pokud každá hodina používá službu pro každé každou službu rozhraní API, pak se v tabulkách transakcí s metrikami, pokud jste povolili souhrn na úrovni služby a rozhraní API, ukládají přibližně 148KB dat každou hodinu.  
-   V rámci každé hodiny služba využívá každé rozhraní API ve službě, a pokud jste povolili jenom souhrn na úrovni služby, jsou každou hodinu ukládána přibližně 12KB dat v tabulkách transakcí metrik.  
-   V tabulce Capacity pro objekty BLOB se každý den přidaly dva řádky, za předpokladu, že jste se rozhodli pro protokoly. To znamená, že každý den se velikost této tabulky zvětšuje až o přibližně 300 bajtů.

## <a name="next-steps"></a>Další kroky
* [Jak monitorovat účet úložiště](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schéma tabulky Analýza úložiště metriky](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Protokolování Analýza úložiště](storage-analytics-logging.md)
