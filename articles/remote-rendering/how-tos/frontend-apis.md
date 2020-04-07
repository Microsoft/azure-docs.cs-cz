---
title: Azure Front-end ová API pro ověřování
description: Vysvětluje, jak používat rozhraní API front-end jazyka C# pro ověřování.
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: 04296a3dab61fdb569126abc1bc1f975d69e226d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681347"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>Použití api pro front-end Azure pro ověřování

V této části popíšeme, jak používat rozhraní API jazyka C# pro ověřování.

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo se používá k nastavení ověřovacích informací pro ```AzureFrontend``` instanci v sadě SDK.

Důležitými poli jsou:

```cs

    public class AzureFrontendAccountInfo
    {
        // Something akin to "<region>.mixedreality.azure.com"
        public string AccountDomain;

        // Can use one of:
        // 1) ID and Key.
        // 2) AuthenticationToken.
        // 3) AccessToken.
        public string AccountId = Guid.Empty.ToString();
        public string AccountKey = string.Empty;
        public string AuthenticationToken = string.Empty;
        public string AccessToken = string.Empty;
    }

```

Pro _oblast_ ovou část v doméně použijte [oblast ve vašem okolí](../reference/regions.md).

Informace o účtu lze získat z portálu, jak je popsáno v odstavci [informace o načtení účtu.](create-an-account.md#retrieve-the-account-information)

## <a name="azure-frontend"></a>Azure Front-end

Příslušné třídy ```AzureFrontend``` ```AzureSession```jsou a . ```AzureFrontend```se používá pro správu účtu a funkce na úrovni účtu, která zahrnuje: převod majetku a vytváření relací. ```AzureSession```používá se pro funkce na úrovni relace a zahrnuje: aktualizaci relace, dotazy, obnovení a vyřazení z provozu.

Každý otevřený/vytvořený ```AzureSession``` bude mít odkaz na front-end, který je vytvořen. Chcete-li čistě vypnout, všechny relace musí být přiděleny před front-end bude deallocated.

Zrušení přidělení relace nezastaví virtuální počítač v Azure, `AzureSession.StopAsync` musí být explicitně volána.

Po vytvoření relace a její stav byl označen jako připravený, může se `AzureSession.ConnectToRuntime`připojit ke vzdálenému vykreslování runtime s .

### <a name="threading"></a>Dělení na vlákna

Všechna asynchronní volání AzureSession a AzureFrontend jsou dokončena ve vlákně na pozadí, nikoli v hlavním vlákně aplikace.

### <a name="conversion-apis"></a>Předávací prostředí převodu

Další informace o službě převodu naleznete [v rozhraní REST API pro převod modelu](conversion/conversion-rest-api.md).

#### <a name="start-asset-conversion"></a>Zahájit převod majetku

``` cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string modelName, string modelUrl, string assetContainerUrl)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionParams(modelName, modelUrl, assetContainerUrl));
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

#### <a name="get-conversion-status"></a>Získání stavu konverze

``` cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetConversionStatusAsync(assetId);
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

### <a name="rendering-apis"></a>Vykreslovací api

Podrobnosti o [správě relací](session-rest-api.md) najdete v rozhraní REST API pro správu relací.

Relace vykreslování lze vytvořit dynamicky ve službě nebo již existující ID relace lze "otevřít" do objektu AzureSession.

#### <a name="create-rendering-session"></a>Vytvořit relaci vykreslování

``` cs
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

#### <a name="open-an-existing-rendering-session"></a>Otevření existující relace vykreslování

Otevření existující relace je synchronní volání.

``` cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

#### <a name="get-current-rendering-sessions"></a>Získat aktuální relace vykreslování

``` cs
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

### <a name="session-apis"></a>Api relace

#### <a name="get-rendering-session-properties"></a>Získání vlastností relace vykreslování

``` cs
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

#### <a name="update-rendering-session"></a>Aktualizovat relaci vykreslování

``` cs
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

#### <a name="stop-rendering-session"></a>Ukončení relace vykreslování

``` cs
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

#### <a name="connect-to-arr-inspector"></a>Připojení k inspektoru ARR

``` cs
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

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu](create-an-account.md)
* [Příklad skriptů prostředí PowerShell](../samples/powershell-example-scripts.md)
