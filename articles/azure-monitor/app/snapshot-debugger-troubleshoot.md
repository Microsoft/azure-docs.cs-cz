---
title: Řešení potíží s Azure Application Insights Snapshot Debugger
description: Tento článek představuje postup řešení potíží a informace, které vývojářům pomůžou při povolování a používání Application Insights Snapshot Debugger.
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671405"
---
# <a id="troubleshooting"></a>Řešení potíží s povolením Application Insights Snapshot Debugger nebo zobrazením snímků
Pokud jste u své aplikace povolili Application Insights Snapshot Debugger, ale nevidíte snímky pro výjimky, můžete tyto pokyny použít k řešení potíží. Může existovat mnoho různých důvodů, proč se snímky negenerují. Můžete spustit kontrolu stavu snímku a identifikovat některé z možných běžných příčin.

## <a name="use-the-snapshot-health-check"></a>Použít kontrolu stavu snímku
Několik běžných problémů za následek otevřít snímek ladění nezobrazuje. Pomocí zastaralé Snapshot Collector, například dosažení denního limitu pro nahrávání; nebo možná snímku je právě trvá příliš dlouho k nahrání. Pomocí kontroly stavu. snímek s řešením běžných problémů.

V podokně výjimka trasování začátku do konce zobrazení, která vás ke kontrole stavu snímku je odkaz.

![Zadejte Kontrola stavu snímku](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interaktivní rozhraní jako chat hledá běžné problémy a provede vás a opravte je.

![Kontrola stavu](./media/snapshot-debugger/healthcheck.png)

Pokud se problém nevyřeší, pak použijte Manuál ke následujících kroků pro řešení potíží.

## <a name="verify-the-instrumentation-key"></a>Ověřte, že Instrumentační klíč

Ujistěte se, že používáte správné Instrumentační klíč v publikované aplikaci. Instrumentační klíč je obvykle čtení ze souboru ApplicationInsights.config. Ověřte, že hodnota je stejná jako Instrumentační klíč pro prostředek služby Application Insights, který se zobrazí na portálu.

## <a name="preview-versions-of-net-core"></a>Verze Preview rozhraní .NET Core
Pokud aplikace používá verzi Preview rozhraní .NET Core a Snapshot Debugger byla povolena prostřednictvím [podokna Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) na portálu, Snapshot Debugger pravděpodobně nebude možné spustit. Postupujte podle pokynů v části [povolit Snapshot debugger pro další prostředí](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , abyste před povolením prostřednictvím [podokna Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)zahrnuli ***také*** balíček NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) .


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade na nejnovější verzi balíčku NuGet.

Pokud byla Snapshot Debugger povolena prostřednictvím [podokna Application Insights na portálu](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), měla by již vaše aplikace používat nejnovější balíček NuGet. Pokud byla povolená Snapshot Debugger zahrnutím balíčku NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , použijte Správce balíčků NuGet sady Visual Studio a ujistěte se, že používáte nejnovější verzi Microsoft. ApplicationInsights. SnapshotCollector. Poznámky k verzi najdete na adrese https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>V protokolech uživatele nahrávajícího

Po vytvoření snímku na disk se vytvoří soubor minimálního výpisu (.dmp). Samostatné uživatele nahrávajícího procesu vytvoří tento minimální výpis soubor a nahraje ho, spolu s všechny přidružené soubory PDB do Application Insights Snapshot debuggeru úložiště. Po úspěšném odeslání minimálním výpisu je odstranit z disku. Na disku jsou uloženy soubory protokolu pro proces odeslání. V prostředí App Service můžete tyto protokoly najít v `D:\Home\LogFiles`. Použijte správu webu kudu pro službu App Service k vyhledání těchto souborů protokolu.

1. Otevřete aplikaci služby App Service na webu Azure Portal.
2. Klikněte na **Rozšířené nástroje**nebo vyhledejte **Kudu**.
3. Klikněte na **Přejít**.
4. V rozevíracím seznamu **Konzola ladění** vyberte **cmd**.
5. Klikněte na **soubory protokolu**.

Měl by se zobrazit aspoň jeden soubor s názvem, který začíná `Uploader_` nebo `SnapshotUploader_` a příponou `.log`. Klikněte na příslušnou položku, abyste si stáhli všechny soubory protokolů nebo otevřete v prohlížeči.
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
> Výše uvedený příklad je z balíčku Microsoft.ApplicationInsights.SnapshotCollector NuGet verzi 1.2.0. V dřívějších verzích se proces odeslání nazývá `MinidumpUploader.exe` a protokol je méně podrobný.

V předchozím příkladu je klíč instrumentace `c12a605e73c44346a984e00000000000`. Tato hodnota by měla odpovídat Instrumentační klíč pro vaši aplikaci.
S minimálním výpisem je přidružen k snímku s ID `139e411a23934dc0b9ea08a626db16c5`. Toto ID můžete použít později k vyhledání telemetrie související výjimek v Application Insights Analytics.

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

V případě aplikací, které _nejsou_ hostované v App Service, jsou protokoly odeslání ve stejné složce jako mini výpisy: `%TEMP%\Dumps\<ikey>` (kde `<ikey>` je klíč instrumentace).

## <a name="troubleshooting-cloud-services"></a>Řešení potíží s Cloud Services
Pro role v cloudových službách může být příliš nízká k uložení souborů s minimálním výpisem, což vede ke ztrátě snímků výchozí dočasné složky.
Místo potřeby závisí na celkové pracovní sada aplikace, a počet souběžných snímků.
Pracovní sada webová role ASP.NET 32 bitů je obvykle 200 MB až 500 MB.
Povolit pro alespoň dva souběžné snímky.
Například pokud vaše aplikace používá 1 GB celkového pracovní sady, by měl zajistíte, že je minimálně 2 GB místa na disku pro ukládání snímků.
Postupujte podle těchto kroků a nakonfigurujte roli vaší cloudové služby pomocí vyhrazené místní prostředek pro snímky.

1. Přidejte nový místní prostředek ke cloudové službě úpravou souboru definičních (.csdef) cloudové služby. V následujícím příkladu je definován prostředek s názvem `SnapshotStore` o velikosti 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Úpravou spouštěcího kódu role přidejte proměnnou prostředí, která odkazuje na `SnapshotStore` místní prostředek. Pro role pracovního procesu by se kód měl přidat do `OnStart` metody vaší role:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   V případě webových rolí (ASP.NET) by se měl kód přidat do metody `Application_Start` vaší webové aplikace:
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

3. Aktualizujte soubor ApplicationInsights. config vaší role pro přepsání umístění dočasné složky používaného `SnapshotCollector`
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

Pokud se vhodná složka nenajde, Snapshot Collector nahlásí chybu oznamující, že _se nepovedlo najít vhodnou složku stínové kopie._

Pokud se kopie nezdařila, Snapshot Collector hlásí chybu `ShadowCopyFailed`.

Pokud nelze odesláním odeslání spustit, Snapshot Collector hlásí chybu `UploaderCannotStartFromShadowCopy`. Tělo zprávy často obsahuje `System.UnauthorizedAccessException`. K této chybě obvykle dochází, protože je aplikace spuštěna pod účtem s omezenými oprávněními. Účet má oprávnění k zápisu do složky stínové kopie, ale nemá oprávnění k provádění kódu.

Vzhledem k tomu, že k těmto chybám obvykle dochází při spuštění, obvykle se jedná o `ExceptionDuringConnect` chybu oznamující, že _se nepovedlo spustit odeslání._

Pokud chcete tyto chyby obejít, můžete ručně zadat složku stínové kopie pomocí možnosti konfigurace `ShadowCopyFolder`. Například pomocí souboru ApplicationInsights.config:

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

Při vytváření snímku aktivační výjimky označené pomocí ID snímku. Toto ID snímku je dostupná jako vlastní vlastnost při telemetrie výjimek se hlásí do Application Insights. Pomocí **vyhledávání** v Application Insights můžete najít veškerou telemetrii pomocí vlastní vlastnosti `ai.snapshot.id`.

1. Přejděte do prostředku Application Insights na webu Azure Portal.
2. Klikněte na tlačítko **Hledat**.
3. Do textového pole hledání zadejte `ai.snapshot.id` a stiskněte klávesu ENTER.

![Hledat telemetrii s ID snímku na portálu](./media/snapshot-debugger/search-snapshot-portal.png)

Pokud toto hledání nebyly vráceny žádné výsledky, pak žádné snímky se hlásí do Application Insights pro vaši aplikaci ve vybraném časovém rozsahu.

K vyhledání ID konkrétní snímek z nástroje odeslání protokolů, zadejte do vyhledávacího pole tímto Identifikátorem. Pokud nelze najít telemetrii pro snímek, o kterém víte, že byl odeslán, postupujte podle těchto kroků:

1. Zkontrolujte, že se díváte na správný prostředek Application Insights tak, že ověříte Instrumentační klíč.

2. Pomocí časové razítko od odeslání protokolů, upravte filtr časový rozsah vyhledávání pro tento časový rozsah.

Pokud stále nevidíte výjimka s tímto ID snímku, nebyl telemetrie výjimek hlásí do Application Insights. Tato situace může nastat, pokud vaše aplikace došlo k chybě po, jakou trvalo snímku, ale před oznámila telemetrie výjimek. V takovém případě zkontrolujte protokoly App Service v části `Diagnose and solve problems` a zjistěte, jestli nedošlo k neočekávanému restartování nebo neošetřené výjimce.

## <a name="edit-network-proxy-or-firewall-rules"></a>Upravit pravidla proxy nebo brány firewall sítě

Pokud vaše aplikace připojuje k Internetu přes proxy server nebo brána firewall, budete muset upravit pravidla, aby vaše aplikace mohla komunikovat se službou Snapshot Debugger. IP adresy, které používá Snapshot Debugger, jsou součástí značky služby Azure Monitor.
