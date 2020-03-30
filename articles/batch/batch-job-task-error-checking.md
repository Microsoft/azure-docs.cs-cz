---
title: Kontrola chyb úloh a úloh – Azure Batch | Dokumenty společnosti Microsoft
description: Chyby při kontrole a odstraňování úloh a úkolů při odstraňování problémů
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087005"
---
# <a name="job-and-task-error-checking"></a>Kontrola chyb úloh a úloh

Existují různé chyby, které mohou nastat při přidávání úloh a úkolů. Zjišťování chyb pro tyto operace je jednoduché, protože všechny chyby jsou vráceny okamžitě rozhraní API, rozhraní CLI nebo ui.  Existují však chyby, které se mohou stát později při plánování a spuštění úloh a úloh.

Tento článek popisuje chyby, které mohou nastat po odeslání úloh a úkolů. Uvádí a vysvětluje chyby, které je třeba zkontrolovat a zpracovat.

## <a name="jobs"></a>Úlohy

Úloha je seskupení jednoho nebo více úkolů, úkoly ve skutečnosti určují příkazové řádky, které mají být spuštěny.

Při přidávání úlohy lze zadat následující parametry, které mohou ovlivnit, jak může úloha selhat:

- [Omezení úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - Vlastnost `maxWallClockTime` může být volitelně určena pro nastavení maximální doby, po kterou může být úloha aktivní nebo spuštěná. Pokud je překročena, úloha bude `terminateReason` ukončena s vlastností nastavenou v [exekuciInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) pro úlohu.
- [Úloha přípravy úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Pokud je zadán, úloha přípravy úlohy je spuštěna při prvním spuštění úlohy pro úlohu v uzlu. Úloha přípravy úlohy může selhat, což povede k nespuštění úlohy a k nedokončení úlohy.
- [Úloha uvolnění úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Úlohu uvolnění úlohy lze zadat pouze v případě, že je nakonfigurována úloha přípravy úlohy. Při ukončení úlohy je úloha uvolnění úlohy spuštěna na každém uzle fondu, kde byla spuštěna úloha přípravy úlohy. Úloha uvolnění úlohy může selhat, `completed` ale úloha se bude stále přesouvat do stavu.

### <a name="job-properties"></a>Vlastnosti úlohy

Následující vlastnosti úlohy by měly být kontrolovány na chyby:

- '[exekuceInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - Vlastnost `terminateReason` může mít hodnoty `maxWallClockTime`označující, že , zadaná v omezení úlohy, byla překročena, a proto byla úloha ukončena. Lze také nastavit, aby bylo možné `onTaskFailure` označit, že úloha se nezdařila, pokud byla vlastnost úlohy nastavena správně.
  - Vlastnost [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) je nastavena, pokud došlo k chybě plánování.
 
### <a name="job-preparation-tasks"></a>Úkoly přípravy práce

Pokud je pro úlohu určena úloha přípravy úlohy, bude instance této úlohy spuštěna při prvním spuštění úlohy na uzlu. Úlohu přípravy úlohnakovanou v úloze lze považovat za šablonu úlohy, přičemž je spuštěno více instancí úloh přípravy úloh, a to až do počtu uzlů ve fondu.

Instance úloh přípravy úloh y úlohy úlohy by měly být zkontrolovány, aby se zjistilo, zda došlo k chybám:
- Při spuštění úlohy přípravy úlohy se úloha, která [state](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) spustila `preparing`úlohu přípravy úlohy, přesune do stavu ; Pokud úloha přípravy úlohy selže, aktivační úloha se vrátí do `active` stavu a nebude spuštěna.  
- Všechny instance úlohy přípravy úlohy, které byly spuštěny lze získat z úlohy pomocí [seznam příprava a uvolnění úlohy stav](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) rozhraní API. Stejně jako u všech úkolů jsou k `failureInfo` `exitCode`dispozici `result` [informace o spuštění](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) s vlastnostmi, jako jsou například , a .
- Pokud úlohy přípravy úloh nezdaří, nebudou spuštěny úlohy spouštěcí úlohy, úloha nebude dokončena a bude zablokovaná. Fond může přejít nevyužité, pokud neexistují žádné jiné úlohy s úkoly, které lze naplánovat.

### <a name="job-release-tasks"></a>Úkoly uvolnění úlohy

Pokud je pro úlohu určena úloha uvolnění úlohy, pak při ukončení úlohy je spuštěna instance úlohy uvolnění úlohy na každém uzlech fondu, kde byla spuštěna úloha přípravy úlohy.  Instance úlohy uvolnění úlohy úlohy by měly být kontrolovány, aby se zjistilo, zda došlo k chybám:
- Všechny instance úlohy uvolnění úlohy lze získat z úlohy pomocí seznamu rozhraní API [příprava a uvolnění stavu úlohy](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Stejně jako u všech úkolů jsou k `failureInfo` `exitCode`dispozici `result` [informace o spuštění](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) s vlastnostmi, jako jsou například , a .
- Pokud jeden nebo více úloh uvolnění úlohy nezdaří, pak `completed` úloha bude stále ukončena a přesunout do stavu.

## <a name="tasks"></a>Úlohy

Úlohy úloh mohou selhat z několika důvodů:

- Příkazový řádek úkolu se nezdaří a vrátí se s nenulovým ukončovacím kódem.
- Pro `resourceFiles` úlohu jsou určeny, ale došlo k chybě, která znamenala, že jeden nebo více souborů nebylo staženo.
- Pro `outputFiles` úlohu jsou určeny, ale došlo k chybě, která znamenala, že jeden nebo více souborů nebylo nahráno.
- Uplynulý čas úkolu určený `maxWallClockTime` vlastností v [omezeních](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)úkolu byl překročen.

Ve všech případech je nutné zkontrolovat následující vlastnosti, zda nezasazují o chyby a informace o chybách:
- Vlastnost [tasks executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) obsahuje více vlastností, které poskytují informace o chybě. [výsledek](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) označuje, zda se úloha `exitCode` `failureInfo` z nějakého důvodu nezdařila, a poskytuje další informace o selhání.
- Úkol se vždy přesune `completed` do [stavu](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate), nezávisle na tom, zda byl úspěšný nebo neúspěšný.

Je třeba vzít v úvahu dopad selhání úloh na úlohu a všechny závislosti mezi úkoly.  Vlastnost [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) může být určena pro úlohu pro konfiguraci akce pro závislosti a pro úlohu.
- U závislostí určuje, zda jsou [úkoly](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) závislé na neúspěšném úkolu blokovány nebo spuštěny.
- Pro úlohu [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) určuje, zda neúspěšný úkol vede k práci, která je zakázána, ukončena nebo ponechána beze změny.

### <a name="task-command-line-failures"></a>Selhání příkazového řádku úkolu

Při spuštění příkazového řádku úlohy `stderr.txt` je `stdout.txt`výstup zapsán do a . Kromě toho aplikace může zapisovat do souborů protokolu specifické pro aplikaci.

Pokud uzel fondu, na kterém byla úloha spuštěna, stále existuje, lze získat a zobrazit soubory protokolu. Například portál Azure uvádí a můžete zobrazit soubory protokolu pro úlohu nebo uzel fondu. Více souborů API také umožňují, aby byly uvedeny a získány soubory úloh, například [Získat z úlohy](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Vzhledem k fondy a uzly fondu často dočasné, s uzly jsou průběžně přidávány a mazány, pak se doporučuje, aby soubory protokolu jsou trvalé. [Výstupní soubory úloh](https://docs.microsoft.com/azure/batch/batch-task-output-files) jsou pohodlný způsob ukládání souborů protokolu do služby Azure Storage.

### <a name="output-file-failures"></a>Selhání výstupních souborů
Při každém nahrání souboru Batch zapíše dva `fileuploadout.txt` `fileuploaderr.txt`soubory protokolu do výpočetního uzlu a . Můžete prozkoumat tyto soubory protokolu další informace o konkrétní selhání. V případech, kdy se o nahrání souboru nikdy nepokusili, například proto, že samotná úloha nemohla být spuštěna, tyto soubory protokolu nebudou existovat.  

## <a name="next-steps"></a>Další kroky

Zkontrolujte, zda aplikace implementuje komplexní kontrolu chyb; může být důležité okamžitě zjistit a diagnostikovat problémy.
