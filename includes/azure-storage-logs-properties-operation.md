---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011127"
---
| Vlastnost | Popis |
|:--- |:---|
|**interval** | Světový čas koordinovaný (UTC), kdy se požadavek přijal úložištěm. Příklad: `2018/11/08 21:09:36.6900118`.|
|**Prostředku** | ID prostředku účtu úložiště. Příklad: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**kategorií** | Kategorie požadované operace. Například: `StorageRead` , `StorageWrite` , nebo `StorageDelete` .|
|**operationName** | Typ operace REST, která byla provedena. <br> Úplný seznam operací najdete v tématu [Analýza úložiště protokolovaných operací a zpráv o stavu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Verze služby úložiště, která byla určena při podání žádosti. To je ekvivalentní hodnotě v hlavičce **x-MS-Version** . Příklad: `2017-04-17`.|
|**schemaVersion** | Verze schématu protokolu Příklad: `1.0`.|
|**statusCode** | Stavový kód protokolu HTTP pro požadavek. Pokud je požadavek přerušený, může být tato hodnota nastavena na `Unknown` . <br> Příklad: `206` |
|**statusText** | Stav požadované operace.  Úplný seznam stavových zpráv najdete v tématu [Analýza úložiště protokolovaných operací a zpráv o stavu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Ve verzi 2017-04-17 a novější se stavová zpráva `ClientOtherError` nepoužívá. Místo toho obsahuje toto pole kód chyby. Příklad: `SASSuccess`  |
|**Trvání v MS** | Celková doba, vyjádřená v milisekundách k provedení požadované operace. To zahrnuje čas ke čtení příchozího požadavku a k odeslání odpovědi žadateli. Příklad: `12`.|
|**callerIpAddress** | IP adresa žadatele včetně čísla portu. Příklad: `192.100.0.102:4362`. |
|**ID** | ID, které se používá ke korelaci protokolů napříč prostředky. Příklad: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**oblasti** | Umístění účtu úložiště. Příklad: `North Europe`. |
|**protokol**|Protokol, který se používá v operaci. Například: `HTTP` , `HTTPS` , `SMB` nebo `NFS`|
| **identifikátor URI** | Požadovaný identifikátor Uniform Resource. |