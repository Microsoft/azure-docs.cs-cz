---
title: Protokolování a diagnostika
description: Podrobné vysvětlení, jak generovat a načíst protokolování a diagnostiku v prostorových ukotveních Azure.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 45d345e8d8dbe45ecb4873904f31141111827647
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097350"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Protokolování a diagnostika v prostorových ukotveních Azure

Prostorové kotvy Azure poskytují standardní mechanismus protokolování, který je vhodný pro vývoj aplikací. Režim protokolování diagnostiky prostorových ukotvení je užitečný, pokud potřebujete další informace pro ladění. Protokolování diagnostiky ukládá image prostředí.

## <a name="standard-logging"></a>Standardní protokolování
V rozhraní API prostorových ukotvení se můžete přihlásit k odběru mechanismu protokolování, abyste získali užitečné protokoly pro vývoj a ladění aplikací. Rozhraní API pro standardní protokolování neukládají obrázky prostředí na disku zařízení. Sada SDK poskytuje tyto protokoly jako zpětná volání událostí. Tyto protokoly můžete integrovat do mechanismu protokolování aplikace.

### <a name="configuration-of-log-messages"></a>Konfigurace zpráv protokolu
Existují dvě zpětná volání, která jsou zajímavá pro uživatele. Následující příklad ukazuje, jak konfigurovat relaci.

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

Tato zpětná volání událostí jsou k dispozici pro zpracování protokolů a chyb z relace:

- [LogLevel](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Určuje úroveň podrobností pro události, které mají být přijímány z modulu runtime.
- [OnLogDebug](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): poskytuje standardní události protokolu ladění.
- [Chyba](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): poskytuje protokolové události, které modul runtime považuje za chyby.

## <a name="diagnostics-logging"></a>Protokolování diagnostiky

Kromě standardního režimu provozu pro protokolování má prostorové kotvy také režim diagnostiky. Režim diagnostiky zachycuje image prostředí a zapisuje je na disk. Tento režim můžete použít k ladění určitých druhů problémů, jako je selhání prediktivního vyhledání kotvy. Povolte protokolování diagnostiky jenom pro reprodukování konkrétního problému. Pak ho zakažte. Nepovolujte diagnostiku, pokud používáte aplikace normálně.

Při spolupráci s Microsoftem se může zástupce Microsoftu zeptat, jestli máte ochotni odeslat sadu diagnostiky k dalšímu prošetření. V takovém případě se můžete rozhodnout povolit diagnostiku a reprodukování problému, abyste mohli balíček diagnostiky odeslat.

Pokud odešlete diagnostický protokol společnosti Microsoft bez předchozího potvrzení od zástupce společnosti Microsoft, odeslání příspěvku bude nezodpovězeno.

V následujících částech se dozvíte, jak povolit režim diagnostiky a také jak odeslat protokoly diagnostiky společnosti Microsoft.

### <a name="enable-diagnostics-logging"></a>Povolení diagnostického protokolování

Pokud povolíte relaci pro protokolování diagnostiky, všechny operace v relaci mají odpovídající protokolování diagnostiky v místním systému souborů. Během protokolování se image prostředí uloží na disk.

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

Následující fragment kódu ukazuje, jak odeslat diagnostické sady společnosti Microsoft. Tato sada bude obsahovat image prostředí zachycené relací po povolení diagnostiky.

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

### <a name="parts-of-a-diagnostics-bundle"></a>Části sady diagnostiky
Sada diagnostiky může obsahovat následující informace:

- **Obrázky klíčového snímku**: image prostředí zaznamenaného během relace v době, kdy byly povoleny diagnostiky.
- **Protokoly**: protokoluje události zaznamenané modulem runtime.
- **Metadata relace**: metadata identifikující relaci.