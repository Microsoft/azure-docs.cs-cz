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
ms.openlocfilehash: 2ba97e43616386a0ff8459316bfc4d3ddfe241a0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39000891"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Trasování toku aplikace Cloud Services s využitím Azure Diagnostics
Trasování je způsob, jak můžete monitorovat provádění aplikace během jejího běhu. Můžete použít [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx), a [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) třídy k zaznamenání informací o chybách a spuštění aplikace v protokolech, textové soubory nebo jiná pro pozdější analýzu. Další informace o trasování najdete v tématu [trasování a instrumentace aplikací](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Pomocí příkazů trasování a přepínačů trasování
Implementace trasování aplikace Cloud Services tak, že přidáte [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) ke konfiguraci aplikace a volání System.Diagnostics.Trace nebo System.Diagnostics.Debug ve vaší kód aplikace. Použijte konfigurační soubor *app.config* rolí pracovních procesů a *web.config* pro webové role. Když vytvoříte novou hostovanou službu pomocí šablony sady Visual Studio, Azure Diagnostics se automaticky přidá do projektu a DiagnosticMonitorTraceListener se přidá do příslušného konfiguračního souboru pro role, které přidáte.

Informace o umístění příkazů trasování najdete v tématu [postupy: Přidání příkazů trasování do kódu aplikace](https://msdn.microsoft.com/library/zd83saa2.aspx).

Tím, že umístíte [přepínačů trasování](https://msdn.microsoft.com/library/3at424ac.aspx) ve vašem kódu, můžete řídit, jestli dojde k trasování a jak rozsáhlé je. To vám umožňuje monitorovat stav vaší aplikace v produkčním prostředí. To je obzvláště důležité v obchodních aplikací, které používá více součástí, které běží na několika počítačích. Další informace najdete v tématu [postupy: konfigurace přepínačů trasování](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurace naslouchacího procesu trasování v aplikaci Azure
Trasování, ladění a TraceSource, vyžadují nastavení "naslouchacích procesů" ke shromáždění a zaznamenání zprávy, které se odesílají. Naslouchací procesy shromažďování, ukládání a směrovat trasovací zprávy. Jejich přímý výstup trasování příslušný cíli, jako je protokol, okno nebo textový soubor. Pomocí diagnostiky Azure [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) třídy.

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

Další informace o naslouchacích procesů, naleznete v tématu [naslouchacích procesů trasování](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Po dokončení kroků pro přidání naslouchacího procesu, je přidání příkazů trasování do kódu.

### <a name="to-add-trace-statement-to-your-code"></a>Chcete-li přidat příkaz trasování kódu
1. Otevřete zdrojový soubor pro vaši aplikaci. Například <RoleName>.cs soubor pro webové role nebo role pracovního procesu.
2. Přidejte následující příkaz using, pokud nebyl již přidán:
    ```
        using System.Diagnostics;
    ```
3. Přidání příkazů trasování, ve kterém chcete zaznamenat informace o stavu vaší aplikace. Můžete formátovat výstup příkazu trasování můžete použít různé metody. Další informace najdete v tématu [postupy: Přidání příkazů trasování do kódu aplikace](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Zdrojový soubor uložte.

