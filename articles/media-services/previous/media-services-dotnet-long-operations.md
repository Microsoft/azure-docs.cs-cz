---
title: Dotazování dlouho běžící operace | Dokumentace Microsoftu
description: Toto téma ukazuje, jak dlouho běžící operace dotazování.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: f5098b2691f7c73be5df6b44479082bf25effde7
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998033"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Zajištění živého streamování pomocí služby Azure Media Services

## <a name="overview"></a>Přehled

Microsoft Azure Media Services nabízí rozhraní API, která odesílají požadavky na spuštění operace Media Services (Příklad: vytvoření, spuštění, zastavení nebo odstranění kanálu). Tyto operace jsou časově náročné.

Media Services .NET SDK poskytuje rozhraní API, která odešlete žádost a počkejte na dokončení operace (interně, rozhraní API jsou dotazování na průběh operace v některých intervalech). Například při volání kanálu. Start(), metoda vrátí po spuštění kanálu. Můžete také použít asynchronní verze: await kanálu. StartAsync() (informace o asynchronní vzor založený na úlohách najdete v tématu [klepněte](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). Rozhraní API, které poslat žádost o operaci a poté dotazování na stav, až do dokončení operace se označují jako "dotazování metody". Tyto metody (zejména asynchronní verze) se doporučují pro aplikacemi rich client a/nebo stavové služby.

Existují situacích, kdy aplikace nemůže čekat dlouho spuštěný požadavek http a chce, aby se k dotazování na průběh operace ručně. Typickým příkladem bude prohlížeč interakci s bezstavovou webovou službu: když Pokud chcete vytvořit kanál požádá o prohlížeče, webové služby inicializuje dlouhotrvající operace a vrátí ID operace do prohlížeče. Prohlížeč pak požádat o webové službě se získat stav operace vycházející z ID. Media Services .NET SDK poskytuje rozhraní API, které jsou užitečné pro tento scénář. Tato rozhraní API, se nazývají "-cyklického dotazování metody".
"Bez dotazování metody" mají následující vzor pro pojmenování: Odeslat*OperationName*operace (například SendCreateOperation). Odeslat*OperationName*metodám operace vrátit **IOperation** objekt; vráceného objektu obsahuje informace, které je možné sledovat operaci. Odeslat*OperationName*OperationAsync metody vrací **úloh<IOperation>**.

Následující třídy v současné době podporují metody bez dotazování:  **Kanál**, **StreamingEndpoint**, a **Program**.

K dotazování na stav operace, použijte **GetOperation** metodu **OperationBaseCollection** třídy. Pomocí následujících intervalech zkontrolovat stav operace: pro **kanál** a **StreamingEndpoint** operace, použijte 30 sekund; pro **Program** operace, použijte 10 sekund.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Příklad:

Následující příklad definuje třídu s názvem **ChannelOperations**. Definici této třídy může být výchozí bod pro třídy definice webové služby. Pro zjednodušení následující příklady používají jiné asynchronní verze metod.

Příklad také ukazuje, jak může klient použít tuto třídu.

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

### <a name="the-client-code"></a>Klientský kód

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

