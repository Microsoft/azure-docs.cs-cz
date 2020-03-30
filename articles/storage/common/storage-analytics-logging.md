---
title: Protokolování Analýzy úložiště Azure
description: Přečtěte si, jak protokolovat podrobnosti o požadavcích na Azure Storage.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 25c047dc9b2ce08ca39e69c6f106e41c5d9bd0dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268415"
---
# <a name="azure-storage-analytics-logging"></a>Protokolování Analýzy úložiště Azure

Analýza úložiště protokoluje podrobné informace o úspěšných a neúspěšných požadavcích na službu úložiště. Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí.

 Protokolování Analýzy úložiště ve výchozím nastavení není pro váš účet úložiště povolené. Můžete ji povolit na [webu Azure Portal](https://portal.azure.com/); Podrobnosti najdete [v tématu Sledování účtu úložiště na webu Azure Portal](/azure/storage/storage-monitor-storage-account). Analýzu úložiště můžete také povolit programově prostřednictvím rozhraní REST API nebo klientské knihovny. Pomocí operací [Získat vlastnosti služby blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [Získat vlastnosti služby fronty](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)a Získat [službu table service pro](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) každou službu povolte analýzu úložiště.

 Položky protokolu jsou vytvořeny pouze v případě, že jsou požadavky proti koncovému bodu služby. Například pokud účet úložiště má aktivitu v jeho koncovém bodě objektu blob, ale ne v jeho koncové body tabulky nebo fronty, budou vytvořeny pouze protokoly týkající se služby objektu Blob.

> [!NOTE]
>  Protokolování Analýzy úložiště je v současné době dostupné pouze pro služby Blob a Table service a Službu front. Účet úložiště Premium se však nepodporuje.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>Požadavky přihlášené k protokolování
### <a name="logging-authenticated-requests"></a>Protokolování ověřených požadavků

 Protokolují se následující typy ověřovaných požadavků:

- Úspěšné požadavky
- Neúspěšné požadavky, včetně vypršení časového limitu, omezování, chyb sítě, selhání autorizace a dalších chyb
- Požadavky používající SAS (Shared Access Signature) nebo OAuth (Open Authorization), včetně neúspěšných i úspěšných požadavků
- Žádosti o analytická data

  Požadavky provedené samotnou službou Storage Analytics, jako je například vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat je zdokumentován v tématech [protokolovaných operací služby Storage Analytics a zpráv o stavu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a v [tématech formátu protokolu služby Storage Analytics.](/rest/api/storageservices/storage-analytics-log-format)

### <a name="logging-anonymous-requests"></a>Protokolování anonymních požadavků

 Jsou protokolovány následující typy anonymních požadavků:

- Úspěšné požadavky
- Chyby serveru
- Chyby časového oběhu pro klienta i server
- Neúspěšné požadavky GET s kódem chyby 304 (Nezměněno)

  Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat je zdokumentován v tématech [protokolovaných operací služby Storage Analytics a zpráv o stavu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a v [tématech formátu protokolu služby Storage Analytics.](/rest/api/storageservices/storage-analytics-log-format)

## <a name="how-logs-are-stored"></a>Jak jsou protokoly uloženy

Všechny protokoly jsou uloženy v blobů bloku v kontejneru s názvem `$logs`, který se automaticky vytvoří, když je služba Storage Analytics povolena pro účet úložiště. Kontejner `$logs` se nachází v oboru názvů objektů blob účtu `http://<accountname>.blob.core.windows.net/$logs`účtu úložiště, například: . Tento kontejner nelze odstranit, jakmile je povolena služba Storage Analytics, i když jeho obsah lze odstranit. Pokud používáte nástroj pro procházení úložiště k navigaci do kontejneru přímo, uvidíte všechny objekty BLOB, které obsahují data protokolování.

> [!NOTE]
>  Kontejner `$logs` se nezobrazí při provádění operace výpisu kontejneru, jako je například operace Seznam kontejnerů. Musí být přístupné přímo. Můžete například použít operaci objektů BLOB seznamu pro přístup `$logs` k objektům BLOB v kontejneru.

Při zaznamenávání požadavků služba Storage Analytics nahraje průběžné výsledky jako bloky. Služba Storage Analytics tyto bloky pravidelně potvrdí a zpřístupní je jako objekt blob. Může trvat až hodinu, než se data protokolu zobrazí v objektech BLOB v kontejneru **$logs** protože frekvence, na které služba úložiště vyprázdní zapisovače protokolu. Duplicitní záznamy mohou existovat pro protokoly vytvořené ve stejné hodině. Můžete určit, zda záznam je duplicitní kontrolou **RequestId** a **číslo operace.**

Pokud máte velký objem dat protokolu s více soubory pro každou hodinu, pak můžete použít metadata objektu blob k určení, jaká data protokol obsahuje kontrolou polí metadat objektu blob. To je také užitečné, protože někdy může být zpoždění při zápisu dat do souborů protokolu: metadata objektu blob poskytuje přesnější označení obsahu objektu blob než název objektu blob.

Většina nástrojů pro procházení úložiště umožňuje zobrazit metadata objektů BLOB; Můžete také číst tyto informace pomocí prostředí PowerShell nebo programově. Následující fragment prostředí PowerShell je příkladem filtrování seznamu objektů BLOB protokolu podle názvu a metadat a metadat identifikovat pouze ty protokoly, které obsahují operace **zápisu.**  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Informace o programovém výpisu objektů blob naleznete v [tématu Výčet prostředků objektů Blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) a [nastavení a načítání vlastností a metadat pro prostředky objektů blob](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Protokolovat konvence pojmenování

 Každý protokol bude zapsán v následujícím formátu:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 Následující tabulka popisuje každý atribut v názvu protokolu:

|Atribut|Popis|
|---------------|-----------------|
|`<service-name>`|Název služby úložiště. Například: `blob` `table`, , nebo`queue`|
|`YYYY`|Čtyřmístný rok pro protokol. Příklad: `2011`|
|`MM`|Dvoumístný měsíc pro protokol. Příklad: `07`|
|`DD`|Dvoumístný den pro protokol. Příklad: `31`|
|`hh`|Dvoumístná hodina, která označuje počáteční hodinu pro protokoly ve formátu UTC 24 hodin. Příklad: `18`|
|`mm`|Dvoumístné číslo, které označuje počáteční minutu pro protokoly. **Poznámka:**  Tato hodnota není podporována v aktuální verzi služby Storage `00`Analytics a její hodnota bude vždy .|
|`<counter>`|Čítač založený na nule se šesti číslicemi, který označuje počet objektů BLOB protokolu generovaných pro službu úložiště v hodinovém časovém období. Tento čítač začíná na `000000`. Příklad: `000001`|

 Následuje úplný ukázkový název protokolu, který kombinuje výše uvedené příklady:

 `blob/2011/07/31/1800/000001.log`

 Následuje ukázkový identifikátor URI, který lze použít pro přístup k výše uvedenému protokolu:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Při zaznamenání požadavku na úložiště výsledný název protokolu koreluje s hodinou po dokončení požadované operace. Například pokud požadavek GetBlob byla dokončena v 18:30 na 7/31/2011, protokol by být zapsáns následující předponou:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Protokolovat metadata

 Všechny objekty BLOB protokolu jsou uloženy s metadaty, které lze použít k identifikaci, jaká data protokolování objekt blob obsahuje. Následující tabulka popisuje každý atribut metadat:

|Atribut|Popis|
|---------------|-----------------|
|`LogType`|Popisuje, zda protokol obsahuje informace týkající se operací čtení, zápisu nebo odstraňování. Tato hodnota může zahrnovat jeden typ nebo kombinaci všech tří, oddělených čárkami.<br /><br /> Příklad 1:`write`<br /><br /> Příklad 2:`read,write`<br /><br /> Příklad 3:`read,write,delete`|
|`StartTime`|Nejbližší čas zápisu do protokolu ve formě `YYYY-MM-DDThh:mm:ssZ`. Příklad: `2011-07-31T18:21:46Z`|
|`EndTime`|Poslední čas zápisu v protokolu, ve `YYYY-MM-DDThh:mm:ssZ`formě . Příklad: `2011-07-31T18:22:09Z`|
|`LogVersion`|Verze formátu protokolu.|

 Následující seznam zobrazuje úplná ukázková metadata pomocí výše uvedených příkladů:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Povolení protokolování úložiště

Protokolování úložiště můžete povolit pomocí Azure Portal, PowerShellu a sad SDK úložiště.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Povolení protokolování úložiště pomocí portálu Azure  

Na webu Azure Portal použijte okno **Nastavení diagnostiky (klasické)** k řízení protokolování úložiště, které je přístupné z části **Monitorování (klasické)** **v okně menu**účtu úložiště .

Můžete určit služby úložiště, které chcete protokolovat, a dobu uchování (ve dnech) pro protokolovaná data.  

### <a name="enable-storage-logging-using-powershell"></a>Povolení protokolování úložiště pomocí PowerShellu  

 Pomocí Prostředí PowerShell v místním počítači můžete nakonfigurovat protokolování úložiště v účtu úložiště pomocí rutiny Azure PowerShell **Get-AzureStorageServiceLoggingProperty** k načtení aktuálního nastavení a rutina **Set-AzureStorageServiceLoggingProperty** pro změnu aktuálního nastavení.  

 Rutiny, které řídí protokolování úložiště použít **LoggingOperations** parametr, který je řetězec obsahující čárka oddělený seznam typů požadavků protokolu. Tři možné typy požadavků jsou **čtení**, **zápis**a **odstranění**. Chcete-li protokolování vypnout, použijte pro parametr **LoggingOperations** hodnotu **žádná.**  

 Následující příkaz se přepíná při protokolování požadavků na čtení, zápis a odstranění ve službě Fronta ve výchozím účtu úložiště s možností uchovávání informací nastavenou na pět dní:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Následující příkaz vypne protokolování pro službu table service ve výchozím účtu úložiště:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Informace o tom, jak nakonfigurovat rutiny Azure PowerShellu tak, aby fungovaly s vaším předplatným Azure, a jak vybrat výchozí účet úložiště, který se má použít, najdete v [tématu: Jak nainstalovat a nakonfigurovat Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Programové povolení protokolování úložiště  

 Kromě použití portálu Azure Portal nebo rutin Azure PowerShell k řízení protokolování úložiště můžete také použít jedno z api úložiště Azure. Pokud například používáte jazyk .NET, můžete použít klientskou knihovnu úložiště.  

 Třídy **CloudBlobClient**, **CloudQueueClient**a **CloudTableClient** mají všechny metody, jako jsou **SetServiceProperties** a **SetServicePropertiesAsync,** které berou objekt **ServiceProperties** jako parametr. Objekt **ServiceProperties** můžete použít ke konfiguraci protokolování úložiště. Například následující fragment c# ukazuje, jak změnit, co je zaznamenána a doba uchování pro protokolování fronty:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Další informace o použití jazyka .NET ke konfiguraci protokolování úložiště naleznete v [tématu Odkaz na klientskou knihovnu úložiště](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Obecné informace o konfiguraci protokolování úložiště pomocí rozhraní REST API naleznete v tématu [Povolení a konfigurace analýzy úložiště](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Stáhnout data protokolu protokolu úložiště

 Chcete-li zobrazit a analyzovat data protokolu, měli byste stáhnout objekty BLOB, které obsahují data protokolu, které vás zajímají, do místního počítače. Mnoho nástrojů pro procházení úložiště umožňuje stahovat objekty BLOB z vašeho účtu úložiště; Můžete také použít tým Azure Storage za předpokladu, příkazový řádek Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) ke stažení dat protokolu.  
 
>[!NOTE]
> Kontejner `$logs` není integrován s Event Grid, takže nebudete dostávat oznámení při zápisu souborů protokolu. 

 Chcete-li se ujistit, že stáhnete data protokolu, která vás zajímají, a vyhnout se stahování stejných dat protokolu více než jednou:  

-   Pomocí konvence pojmenování data a času pro objekty BLOB obsahující data protokolu můžete sledovat, které objekty BLOB jste již stáhli pro analýzu, abyste zabránili opětovnému stažení stejných dat více než jednou.  

-   Pomocí metadat na objekty BLOB obsahující data protokolu k identifikaci konkrétní období, pro které objekt blob obsahuje data protokolu k identifikaci přesné objektu blob, který je třeba stáhnout.  

Informace o tom, jak začít s AzCopy, [najdete v tématu Začínáme s AzCopy](storage-use-azcopy-v10.md) 

Následující příklad ukazuje, jak můžete stáhnout data protokolu pro službu fronty pro hodiny začínající v 09:00, 10:00 a 11:00 20:00 20:00.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Další informace o stahování konkrétních souborů naleznete v [tématu Stahování konkrétních souborů](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files).

Po stažení dat protokolu můžete zobrazit položky protokolu v souborech. Tyto soubory protokolu používají oddělený textový formát, který mnoho nástrojů pro čtení protokolu jsou schopni analyzovat, včetně Microsoft Message Analyzer (další informace naleznete v příručce [Monitorování, Diagnostika a řešení potíží s Úložiště Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md)). Různé nástroje mají různé možnosti formátování, filtrování, řazení, prohledávání obsahu souborů protokolu. Další informace o formátu a obsahu souboru protokolu protokolu protokolování úložiště naleznete v [tématu Formát protokolu služby Storage Analytics](/rest/api/storageservices/storage-analytics-log-format) a protokolované operace a [zprávy o stavu služby Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Další kroky

* [Formát protokolu analýzy úložiště](/rest/api/storageservices/storage-analytics-log-format)
* [Služby pro protokolované operace a stavové zprávy analýzy úložiště](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metriky analýzy úložiště (klasické)](storage-analytics-metrics.md)
