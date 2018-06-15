---
title: Sledování toku v aplikaci služby cloudu s Azure Diagnostics | Microsoft Docs
description: Přidání trasování zprávy do Azure aplikace usnadňuje ladění, měření výkonu, monitorování, analýza provozu a další.
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: adegeo
ms.openlocfilehash: 7551ae2e3a7c5f3ff6010bf34e7600da1028d67f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
ms.locfileid: "29461232"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Trasování toku aplikace cloudových služeb s Azure Diagnostics
Trasování je způsob, jak můžete monitorovat aplikace, když je spuštěná. Můžete použít [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx), a [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) třídy k zaznamenání informací o chybách a spuštění aplikace v protokolech, textové soubory nebo jiné zařízení pro pozdější analýzu. Další informace o trasování najdete v tématu [trasování a instrumentace aplikací](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Pomocí příkazů trasování a trasování – přepínače
Implementace trasování v aplikaci cloudové služby tak, že přidáte [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) konfiguraci aplikace a volání System.Diagnostics.Trace nebo System.Diagnostics.Debug v kódu aplikace. Použití konfiguračního souboru *app.config* pro role pracovního procesu a *web.config* pro webové role. Když vytvoříte novou hostovanou službu pomocí šablony sady Visual Studio, Azure Diagnostics se automaticky přidá do projektu a DiagnosticMonitorTraceListener je přidán do příslušné konfiguračního souboru pro role, které přidáte.

Informace o umístění trasovacích příkazů najdete v tématu [postupy: Přidání příkazů trasování do kódu aplikace](https://msdn.microsoft.com/library/zd83saa2.aspx).

Tím, že umístíte [trasování – přepínače](https://msdn.microsoft.com/library/3at424ac.aspx) ve vašem kódu můžete ovládat, zda dojde k trasování a jak rozsáhlé je. To vám umožňuje monitorovat stav aplikace v provozním prostředí. To je obzvláště důležité v obchodní aplikace, která používá několik součástí běžící na více počítačích. Další informace najdete v tématu [postupy: Konfigurace trasování – přepínače](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurace trasování modulu naslouchání v aplikaci Azure
Trasování ladění a TraceSource, musíte nastavit "naslouchací procesy" ke sběru a zaznamenejte zprávy, které se odesílají. Naslouchací procesy shromažďování, ukládání a směrovat trasovací zprávy. Jejich přesměrujte výstup trasování do příslušné cílové, jako je například protokol, okno nebo textového souboru. Používá Azure Diagnostics [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) třídy.

Před provedením následujícího postupu, je nutné inicializovat Azure monitorování diagnostiky. Chcete-li to provést, přečtěte si téma [povolení diagnostiky v Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Všimněte si, že pokud používáte šablony, které jsou k dispozici Visual Studio, konfiguraci naslouchacího procesu se automaticky přidá za vás.

### <a name="add-a-trace-listener"></a>Přidejte naslouchací proces trasování
1. Otevřete soubor web.config nebo app.config pro vaši roli.
2. Přidejte následující kód do souboru. Změňte atribut verze používat číslo verze sestavení, ve kterém je odkazováno na. Verze sestavení nezmění nutně při každém vydání sady Azure SDK Pokud existují aktualizace.
   
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
   > Ujistěte se, že máte projektu odkaz na sestavení Microsoft.WindowsAzure.Diagnostics. Aktualizujte číslo verze ve výše uvedené xml tak, aby odpovídala verzi odkazované sestavení Microsoft.WindowsAzure.Diagnostics.
   > 
   > 
3. Uložte do konfiguračního souboru.

Další informace o naslouchací procesy najdete v tématu [trasování – moduly naslouchání](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Po dokončení kroků pro přidání naslouchací proces, je přidání příkazů trasování do kódu.

### <a name="to-add-trace-statement-to-your-code"></a>Chcete-li přidat příkaz trasování do kódu
1. Otevřete zdrojový soubor pro vaši aplikaci. Například <RoleName>.cs soubor pro roli pracovního procesu, nebo webové role.
2. Přidejte následující pomocí příkazu, pokud již nebyly přidané:
    ```
        using System.Diagnostics;
    ```
3. Přidání příkazů trasování, kde chcete zaznamenat informace o stavu vaší aplikace. K formátování výstupu příkazu trasování můžete použít různé metody. Další informace najdete v tématu [postupy: Přidání příkazů trasování do kódu aplikace](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Zdrojový soubor uložte.

