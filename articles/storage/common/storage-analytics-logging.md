---
title: Protokolování služby Azure Storage Analytics
description: Zjistěte, jak protokolovat podrobnosti o požadavky na služby Azure Storage.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: a350576742a9bcb899405aae19c032cc9b966975
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351317"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage analytics protokolování

Analýza úložiště protokoluje podrobné informace o úspěšné i neúspěšné požadavky do služby úložiště. Tyto informace slouží ke sledování jednotlivých požadavků a Diagnostikujte problémy pomocí služby storage. Žádosti o přihlášení snažíme maximálně vyhovět.

 Ve výchozím nastavení pro váš účet úložiště není povoleno protokolování Storage Analytics. Můžete povolit v [webu Azure portal](https://portal.azure.com/); podrobnosti najdete v tématu [monitorování účtu úložiště na webu Azure Portal](/azure/storage/storage-monitor-storage-account). Můžete také povolit Storage Analytics prostřednictvím kódu programu přes rozhraní REST API nebo knihovny klienta. Použití [získat vlastnosti služby Blob Service](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [získání vlastností služby Queue](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties), a [získat vlastnosti služby tabulky](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) operace povolující analytika úložiště pro každou službu.

 Položky protokolu se vytvoří pouze v případě, že jsou požadavky na koncový bod služby. Například pokud účet úložiště má aktivitu v její koncový bod služby Blob, ale ne v jeho tabulek nebo front koncových bodů, bude vytvořen pouze protokoly týkající se služby Blob service.

> [!NOTE]
>  Je aktuálně k dispozici pouze pro služby objektů Blob, Queue a Table Storage Analytics protokolování. Však nepodporuje účet premium storage.

## <a name="requests-logged-in-logging"></a>Žádosti o přihlášení protokolování

### <a name="logging-authenticated-requests"></a>Protokolování ověření požadavků

 Jsou zaznamenány následující typy ověřených požadavků:

- Úspěšné požadavky
- Neúspěšné požadavky, včetně vypršení časového limitu, omezování, sítě, autorizace a dalších chyb
- Požadavky pomocí sdíleného přístupového podpisu (SAS) nebo OAuth, včetně úspěšné a neúspěšné požadavky
- Požadavky na analytických dat

  Storage Analytics, jako je protokol vytvoření nebo odstranění, žádosti se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolovanými operacemi](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [formát Log Analytics úložiště](/rest/api/storageservices/storage-analytics-log-format) témata.

### <a name="logging-anonymous-requests"></a>Protokolování anonymních požadavků

 Jsou zaznamenány následující typy anonymních požadavků:

- Úspěšné požadavky
- Chyby serveru
- Chyby časového limitu klienta a serveru
- Neúspěšné požadavky GET s kódem chyby 304 (Neupraveno)

  Všechny ostatní neúspěšné anonymní žádosti se neprotokolují. Úplný seznam dat protokolu jsou uvedené v [stavové zprávy a Storage Analytics protokolovanými operacemi](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [formát Log Analytics úložiště](/rest/api/storageservices/storage-analytics-log-format) témata.

## <a name="how-logs-are-stored"></a>Jak se ukládají protokoly

Všechny protokoly se ukládají v objektech BLOB bloku v kontejneru nazvaném `$logs`, které se automaticky vytvoří během analýzy úložiště je povolený pro účet úložiště. `$logs` Kontejneru se nachází v oboru názvů objektů blob v účtu úložiště, například: `http://<accountname>.blob.core.windows.net/$logs`. Tento kontejner nejde odstranit, jakmile bylo povoleno Storage Analytics, i když je možné odstranit její obsah. Pokud používáte nástroj procházení úložiště přejděte přímo do kontejneru, zobrazí se všechny objekty BLOB, které obsahují data protokolování.

> [!NOTE]
>  `$logs` Kontejner se nezobrazí, pokud kontejner výpis operace provádí, jako je například operace seznam kontejnerů. To musí být k nim přistupuje přímo. Například můžete použít operaci výpis objektů blob pro přístup k objektům BLOB v `$logs` kontejneru.

Jak žádosti o přihlášení, analytika úložiště odešlete mezilehlých výsledků jako bloky. Analýza úložiště bude pravidelně, potvrďte tyto bloky a zpřístupněte je jako objekt blob. Může trvat až hodinu, se zobrazí v objektech BLOB v protokolu data **$logs** kontejneru protože frekvenci, s jakou službu storage vyprázdní zapisovače protokolu. Duplicitní záznamy může existovat pro protokoly vytvořené v jedné hodiny. Můžete určit, zda záznam je duplicitní kontrolou **RequestId** a **operace** číslo.

Pokud máte velký objem dat protokolu s více soubory pro každou hodinu, můžete použít metadata objektu blob do určujete, jaká data protokolu obsahuje prozkoumáním pole metadat objektu blob. To je také užitečné vzhledem k tomu může někdy docházet k prodlevám při data se zapisují do souboru protokolu: metadata objektu blob poskytuje přesnější údaj o obsah objektu blob než název objektu blob.

Většina nástrojů procházení úložiště umožňují zobrazit metadata objektů BLOB; Můžete si také přečíst tyto informace pomocí Powershellu nebo prostřednictvím kódu programu. Následující fragment kódu Powershellu je příkladem filtrování seznamu objektů BLOB protokolu podle názvu, jak určit čas a podle metadat k identifikaci pouze tyto protokoly, které obsahují **zápisu** operace.  

 ```  
 Get-AzureStorageBlob -Container '$logs' |  
 where {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 foreach {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Informace o výpis objektů BLOB prostřednictvím kódu programu najdete v tématu [výčet prostředků objektu Blob](http://msdn.microsoft.com/library/azure/hh452233.aspx) a [nastavení a načtení vlastností a metadat prostředkům blobu](http://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Zásady vytváření názvů protokolu

 Každý protokol bude zapsán v následujícím formátu:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 Následující tabulka popisuje jednotlivé atributy v název protokolu:

|Atribut|Popis|
|---------------|-----------------|
|`<service-name>`|Název služby úložiště. Příklad: `blob`, `table`, nebo `queue`|
|`YYYY`|Čtyřmístné rok protokolu. Příklad: `2011`|
|`MM`|Měsíc dvě číslice pro protokol. Příklad: `07`|
|`DD`|Den dvě číslice pro protokol. Příklad: `31`|
|`hh`|Hodiny dvě číslice, která označuje počáteční hodina protokolů, v průběhu 24 hodin UTC formátu. Příklad: `18`|
|`mm`|Dvoumístné číslo určující počáteční minut protokolů. **Poznámka:**  Tato hodnota není podporován v aktuální verzi analytika úložiště a jeho hodnota bude vždy `00`.|
|`<counter>`|Založený na nule čítač se šesti číslicemi, která určuje počet objektů BLOB protokolu vygenerovaný pro službu storage do jedné hodiny časové období. Tento čítač začíná `000000`. Příklad: `000001`|

 Toto je název protokolu úplnou ukázku, která kombinuje výše uvedených příkladech:

 `blob/2011/07/31/1800/000001.log`

 Následuje ukázkový identifikátor URI, který lze použít pro přístup k protokolu výše:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Když se zaznamená žádost úložiště, výsledný název protokolu souvisí s hodina dokončení požadované operace. Například pokud žádost GetBlob se dokončila na 18:30:00 na 7/31/2011, protokolu by být zapsaný s následující předpony: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadata protokolu

 Všechny objekty BLOB protokolu se ukládají se metadata, která je možné určit protokolování data, která obsahuje objekt blob. Následující tabulka popisuje každý atribut metadat:

|Atribut|Popis|
|---------------|-----------------|
|`LogType`|Popisuje, zda v protokolu obsahuje informace týkající se číst, zapsat nebo odstranit operace. Tato hodnota může obsahovat jeden typ nebo s použitím kombinace všech tří, oddělené čárkami.<br /><br /> Příklad 1: `write`<br /><br /> Příklad 2: `read,write`<br /><br /> Příklad 3: `read,write,delete`|
|`StartTime`|Nejdřívější čas záznam v protokolu ve formě `YYYY-MM-DDThh:mm:ssZ`. Příklad: `2011-07-31T18:21:46Z`|
|`EndTime`|Čas posledního položky v protokolu ve formě `YYYY-MM-DDThh:mm:ssZ`. Příklad: `2011-07-31T18:22:09Z`|
|`LogVersion`|Verze formátu protokolu.|

 Následující seznam zobrazuje metadata úplnou ukázku pomocí výše uvedených příkladech:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Povolit protokolování úložiště

Můžete povolit protokolování úložiště pomocí webu Azure portal, PowerShell a sady SDK úložiště.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Povolit protokolování úložiště pomocí webu Azure portal  

Na webu Azure Portal, použijte **nastavení diagnostiky (klasické)** okno na úložiště, protokolování, přístupné z ovládacího prvku **monitorování (klasické)** části účtu služby storage **nabídky okna** .

Můžete zadat služby úložiště, které chcete se přihlásit a doba uchování (ve dnech) dat protokolu.  

### <a name="enable-storage-logging-using-powershell"></a>Povolit protokolování úložiště pomocí Powershellu  

 Můžete použijete prostředí PowerShell na místním počítači ke konfiguraci protokolování úložiště ve vašem účtu úložiště pomocí Azure powershellu **Get-AzureStorageServiceLoggingProperty** načíst aktuální nastavení a rutiny  **Set-AzureStorageServiceLoggingProperty** změnit aktuální nastavení.  

 Pomocí rutin, které řídí protokolování úložiště **LoggingOperations** parametr, který představuje řetězec obsahující seznam oddělený čárkami typy požadavků do protokolu. Jsou tři typy možných požadavků **čtení**, **zápisu**, a **odstranit**. Chcete-li vypnout protokolování, použijte hodnotu **žádný** pro **LoggingOperations** parametru.  

 Následující příkaz zapne protokolování pro čtení, zápis a odstranění žádostí ve službě Queue ve výchozí účet úložiště s uchováváním informací. nastavení bylo o pět dnů:  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Následující příkaz vypne protokolování pro službu tabulky ve výchozí účet úložiště:  

```  
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Informace o tom, jak nakonfigurovat rutiny Azure Powershellu pro práci s vaším předplatným Azure a jak vybrat výchozí účet úložiště pro použití najdete tady: [Jak nainstalovat a nakonfigurovat Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Povolit úložiště protokolování prostřednictvím kódu programu  
 Kromě použití webu Azure portal nebo rutin Azure Powershellu pro řízení protokolování úložiště, můžete také použít jeden z rozhraní API služby Azure Storage. Například pokud používáte jazyk .NET můžete použít klientskou knihovnu pro úložiště.  

 Třídy **CloudBlobClient**, **CloudQueueClient**, a **CloudTableClient** všechny metody, jako mají **SetServiceProperties** a **SetServicePropertiesAsync** trvají **ServiceProperties** objektu jako parametr. Můžete použít **ServiceProperties** objektu, který chcete nakonfigurovat protokolování úložiště. Například následující C# fragment kódu ukazuje, jak změnit, co je protokolováno a doby uchování pro protokolování fronty:  

```  
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Další informace o používání jazyka .NET Pokud chcete nakonfigurovat protokolování úložiště najdete v tématu [Reference knihovny klienta úložiště](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Obecné informace o konfiguraci úložiště protokolování pomocí rozhraní REST API najdete v tématu [povolení a konfigurace úložiště analýzy](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Stáhněte si úložiště, protokolování dat protokolu

 Můžete zobrazit a analyzovat data protokolů, byste si měli stáhnout objekty BLOB, které obsahují data protokolu, které vás zajímají do místního počítače. Mnoho nástrojů procházení úložiště umožňují stahovat objekty BLOB z účtu úložiště; Můžete také použít tým Azure Storage poskytuje zkopírujte nástroj příkazového řádku Azure (**AzCopy**) Chcete-li stáhnout data protokolu.  

 Aby se zabránilo více než jednou stahování stejná data protokolu a ujistěte se, že si stáhnout data protokolu, které vás zajímají:  

-   Datum a čas zásady vytváření názvů objektů BLOB, který obsahuje data protokolu pro sledování, které objekty BLOB, můžete staženy pro analýzu, aby se zabránilo znovu stejná data stáhnout více než jednou.  

-   Metadata pro objekty BLOB, který obsahuje data protokolu slouží k identifikaci konkrétní období, pro který objekt blob obsahuje data protokolu pro identifikaci přesné objekt blob, které je potřeba stáhnout.  

> [!NOTE]
>  AzCopy je součástí sady Azure SDK, ale můžete kdykoli stáhnout nejnovější verzi z [ https://aka.ms/AzCopy ](https://aka.ms/AzCopy). Ve výchozím nastavení je nástroj AzCopy nainstalovaný ve složce **C:\Program Files (x86) \Microsoft Azure\AzCopy**, a před pokusem o spuštění nástroje v okně Powershellu nebo příkazového řádku, měli byste přidat tuto složku do cesty.  

 Následující příklad ukazuje, jak si můžete stáhnout data protokolu pro službu front v hodinách od 09 AM, 10 AM a 11. hodinou RÁNO ve 20. května 2014. **/S** parametr AzCopy k vytvoření struktury místní složky podle data a časy v názvech souborů protokolu; způsobí, že **/V** parametr AzCopy podrobný výstup; způsobí, že **/Y** parametr způsobí, že AzCopy můžete přepsat všechny místní soubory. Nahraďte **< yourstorageaccount\>**  s názvem účtu úložiště a nahraďte **< yourstoragekey\>**  vaším klíčem účtu úložiště.  

```  
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy má také některé užitečné parametry jak nastaví čas poslední změny na soubory, které stáhne ovládacího prvku a určuje, zda se pokusí stáhnout soubory, které jsou novější než soubory, které již existují na místním počítači nebo starší. Můžete ho spustit také v režimu restartování. Úplné podrobnosti zobrazíte nápovědu spuštěním **AzCopy /?** příkaz.  

 Příklad toho, jak stáhnout data protokolů prostřednictvím kódu programu, najdete v příspěvku blogu [protokolování úložiště Windows Azure: Pomocí protokolů pro sledování požadavků na úložiště](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) a vyhledejte slovo "DumpLogs" na stránce.  

 Když jste si stáhli dat protokolů, zobrazí se položky protokolu v souborech. Tyto soubory protokolu použití formátu odděleného textu tolik protokolu čtení nástroje jsou rozboru, včetně Microsoft Message Analyzer (Další informace najdete v příručce [monitorování, diagnostikování a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Jiné nástroje mají různé funkce pro formátování, filtrování, řazení, hledání obsah souborů protokolu ad. Další informace o protokolování úložiště formát souborů protokolu a obsahu najdete v tématu [formát Log Analytics úložiště](/rest/api/storageservices/storage-analytics-log-format) a [stavové zprávy a Storage Analytics protokolovanými operacemi](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Další postup
* [Formát úložiště analýzy protokolů](/rest/api/storageservices/storage-analytics-log-format)
* [Analýza úložiště protokoluje operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metrikách Storage Analytics (classic)](storage-analytics-metrics.md)
