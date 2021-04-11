---
title: Protokolování Analýzy úložiště Azure
description: K protokolování podrobností o Azure Storage požadavků použijte Analýza úložiště. Podívejte se, jaké požadavky se protokolují, jak se ukládají protokoly, jak povolit protokolování úložiště a další.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: a5d1d6af68fcbd6a5822b2652ee79c464d02241f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200765"
---
# <a name="azure-storage-analytics-logging"></a>Protokolování analýz služby Azure Storage

Analýza úložiště protokoluje podrobné informace o úspěšných a neúspěšných požadavcích na službu úložiště. Tyto informace je možné použít k monitorování jednotlivých požadavků a diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí. To znamená, že většina požadavků bude mít za následek záznam protokolu, ale úplnost a časová osa Analýza úložištěch protokolů nebudou zaručeny. 

> [!NOTE]
> Místo protokolů Analýza úložiště doporučujeme používat protokoly Azure Storage v Azure Monitor. Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek. Další informace najdete v následujících článcích:
>
> - [Monitorování Blob Storage Azure](../blobs/monitor-blob-storage.md)
> - [Monitorování souborů Azure](../files/storage-files-monitoring.md)
> - [Monitorování Queue Storage Azure](../queues/monitor-queue-storage.md)
> - [Monitorování úložiště tabulek v Azure](../tables/monitor-table-storage.md)

 Protokolování Analýzy úložiště ve výchozím nastavení není pro váš účet úložiště povolené. Můžete ji povolit v [Azure Portal](https://portal.azure.com/) nebo pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI. Podrobné pokyny najdete v tématu [povolení a správa protokolů analýza úložiště Azure (Classic)](manage-storage-analytics-logs.md). 

Protokoly Analýza úložiště můžete také povolit programově prostřednictvím REST API nebo klientské knihovny. K povolení Analýza úložiště pro každou službu použijte vlastnosti [získat službu BLOB](/rest/api/storageservices/Blob-Service-REST-API)Service, [získat vlastnosti služby front](/rest/api/storageservices/Get-Queue-Service-Properties)a [získat operace vlastností služby Table](/rest/api/storageservices/Get-Table-Service-Properties) Service. Pokud chcete zobrazit příklad, který umožňuje protokoly Analýza úložiště pomocí rozhraní .NET, přečtěte si téma [Povolení protokolů](manage-storage-analytics-logs.md)

 Položky protokolu jsou vytvořeny pouze v případě, že jsou zadány požadavky na koncový bod služby. Pokud například účet úložiště obsahuje aktivitu v koncovém bodu objektu blob, ale ne ve svých koncových bodech tabulky nebo fronty, vytvoří se jenom protokoly týkající se Blob service.

> [!NOTE]
>  Protokolování Analýzy úložiště je v současné době dostupné pouze pro služby Blob a Table service a Službu front. K dispozici je také protokolování Analýza úložiště pro účty Premium-Performance [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) . Není ale k dispozici pro účty pro obecné účely v2 s výkonem Premium.

## <a name="requests-logged-in-logging"></a>Požadavky zaznamenávané v protokolování
### <a name="logging-authenticated-requests"></a>Protokolování ověřených požadavků

 Protokolují se následující typy ověřovaných požadavků:

- Úspěšné požadavky
- Neúspěšné požadavky, včetně vypršení časového limitu, omezování, chyb sítě, selhání autorizace a dalších chyb
- Požadavky používající SAS (Shared Access Signature) nebo OAuth (Open Authorization), včetně neúspěšných i úspěšných požadavků
- Žádosti o analytická data

  Požadavky vytvořené Analýza úložiště samotné, například vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat je popsán v tématech [Analýza úložiště protokolovaných operací a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Analýza úložiště formátu protokolu](/rest/api/storageservices/storage-analytics-log-format) .

### <a name="logging-anonymous-requests"></a>Protokolování anonymních požadavků

 Protokolují se tyto typy anonymních požadavků:

- Úspěšné požadavky
- Chyby serveru
- Chyby časového limitu pro klienta i server
- Neúspěšné požadavky GET s kódem chyby 304 (Nezměněno)

  Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat je popsán v tématech [Analýza úložiště protokolovaných operací a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Analýza úložiště formátu protokolu](/rest/api/storageservices/storage-analytics-log-format) .

## <a name="how-logs-are-stored"></a>Způsob ukládání protokolů

Všechny protokoly se ukládají v objektech blob bloku v kontejneru s názvem `$logs` , který se automaticky vytvoří, když je povolený analýza úložiště pro účet úložiště. `$logs`Kontejner se nachází v oboru názvů objektu BLOB účtu úložiště, například: `http://<accountname>.blob.core.windows.net/$logs` . Po povolení Analýza úložiště nelze tento kontejner odstranit, i když jeho obsah lze odstranit. Pokud použijete nástroj pro procházení úložiště k přímému přechodu do kontejneru, zobrazí se všechny objekty blob, které obsahují vaše data protokolování.

> [!NOTE]
>  `$logs`Kontejner se nezobrazuje, když je provedena operace výpisu kontejneru, jako je například operace Container lists. K němu je potřeba získat přímý pøístup. Můžete například použít operaci list BLOBs pro přístup k objektům blob v `$logs` kontejneru.

Jak jsou požadavky protokolovány, Analýza úložiště odešle mezilehlé výsledky jako bloky. Analýza úložiště tyto bloky pravidelně zařadí a zpřístupní je jako objekt BLOB. Může trvat až hodinu, než se data protokolu objeví v objektech blob v kontejneru **$logs** , protože frekvence, s jakou služba úložiště vyprázdní zapisovače protokolů. Pro protokoly vytvořené ve stejnou hodinu můžou existovat duplicitní záznamy. Zaškrtnutím čísla **ID** a **operace** můžete určit, jestli je záznam duplicitní.

Pokud máte velké množství dat protokolu s více soubory pro každou hodinu, pak můžete použít metadata objektu BLOB k určení dat, která protokol obsahuje, prozkoumáním polí metadat objektů BLOB. To je užitečné také v případě, že při zápisu dat do souborů protokolu může dojít k prodlevě: metadata objektů BLOB poskytují přesnější indikaci obsahu objektu blob, než je název objektu BLOB.

Většina nástrojů pro procházení úložiště vám umožní zobrazit metadata objektů BLOB. Tyto informace můžete přečíst také pomocí PowerShellu nebo prostřednictvím kódu programu. Následující fragment kódu prostředí PowerShell představuje příklad filtrování seznamu objektů BLOB protokolu podle názvu pro určení času a metadaty k identifikaci pouze těch protokolů, které obsahují operace **zápisu** .  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Informace o tom, jak programově vypsat objekty blob, najdete v tématech [vytváření výčtu prostředků objektů BLOB](/rest/api/storageservices/Enumerating-Blob-Resources) a [nastavení a načítání vlastností a metadat pro prostředky objektů BLOB](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources).  

### <a name="log-naming-conventions"></a>Zásady vytváření názvů protokolů

 Každý protokol bude napsaný v následujícím formátu:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 V následující tabulce jsou popsány jednotlivé atributy v názvu protokolu:

|Atribut|Popis|
|---------------|-----------------|
|`<service-name>`|Název služby úložiště Například: `blob` , `table` , nebo `queue`|
|`YYYY`|Rok čtyř číslice pro protokol. Příklad: `2011`|
|`MM`|Dva číslice měsíce pro protokol. Příklad: `07`|
|`DD`|Dva číselné dny pro protokol. Příklad: `31`|
|`hh`|Dvě číslice hodiny, která označuje počáteční hodinu pro protokoly ve formátu UTC (24 hodin). Příklad: `18`|
|`mm`|Dva číselné číslo, které označuje počáteční minutu pro protokoly. **Poznámka:**  Tato hodnota není v aktuální verzi Analýza úložiště podporována a její hodnota bude vždy `00` .|
|`<counter>`|Čítač založený na nule se šesti číslicemi, který indikuje počet objektů BLOB protokolu generovaných pro službu úložiště v časovém intervalu. Tento čítač začíná na `000000` . Příklad: `000001`|

 Následuje úplný název ukázkového protokolu, který kombinuje výše uvedené příklady:

 `blob/2011/07/31/1800/000001.log`

 Následuje ukázkový identifikátor URI, který lze použít pro přístup k výše uvedenému protokolu:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Po zaznamenání žádosti o úložiště se výsledný název protokolu koreluje do hodiny, kdy se požadovaná operace dokončila. Pokud se například požadavek getblob dokončil v 6:17:30 v 7/31/2011, protokol se zapíše s následující předponou: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadata protokolu

 Všechny objekty blob protokolu jsou uložené s metadaty, která se dají použít k identifikaci dat protokolování, která objekt BLOB obsahuje. Následující tabulka popisuje jednotlivé atributy metadat:

|Atribut|Popis|
|---------------|-----------------|
|`LogType`|Popisuje, jestli protokol obsahuje informace týkající se operací čtení, zápisu nebo odstranění. Tato hodnota může obsahovat jeden typ nebo kombinaci všech tří, oddělených čárkami.<br /><br /> Příklad 1: `write`<br /><br /> Příklad 2: `read,write`<br /><br /> Příklad 3: `read,write,delete`|
|`StartTime`|Čas nejdřívějšího záznamu v protokolu ve formě `YYYY-MM-DDThh:mm:ssZ` . Příklad: `2011-07-31T18:21:46Z`|
|`EndTime`|Poslední čas záznamu v protokolu ve formě `YYYY-MM-DDThh:mm:ssZ` . Příklad: `2011-07-31T18:22:09Z`|
|`LogVersion`|Verze formátu protokolu|

 Následující seznam obsahuje kompletní ukázková metadata pomocí výše uvedených příkladů:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>Další kroky

* [Povolit a spravovat protokoly Analýza úložiště Azure (klasické)](manage-storage-analytics-logs.md)
* [Formát protokolu Analýza úložiště](/rest/api/storageservices/storage-analytics-log-format)
* [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Analýza úložiště metriky (klasické)](storage-analytics-metrics.md)
