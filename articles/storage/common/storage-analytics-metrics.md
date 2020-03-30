---
title: Metriky analýzy azure úložiště (classic)
description: Přečtěte si, jak používat metriky ve službě Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 897ae1fa474de8726ed0caa1def162a00e142dbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268402"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metriky analýzy azure úložiště (classic)

Storage Analytics můžete ukládat metriky, které zahrnují agregované statistiky transakcí a údaje o kapacitě o požadavcích na službu úložiště. Transakce jsou vykazovány na úrovni operace rozhraní API i na úrovni služby úložiště a kapacita je hlášena na úrovni služby úložiště. Data metrik lze použít k analýze využití služby úložiště, diagnostikovat problémy s požadavky na službu úložiště a zlepšit výkon aplikací, které používají službu.  

 Metriky Storage Analytics jsou ve výchozím nastavení povolené pro nové účty úložiště. Metriky můžete nakonfigurovat na [webu Azure Portal](https://portal.azure.com/); Podrobnosti najdete [v tématu Sledování účtu úložiště na webu Azure Portal](/azure/storage/storage-monitor-storage-account). Analýzu úložiště můžete také povolit programově prostřednictvím rozhraní REST API nebo klientské knihovny. Pomocí operací Nastavit vlastnosti služby povolte analýzu úložiště pro každou službu.  

> [!NOTE]
> Metriky Storage Analytics jsou k dispozici pro služby Blob, Fronta, Table a File Services.
> Metriky Storage Analytics jsou teď klasické metriky. Společnost Microsoft doporučuje používat [metriky úložiště v Azure Monitoru](storage-metrics-in-azure-monitor.md) místo metrik storage analytics.

## <a name="transaction-metrics"></a>Transakční metriky  
 Robustní sada dat se zaznamenává v hodinových nebo minutových intervalech pro každou službu úložiště a požadovanou operaci rozhraní API, včetně příchozího přenosu dat, dostupnosti, chyb a procent požadavků zařazených do kategorií. Úplný seznam podrobností o transakci najdete v tématu [schéma tabulky metrik analýzy úložiště.](/rest/api/storageservices/storage-analytics-metrics-table-schema)  

 Transakční data se zaznamenávají na dvou úrovních – úroveň služby a úroveň operace rozhraní API. Na úrovni služby jsou statistiky shrnující všechny požadované operace rozhraní API zapisovány do entity tabulky každou hodinu, i když nebyly provedeny žádné požadavky na službu. Na úrovni operace rozhraní API jsou statistiky zapsány do entity pouze v případě, že operace byla požadována během této hodiny.  

 Například pokud provedete operaci **GetBlob** ve službě Blob, metriky Storage Analytics protokolí požadavek a zahrne jej do agregovaných dat pro službu blob i operace **GetBlob.** Pokud však během hodiny není požadována žádná operace **GetBlob,** entita nebude zapsána do *$MetricsTransactionsBlob* pro tuto operaci.  

 Metriky transakcí se zaznamenávají pro požadavky uživatelů i požadavky provedené samotnou službou Storage Analytics. Například jsou zaznamenány požadavky služby Storage Analytics na zápis protokolů a entit tabulek.

## <a name="capacity-metrics"></a>Metriky kapacity  

> [!NOTE]
>  Metriky kapacity jsou v současné době k dispozici jenom pro službu objektů Blob.

 Data kapacity se zaznamenávají denně pro službu blob účtu úložiště a jsou zapsány dvě entity tabulky. Jedna entita poskytuje statistiky pro uživatelská `$logs` data a druhá poskytuje statistiky o kontejneru objektů blob používaném službou Storage Analytics. Tabulka *$MetricsCapacityBlob* obsahuje následující statistiky:  

- **Kapacita**: Velikost úložiště používané službou Blob účtu úložiště v bajtech.  
- **ContainerCount**: Počet kontejnerů objektů blob ve službě objektu blob účtu úložiště.  
- **ObjectCount**: Počet potvrzených a nepotvrzených objektů BLOB bloku nebo stránky ve službě objektu blob účtu úložiště.  

  Další informace o metrikách kapacity najdete v [tématu Schéma tabulky metrik metrik analýzy úložiště](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Jak jsou metriky uloženy  

 Všechna data metrik pro každou službu úložiště jsou uložena ve třech tabulkách vyhrazených pro tuto službu: jedna tabulka pro informace o transakcích, jedna tabulka pro informace o minutových transakcích a další tabulka pro informace o kapacitě. Informace o transakcích a minutových transakcích se skládají z dat požadavku a odpovědi a informace o kapacitě se skládají z dat o využití úložiště. Hodinové metriky, minutové metriky a kapacita pro službu blob účtu úložiště jsou přístupné v tabulkách, které jsou pojmenované, jak je popsáno v následující tabulce.  

|Úroveň metrik|Názvy tabulek|Podporováno pro verze|  
|-------------------|-----------------|----------------------------|  
|Hodinové metriky, primární umístění|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|Pouze verze před 2013-08-15. I když jsou tyto názvy stále podporovány, doporučujeme přepnout na použití níže uvedených tabulek.|  
|Hodinové metriky, primární umístění|- $MetricsHourPrimaryTransactionsBlob<br />- $MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|Všechny verze. Podpora metrik služby Souborů je k dispozici pouze ve verzi 2015-04-05 a novější.|  
|Minutové metriky, primární umístění|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />- $MetricsMinutePrimaryTransactionsFile|Všechny verze. Podpora metrik služby Souborů je k dispozici pouze ve verzi 2015-04-05 a novější.|  
|Hodinové metriky, sekundární umístění|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />- $MetricsHourSecondaryTransactionsQueue|Všechny verze. Geograficky redundantní replikace pro čtení musí být povolena.|  
|Minutové metriky, sekundární umístění|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|Všechny verze. Geograficky redundantní replikace pro čtení musí být povolena.|  
|Kapacita (pouze služba objektů blob)|$MetricsCapacityBlob|Všechny verze.|  

 Tyto tabulky se automaticky vytvoří, když je služba Storage Analytics povolena pro koncový bod služby úložiště. Jsou přístupné prostřednictvím oboru názvů účtu úložiště, `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`například: . Tabulky metrik se nezobrazují v operaci výpisu a musí být přístupné přímo prostřednictvím názvu tabulky.  

## <a name="enable-metrics-using-the-azure-portal"></a>Povolení metrik pomocí portálu Azure
Podle následujících kroků povolte metriky na [webu Azure Portal:](https://portal.azure.com)

1. Přejděte na svůj účet úložiště.
1. V podokně **Nabídka** vyberte **Nastavení diagnostiky (klasické).**
1. Ujistěte **se,** že je stav nastaven **na zapnuto**.
1. Vyberte metriky pro služby, které chcete sledovat.
1. Zadejte zásady uchovávání informací, které označují, jak dlouho chcete uchovávat metriky a data protokolu.
1. Vyberte **Uložit**.

[Portál Azure](https://portal.azure.com) aktuálně neumožňuje konfigurovat metriky minut ve vašem účtu úložiště. je nutné povolit metriky minut pomocí prostředí PowerShell nebo programově.

## <a name="enable-storage-metrics-using-powershell"></a>Povolení metrik úložiště pomocí PowerShellu  
Pomocí prostředí PowerShell v místním počítači můžete nakonfigurovat metriky úložiště ve vašem účtu úložiště pomocí rutiny Azure PowerShell **Get-AzStorageServiceMetricsProperty** k načtení aktuálního nastavení a rutina **Set-AzStorageServiceMetricsProperty** pro změnu aktuálního nastavení.  

Rutiny, které řídí metriky úložiště, používají následující parametry:  

* **ServiceType**, možné hodnoty jsou **objekt Blob**, **Fronta**, **Tabulka**a **Soubor**.
* **MetricsType**, možné hodnoty jsou **hodina** a **minuta**.  
* **MetricsLevel**, možné hodnoty jsou:
* **Žádné**: Vypne monitorování.
* **Služba**: Shromažďuje metriky, jako je příchozí přenos dat/odchozí přenos dat, dostupnost, latence a procenta úspěšnosti, které jsou agregovány pro objekt blob, fronty, tabulky a souborové služby.
* **ServiceAndApi**: Kromě metrikslužby shromažďuje stejnou sadu metrik pro každou operaci úložiště v rozhraní API služby Azure Storage.

Například následující příkaz zapne minutové metriky pro službu objektů blob ve vašem účtu úložiště s dobou uchovávání nastavenou na pět dní: 

> [!NOTE]
> Tento příkaz předpokládá, že jste se přihlásili `Connect-AzAccount` k předplatnému Azure pomocí příkazu.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* `<resource-group-name>` Nahraďte zástupnou hodnotu názvem skupiny prostředků.
        
* `<storage-account-name>` Nahraďte zástupnou hodnotu názvem svého účtu úložiště.



Následující příkaz načte aktuální hodinovou úroveň metrik a dny uchovávání informací pro službu objektů blob ve výchozím účtu úložiště:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Informace o tom, jak nakonfigurovat rutiny Azure PowerShellu tak, aby fungovaly s vaším předplatným Azure, a jak vybrat výchozí účet úložiště, který se má použít, najdete v [tématu: Jak nainstalovat a nakonfigurovat Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Programové povolení metrik úložiště  
Kromě použití portálu Azure Portal nebo rutin Azure PowerShell k řízení metrik úložiště můžete také použít jedno z api úložiště Azure. Pokud například používáte jazyk .NET, můžete použít klientskou knihovnu úložiště.  

Třídy **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**a **CloudFileClient** mají všechny metody, jako jsou **SetServiceProperties** a **SetServicePropertiesAsync,** které berou objekt **ServiceProperties** jako parametr. Objekt **ServiceProperties** můžete použít ke konfiguraci metrik úložiště. Například následující fragment c# ukazuje, jak změnit úroveň metrika a retenční dny pro metriky hodinové fronty:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Další informace o použití jazyka .NET ke konfiguraci metrik úložiště naleznete v [tématu Knihovna klienta úložiště pro rozhraní .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Obecné informace o konfiguraci metrik úložiště pomocí rozhraní REST API naleznete v tématu [Povolení a konfigurace analýzy úložiště](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Zobrazení metrik úložiště  
Po konfiguraci metrik služby Storage Analytics pro monitorování vašeho účtu úložiště zaznamená Služba Storage Analytics metriky v sadě známých tabulek ve vašem účtu úložiště. Grafy můžete nakonfigurovat tak, aby zobrazovaly hodinové metriky na [webu Azure Portal](https://portal.azure.com):

1. Přejděte na svůj účet úložiště na [webu Azure Portal](https://portal.azure.com).
1. V okně **Menu** pro službu, jejíž metriky chcete zobrazit, vyberte **metriky (klasické).**
1. Klikněte na graf, který chcete konfigurovat.
1. V okně **Upravit graf** vyberte **časový rozsah**, typ **grafu**a metriky, které chcete v grafu zobrazit.

V **části Monitorování (klasické)** v okně nabídky účtu úložiště na webu Azure Portal můžete nakonfigurovat [pravidla výstrah](#metrics-alerts), jako je odesílání e-mailových upozornění, která vás upozorní, když konkrétní metrika dosáhne určité hodnoty.

Pokud chcete stáhnout metriky pro dlouhodobé úložiště nebo je analyzovat místně, musíte použít nástroj nebo napsat nějaký kód pro čtení tabulek. Je nutné stáhnout minutové metriky pro analýzu. Tabulky se nezobrazí, pokud zobrazíte všechny tabulky v účtu úložiště, ale můžete k nim přistupovat přímo podle názvu. Mnoho nástrojů pro procházení úložiště si tyto tabulky uvědomuje a umožňuje jejich přímé zobrazení (seznam dostupných nástrojů najdete v tématu [Nástroje klienta úložiště Azure).](/azure/storage/storage-explorers)

||||  
|-|-|-|  
|**Metriky**|**Názvy tabulek**|**Poznámky**|  
|Hodinové metriky|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Ve verzích před 2013-08-15 byly tyto tabulky známé jako:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metriky pro službu Soubor jsou k dispozici počínaje verzí 2015-04-05.|  
|Minutové metriky|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Lze povolit pouze pomocí prostředí PowerShell nebo programově.<br /><br /> Metriky pro službu Soubor jsou k dispozici počínaje verzí 2015-04-05.|  
|Kapacita|$MetricsCapacityBlob|Pouze služba blob.|  

Úplné podrobnosti o schématech těchto tabulek najdete na jdete na jdete na jdete na jdete na jdete na jdete v tabulce [metriky úložiště.](/rest/api/storageservices/storage-analytics-metrics-table-schema) Ukázkové řádky níže zobrazují pouze podmnožinu sloupců, které jsou k dispozici, ale ilustrují některé důležité funkce způsobu ukládání metrik úložiště:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Časové razítko**|**TotalRequests**|**TotalBillablePožadavky**|**TotalIngress**|**TotalEgress**|**Dostupnost**|**AverageE2ELatency**|**AverageServerLatency**|**ProcentoÚspěch**|  
|20140522T1100|uživatel; Všechny|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|uživatel; QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|uživatel; QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|uživatel; UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

V tomto příkladu data metriky minut, klíč oddílu používá čas na minutu rozlišení. Klíč řádku identifikuje typ informací, které jsou uloženy v řádku a skládá se ze dvou částí informací, typu přístupu a typu požadavku:  

-   Typ přístupu je buď **uživatel** nebo **systém**, kde **uživatel** odkazuje na všechny požadavky uživatelů na službu úložiště a **systém** odkazuje na požadavky provedené službou Storage Analytics.  

-   Typ požadavku je buď **všechny,** v takovém případě se jedná o souhrnný řádek, nebo identifikuje konkrétní rozhraní API, jako je **QueryEntity** nebo **UpdateEntity**.  

Ukázková data výše zobrazuje všechny záznamy pro jednu minutu (počínaje 11:00), takže počet **QueryEntities** požadavky plus počet **QueryEntity** požadavky plus počet **UpdateEntity** požadavky přidat až sedm, což je součet zobrazí na **user:All** řádek. Podobně můžete odvodit průměrnou latenci od konce do konce 104.4286 na **uživatele:Všechny** řádky výpočtem ((143.8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Upozornění na metriky
Měli byste zvážit nastavení výstrah na [webu Azure Portal,](https://portal.azure.com) abyste byli automaticky upozorněni na důležité změny v chování služeb úložiště. Pokud ke stažení těchto dat metrik ve formátu s oddělovači používáte nástroj průzkumníka úložiště, můžete data analyzovat pomocí aplikace Microsoft Excel. Seznam dostupných nástrojů průzkumníka úložiště najdete v tématu [Nástroje klienta úložiště Azure.](/azure/storage/storage-explorers) Výstrahy můžete nakonfigurovat v okně **Výstraha (klasické),** přístupné v části **Monitorování (klasické)** v okně nabídky účtu úložiště.

> [!IMPORTANT]
> Může být zpoždění mezi událostí úložiště a při zaznamenání odpovídajících hodinových nebo minutových metrik. V případě minutových metrik může být zapsáno několik minut dat najednou. To může vést k transakce z předchozích minut agregovány do transakce pro aktuální minutu. V takovém případě služba výstrahy nemusí mít všechna dostupná data metrik pro nakonfigurovaný interval výstrah, což může vést k neočekávaně spuštění výstrah.
>

## <a name="accessing-metrics-data-programmatically"></a>Programový přístup k datům metrik  
Následující výpis ukazuje ukázkový kód Jazyka C#, který přistupuje k minutovým metrikám pro rozsah minut a zobrazuje výsledky v okně konzoly. Ukázka kódu používá klientskou knihovnu Azure Storage Library verze 4.x nebo novější, která zahrnuje třídu **CloudAnalyticsClient,** která zjednodušuje přístup k tabulkám metrik v úložišti.  

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

## <a name="billing-on-storage-metrics"></a>Fakturace metrik úložiště  
Požadavky na zápis k vytvoření entit tabulky pro metriky se účtují standardními sazbami platnými pro všechny operace azure storage.  

Číst a odstraňovat požadavky na data metrik y klientem jsou také fakturovatelné za standardní sazby. Pokud jste nakonfigurovali zásady uchovávání dat, nebude vám účtována, když Azure Storage odstraní stará data metriky. Pokud však odstraníte analytická data, bude se z vašeho účtu účtovat poplatek za operace odstranění.  

Kapacita používaná tabulkami metrik je také fakturovatelná. K odhadu kapacity použité pro ukládání dat metrik můžete použít následující:  

-   Pokud každou hodinu služba využívá každé rozhraní API v každé službě, pak přibližně 148 KB dat je uložena každou hodinu v tabulkách transakcí metriky, pokud jste povolili souhrn na úrovni služby a rozhraní API.  
-   Pokud během každé hodiny služba využívá každé rozhraní API ve službě, pak přibližně 12 kB dat je uloženkaždou hodinu v tabulkách transakcí metriky, pokud jste povolili pouze souhrn na úrovni služby.  
-   Tabulka kapacity pro objekty BLOB má každý den přidány dva řádky za předpokladu, že jste se přihlásili k protokolům. To znamená, že každý den se velikost této tabulky zvětší až o přibližně 300 bajtů.

## <a name="next-steps"></a>Další kroky
* [Jak sledovat účet úložiště](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schéma tabulky metrik analýzy úložiště](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Služby pro protokolované operace a stavové zprávy analýzy úložiště](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Protokolování Analýzy úložiště](storage-analytics-logging.md)
