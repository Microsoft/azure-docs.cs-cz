---
title: Povolit a spravovat metriky Analýza úložiště Azure (Classic) | Microsoft Docs
description: Naučte se, jak povolit, upravovat a zobrazovat Analýza úložiště Azure metriky.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99221573"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Povolit a spravovat metriky Analýza úložiště Azure (Classic)

[Analýza úložiště Azure](storage-analytics.md) poskytuje metriky pro všechny služby úložiště pro objekty blob, fronty a tabulky. Pomocí [Azure Portal](https://portal.azure.com) můžete nakonfigurovat, které metriky se budou zaznamenávat pro váš účet, a nakonfigurovat grafy, které poskytují vizuální reprezentace dat metrik. V tomto článku se dozvíte, jak povolit a spravovat metriky. Informace o tom, jak povolit protokoly, najdete v tématu [povolení a správa protokolů analýza úložiště Azure (Classic)](manage-storage-analytics-logs.md).

Doporučujeme, abyste zkontrolovali [Azure monitor pro úložiště](../../azure-monitor/insights/storage-insights-overview.md) (Preview). Jedná se o funkci Azure Monitor, která nabízí ucelený přehled o vašich Azure Storagech účtech tím, že poskytuje jednotný přehled o výkonu, kapacitě a dostupnosti služby Azure Storage Services. Nevyžaduje, abyste povolili ani nenakonfigurovali cokoli. Tyto metriky můžete okamžitě zobrazit z předem definovaných interaktivních grafů a dalších vizualizací, které jsou v ní obsažené.

> [!NOTE]
> Existují náklady spojené s zkoumáním dat monitorování v Azure Portal. Další informace najdete v tématu [Analýza úložiště](storage-analytics.md).
>
> Účty úložiště blob bloku úrovně Premium nepodporují Analýza úložiště metriky. Pokud chcete zobrazit metriky s účty úložiště blob bloku Premium Performance, zvažte použití [Azure Storage metrik v Azure monitor](../blobs/monitor-blob-storage.md).
>
> Podrobný návod k používání Analýza úložiště a dalších nástrojů k identifikaci, diagnostice a odstraňování potíží souvisejících s Azure Storage najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Povolit metriky

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)vyberte **účty úložiště** a potom název účtu úložiště otevřete řídicí panel účtu.

2. V části **monitorování (Classic)** v okně nabídky vyberte **nastavení diagnostiky (Classic** ).
   
   ![Snímek obrazovky, který zvýrazní možnost nastavení diagnostiky (Classic) v části monitorování (Classic).](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Vyberte **typ** dat metriky pro každou **službu** , kterou chcete monitorovat, a **zásady uchovávání** dat. Monitorování můžete také zakázat nastavením **stav** na **vypnuto**.

   > [!div class="mx-imgBorder"]
   > ![Nakonfigurujte protokolování v Azure Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Chcete-li nastavit zásady uchovávání dat, přesuňte posuvník **Doba uchovávání (dny)** nebo zadejte počet dní, po které se mají data uchovávat, od 1 do 365. Výchozí hodnota pro nové účty úložiště je sedm dní. Pokud nechcete nastavit zásady uchovávání informací, zadejte nula. Pokud neexistují žádné zásady uchovávání informací, je třeba odstranit data monitorování.

   > [!WARNING]
   > Metics se ukládají jako data ve vašem účtu. Data metriky se ve vašem účtu můžou shromažďovat v čase, což může zvýšit náklady na úložiště. Pokud budete potřebovat data metrik jenom pro malou dobu, můžete snížit náklady tím, že upravíte zásady uchovávání dat. Systém odstranil data zastaralých metrik (data starší než vaše zásady uchovávání informací). Doporučujeme nastavit zásady uchovávání informací na základě doby, po kterou chcete uchovávat data metriky pro váš účet. Další informace najdete v tématu [fakturace na metrikách úložiště](storage-analytics-metrics.md#billing-on-storage-metrics) .
   >

4. Po dokončení konfigurace monitorování vyberte **Uložit**.

V grafech v okně **Přehled** se zobrazí výchozí sada metrik a také okno **metriky (Classic)** . Jakmile u služby povolíte metriky, může trvat až hodinu, než se data zobrazí ve svých grafech. Můžete vybrat **Upravit** v libovolném grafu metriky a nakonfigurovat, které metriky se zobrazí v grafu.

Shromažďování a protokolování metrik můžete zakázat nastavením **stav** na **vypnuto**.

> [!NOTE]
> Azure Storage využívá [úložiště tabulek](storage-introduction.md#table-storage) k ukládání metrik pro váš účet úložiště a ukládá metriky v tabulkách ve vašem účtu. Další informace najdete v tématech: [Jak jsou uloženy metriky](storage-analytics-metrics.md#how-metrics-are-stored).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Otevřete okno příkazového řádku Windows PowerShellu.

2. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

   ```powershell
   Connect-AzAccount
   ```

3. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

5. Získejte kontext účtu úložiště, který definuje účet úložiště, který chcete použít.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem vaší skupiny prostředků.

   * Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště. 

6. Pomocí PowerShellu na místním počítači můžete nakonfigurovat metriky úložiště ve vašem účtu úložiště. Chcete-li změnit aktuální nastavení, použijte rutinu Azure PowerShell **set-AzStorageServiceMetricsProperty** . 

   Následující příkaz přepne na minuty pro službu BLOB Service ve vašem účtu úložiště s dobou uchování nastavenou na pět dní.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Tato rutina používá následující parametry:  

   - **ServiceType**: možné hodnoty jsou **BLOB**, **Queue**, **Table** a **File**.
   - **MetricsType**: možné hodnoty jsou **Hour** a **minute**.  
   - **MetricsLevel**: možné hodnoty:
      - **Žádné**: vypne monitorování.
      - **Služba**: shromažďuje metriky, jako jsou příchozí a odchozí, dostupnost, latence a procento úspěšnosti, které jsou agregované pro objekty blob, front, tabulek a souborové služby.
      - **ServiceAndApi**: Kromě metrik služby shromažďuje stejnou sadu metrik pro každou operaci úložiště v rozhraní API služby Azure Storage.

   Následující příkaz načte aktuální hodinovou metriku a dny uchování pro službu BLOB Service ve vašem výchozím účtu úložiště:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Informace o tom, jak nakonfigurovat rutiny Azure PowerShell pro práci s předplatným Azure a jak vybrat výchozí účet úložiště, který se má použít, najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Další informace o tom, jak pomocí jazyka .NET nakonfigurovat metriky úložiště, najdete v tématu [Azure Storage klientských knihoven pro .NET](/dotnet/api/overview/azure/storage).  

Obecné informace o konfiguraci metrik úložiště pomocí REST API najdete v tématu [povolení a konfigurace analýza úložiště](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Další informace o tom, jak pomocí jazyka .NET nakonfigurovat metriky úložiště, najdete v tématu [Azure Storage klientských knihoven pro .NET](/dotnet/api/overview/azure/storage).  

Obecné informace o konfiguraci metrik úložiště pomocí REST API najdete v tématu [povolení a konfigurace analýza úložiště](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Zobrazení metrik v grafu

Když nakonfigurujete metriky Analýza úložiště, abyste mohli monitorovat svůj účet úložiště, Analýza úložiště zaznamenává metriky v sadě dobře známých tabulek v účtu úložiště. Můžete nakonfigurovat grafy pro zobrazení hodinových metrik v [Azure Portal](https://portal.azure.com).

Pomocí následujícího postupu můžete zvolit metriky úložiště, které se mají zobrazit v grafu metrik.

1. Začněte zobrazením grafu metriky úložiště v Azure Portal. Grafy můžete najít v okně **účet úložiště** a v okně **metriky (klasické)** .

   V tomto příkladu používáme následující graf, který se zobrazí v okně **účet úložiště**:

   ![Výběr grafu v Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Graf upravíte Kliknutím kamkoli v grafu.

3. V dalším kroku vyberte **časový rozsah** metrik, který se má zobrazit v grafu, a **službu** (objekt blob, frontu, tabulku, soubor), jejíž metriky chcete zobrazit. Tady se vyberou metriky v minulém týdnu, které se mají zobrazit pro službu BLOB Service:

   ![Časový rozsah a výběr služby v okně Upravit graf](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Vyberte jednotlivé **metriky** , které chcete zobrazit v grafu, a pak klikněte na **OK**.

   ![Výběr jednotlivých metrik v okně Upravit graf](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

Nastavení grafu nemá vliv na shromažďování, agregaci ani ukládání dat monitorování v účtu úložiště.

#### <a name="metrics-availability-in-charts"></a>Dostupnost metrik v grafech

Seznam dostupných metrik se mění podle toho, kterou službu jste zvolili v rozevíracím seznamu, a typem jednotky pro graf, který upravujete. Můžete například vybrat procentuální metriky jako *PercentNetworkError* a *PercentThrottlingError* pouze v případě, že upravujete graf zobrazující jednotky v procentech:

![Graf procento chyb žádosti v Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Řešení metrik

Metriky, které jste vybrali v části **Diagnostika** , určují řešení metrik, které jsou k dispozici pro váš účet:

* **Agregované** monitorování poskytuje metriky, jako je příchozí/odchozí, dostupnost, latence a procento úspěšnosti. Tyto metriky jsou agregované ze služeb blob, Table, File a Queue.
* **Každé rozhraní API** poskytuje přesnější řešení s metrikami, které jsou dostupné pro jednotlivé operace úložiště, a navíc k agregacím na úrovni služby.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Stáhnout metriky k archivaci nebo analýze místně

Pokud chcete stáhnout metriky pro dlouhodobé ukládání nebo je analyzovat místně, je nutné použít nástroj nebo napsat kód pro čtení tabulek. Tabulky se nezobrazí, pokud vypíšete všechny tabulky v účtu úložiště, ale můžete k nim přistupovat přímo podle názvu. Mnoho nástrojů pro procházení úložiště ví o těchto tabulkách a umožňuje vám jejich přímé prohlížení. Seznam dostupných nástrojů najdete v tématu [Azure Storage klientských nástrojů](./storage-explorers.md).

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

## <a name="view-metrics-data-programmatically"></a>Zobrazení dat metrik prostřednictvím kódu programu

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

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Přidání grafů metrik na řídicí panel portálu

Do řídicího panelu portálu můžete přidat grafy Azure Storage metriky pro libovolný účet úložiště.

1. Vyberte možnost **Upravit řídicí panel** při prohlížení řídicího panelu v [Azure Portal](https://portal.azure.com).
1. V **galerii dlaždic** vyberte **najít dlaždice podle**  >  **typu**.
1. Vyberte **typ**  >  **účty úložiště**.
1. V části **prostředky** vyberte účet úložiště, jehož metriky chcete přidat na řídicí panel.
1. Vyberte možnost  >  **monitorování** kategorií.
1. Přetáhněte dlaždici grafu na řídicí panel, aby se zobrazila metrika, kterou chcete zobrazit. Opakujte pro všechny metriky, které byste chtěli zobrazit na řídicím panelu. Na následujícím obrázku je jako příklad zvýrazněný graf objekty blob – celkový počet požadavků, ale všechny grafy jsou k dispozici pro umístění na řídicím panelu.

   ![Galerie dlaždic v Azure Portal](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Až skončíte s přidáváním grafů, vyberte **Hotovo přizpůsobení** u horní části řídicího panelu.

Po přidání grafů na řídicí panel je můžete dále upravit, jak je popsáno v tématu přizpůsobení grafů metrik.

## <a name="next-steps"></a>Další kroky

* Další informace o Analýza úložiště najdete v tématu [Analýza úložiště](storage-analytics.md) analýza úložiště.
* [Nakonfigurujte protokoly analýza úložiště](manage-storage-analytics-logs.md).
* Přečtěte si další informace o schématu metrik. Viz [Analýza úložiště schéma tabulky metrik](/rest/api/storageservices/storage-analytics-metrics-table-schema).