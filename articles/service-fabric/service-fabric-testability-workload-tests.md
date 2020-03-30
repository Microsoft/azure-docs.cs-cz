---
title: Simulace chyb v aplikacích Azure Service Fabric
description: Přečtěte si, jak posílit vaše služby Azure Service Fabric proti řádné a ungraceful selhání.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d3d9f6478336c59adb875bf21438d5ffa457b1d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645986"
---
# <a name="simulate-failures-during-service-workloads"></a>Simulace selhání během úloh služby
Scénáře testovatelnosti ve službě Azure Fabric umožňují vývojářům nestarat se o řešení jednotlivých chyb. Existují však scénáře, kde může být potřeba explicitní prokládání zatížení klienta a selhání. Prokládání zatížení klienta a chyb zajišťuje, že služba skutečně provádí některé akce, když dojde k selhání. Vzhledem k úrovni kontroly, kterou poskytuje testovatelnost, mohou být v přesných bodech provádění úlohy. Tato indukce chyb v různých stavech v aplikaci může najít chyby a zlepšit kvalitu.

## <a name="sample-custom-scenario"></a>Ukázkový vlastní scénář
Tento test ukazuje scénář, který interleaves obchodní zatížení s [řádné a ungraceful selhání](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). Chyby by měly být vyvolány uprostřed operací služby nebo vypočítat pro dosažení nejlepších výsledků.

Pojďme projít příklad služby, která zveřejňuje čtyři úlohy: A, B, C a D. Každý odpovídá sadě pracovních postupů a může být výpočetní, úložiště nebo mix. V zájmu jednoduchosti, budeme abstraktní z úlohy v našem příkladu. Různé chyby provedené v tomto příkladu jsou:

* RestartNode: Ungraceful chyba simulovat restartování počítače.
* RestartDeployedCodePackage: Ungraceful chyba simulovat proces u hostitele služby havaruje.
* RemoveReplica: Elegantní chyba simulovat odstranění repliky.
* MovePrimary: Elegantní chyba simulovat repliky přesune spuštěna service fabric vyrovnávání zatížení.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.TestManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.FaultManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.FaultManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.FaultManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.FaultManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
