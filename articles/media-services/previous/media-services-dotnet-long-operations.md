---
title: Dotazování na dlouhodobé operace | Dokumenty společnosti Microsoft
description: Azure Media Services nabízí api, která odesílají požadavky na media services ke spuštění operací (například vytvořit, spustit, zastavit nebo odstranit kanál), tyto operace jsou dlouhotrvající. Toto téma ukazuje, jak dotazování dlouhotrvající operace.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 43d9a6adc935010eab6e5e52d73f2019c8afcf5f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887149"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Doručování živého streamování pomocí Mediálních služeb Azure

## <a name="overview"></a>Přehled

Microsoft Azure Media Services nabízí rozhraní API, která odesílají požadavky na služby Media Services ke spuštění operací (například: vytvoření, spuštění, zastavení nebo odstranění kanálu). Tyto operace jsou dlouhodobé.

Sada Media Services .NET SDK poskytuje rozhraní API, která odesílají požadavek a čekají na dokončení operace (interně jsou rozhraní API dotazování pro průběh operace v některých intervalech). Například při volání kanálu. Start(), metoda vrátí po spuštění kanálu. Můžete také použít asynchronní verzi: await kanálu. StartAsync() (informace o asynchronním vzoru založeném na úlohách naleznete v [tématu TAP).](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx) Api, které odesílají požadavek na operaci a potom dotazování na stav, dokud operace je dokončena se nazývají "metody dotazování". Tyto metody (zejména verze Async) jsou doporučeny pro bohaté klientské aplikace nebo stavové služby.

Existují scénáře, kde aplikace nemůže čekat na dlouhotrvající požadavek http a chce dotazování na průběh operace ručně. Typickým příkladem by byl prohlížeč, který interaguje s webovou službou bez stavů: když prohlížeč požádá o vytvoření kanálu, webová služba zahájí dlouho běžící operaci a vrátí ID operace prohlížeči. Prohlížeč pak může požádat webovou službu o získání stavu operace na základě ID. Sada Media Services .NET SDK poskytuje rozhraní API, která jsou užitečná pro tento scénář. Tato api se nazývají "non-polling metody".
"Metody bez dotazování" mají následující vzor pojmenování: Send*OperationName*Operation (například SendCreateOperation). Metody Send*Operation*Operation vrátí objekt **IOperation.** vrácený objekt obsahuje informace, které lze použít ke sledování operace. Metody Send*OperationName*OperationAsync vrátí **operaci\<IOperation>**.

V současné době podporují následující třídy metody bez dotazování: **Kanál**, **StreamingEndpoint**a **Program**.

Chcete-li dotazování na stav operace, použijte metodu **GetOperation** ve třídě **OperationBaseCollection.** Ke kontrole stavu operace použijte následující intervaly: pro operace **Kanálu** a **StreamingEndpoint** použijte 30 sekund. pro **programové** operace použijte 10 sekund.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Příklad

Následující příklad definuje třídu s názvem **ChannelOperations**. Tato definice třídy může být výchozím bodem pro definici třídy webové služby. Pro jednoduchost následující příklady používají neasynchronní verze metod.

Příklad také ukazuje, jak může klient používat tuto třídu.

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

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

