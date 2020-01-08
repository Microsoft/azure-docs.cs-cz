---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 59dd5d38b1cb89ce966a74284f0e392af52e9827
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457689"
---
## <a name="what-is-queue-storage"></a>Co je Queue Storage?

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště. Fronta úložiště se často používá k vytvoření nevyřízených položek pro asynchronní zpracování.

## <a name="queue-service-concepts"></a>Služba front koncepty

Služba front Azure obsahuje následující komponenty:

![Součásti Azure Služba front](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL: http://`<storage account>`. queue.core.windows.net/`<queue>`
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Účet úložiště:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Další informace o účtech úložiště najdete v tématu [přehled účtu úložiště] [.. /articles/storage/common/storage-account-overview.md].
* **Fronta:** Fronta obsahuje sadu zpráv. Všechny zprávy musí být ve frontě. Upozorňujeme, že název fronty musí být psaný malými písmeny. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Zpráva:** Zprávu v libovolném formátu o velikosti až 64 kB. Maximální doba, po kterou může zpráva zůstat ve frontě, je 7 dní. V případě verze 2017-07-29 nebo novější může být maximální doba do živého kladného čísla nebo-1, což znamená, že platnost zprávy nevyprší. Pokud je tento parametr vynechán, výchozí hodnota TTL (Time to Live) je sedm dní.

