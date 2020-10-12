---
title: Zpracování chyb a detekce v Azure Batch
description: Přečtěte si o zpracování chyb v pracovních postupech služby Batch z hlediska vývoje.
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964273"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Zpracování chyb a detekce v Azure Batch

V některých případech se může stát, že bude nutné, abyste v rámci řešení Batch mohli zpracovávat chyby úloh i aplikací. Tento článek pojednává o typech chyb a o tom, jak je vyřešit.

## <a name="error-codes"></a>Kódy chyb

Mezi obecné typy chyb patří:

- Selhání sítě pro žádosti, které nikdy nedorazily do dávky, nebo když se odpověď dávky nedostala klientovi v čase.
- Interní chyby serveru (standardní 5xx stavový kód odpovědi HTTP).
- Chyby související s omezením, jako jsou například 429 nebo 503 odpovědi HTTP na stavový kód s hlavičkou Retry-After.
- 4xx chyby, jako je například už existuje a InvalidOperation. To znamená, že prostředek není ve správném stavu pro přechod stavu.

Podrobné informace o konkrétních kódech chyb, včetně kódů chyb pro REST API, službě Batch a úlohy/plánování úloh, najdete v tématu [stav služby Batch a kódy chyb](/rest/api/batchservice/batch-status-and-error-codes).

## <a name="application-failures"></a>Selhání aplikace

Během provádění může aplikace generovat diagnostický výstup, který lze použít k řešení potíží. Jak je popsáno v části [soubory a adresáře](files-and-directories.md), služba Batch zapisuje standardní výstup a standardní chyby `stdout.txt` do `stderr.txt` souborů v adresáři úkolů na výpočetním uzlu.

Tyto soubory můžete stáhnout pomocí webu Azure Portal nebo jedné ze sad SDK služby Batch. Tyto a další soubory můžete pro účely odstraňování potíží načíst například v knihovně Batch .NET pomocí metod [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) a [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask).

## <a name="task-errors"></a>Chyby úlohy

Chyby úloh spadají do několika kategorií.

### <a name="pre-processing-errors"></a>Chyby předběžného zpracování

Pokud úkol se nepodaří spustit, nastaví se pro něj chyba předběžného zpracování.  

Pokud se přesunuly soubory prostředků úlohy, může dojít k chybám předběžného zpracování, protože účet úložiště už není dostupný, nebo došlo k jinému problému, který zabránil úspěšnému zkopírování souborů do uzlu.

### <a name="file-upload-errors"></a>Chyby nahrávání souborů

Pokud se soubory zadané pro úlohu z jakéhokoli důvodu nepodaří nahrát, pro úlohu se nastaví chyba nahrávání souboru.

K chybám nahrávání souborů může dojít, pokud podpis SAS zadaný pro přístup k Azure Storage je neplatný nebo neposkytuje oprávnění k zápisu, pokud účet úložiště už není dostupný nebo pokud byl došlo k jinému problému, který zabránil úspěšnému zkopírování souborů z uzlu.

### <a name="application-errors"></a>Chyby aplikace

Selhat může i proces zadaný pomocí příkazového řádku úkolu. Proces se považuje za neúspěšný, pokud proces provedený úkolem vrátí ukončovací kód s nenulovou hodnotou (viz *Ukončovací kódy úkolů* v následující části).

V případě chyb aplikací můžete nakonfigurovat dávku tak, aby automaticky opakovala úkol až do zadaného počtu opakování.

### <a name="constraint-errors"></a>Chyby omezení

Pomocí proměnné *maxWallClockTime* můžete nastavit omezení, které určuje maximální dobu trvání provádění úlohy nebo úkolu. To může být užitečné pro ukončení neodpovídajících úloh.

Při překročení maximální doby je úkol označen jako *dokončený*, ale ukončovací kód je nastaven na hodnotu `0xC000013A` a pole *schedulingError* bude označeno jako `{ category:"ServerError", code="TaskEnded"}`.

## <a name="task-exit-codes"></a>Ukončovací kódy úkolů

Jak bylo zmíněno výše, je úkol službou Batch označen jako neúspěšný, pokud proces, který je úkolem spuštěn, vrátí nenulový ukončovací kód. Pokud úkol spustí proces, služba Batch vyplní vlastnost ukončovacího kódu úkolu návratovým kódem procesu.

Je důležité si uvědomit, že ukončovací kód úkolu neurčuje služba Batch. Ukončovací kód úkolu se určuje podle samotného procesu nebo operačního systému, na kterém byl proces spuštěn.

## <a name="task-failures-or-interruptions"></a>Selhání úlohy nebo přerušení

Úkoly mohou občas selhat nebo být přerušeny. Vlastní aplikace úkolu může selhat, uzel, na kterém je spuštěn úkol, může být restartován nebo může být uzel odebrán z fondu během operace změny velikosti fondu (Pokud je zásada zrušení přidělení fondu nastavena na odebrání uzlů okamžitě bez čekání na dokončení úloh). Ve všech případech může být úkol službou Batch automaticky znovu zařazen do fronty, aby byl spuštěn na jiném uzlu.

Je také možné, že při přerušovaném problému dojde k tomu, že úloha přestane reagovat nebo může trvat příliš dlouho. Pro úkol můžete nastavit maximální interval provádění. Pokud dojde k překročení maximálního intervalu provádění, služba Batch přeruší aplikaci úkolu.

## <a name="connect-to-compute-nodes"></a>Připojení k výpočetním uzlům

Další ladění a řešení potíží můžete provádět při vzdáleném přihlášení k výpočetnímu uzlu. Pro uzly Windows si můžete na portálu Azure stáhnout soubor protokolu RDP (Remote Desktop) a pro uzly Linux získat informace o připojení Secure Shell (SSH). Můžete to také provést pomocí rozhraní API pro Batch, jako je například s [Batch .NET](/dotnet/api/microsoft.azure.batch.computenode) nebo [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Pokud se chcete připojit k uzlu prostřednictvím protokolu RDP nebo SSH, musíte na uzlu nejprve vytvořit uživatele. Můžete to provést takto: na webu Azure Portal [přidáte uživatelský účet do uzlu](/rest/api/batchservice/computenode/adduser) pomocí rozhraní Batch REST API a zavoláte metodu [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode) v Batch .NET nebo zavoláte metodu [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) v modulu Batch Python.

Pokud potřebujete omezit nebo zakázat přístup k výpočetním uzlům pomocí protokolu RDP nebo SSH, postupujte podle článku o [konfiguraci nebo zakázání vzdáleného přístupu k výpočetní uzlům ve fondu služby Azure Batch](pool-endpoint-configuration.md).

## <a name="troubleshoot-problem-nodes"></a>Řešení potíží s uzly

V situacích, kdy některé úkoly selhávají, může klientská aplikace nebo služba Batch prozkoumat metadata neúspěšných úkolů, aby identifikovala uzel, který se chová nesprávně. Každý uzel ve fondu má přiřazeno jedinečné číslo ID a uzel, na kterém je spuštěn úkol, je zahrnut v metadatech úkolu. Po identifikaci problémového uzlu s ním můžete provést několik akcí:

- **Restartování uzlu** ([REST](/rest/api/batchservice/computenode/reboot)  |  [.NET](/dotnet/api/microsoft.azure.batch.computenode.reboot)))

    Restartování uzlu může někdy odstranit latentní problémy, jako jsou zablokované nebo zhroucené procesy. Pokud váš fond používá spouštěcí úkol nebo pokud úloha používá úkol přípravy úlohy, spustí se při restartování uzlu.
- **Obnovení uzlu z image** ([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    Tato možnost přeinstaluje operační systém na uzlu. Stejně jako u restartování uzlu jsou spouštěcí úkoly a úkoly přípravy úlohy znovu spuštěny poté, co uzel byl obnoven z image.
- **Odebrání uzlu z fondu** ([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    Někdy je nezbytné úplně odebrat uzel z fondu.
- **Zakázání plánování úkolů na uzlu** ([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    To efektivně převede uzel do režimu offline, aby se mu nepřiřazovaly žádné další úkoly, ale umožňuje, aby zůstal spuštěný a ve fondu. Díky tomu můžete provést další šetření příčin selhání bez ztráty dat neúspěšného úkolu, aniž by uzel způsobil selhání dalších úkolů. Můžete například zakázat plánování úloh na uzlu a pak se přihlásit vzdáleně a prohlédnout si protokoly událostí uzlu nebo provádět jiné řešení potíží. Po dokončení šetření můžete přenést uzel zpět do režimu online povolením plánování úkolů ([REST](/rest/api/batchservice/computenode/enablescheduling)  |  [.NET](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)nebo provést jednu z dalších akcí popsaných výše).

> [!IMPORTANT]
> Pomocí akcí popsaných výše může youc určit, jak se při provádění této akce zpracují úlohy, které jsou aktuálně spuštěné na uzlu. Pokud například zakážete plánování úkolů na uzlu pomocí klientské knihovny Batch .NET, můžete zadat hodnotu výčtu [DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) a určit, jestli spuštěné úkoly ukončit (**Terminate**), znovu zařadit do fronty (**Requeue**) pro naplánování na jiných uzlech, nebo umožnit dokončení běžících úkolů před provedením akce (**TaskCompletion**).

## <a name="retry-after-errors"></a>Opakovat po chybách

Rozhraní API dávky vás upozorní, pokud dojde k selhání. Všechny tyto akce se dají opakovat a všechny zahrnují pro tento účel globální obslužnou rutinu opakování. Je nejvhodnější použít tento vestavěný mechanismus.

Po selhání byste před opakováním pokusu měli počkat (několik sekund mezi opakováními). Pokud se znovu pokusíte příliš často nebo příliš rychle, obslužná rutina opakování omezí.

## <a name="next-steps"></a>Další kroky

- Naučte se, jak [kontrolovat chyby ve fondech a uzlech](batch-pool-node-error-checking.md).
- Přečtěte si, jak [zjistit chyby úloh a úkolů](batch-job-task-error-checking.md).
- Zkontrolujte seznam [stavů a kódů dávek](/rest/api/batchservice/batch-status-and-error-codes).
