---
title: Zjistit chyby úlohy a úlohy – Azure Batch | Microsoft Docs
description: Chyby při kontrole úloh a úloh pro řešení potíží
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087005"
---
# <a name="job-and-task-error-checking"></a>Kontrola chyb úlohy a úlohy

Existují různé chyby, ke kterým může dojít při přidávání úloh a úkolů. Zjištění selhání pro tyto operace je jednoduché, protože jakékoli chyby jsou vráceny hned rozhraním API, CLI nebo UI.  Existují však chyby, ke kterým může dojít později při plánování a spouštění úloh a úloh.

Tento článek se zabývá chybami, ke kterým může dojít po odeslání úloh a úkolů. Zobrazuje seznam a vysvětluje chyby, které je nutné zkontrolovat a zpracovat.

## <a name="jobs"></a>Úlohy

Úloha je seskupení jedné nebo více úkolů, což jsou úlohy, které ve skutečnosti určují příkazové řádky, které mají být spuštěny.

Při přidávání úlohy je možné zadat následující parametry, které mohou mít vliv na to, jak může úloha selhat:

- [Omezení úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - Vlastnost `maxWallClockTime` lze volitelně zadat pro nastavení maximální doby, po kterou může být úloha aktivní nebo spuštěná. V případě překročení bude úloha ukončena s vlastností `terminateReason` nastavenou v [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) pro úlohu.
- [Úkol přípravy úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Je-li tento parametr zadán, spustí se úkol přípravy úlohy při prvním spuštění úlohy pro úlohu na uzlu. Úkol přípravy úlohy může selhat, což vede k nespuštěnému úkolu a úloha se nedokončuje.
- [Úkol uvolnění úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Úkol uvolnění úlohy se dá zadat jenom v případě, že je nakonfigurovaný úkol přípravy úlohy. Po ukončení úlohy se úkol uvolnění úlohy spustí na všech uzlech fondu, kde se spustil úkol přípravy úlohy. Úkol uvolnění úlohy může selhat, ale úloha zůstane přesunuta do stavu `completed`.

### <a name="job-properties"></a>Vlastnosti úlohy

U následujících vlastností úlohy je nutné zkontrolovat chyby:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - Vlastnost `terminateReason` může mít hodnoty, které označují, že došlo k překročení `maxWallClockTime`zadané v omezeních úlohy, a proto byla úloha ukončena. Můžete ji také nastavit tak, aby označovala, že se úloha nezdařila, pokud byla vlastnost `onTaskFailure` úlohy nastavena správně.
  - Vlastnost [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) je nastavena, pokud došlo k chybě plánování.
 
### <a name="job-preparation-tasks"></a>Úkoly přípravy úlohy

Pokud je pro úlohu určen úkol přípravy úlohy, pak se instance této úlohy spustí při prvním spuštění úlohy pro úlohu na uzlu. Úkol přípravy úlohy nakonfigurovaný v úloze si můžete představit jako šablonu úkolu s více spuštěnými instancemi úlohy přípravy úlohy, a to až do počtu uzlů ve fondu.

Aby bylo možné zjistit, zda došlo k chybám, je třeba zkontrolovat instance úloh přípravy úlohy:
- Po spuštění úlohy přípravy úlohy se úloha, která aktivovala úlohu přípravy úlohy, přesune do [stavu](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) `preparing`; Pokud úkol přípravy úlohy selže, aktivační úloha se vrátí do stavu `active` a nebude spuštěna.  
- Všechny instance úlohy přípravy úlohy, které byly spuštěny, lze získat z úlohy pomocí rozhraní API pro [přípravu seznamu a stav úlohy uvolnění](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Stejně jako u všech úkolů jsou k dispozici [informace o spuštění](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) s vlastnostmi, jako jsou `failureInfo`, `exitCode`a `result`.
- Pokud úkoly přípravy úlohy selžou, úlohy aktivace úloh se nespustí, úloha nebude dokončena a bude zablokována. Fond může být nevyužitý, pokud neexistují žádné další úlohy s úkoly, které je možné naplánovat.

### <a name="job-release-tasks"></a>Úkoly uvolnění úloh

Pokud je pro úlohu určena úloha uvolnění úlohy, pak při ukončení úlohy se instance úlohy uvolnění úlohy spustí na všech uzlech fondu, kde se spustil úkol přípravy úlohy.  Aby bylo možné zjistit, zda došlo k chybám, je třeba zkontrolovat instance úloh uvolnění úloh.
- Všechny instance spouštěného úkolu uvolnění úlohy je možné získat z úlohy pomocí funkce [vypsat seznam rozhraní API a stavu úlohy vydání](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Stejně jako u všech úkolů jsou k dispozici [informace o spuštění](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) s vlastnostmi, jako jsou `failureInfo`, `exitCode`a `result`.
- Pokud jedna nebo více úloh vydání úlohy selže, bude úloha stále ukončena a bude přesunuta do stavu `completed`.

## <a name="tasks"></a>Úlohy

Úlohy úlohy můžou selhat z několika důvodů:

- Příkazový řádek úlohy se nezdařil a vrátí nenulový ukončovací kód.
- Pro úlohu jsou zadány `resourceFiles`, ale došlo k chybě, která se nestáhla z jednoho nebo více souborů.
- Pro úlohu jsou zadány `outputFiles`, ale došlo k chybě, která způsobila, že jeden nebo více souborů nebylo odesláno.
- Překročil se uplynulý čas pro úlohu, která je určená vlastností `maxWallClockTime` v [omezeních](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)úlohy.

Ve všech případech je nutné zkontrolovat chyby a informace o chybách v následujících vlastnostech:
- Vlastnost Tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) obsahuje více vlastností, které poskytují informace o chybě. [výsledek](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) určuje, jestli se úloha z nějakého důvodu nezdařila, `exitCode` a `failureInfo` o selhání.
- Úloha se vždycky přesune do [stavu](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)`completed`, nezávisle na tom, jestli byla úspěšná nebo neúspěšná.

Je třeba vzít v úvahu dopad selhání úkolů na úlohu a jakékoli závislosti úkolů.  Vlastnost [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) lze zadat pro úkol ke konfiguraci akce pro závislosti a pro úlohu.
- Pro závislosti [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) určuje, jestli jsou úlohy závislé na neúspěšném úkolu blokované nebo spuštěné.
- V případě úlohy [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) určuje, jestli neúspěšná úloha vede k vypínání, ukončení nebo ponechání úlohy beze změny.

### <a name="task-command-line-failures"></a>Selhání příkazového řádku úlohy

Po spuštění příkazového řádku úlohy se výstup zapíše do `stderr.txt` a `stdout.txt`. Kromě toho aplikace může zapisovat do souborů protokolu specifických pro aplikace.

Pokud uzel fondu, na kterém je úloha spuštěná, stále existuje, můžete soubory protokolu získat a zobrazit. Například Azure Portal seznamy a mohou zobrazovat soubory protokolu pro úlohu nebo uzel fondu. Více rozhraní API také umožňuje vypsat a získat soubory úloh, například [získat z úlohy](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Vzhledem k tomu, že se často dodávají fondy a uzly fondu, se uzly neustále přidávají a odstraňují, doporučuje se, aby byly soubory protokolu trvalé. [Výstupní soubory úlohy](https://docs.microsoft.com/azure/batch/batch-task-output-files) představují pohodlný způsob, jak ukládat soubory protokolu do Azure Storage.

### <a name="output-file-failures"></a>Selhání výstupního souboru
Při každém nahrání souboru Batch zapisuje do výpočetního uzlu dva soubory protokolu `fileuploadout.txt` a `fileuploaderr.txt`. Můžete si prohlédnout tyto soubory protokolu a získat další informace o konkrétní chybě. V případech, kdy se odeslání souboru nikdy nepokoušelo, například kvůli tomu, že se nezdařilo spustit samotný úkol, tyto soubory protokolu nebudou existovat.  

## <a name="next-steps"></a>Další kroky

Ověřte, že vaše aplikace implementuje komplexní kontrolu chyb. může být zásadní, aby mohl rychle detekovat a diagnostikovat problémy.
