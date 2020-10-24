---
title: Analýza úložiště Azure metriky (klasické)
description: Naučte se používat Analýza úložiště metriky v Azure Storage. Seznamte se s metrikami transakcí a kapacity, o tom, jak se ukládají metriky, umožňují metriky a další.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: ef38e36ce1d2c7968e3eb7079270626629523334
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518731"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Analýza úložiště Azure metriky (klasické)

Azure Storage používá řešení Analýza úložiště k ukládání metrik, které zahrnují agregovanou statistiku transakcí a data o kapacitě žádostí do služby úložiště. Transakce jsou hlášeny na úrovni operace rozhraní API a na úrovni služby úložiště. Kapacita se oznamuje na úrovni služby úložiště. Data metrik se dají použít k těmto akcím:
- Analýza využití služby úložiště
- Diagnostikujte problémy s požadavky provedenými u služby úložiště.
- Zvyšte výkon aplikací, které používají službu.

 Pro nové účty úložiště jsou ve výchozím nastavení povolené metriky Analýza úložiště. Metriky můžete konfigurovat v [Azure Portal](https://portal.azure.com/). Další informace najdete v tématu [monitorování účtu úložiště v Azure Portal](/azure/storage/storage-monitor-storage-account). Analýza úložiště můžete také povolit programově prostřednictvím REST API nebo klientské knihovny. K povolení Analýza úložiště pro každou službu použijte operace set Service Properties.  

> [!NOTE]
> K dispozici jsou Analýza úložiště metriky pro Azure Blob Storage, Azure Queue Storage, Azure Table Storage a Azure Files.
> Metriky Analýza úložiště jsou teď klasickými metrikami. Místo Analýza úložištěch metrik doporučujeme použít [metriky úložiště v Azure monitor](monitor-storage.md) .

## <a name="transaction-metrics"></a>Transakční metriky  
 Robustní sada dat se zaznamenává v hodinovém nebo minutovém intervalu pro každou službu úložiště a požadovanou operaci rozhraní API, která zahrnuje příchozí a odchozí přenosy, dostupnost, chyby a procentní podíly požadavků na kategorizované. Úplný seznam podrobností o transakcích najdete v tématu [Analýza úložiště schéma tabulky metrik](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Data transakce se zaznamenávají na úrovni služby a na úrovni operace rozhraní API. Na úrovni služby jsou statistiky, které shrnují všechny požadované operace rozhraní API, zapisovány do entity tabulky každou hodinu, a to i v případě, že služba nevytvořila žádné požadavky. Na úrovni operace rozhraní API se Statistika zapisuje jenom na entitu, pokud se operace v této hodině vyžádala.  

 Pokud třeba ve službě BLOB Service provedete operaci **getblob** , analýza úložiště metriky protokolují požadavek a budou ho zahrnovat do agregovaných dat pro službu BLOB Service a operaci **getblob** . Pokud se během hodiny nepožaduje žádná operace **getblob** , pro tuto operaci není zadána entita pro *$MetricsTransactionsBlob* .  

 Pro požadavky uživatelů a požadavky vytvořené Analýza úložiště samotné se zaznamenávají transakční metriky. Například požadavky, které Analýza úložiště k zápisu protokolů a entit tabulek, se zaznamenávají.

## <a name="capacity-metrics"></a>Kapacitní metriky  

> [!NOTE]
>  V současné době jsou metriky kapacity k dispozici pouze pro službu BLOB Service.

 Data o kapacitě se zaznamenávají denně pro službu BLOB účtu úložiště a napíší se dvě entity tabulky. Jedna entita poskytuje statistiku pro uživatelská data a druhá poskytuje statistiku o `$logs` kontejneru objektů blob, který používá analýza úložiště. Tabulka *$MetricsCapacityBlob* obsahuje následující statistiky:  

- **Kapacita**: velikost úložiště využitá službou BLOB účtu úložiště v bajtech.  
- **ContainerCount**: počet kontejnerů objektů BLOB ve službě BLOB účtu úložiště.  
- **ObjectCount**: počet potvrzených a nepotvrzených objektů blob bloku nebo stránky ve službě BLOB účtu úložiště.  

  Další informace o metrikách kapacity najdete v tématu [Analýza úložiště schéma tabulky metrik](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Jak jsou uloženy metriky  

 Všechna data metrik pro každou ze služeb úložiště se ukládají ve třech tabulkách rezervovaných pro danou službu. Jedna tabulka je určena pro informace o transakcích, jedna tabulka je určena pro minutové informace o transakcích a další tabulka slouží k informacím o kapacitě. Informace o transakcích a minutách transakce se skládají z dat požadavků a odpovědí. Informace o kapacitě se skládají z dat využití úložiště. V tabulkách, které jsou popsány v následující tabulce, je k dispozici metrika hodin, minutové metriky a kapacita služby BLOB účtu úložiště.  

|Úroveň metrik|Názvy tabulek|Podporováno pro verze|  
|-------------------|-----------------|----------------------------|  
|Hodinové metriky, primární umístění|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Verze starší než 15. srpna 2013. I když jsou tyto názvy stále podporovány, doporučujeme, abyste se přepnuli na použití tabulek, které následují.|  
|Hodinové metriky, primární umístění|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Všechny verze. Podpora pro metriky souborové služby je k dispozici pouze ve verzi 5. dubna 2015 a novější.|  
|Minutové metriky, primární umístění|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Všechny verze. Podpora pro metriky souborové služby je k dispozici pouze ve verzi 5. dubna 2015 a novější.|  
|Hodinové metriky, sekundární umístění|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Všechny verze. Musí být povolená geograficky redundantní replikace s přístupem pro čtení.|  
|Minutové metriky, sekundární umístění|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Všechny verze. Musí být povolená geograficky redundantní replikace s přístupem pro čtení.|  
|Kapacita (pouze služba BLOB Service)|$MetricsCapacityBlob|Všechny verze.|  

 Tyto tabulky se automaticky vytvoří, když je povolený Analýza úložiště pro koncový bod služby úložiště. Jsou k nim přistupované prostřednictvím oboru názvů účtu úložiště, například `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` . Tabulky metrik se nezobrazují v operaci výpisu a musí být dostupné přímo prostřednictvím názvu tabulky.

## <a name="enable-metrics-by-using-the-azure-portal"></a>Povolit metriky pomocí Azure Portal
Pomocí těchto kroků povolíte metriky v [Azure Portal](https://portal.azure.com):

1. Přejít na účet úložiště.
1. V podokně nabídky vyberte **nastavení diagnostiky (klasické)** .
1. Zajistěte, aby byl **stav** nastaven **na zapnuto**.
1. Vyberte metriky pro služby, které chcete monitorovat.
1. Zadejte zásady uchovávání informací, které určují, jak dlouho se mají uchovávat metriky a data protokolu.
1. Vyberte **Uložit**.

[Azure Portal](https://portal.azure.com) v tuto chvíli neumožňují konfigurovat minuty metrik v účtu úložiště. Minutové metriky je nutné povolit pomocí prostředí PowerShell nebo prostřednictvím kódu programu.

## <a name="enable-storage-metrics-by-using-powershell"></a>Povolení metrik úložiště pomocí prostředí PowerShell  
Pomocí PowerShellu na místním počítači můžete nakonfigurovat metriky úložiště ve vašem účtu úložiště pomocí rutiny Azure PowerShell **Get-AzStorageServiceMetricsProperty** pro načtení aktuálních nastavení. Chcete-li změnit aktuální nastavení, použijte rutinu **set-AzStorageServiceMetricsProperty** .  

Rutiny, které řídí metriky úložiště, používají následující parametry:  

* **ServiceType**: možné hodnoty jsou **BLOB**, **Queue**, **Table**a **File**.
* **MetricsType**: možné hodnoty jsou **Hour** a **minute**.  
* **MetricsLevel**: možné hodnoty:
   * **Žádné**: vypne monitorování.
   * **Služba**: shromažďuje metriky, jako jsou příchozí a odchozí, dostupnost, latence a procento úspěšnosti, které jsou agregované pro objekty blob, front, tabulek a souborové služby.
   * **ServiceAndApi**: Kromě metrik služby shromažďuje stejnou sadu metrik pro každou operaci úložiště v rozhraní API služby Azure Storage.

Například následující příkaz přepne na minuty pro službu BLOB Service ve vašem účtu úložiště s dobou uchování nastavenou na pět dní: 

> [!NOTE]
> Tento příkaz předpokládá, že jste se přihlásili ke svému předplatnému Azure pomocí `Connect-AzAccount` příkazu.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem vaší skupiny prostředků.      
* Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště.



Následující příkaz načte aktuální hodinovou metriku a dny uchování pro službu BLOB Service ve vašem výchozím účtu úložiště:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Informace o tom, jak nakonfigurovat rutiny Azure PowerShell pro práci s předplatným Azure a jak vybrat výchozí účet úložiště, který se má použít, najdete v tématu [instalace a konfigurace Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Povolení metrik úložiště prostřednictvím kódu programu  
Kromě použití Azure Portal nebo rutin Azure PowerShell k řízení metrik úložiště můžete použít také jedno z rozhraní API pro Azure Storage. Pokud například používáte jazyk .NET, můžete použít knihovnu klienta Azure Storage.  

Třídy **CloudBlobClient**, **CloudQueueClient**, **cloudtableclient vám**a **CloudFileClient** mají metody jako **SetServiceProperties** a **SetServicePropertiesAsync** , které přijímají objekt **ServiceProperties** jako parametr. Pomocí objektu **ServiceProperties** můžete nakonfigurovat metriky úložiště. Například následující fragment kódu jazyka C# ukazuje, jak změnit úroveň metrik a dny uchování pro hodinové metriky fronty:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Další informace o tom, jak pomocí jazyka .NET nakonfigurovat metriky úložiště, najdete v tématu [Azure Storage klientských knihoven pro .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Obecné informace o konfiguraci metrik úložiště pomocí REST API najdete v tématu [povolení a konfigurace analýza úložiště](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="view-storage-metrics"></a>Zobrazit metriky úložiště  
Když nakonfigurujete metriky Analýza úložiště, abyste mohli monitorovat svůj účet úložiště, Analýza úložiště zaznamenává metriky v sadě dobře známých tabulek v účtu úložiště. Můžete nakonfigurovat grafy pro zobrazení hodinových metrik v [Azure Portal](https://portal.azure.com):

1. V [Azure Portal](https://portal.azure.com)přejít na svůj účet úložiště.
1. V podokně nabídky pro službu, jejíž metriky chcete zobrazit, vyberte **metriky (Classic)** .
1. Vyberte graf, který chcete konfigurovat.
1. V podokně **Upravit graf** vyberte **časový rozsah**, **typ grafu**a metriky, které chcete zobrazit v grafu.

V části **monitorování (Classic)** v podokně nabídky účtu úložiště v Azure Portal můžete nakonfigurovat [pravidla výstrah](#metrics-alerts). Můžete například odeslat e-mailová upozornění, která vás upozorní, když konkrétní metrika dosáhne určité hodnoty.

Pokud chcete stáhnout metriky pro dlouhodobé ukládání nebo je analyzovat místně, je nutné použít nástroj nebo napsat kód pro čtení tabulek. Je nutné stáhnout minutové metriky pro analýzu. Tabulky se nezobrazí, pokud vypíšete všechny tabulky v účtu úložiště, ale můžete k nim přistupovat přímo podle názvu. Mnoho nástrojů pro procházení úložiště ví o těchto tabulkách a umožňuje vám jejich přímé prohlížení. Seznam dostupných nástrojů najdete v tématu [Azure Storage klientských nástrojů](/azure/storage/storage-explorers).

|Metriky|Názvy tabulek|Poznámky| 
|-|-|-|  
|Hodinové metriky|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Ve verzích starších než 15. srpna 2013 byly tyto tabulky známé jako:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metriky pro souborovou službu jsou k dispozici od verze 5. dubna 2015.|  
|Minutové metriky|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Dá se povolit jenom pomocí PowerShellu nebo prostřednictvím kódu programu.<br /><br /> Metriky pro souborovou službu jsou k dispozici od verze 5. dubna 2015.|  
|Kapacita|$MetricsCapacityBlob|Pouze Blob service.|  

Úplné podrobnosti o schématech pro tyto tabulky najdete v tématu [Analýza úložiště schématu tabulky metrik](/rest/api/storageservices/storage-analytics-metrics-table-schema). Následující ukázkové řádky obsahují pouze podmnožinu dostupných sloupců, ale ilustrují některé důležité funkce, kterými metrika úložiště ukládá tyto metriky:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Dostupnost|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|uživatelský Všem|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|uživatelský QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|uživatelský QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|uživatelský UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

V tomto příkladu data metriky, klíč oddílu používá dobu během minutového řešení. Klíč řádku identifikuje typ informací, které jsou uloženy na řádku. Informace se skládají z typu přístupu a typu požadavku:  

-   Typ přístupu je buď **uživatel** , nebo **systém**, kde **uživatel** odkazuje na všechny požadavky uživatelů na službu úložiště a **systém** odkazuje na požadavky vytvořené analýza úložiště.  
-   Typ žádosti je buď **vše**, v takovém případě se jedná o souhrnný řádek nebo identifikuje konkrétní rozhraní API, jako je například **QueryEntity** nebo **UpdateEntity**.  

Tato ukázková data zobrazí všechny záznamy za jednu minutu (počínaje 11.10:00), takže počet požadavků **QueryEntities** plus počet požadavků **QueryEntity** plus počet žádostí o **UpdateEntity** přidá až 7. Tento součet je zobrazený v řádku **Uživatel: vše** . Podobně můžete odvodit průměrnou koncovou latenci 104,4286 u **uživatele: všechny** řádky vypočítané ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Výstrahy metrik
Zvažte nastavení výstrah v [Azure Portal](https://portal.azure.com) , takže budete automaticky upozorňováni na důležité změny v chování vašich služeb úložiště. Použijete-li nástroj Průzkumník služby Storage ke stažení těchto dat metrik ve formátu s oddělovači, můžete data analyzovat pomocí aplikace Microsoft Excel. Seznam dostupných Průzkumník služby Storage nástrojů najdete v tématu [Azure Storage klientských nástrojů](/azure/storage/storage-explorers). Výstrahy můžete nakonfigurovat v podokně **Výstraha (klasické)** , které je dostupné v části **monitorování (Classic)** v podokně nabídky účtu úložiště.

> [!IMPORTANT]
> Může dojít ke zpoždění mezi událostí úložiště a při nahrávání odpovídajících hodinových dat metriky nebo minut. V případě minutové metriky je možné zapsat několik minut dat najednou. Tento problém může vést k tomu, že se transakce z předchozích minut agreguje do transakce pro aktuální minutu. Pokud k tomuto problému dojde, služba Výstrahy nemusí mít všechna dostupná data metriky pro nakonfigurovaný interval upozornění, což by mohlo vést k neočekávanému spouštění výstrah.
>

## <a name="access-metrics-data-programmatically"></a>Přístup k datům metrik prostřednictvím kódu programu  
Následující výpis zobrazuje ukázkový kód jazyka C#, který přistupuje k minutovým metrikám po dobu celé řady minut a zobrazuje výsledky v okně konzoly. Ukázka kódu používá Azure Storage klientskou knihovnu verze 4. x nebo novější, která obsahuje třídu **CloudAnalyticsClient** , která zjednodušuje přístup k tabulkám metrik v úložišti. 

> [!NOTE]
> Třída **CloudAnalyticsClient** není součástí klientské knihovny Azure Blob Storage V12 pro .NET. Od **31. srpna 2023** analýza úložiště metriky, označované také jako *klasické metriky* , budou vyřazeny. Další informace najdete v [oficiálním oznámení](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Pokud používáte klasické metriky, doporučujeme přejít na metriky v Azure Monitor před tímto datem. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
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

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
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

Žádosti o čtení a odstranění požadavků na metriky klienta jsou také Fakturovatelné se standardními sazbami. Pokud jste nakonfigurovali zásady uchovávání dat, neúčtují se vám Azure Storage odstraní stará data metrik. Pokud odstraníte analytické údaje, účtují se vám za operace odstranění účtu.  

Kapacita využívaná tabulkami metriky je také fakturovatelná. Pomocí následujících informací můžete odhadnout množství kapacity využité k ukládání dat metrik:  

-   Pokud je každé hodiny, kdy služba využívá každé z rozhraní API každé služby, v tabulkách transakcí metriky se každý den ukládá přibližně 148 KB dat, pokud jste povolili souhrn na úrovni služby a rozhraní API.  
-   Pokud v rámci každé hodiny služba využívá každé rozhraní API ve službě, v tabulkách transakcí metriky se v případě, že jste povolili jenom souhrn na úrovni služby, ukládá přibližně 12 KB dat každou hodinu.  
-   V tabulce Capacity pro objekty BLOB se každý den přidaly dva řádky, pro které jste se rozhodli o protokolech. Tento scénář předpokládá, že velikost této tabulky se zvyšuje až o přibližně 300 bajtů.

## <a name="next-steps"></a>Další kroky
* [Monitorování účtu úložiště](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schéma tabulky Analýza úložiště metriky](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Protokolování Analýza úložiště](storage-analytics-logging.md)
