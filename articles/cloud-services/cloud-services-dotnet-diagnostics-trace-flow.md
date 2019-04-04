---
title: Sledování toku v aplikaci cloudové služby pomocí diagnostiky Azure | Dokumentace Microsoftu
description: Přidáte zprávy trasování do aplikací Azure usnadňují ladění, měření výkonu, monitorování, analýza provozu a další.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: jeconnoc
ms.openlocfilehash: f0724fd6e5f08f3e09bcb147c12d1657235dc704
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916882"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Trasování toku aplikace Cloud Services s využitím Azure Diagnostics
Trasování je způsob, jak můžete monitorovat provádění aplikace během jejího běhu. Můžete použít [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace), [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug), a [System.Diagnostics.TraceSource](/dotnet/api/system.diagnostics.tracesource) třídy k zaznamenání informací o chybách a spuštění aplikace v protokolech, textové soubory nebo jiná pro pozdější analýzu. Další informace o trasování najdete v tématu [trasování a instrumentace aplikací](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Pomocí příkazů trasování a přepínačů trasování
Implementace trasování aplikace Cloud Services tak, že přidáte [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) ke konfiguraci aplikace a volání System.Diagnostics.Trace nebo System.Diagnostics.Debug ve vaší kód aplikace. Použijte konfigurační soubor *app.config* rolí pracovních procesů a *web.config* pro webové role. Když vytvoříte novou hostovanou službu pomocí šablony sady Visual Studio, Azure Diagnostics se automaticky přidá do projektu a DiagnosticMonitorTraceListener se přidá do příslušného konfiguračního souboru pro role, které přidáte.

Informace o umístění příkazů trasování najdete v tématu [jak: Přidání příkazů trasování do kódu aplikace](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Tím, že umístíte [přepínačů trasování](/dotnet/framework/debug-trace-profile/trace-switches) ve vašem kódu, můžete řídit, jestli dojde k trasování a jak rozsáhlé je. To vám umožňuje monitorovat stav vaší aplikace v produkčním prostředí. To je obzvláště důležité v obchodních aplikací, které používá více součástí, které běží na několika počítačích. Další informace najdete v tématu [jak: Konfigurace přepínačů trasování](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurace naslouchacího procesu trasování v aplikaci Azure
Trasování, ladění a TraceSource, vyžadují nastavení "naslouchacích procesů" ke shromáždění a zaznamenání zprávy, které se odesílají. Naslouchací procesy shromažďování, ukládání a směrovat trasovací zprávy. Jejich přímý výstup trasování příslušný cíli, jako je protokol, okno nebo textový soubor. Pomocí diagnostiky Azure [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) třídy.

Před provedením následujícího postupu, musí se inicializovat Azure monitorování diagnostiky. Chcete-li to provést, najdete v článku [povolení diagnostiky v Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Všimněte si, že pokud použijete šablony, které jsou k dispozici v sadě Visual Studio, konfigurace naslouchacího procesu je automaticky přidán za vás.

### <a name="add-a-trace-listener"></a>Přidat naslouchací proces trasování
1. Otevřete soubor web.config nebo app.config pro vaši roli.
2. Přidejte následující kód do souboru. Změňte atribut verze, který chcete použít číslo verze sestavení, na které odkazujete. Verze sestavení nezmění nutně s každou vydanou verzí sady Azure SDK pouze v případě aktualizace.
   
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
   > Ujistěte se, že máte odkaz na sestavení Microsoft.WindowsAzure.Diagnostics. Aktualizujte číslo verze v souboru xml výše uvedené tak, aby odpovídala verzi odkazované sestavení Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Uložte konfigurační soubor.

Další informace o naslouchacích procesů, naleznete v tématu [naslouchacích procesů trasování](/dotnet/framework/debug-trace-profile/trace-listeners).

Po dokončení kroků pro přidání naslouchacího procesu, je přidání příkazů trasování do kódu.

### <a name="to-add-trace-statement-to-your-code"></a>Chcete-li přidat příkaz trasování kódu
1. Otevřete zdrojový soubor pro vaši aplikaci. Například <RoleName>.cs soubor pro webové role nebo role pracovního procesu.
2. Přidejte následující příkaz using, pokud nebyl již přidán:
    ```
        using System.Diagnostics;
    ```
3. Přidání příkazů trasování, ve kterém chcete zaznamenat informace o stavu vaší aplikace. Můžete formátovat výstup příkazu trasování můžete použít různé metody. Další informace najdete v tématu [jak: Přidání příkazů trasování do kódu aplikace](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Zdrojový soubor uložte.

