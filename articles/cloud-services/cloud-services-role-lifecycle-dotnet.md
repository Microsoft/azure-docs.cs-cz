---
title: Zpracování událostí životního cyklu cloudové služby (Classic) | Microsoft Docs
description: Naučte se používat metody životního cyklu role cloudové služby v .NET, včetně RoleEntryPoint, která poskytuje metody pro reakci na události životního cyklu.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b5aa4bd061647f63ebcc70109f0ba21b39e814cc
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741328"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Přizpůsobení životního cyklu webové role nebo role pracovního procesu v .NET

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Když vytváříte roli pracovního procesu, rozšíříte třídu [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) , která poskytuje metody pro přepsání, které umožňují reagovat na události životního cyklu. Pro webové role je tato třída volitelná, takže ji musíte použít k reakci na události životního cyklu.

## <a name="extend-the-roleentrypoint-class"></a>Rozšiřování třídy RoleEntryPoint
Třída [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) zahrnuje metody, které Azure volá při **spuštění**, **spuštění** nebo **zastavení** webové role nebo role pracovního procesu. Volitelně můžete tyto metody přepsat pro správu inicializace rolí, sekvencí vypnutí rolí nebo vlákna spuštění role. 

Při rozšiřování **RoleEntryPoint** byste měli mít na paměti následující chování metod:

* Metoda [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) vrátí logickou hodnotu, takže je možné vrátit **hodnotu false** z této metody.
  
   Pokud váš kód vrátí **hodnotu false**, proces role se náhle ukončí, aniž by se musela spustit žádná sekvence vypnutí. Obecně byste se měli vyhnout vrácení **hodnoty false** z metody **OnStart** .
* Jakékoli nezachycené výjimky v rámci přetížení metody **RoleEntryPoint** se považují za neošetřenou výjimku.
  
   Pokud dojde k výjimce v rámci jedné z metod životního cyklu, Azure vyvolá událost [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) a proces se ukončí. Po převedení role do režimu offline bude Azure restartován. Pokud dojde k neošetřené výjimce, událost [zastavení](/previous-versions/azure/reference/ee758136(v=azure.100)) není vyvolána a metoda při **chybě** není volána.

Pokud se vaše role nespustí nebo je recyklace mezi inicializací, zaneprázdněnou a stavem zastavení, váš kód může při každém restartování role vystavit neošetřenou výjimku v rámci jedné z událostí životního cyklu. V takovém případě použijte událost [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) k určení příčiny výjimky a patřičně ji zpracujte. Vaše role může být vrácena také z metody [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) , což způsobí, že se role restartuje. Další informace o stavech nasazení najdete v tématu [běžné problémy, které způsobují recyklaci rolí](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Pokud používáte **nástroje Azure pro Microsoft Visual Studio** k vývoji vaší aplikace, šablony projektu role automaticky rozšířily třídu **RoleEntryPoint** pro vás, v souborech *WebRole.cs* a *WorkerRole.cs* .
> 
> 

## <a name="onstart-method"></a>OnStart – metoda
Metoda **OnStart** se volá, když Azure vaši instanci role dovede do online režimu. I když je spuštěn kód OnStart, je instance role označena jako **zaneprázdněná** a nástroj pro vyrovnávání zatížení do něj nesměruje žádný externí provoz. Tuto metodu můžete přepsat pro provedení inicializační práce, například implementace obslužných rutin událostí a spouštění [Azure Diagnostics](cloud-services-how-to-monitor.md).

Pokud **OnStart** vrátí **hodnotu true**, instance se úspěšně Inicializuje a Azure zavolá metodu **RoleEntryPoint. Run** . Pokud **OnStart** vrátí **hodnotu false**, role se okamžitě ukončí, aniž by bylo nutné spouštět žádné plánované sekvence vypnutí.

Následující příklad kódu ukazuje, jak přepsat metodu **OnStart** . Tato metoda konfiguruje a spustí diagnostické monitorování při spuštění instance role a nastaví přenos protokolovaných dat do účtu úložiště:

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

## <a name="onstop-method"></a>Metoda zastavení
Metoda **instop** se volá po převedení instance role do režimu offline v Azure a před ukončením procesu. Tuto metodu můžete přepsat pro volání kódu, který je požadován pro vaši instanci role k čistěmu vypnutí.

> [!IMPORTANT]
> Kód spuštěný v metodě- **stop** má časově omezený čas na jeho dokončení, pokud je volán z jiných důvodů než vypnutí iniciované uživatelem. Po uplynutí této doby se proces ukončí, takže je nutné zajistit, aby kód v metodě k **zastavení** mohl rychle běžet nebo tolerovat nechody do dokončení. Metoda **instop** je volána po vyvolání události **zastavení** .
> 
> 

## <a name="run-method"></a>Run – metoda
Metodu **Run** můžete přepsat tak, aby pro vaši instanci role implementovala dlouhodobě běžící vlákno.

Přepsání metody **Run** není vyžadováno; Výchozí implementace spustí vlákno, které je trvale úsporné. Pokud přepíšete metodu **Run** , váš kód by měl blokovat neomezenou dobu. Pokud se metoda **Run** vrátí, role se automaticky recykluje. Jinými slovy, Azure vyvolá událost **zastavení** a zavolá metodu **restop** , aby bylo možné spustit sekvence vypnutí, než bude role přepnuta do režimu offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementace metod životního cyklu ASP.NET pro webovou roli
Pomocí metod životního cyklu ASP.NET můžete kromě těch, které poskytuje třída **RoleEntryPoint** , spravovat inicializační a ukončovací sekvenci webové role. To může být užitečné pro účely kompatibility, Pokud předáváte existující aplikaci ASP.NET do Azure. Metody životního cyklu ASP.NET se volají v rámci metod **RoleEntryPoint** . Metoda **\_ spuštění aplikace** je volána po dokončení metody **RoleEntryPoint. OnStart** . Metoda **\_ end aplikace** je volána před voláním metody **RoleEntryPoint... stop** .

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [vytvořit balíček cloudové služby](cloud-services-model-and-package.md).




