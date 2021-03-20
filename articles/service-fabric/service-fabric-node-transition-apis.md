---
title: Spouštění a zastavování uzlů clusteru
description: Naučte se používat vkládání chyb k otestování Service Fabric aplikace spuštěním a zastavením uzlů clusteru.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c31040ec13084f9e4b08bbc9a347e4ad44975bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89021251"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Nahrazení počátečního uzlu a zastavení rozhraní API uzlu pomocí rozhraní API pro přechod uzlů

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Co dělají uzel zastavení a rozhraní API pro spouštění uzlů?

Rozhraní API pro zastavení uzlu (spravované: [StopNodeAsync ()][stopnode], PowerShell: [stop-ServiceFabricNode][stopnodeps]) zastaví uzel Service Fabric.  Uzel Service Fabric je proces, ne virtuální počítač nebo počítač – virtuální počítač nebo počítač budou pořád běžet.  Pro zbytek dokumentu "Node" bude znamenat Service Fabric uzel.  Zastavení *uzlu přesune* tento uzel do stavu *Zastaveno* , kde není členem clusteru a nemůže hostovat služby, čímž simuluje uzel uzlu.  To je užitečné pro vkládání chyb do systému za účelem testování vaší aplikace.  Rozhraní API spouštěcího uzlu (spravované: [StartNodeAsync ()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) vrátí zpět rozhraní API pro zastavení uzlu, které převede uzel zpátky do normálního stavu.

## <a name="why-are-we-replacing-these"></a>Proč je nahrazujeme?

Jak je popsáno výše, *zastavený* Service Fabric uzel je záměrně cílen na uzel pomocí rozhraní API pro zastavení uzlu.  Uzel *je mimo provoz* mimo jiný důvod (například virtuální počítač nebo počítač je vypnutý).  V případě rozhraní API pro zastavení systému nevystavuje systém informace k rozlišení mezi *zastavenými* uzly a uzly *mimo provoz* .

Kromě toho některé chyby vracené těmito rozhraními API nejsou tak popisné, jak by mohly být.  Například volání rozhraní API pro zastavení uzlu na již *zastaveném* uzlu vrátí chybu *InvalidAddress*.  Toto prostředí je možné zlepšit.

Doba, po kterou je uzel zastaven, je také "nekonečné", dokud není vyvoláno rozhraní API pro spuštění uzlu.  Zjistili jsme, že to může způsobovat problémy a může být náchylné k chybám.  Například jsme viděli problémy, kdy uživatel vyvolal rozhraní API pro zastavení uzlu na uzlu a pak na něj zapomněl.  Později je nejasné, jestli byl uzel *mimo provoz* nebo *zastavil*.


## <a name="introducing-the-node-transition-apis"></a>Představujeme rozhraní API pro přechod uzlů

Tyto problémy jsme vyřešili v nové sadě rozhraní API.  Rozhraní API pro přechod k novému uzlu (spravované: [StartNodeTransitionAsync ()][snt]) se dá použít k převedení Service Fabric uzlu do stavu *Zastaveno* nebo k jeho přechodu ze stavu *Zastaveno* do normálního stavu.  Všimněte si prosím, že "Start" v názvu rozhraní API neodkazuje na spuštění uzlu.  Odkazuje na zahájení asynchronní operace, kterou systém provede, aby přešl uzel do stavu *Zastaveno* nebo spuštěno.

**Použití**

Pokud rozhraní API pro přechod uzlů nevyvolá výjimku při vyvolání, systém přijal asynchronní operaci a provede ji.  Úspěšné volání neznamená, že operace ještě skončila.  Chcete-li získat informace o aktuálním stavu operace, zavolejte rozhraní API průběhu přechodu na uzel (spravované: [GetNodeTransitionProgressAsync ()][gntp]) s identifikátorem GUID použitým při vyvolání rozhraní API pro přechod uzlů pro tuto operaci.  Rozhraní API průběhu přechodu na uzel vrací objekt NodeTransitionProgress.  Vlastnost State tohoto objektu určuje aktuální stav operace.  Pokud je stav "spuštěno", operace je prováděna.  Pokud je dokončená, operace se dokončila bez chyby.  Pokud dojde k chybě, při provádění operace došlo k potížím.  Vlastnost Result vlastnosti Exception indikuje, co byl problém.  https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstateDalší informace o vlastnosti State naleznete v části "ukázkové použití" níže v tématu Příklady kódu.


**Rozlišit mezi zastaveným uzlem a uzlem dolů** Pokud se uzel *zastavil* pomocí rozhraní API pro přechod uzlů, výstup dotazu na uzel (spravovaný: [GetNodeListAsync ()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) zobrazí, že má tento uzel *hodnotu vlastnosti hodnotu* true.  Všimněte si, že se liší od hodnoty vlastnosti *NodeStatus* , která bude *vyslovovat*.  Pokud má vlastnost *NodeStatus* hodnotu *Down*, ale hodnota *Stopped* je false, uzel nebyl zastaven pomocí rozhraní API pro přechod uzlů a z jiného důvodu je *mimo provoz* .  Pokud má vlastnost *Stopped* hodnotu true a vlastnost *NodeStatus* je *mimo provoz*, bylo zastaveno pomocí rozhraní API pro přechod uzlů.

Spuštění *zastaveného* uzlu pomocí rozhraní API pro přechod uzlů vrátí ho do funkce jako normálního člena clusteru znovu.  Výstup rozhraní API pro dotaz na uzel se zobrazí *jako false* a *NodeStatus* jako něco mimo provoz (například nahoru).


**Omezená doba trvání** Když použijete rozhraní API pro přechod uzlů k zastavení uzlu, jeden z požadovaných parametrů *stopNodeDurationInSeconds* představuje dobu v sekundách, po kterou se uzel *zastavil*.  Tato hodnota musí být v povoleném rozsahu, který má minimálně 600 a maximálně 14400.  Po uplynutí této doby se uzel automaticky restartuje do stavu nahoru.  Příklad použití najdete v ukázce 1 níže.

> [!WARNING]
> Vyhněte se kombinování rozhraní API pro přechod uzlů a zastavování rozhraní API pro uzel zastavení a spuštění.  Doporučujeme použít pouze rozhraní API pro přechod uzlů.  > Pokud byl uzel už zastavený pomocí rozhraní API pro zastavení uzlu, měl by se nejdřív začít používat rozhraní API pro spuštění uzlu předtím, než se použijí rozhraní API pro přechod uzlů >.

> [!WARNING]
> Volání rozhraní API přechodu s více uzly nelze současně provést na jednom uzlu.  V takové situaci > rozhraní API pro přechod uzlů vyvolat výjimku FabricException s hodnotou vlastnosti ErrorCode NodeTransitionInProgress.  Jakmile je přechod uzlu na konkrétním uzlu > spuštěný, měli byste počkat, dokud operace nedosáhne stavu terminálu (dokončeno, chyba nebo ForceCancelled) před spuštěním > nového přechodu na stejný uzel.  Volání přechodu paralelního uzlu na různých uzlech jsou povolená.


#### <a name="sample-usage"></a>Ukázka použití


**Ukázka 1** – následující ukázka používá rozhraní API pro přechod uzlů k zastavení uzlu.

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

**Ukázka 2** – následující ukázka spustí *zastavený* uzel.  Používá některé pomocné metody z první ukázky.

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

**Ukázka 3** – následující ukázka ukazuje nesprávné použití.  Toto použití je nesprávné, protože *stopDurationInSeconds* , který poskytuje, je větší než povolený rozsah.  Vzhledem k tomu, že StartNodeTransitionAsync () selže s závažnou chybou, operace nebyla přijata a rozhraní API pro průběh by se nemělo volat.  Tato ukázka používá některé pomocné metody z první ukázky.

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

**Ukázka 4** : Následující příklad ukazuje informace o chybě, které budou vráceny z rozhraní API průběhu přechodu uzlu, když je přijata operace iniciovaná rozhraním API pro přechod uzlů, ale později při jejich spuštění selže.  V případě tohoto případu dojde k chybě, protože rozhraní API pro přechod uzlů se pokusí spustit uzel, který neexistuje.  Tato ukázka používá některé pomocné metody z první ukázky.

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

[stopnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[stopnodeps]: /previous-versions/azure/mt125982(v=azure.100)
[startnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[startnodeps]: /previous-versions/azure/mt163520(v=azure.100)
[nodequery]: /dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: /powershell/module/servicefabric/get-servicefabricnode
[snt]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
