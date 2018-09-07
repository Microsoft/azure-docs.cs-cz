---
title: Spustit a zastavit uzly clusteru k testování aplikací pro Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak pomocí vkládání selhání otestujte aplikaci Service Fabric spouští a zastavují se uzly clusteru.
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: ''
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: 95c3726caeb19d6bbf7153533951bb18cd7d0e57
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055399"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Nahraďte uzel spuštění a zastavení uzlu rozhraní API s rozhraním API pro přechod uzlů

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Co rozhraní API uzlu spuštění a zastavení uzlu?

Rozhraní API pro zastavení uzlu (spravované: [StopNodeAsync()][stopnode], prostředí PowerShell: [Stop-ServiceFabricNode][stopnodeps]) uzlu Service Fabricu se zastaví.  Uzel Service Fabric je proces, není virtuální počítač nebo počítač – virtuální počítač nebo počítač bude stále běžet.  Pro zbývající části dokumentu "uzel" znamená uzlu Service Fabric.  Zastavení uzlu umístí jej do *zastavena* stavu, ve kterém není členem clusteru a nemůže hostovat služby, proto budete jen simulovat *dolů* uzlu.  To je užitečné pro vkládání chyb do systému pro testování vaší aplikace.  Rozhraní API pro spuštění uzlu (spravované: [StartNodeAsync()][startnode], prostředí PowerShell: [Start-ServiceFabricNode][startnodeps]]) obrátí rozhraní API zastavení uzlu  který převede uzel zpátky do normálního stavu.

## <a name="why-are-we-replacing-these"></a>Proč tyto jsme nahradit?

Jak je popsáno výše, *zastavena* uzlu Service Fabric je uzel záměrně cílené pomocí rozhraní API pro zastavení uzlu.  A *dolů* uzel je uzel, který je mimo provoz z jiného důvodu (například virtuální počítač nebo počítač vypnutý).  Zastavit uzel rozhraní API, systém nemůže vystavovat informace k rozlišení *zastavena* uzly a *dolů* uzly.

Kromě toho nejsou popisný by mohly být některé chyby vrácené systémem tato rozhraní API.  Například volání rozhraní API pro zastavení uzlu na příslušný již *zastavena* uzel vrátí chybu *InvalidAddress*.  Tyto možnosti lze zlepšit.

Navíc je dobu, po kterou uzel je zastaveno z důvodu "nekonečno" dokud rozhraní API pro spuštění uzlu, je vyvolána.  Zjistili jsme, to může způsobit problémy a mohou být náchylné k chybě.  Například jsme se seznámili s problémy, kde uživatel vyvolá rozhraní API uzlu zastavit v uzlu a pak zapomněl(a) jsem ho.  Později, bylo jasné, zda byl uzel *dolů* nebo *zastavena*.


## <a name="introducing-the-node-transition-apis"></a>Úvod do rozhraní API pro přechod uzlů

Zaměřili jsme se na tyto problémy výše v nové sady rozhraní API.  Nové rozhraní API uzlu přechod (spravované: [StartNodeTransitionAsync()][snt]) slouží k uzlu Service Fabricu pro přechod *zastavena* stavu, nebo přechod z *zastavena* stavu do normálního stavu.  Mějte prosím na paměti, že "Start" název rozhraní API neodkazuje na počáteční uzel.  Odkazuje na od asynchronní operace, která se spustí v systému přechod uzel, který má buď *zastavena* nebo stavu spuštění.

**Využití**

Pokud rozhraní API pro přechod uzlů nevyvolá výjimku při vyvolání, pak systém přijal asynchronní operace a spustí ho.  Úspěšné volání neznamená, že ještě dokončení operace.  Chcete-li získat informace o stavu aktuální operace, volání rozhraní API průběh přechodu uzlu (spravované: [GetNodeTransitionProgressAsync()][gntp]) s identifikátorem guid při volání rozhraní API pro přechod k uzlu pro tuto operaci.  Rozhraní API průběh přechodu uzel vrátí objekt NodeTransitionProgress.  Tento objekt stavu vlastnost určuje aktuální stav operace.  Pokud státu je "systém", operace provádí.  Pokud se nedokončí operace dokončena bez chyb.  Pokud je k chybě, došlo k potížím, provádění operace.  Vlastnost Exception vlastnost výsledek označí, jak se tento problém.  Zobrazit https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate Další informace o vlastnosti stavu a v části "Ukázkový používání" následující příklady kódu.


**Rozdíl mezi zastavené uzlu a uzel dolů** Pokud uzel je *zastavena* pomocí rozhraní API uzlu přechodu, výstup tohoto uzlu dotazu (spravované: [GetNodeListAsync()] [ nodequery], Prostředí PowerShell: [Get-ServiceFabricNode][nodequeryps]) se zobrazí, že tento uzel má *IsStopped* vlastnost hodnotu true.  Všimněte si to se liší od hodnoty *NodeStatus* vlastnost, která bude sdělení *dolů*.  Pokud *NodeStatus* vlastnost má hodnotu *dolů*, ale *IsStopped* má hodnotu false, pak uzel nebyla zastavena, pomocí rozhraní API pro přechod uzlů a je *dolů*  kvůli nějakého jiného důvodu.  Pokud *IsStopped* vlastnost má hodnotu true a *NodeStatus* je vlastnost *dolů*, pak se zastavila, pomocí rozhraní API pro přechod uzlů.

Spouští se *zastavena* uzlu pomocí rozhraní API pro přechod uzlů fungování jako normální členem clusteru znovu vrátí.  Výstup dotazu uzlu rozhraní API bude zobrazovat *IsStopped* hodnotu false, a *NodeStatus* jako něco, co není dolů (například zapnutý).


**Po omezenou dobu** při zastavení uzlu, jeden z požadovaných parametrů pomocí rozhraní API pro přechod uzlů *stopNodeDurationInSeconds*, představuje dobu v sekundách zachovat uzel *zastavena*.  Tato hodnota musí být v povoleném rozsahu, který má aspoň 600 a maximálně 14400.  Po vypršení této doby na uzel samotný do stav automaticky restartuje.  Odkazovat na 1 ukázka níže pro příklad použití.

> [!WARNING]
> Nekombinujte ani rozhraní API pro přechod uzlů a rozhraní API uzlu spuštění a zastavení uzlu.  Použijte pouze API přechod uzlu se doporučuje.  > Pokud je uzel již byla zastavena, pomocí rozhraní API pro zastavení uzlu, ji by měl být spuštěn pomocí uzlu API spustit před použitím > rozhraní API pro přechod uzlů.

> [!WARNING]
> Není možné provést několik volání rozhraní API pro přechod uzlů na stejném uzlu současně.  V takovém případě bude rozhraní API pro přechod uzlů > throw FabricException s hodnotou vlastnosti ErrorCode NodeTransitionInProgress.  Jakmile má přechod uzlů v konkrétním uzlu > byla spuštěna, jste měli počkat, dokud operace přejde do konečného stavu (dokončeno, Faulted nebo ForceCancelled) před zahájením > nový přechod na stejném uzlu.  Jsou povoleny paralelních uzel volání přechod na různých uzlech.


#### <a name="sample-usage"></a>Využití vzorků


**Příklad 1** -následující příklad používá rozhraní API pro přechod uzlů k zastavení uzlu.

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

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Příklad 2** -následující ukázkový spustí *zastavena* uzlu.  Některé metody helper z první příklad používá.

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

            // Now call StartNodeTransitionProgressAsync() until hte desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Ukázka 3** -následující příklad ukazuje nesprávné použití.  Toto použití je nesprávný vzhledem k tomu, *stopDurationInSeconds* poskytuje větší než povolený rozsah.  Protože StartNodeTransitionAsync() selže s kritickou chybou, nebyl přijat operace a rozhraní API průběh by neměla být volána.  Tato ukázka používá některé metody helper z první ukázku.

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

**Ukázka 4** -následující příklad ukazuje informace o chybě, bude vrácena z rozhraní API průběh přechodu uzlu, když operace iniciovaná rozhraní API pro přechod uzlů je přijímán, ale později selže při provádění.  V případě nezdaří se, protože rozhraní API pro přechod uzlů se pokusí spustit uzel, který neexistuje.  Tato ukázka používá některé metody helper z první ukázku.

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

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StopNodeAsync_System_String_System_Numerics_BigInteger_System_Fabric_CompletionMode_
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN#System_Fabric_FabricClient_FaultManagementClient_StartNodeAsync_System_String_System_Numerics_BigInteger_System_String_System_Int32_System_Fabric_CompletionMode_System_Threading_CancellationToken_
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient#System_Fabric_FabricClient_QueryClient_GetNodeListAsync_System_String_
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartNodeTransitionAsync_System_Fabric_Description_NodeTransitionDescription_System_TimeSpan_System_Threading_CancellationToken_
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_GetNodeTransitionProgressAsync_System_Guid_System_TimeSpan_System_Threading_CancellationToken_
