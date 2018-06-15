---
title: Zpracování událostí životního cyklu cloudové služby | Microsoft Docs
description: Zjistěte, jak lze použít metody životního cyklu Cloudová služba role v rozhraní .NET
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: eb78c05df3b3cdf3887334c11bdabd5cebb74747
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843445"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Přizpůsobení životního cyklu webové role nebo role pracovního procesu v .NET
Když vytvoříte roli pracovního procesu, můžete rozšířit [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) třída, která poskytuje metody pro vás k přepsání, které vám umožní reagovat na události životního cyklu. Pro webové role Tato třída je volitelné, takže je nutné použít na reakce na události životního cyklu.

## <a name="extend-the-roleentrypoint-class"></a>Rozšíření třídy RoleEntryPoint
[RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) třída obsahuje metody, které se nazývají Azure při jeho **spustí**, **spouští**, nebo **zastaví** roli web nebo worker. Volitelně můžete přepsat tyto metody pro správu role inicializace, role vypnutí pořadí nebo prováděcí vlákno role. 

Při rozšiřování **RoleEntryPoint**, byste měli vědět z následujících chování metod:

* [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) a [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) metody vrátit logickou hodnotu, takže je možné vrátit **false** z těchto metod.
  
   Pokud váš kód vrátí **false**neočekávaného ukončení procesu role, bez spuštění jakékoli pořadí vypnutí můžete mít na místě. Obecně platí, neměli byste vrácení **false** z **OnStart** metoda.
* Žádné nezachycená v rámci přetížení **RoleEntryPoint** metoda je považován za k neošetřené výjimce.
  
   Pokud dojde k výjimce v rámci jedné z metod životního cyklu, bude vyvolána Azure [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) událostí a proces je ukončen. Po vaše role je v režimu offline, se restartuje v Azure. Když dojde k neošetřené výjimce [zastavení](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) událost se vyvolá a **OnStop** metoda není volána.

Pokud vaše role se nespustí, nebo je recyklace inicializace zaneprázdněný, až zastavením stavy, může váš kód vyvolání k neošetřené výjimce v rámci jedné události životního cyklu každém restartování roli. V takovém případě použijte [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) událostí a zjistěte příčinu výjimka správně zpracovat. Vaše role může být také vrácení z [spustit](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metoda, což způsobí, že role, kterou chcete restartovat. Další informace o stavy nasazení najdete v tématu [běžné problémy které příčina rolí k recyklaci](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Pokud používáte **nástroje Azure pro sadu Microsoft Visual Studio** vyvíjet aplikace, šablony projektů role automaticky rozšířit **RoleEntryPoint** třídy, v  *WebRole.cs* a *WorkerRole.cs* soubory.
> 
> 

## <a name="onstart-method"></a>OnStart – metoda
**OnStart** metoda je volána, když vaše instance role do online režimu Azure. Při spouštění kód OnStart role instance je označena jako **zaneprázdněn** a žádný externí provoz budete přesměrováni na je nástroj pro vyrovnávání zatížení. Můžete přepsat tuto metodu za účelem inicializace práci, jako je například implementace obslužné rutiny událostí a spuštění [Azure Diagnostics](cloud-services-how-to-monitor.md).

Pokud **OnStart** vrátí **true**, instance je úspěšně inicializován a Azure volá **RoleEntryPoint.Run** metoda. Pokud **OnStart** vrátí **false**, roli ukončí okamžitě, bez spuštění jakýchkoli pořadí plánované vypnutí.

Následující příklad kódu ukazuje, jak lze přepsat **OnStart** metoda. Tato metoda nakonfiguruje a spustí monitorování diagnostiky, když role instance spuštění a nastaví přenosu dat protokolování na účet úložiště:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Onstop – metoda
**OnStop** metoda je volána po instanci role je v režimu offline v Azure a před proces bude ukončen. Mohou přepsat tuto metodu volat kódu vyžadovaného pro instanci role řádně vypnout.

> [!IMPORTANT]
> Kód spuštěný ve **OnStop** metoda má po omezenou dobu ukončíte při volání z jiných důvodů než vypnutí spuštěné uživatelem. Po uplynutí této doby, ukončení procesu, ujistěte se, že kód **OnStop** metoda lze rychle spustit nebo toleruje není spuštěna na dokončení. **OnStop** metoda je volána po **zastavení** událost se vyvolá.
> 
> 

## <a name="run-method"></a>Run – metoda
Je možné přepsat **spustit** metody k implementaci dlouho spuštěných vláken pro instanci role.

Přepsání **spustit** metoda není vyžadováno; výchozí implementace spustí vlákno, které uspí navždy. Pokud přepíšete **spustit** metoda, váš kód by měl blokovat po neomezenou dobu. Pokud **spustit** metoda vrátí hodnotu, je automaticky řádně recykluje roli; jinými slovy, vyvolá Azure **zastavení** událostí a volání **OnStop** metoda tak, aby vaše vypnutí pořadí může být spuštěna před role do režimu offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementace metody životního cyklu ASP.NET pro webové role
Můžete použít metody životního cyklu ASP.NET, kromě těch, které poskytuje **RoleEntryPoint** třídy, ke správě pořadí inicializace a vypnutí pro webové role. To může být užitečná pro účely kompatibility, pokud jsou portování stávající aplikaci ASP.NET do Azure. Metody životního cyklu ASP.NET jsou volány prostřednictvím **RoleEntryPoint** metody. **Aplikace\_spustit** metoda je volána po **RoleEntryPoint.OnStart** Metoda dokončení. **Aplikace\_End** metoda je volána před provedením **RoleEntryPoint.OnStop** metoda je volána.

## <a name="next-steps"></a>Další kroky
Zjistěte, jak [vytvořit balíček služby cloud](cloud-services-model-and-package.md).

