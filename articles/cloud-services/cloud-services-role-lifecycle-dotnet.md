---
title: Zpracování událostí životního cyklu cloudových služeb | Dokumentace Microsoftu
description: Přečtěte si použití metody životního cyklu role cloudové služby v .NET
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 56f7b5e3b303ce68868f15528d1ec200919b52aa
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001554"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Přizpůsobení životního cyklu webové role nebo role pracovního procesu v .NET
Když vytvoříte roli pracovního procesu, můžete rozšířit [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) třídy, které poskytuje metody pro vás k přepsání, které vám umožní reagovat na události životního cyklu. V případě webových rolí Tato třída je volitelné, takže je nutné použít pro reakci na události životního cyklu.

## <a name="extend-the-roleentrypoint-class"></a>Rozšíření třídy RoleEntryPoint
[RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) třída obsahuje metody, které jsou volány aplikací Azure při jeho **spustí**, **spustí**, nebo **zastaví** webové nebo pracovní role. Volitelně můžete přepsat tyto metody pro správu role inicializace, sekvence vypnutí rolí nebo vlákno provádění role. 

Při rozšiřování **RoleEntryPoint**, je třeba si uvědomit následující chování metody:

* [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) a [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) metody vrací logickou hodnotu, takže je možné vrátit **false** z těchto metod.
  
   Pokud váš kód vrátí **false**, ukončí proces role, bez nutnosti spuštění jakékoli sekvence vypnutí můžete mít na místě. Obecně byste se měli vyhnout vrácení **false** z **OnStart** metody.
* Některé nezachycená výjimka v rámci přetížení **RoleEntryPoint** metoda považuje za neošetřenou výjimku.
  
   Pokud dojde k výjimce v rámci jedné z metod životního cyklu, bude vyvolána Azure [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) událostí a proces je ukončen. Poté, co vaše role bylo převedeno do režimu offline, se restartuje v Azure. Když dojde k neošetřené výjimce [zastavení](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) není vyvolána událost a **OnStop** metoda není volána.

Pokud vaše role se nespustí nebo se recykluje mezi inicializace, zaneprázdněný a zastavení stavy, může váš kód došlo k neošetřené výjimce v jednom z události životního cyklu pokaždé, když role restartuje. V takovém případě použijte [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) událost, abyste zjistili příčinu výjimkou a odpovídajícím způsobem zpracovat. Vaše role může být také vrácení z [spustit](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metodu, která způsobí restartování role. Další informace o stavy nasazení najdete v tématu [běžné problémy který příčina role recyklovat](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Pokud používáte **nástroje Azure pro Microsoft Visual Studio** vyvíjet aplikace, šablony projektů role automaticky rozšíří **RoleEntryPoint** třídy, v  *WebRole.cs* a *WorkerRole.cs* soubory.
> 
> 

## <a name="onstart-method"></a>OnStart – metoda
**OnStart** metoda se volá, když vaše instance role do online režimu Azure. Při provádění operace OnStart kód role instance je označen jako **zaneprázdněn** a žádné externí přenosy budou přesměrováni na ho pomocí služby Vyrovnávání zatížení. Můžete přepsat tuto metodu za účelem inicializace činnosti, jako jsou implementace obslužné rutiny událostí a spuštění [Azure Diagnostics](cloud-services-how-to-monitor.md).

Pokud **OnStart** vrátí **true**instance je úspěšně inicializován a Azure zavolá **RoleEntryPoint.Run** metody. Pokud **OnStart** vrátí **false**, roli skončí okamžitě, bez provádění jakékoli plánované vypnutí pořadí.

Následující příklad kódu ukazuje, jak přepsat **OnStart** metody. Tato metoda konfiguruje a monitorování diagnostiky se spustí při spuštění role instance a nastaví přenos protokolování dat do účtu úložiště:

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

## <a name="onstop-method"></a>OnStop – metoda
**OnStop** metoda se volá, když role instance je v režimu offline v Azure a předtím, než se proces ukončí. Mohou přepsat tuto metodu volat kód potřebný pro vaši instanci role pro vypnutí čistě.

> [!IMPORTANT]
> Kód spuštěný v **OnStop** metoda má po omezenou dobu pro dokončení, když je volána z důvodů, než je vypnutí iniciované uživatelem. Po uplynutí této doby je proces ukončen, takže musíte přesvědčit, že kód v **OnStop** metodu můžete rychle spustit nebo toleruje neběží na dokončení. **OnStop** metoda je volána po **zastavení** událost se vyvolá.
> 
> 

## <a name="run-method"></a>Run – metoda
Je možné přepsat **spustit** metody k implementaci dlouho běžící vlákna pro vaši instanci role.

Přepsání **spustit** metoda se nevyžaduje; výchozí implementace spuštění vlákna, které stále v režimu spánku. Pokud přepíšete **spustit** metody kódu měla být trvale blokovaná. Pokud **spustit** metoda vrací výsledek, je automaticky řádně recyklaci role; jinými slovy, vyvolá Azure **zastavení** událostí a volání **OnStop** metoda tak, aby vaše vypnutí pořadí může být spuštěna před roli je převedeno do režimu offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementace metody životního cyklu technologie ASP.NET pro webové role
Můžete použít metody životního cyklu ASP.NET poskytovaným **RoleEntryPoint** třídy související se správou pořadí inicializace a ukončení pro webovou roli. To může být užitečné pro účely kompatibility, pokud se přenos stávající aplikaci ASP.NET do Azure. Volání těchto metod životního cyklu technologie ASP.NET v rámci **RoleEntryPoint** metody. **Aplikace\_Start** metoda je volána po **RoleEntryPoint.OnStart** dokončení metody. **Aplikace\_End** metoda je volána před provedením **RoleEntryPoint.OnStop** metoda je volána.

## <a name="next-steps"></a>Další postup
Zjistěte, jak [vytvořit balíček cloudové služby](cloud-services-model-and-package.md).

