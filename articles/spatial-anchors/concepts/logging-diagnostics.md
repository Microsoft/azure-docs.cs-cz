---
title: Protokolování a diagnostika
description: Podrobné vysvětlení, jak generovat a načítat protokolování a diagnostiku v prostorových kotvách Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270132"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Protokolování a diagnostika v prostorových kotvách Azure

Azure Spatial Anchors poskytuje standardní mechanismus protokolování, který je užitečný pro vývoj aplikací. Režim protokolování diagnostiky prostorových kotev je užitečný, když potřebujete další informace pro ladění. Protokolování diagnostiky ukládá obrazy prostředí.

## <a name="standard-logging"></a>Standardní protokolování
V rozhraní API prostorových ukotvení se můžete přihlásit k odběru mechanismu protokolování a získat užitečné protokoly pro vývoj aplikací a ladění. Standardní rozhraní API protokolování neukládají obrázky prostředí na disk zařízení. Sada SDK poskytuje tyto protokoly jako zpětná volání událostí. Je na vás, abyste tyto protokoly integrovali do mechanismu protokolování aplikace.

### <a name="configuration-of-log-messages"></a>Konfigurace protokolových zpráv
Existují dvě zpětná volání zájmu pro uživatele. Následující ukázka ukazuje, jak nakonfigurovat relaci.

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

### <a name="events-and-properties"></a>Události a vlastnosti

Tato zpětná volání událostí jsou k dispozici ke zpracování protokolů a chyb z relace:

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Určuje úroveň podrobností pro události, které mají být přijímány z běhu.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Poskytuje standardní události protokolu ladění.
- [Chyba](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Poskytuje události protokolu, které za chyby považuje za chyby.

## <a name="diagnostics-logging"></a>Protokolování diagnostiky

Kromě standardního režimu provozu pro protokolování, prostorové kotvy má také diagnostický režim. Režim diagnostiky zachycuje obrazy prostředí a zaznamenává je na disk. Tento režim můžete použít k ladění určitých druhů problémů, jako je selhání předvídatelně vyhledejte kotvu. Povolte protokolování diagnostiky pouze pro reprodukci konkrétního problému. Pak ji vypněte. Nepovolujte diagnostiku, když aplikace normálně používáte.

Během interakce podpory se společností Microsoft se může zástupce společnosti Microsoft zeptat, zda jste ochotni odeslat sadu diagnostiky k dalšímu šetření. V takovém případě se můžete rozhodnout povolit diagnostiku a reprodukovat problém, abyste mohli odeslat diagnostický balíček.

Pokud společnosti Microsoft odešlete diagnostický protokol bez předchozího potvrzení od zástupce společnosti Microsoft, příspěvek zůstane nezodpovězen.

Následující části ukazují, jak povolit režim diagnostiky a také jak odeslat protokoly diagnostiky společnosti Microsoft.

### <a name="enable-diagnostics-logging"></a>Povolení diagnostického protokolování

Pokud povolíte relaci pro protokolování diagnostiky, všechny operace v relaci mají odpovídající protokolování diagnostiky v místním systému souborů. Během protokolování jsou na disk uloženy obrázky prostředí.

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

### <a name="submit-the-diagnostics-bundle"></a>Odeslat balíček diagnostiky

Následující fragment kódu ukazuje, jak odeslat sadu diagnostiky společnosti Microsoft. Tato sada bude obsahovat obrázky prostředí zachycené relace po povolení diagnostiky.

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

### <a name="parts-of-a-diagnostics-bundle"></a>Části diagnostického svazku
Balíček diagnostiky může obsahovat následující informace:

- **Snímky klíčových snímků**: Obrázky prostředí zachycené během relace během diagnostiky byly povoleny.
- **Protokoly**: Protokolovat události zaznamenané za běhu.
- **Metadata relace**: Metadata, která identifikují relaci.
