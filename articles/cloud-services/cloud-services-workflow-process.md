---
title: Pracovní postup architektury virtuálních počítačích Azure s Windows | Dokumentace Microsoftu
description: Tento článek obsahuje přehled procesů pracovního postupu při nasazení služby.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480739"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Pracovní postup architektura klasického virtuálního počítače Windows Azure 
Tento článek obsahuje přehled procesů pracovních postupů, ke kterým dochází při nasazení nebo aktualizovat prostředek Azure, jako je například virtuální počítač. 

> [!NOTE]
>Azure má dva různé modely nasazení pro vytváření a práci s prostředky: Resource Manager a classic. Tento článek se věnuje použití klasického modelu nasazení.

V níže uvedeném diagramu architektury prostředků Azure.

![Azure pracovního postupu](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Základní informace o pracovním postupu
   
**A**. RDFE / FFE je komunikační trasa od uživatele do prostředků infrastruktury. RDFE (RedDog Front End) je veřejně vystavené rozhraní API, která je front-endu na portálu Management Portal a rozhraní API pro správu služby jako je Visual Studio, Azure konzoly MMC a tak dále.  Všechny žádosti od uživatele, projděte si RDFE. FFE (Fabric front-endu) je vrstva, která se překládá požadavky z RDFE příkazy prostředků infrastruktury. Všechny žádosti od RDFE projít FFE k dosažení kontrolery prostředcích infrastruktury.

**B**. Kontroler prostředků infrastruktury je zodpovědná za údržbu a monitorování všech prostředků v datovém centru. Komunikuje s agenty hostitele prostředků infrastruktury v prostředcích infrastruktury odeslání informací operačního systému, jako je například verze hostovaného operačního systému, balíček služby, konfigurace služby a stav služby.

**C**. Agent hostitele může fungovat OSsystem hostitele a je zodpovědný za nastavení hostovaného operačního systému a komunikaci s agentem hosta (WindowsAzureGuestAgent), aby bylo možné aktualizovat roli směrem k určené cílový stav a proveďte prezenčního signálu zkontroluje agenta hosta. Pokud Agent hostitele neobdrží odpověď prezenčního signálu po dobu 10 minut, Agent hostitele restartuje hostovaného operačního systému.

**C2**. WaAppAgent zodpovídá za instalaci, konfiguraci a aktualizaci WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent zodpovídá za následující:

1. Konfigurace hostovaného operačního systému, včetně brány firewall, seznamy ACL, LocalStorage prostředky, balíček služby a konfigurace a certifikáty. Nastavení SID pro uživatelský účet, pod kterými poběží role.
2. Stav role komunikaci do prostředků infrastruktury.
3. Spuštění WaHostBootstrapper a monitorování tak, že role je ve stavu cíle.

**E**. WaHostBootstrapper je zodpovědný za:

1. Čte se konfigurace role, a spouští všechny příslušné úlohy a procesy pro konfiguraci a spuštění role.
2. Monitorování všech jeho podřízených procesů.
3. Vyvolání události StatusCheck na hostitelský proces role.

**F**. IISConfigurator spustí v případě, že role je nakonfigurovaný jako webová role úplnou službu IIS (nebude fungovat u sady SDK 1.2 HWC rolí). Je zodpovědná za:

1. Spouští se standardní služby IIS
2. Konfigurace modulu revize v konfiguraci webové
3. Nastavení fondu aplikací pro roli nakonfigurované v modelu služby
4. Nastavení protokolování služby IIS tak, aby odkazoval na složku DiagnosticStore LocalStorage
5. Konfiguraci oprávnění a seznamy ACL
6. Na webu se nachází v % roleroot%:\sitesroot\0 a body fondu aplikací do tohoto umístění, aby spustil službu IIS. 

**G**. Úlohy po spuštění jsou určené role modelu a tím, že WaHostBootstrapper. Úlohy po spuštění může být nakonfigurován pro spouštění na pozadí asynchronně a spustí úlohy po spuštění a pokračujte k další úlohy po spuštění hostitele zaváděcí nástroj. Úlohy po spuštění můžete nakonfigurovat také pro spuštění v režimu jednoduchého (výchozí), ve kterém hostitele zaváděcí nástroj bude čekat na skončí a vrátit ukončovací kód úspěchu (0) před pokračováním k dalšímu úkolu po spuštění úlohy po spuštění.

**H**. Tyto úlohy jsou součástí sady SDK a jsou definované jako moduly plug-in v definici role služby (.csdef). Při rozbalení do úlohy po spuštění **DiagnosticsAgent** a **RemoteAccessAgent** jsou jedinečné tím, každá definovat dvě úlohy po spuštění, jeden běžný a ten, který má **/blockStartup** parametru. Normální spuštění úkolu je definován jako úlohu na pozadí při spuštění tak, aby ho může běžet na pozadí je spuštěna role samotný. **/BlockStartup** úloha po spuštění je definován jako úloha po spuštění jednoduché tak, aby WaHostBootstrapper čeká na jeho před pokračováním ukončete. **/BlockStartup** úloha čeká na dokončení inicializace pravidelné úlohy, a poté ukončí a povolit zaváděcí nástroj hostitele pokračujte. To se provádí tak, aby Diagnostika a přístup protokolu RDP, je možné nakonfigurovat před začátkem procesy role (to se provádí prostřednictvím úkolu /blockStartup). To také umožňuje přístup protokolu RDP, aby kontinuálně běžely, po dokončení úlohy po spuštění (to se provádí prostřednictvím normální úlohy) zaváděcí nástroj hostitele a Diagnostika.

**I**. WaWorkerHost je standardní hostitelský proces pro normální pracovních rolí. Knihovny DLL a vstupní bod kódu, jako je operace OnStart a spustit všechny role je hostitelem tohoto hostitelského procesu.

**J**. WaWebHost je standardní hostitelský proces pro webové role, pokud jsou nakonfigurovány pro použití sadu SDK 1.2 kompatibilní s možnostmi hostitele webové jádro (HWC). Role můžete povolit režim HWC odebráním element z definice služby (.csdef). V tomto režimu spusťte kód všechny služby a knihovny DLL z procesu WaWebHost. Služba IIS (w3wp) se nepoužívá a neexistují žádné fondu aplikací nakonfigurovat ve Správci služby IIS, protože je uvnitř WaWebHost.exe, hostovaná IIS.

**K**. WaIISHost je hostitelský proces pro role vstupní bod kódu pro webové role, které používají úplnou službu IIS. Tento proces načte první knihovnu DLL, která se nachází, který používá **RoleEntryPoint** třídy a spustí kód z této třídy (při spuštění, spuštění, OnStop). Žádné **RoleEnvironment** událostí (například StatusCheck a změna), které jsou vytvořeny ve třídě RoleEntryPoint jsou vyvolány v tomto procesu.

**L**. W3wp je standardní pracovní proces služby IIS, který se používá v případě, že role je nakonfigurován na použití úplnou službu IIS. Toto řešení běží z IISConfigurator fondu aplikací, který je nakonfigurovaný. V tomto procesu jsou vyvolány žádné RoleEnvironment události (jako je například StatusCheck a změna), které jsou vytvořené v tomto poli. Všimněte si, že RoleEnvironment události se aktivují v obou umístěních (WaIISHost a w3wp.exe), pokud jste přihlášení k odběru událostí v oba tyto procesy.

## <a name="workflow-processes"></a>Pracovní postupy

1. Uživatel odešle požadavek, jako jsou nahrávání souborů .cspkg a .cscfg, sděluje zdroje stop, nebo provedením změny konfigurace, a tak dále. To lze provést prostřednictvím webu Azure portal nebo nástroj, který používá rozhraní API pro správu služby, jako je například funkci Publikovat Visual Studio. Tento požadavek přejde na RDFE provedete všechny týkajících se předplatného práci a pak požadavek na FFE komunikovat. Zbývající část těchto kroků pracovního postupu se nový balíček pro nasazení, spusťte ji.
2. FFE nalezne správné počítače fondu (na základě vstupu zákazníka, jako skupina vztahů nebo zeměpisné umístění a vstup z prostředků infrastruktury, jako je například dostupnost počítače) a komunikuje s kontrolerem hlavní prostředků infrastruktury v tomto fondu počítače.
3. Kontroler prostředků infrastruktury zjistí, která je dostupná jádra procesoru hostitele (nebo přede nahoru nového hostitele). Balíček služby a konfiguraci se zkopíruje do hostitele a kontroler prostředků infrastruktury komunikuje s agentem hostitele na hostitelském operačním systému pro nasazení balíčku (konfigurace vyhrazené IP adresy, portů, hostovaný operační systém a tak dále).
4. Agent hostitele spustí hostovaného operačního systému a komunikuje s agentem hosta (WindowsAzureGuestAgent). Hostitel odešle prezenční signály hosta, abyste měli jistotu, že role funguje směrem k jeho cílový stav.
5. WindowsAzureGuestAgent nastaví hostovaného operačního systému (brány firewall, seznamy ACL, LocalStorage a tak dále), zkopíruje nový soubor XML konfigurace c:\Config a pak spustí proces WaHostBootstrapper.
6. Pro úplnou službu IIS webové role WaHostBootstrapper spustí IISConfigurator a že má-li odstranit všechny existující fondu aplikací pro webovou roli služby IIS.
7. Přečte WaHostBootstrapper **spuštění** úkoly z E:\RoleModel.xml a zahájí provádění úloh po spuštění. WaHostBootstrapper počká, až všechny úkoly po spuštění jednoduché bylo dokončeno a vrátil zprávu "ÚSPĚCH".
8. Pro úplnou službu IIS webové role WaHostBootstrapper říká IISConfigurator ke konfiguraci fondu aplikací služby IIS a bodů lokality tak, aby `E:\Sitesroot\<index>`, kde `<index>` je 0 na základě indexu do počtu <Sites> prvky definované pro službu.
9. WaHostBootstrapper spustí proces hostitele v závislosti na typu role:
    1. **Role pracovního procesu**: WaWorkerHost.exe je spuštěn. WaHostBootstrapper provede metodu operace OnStart(). Po jeho vrácení, WaHostBootstrapper spuštění metodu Run() současně označí jako připravené role a vloží je do oběhu nástroje pro vyrovnávání zatížení (je-li definována InputEndpoints). WaHostBootsrapper pak přejde do smyčku kontroluje se stav role.
    1. **Sada SDK 1.2 HWC webová Role**: WaWebHost je spuštěn. WaHostBootstrapper provede metodu operace OnStart(). Po jeho vrácení, WaHostBootstrapper spuštění metodu Run() a současně označí jako připravené role a vloží je do oběhu nástroje pro vyrovnávání zatížení. WaWebHost vydá požadavek na zahřívání (GET /do.rd_runtime_init). Všechny webové požadavky se posílají WaWebHost.exe. WaHostBootsrapper pak přejde do smyčku kontroluje se stav role.
    1. **Úplné služby IIS webová Role**: aIISHost je spuštěná. WaHostBootstrapper provede metodu operace OnStart(). Po jeho vrácení, spustí se vykonat metodu Run() a současně označí jako připravené role a vloží je do oběhu nástroje pro vyrovnávání zatížení. WaHostBootsrapper pak přejde do smyčku kontroluje se stav role.
10. Příchozích webových požadavků pro úplnou službu IIS webové role aktivace služby IIS obsluhovat žádosti, stejně jako by tomu bylo v IIS v místním prostředí a zahájit proces W3WP.

## <a name="log-file-locations"></a>Umístění souboru protokolu

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Tento protokol obsahuje změny ve službě, včetně spuštění, zastavení a nové konfigurace. Pokud služba nezmění, které můžete očekávat velké mezery čas v tomto souboru protokolu.
- C:\Logs\WaAppAgent.Log.  
Tento protokol obsahuje aktualizace stavu a upozornění prezenčního signálu a je aktualizována každé 2-3 sekundy.  Tento protokol obsahuje Historický pohled na stav instance a dozvíte se, když instance nebyla ve stavu Připraveno.
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid >.<role> \WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID >.<role> \WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**Protokoly IIS**

C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1
 
**Protokoly událostí Windows**

D:\Windows\System32\Winevt\Logs
 



