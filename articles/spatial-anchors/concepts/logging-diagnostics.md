---
title: Protokolování a Diagnostika v Azure prostorových kotvy | Dokumentace Microsoftu
description: Podrobnější vysvětlení toho, jak vygenerovat a načíst protokolování a Diagnostika v Azure prostorových ukotvení.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: d9377e2b5b66a7d426373a8a85e4880dafeaeee6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753001"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Protokolování a Diagnostika v Azure prostorových kotvy

Azure prostorových kotvy poskytuje mechanismus standardní protokolování užitečné pro vývoj aplikací. Kromě toho je režim protokolování diagnostiky užitečné, když jsou požadovány pro ladění i další informace. Protokolování diagnostiky zahrnuje ukládání imagí prostředí.

## <a name="standard-logging-in-azure-spatial-anchors"></a>Standardní protokolování v Azure prostorových kotvy
Rozhraní API Azure prostorových kotvy poskytuje mechanismus pro protokolování aplikací mohou přihlásit k odběru pro příjem protokolů užitečné pro vývoj aplikací a ladění. Standardní protokolování rozhraní API není zachována pořizovat snímky prostředí tak, aby disk zařízení. Sada SDK poskytuje tyto protokoly jako zpětná volání události. Je to na můžete integrovat tyto protokoly do mechanismu protokolování aplikace.

### <a name="how-to-configure-the-log-messages"></a>Jak nakonfigurovat zprávy protokolu
Existují dvě zpětná volání zajímavé pro uživatele. V následující ukázce uvidíte postup konfigurace relace.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events--properties"></a>Akce a vlastnosti

Zpětná volání událostí ke zpracování chyby z relace a protokolování k dispozici.

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): Určuje úroveň podrobností pro události, které mají přijímat z modulu runtime.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): Tato událost zpětného volání obsahuje standardní ladění protokolu událostí.
- [Chyba](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): Tato událost zpětného volání obsahuje události protokolu uznán chyby modulu runtime.

## <a name="diagnostics-logging-in-azure-spatial-anchors"></a>Diagnostika protokolování v Azure prostorových kotvy

Kromě standardní režim operaci pro protokolování, které bylo uvedeno výše prostorová kotvy Azure má také diagnostickém režimu, který vývojáři budou moct zvolit. Diagnostika zachytí Image prostředí a protokoly na disk. Tento režim je užitečný pro ladění určité druhy problémů, jako je, když nejste schopni předvídatelně vyhledejte ukotvení. Povolte pouze Diagnostika protokolování pro reprodukci konkrétního problému a poté ho zakážete. Nelze spustit vaše aplikace obvykle s povolenou diagnostikou.

V průběhu podporu interakce s Microsoftem zástupce společnosti Microsoft požádat, pokud jste ochotní odeslání diagnostiky sady Microsoftu o pomoc. V takovém případě můžete rozhodnout k povolení diagnostiky, reprodukujte problém a odesílání diagnostiky sady Microsoftu o pomoc. Diagnostické protokoly se odeslaly do společnosti Microsoft bez předchozího potvrzení zástupcem společnosti Microsoft, půjdou nezodpovězené.

Následující fragmenty kódu ukazují, jak povolit režim Diagnostika a také jak můžete odeslat diagnostické protokoly do Microsoftu.

### <a name="enabling-diagnostics-logging"></a>Povolení diagnostického protokolování

Relaci je povolen pro protokolování diagnostiky, budou všechny operace v relaci mít odpovídající Diagnostika protokolování v místním systému souborů. Protokolování zahrnuje ukládání imagí prostředí na disk.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // setup the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // Opt-in to diagnostics logging of environment images.
    // If this is enabled, the diagnostics bundle will include images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submitting-the-diagnostic-bundle"></a>Odesílání diagnostiky sady prostředků

Následující fragment kódu ukazuje, jak odeslat diagnostiky sady pro Microsoft. Poznámka: Tato obsahuje Image prostředí nezachytává relace po povolení diagnostiky. Diagnostika sady odeslána společnosti Microsoft bez předchozího potvrzení zástupcem společnosti Microsoft navíc půjdou nezodpovězené.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest  to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="anatomy-of-the-diagnostics-bundle"></a>Anatomie balíčku diagnostiky
Tyto informace mohou být přítomny v sadě diagnostiky:

- Klíčový snímek Image - bitové kopie prostředí zaznamenána během relace, zatímco byla povolená Diagnostika.
- Protokoly – protokol událostí zaznamenaných tímto modulem.
- Metadata relace – metadata, která identifikuje relaci.
