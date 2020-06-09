---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80151149"
---
## <a name="what-is-queue-storage"></a>Co je Queue Storage?

Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště. Fronta úložiště se často používá k vytvoření nevyřízených položek pro asynchronní zpracování.

## <a name="queue-service-concepts"></a>Služba front koncepty

Služba front Azure obsahuje následující komponenty:

![Součásti Azure Služba front](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL: http:// `<storage account>` . Queue.Core.Windows.NET/`<queue>`
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Účet úložiště:** Veškerý přístup ke službě Azure Storage se provádí prostřednictvím účtu úložiště. Další informace o účtech úložiště najdete v tématu [Přehled účtu úložiště](../articles/storage/common/storage-account-overview.md).
* **Fronta:** Fronta obsahuje sadu zpráv. Všechny zprávy musí být ve frontě. Upozorňujeme, že název fronty musí být psaný malými písmeny. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Zpráva:** Zprávu v libovolném formátu o velikosti až 64 kB. Maximální doba, po kterou může zpráva zůstat ve frontě, je 7 dní. V případě verze 2017-07-29 nebo novější může být maximální doba do živého kladného čísla nebo-1, což znamená, že platnost zprávy nevyprší. Pokud je tento parametr vynechán, výchozí hodnota TTL (Time to Live) je sedm dní.

