---
title: Rozhraní API Azure front-endu pro ověřování
description: Vysvětluje, jak používat C# front-endové rozhraní API pro ověřování.
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 7149d7ac2625eb60a1d0d22253b93b68a99475de
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592088"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Použití rozhraní API front-endu Azure pro ověřování

V této části popíšeme, jak používat rozhraní API pro ověřování a správu relací.

> [!CAUTION]
> Funkce popsané v této kapitole vydávají interní volání REST na serveru. Stejně jako u všech volání REST odesílají tyto příkazy příliš často, což způsobí, že server bude omezovat a vracet chybu nakonec. Hodnota `SessionGeneralContext.HttpResponseCode` člena v tomto případě je 429 (příliš mnoho požadavků). Jako pravidlo pro palec by se měla **mezi následnými voláními čekat 5-10 sekund**.


## <a name="sessionconfiguration"></a>SessionConfiguration

SessionConfiguration se používá k nastavení ověřovacích informací pro ```RemoteRenderingClient``` instanci v sadě SDK.

Důležitá jsou tato pole:

```cs
public class SessionConfiguration
{
    // Domain that will be used for account authentication for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be set to the domain of the Azure Remote Rendering account.
    public string AccountDomain;
    // Domain that will be used to generate sessions for the Azure Remote Rendering service, in the form [region].mixedreality.azure.com.
    // [region] should be selected based on the region closest to the user. For example, westus2.mixedreality.azure.com or westeurope.mixedreality.azure.com.
    public string RemoteRenderingDomain;

    // Can use one of:
    // 1) ID and Key.
    // 2) ID and AuthenticationToken.
    // 3) ID and AccessToken.
    public string AccountId = Guid.Empty.ToString();
    public string AccountKey = string.Empty;
    public string AuthenticationToken = string.Empty;
    public string AccessToken = string.Empty;
}
```

Protějšek C++ vypadá takto:

```cpp
struct SessionConfiguration
{
    std::string AccountDomain{};
    std::string RemoteRenderingDomain{};
    std::string AccountId{};
    std::string AccountKey{};
    std::string AuthenticationToken{};
    std::string AccessToken{};
};
```

Pro část _oblasti_ v doméně použijte [oblast blízko sebe](../reference/regions.md).

Informace o účtu lze získat z portálu, jak je popsáno v odstavci [načtení informací o účtu](create-an-account.md#retrieve-the-account-information) .

## <a name="azure-frontend"></a>Azure front-end

Příslušné třídy jsou ```RemoteRenderingClient``` a ```RenderingSession``` . ```RemoteRenderingClient``` se používá ke správě účtů a funkcím na úrovni účtu, mezi které patří: převod assetů a vytváření relací vykreslování. ```RenderingSession``` se používá pro funkce na úrovni relace a zahrnuje: aktualizace relace, dotazy, obnovení a vyřazení z provozu.

Každý otevřený/vytvořený ```RenderingSession``` bude mít odkaz na front-end, který je vytvořil. Chcete-li provést čistě vypnutí, je nutné zrušit přidělení všech relací před tím, než dojde k uvolnění front-endu.

Zrušení přidělení relace nezastaví Server v Azure, `RenderingSession.StopAsync` musí se volat explicitně.

Jakmile je relace vytvořena a její stav byl označen jako připravený, může se připojit k modulu runtime vzdáleného vykreslování pomocí `RenderingSession.ConnectAsync` .

### <a name="threading"></a>Dělení na vlákna

Všechna asynchronní volání RenderingSession a RemoteRenderingClient jsou dokončena ve vlákně na pozadí, nikoli v hlavním vlákně aplikace.

### <a name="conversion-apis"></a>Rozhraní API pro převod

Další informace o službě převodu najdete v [REST API převodu modelů](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Spustit převod assetu

```cs
async void StartAssetConversion(RemoteRenderingClient client, string storageContainer, string blobinputpath, string bloboutpath, string modelName, string outputName)
{
    var result = await client.StartAssetConversionAsync(
        new AssetConversionInputOptions(storageContainer, blobinputpath, "", modelName),
        new AssetConversionOutputOptions(storageContainer, bloboutpath, "", outputName)
        );
}
```

```cpp
void StartAssetConversion(ApiHandle<RemoteRenderingClient> client, std::string storageContainer, std::string blobinputpath, std::string bloboutpath, std::string modelName, std::string outputName)
{
    AssetConversionInputOptions input;
    input.BlobContainerInformation.BlobContainerName = blobinputpath;
    input.BlobContainerInformation.StorageAccountName = storageContainer;
    input.BlobContainerInformation.FolderPath = "";
    input.InputAssetPath = modelName;

    AssetConversionOutputOptions output;
    output.BlobContainerInformation.BlobContainerName = blobinputpath;
    output.BlobContainerInformation.StorageAccountName = storageContainer;
    output.BlobContainerInformation.FolderPath = "";
    output.OutputAssetPath = outputName;

    client->StartAssetConversionAsync(input, output, [](Status status, ApiHandle<AssetConversionResult> result) {
        if (status == Status::OK)
        {
            //use result
        }
        else
        {
            printf("Failed to start asset conversion!");
        }
    });
}
```

#### <a name="get-conversion-status"></a>Získat stav převodu

```cs
async void GetConversionStatus(RemoteRenderingClient client, string assetId)
{
    AssetConversionStatusResult status = await client.GetAssetConversionStatusAsync(assetId);
    // do something with status (e.g. check current status etc.)
}
```

```cpp
void GetConversionStatus(ApiHandle<RemoteRenderingClient> client, std::string assetId)
{
    client->GetAssetConversionStatusAsync(assetId, [](Status status, ApiHandle<AssetConversionStatusResult> result) {
        if (status == Status::OK)
        {
            // do something with result (e.g. check current status etc.)
        }
        else
        {
            printf("Failed to get status of asset conversion!");
        }
    });
}

```


### <a name="rendering-apis"></a>Rozhraní API pro vykreslování

Podrobnosti o správě relací najdete v [REST API správy relací](session-rest-api.md) .

Relaci vykreslování lze buď vytvořit dynamicky na službě, nebo již existující ID relace lze otevřít do objektu RenderingSession.

#### <a name="create-rendering-session"></a>Vytvořit relaci vykreslování

```cs
async void CreateRenderingSession(RemoteRenderingClient client, RenderingSessionVmSize vmSize, int maxLeaseInMinutes)
{
    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationOptions(vmSize, maxLeaseInMinutes / 60, maxLeaseInMinutes % 60));

    // if the call was successful, result.Session holds a valid session reference, otherwise check result.Context for error information
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client, RenderingSessionVmSize vmSize, int maxLeaseInMinutes)
{
    RenderingSessionCreationOptions params;
    params.MaxLeaseInMinutes = maxLeaseInMinutes;
    params.Size = vmSize;
    client->CreateNewRenderingSessionAsync(params, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            result->GetSession();
            //use res->Result
        }
        else
        {
            printf("Failed to create session!");
        }
    });
}
```

#### <a name="open-an-existing-rendering-session"></a>Otevřít existující relaci vykreslování

Otevření existující relace je synchronní volání.

```cs
async void CreateRenderingSession(RemoteRenderingClient client, string sessionId)
{
    CreateRenderingSessionResult result = await client.OpenRenderingSessionAsync(sessionId);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // Query session status, etc.
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client, std::string sessionId)
{
    client->OpenRenderingSessionAsync(sessionId, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode()==Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // Query session status, etc.
        }
    });
}
```


#### <a name="get-current-rendering-sessions"></a>Získat aktuální relace vykreslování

```cs
async void GetCurrentRenderingSessions(RemoteRenderingClient client)
{
    RenderingSessionPropertiesArrayResult result = await client.GetCurrentRenderingSessionsAsync();
    if (result.ErrorCode == Result.Success)
    {
        RenderingSessionProperties[] properties = result.SessionProperties;
        // Query session status, etc.
    }
}
```

```cpp
void GetCurrentRenderingSessions(ApiHandle<RemoteRenderingClient> client)
{
    client->GetCurrentRenderingSessionsAsync([](Status status, ApiHandle<RenderingSessionPropertiesArrayResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            std::vector<RenderingSessionProperties> properties;
            result->GetSessionProperties(properties);
        }
        else
        {
            printf("Failed to get current rendering sessions!");
        }
    });
}
```

### <a name="session-apis"></a>Rozhraní API relace

#### <a name="get-rendering-session-properties"></a>Získat vlastnosti relace vykreslování

```cs
async void GetRenderingSessionProperties(RenderingSession session)
{
    RenderingSessionPropertiesResult result = await session.GetPropertiesAsync();
    if (result.ErrorCode == Result.Success)
    {
        RenderingSessionProperties properties = result.SessionProperties;
    }
    else
    {
        Console.WriteLine("Failed to get properties of session!");
    }
}
```

```cpp
void GetRenderingSessionProperties(ApiHandle<RenderingSession> session)
{
    session->GetPropertiesAsync([](Status status, ApiHandle<RenderingSessionPropertiesResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            RenderingSessionProperties properties = result->GetSessionProperties();
        }
        else
        {
            printf("Failed to get properties of session!");
        }
    });
}
```

#### <a name="update-rendering-session"></a>Relace vykreslování aktualizace

```cs
async void UpdateRenderingSession(RenderingSession session, int updatedLeaseInMinutes)
{
    SessionContextResult result = await session.RenewAsync(
        new RenderingSessionUpdateOptions(updatedLeaseInMinutes / 60, updatedLeaseInMinutes % 60));
    if (result.ErrorCode == Result.Success)
    {
        Console.WriteLine("Rendering session renewed succeeded!");
    }
    else
    {
        Console.WriteLine("Failed to renew rendering session!");
    }
}
```

```cpp
void UpdateRenderingSession(ApiHandle<RenderingSession> session, int updatedLeaseInMinutes)
{
    RenderingSessionUpdateOptions params;
    params.MaxLeaseInMinutes = updatedLeaseInMinutes;
    session->RenewAsync(params, [](Status status, ApiHandle<SessionContextResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            printf("Rendering session renewed succeeded!");
        }
        else
        {
            printf("Failed to renew rendering session!");
        }
    });
}
```

#### <a name="stop-rendering-session"></a>Zastavit relaci vykreslování

```cs
async void StopRenderingSession(RenderingSession session)
{
    SessionContextResult result = await session.StopAsync();
    if (result.ErrorCode == Result.Success)
    {
        Console.WriteLine("Rendering session stopped successfully!");
    }
    else
    {
        Console.WriteLine("Failed to stop rendering session!");
    }
}
```

```cpp
void StopRenderingSession(ApiHandle<RenderingSession> session)
{
    session->StopAsync([](Status status, ApiHandle<SessionContextResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            printf("Rendering session stopped successfully!");
        }
        else
        {
            printf("Failed to stop rendering session!");
        }
    });
}
```

#### <a name="connect-to-arr-inspector"></a>Připojit ke kontrole ARR

```cs
async void ConnectToArrInspector(RenderingSession session)
{
    string htmlPath = await session.ConnectToArrInspectorAsync();
#if WINDOWS_UWP
    UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
    {
        var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
        await Windows.System.Launcher.LaunchFileAsync(file);
    }, true);
#else
    InvokeOnAppThreadAsync(() =>
        {
            System.Diagnostics.Process.Start("file:///" + htmlPath);
        });
#endif
}
```

```cpp
void ConnectToArrInspector(ApiHandle<RenderingSession> session)
{
    session->ConnectToArrInspectorAsync([](Status status, std::string result) {
        if (status == Status::OK)
        {
            // Launch the html file with default browser
            std::string htmlPath = "file:///" + result;
            ShellExecuteA(NULL, "open", htmlPath.c_str(), NULL, NULL, SW_SHOWDEFAULT);
        }
        else
        {
            printf("Failed to connect to ARR inspector!");
        }
    });
}
```

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu](create-an-account.md)
* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)
