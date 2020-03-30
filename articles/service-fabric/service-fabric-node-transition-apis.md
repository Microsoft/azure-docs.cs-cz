---
title: Spuštění a zastavení uzlů clusteru
description: Zjistěte, jak pomocí vkládání chyb otestovat aplikaci Service Fabric spuštěním a zastavením uzlů clusteru.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 8f2eefec94ad4763a054ee089b17232c41e642dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609787"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Nahrazení rozhraní API uzlu start uzlu a zastavení uzlu rozhraním API pro přechod uzlu

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>K čemu se dělají prostředí Stop Node a Start Node?

Rozhraní API stop uzlu (spravované: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) zastaví uzel Service Fabric.  Uzel Service Fabric je proces, není virtuální počítač nebo počítač – virtuální počítač nebo počítač bude stále spuštěn.  Pro zbytek dokumentu "uzel" bude znamenat uzel Service Fabric.  Zastavení uzlu jej umístí do *zastaveného* stavu, kde není členem clusteru a nemůže hostovat služby, a tím simulovat uzel *dolů.*  To je užitečné pro vstřikování chyb do systému k testování aplikace.  Rozhraní API počátečního uzlu (spravované: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) obrátí rozhraní API stop uzlu, které přenese uzel zpět do normálního stavu.

## <a name="why-are-we-replacing-these"></a>Proč je nahrazujeme?

Jak je popsáno výše, *zastavený* uzel Service Fabric je uzel záměrně cílené pomocí rozhraní API stop uzlu.  Down *down* node je uzel, který je z jakéhokoli jiného důvodu (například virtuální počítač nebo počítač je vypnutý).  Pomocí rozhraní STOP Node ROZHRANÍ API systému nezveřejňuje informace rozlišovat mezi *zastavené* uzly a *dolů* uzly.

Kromě toho některé chyby vrácené těmito api nejsou tak popisné, jak by mohly být.  Například vyvolání rozhraní API stop uzlu na již *zastaveném* uzlu vrátí chybu *InvalidAddress*.  Tato zkušenost by mohla být zlepšena.

Doba trvání uzlu je zastavena pro je "nekonečné", dokud počáteční uzel rozhraní API je vyvolána.  Zjistili jsme, že to může způsobit problémy a může být náchylné k chybám.  Například jsme viděli problémy, kdy uživatel vyvolal rozhraní API stop uzlu na uzlu a pak na něj zapomněl.  Později nebylo jasné, zda byl uzel *vypnutý* nebo *zastavený*.


## <a name="introducing-the-node-transition-apis"></a>Představujeme nastavení API pro přechod uzlu

Tyto problémy jsme vyřešili výše v nové sadě api.  Nové rozhraní API přechodu uzlu (spravované: [StartNodeTransitionAsync()][snt]) lze použít k přechodu uzlu Service Fabric do *zastaveného* stavu nebo k jeho přechodu z *zastaveného* stavu do normálního stavu nahoru.  Vezměte prosím na vědomí, že "Start" v názvu rozhraní API neodkazuje na spuštění uzlu.  Odkazuje na zahájení asynchronní operace, kterou systém spustí pro přechod uzlu na *zastavený* nebo spuštěný stav.

**Použití**

Pokud rozhraní API přechoduzlu nevyvolá výjimku při vyvolání, pak systém přijal asynchronní operaci a provede ji.  Úspěšné volání neznamená, že operace je ještě dokončena.  Chcete-li získat informace o aktuálním stavu operace, volejte rozhraní API pro průběh přechodu uzlu (spravované: [GetNodeTransitionProgressAsync()][gntp]) s identifikátorem GUID používaným při vyvolání rozhraní API pro přechod uzlu pro tuto operaci.  Rozhraní API pro průběh přechodu uzlu vrátí objekt NodeTransitionProgress.  Vlastnost State tohoto objektu určuje aktuální stav operace.  Pokud je stav "Spuštěno", operace se provádí.  Pokud je dokončena, operace byla dokončena bez chyby.  Pokud je Chybný, došlo k potížím při provádění operace.  Vlastnost Result Exception vlastnost bude označovat, co byl problém.  Další https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate informace o vlastnosti State a níže uvedené části "Ukázkové využití" naleznete v části příklady kódu.


**Rozlišování mezi zastaveným uzlinou a dolů uzlu** Pokud uzel je *zastaven* pomocí rozhraní API přechoduzlu, výstup uzlu dotazu (spravované: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) zobrazí, že tento uzel má *IsStopped* hodnotu vlastnosti true.  Všimněte si, že se liší od hodnoty *NodeStatus* vlastnost, která bude říkat *Dolů*.  Pokud *Vlastnost NodeStatus* má hodnotu *Dolů*, ale *IsStopped* je false, pak uzel nebyl zastaven pomocí rozhraní API přechodu uzlu a je *dolů* z nějakého jiného důvodu.  Pokud je vlastnost *IsStopped* true a vlastnost *NodeStatus* je *dolů*, byla zastavena pomocí rozhraní API pro přechod uzlu.

Spuštění *zastaveného* uzlu pomocí rozhraní API pro přechod uzlu jej vrátí do funkce normálního člena clusteru znovu.  Výstup rozhraní API dotazu uzlu se zobrazí *IsStopped* jako false a *NodeStatus* jako něco, co není dolů (například nahoru).


**Omezená doba trvání** Při použití rozhraní API přechodu uzlu k zastavení uzlu představuje jeden z požadovaných parametrů *stopNodeDurationInSeconds*, dobu v sekundách, aby byl uzel *zastaven*.  Tato hodnota musí být v povoleném rozsahu, který má minimálně 600 a maximálně 14400.  Po uplynutí této doby se uzel automaticky restartuje do stavu Nahoru.  Příklad použití naleznete v části Ukázka 1 níže.

> [!WARNING]
> Vyhněte se míchání node přechodová api a zastavit uzel a spustit uzel API.  Doporučujeme používat pouze rozhraní API pro přechod uzlu.  > Pokud již byl uzel zastaven pomocí rozhraní STOP Node API, měl by být nejprve spuštěn pomocí rozhraní API počátečního uzlu před použitím rozhraní API pro přechod uzlu >.

> [!WARNING]
> Více násobné přechodu volání API nelze provést na stejném uzlu paralelně.  V takové situaci rozhraní API přechodu uzlu bude > vyvolat FabricException s ErrorCode hodnotu vlastnosti NodeTransitionInProgress.  Jakmile > byl spuštěn přechod uzlu na konkrétním uzlu, měli byste počkat, až operace dosáhne terminálového stavu (Dokončeno, Chyba nebo ForceCancelled) před spuštěním > nový přechod na stejném uzlu.  Paralelní volání přechodu uzlu na různých uzlech jsou povoleny.


#### <a name="sample-usage"></a>Ukázka použití


**Ukázka 1** – Následující ukázka používá rozhraní API přechodu uzlu k zastavení uzlu.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Ukázka 2** - Následující ukázka spustí *zastavený* uzel.  Používá některé pomocné metody z první ukázky.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Ukázka 3** - Následující ukázka ukazuje nesprávné použití.  Toto použití je nesprávné, protože *stopDurationInSeconds* poskytuje je větší než povolený rozsah.  Vzhledem k tomu, že StartNodeTransitionAsync() selže s závažnou chybou, operace nebyla přijata a rozhraní API průběhu by neměla být volána.  Tato ukázka používá některé pomocné metody z první ukázky.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Ukázka 4** – následující ukázka zobrazuje informace o chybě, které budou vráceny z rozhraní API pro průběh přechodu uzlu při přijetí operace iniciované rozhraní min. rozhraní API přechodu uzlu, ale později se nezdaří při provádění.  V případě, že se nezdaří, protože rozhraní API přechoduzlu pokusí spustit uzel, který neexistuje.  Tato ukázka používá některé pomocné metody z první ukázky.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
