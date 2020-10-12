---
title: Pracovní postup architektury Windows Azure VM | Microsoft Docs
description: Tento článek poskytuje přehled procesů pracovního postupu při nasazení služby.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 9c427982854e1d328b5d1553aa86866ad298eea1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461307"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Pracovní postup architektury klasického virtuálního počítače Windows Azure 
Tento článek poskytuje přehled procesů pracovních postupů, ke kterým dochází při nasazování nebo aktualizaci prostředku Azure, jako je třeba virtuální počítač. 

> [!NOTE]
>Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: Správce prostředků a Classic. Tento článek se věnuje použití klasického modelu nasazení.

Následující diagram znázorňuje architekturu prostředků Azure.

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<ALT – obrázek o pracovním postupu Azure>":::

## <a name="workflow-basics"></a>Základy pracovního postupu
   
**A**. RDFE/FFE je komunikační cesta od uživatele k prostředkům infrastruktury. RDFE (RedDog Front End) je veřejně vystavené rozhraní API, které je front-end pro Portál pro správu a rozhraní API pro správu služeb, jako je například Visual Studio, Azure MMC a tak dále.  Všechny žádosti od uživatele procházejí přes RDFE. FFE (Fabric front end) je vrstva, která překládá požadavky z RDFE do příkazů prostředků infrastruktury. Všechny požadavky z RDFE procházejí přes FFE a dosáhnou řadičů infrastruktury.

**B**. Kontroler prostředků infrastruktury zodpovídá za údržbu a monitorování všech prostředků v datovém centru. Komunikuje s agenty hostitele prostředků infrastruktury v operačním systému prostředků infrastruktury odesílající informace, jako je verze operačního systému hosta, balíček služby, konfigurace služby a stav služby.

**C**. Hostitelský agent bydlí v hostitelském operačním systému a zodpovídá za nastavení hostovaného operačního systému a komunikaci s agentem hosta (WindowsAzureGuestAgent), aby se role aktualizovala směrem k zamýšlenému stavu cíle a aby se pomocí agenta hosta kontroly prezenčního signálu. Pokud agent hostitele neobdrží odpověď prezenčního signálu po dobu 10 minut, Agent hosta restartuje hostovaný operační systém.

**C2**. WaAppAgent zodpovídá za instalaci, konfiguraci a aktualizaci WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent zodpovídá za následující:

1. Konfigurace hostovaného operačního systému včetně brány firewall, seznamů ACL, prostředků LocalStorage, balíčku služby a konfigurace a certifikátů.
2. Nastavení identifikátoru SID pro uživatelský účet, pod kterým bude role běžet.
3. Komunikace se stavem role do prostředků infrastruktury.
4. Spouští se WaHostBootstrapper a monitoruje se, aby se zajistilo, že role je ve stavu cíle.

**E**. WaHostBootstrapper zodpovídá za:

1. Načetli jste konfiguraci rolí a spustíte všechny příslušné úlohy a procesy pro konfiguraci a spuštění role.
2. Monitorování všech podřízených procesů.
3. Vyvolává se událost StatusCheck v procesu hostitele role.

**F**. IISConfigurator se spustí, pokud je role nakonfigurovaná jako plná webová role služby IIS (nespustí se pro role SDK 1,2 umožní). Zodpovídá za:

1. Spuštění standardních služeb IIS
2. Konfigurace modulu pro přepis ve webové konfiguraci
3. Nastavení fondu aplikací pro konfigurovanou roli v modelu služby
4. Nastavení protokolování služby IIS tak, aby odkazovalo na složku DiagnosticStore LocalStorage
5. Konfigurace oprávnění a seznamů ACL
6. Web se nachází ve službě% roleroot%: \sitesroot\0 a fond aplikací odkazuje na toto umístění, aby spouštěl službu IIS. 

**G**. Úlohy po spuštění jsou definovány modelem role a spouštěny pomocí WaHostBootstrapper. Úlohy po spuštění je možné nakonfigurovat tak, aby se spouštěly na pozadí asynchronně, a zaváděcí nástroj hostitele spustí úlohu po spuštění a pak bude pokračovat na další úlohy při spuštění. Úlohy po spuštění je také možné nakonfigurovat tak, aby běžely v jednoduchém (výchozím) režimu, ve kterém bude zaváděcí nástroj hostitele čekat na dokončení spouštěcí úlohy, a před pokračováním na další úlohu po spuštění vrátit ukončovací kód o úspěšnosti (0).

**H**. Tyto úlohy jsou součástí sady SDK a jsou definovány jako moduly plug-in v definici služby role (. csdef). Při rozšíření na úlohy po spuštění jsou **DiagnosticsAgent** a **RemoteAccessAgent** jedinečné v tom, že každý definuje dvě úlohy po spuštění, jednu regulární a jednu, která má parametr **/blockStartup** . Běžný spouštěcí úkol je definován jako spouštěcí úkol na pozadí, aby mohl běžet na pozadí, zatímco je spuštěná role sama. Úloha po spuštění **/blockStartup** je definována jako jednoduchý spouštěcí úkol, aby WaHostBootstrapper čekal na ukončení před pokračováním. Úloha **/blockStartup** čeká na dokončení inicializace pravidelného úkolu a potom ukončí a povolí zaváděcímu nástroji hostitele pokračovat. To se provádí tak, aby bylo možné nakonfigurovat přístup k diagnostice a protokolu RDP předtím, než se spustí procesy role (provádí se pomocí úlohy/blockStartup). To také umožňuje, aby Diagnostika a přístup protokolu RDP běžely i poté, co nástroj Host dokončil úlohy po spuštění (provádí se pomocí normální úlohy).

**I**. WaWorkerHost je standardní hostitelský proces pro normální role pracovního procesu. Tento hostitelský proces je hostitelem všech knihoven DLL role a kódu vstupního bodu, jako je například OnStart a Run.

**J**. WaIISHost je hostitelský proces pro kód vstupního bodu role pro webové role, které používají plnou službu IIS. Tento proces načte první nalezenou knihovnu DLL, která používá třídu **RoleEntryPoint** , a spustí kód z této třídy (OnStart, Run, OnStart). V tomto procesu jsou vyvolány jakékoli události **RoleEnvironment** (například StatusCheck a změněné), které jsou vytvořeny ve třídě RoleEntryPoint.

**K**. W3WP je standardní pracovní proces služby IIS, který se používá, pokud je role nakonfigurovaná tak, aby používala plnou službu IIS. Tím se spustí fond, který je nakonfigurovaný z IISConfigurator. V tomto procesu jsou vyvolány jakékoli události RoleEnvironment (například StatusCheck a změněné), které jsou vytvořeny zde. Všimněte si, že RoleEnvironment události se aktivují v obou umístěních (WaIISHost a w3wp.exe), pokud se přihlásíte k odběru událostí v obou procesech.

## <a name="workflow-processes"></a>Pracovní postupy

1. Uživatel vytvoří požadavek, jako je nahrání souborů ". cspkg" a ". cscfg", oznamuje zdroji, aby zastavil nebo prováděl změnu konfigurace atd. To lze provést prostřednictvím Azure Portal nebo pomocí nástroje, který používá rozhraní API pro správu služeb, jako je například funkce publikování aplikace Visual Studio. Tento požadavek směřuje na RDFE, aby provede celou práci související s předplatným, a pak sdělí požadavek FFE. Zbývajících z těchto kroků pracovního postupu je nasazení nového balíčku a jeho spuštění.
2. FFE vyhledá správný fond počítačů (na základě zákaznického vstupu, jako je skupina vztahů nebo geografické umístění a vstup z prostředků infrastruktury, jako je třeba dostupnost počítače) a komunikuje s hlavním řadičem prostředků infrastruktury v daném fondu počítačů.
3. Kontroler prostředků infrastruktury vyhledá hostitele, který má dostupné jádra procesoru (nebo roztočí nového hostitele). Balíček služby a konfigurace se zkopírují na hostitele a kontrolér Fabric komunikuje s agentem hostitele na hostitelském operačním systému, aby nasadil balíček (konfigurace DIP, portů, hostovaného operačního systému atd.).
4. Agent hostitele spustí hostovaný operační systém a komunikuje s agentem hosta (WindowsAzureGuestAgent). Hostitel pošle prezenční signály hostovi, aby se ujistil, že role pracuje směrem k jejímu cílovému stavu.
5. WindowsAzureGuestAgent nastaví hostovaný operační systém (bránu firewall, seznamy ACL, LocalStorage atd.), zkopíruje nový konfigurační soubor XML do c:\Config a potom spustí proces WaHostBootstrapper.
6. U úplných webových rolí služby IIS WaHostBootstrapper spustí IISConfigurator a oznámí IT, aby odstranil všechny existující služby fondů pro webovou roli z IIS.
7. WaHostBootstrapper přečte úlohy **po spuštění** z E:\RoleModel.xml a začne spouštět úlohy po spuštění. WaHostBootstrapper počká, dokud nebudou dokončeny všechny jednoduché úvodní úlohy a vrátila zprávu "úspěch".
8. U úplných webových rolí služby IIS WaHostBootstrapper oznamuje IISConfigurator, že má nakonfigurovat službu IIS AppPool a odkazuje na lokalitu `E:\Sitesroot\<index>` , kde `<index>` je index založený na nule na počet `<Sites>` prvků definovaných pro danou službu.
9. WaHostBootstrapper spustí proces hostitele v závislosti na typu role:
    1. **Role pracovního procesu**: WaWorkerHost.exe je spuštěná. WaHostBootstrapper spustí metodu OnStart (). Jakmile se vrátí, WaHostBootstrapper spustí metodu Run () a pak ji současně označí jako připravenou a umístí ji do rotace nástroje pro vyrovnávání zatížení (pokud jsou definovány InputEndpoints). WaHostBootsrapper pak přejde do smyčky kontroly stavu role.
    2. **Plná webová role služby IIS**: aIISHost je spuštěná. WaHostBootstrapper spustí metodu OnStart (). Po návratu začne spustit metodu Run () a pak zároveň označí roli jako připravenou a umístí ji do rotace nástroje pro vyrovnávání zatížení. WaHostBootsrapper pak přejde do smyčky kontroly stavu role.
10. Příchozí webové požadavky na úplnou webovou roli služby IIS spustí službu IIS, aby spouštěla proces W3WP a obsluhují požadavek stejným způsobem jako v místním prostředí služby IIS.

## <a name="log-file-locations"></a>Umístění souborů protokolu

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Tento protokol obsahuje změny služby, včetně spuštění, zastavení a nových konfigurací. Pokud se služba nezměnila, můžete očekávat, že se v tomto souboru protokolu zobrazí velké časové prodlevy.
- C:\Logs\WaAppAgent.Log.  
Tento protokol obsahuje aktualizace stavu a oznámení prezenčního signálu a aktualizuje se každých 2-3 sekund.  Tento protokol obsahuje historický pohled na stav instance a upozorní vás, když instance nebyla připravena.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Protokoly IIS**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Protokoly událostí systému Windows**

`D:\Windows\System32\Winevt\Logs`