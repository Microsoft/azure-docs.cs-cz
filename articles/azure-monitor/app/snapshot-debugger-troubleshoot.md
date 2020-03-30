---
title: Poradce při potížích s ladicím programem pro přehledy přehledů aplikací Azure
description: Tento článek představuje postupy řešení potíží a informace, které pomáhají vývojářům, kteří mají potíže s povolením nebo použitím ladicího programu snímek Application Insights.
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671405"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a>Poradce při potížích se povolením debakuaonu snímků přehledů aplikací nebo zobrazení snímků
Pokud jste povolili Debugger snímků Application Insights pro vaši aplikaci, ale nezobrazují snímky pro výjimky, můžete použít tyto pokyny k řešení potíží. Může existovat mnoho různých důvodů, proč nejsou generovány snímky. Můžete spustit kontrolu stavu snímek k identifikaci některé z možných běžných příčin.

## <a name="use-the-snapshot-health-check"></a>Použití kontroly stavu snímku
Několik běžných problémů za následek open ladění snímek nezobrazuje. Použití zastaralé snímek kolektoru, například; dosažení denního limitu pro nahrávání; nebo snad snímek je jen s dlouhou dobu nahrát. Pomocí kontroly stavu snímek řešení běžných problémů.

V podokně výjimek zobrazení trasování mezi koncovými kroky je odkaz, který vás přenese do kontroly stavu snímek.

![Zadat kontrolu stavu snímku](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interaktivní rozhraní podobné chatu hledá běžné problémy a vede vás k jejich opravě.

![Kontrola stavu](./media/snapshot-debugger/healthcheck.png)

Pokud se tím problém nevyřeší, přečtěte si následující postup řešení potíží.

## <a name="verify-the-instrumentation-key"></a>Ověření klíče instrumentace

Ujistěte se, že používáte správný klíč instrumentace v publikované aplikaci. Klíč instrumentace se obvykle čte ze souboru ApplicationInsights.config. Ověřte, zda je hodnota stejná jako klíč instrumentace pro prostředek Application Insights, který se zobrazí na portálu.

## <a name="preview-versions-of-net-core"></a>Náhled verzí jádra .NET
Pokud aplikace používá verzi náhledu .NET Core a debugger snímků byl povolen prostřednictvím [podokna Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) na portálu, pak se debugger snímků nemusí spustit. Postupujte podle pokynů na [povolit snímek debugger pro jiná prostředí](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) nejprve zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet balíček s aplikací ***kromě*** povolení prostřednictvím [podokna Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade na nejnovější verzi balíčku NuGet

Pokud byl ladicí program snímků povolen prostřednictvím [podokna Application Insights na portálu](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), měla by vaše aplikace již spustit nejnovější balíček NuGet. Pokud byl ladicí program snímků povolen zahrnutím balíčku [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet, použijte Správce balíčků NuGet sady Visual Studio a ujistěte se, že používáte nejnovější verzi Microsoft.ApplicationInsights.SnapshotCollector. Poznámky k verzi naleznete na adresehttps://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Zkontrolujte protokoly nahrávače

Po vytvoření snímku je na disku vytvořen soubor minidump (DMP). Samostatný proces nahrávání vytvoří tento soubor minidump a nahraje jej spolu se všemi přidruženými soubory PDB do úložiště debuggeru snímků application insights. Po úspěšném nahrání minidumpu se odstraní z disku. Soubory protokolu pro proces nahrávání jsou uloženy na disku. V prostředí služby App Service najdete `D:\Home\LogFiles`tyto protokoly v . Pomocí webu správy Kudu pro službu App Service vyhledání těchto souborů protokolu.

1. Otevřete aplikaci App Service na webu Azure Portal.
2. Klepněte na **položku Upřesnit nástroje**nebo vyhledejte **položku Kudu**.
3. Klepněte na tlačítko **Přejít**.
4. V rozevíracím seznamu **Ladicí konzola** vyberte **cmd**.
5. Klepněte na **položku LogFiles**.

Měli byste vidět alespoň jeden soubor s `Uploader_` názvem, který začíná nebo `SnapshotUploader_` a příponou. `.log` Kliknutím na příslušnou ikonu stáhnete soubory protokolu nebo je otevřete v prohlížeči.
Název souboru obsahuje jedinečnou příponu, která identifikuje instanci služby App Service. Pokud je vaše instance služby App Service hostovaná na více než jednom počítači, existují pro každý počítač samostatné soubory protokolu. Když nahrávač zjistí nový soubor minidump, zaznamená se do souboru protokolu. Zde je příklad úspěšného snímku a nahrání:

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
> Výše uvedený příklad je z verze 1.2.0 balíčku Microsoft.ApplicationInsights.SnapshotCollector NuGet. V dřívějších verzích je volána `MinidumpUploader.exe` proces nahrávání a protokol je méně podrobné.

V předchozím příkladu je `c12a605e73c44346a984e00000000000`klíč instrumentace . Tato hodnota by měla odpovídat klíč instrumentace pro vaši aplikaci.
Minidump je spojena s snímek `139e411a23934dc0b9ea08a626db16c5`s ID . Toto ID můžete později použít k vyhledání související telemetrie výjimek v Application Insights Analytics.

Nahrávač skenuje nové PDBs asi jednou za 15 minut. Tady je příklad:

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

Pro aplikace, které _nejsou_ hostované ve službě App Service, protokoly nahrále `%TEMP%\Dumps\<ikey>` e-potomka jsou ve stejné složce jako minidumps: (kde `<ikey>` je váš instrumentační klíč).

## <a name="troubleshooting-cloud-services"></a>Poradce při potížích s cloudovými službami
Pro role v cloudových službách může být výchozí dočasná složka příliš malá pro uložení souborů minidump, což vede ke ztrátě snímků.
Potřebné místo závisí na celkové pracovní sady aplikace a počet souběžných snímků.
Pracovní sada 32bitové ASP.NET webové role je obvykle mezi 200 MB a 500 MB.
Povolit alespoň dva souběžné snímky.
Například pokud vaše aplikace používá 1 GB celkové pracovní sady, měli byste se ujistit, že je alespoň 2 GB místa na disku pro ukládání snímků.
Následujícím postupem nakonfigurujte roli cloudové služby pomocí vyhrazeného místního prostředku pro snímky.

1. Přidejte do cloudové služby nový místní prostředek úpravou souboru definice cloudové služby (.csdef). Následující příklad definuje prostředek `SnapshotStore` s názvem s velikostí 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Upravte spouštěcí kód role a přidejte proměnnou `SnapshotStore` prostředí, která odkazuje na místní prostředek. Pro role pracovního procesu by měl `OnStart` být kód přidán do metody vaší role:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Pro webové role (ASP.NET) by měl být kód `Application_Start` přidán do metody webové aplikace:
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

3. Aktualizace souboru ApplicationInsights.config vaší role za účelem přepsání dočasného umístění složky používaného`SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Přepsání složky Stínová kopie

Při spuštění snímek kolektoru se pokusí najít složku na disku, který je vhodný pro spuštění procesu snímek uploader. Vybraná složka se označuje jako složka Stínová kopie.

Kolekce snímků zkontroluje několik známých umístění a ujistěte se, že má oprávnění ke kopírování binárních souborů nahraný snímek. Používají se následující proměnné prostředí:
- Fabric_Folder_App_Temp
- MÍSTNÍ APPDATA
- Appdata
- Temp

Pokud nelze najít vhodnou složku, kolektor snímků hlásí chybu s _nápisem "Nelze najít vhodnou složku stínové kopie"_

Pokud se kopie nezdaří, `ShadowCopyFailed` kolektor snímků hlásí chybu.

Pokud nahrál e-mail nelze spustit, `UploaderCannotStartFromShadowCopy` sběratel snímků hlásí chybu. Text zprávy často obsahuje `System.UnauthorizedAccessException`. K této chybě obvykle dochází, protože aplikace je spuštěna pod účtem se sníženými oprávněními. Účet má oprávnění k zápisu do složky stínové kopie, ale nemá oprávnění ke spuštění kódu.

Vzhledem k tomu, že k těmto chybám `ExceptionDuringConnect` obvykle dochází při spuštění, obvykle je bude následovat chyba s nápisem _"Uploader se nepodařilo spustit"._

Chcete-li tyto chyby obejít, můžete složku `ShadowCopyFolder` stínové kopie zadat ručně pomocí možnosti konfigurace. Například pomocí ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Nebo pokud používáte appsettings.json s aplikací .NET Core:

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Použití hledání Přehledů aplikací k vyhledání výjimek se snímky

Při vytvoření snímku je výjimka vyvolání označena ID snímku. Toto ID snímku je zahrnuta jako vlastní vlastnost, když telemetrie výjimky je hlášena application insights. Pomocí **hledání** v Application Insights, můžete najít `ai.snapshot.id` všechny telemetrie s vlastní vlastnost.

1. Přejděte k prostředku Application Insights na webu Azure Portal.
2. Klikněte na **Vyhledat**.
3. Zadejte `ai.snapshot.id` textové pole Hledat a stiskněte Enter.

![Hledání telemetrie s ID snímku na portálu](./media/snapshot-debugger/search-snapshot-portal.png)

Pokud toto hledání vrátí žádné výsledky, pak žádné snímky byly hlášeny Application Insights pro vaši aplikaci ve vybraném časovém rozsahu.

Chcete-li vyhledat konkrétní ID snímku z protokolů uploaderu, zadejte toto ID do pole Hledat. Pokud nemůžete najít telemetrii pro snímek, o kterém víte, že byl nahrán, postupujte takto:

1. Zkontrolujte, zda se díváte na správný prostředek Application Insights ověřením klíče instrumentace.

2. Pomocí časového razítka z protokolu Uploader upravte filtr časového rozsahu hledání tak, aby pokrýval tento časový rozsah.

Pokud stále nevidíte výjimku s tímto ID snímku, pak telemetrie výjimky nebyla hlášena application insights. Tato situace může dojít, pokud vaše aplikace havaroval po pořízení snímku, ale před ohlášením telemetrie výjimky. V takovém případě zkontrolujte protokoly `Diagnose and solve problems` služby App Service pod a zjistěte, zda došlo k neočekávané restartování nebo neošetřené výjimky.

## <a name="edit-network-proxy-or-firewall-rules"></a>Úprava pravidel síťového serveru proxy nebo brány firewall

Pokud se vaše aplikace připojuje k Internetu prostřednictvím serveru proxy nebo brány firewall, bude pravděpodobně nutné upravit pravidla, která aplikaci umožní komunikovat se službou Ladicí program snímků. IP adresy používané ladicí program snímek jsou zahrnuty v značku služby Azure Monitor.
