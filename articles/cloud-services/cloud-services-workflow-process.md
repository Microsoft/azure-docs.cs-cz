---
title: Pracovní postup architektury virtuálních počítačů Windows Azure | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162145"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Pracovní postup klasické architektury virtuálních počítačích Windows Azure 
Tento článek obsahuje přehled procesů pracovního postupu, ke kterým dochází při nasazení nebo aktualizaci prostředku Azure, jako je například virtuální počítač. 

> [!NOTE]
>Azure má dva různé modely nasazení pro vytváření a práci s prostředky: Resource Manager a klasické. Tento článek se věnuje použití klasického modelu nasazení.

Následující diagram představuje architekturu prostředků Azure.

![Pracovní postup Azure](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Základy pracovního postupu
   
**A**. RDFE / FFE je komunikační cesta od uživatele k tkanině. RDFE (RedDog Front End) je veřejně vystavené rozhraní API, které je front-end pro portál pro správu a rozhraní API pro správu služeb, jako je Visual Studio, Azure MMC a tak dále.  Všechny požadavky od uživatele procházejí RDFE. FFE (Fabric Front End) je vrstva, která převádí požadavky z RDFE na příkazy infrastruktury. Všechny požadavky RDFE procházejí prostřednictvím FFE, aby se dostaly k řadičům textilií.

**B**. Řadič prostředků infrastruktury je zodpovědný za údržbu a monitorování všech prostředků v datovém centru. Komunikuje s agenty hostitele prostředků infrastruktury v operačním systému fabric odesílání informací, jako je například verze hostovaného operačního systému, balíček služby, konfigurace služby a stav služby.

**C**. Hostitelský agent žije v hostitelském os a je zodpovědný za nastavení hostovaného operačního systému a komunikaci s agentem hosta (WindowsAzureGuestAgent) za účelem aktualizace role směrem k zamýšlenému stavu cíle a provádění kontrol prezenčního signálu s agentem Guest. Pokud hostitelský agent neobdrží odpověď prezenčního signálu po dobu 10 minut, hostitelský agent restartuje hostovaného operačního serveru.

**C2**. WaAppAgent je zodpovědný za instalaci, konfiguraci a aktualizaci programu WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent je zodpovědný za následující:

1. Konfigurace hostovaného operačního systému včetně brány firewall, seznamů AC, prostředků LocalStorage, balíčku služeb a konfigurace a certifikátů.
2. Nastavení SID pro uživatelský účet, pod kterým bude role spuštěna.
3. Komunikace stavu role na prostředků infrastruktury.
4. Spuštění WaHostBootstrapper a sledování, aby se ujistil, že role je ve stavu cíle.

**E**. WaHostBootstrapper je zodpovědný za:

1. Čtení konfigurace role a spuštění všech příslušných úloh a procesů pro konfiguraci a spuštění role.
2. Sledování všech jeho podřízených procesů.
3. Vyvolání události StatusCheck v procesu hostitele role.

**F**. IISConfigurator se spustí, pokud je role nakonfigurována jako úplná webová role služby IIS (nebude spuštěna pro role HWC sady SDK 1.2). Odpovídá za:

1. Spuštění standardních služeb IIS
2. Konfigurace modulu přepisu ve webové konfiguraci
3. Nastavení Fondu aplikací pro nakonfigurovanou roli v modelu služby
4. Nastavení protokolování služby IIS tak, aby přectolo na složku DiagnosticStore LocalStorage
5. Konfigurace oprávnění a seznamu ACL
6. Web je umístěn v %roleroot%:\sitesroot\0 a Fond aplikací odkazuje na toto umístění a spouští službu IIS. 

**G**. Úlohy při spuštění jsou definovány vzorem a spuštěny nástrojem WaHostBootstrapper. Úlohy při spuštění lze nakonfigurovat tak, aby se spouštěly na pozadí asynchronně, a zaváděcí nástroj hostitele spustí úlohu při spuštění a poté bude pokračovat na další úlohy při spuštění. Úlohy při spuštění lze také nakonfigurovat tak, aby se spouštěli v jednoduchém (výchozím) režimu, ve kterém bude hostitelský zaváděcí nástroj čekat na dokončení úlohy spuštění a vrátí úspěšný kód ukončení (0) před pokračováním na další úlohu při spuštění.

**H**. Tyto úkoly jsou součástí sady SDK a jsou definovány jako moduly plug-in v definici služby role (.csdef). Při rozšíření do úlohy spuštění **DiagnosticsAgent** a **RemoteAccessAgent** jsou jedinečné v tom, že každý definovat dva úlohy při spuštění, jeden pravidelný a jeden, který má **parametr /blockStartup.** Normální úloha při spuštění je definována jako úloha spuštění na pozadí, takže může být spuštěna na pozadí, zatímco je spuštěna samotná role. Úloha spuštění **/blockStartup** je definována jako úloha jednoduchého spuštění, takže wahostbootstrapper čeká na jeho ukončení před pokračováním. Úloha **/blockStartup** čeká na dokončení inicializace běžné úlohy a poté ukončí a umožní pokračovat v zaváděcím nástrojidlu hostitele. To se provádí tak, aby diagnostika a přístup k prv lze nakonfigurovat před spuštěním procesů role (to se provádí prostřednictvím /blockStartup úlohy). To také umožňuje diagnostiku a přístup k rdp pokračovat v běhu po hostitelský zaváděcí nástroj dokončil úlohy při spuštění (to se provádí prostřednictvím normální úlohy).

**I**. WaWorkerHost je standardní hostitelský proces pro normální role pracovního procesu. Tento hostitelský proces hostuje všechny knihovny DLL role a kód vstupního bodu, například OnStart a Run.

**J**. WaWebHost je standardní hostitelský proces pro webové role, pokud jsou nakonfigurovány pro použití Sady SDK 1.2 kompatibilní hostable Web Core (HWC). Role můžete povolit režim HWC odebráním prvku z definice služby (.csdef). V tomto režimu všechny kód služby a knihovny DLL spustit z procesu WaWebHost. Služba IIS (w3wp) se nepoužívá a ve Správci služby IIS nejsou nakonfigurovány žádné fondy aplikací, protože služba IIS je hostována uvnitř programu WaWebHost.exe.

**K**. WaIISHost je hostitelský proces pro kód vstupního bodu role pro webové role, které používají úplnou službu IIS. Tento proces načte první dll, který je nalezen, který používá **Třída RoleEntryPoint** a spustí kód z této třídy (OnStart, Run, OnStop). Všechny **roleenvironment** události (například StatusCheck a Changed), které jsou vytvořeny ve třídě RoleEntryPoint jsou vyvolány v tomto procesu.

**L**. W3WP je standardní pracovní proces služby IIS, který se používá, pokud je role nakonfigurována pro použití úplné služby IIS. To spustí AppPool, který je nakonfigurován z IISConfigurator. Všechny RoleEnvironment události (například StatusCheck a Changed), které jsou zde vytvořeny jsou vyvolány v tomto procesu. Všimněte si, že RoleEnvironment události se bude aktivovat v obou umístěních (WaIISHost a w3wp.exe), pokud se přihlásíte k odběru událostí v obou procesech.

## <a name="workflow-processes"></a>Pracovní postupy

1. Uživatel provede požadavek, například nahrávání souborů ".cspkg" a ".cscfg", sdělit prostředku zastavit nebo provedení změny konfigurace a tak dále. To lze provést prostřednictvím portálu Azure nebo nástroj, který používá rozhraní API pro správu služeb, jako je například funkce publikování visual studia. Tento požadavek je předává rdfe provést všechny práce související s předplatným a pak sdělit požadavek ffe. Zbývající kroky pracovního postupu jsou nasadit nový balíček a spustit jej.
2. FFE vyhledá správný fond strojů (na základě vstupu zákazníka, jako je například skupina spřažení nebo zeměpisná poloha plus vstup z prostředků infrastruktury, jako je například dostupnost stroje) a komunikuje s hlavním řadičem infrastruktury v tomto fondu strojů.
3. Řadič prostředků infrastruktury najde hostitele, který má k dispozici jádra procesoru (nebo se točí nový hostitel). Balíček služeb a konfigurace je zkopírována do hostitele a řadič prostředků infrastruktury komunikuje s hostitelským agentem v hostitelském osu k nasazení balíčku (konfigurace DIPs, porty, hostovaný operační systém a tak dále).
4. Hostitelský agent spustí hostovaný operační systém a komunikuje s agentem hosta (WindowsAzureGuestAgent). Hostitel odešle prezenční signály hostu, aby se ujistil, že role pracuje na jeho stavu cíle.
5. WindowsAzureGuestAgent nastaví hostovaný operační systém (brána firewall, seznamy ACL, LocalStorage a tak dále), zkopíruje nový konfigurační soubor XML do c:\Config a spustí proces WaHostBootstrapper.
6. U webových rolí služby IIS spustí nástroj WaHostBootstrapper službu IISConfigurator a sdělí mu, aby ze služby IIS odstranil všechny existující fondy aplikací pro webovou roli.
7. Nástroj WaHostBootstrapper přečte úlohy **po spuštění** z souboru E:\RoleModel.xml a zahájí provádění úloh při spuštění. WaHostBootstrapper čeká, až všechny jednoduché spuštění úkoly byly dokončeny a vrátil "úspěch" zprávu.
8. U webových rolí služby IIS wahostbootstrapper informuje iISConfigurator o konfiguraci `<index>` fondu iis apppoolu `<Sites>` a odkazuje web na `E:\Sitesroot\<index>`, kde je index založený na 0 na počet prvků definovaných pro službu.
9. WaHostBootstrapper spustí proces hostitele v závislosti na typu role:
    1. **Role pracovního procesu**: WaWorkerHost.exe je spuštěn. WaHostBootstrapper provede metodu OnStart(). Po vrátí, WaHostBootstrapper začne provádět Run() metoda a současně označí roli jako Ready a vloží ji do rotace nástroje pro vyrovnávání zatížení (pokud InputEndpoints jsou definovány). WaHostBootsrapper pak přejde do smyčky kontroly stavu role.
    1. **Webová role HwC sady SDK 1.2**: WaWebHost je spuštěn. WaHostBootstrapper provede metodu OnStart(). Po návratu WaHostBootstrapper začne provádět Run() metoda a současně označí roli jako Ready a vloží ji do rotace nástroje pro vyrovnávání zatížení. WaWebHost vydává žádost o zahřívání (GET /do.rd_runtime_init). Všechny webové požadavky jsou odesílány do souboru WaWebHost.exe. WaHostBootsrapper pak přejde do smyčky kontroly stavu role.
    1. **Úplná webová role služby IIS**: aIISHost je spuštěn. WaHostBootstrapper provede metodu OnStart(). Po vrátí, začne provádět Run() metoda a současně označí roli jako Ready a vloží ji do rotace vyrovnávání zatížení. WaHostBootsrapper pak přejde do smyčky kontroly stavu role.
10. Příchozí webové požadavky na webovou roli úplné služby IIS spustí službu IIS, aby spustila proces W3WP a obsluhovala požadavek stejně jako v místním prostředí služby IIS.

## <a name="log-file-locations"></a>Umístění souborů protokolu

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Tento protokol obsahuje změny služby včetně spuštění, zastavení a nové konfigurace. Pokud se služba nezmění, můžete očekávat, že v tomto souboru protokolu uvidíte velké časové mezery.
- C:\Protokoly\WaAppAgent.Log.  
Tento protokol obsahuje aktualizace stavu a upozornění na prezenční signála a je aktualizován každé 2-3 sekundy.  Tento protokol obsahuje historické zobrazení stavu instance a řekne, kdy instance nebyla ve stavu Připraveno.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaiISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISKonfigurátor**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**Protokoly iis**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Protokoly událostí systému Windows**

`D:\Windows\System32\Winevt\Logs`
 



