---
title: Cyklické dotazování na operace Long-Running | Microsoft Docs
description: Azure Media Services nabízí rozhraní API, která odesílají žádosti do Media Services spuštění operací (například vytvoření, spuštění, zastavení nebo odstranění kanálu), tyto operace jsou dlouhotrvající. V tomto tématu se dozvíte, jak se dotazovat na dlouhodobě běžící operace.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7926f7aaa427d49d13cab5e13f5153bcd22e5898
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013818"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Živé streamování pomocí Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Přehled

Microsoft Azure Media Services nabízí rozhraní API, která odesílají žádosti do Media Services spuštění operací (například: vytvoření, spuštění, zastavení nebo odstranění kanálu). Tyto operace jsou dlouhodobě spuštěné.

Sada Media Services .NET SDK poskytuje rozhraní API, která odesílají požadavek a čekají na dokončení operace (interně se rozhraní API dotazuje na průběh operace v některých intervalech). Například při volání metody Channel. Start (), metoda se vrátí po spuštění kanálu. Můžete také použít asynchronní verzi: await kanál. StartAsync () (informace o asynchronním vzoru založeném na úlohách naleznete v tématu [klepněte](./media-services-mes-schema.md)). Rozhraní API, která odesílají požadavek operace a následně se dotazují na stav, dokud se operace nedokončí, se nazývají "metody cyklického dotazování". Tyto metody (obzvláště asynchronní verze) se doporučují pro rozšířené klientské aplikace a/nebo stavové služby.

K dispozici jsou situace, kdy aplikace nemůže čekat na dlouho běžící požadavek HTTP, a chce se dotazovat na průběh operace ručně. Typickým příkladem může být prohlížeč, který pracuje s bezstavovou webovou službou: když prohlížeč požaduje vytvoření kanálu, Webová služba spustí dlouhou běžící operaci a vrátí ID operace do prohlížeče. Prohlížeč pak může požádat webovou službu, aby získala stav operace na základě ID. Sada Media Services .NET SDK poskytuje rozhraní API, která jsou užitečná pro tento scénář. Tato rozhraní API se nazývají "metody bez cyklického dotazování".
Metody "non-cyklického dotazování" mají následující vzor pojmenovávání:*operace Odeslat operaci*(například SendCreateOperation). Metody operace Send *operace* vrátí objekt **IOperation** ; vrácený objekt obsahuje informace, které lze použít ke sledování operace. Vrátí **úlohu \<IOperation>** metody OperationAsync *operace* odeslání.

V současné době následující třídy podporují metody bez cyklického dotazování:  **kanál**, **StreamingEndpoint** a **program**.

Chcete-li se dotázat na stav operace, použijte metodu **GetOperation** pro třídu **OperationBaseCollection** . Pomocí následujících intervalů ověřte stav operace: pro operace **kanálu** a **StreamingEndpoint** použijte 30 sekund; pro operace **programu** použijte 10 sekund.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia

Nastavte své vývojové prostředí a v souboru app.config vyplňte informace o připojení, jak je popsáno v tématu [Vývoj pro Media Services v .NET](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Příklad

Následující příklad definuje třídu s názvem **ChannelOperations**. Tato definice třídy by mohla být výchozím bodem definice vaší třídy webové služby. Pro zjednodušení následující příklady používají neasynchronní verze metod.

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