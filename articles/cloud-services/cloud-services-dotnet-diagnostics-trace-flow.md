---
title: Trasování toku v aplikaci Cloud Services s Azure Diagnostics
titleSuffix: Azure Cloud Services
description: Přidejte trasovací zprávy do aplikace Azure, které vám pomůžou při ladění, měření výkonu, monitorování, analýze provozu a dalších.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.custom: devx-track-dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: tagore
ms.openlocfilehash: 8270766413729454181c461d469d49e418a1aa67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932300"
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Trasování toku Cloud Services aplikace pomocí Azure Diagnostics
Trasování je způsob, jak můžete monitorovat provádění aplikace, když je spuštěná. Třídy [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace), [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug)a [System. Diagnostics. TraceSource](/dotnet/api/system.diagnostics.tracesource) můžete použít k zaznamenání informací o chybách a spouštění aplikací v protokolech, textových souborech nebo jiných zařízeních pro pozdější analýzu. Další informace o trasování najdete v tématu [trasování a instrumentace aplikací](/dotnet/framework/debug-trace-profile/tracing-and-instrumenting-applications).

## <a name="use-trace-statements-and-trace-switches"></a>Použití příkazů trasování a přepínačů trasování
Implementujte trasování do aplikace Cloud Services přidáním [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) do konfigurace aplikace a provedením volání System. Diagnostics. Trace nebo System. Diagnostics. Debug v kódu aplikace. Použijte konfigurační soubor *app.config* pro role pracovního procesu a *web.config* pro webové role. Když vytváříte novou hostovanou službu pomocí šablony sady Visual Studio, Azure Diagnostics je automaticky přidáno do projektu a DiagnosticMonitorTraceListener se přidá do příslušného konfiguračního souboru pro role, které přidáte.

Informace o umístění příkazů trasování naleznete v tématu [How to: Add Trace Statements to Application Code](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).

Vložením [přepínačů trasování](/dotnet/framework/debug-trace-profile/trace-switches) do kódu můžete určit, zda trasování probíhá a jak rozsáhlý je. To vám umožní monitorovat stav aplikace v produkčním prostředí. To je obzvláště důležité v obchodní aplikaci, která používá více komponent spuštěných na více počítačích. Další informace najdete v tématu [Postup: konfigurace přepínačů trasování](/dotnet/framework/debug-trace-profile/how-to-create-initialize-and-configure-trace-switches).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Konfigurace naslouchacího procesu trasování v aplikaci Azure
Trasování, ladění a TraceSource, vyžadují, abyste nastavili naslouchací procesy pro shromažďování a zaznamenávání odesílaných zpráv. Naslouchací procesy shromažďují, ukládají a směrují trasovací zprávy. Směrují výstup trasování do příslušného cíle, jako je protokol, okno nebo textový soubor. Azure Diagnostics používá třídu [DiagnosticMonitorTraceListener](/previous-versions/azure/reference/ee758610(v=azure.100)) .

Než dokončíte následující postup, musíte inicializovat diagnostické monitorování Azure. Postup najdete v tématu [Povolení diagnostiky v Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Všimněte si, že pokud použijete šablony, které jsou k dispozici v aplikaci Visual Studio, konfigurace naslouchacího procesu se přidá automaticky.

### <a name="add-a-trace-listener"></a>Přidání naslouchacího procesu trasování
1. Otevřete web.config nebo app.config soubor pro vaši roli.
2. Do souboru přidejte následující kód. Změňte atribut verze tak, aby používal číslo verze sestavení, na které odkazujete. Verze sestavení se nemusí nutně měnit u každé verze sady Azure SDK, pokud k ní neexistují žádné aktualizace.
   
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
   > Ujistěte se, že máte odkaz na projekt v sestavení Microsoft. WindowsAzure. Diagnostics. Aktualizujte číslo verze v XML výše tak, aby odpovídalo verzi odkazovaného sestavení Microsoft. WindowsAzure. Diagnostics.
   > 
   > 
3. Uložte konfigurační soubor.

Další informace o posluchačích naleznete v tématu [Trace Listeners](/dotnet/framework/debug-trace-profile/trace-listeners).

Po dokončení kroků pro přidání naslouchacího procesu můžete do kódu přidat příkazy TRACE.

### <a name="to-add-trace-statement-to-your-code"></a>Přidání příkazu trace do kódu
1. Otevřete zdrojový soubor pro aplikaci. Například \<RoleName> soubor. cs pro roli pracovního procesu nebo webovou roli.
2. Přidejte následující direktivu using, pokud ještě nebyla přidána:
    ```
        using System.Diagnostics;
    ```
3. Přidejte příkazy trasování, kde chcete zachytit informace o stavu aplikace. K formátování výstupu příkazu trace lze použít různé metody. Další informace naleznete v tématu [Postupy: Přidání příkazů trasování do kódu aplikace](/dotnet/framework/debug-trace-profile/how-to-add-trace-statements-to-application-code).
4. Uložte zdrojový soubor.




