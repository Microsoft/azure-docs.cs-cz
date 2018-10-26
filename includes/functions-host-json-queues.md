---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 7139b88ee9ef137ca28484538262b8bb2fe804db
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134303"
---
```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Vlastnost  |Výchozí | Popis |
|---------|---------|---------| 
|maxPollingInterval|60000|Maximální interval v milisekundách mezi dotazuje fronty.| 
|visibilityTimeout|0|Časový interval mezi opakovanými pokusy při zpracování zprávy se nezdaří.| 
|batchSize|16|Počet zpráv fronty, které modul runtime služby Functions současně načte a zpracuje paralelně. Jakmile číslo zpracovává přejdete dolů k `newBatchThreshold`, modul runtime získá další dávku a spustí zpracování zprávy. Maximální počet souběžných za funkce zpracování zprávy je `batchSize` plus `newBatchThreshold`. Toto omezení platí zvlášť pro každou funkci aktivovanou protokolem fronty. <br><br>Pokud chcete se vyhnout paralelní provádění pro zprávy přijaté pro jednu frontu, můžete nastavit `batchSize` na hodnotu 1. Toto nastavení však eliminuje souběžnosti, pouze tak dlouho, dokud vaše aplikace function app běží na jeden virtuální počítač (VM). Pokud aplikace function app škálovat do několika virtuálních počítačů, každý virtuální počítač může spustit jednu instanci každé funkce aktivované triggerem queue.<br><br>Maximální počet `batchSize` je 32. | 
|maxDequeueCount|5|Počet pokusů, zpracovává zprávu před přesunutím do poškozené fronty.| 
|newBatchThreshold|batchSize/2|Pokaždé, když se počet zpráv souběžně zpracováváno získá na toto číslo, modul runtime načte jiná dávka.| 



