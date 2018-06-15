---
title: Dotazování dlouhotrvající operace | Microsoft Docs
description: Toto téma ukazuje, jak dlouho běžící operace dotazování.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: fa456a2d0bb427af80d67a0b971fe8bd41bb7868
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788421"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Doručování živé streamování pomocí služby Azure Media Services

## <a name="overview"></a>Přehled

Microsoft Azure Media Services nabízí rozhraní API, která odesílat žádosti do Media Services spuštění operace (například: vytvoření, spuštění, zastavení nebo odstranit kanál). Tyto operace jsou časově náročné.

.NET SDK služby Media Services poskytuje rozhraní API, která odeslat požadavek a počkejte na dokončení operace (interně rozhraní API se dotazuje na průběh operace v některé intervalech). Například při volání kanálu. Start(), metoda vrátí po spuštění kanálu. Můžete také použít asynchronní verze: await kanál. StartAsync() (informace o asynchronní vzor založený na úlohách najdete v tématu [klepněte na](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). Rozhraní API, které odeslat žádost o operaci a poté dotazování na stav, dokud se nedokončí operaci se nazývají "dotazování metody". Tyto metody (obzvláště verzi asynchronní) – se doporučují pro aplikacemi rich client nebo stavové služby.

Existují scénáře, kde aplikace nemůže čekat na dlouho spuštěný požadavek http a chce pro dotazování na průběh operace ručně. Typickým příkladem by prohlížeč interakci s bezstavové webové služby: Pokud v prohlížeči požádá o vytvoření kanálu, webovou službu inicializuje dlouhotrvající operace a vrátí ID operace v prohlížeči. Prohlížeč může pak požádejte webové služby a získat stav operace podle ID. .NET SDK služby Media Services poskytuje rozhraní API, které jsou užitečné pro tento scénář. Tato rozhraní API se nazývají "-cyklického dotazování metody".
"-Cyklického dotazování metody" mají následující vzoru pro pojmenovávání: Odeslat*OperationName*operace (například SendCreateOperation). Odeslat*OperationName*vrátit operaci metody **IOperation** objekt; vrácený objekt obsahuje informace, které můžete použít ke sledování operaci. Odesílání*OperationName*OperationAsync metody vrací **úloh<IOperation>**.

Následující třídy v současné době podporují metody cyklického dotazování: **kanál**, **StreamingEndpoint**, a **programu**.

Dotazování na stav operace, použijte **GetOperation** metodu **OperationBaseCollection** třídy. Zkontrolujte stav operace pomocí následující intervaly: pro **kanál** a **StreamingEndpoint** operace, použijte 30 sekund; pro **Program** operace, použijte 10 sekund.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Příklad:

Následující příklad definuje třídu s názvem **ChannelOperations**. Definice této třídy může být výchozím bodem pro – třída definice webové služby. Pro jednoduchost použijte následující příklady verze bez asynchronní metody.

Tento příklad také ukazuje, jak může klient použít tuto třídu.

### <a name="channeloperations-class-definition"></a>Definice třídy ChannelOperations

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
{
    // Read values from the App.config file.
    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>Kód klienta

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

