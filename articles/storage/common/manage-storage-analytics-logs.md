---
title: Povolit a spravovat protokoly Analýza úložiště Azure (Classic) | Microsoft Docs
description: Naučte se monitorovat účet úložiště v Azure pomocí Analýza úložiště Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 0c182e1093c29206d27a0e55a46dd9a5607fa6ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701701"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Povolit a spravovat protokoly Analýza úložiště Azure (klasické)

[Analýza úložiště Azure](storage-analytics.md) poskytuje protokoly pro objekty blob, fronty a tabulky. Ke konfiguraci protokolů pro svůj účet můžete použít [Azure Portal](https://portal.azure.com) . V tomto článku se dozvíte, jak povolit a spravovat protokoly. Informace o tom, jak povolit metriky, najdete v tématu [povolení a Správa metrik analýza úložiště Azure (Classic)]().  Existují náklady spojené s zkoumáním a ukládáním dat monitorování v Azure Portal. Další informace najdete v tématu [Analýza úložiště](storage-analytics.md).

> [!NOTE]
> Místo protokolů Analýza úložiště doporučujeme používat protokoly Azure Storage v Azure Monitor. Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Další informace najdete v následujících článcích:
>
> - [Monitorování Blob Storage Azure](../blobs/monitor-blob-storage.md)
> - [Monitorování souborů Azure](../files/storage-files-monitoring.md)
> - [Monitorování Queue Storage Azure](../queues/monitor-queue-storage.md)
> - [Monitorování úložiště tabulek v Azure](../tables/monitor-table-storage.md)

Podrobný návod k používání Analýza úložiště a dalších nástrojů k identifikaci, diagnostice a odstraňování potíží souvisejících s Azure Storage najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Povolení protokolů

Můžete dát Azure Storage k uložení protokolů diagnostiky pro požadavky na čtení, zápis a odstranění pro služby blob, Table a Queue. Zásady uchovávání dat, které nastavíte, se vztahují také na tyto protokoly.

> [!NOTE]
> Soubory Azure aktuálně podporují Analýza úložiště metriky, ale nepodporují protokolování Analýza úložiště.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)vyberte **účty úložiště** a potom název účtu úložiště otevřete okno účtu úložiště.

2. V části **monitorování (Classic)** v okně nabídky vyberte **nastavení diagnostiky (Classic** ).

    ![Položka nabídky Diagnostika v části sledování v Azure Portal.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Zajistěte, aby byl **stav** nastaven **na zapnuto**, a vyberte **služby** , pro které chcete povolit protokolování.

   > [!div class="mx-imgBorder"]
   > ![Nakonfigurujte protokolování v Azure Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Ujistěte se, že je zaškrtnuté políčko **Odstranit data** .  Potom nastavte počet dní, po které chcete uchovávat data protokolu přesunutím ovládacího prvku posuvník pod zaškrtávací políčko, nebo přímou úpravou hodnoty, která se zobrazí v textovém poli vedle ovládacího prvku posuvník. Výchozí hodnota pro nové účty úložiště je sedm dní. Pokud nechcete nastavit zásady uchovávání informací, zadejte nula. Pokud neexistují žádné zásady uchovávání informací, je třeba odstranit data protokolu.

   > [!WARNING]
   > Protokoly se ukládají jako data ve vašem účtu. data protokolu se ve vašem účtu můžou shromažďovat v čase, což může zvýšit náklady na úložiště. Pokud potřebujete data protokolu jenom v krátké době, můžete snížit náklady tím, že upravíte zásady uchovávání dat. Zastaralá data protokolu (data starší než vaše zásady uchovávání) jsou v systému odstraněna. Doporučujeme nastavit zásady uchovávání informací na základě doby, po kterou chcete uchovávat data protokolu pro svůj účet. Další informace najdete v tématu [fakturace na metrikách úložiště](storage-analytics-metrics.md#billing-on-storage-metrics) .

4. Klikněte na **Uložit**.

   Diagnostické protokoly se ukládají do kontejneru objektů BLOB s názvem *$logs* ve vašem účtu úložiště. Data protokolu můžete zobrazit pomocí Průzkumníka služby Storage, jako je [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com), nebo programově pomocí klientské knihovny pro úložiště nebo PowerShellu.

   Informace o přístupu k kontejneru $logs najdete v tématu [protokolování služby Storage Analytics](storage-analytics-logging.md).

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

6. Chcete-li změnit aktuální nastavení protokolu, použijte **příkaz set-AzStorageServiceLoggingProperty** . Rutiny, které řídí protokolování úložiště, používají parametr **LoggingOperations** , který je řetězec obsahující seznam typů požadavků oddělených čárkami, které se mají protokolovat. Tři možné typy požadavků jsou **čtení**, **zápis** a **odstranění**. Chcete-li přepnout protokolování, použijte hodnotu **none** pro parametr **LoggingOperations** .  

   Následující příkaz přepne při protokolování žádostí o čtení, zápis a odstranění v Služba front ve vašem výchozím účtu úložiště s možností uchování na pět dní:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > Protokoly se ukládají jako data ve vašem účtu. data protokolu se ve vašem účtu můžou shromažďovat v čase, což může zvýšit náklady na úložiště. Pokud potřebujete data protokolu jenom v krátké době, můžete snížit náklady tím, že upravíte zásady uchovávání dat. Zastaralá data protokolu (data starší než vaše zásady uchovávání) jsou v systému odstraněna. Doporučujeme nastavit zásady uchovávání informací na základě doby, po kterou chcete uchovávat data protokolu pro svůj účet. Další informace najdete v tématu [fakturace na metrikách úložiště](storage-analytics-metrics.md#billing-on-storage-metrics) .
   
   Následující příkaz odpíná protokolování služby Table Service ve vašem výchozím účtu úložiště:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Informace o tom, jak nakonfigurovat rutiny Azure PowerShell pro práci s předplatným Azure a jak vybrat výchozí účet úložiště, který se má použít, najdete v tématu: [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Upravit dobu uchovávání dat protokolu

Data protokolu se ve vašem účtu můžou shromažďovat v čase, což může zvýšit náklady na úložiště. Pokud potřebujete data protokolu jenom v krátké době, můžete snížit náklady tím, že upravíte dobu uchovávání dat protokolu. Pokud například potřebujete protokolovat pouze tři dny, nastavte dobu uchovávání dat protokolu na hodnotu `3` . Tímto způsobem se protokoly z vašeho účtu automaticky odstraní po 3 dnech. V této části se dozvíte, jak zobrazit aktuální dobu uchovávání dat protokolu a pak tuto dobu aktualizovat, pokud to chcete udělat.

> [!NOTE]
> Tyto kroky platí jenom pro účty, u kterých není povolené nastavení **hierarchického oboru názvů** . Pokud jste toto nastavení povolili na svém účtu, nastavení pro dny uchování se zatím nepodporuje. Místo toho budete muset protokoly odstranit ručně pomocí libovolného podporovaného nástroje, například Průzkumník služby Azure Storage, REST nebo sady SDK. Pokud chcete najít tyto protokoly v účtu úložiště, přečtěte si téma [jak se ukládají protokoly](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)vyberte **účty úložiště** a potom název účtu úložiště otevřete okno účtu úložiště.
2. V části **monitorování (Classic)** v okně nabídky vyberte **nastavení diagnostiky (Classic** ).

    ![Položka nabídky Diagnostika v části sledování v Azure Portal](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Ujistěte se, že je zaškrtnuté políčko **Odstranit data** .  Potom nastavte počet dní, po které chcete uchovávat data protokolu přesunutím ovládacího prvku posuvník pod zaškrtávací políčko, nebo přímou úpravou hodnoty, která se zobrazí v textovém poli vedle ovládacího prvku posuvník.

   > [!div class="mx-imgBorder"]
   > ![Upravit dobu uchování v Azure Portal](./media/manage-storage-analytics-logs/modify-retention-period.png)

   Výchozí počet dní pro nové účty úložiště je sedm dní. Pokud nechcete nastavit zásady uchovávání informací, zadejte nula. Pokud neexistují žádné zásady uchovávání informací, je třeba odstranit data monitorování.
   
4. Klikněte na **Uložit**.

   Diagnostické protokoly se ukládají do kontejneru objektů BLOB s názvem *$logs* ve vašem účtu úložiště. Data protokolu můžete zobrazit pomocí Průzkumníka služby Storage, jako je [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com), nebo programově pomocí klientské knihovny pro úložiště nebo PowerShellu.

   Informace o přístupu k kontejneru $logs najdete v tématu [protokolování služby Storage Analytics](storage-analytics-logging.md).

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

5. Získejte kontext účtu úložiště, který definuje účet úložiště.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem vaší skupiny prostředků.

   * Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště. 

6. K zobrazení aktuálních zásad uchovávání protokolů použijte [příkaz Get-AzStorageServiceLoggingProperty](/powershell/module/az.storage/get-azstorageserviceloggingproperty) . Následující příklad vytiskne do konzoly dobu uchování pro služby BLOB a Queue Storage.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   V výstupu konzoly se doba uchování zobrazuje pod `RetentionDays` záhlavím sloupce.

   > [!div class="mx-imgBorder"]
   > ![Zásady uchovávání informací ve výstupu PowerShellu](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Dobu uchovávání můžete změnit pomocí [set-AzStorageServiceLoggingProperty](/powershell/module/az.storage/set-azstorageserviceloggingproperty) . Následující příklad změní dobu uchování na 4 dny.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Informace o tom, jak nakonfigurovat rutiny Azure PowerShell pro práci s předplatným Azure a jak vybrat výchozí účet úložiště, který se má použít, najdete v tématu: [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Následující příklad vytiskne do konzoly dobu uchování pro služby BLOB a Queue Storage.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

Následující příklad změní dobu uchování na 4 dny. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Následující příklad vytiskne do konzoly dobu uchování pro služby BLOB a Queue Storage.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

Následující příklad změní dobu uchování protokolů pro objekty BLOB a Queue Storage na 4 dny. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Ověřte, že se odstraňují data protokolu.

To, jestli se protokoly odstraňují, můžete ověřit zobrazením obsahu `$logs` kontejneru účtu úložiště. Následující obrázek ukazuje obsah složky v `$logs` kontejneru. Složka odpovídá lednu 2021 a každá složka obsahuje protokoly pro jeden den. Pokud dnešní den byl vysílání 29.2021 a vaše zásady uchovávání informací jsou nastavené jenom na jeden den, měla by tato složka obsahovat protokoly jenom pro jeden den.

> [!div class="mx-imgBorder"]
> ![Seznam složek protokolu na webu Azure Portal](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Zobrazit data protokolu

 Pokud chcete zobrazit a analyzovat data protokolu, měli byste si stáhnout objekty blob, které obsahují data protokolu, která vás zajímají, do místního počítače. Mnoho nástrojů pro procházení úložiště vám umožní stahovat objekty BLOB z účtu úložiště. k stažení dat protokolu můžete použít taky Azure Storage týmu [AzCopy](storage-use-azcopy-v10.md) příkazového řádku Azure Copy.  
 
>[!NOTE]
> `$logs`Kontejner není integrován s Event Grid, takže nebudete dostávat oznámení, když budou soubory protokolu zapisovány. 

 Abyste se ujistili, že jste si stáhli data protokolu, která vás zajímají, a nestahovat stejná data protokolu více než jednou:  

-   Pomocí konvence pojmenování data a času pro objekty BLOB obsahující data protokolu můžete sledovat, které objekty blob jste už stáhli k analýze, abyste se vyhnuli opakovanému stažení stejných dat více než jednou.  

-   Použijte metadata v objektech blob obsahující data protokolu k identifikaci konkrétního období, pro které objekt BLOB uchovává data protokolu k identifikaci přesného objektu blob, který potřebujete stáhnout.  

Pokud chcete začít s AzCopy, přečtěte si téma Začínáme [s AzCopy](storage-use-azcopy-v10.md) . 

Následující příklad ukazuje, jak můžete stáhnout data protokolu pro službu fronty po dobu od 09 do 12:00, od 10 DOP. a 11 AM 20. května 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Další informace o tom, jak stáhnout konkrétní soubory, najdete v tématu [stažení objektů BLOB ze služby Azure Blob Storage pomocí AzCopy v10 za účelem](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Po stažení dat protokolu můžete zobrazit položky protokolu v souborech. Tyto soubory protokolu používají textový formát s oddělovači, který dokáže analyzovat mnoho nástrojů pro čtení protokolu (Další informace najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Různé nástroje mají různá zařízení pro formátování, filtrování, řazení a reklamu při hledání obsahu souborů protokolu. Další informace o formátu a obsahu souboru protokolu protokolování úložiště najdete v článku [Formát protokolu analýza úložiště](/rest/api/storageservices/storage-analytics-log-format) a [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Další kroky

* Další informace o Analýza úložiště najdete v tématu [Analýza úložiště](storage-analytics.md) analýza úložiště.
* Další informace o použití jazyka .NET ke konfiguraci protokolování úložiště najdete v tématu [Reference k klientské knihovně pro úložiště](/previous-versions/azure/dn261237(v=azure.100)). 
* Obecné informace o konfiguraci protokolování úložiště pomocí REST API najdete v tématu [povolení a konfigurace analýza úložiště](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* Přečtěte si další informace o formátu protokolů Analýza úložiště. Viz [Formát protokolu analýza úložiště](/rest/api/storageservices/storage-analytics-log-format).
