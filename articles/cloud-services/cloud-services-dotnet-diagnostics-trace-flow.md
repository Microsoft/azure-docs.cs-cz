---
title: Sledování toku v aplikaci cloudových služeb pomocí diagnostiky Azure
titleSuffix: Azure Cloud Services
description: Přidejte do aplikace Azure trasovací zprávy, které vám pomůžou ladit, měřit výkon, monitorovat, analýzu provozu a další.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: tagore
ms.openlocfilehash: 47a33ba27dd6d2df626d93695c421303bace6a0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386506"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Sledování toku aplikace cloudových služeb pomocí Azure Diagnostics
Trasování je způsob, jak sledovat provádění aplikace, zatímco je spuštěna. Třídy [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug)a [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) můžete použít k zaznamenání informací o chybách a spuštění aplikace v protokolech, textových souborech nebo jiných zařízeních pro pozdější analýzu. Další informace o trasování naleznete v [tématu Trasování a instrumentační aplikace](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Použití příkazů trasování a přepínačů trasování
Implementujte trasování v aplikaci cloudových služeb přidáním [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) do konfigurace aplikace a voláním System.Diagnostics.Trace nebo System.Diagnostics.Debug v kódu aplikace. Konfigurační soubor *app.config* pro role pracovního procesu a *web.config* pro webové role. Když vytvoříte novou hostovanou službu pomocí šablony Sady Visual Studio, diagnostika Azure se automaticky přidá do projektu a DiagnosticMonitorTraceListener se přidá do příslušného konfiguračního souboru pro role, které přidáte.

Informace o umístění příkazů trasování naleznete v [tématu How to: Add Trace Statements to Application Code](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Umístěním [přepínačů trasování](/dotnet/framework/debug-trace-profile/trace-switches) do kódu můžete určit, zda dojde k trasování a jak rozsáhlé je. To umožňuje sledovat stav aplikace v provozním prostředí. To je obzvláště důležité v obchodní aplikaci, která používá více součástí spuštěných ve více počítačích. Další informace naleznete v [tématu How to: Configure Trace Switches](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurace naslouchací proces trasování v aplikaci Azure
Trasování, Ladění a TraceSource, vyžadují nastavit "naslouchací procesy" shromažďovat a zaznamenávat zprávy, které jsou odesílány. Naslouchací procesy shromažďují, ukládají a směrují trasování zpráv. Nasměrují výstup trasování na příslušný cíl, například na protokol, okno nebo textový soubor. Diagnostika Azure používá třídu [DiagnosticMonitorTraceListener.](/previous-versions/azure/reference/ee758610(v=azure.100))

Před dokončením následujícího postupu je nutné inicializovat diagnostické monitorování Azure. Chcete-li to provést, [přečtěte si téma Povolení diagnostiky v Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Všimněte si, že pokud používáte šablony, které jsou poskytovány visual studio, konfigurace naslouchací proces je přidán automaticky za vás.

### <a name="add-a-trace-listener"></a>Přidání naslouchací proces trasování
1. Otevřete soubor web.config nebo app.config pro svou roli.
2. Přidejte do souboru následující kód. Změňte atribut Version tak, aby používal číslo verze sestavení, na které odkazujete. Verze sestavení se nemusí nutně měnit s každou verzí sady Azure SDK, pokud nejsou k dispozici aktualizace.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Ujistěte se, že máte odkaz na projekt na sestavení Microsoft.WindowsAzure.Diagnostics. Aktualizujte číslo verze ve výše uvedeném xml tak, aby odpovídalo verzi odkazovaného sestavení Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Uložte konfigurační soubor.

Další informace o naslouchacích procesech naleznete v tématu [Trace Listeners](/dotnet/framework/debug-trace-profile/trace-listeners).

Po dokončení kroků k přidání naslouchací proces, můžete přidat příkazy trasování do kódu.

### <a name="to-add-trace-statement-to-your-code"></a>Přidání příkazu trasování do kódu
1. Otevřete zdrojový soubor pro vaši aplikaci. Například soubor \<RoleName>.cs pro roli pracovního procesu nebo webovou roli.
2. Přidejte následující using direktivu, pokud ještě nebyla přidána:
    ```
        using System.Diagnostics;
    ```
3. Přidejte příkazy trasování, kde chcete zachytit informace o stavu aplikace. Můžete použít různé metody k formátování výstupu Trace prohlášení. Další informace naleznete v [tématu How to: Add Trace Statements to Application Code](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Uložte zdrojový soubor.




