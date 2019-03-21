---
title: Azure Storage analytics metriky (klasické)
description: Další informace o použití metrik ve službě Azure Storage.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 9b4bceba53658cb8ac3c73e75e0d19faf3fe3f0b
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259750"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage analytics metriky (klasické)

Analýza úložiště může ukládat data, včetně transakce agregované statistiky a dat o kapacitě požadavků na službu úložiště. Transakce jsou zaznamenány na úrovni operace rozhraní API i na úrovni služby úložiště, a je kapacita ohlášená na úrovni služby úložiště. Můžete například měřená data slouží k analýze využití služeb úložiště, Diagnostika potíží s požadavky na službu úložiště a zlepšit výkon aplikace, které používají službu.  

 Ve výchozím nastavení pro nové účty úložiště jsou povolené metrikách Storage Analytics. Můžete nakonfigurovat metriky na [webu Azure portal](https://portal.azure.com/); podrobnosti najdete v tématu [monitorování účtu úložiště na webu Azure Portal](/azure/storage/storage-monitor-storage-account). Můžete také povolit Storage Analytics prostřednictvím kódu programu přes rozhraní REST API nebo knihovny klienta. Operace nastavení vlastností služby použijte k povolení analýzy úložiště pro každou službu.  

> [!NOTE]
> Metrikách Storage Analytics jsou k dispozici pro služby objektů Blob, fronty, tabulky a soubor.
> Metrikách Storage Analytics jsou nyní klasické metriky. Společnost Microsoft doporučuje používat [metrik úložiště ve službě Azure Monitor](storage-metrics-in-azure-monitor.md) místo metrik Storage Analytics.

## <a name="transaction-metrics"></a>Metriku transakcí  
 Robustní sadu dat je zaznamenán v intervalech hodinových nebo minutu pro každou službu úložiště a požadovanou operaci rozhraní API, včetně příchozí a odchozí transakce, dostupnosti, chyby a zařazený do kategorie žádost o procenta. Můžete zobrazit úplný seznam všech podrobností transakcí v [tabulkovému schématu metrik Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema) tématu.  

 Transakce údaje jsou zaznamenány na dvou úrovních – úroveň služby a úroveň operace rozhraní API. Na úrovni služby statistiky sumarizace všech požadovaná operace rozhraní API se zapisují do tabulky entitu každou hodinu i v případě, že nebyly zadány žádné žádosti do služby. Na úrovni rozhraní API operace statistiky zapíšou jenom do entity Pokud za tuto hodinu se požadovaná operace.  

 Například pokud provedete **GetBlob** operace ve službě Blob service, metrik Storage Analytics protokolovat žádosti a zahrnout do agregovaná data pro službu Blob i na **GetBlob** operace. Ale pokud ne **GetBlob** operaci je požadováno během dané hodiny, entity nebude zapisovat *$MetricsTransactionsBlob* pro danou operaci.  

 Transakce jsou zaznamenány pro uživatelských požadavků a požadavků provedených Storage Analytics, samotného. Například se zaznamenávají požadavky podle analýzy úložiště pro zápis protokolů a tabulkové entity.

## <a name="capacity-metrics"></a>Kapacitní metriky  

> [!NOTE]
>  V současné době kapacitní metriky jsou k dispozici pouze pro službu Blob service.

 Kapacity dat je denně zaznamenána pro službu Blob service účtu úložiště a dvě tabulky entity, které jsou zapsány. Jedna entita poskytuje statistiky pro uživatelská data a druhý poskytuje statistické údaje o `$logs` kontejner objektů blob Storage Analytics používá. *$MetricsCapacityBlob* tabulka obsahuje následující statistiky:  

- **Kapacita**: Velikost úložiště využitá službou Blob service účtu úložiště, v bajtech.  
- **ContainerCount**: Počet kontejnerů objektů blob ve službě Blob service účtu úložiště.  
- **ObjectCount**: Počet potvrzených a nepotvrzené bloku nebo stránky objekty BLOB ve službě Blob service účtu úložiště.  

  Další informace o metriky kapacity najdete v tématu [tabulkovému schématu metrik Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Jak jsou uloženy metriky  

 Všechna data metriky pro každou ze služeb úložiště se ukládají do tří tabulek vyhrazené pro tuto službu: jednu tabulku pro informace o transakcích, jednu tabulku pro minuty transakce informace a další tabulku pro informace o kapacitě. Informace o transakcích transakce a minuty se skládá z dat požadavků a odpovědí a obsahuje informace o kapacitě úložiště dat o využití. Hodinové metriky, minutové metriky a kapacitu úložiště, které je služba objektů Blob v účtu je přístupná v tabulkách, které jsou pojmenovány podle popisu v následující tabulce.  

|Úroveň metriky|Názvy tabulek|Podporováno ve verzích|  
|-------------------|-----------------|----------------------------|  
|Hodinové metriky, primární lokalita|-   $MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Verze starší než 2013-08-15 pouze. I když se tyto názvy jsou stále podporuje, doporučuje se, že přejdete na používání níže uvedené tabulky.|  
|Hodinové metriky, primární lokalita|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Všechny verze. Podpora pro metriky služby soubor je k dispozici pouze ve verzi 2015-04-05 a novějším.|  
|Minutové metriky, primární lokalita|-   $MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Všechny verze. Podpora pro metriky služby soubor je k dispozici pouze ve verzi 2015-04-05 a novějším.|  
|Hodinové metriky, sekundární lokality|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Všechny verze. Geograficky redundantní replikace jen pro čtení musí být povolena.|  
|Minutové metriky, sekundární lokality|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Všechny verze. Geograficky redundantní replikace jen pro čtení musí být povolena.|  
|Kapacita (pouze služby Blob service)|$MetricsCapacityBlob|Všechny verze.|  

 Tyto tabulky se automaticky vytvoří při Storage Analytics je povolené pro koncový bod služby úložiště. Jsou přístupné přes obor názvů tohoto účtu úložiště, například: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Tabulky metrik nezobrazují v operaci výpisu a musí být získávat přístup přímo název tabulky.  

## <a name="enable-metrics-using-the-azure-portal"></a>Povolení metriky pomocí webu Azure portal
Postupujte podle těchto kroků k zapnutí metrik v [webu Azure portal](https://portal.azure.com):

1. Přejděte na svůj účet úložiště.
1. Vyberte **nastavení diagnostiky (klasické)** z **nabídky** podokně.
1. Ujistěte se, že **stav** je nastavena na **na**.
1. Vyberte metriku pro služby, které chcete monitorovat.
1. Zadejte zásady uchovávání informací udávajících dobu uchovávat metrik a protokolování dat o.
1. Vyberte **Uložit**.

[Webu Azure portal](https://portal.azure.com) nepovolíte aktuálně je možné nakonfigurovat minutové metriky ve vašem účtu úložiště, je nutné povolit minutové metriky pomocí Powershellu nebo prostřednictvím kódu programu.

> [!NOTE]
>  Všimněte si, že na webu Azure portal neumožňuje aktuálně je možné nakonfigurovat minutové metriky ve vašem účtu úložiště. Je nutné povolit minutové metriky pomocí Powershellu nebo prostřednictvím kódu programu.  

## <a name="enable-storage-metrics-using-powershell"></a>Zapnutí metrik úložiště pomocí Powershellu  
Konfigurace metrik úložiště ve vašem účtu úložiště pomocí rutiny prostředí Azure PowerShell můžete použít prostředí PowerShell na místním počítači **Get-AzureStorageServiceMetricsProperty** načíst aktuální nastavení a rutiny  **Set-AzureStorageServiceMetricsProperty** změnit aktuální nastavení.  

Rutiny, které řídí metrik úložiště, použijte následující parametry:  

* **ServiceType**, možné hodnoty jsou **Blob**, **fronty**, **tabulky**, a **souboru**.
* **MetricsType**, možné hodnoty jsou **hodinu** a **minutu**.  
* **MetricsLevel**, možné hodnoty jsou:
* **Žádný**: Vypne sledování.
* **Služba**: Shromažďuje metriky, jako je příchozí a odchozí transakce, dostupnosti, latence a procenta úspěšnosti, které se zobrazují se pro objekt blob, fronty, tabulky a souborové služby.
* **ServiceAndApi**: Kromě metrik služeb shromažďuje stejnou sadu metrik pro každou operaci úložiště v rozhraní API služby Azure Storage.

Například následující příkaz zapne minutové metriky pro službu blob service ve výchozí účet úložiště s uchováním nastavte období na pět dní:  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

Následující příkaz načte aktuální hodinové metriky úroveň a uchovávání dní pro službu blob service ve výchozí účet úložiště:  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

Informace o tom, jak nakonfigurovat rutiny Azure Powershellu pro práci s vaším předplatným Azure a jak vybrat výchozí účet úložiště pro použití najdete tady: [Jak nainstalovat a nakonfigurovat Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Zapnutí metrik Storage prostřednictvím kódu programu  
Kromě použití webu Azure portal nebo rutin Azure Powershellu pro ovládací prvek metrik úložiště, můžete také použít jeden z rozhraní API služby Azure Storage. Například pokud používáte jazyk .NET můžete použít klientskou knihovnu pro úložiště.  

Třídy **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**, a **CloudFileClient** mít všechny metody, jako je například  **SetServiceProperties** a **SetServicePropertiesAsync** trvají **ServiceProperties** objektu jako parametr. Můžete použít **ServiceProperties** objekt konfigurace metrik úložiště. Například následující C# fragment kódu ukazuje, jak změnit metriky úroveň a uchovávání dnů pro hodinové metriky fronty:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Další informace o používání jazyka .NET konfigurace metrik úložiště, najdete v části [Klientská knihovna pro úložiště pro .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Obecné informace o konfiguraci metrik Storage pomocí rozhraní REST API najdete v tématu [povolení a konfigurace úložiště analýzy](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Zobrazení metrik Storage  
Po dokončení konfigurace metrik Storage Analytics k monitorování vašeho účtu úložiště, analytika úložiště zaznamenává metriky sadu známých tabulek ve vašem účtu úložiště. Můžete konfigurovat grafy pro zobrazení hodinové metriky [webu Azure portal](https://portal.azure.com):

1. Přejděte do svého účtu úložiště v [webu Azure portal](https://portal.azure.com).
1. Vyberte **metriky (klasické)** v **nabídky** okno pro službu, jejíž metriky, které chcete zobrazit.
1. Klikněte na graf, který chcete konfigurovat.
1. V **upravit graf** okno, vyberte **časový rozsah**, **typ grafu**a metriky, které chcete zobrazit v grafu.

V **monitorování (klasické)** části nabídky okna účtu úložiště na webu Azure Portal, můžete nakonfigurovat [pravidla upozornění](#metrics-alerts), jako je odeslání e-mailové výstrahy, které vás, když se určité metriky dosáhne určitou hodnotu.

Pokud chcete stáhnout metriky pro dlouhodobé uložení nebo jejich analýze místně, musíte použít nástroj nebo napsání kódu pro čtení tabulek. Je nutné stáhnout minutové metriky pro účely analýzy. Tabulky nezobrazí seznam všech tabulek v účtu úložiště, ale je můžete přistupovat přímo podle názvu. Mnoho nástrojů procházení úložiště víme z těchto tabulek a vám umožní zobrazit přímo (viz [klientské nástroje pro Azure Storage](/azure/storage/storage-explorers) seznam dostupných nástrojů).

||||  
|-|-|-|  
|**Metriky**|**Názvy tabulek**|**Poznámky**|  
|Hodinové metriky|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|Ve verzích před 2013-08-15 tyto tabulky používalo označení:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metriky pro službu souborů jsou k dispozici od verze 2015-04-05.|  
|Minutové metriky|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Jde Povolit jenom pomocí Powershellu nebo prostřednictvím kódu programu.<br /><br /> Metriky pro službu souborů jsou k dispozici od verze 2015-04-05.|  
|Kapacita|$MetricsCapacityBlob|Pouze služba objektů BLOB.|  

Pro tyto tabulky na najdete úplné podrobnosti o schémata [tabulkovému schématu metrik Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema). Zobrazit pouze podmnožinu sloupců, které jsou k dispozici následující řádky vzorku, ale ukazují některé důležité funkce způsob, jakým metrik Storage ukládá tyto metriky:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**partitionKey**|**RowKey**|**Časové razítko**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Dostupnost**|**Hodnotu AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|uživatele. Všechny|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|uživatele. QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|uživatele. QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|uživatele. UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

V těchto datech minutové metriky příklad používá klíč oddílu čas minuta rozlišením. Klíč řádku určuje typ informací uložených v řádku, a to se skládá ze dvou částí informací, typ přístupu a typ požadavku:  

-   Typ přístupu je buď **uživatele** nebo **systému**, kde **uživatele** odkazuje na všechny žádosti uživatelů pro službu storage a **systému** odkazuje na požadavky od analytika úložiště.  

-   Typ požadavku je buď **všechny** v takovém případě je souhrnný řádek nebo identifikuje konkrétní rozhraní API, jako **QueryEntity** nebo **UpdateEntity**.  

Ukázková data výše zobrazuje všechny záznamy pro za minutu (cena začíná v 11:00), tak počet **QueryEntities** požadavků plus počet **QueryEntity** požadavků plus počet  **UpdateEntity** žádosti přidat až sedm, což je celkový počet je vidět na **uživatele: všechny** řádek. Obdobně lze odvodit průměrnou dobu vyřízení začátku do konce 104.4286 na **uživatele: všechny** řádek výpočtem ((143.8 * 5) + 3 + 9) / 7.  

## <a name="metrics-alerts"></a>Upozornění metriky
Měli byste zvážit vytvoření výstrah ve službě [webu Azure portal](https://portal.azure.com) tak se budou důležité změny v chování služby úložiště automaticky upozornění. Pokud používáte storage explorer nástroj ke stažení tato data metrik ve formátu s oddělovači, můžete analyzovat data aplikace Microsoft Excel. Zobrazit [klientské nástroje pro Azure Storage](/azure/storage/storage-explorers) seznam nástrojů Průzkumníka úložiště k dispozici. Výstrahy v můžete nakonfigurovat **upozornění (klasická)** okno, přístupná pro **monitorování (klasické)** v nabídce okno účet úložiště.

> [!IMPORTANT]
> Může existovat zpoždění mezi úložiště událostí a kdy se zaznamenává odpovídajících dat hodinových nebo minutové metriky. V případě minutové metriky může několik minut, než data zapsat najednou. To může vést k transakce z dřívější minuty se agregují do transakce pro do aktuální minuty. Pokud k tomu dojde, služba upozornění nemusí mít všechna data dostupné metriky pro nakonfigurovaný interval výstrah, což může vést k neočekávaně aktivaci výstrahy.
>

## <a name="accessing-metrics-data-programmatically"></a>Přístup k datům metriky prostřednictvím kódu programu  
Následující výpis ukazuje ukázkový kód jazyka C#, který přistupuje k nim minutové metriky pro celou řadu minut a zobrazí výsledky v okně konzoly. Vzorový kód používá Klientská knihovna Azure Storage verze 4.x nebo novější, což zahrnuje **CloudAnalyticsClient** třída, která zjednodušuje přístup k tabulkám metriky ve službě storage.  

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

## <a name="billing-on-storage-metrics"></a>Fakturace na metriky úložiště  
Zapsat požadavky na vytvoření entity tabulky pro metriky se účtují standardní sazby platné pro všechny operace služby Azure Storage.  

Požadavky na čtení a odstranění dat metrik pro klienta se taky účtují za standardní sazby. Pokud jste nakonfigurovali zásady uchovávání dat, se vám neúčtují, odstranění starých dat metrik Azure Storage. Ale při odstranění analytická data, váš účet se účtuje za operace odstranění.  

Kapacita použitá tabulky metrik je také fakturovatelný. K odhadu kapacity se používá k ukládání dat metrik můžete použít následující:  

-   Pokud každou hodinu služba využívá každé rozhraní API v každé službě, pak přibližně 148KB dat se ukládají v tabulce metrik transakcí za hodinu Pokud jste povolili obě služby – a úroveň rozhraní API – souhrn.  
-   Pokud v rámci každé hodiny služba využívá každé rozhraní API ve službě, přibližně 12KB dat není uložen každou hodinu v tabulce metrik transakcí, pokud jste povolili souhrn jenom úroveň služby.  
-   V tabulce kapacity pro objekty BLOB má dva řádky přidané každý den, pokud budete mít povoleno v protokolu. Z toho vyplývá, že každý den, velikost této tabulky zvýší o přibližně 300 bajtů.

## <a name="next-steps"></a>Další postup
* [Monitorování účtu úložiště](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Tabulkovému schématu metrik Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Analýza úložiště protokoluje operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Protokolování Storage Analytics](storage-analytics-logging.md)
