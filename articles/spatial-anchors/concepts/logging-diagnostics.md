---
title: Protokolování a Diagnostika v Azure prostorových kotvy | Dokumentace Microsoftu
description: Podrobnější vysvětlení toho, jak vygenerovat a načíst protokolování a Diagnostika v Azure prostorových ukotvení.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: b66dc7d6ec9d11fe645587fe791824009231b7c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964754"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Protokolování a Diagnostika v Azure prostorových kotvy

Azure prostorových kotvy poskytuje mechanismus standardní protokolování, které jsou užitečné pro vývoj aplikací. Režim protokolování diagnostiky prostorových kotvy je užitečné, když budete potřebovat další informace pro ladění. Protokolování diagnostiky ukládá obrázky prostředí.

## <a name="standard-logging"></a>Standardní protokolování
V rozhraní API prostorových kotev vztahů k odběru mechanismus na protokolování získat užitečné protokoly pro vývoj aplikací a ladění. Standardní protokolování rozhraní API neukládejte pořizovat snímky prostředí na disk zařízení. Sada SDK poskytuje tyto protokoly jako zpětná volání události. Je to na můžete integrovat tyto protokoly do mechanismu protokolování aplikace.

### <a name="configuration-of-log-messages"></a>Konfigurace protokolu zpráv
Existují dvě zpětná volání zajímavé pro uživatele. Následující příklad ukazuje postup při konfiguraci relace.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>Vlastnosti a události

Tato zpětná volání události jsou k dispozici pro zpracování chyb z relace a protokolování:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Určuje úroveň podrobností pro události, které mají přijímat z modulu runtime.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Poskytuje standardní ladění protokolu událostí.
- [Chyba:](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error) Poskytuje události protokolu, které modul runtime bere v úvahu k dojít k chybám.

## <a name="diagnostics-logging"></a>Protokolování diagnostiky

Kromě standardní režim operaci pro protokolování prostorových kotvy má také diagnostickém režimu. Režim Diagnostika zachytí Image prostředí a protokoly na disk. V tomto režimu můžete použít k ladění určité druhy problémů, jako je selhání předvídatelně najít ukotvení. Povolte diagnostiku protokolování pouze pro reprodukci konkrétního problému. Potom jej vypněte. Není povolte diagnostiku, obvykle pomocí vašich aplikací.

V průběhu podporu interakce s Microsoftem zástupce společnosti Microsoft požádat, pokud jste ochotní odeslání diagnostiky sady pro další zkoumání. V takovém případě můžete rozhodnout k povolení diagnostiky a reprodukujte problém, můžete odeslat diagnostické sady. 

Pokud odešlete diagnostický protokol společnosti Microsoft bez předchozího potvrzení od zástupce společnosti Microsoft, přejdete nezodpovězené odeslání.

Následující části vysvětlují, jak povolit režim Diagnostika a také jak odeslat diagnostické protokoly do Microsoftu.

### <a name="enable-diagnostics-logging"></a>Povolení diagnostického protokolování

Když povolíte relace pro diagnostiku protokolování, všechny operace v relaci mít odpovídající Diagnostika protokolování v místním systému souborů. Během protokolování, bitové kopie prostředí se uloží na disk.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>Odeslání sady diagnostiky

Následující fragment kódu ukazuje, jak odeslat diagnostiky sady pro Microsoft. Tato sada bude obsahovat obrázky prostředí, které jsou zachyceny relace po povolení diagnostiky. 

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>Součástí sady diagnostiky
Diagnostika sady prostředků může obsahovat následující informace:

- **Klíčový snímek imagí**: Bitové kopie prostředí zaznamenána během relace, zatímco byla povolená Diagnostika.
- **Protokoly**: Protokol událostí zaznamenaných tímto modulem.
- **Relace metadat**: Metadata, která identifikuje relaci.
