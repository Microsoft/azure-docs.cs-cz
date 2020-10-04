---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 0e23e537043664929bdc1a3636de359953b66db6
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711260"
---
| Vlastnost | Popis |
|:--- |:---|
|**interval** | Světový čas koordinovaný (UTC), kdy se požadavek přijal úložištěm. Například: `2018/11/08 21:09:36.6900118`.|
|**Prostředku** | ID prostředku účtu úložiště. Příklad: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**kategorií** | Kategorie požadované operace. Například: `StorageRead` , `StorageWrite` , nebo `StorageDelete` .|
|**operationName** | Typ operace REST, která byla provedena. <br> Úplný seznam operací najdete v tématu [Analýza úložiště protokolovaných operací a zpráv o stavu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Verze služby úložiště, která byla určena při podání žádosti. To je ekvivalentní hodnotě v hlavičce **x-MS-Version** . Například: `2017-04-17`.|
|**schemaVersion** | Verze schématu protokolu Například: `1.0`.|
|**statusCode** | Stavový kód protokolu HTTP pro požadavek. Pokud je požadavek přerušený, může být tato hodnota nastavena na `Unknown` . <br> Příklad: `206` |
|**statusText** | Stav požadované operace.  Úplný seznam stavových zpráv najdete v tématu [Analýza úložiště protokolovaných operací a zpráv o stavu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Ve verzi 2017-04-17 a novější se stavová zpráva `ClientOtherError` nepoužívá. Místo toho obsahuje toto pole kód chyby. Příklad: `SASSuccess`  |
|**Trvání v MS** | Celková doba, vyjádřená v milisekundách k provedení požadované operace. To zahrnuje čas ke čtení příchozího požadavku a k odeslání odpovědi žadateli. Například: `12`.|
|**callerIpAddress** | IP adresa žadatele včetně čísla portu. Například: `192.100.0.102:4362`. |
|**ID** | ID, které se používá ke korelaci protokolů napříč prostředky. Například: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**oblasti** | Umístění účtu úložiště. Například: `North Europe`. |
|**protokol**|Protokol, který se používá v operaci. Například: `HTTP` , `HTTPS` , `SMB` nebo `NFS`|
| **identifikátor URI** | Požadovaný identifikátor Uniform Resource. |