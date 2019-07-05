---
title: Poradce při potížích s Azure Application Insights Snapshot debuggeru. | Dokumentace Microsoftu
description: Tento článek představuje řešení problémů s kroky a informace, které pomůžou vývojářům, kteří jsou potíže s povolením nebo pomocí Application Insights Snapshot debuggeru.
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: 25ccf20fc78a9ec00d4dfe23a60e824e96d12945
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444548"
---
# <a id="troubleshooting"></a> Poradce při potížích povolení Application Insights Snapshot debuggeru nebo zobrazení snímků
Je-li povolit Application Insights Snapshot debuggeru pro aplikaci, ale nezobrazují se snímky pro výjimky, můžete použít tyto pokyny k řešení potíží s. Může existovat mnoho různých důvodů, proč se negenerují snímky. Můžete spustit kontrolu stavu snímku k identifikaci některé z běžných příčin.

## <a name="use-the-snapshot-health-check"></a>Použít kontrolu stavu snímku
Několik běžných problémů za následek otevřít snímek ladění nezobrazuje. Pomocí zastaralé Snapshot Collector, například dosažení denního limitu pro nahrávání; nebo možná snímku je právě trvá příliš dlouho k nahrání. Pomocí kontroly stavu. snímek s řešením běžných problémů.

V podokně výjimka trasování začátku do konce zobrazení, která vás ke kontrole stavu snímku je odkaz.

![Zadejte Kontrola stavu snímku](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interaktivní rozhraní jako chat hledá běžné problémy a provede vás a opravte je.

![Kontrola stavu](./media/snapshot-debugger/healthcheck.png)

Pokud se problém nevyřeší, pak použijte Manuál ke následujících kroků pro řešení potíží.

## <a name="verify-the-instrumentation-key"></a>Ověřte, že Instrumentační klíč

Ujistěte se, že používáte správné Instrumentační klíč v publikované aplikaci. Instrumentační klíč je obvykle čtení ze souboru ApplicationInsights.config. Ověřte, že hodnota je stejná jako Instrumentační klíč pro prostředek služby Application Insights, který se zobrazí na portálu.

## <a name="preview-versions-of-net-core"></a>Verze Preview sady .NET Core
Pokud aplikace používá verzi preview sady .NET Core a byl zajišťuje Snapshot Debugger [Application Insights podokně](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) na portálu, pak Snapshot Debugger nespustí. Postupujte podle pokynů na adrese [povolit Snapshot Debugger pro jiná prostředí](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) nejprve chcete zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíček NuGet s aplikací ***kromě*** se povolí až [Application Insights podokně](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade na nejnovější verzi balíčku NuGet.

Pokud byl zajišťuje Snapshot Debugger [podokně Application Insights na portálu](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), pak vaše aplikace by již být spuštěné nejnovější balíček NuGet. Pokud ladicí program snímků se aktivovala při včetně [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) balíčku NuGet, Správce balíčků NuGet pomocí Visual Studio, abyste měli jistotu, že používáte nejnovější verzi Microsoft.ApplicationInsights.SnapshotCollector. Zpráva k vydání verze najdete v https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>V protokolech uživatele nahrávajícího

Po vytvoření snímku na disk se vytvoří soubor minimálního výpisu (.dmp). Samostatné uživatele nahrávajícího procesu vytvoří tento minimální výpis soubor a nahraje ho, spolu s všechny přidružené soubory PDB do Application Insights Snapshot debuggeru úložiště. Po úspěšném odeslání minimálním výpisu je odstranit z disku. Na disku jsou uloženy soubory protokolu pro proces odeslání. Ve službě App Service environment najdete v těchto protokolech `D:\Home\LogFiles`. Použijte správu webu kudu pro službu App Service k vyhledání těchto souborů protokolu.

1. Otevřete aplikaci služby App Service na webu Azure Portal.
2. Klikněte na tlačítko **Rozšířené nástroje**, nebo vyhledejte **Kudu**.
3. Klikněte na tlačítko **Přejít**.
4. V **konzolou pro ladění** rozevíracího seznamu vyberte **CMD**.
5. Klikněte na tlačítko **LogFiles**.

Měli byste vidět alespoň jeden soubor s názvem, který začíná `Uploader_` nebo `SnapshotUploader_` a `.log` rozšíření. Klikněte na příslušnou položku, abyste si stáhli všechny soubory protokolů nebo otevřete v prohlížeči.
Název souboru obsahuje jedinečnou příponu, která identifikuje instanci služby App Service. Pokud vaše instance služby App Service je hostovaná na více než jednom počítači, existují samostatné soubory protokolu pro každý počítač. Když uživatele nahrávajícího zjistí nový soubor s minimálním výpisem, se zaznamená do souboru protokolu. Tady je příklad snímku úspěšné a nahrávání:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Výše uvedený příklad je z balíčku Microsoft.ApplicationInsights.SnapshotCollector NuGet verzi 1.2.0. V dřívějších verzích uživatele nahrávajícího proces se nazývá `MinidumpUploader.exe` a v protokolu je méně podrobný.

V předchozím příkladu je Instrumentační klíč `c12a605e73c44346a984e00000000000`. Tato hodnota by měla odpovídat Instrumentační klíč pro vaši aplikaci.
Minimální výpis je přidružený k snímku s ID `139e411a23934dc0b9ea08a626db16c5`. Toto ID můžete použít později k vyhledání telemetrie související výjimek v Application Insights Analytics.

Uživatele nahrávajícího hledá nové soubory PDB o každých 15 minut. Tady je příklad:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Pro aplikace, která _nejsou_ hostované ve službě App Service, uživatele nahrávajícího protokoly jsou ve stejné složce jako mini výpisy: `%TEMP%\Dumps\<ikey>` (kde `<ikey>` je Instrumentační klíč).

## <a name="troubleshooting-cloud-services"></a>Řešení potíží s Cloud Services
Pro role v cloudových službách může být příliš nízká k uložení souborů s minimálním výpisem, což vede ke ztrátě snímků výchozí dočasné složky.
Místo potřeby závisí na celkové pracovní sada aplikace, a počet souběžných snímků.
Pracovní sada webová role ASP.NET 32 bitů je obvykle 200 MB až 500 MB.
Povolit pro alespoň dva souběžné snímky.
Například pokud vaše aplikace používá 1 GB celkového pracovní sady, by měl zajistíte, že je minimálně 2 GB místa na disku pro ukládání snímků.
Postupujte podle těchto kroků a nakonfigurujte roli vaší cloudové služby pomocí vyhrazené místní prostředek pro snímky.

1. Přidejte nový místní prostředek ke cloudové službě úpravou souboru definičních (.csdef) cloudové služby. V následujícím příkladu je definován prostředek s názvem `SnapshotStore` s velikostí 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Upravit vaše role při spuštění kód k přidání proměnné prostředí, která odkazuje na `SnapshotStore` místního prostředku. Rolí pracovních procesů, by měl přidat kód pro vaši roli `OnStart` metody:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Pro webové role (ASP.NET), kód má přidat do vaší webové aplikaci `Application_Start` metody:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Aktualizovat soubor ApplicationInsights.config vaše role k přepsání umístění dočasné složky používají `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Přepsání složka stínové kopie

Při spuštění Snapshot Collector, pokusí se najít složku na disku, který je vhodný pro spuštění procesu uživatele Nahrávajícího snímku. Složka stínové kopie se nazývá zvolenou složku.

Snapshot Collector zkontroluje několik známých umístění, ujistěte se, že má oprávnění ke zkopírování binárních souborů uživatele Nahrávajícího snímku. Používají se následující proměnné prostředí:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- DATA APLIKACÍ
- TEMP

Pokud se nenašel vhodný složky, Snapshot Collector sestavy o tom, že chyba _"Nelze najít složku vhodný stínové kopie."_

Pokud kopie nezdaří, sestavy Snapshot Collector `ShadowCopyFailed` chyby.

Pokud uživatele nahrávajícího nejde spustit, Snapshot Collector sestavy `UploaderCannotStartFromShadowCopy` chyba. Tělo zprávy obsahuje často `System.UnauthorizedAccessException`. K této chybě obvykle dochází, protože je aplikace spuštěna pod účtem s omezenými oprávněními. Účet má oprávnění k zápisu do složky stínové kopie, ale nemá oprávnění k provádění kódu.

Vzhledem k tomu, že tyto chyby se obvykle dojít během spouštění, že budete obvykle následovat `ExceptionDuringConnect` o tom, že chyba _"Uživatele Nahrávajícího se nepodařilo spustit."_

Chcete-li vyřešit tyto chyby, můžete určit složky stínové kopie ručně přes `ShadowCopyFolder` možnosti konfigurace. Například pomocí souboru ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Nebo, pokud používáte aplikaci .NET Core appsettings.json:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Abyste mohli najít výjimky se snímky hledání pomocí služby Application Insights

Při vytváření snímku aktivační výjimky označené pomocí ID snímku. Toto ID snímku je dostupná jako vlastní vlastnost při telemetrie výjimek se hlásí do Application Insights. Pomocí **hledání** ve službě Application Insights, můžete najít všechny telemetrie s `ai.snapshot.id` vlastní vlastnosti.

1. Přejděte do prostředku Application Insights na webu Azure Portal.
2. Klikněte na **Hledat**.
3. Typ `ai.snapshot.id` textového pole pro vyhledávání a stiskněte Enter.

![Hledat telemetrii s ID snímku na portálu](./media/snapshot-debugger/search-snapshot-portal.png)

Pokud toto hledání nebyly vráceny žádné výsledky, pak žádné snímky se hlásí do Application Insights pro vaši aplikaci ve vybraném časovém rozsahu.

K vyhledání ID konkrétní snímek z nástroje odeslání protokolů, zadejte do vyhledávacího pole tímto Identifikátorem. Pokud nelze najít telemetrii pro snímek, o kterém víte, že byl odeslán, postupujte podle těchto kroků:

1. Zkontrolujte, že se díváte na správný prostředek Application Insights tak, že ověříte Instrumentační klíč.

2. Pomocí časové razítko od odeslání protokolů, upravte filtr časový rozsah vyhledávání pro tento časový rozsah.

Pokud stále nevidíte výjimka s tímto ID snímku, nebyl telemetrie výjimek hlásí do Application Insights. Tato situace může nastat, pokud vaše aplikace došlo k chybě po, jakou trvalo snímku, ale před oznámila telemetrie výjimek. V takovém případě v protokolech služby App Service v rámci `Diagnose and solve problems` zobrazíte, pokud došlo k neočekávané restartování nebo neošetřených výjimek.

## <a name="edit-network-proxy-or-firewall-rules"></a>Upravit pravidla proxy nebo brány firewall sítě

Pokud vaše aplikace připojuje k Internetu přes proxy server nebo brána firewall, budete muset upravit pravidla, aby vaše aplikace mohla komunikovat se službou Snapshot Debugger. Tady je [seznam IP adres a portů používaných Snapshot Debugger](../../azure-monitor/app/ip-addresses.md#snapshot-debugger).
