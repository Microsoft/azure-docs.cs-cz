---
title: Zpracování událostí životního cyklu cloudové služby | Dokumenty společnosti Microsoft
description: Zjistěte, jak lze metody životního cyklu role cloudové služby použít v rozhraní .NET
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: a6030cbb756525137497834ac911835033858401
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652080"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Přizpůsobení životního cyklu role webu nebo pracovního procesu v rozhraní .NET
Při vytváření role pracovního procesu rozšíříte třídu [RoleEntryPoint,](/previous-versions/azure/reference/ee758619(v=azure.100)) která poskytuje metody k přepsání, které umožňují reagovat na události životního cyklu. Pro webové role je tato třída volitelná, takže ji musíte použít k reakci na události životního cyklu.

## <a name="extend-the-roleentrypoint-class"></a>Rozšíření třídy RoleEntryPoint
Třída [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) obsahuje metody, které jsou volány Azure při **spuštění**, **spustí**nebo **zastaví** web nebo roli pracovního procesu. Volitelně můžete přepsat tyto metody pro správu inicializace role, sekvence vypnutí role nebo spuštění podprocesu role. 

Při rozšiřování **RoleEntryPoint**, měli byste být vědomi následující chování metod:

* Metoda [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) vrátí logickou hodnotu, takže je možné vrátit **false** z této metody.
  
   Pokud váš kód vrátí **false**, proces role je náhle ukončena, bez spuštění jakékoli sekvence vypnutí, které můžete mít na místě. Obecně byste se měli vyhnout vrácení **false** z **OnStart** metody.
* Všechny nezachycené výjimky v rámci přetížení **Metodu RoleEntryPoint** je považován za neošetřené výjimky.
  
   Pokud dojde k výjimce v rámci jedné z metod životního cyklu, Azure vyvolá [unhandledException](/dotnet/api/system.appdomain.unhandledexception) událost a pak je proces ukončen. Po přepne vaše role offline, bude restartován Azure. Dojde-li k neošetřené výjimce, není vyvolána událost [Stop](/previous-versions/azure/reference/ee758136(v=azure.100)) a není volána metoda **OnStop.**

Pokud se vaše role nespustí nebo je recyklace mezi inicializace, zaneprázdněn a zastavení stavy, váš kód může být vyvolání neošetřené výjimky v rámci jedné z událostí životního cyklu při každém restartování role. V takovém případě použijte [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) událost k určení příčiny výjimky a zpracovat odpovídajícím způsobem. Vaše role může být také vrací z [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) metoda, která způsobí, že role restartovat. Další informace o stavech nasazení naleznete [v tématu Běžné problémy, které způsobují recyklaci rolí](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Pokud k vývoji aplikace používáte **Nástroje Azure pro Microsoft Visual Studio,** šablony projektu rolí automaticky rozšíří třídu **RoleEntryPoint** za vás, v *WebRole.cs* a *WorkerRole.cs* souborů.
> 
> 

## <a name="onstart-method"></a>Metoda OnStart
**OnStart** Metoda se volá, když je instance vaší role přepnuto do režimu online Azure. Při provádění kódu OnStart je instance role označena jako **Zaneprázdněn** a žádný externí provoz na ni nebude směrován vyvyčován vyvyčováváním zatížení. Tuto metodu můžete přepsat a provést inicializační práci, jako je například implementace obslužných rutin událostí a spuštění [diagnostiky Azure](cloud-services-how-to-monitor.md).

Pokud **OnStart** vrátí **true**, instance je úspěšně inicializována a Azure volá **Metodu RoleEntryPoint.Run.** Pokud **OnStart** vrátí **false**, role okamžitě ukončí, bez provedení jakékoli plánované vypnutí sekvence.

Následující příklad kódu ukazuje, jak přepsat metodu **OnStart.** Tato metoda konfiguruje a spustí diagnostický monitor při spuštění instance role a nastaví přenos dat protokolování do účtu úložiště:

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

## <a name="onstop-method"></a>Metoda OnStop
**OnStop** Metoda je volána po instanci role byla přijata do offline Azure a před ukončením procesu. Tuto metodu můžete přepsat a volat kód potřebný k čistému vypnutí instance role.

> [!IMPORTANT]
> Kód spuštěný v **metodě OnStop** má omezený čas na dokončení, pokud je volán z jiných důvodů, než je vypnutí iniciované uživatelem. Po uplynutí této doby je proces ukončen, takže je nutné se ujistit, že kód v **OnStop** metoda může běžet rychle nebo toleruje není spuštěn a dokončení. **OnStop** Metoda je volána po **Stop** je aktivována.
> 
> 

## <a name="run-method"></a>Spustit metodu
Můžete přepsat **Run** metoda implementovat dlouhotrvající vlákno pro instanci role.

Přepsání **Run** metoda není vyžadována; výchozí implementace spustí vlákno, které je v režimu spánku navždy. Pokud přepíšete **Run** metoda, váš kód by měl blokovat neomezeně dlouho. Pokud **Run** metoda vrátí, role je automaticky řádně recyklován; jinými slovy Azure vyvolá **Stop** události a volá **OnStop** metoda tak, aby vaše sekvence vypnutí může být spuštěna před přepne role offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementace ASP.NET metody životního cyklu pro webovou roli
Můžete použít ASP.NET metody životního cyklu, kromě těch, které poskytuje **Třída RoleEntryPoint,** ke správě inicializace a vypnutí sekvence pro webovou roli. To může být užitečné pro účely kompatibility, pokud jsou portování existující ASP.NET aplikace do Azure. Metody životního cyklu ASP.NET jsou volány z metod **RoleEntryPoint.** Metoda **\_Start aplikace** je volána po dokončení metody **RoleEntryPoint.OnStart.** Metoda **\_Application End** je volána před voláním metody **RoleEntryPoint.OnStop.**

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [vytvořit balíček cloudových služeb](cloud-services-model-and-package.md).




