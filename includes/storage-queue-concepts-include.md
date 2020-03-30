---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80151149"
---
## <a name="what-is-queue-storage"></a>Co je úložiště fronty?

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště. Úložiště front se často používá k vytvoření nevyřízených položek práce pro zpracování asynchronně.

## <a name="queue-service-concepts"></a>Koncepty služby fronty

Služba Azure Queue obsahuje následující součásti:

![Součásti služby Azure Queue](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Formát adresy URL:** Fronty lze adresovat pomocí následujícího`<storage account>`formátu adresy URL: http:// .queue.core.windows.net/`<queue>`
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Účet úložiště:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Další informace o účtech úložiště najdete v [tématu Přehled účtu úložiště](../articles/storage/common/storage-account-overview.md).
* **Fronta:** Fronta obsahuje sadu zpráv. Všechny zprávy musí být ve frontě. Upozorňujeme, že název fronty musí být psaný malými písmeny. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Zpráva:** Zprávu v libovolném formátu o velikosti až 64 kB. Maximální doba, po kterou může zpráva zůstat ve frontě, je 7 dní. Pro verzi 2017-07-29 nebo novější maximální doba aktivní může být libovolné kladné číslo nebo -1 označující, že platnost zprávy nevyprší. Pokud je tento parametr vynechán, výchozí doba aktivní je sedm dní.

