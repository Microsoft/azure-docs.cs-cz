---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8a596293a5c1572b30ea6101dad16328c8db2634
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166132"
---
## <a name="what-is-queue-storage"></a>Co je služba Queue Storage?
Azure Queue Storage je služba pro ukládání velkého počtu zpráv, ke které můžete získat přístup z jakéhokoli místa na světě prostřednictvím ověřených volání s využitím protokolu HTTP nebo HTTPS. Zpráva s jednou frontou může mít velikost až 64 kB a jedna fronta můžete obsahovat miliony zpráv, až do dosažení celkové kapacity účtu úložiště.

Toto jsou některá běžná použití úložiště služby Queue Storage:

* Vytvoření backlogu práce k asynchronnímu zpracování
* Předávání zpráv z webové role Azure do role pracovního procesu Azure

## <a name="queue-service-concepts"></a>Koncepty služby front
Služba front obsahuje následující součásti:

![Fronta1](./media/storage-queue-concepts-include/queue1.png)

* **Formát adresy URL:** Fronty jsou adresovatelné v následujícím formátu adresy URL:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Následující adresa URL odkazuje na frontu v diagramu:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Účet úložiště:** Veškerý přístup k úložišti Azure se provádí prostřednictvím účtu úložiště. Podrobné informace o kapacitě účtu úložiště najdete v článku [Škálovatelnost a cíle výkonnosti úložiště Azure](../articles/storage/common/storage-scalability-targets.md).
* **Fronta:** Fronta obsahuje sadu zpráv. Všechny zprávy musí být ve frontě. Upozorňujeme, že název fronty musí být psaný malými písmeny. Informace o pojmenování front najdete v tématu [Pojmenování front a metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Zpráva:** Zprávu v libovolném formátu o velikosti až 64 kB. Maximální doba, po kterou může zpráva zůstat ve frontě, je 7 dní.

