---
title: Spuštění a zastavení uzlů clusteru k testování Azure mikroslužeb | Microsoft Docs
description: Další informace o použití pravděpodobnost vkládání k testování aplikace Service Fabric pomocí spuštění a zastavení uzly clusteru.
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
ms.openlocfilehash: 0ed18097fa18101c237b4408d26dd1bc9c5d5648
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Nahrazení uzel spuštění a zastavení uzlu rozhraní API s rozhraním API pro přechod uzlu

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Co rozhraní API uzlu spuštění a zastavení uzlu udělat?

Rozhraní API zastavení uzlu (spravované: [StopNodeAsync()][stopnode], prostředí PowerShell: [Stop-ServiceFabricNode][stopnodeps]) zastaví uzlem Service Fabric.  Uzel Service Fabric je proces, není virtuálního počítače nebo počítače – virtuálního počítače nebo počítače bude stále spuštěna.  Pro zbývající části dokumentu "uzel" znamená Service Fabric uzlu.  Zastavení uzlu vloží je do *zastavena* stavu, kde není členem clusteru a nemůže být hostitelem služby, proto simulaci *dolů* uzlu.  To je užitečné pro vložení chyb do systému pro testování vaší aplikace.  Rozhraní API spuštění uzlu (spravované: [StartNodeAsync()][startnode], prostředí PowerShell: [Start-ServiceFabricNode][startnodeps]]) obrátí rozhraní API zastavení uzlu  které přepne uzly zpět k normálním stavu.

## <a name="why-are-we-replacing-these"></a>Proč to jsme nahradit?

Jak je popsáno výše, *zastavena* Service Fabric uzlu je uzel záměrně cílené pomocí rozhraní API zastavení uzlu.  A *dolů* uzel je uzel, který je mimo provoz z jiného důvodu (například virtuální počítač nebo počítač je vypnutý).  S rozhraním API zastavení uzlu systému nevystavuje informace k rozlišení mezi *zastavena* uzly a *dolů* uzlů.

Kromě toho nejsou jako popisný také možné, některé chyby vrácené systémem tato rozhraní API.  Například na volání rozhraní API zastavení uzlu na již *zastavena* uzlu vrátí chybu *InvalidAddress*.  Toto prostředí lze zlepšit.

Doba trvání, který uzel je zastavena pro je také "nekonečné" dokud se vyvolá rozhraní API spuštění uzlu.  Jsme zjistili to může způsobit problémy a mohou být náchylné k chybě.  Například jste viděli problémy, kde uživatel volá rozhraní API uzlu zastavit v uzlu a poté zapomněli jste o něm.  Později, bylo jasné, pokud byl uzel *dolů* nebo *zastavena*.


## <a name="introducing-the-node-transition-apis"></a>Úvod do rozhraní API přechod uzlu

Zaměřili jsme se tyto problémy výše v novou sadu rozhraní API.  Nové rozhraní API přechod uzlu (spravované: [StartNodeTransitionAsync()][snt]) může sloužit k přechod uzel do Service Fabric *zastavena* stavu, nebo přechod z *zastavena* stavu normální stav.  Upozorňujeme, že "Start" názvu rozhraní API neodkazuje na spuštění uzlu.  Odkazuje na od asynchronní operace, která v systému, budou spuštěny při přechodu uzel buď *zastavena* nebo spuštění stavu.

**Využití**

Pokud rozhraní API přechod uzlu nevyvolá výjimku při vyvolání, pak systém přijal asynchronní operaci a spustí ho.  Úspěšné volání neznamená, že ještě dokončení operace.  Pokud chcete získat informace o stavu aktuální operace, volání rozhraní API uzlu přechod průběh (spravované: [GetNodeTransitionProgressAsync()][gntp]) s identifikátorem guid používá při volání rozhraní API pro přechod k uzlu pro tuto operaci.  Rozhraní API průběh přechod uzlu vrátí objekt NodeTransitionProgress.  Vlastnost stav tohoto objektu určuje aktuální stav operace.  Pokud stav "spuštěný" operace provádí.  Pokud je dokončena, operace se dokončila bez chyby.  Pokud je s chybou, došlo k potížím, provádění operace.  Vlastnost výsledek výjimka vlastnost označí, co byl tento problém.  V tématu https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate Další informace o vlastnosti stavu a v části "Využití vzorků" následující příklady kódu.


**Rozdíl mezi zastaven uzel a uzel dolů** Pokud uzel je *zastavena* pomocí uzlu přechod API, výstup uzel dotazu (spravované: [GetNodeListAsync()] [ nodequery], Prostředí PowerShell: [Get-ServiceFabricNode][nodequeryps]) se zobrazí, zda má tento uzel *IsStopped* vlastnost hodnotu true.  Poznámka: Tento soubor se liší od hodnoty *NodeStatus* vlastnosti, která se dozvíte *dolů*.  Pokud *NodeStatus* vlastnost má hodnotu *dolů*, ale *IsStopped* je nastavena hodnota false, pak uzlu nebyla zastavena, pomocí rozhraní API pro přechod uzlu a *dolů*  kvůli z jiného důvodu.  Pokud *IsStopped* vlastnost je nastavena hodnota true a *NodeStatus* vlastnost je *dolů*, pak byla zastavena, pomocí rozhraní API pro přechod uzlu.

Spuštění *zastavena* uzlu pomocí rozhraní API přechod uzlu fungování jako normální člena clusteru znovu vrátí.  Výstup uzel dotazu API bude zobrazovat *IsStopped* jako hodnotu false, a *NodeStatus* jako něco, co není dolů (například nahoru).


**Omezené trvání** při zastavení uzlu, jeden z požadovaných parametrů, pomocí rozhraní API přechod uzlu *stopNodeDurationInSeconds*, představuje dobu v sekundách zachovat uzlu *zastavena*.  Tato hodnota musí být v povoleném rozsahu, který má minimálně 600 a maximálně 14400.  Po vypršení této doby uzlu se sám do stavem automaticky restartuje.  Naleznete příklad 1 níže uvedený příklad použití.

> [!WARNING]
> Vyhněte se kombinování uzlu přechod rozhraní API a rozhraní API uzlu spuštění a zastavení uzlu.  Doporučuje se používat pouze API přechod uzlu.  > Pokud uzel byl již byla zastavena, pomocí rozhraní API uzlu zastavit, ho by měl být spuštěn pomocí uzlu spustit rozhraní API před použitím > rozhraní API pro přechod k uzlu.

> [!WARNING]
> Více volání rozhraní API pro přechod k uzlu není možné provést na stejném uzlu paralelně.  V takovém případě bude rozhraní API přechod uzlu > throw FabricException má hodnotu vlastnosti ErrorCode NodeTransitionInProgress.  Po přechodu uzlu v konkrétním uzlu má > byla spuštěna, byste měli počkat, dokud operaci dosáhne stavu terminálu (dokončeno, Faulted nebo ForceCancelled) před spuštěním > nový přechod na stejném uzlu.  Paralelní uzlu volání přechod na jiné uzly jsou povoleny.


#### <a name="sample-usage"></a>Využití vzorků


**Příklad 1** -následující příklad používá rozhraní API uzlu přechod k zastavení uzlu.

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

**Příklad 2** -následující ukázka spustí *zastavena* uzlu.  Používá některé metody helper od první vzorku.

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

**Ukázka 3** -následující příklad ukazuje nesprávné použití.  Toto použití je nesprávný protože *stopDurationInSeconds* poskytuje je větší než povolený rozsah.  Vzhledem k tomu, že StartNodeTransitionAsync() se nezdaří s závažné chybě, operaci nebyl přijat a rozhraní API průběh by neměl být volán.  Tato ukázka používá některé metody helper od první vzorku.

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

**Ukázka 4** -níže uvedená ukázka uvádí informace o chybě, který bude vrácen z rozhraní API průběh přechod uzlu při operaci iniciovaná rozhraní API přechod uzlu byla přijata, ale později selže při provádění.  V případě selže, protože rozhraní API přechod uzel pokusí spustit uzel, který neexistuje.  Tato ukázka používá některé metody helper od první vzorku.

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
[nodequeryps]: https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode?redirectedfrom=msdn
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartNodeTransitionAsync_System_Fabric_Description_NodeTransitionDescription_System_TimeSpan_System_Threading_CancellationToken_
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_GetNodeTransitionProgressAsync_System_Guid_System_TimeSpan_System_Threading_CancellationToken_
