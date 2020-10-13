---
title: Rozhraní API Azure front-endu pro ověřování
description: Vysvětluje, jak používat C# front-endové rozhraní API pro ověřování.
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 420ff7ed838bc9fa14c1276ae0a70220fc7e11a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90024056"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Použití rozhraní API front-endu Azure pro ověřování

V této části popíšeme, jak používat rozhraní API pro ověřování a správu relací.

> [!CAUTION]
> Funkce popsané v této kapitole vydávají interní volání REST na serveru. Stejně jako u všech volání REST odesílají tyto příkazy příliš často, což způsobí, že server bude omezovat a vracet chybu nakonec. Hodnota `SessionGeneralContext.HttpResponseCode` člena v tomto případě je 429 (příliš mnoho požadavků). Jako pravidlo pro palec by se měla **mezi následnými voláními čekat 5-10 sekund**.


## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo se používá k nastavení ověřovacích informací pro ```AzureFrontend``` instanci v sadě SDK.

Důležitá jsou tato pole:

```cs

public class AzureFrontendAccountInfo
{
    // Something akin to "<region>.mixedreality.azure.com"
    public string AccountDomain;

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
struct AzureFrontendAccountInfo
{
    std::string AccountDomain{};
    std::string AccountId{};
    std::string AccountKey{};
    std::string AuthenticationToken{};
    std::string AccessToken{};
};
```

Pro část _oblasti_ v doméně použijte [oblast blízko sebe](../reference/regions.md).

Informace o účtu lze získat z portálu, jak je popsáno v odstavci [načtení informací o účtu](create-an-account.md#retrieve-the-account-information) .

## <a name="azure-frontend"></a>Azure front-end

Příslušné třídy jsou ```AzureFrontend``` a ```AzureSession``` . ```AzureFrontend``` se používá ke správě účtů a funkcím na úrovni účtu, mezi které patří: převod assetů a vytváření relací vykreslování. ```AzureSession``` se používá pro funkce na úrovni relace a zahrnuje: aktualizace relace, dotazy, obnovení a vyřazení z provozu.

Každý otevřený/vytvořený ```AzureSession``` bude mít odkaz na front-end, který je vytvořil. Chcete-li provést čistě vypnutí, je nutné zrušit přidělení všech relací před tím, než dojde k uvolnění front-endu.

Zrušení přidělení relace nezastaví Server v Azure, `AzureSession.StopAsync` musí se volat explicitně.

Jakmile je relace vytvořena a její stav byl označen jako připravený, může se připojit k modulu runtime vzdáleného vykreslování pomocí `AzureSession.ConnectToRuntime` .

### <a name="threading"></a>Dělení na vlákna

Všechna asynchronní volání AzureSession a AzureFrontend jsou dokončena ve vlákně na pozadí, nikoli v hlavním vlákně aplikace.

### <a name="conversion-apis"></a>Rozhraní API pro převod

Další informace o službě převodu najdete v [REST API převodu modelů](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Spustit převod assetu

```cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string storageContainer, string blobinputpath, string bloboutpath, string modelName, string outputName)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionInputParams(storageContainer, blobinputpath, "", modelName),
        new AssetConversionOutputParams(storageContainer, bloboutpath, "", outputName)
        );
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

```cpp
void StartAssetConversion(ApiHandle<AzureFrontend> frontend, std::string storageContainer, std::string blobinputpath, std::string bloboutpath, std::string modelName, std::string outputName)
{
    AssetConversionInputParams input;
    input.BlobContainerInformation.BlobContainerName = blobinputpath;
    input.BlobContainerInformation.StorageAccountName = storageContainer;
    input.BlobContainerInformation.FolderPath = "";
    input.InputAssetPath = modelName;

    AssetConversionOutputParams output;
    output.BlobContainerInformation.BlobContainerName = blobinputpath;
    output.BlobContainerInformation.StorageAccountName = storageContainer;
    output.BlobContainerInformation.FolderPath = "";
    output.OutputAssetPath = outputName;

    ApiHandle<StartAssetConversionAsync> conversionAsync = *frontend->StartAssetConversionAsync(input, output);
    conversionAsync->Completed([](ApiHandle<StartAssetConversionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res.Result
        }
        else
        {
            printf("Failed to start asset conversion!");
        }
    }
    );
}
```


#### <a name="get-conversion-status"></a>Získat stav převodu

```cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetAssetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

```cpp
void GetConversionStatus(ApiHandle<AzureFrontend> frontend, std::string assetId)
{
    ApiHandle<ConversionStatusAsync> pendingAsync = *frontend->GetAssetConversionStatusAsync(assetId);
    pendingAsync->Completed([](ApiHandle<ConversionStatusAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            // use res->Result
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

Relaci vykreslování lze buď vytvořit dynamicky na službě, nebo již existující ID relace lze otevřít do objektu AzureSession.

#### <a name="create-rendering-session"></a>Vytvořit relaci vykreslování

```cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, RenderingSessionVmSize vmSize, const ARRTimeSpan& maxLease)
{
    RenderingSessionCreationParams params;
    params.MaxLease = maxLease;
    params.Size = vmSize;
    ApiHandle<CreateSessionAsync> pendingAsync = *frontend->CreateNewRenderingSessionAsync(params);

    pendingAsync->Completed([] (ApiHandle<CreateSessionAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
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
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, std::string sessionId)
{
    ApiHandle<AzureSession> session = *frontend->OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```


#### <a name="get-current-rendering-sessions"></a>Získat aktuální relace vykreslování

```cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void GetCurrentRenderingSessions(ApiHandle<AzureFrontend> frontend)
{
    ApiHandle<SessionPropertiesArrayAsync> pendingAsync = *frontend->GetCurrentRenderingSessionsAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesArrayAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            // use res.Result
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
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void GetRenderingSessionProperties(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionPropertiesAsync> pendingAsync = *session->GetPropertiesAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res.Result
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
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void UpdateRenderingSession(ApiHandle<AzureSession> session, const ARRTimeSpan& updatedLease)
{
    RenderingSessionUpdateParams params;
    params.MaxLease = updatedLease;
    ApiHandle<SessionAsync> pendingAsync = *session->RenewAsync(params);
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->GetIsRanToCompletion())
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
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void StopRenderingSession(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionAsync> pendingAsync = *session->StopAsync();
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->GetIsRanToCompletion())
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
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
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
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

```cpp
void ConnectToArrInspector(ApiHandle<AzureSession> session, std::string hostname)
{
    ApiHandle<ArrInspectorAsync> pendingAsync = *session->ConnectToArrInspectorAsync(hostname);
    pendingAsync->Completed([](ApiHandle<ArrInspectorAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            // Launch the html file with default browser
            std::string htmlPath = "file:///" + *res->Result();
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
