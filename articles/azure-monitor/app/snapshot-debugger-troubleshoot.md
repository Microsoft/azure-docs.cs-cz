---
title: Řešení potíží s Azure Application Insights Snapshot Debugger
description: Tento článek představuje postup řešení potíží a informace, které vývojářům pomůžou při povolování a používání Application Insights Snapshot Debugger.
ms.topic: conceptual
author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: bb2ac221169cea84205d087cbe0aadfd035d22db
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760508"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Řešení potíží s povolením Application Insights Snapshot Debugger nebo zobrazením snímků
Pokud jste u své aplikace povolili Application Insights Snapshot Debugger, ale nevidíte snímky pro výjimky, můžete tyto pokyny použít k řešení potíží. Snímky se nemusí generovat z několika různých důvodů. Můžete spustit kontrolu stavu snímku a identifikovat některé z možných běžných příčin.

## <a name="use-the-snapshot-health-check"></a>Použít kontrolu stavu snímku
Výsledkem některých běžných problémů je, že se nezobrazují snímky otevření ladicího programu. Použití zastaralých Snapshot Collector například; dosáhnete denního limitu nahrávání; nebo možná budete muset snímek jenom prodloužit. K odstraňování běžných problémů použijte kontrolu stavu snímku.

Existuje odkaz v podokně výjimky v rámci kompletního zobrazení trasování, které vás přesměruje na kontrolu stavu snímku.

![Zadat kontrolu stavu snímku](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interaktivní rozhraní podobné chatu hledá běžné problémy a provede vás při jejich opravě.

![Kontroly stavu](./media/snapshot-debugger/healthcheck.png)

Pokud se tím problém nevyřeší, přečtěte si následující postup ručního řešení potíží.

## <a name="verify-the-instrumentation-key"></a>Ověření klíče instrumentace

Ujistěte se, že ve vaší publikované aplikaci používáte správný klíč instrumentace. Klíč instrumentace se obvykle čte z ApplicationInsights.config souboru. Ověřte, že hodnota je stejná jako klíč instrumentace pro prostředek Application Insights, který vidíte na portálu.

## <a name="check-ssl-client-settings-aspnet"></a><a id="SSL"></a>Kontrolovat nastavení klienta SSL (ASP.NET)

Pokud máte ASP.NET aplikaci hostovanou v Azure App Service nebo ve službě IIS na virtuálním počítači, může se vaší aplikaci nepodaří připojit se ke službě Snapshot Debugger z důvodu chybějícího protokolu zabezpečení SSL.
[Koncový bod Snapshot Debugger vyžaduje TLS verze 1,2](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). Sada protokolů zabezpečení SSL je jedním z adaptivní povolených hodnotou targetFramework targetFramework v části System. Web web.config. Pokud je pole targetFramework targetFramework 4.5.2 nebo nižší, pak se ve výchozím nastavení nezahrne TLS 1,2.

> [!NOTE]
> Hodnota targetFramework aplikace httpRuntime je nezávislá na cílové architektuře používané při sestavování aplikace.

Chcete-li zjistit nastavení, otevřete soubor web.config a vyhledejte část System. Web. Ujistěte se, že je `targetFramework` `httpRuntime` nastavená na 4,6 nebo vyšší.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> Změna hodnoty aplikace httpRuntime targetFramework změní adaptivní modulu runtime, který se aplikuje na vaši aplikaci, a může způsobit další, jemné změny chování. Po provedení této změny nezapomeňte aplikaci důkladně otestovat. Úplný seznam změn kompatibility najdete v tématu. https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> Pokud má targetFramework hodnotu 4,7 nebo vyšší, určí systém Windows dostupné protokoly. V Azure App Service je k dispozici protokol TLS 1,2. Pokud ale používáte vlastní virtuální počítač, možná budete muset v operačním systému povolit TLS 1,2.

## <a name="preview-versions-of-net-core"></a>Verze Preview rozhraní .NET Core
Pokud aplikace používá verzi Preview rozhraní .NET Core a Snapshot Debugger byla povolena prostřednictvím [podokna Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) na portálu, Snapshot Debugger pravděpodobně nebude možné spustit. Postupujte podle pokynů v části [povolit Snapshot debugger pro další prostředí](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , abyste před povolením prostřednictvím [podokna Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)zahrnuli ***také*** balíček NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) .


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade na nejnovější verzi balíčku NuGet

Pokud byla Snapshot Debugger povolena prostřednictvím [podokna Application Insights na portálu](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), měla by již vaše aplikace používat nejnovější balíček NuGet. Pokud byla povolená Snapshot Debugger zahrnutím balíčku NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , použijte Správce balíčků NuGet sady Visual Studio a ujistěte se, že používáte nejnovější verzi Microsoft. ApplicationInsights. SnapshotCollector.

## <a name="check-the-uploader-logs"></a>Podívejte se na protokoly odeslání.

Po vytvoření snímku se na disku vytvoří soubor s minimálním výpisem (. dmp). Samostatný proces odeslání vytvoří soubor s minimálním výpisem a nahraje ho společně s případnými přidruženými soubory PDB pro Application Insights úložiště Snapshot Debugger. Po úspěšném nahrání s minimálním výpisem se z disku odstraní. Soubory protokolu procesu odeslání jsou uchovávány na disku. V prostředí App Service můžete tyto protokoly najít v `D:\Home\LogFiles` . K vyhledání těchto souborů protokolu použijte web pro správu Kudu pro App Service.

1. Otevřete aplikaci App Service v Azure Portal.
2. Klikněte na **Rozšířené nástroje**nebo vyhledejte **Kudu**.
3. Klikněte na **Přejít**.
4. V rozevíracím seznamu **Konzola ladění** vyberte **cmd**.
5. Klikněte na **soubory protokolu**.

Měl by se zobrazit aspoň jeden soubor s názvem, který začíná `Uploader_` nebo `SnapshotUploader_` a `.log` příponou. Kliknutím na příslušnou ikonu stáhnete všechny soubory protokolu nebo je otevřete v prohlížeči.
Název souboru obsahuje jedinečnou příponu, která identifikuje instanci App Service. Pokud je vaše instance App Service hostovaná na více než jednom počítači, pro každý počítač jsou k dispozici samostatné soubory protokolů. Když odeslání zjistí nový soubor s minimálním výpisem, je zaznamenán v souboru protokolu. Tady je příklad úspěšného snímku a nahrání:

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
> Výše uvedený příklad je z verze 1.2.0 balíčku NuGet Microsoft. ApplicationInsights. SnapshotCollector. V dřívějších verzích se volá proces odeslání `MinidumpUploader.exe` a protokol je méně podrobný.

V předchozím příkladu je klíč instrumentace `c12a605e73c44346a984e00000000000` . Tato hodnota by měla odpovídat klíči instrumentace vaší aplikace.
S minimálním výpisem je přidružen ke snímku s ID `139e411a23934dc0b9ea08a626db16c5` . Toto ID můžete později použít k vyhledání přidružené telemetrie výjimky v Application Insights Analytics.

Odeslání nových soubory PDB vyhledá nové každých 15 minut. Tady je příklad:

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

V případě aplikací, které _nejsou_ hostované v App Service, jsou protokoly odeslání ve stejné složce jako mini výpisy: `%TEMP%\Dumps\<ikey>` (kde `<ikey>` je váš klíč instrumentace).

## <a name="troubleshooting-cloud-services"></a>Řešení potíží s Cloud Services
Pro role v Cloud Services může být výchozí dočasná složka příliš malá, aby mohla uchovávat soubory s minimálním výpisem, což vede ke ztrátě snímků.
Požadované místo závisí na celkové pracovní sadě vaší aplikace a na počtu souběžných snímků.
Pracovní sada ASP.NET webové role 32 je obvykle mezi 200 MB a 500 MB.
Povolte aspoň dva souběžné snímky.
Pokud například vaše aplikace používá 1 GB z celkové pracovní sady, měli byste se ujistit, že je k ukládání snímků k dispozici alespoň 2 GB místa na disku.
Pomocí těchto kroků můžete nakonfigurovat roli cloudové služby s vyhrazeným místním prostředkem pro snímky.

1. Úpravou souboru definice cloudové služby (. csdef) přidejte do cloudové služby nový místní prostředek. V následujícím příkladu je definován prostředek `SnapshotStore` s názvem o velikosti 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Úpravou spouštěcího kódu role přidejte proměnnou prostředí, která odkazuje na `SnapshotStore` místní prostředek. Pro role pracovního procesu by se kód měl přidat do metody vaší role `OnStart` :
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   V případě webových rolí (ASP.NET) by se měl kód přidat do metody vaší webové aplikace `Application_Start` :
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

3. Aktualizujte soubor ApplicationInsights.config role pro přepsání umístění dočasné složky používané nástrojem `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Přepsání složky stínové kopie

Po spuštění Snapshot Collector se pokusí najít složku na disku, která je vhodná pro spuštění procesu odeslání snímku. Vybraná složka je známá jako složka stínové kopie.

Snapshot Collector kontroluje několik známých umístění a zajišťuje tak, že má oprávnění ke kopírování binárních souborů nástroje pro odeslání snímků. Používají se následující proměnné prostředí:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APLIKACÍ
- NÁZVEM

Pokud se vhodná složka nenajde, Snapshot Collector nahlásí chybu oznamující, že _se nepovedlo najít vhodnou složku stínové kopie._

Pokud se kopie nezdařila, Snapshot Collector hlásí `ShadowCopyFailed` chybu.

Pokud nelze spustit odeslání, Snapshot Collector hlásí `UploaderCannotStartFromShadowCopy` chybu. Text zprávy obsahuje často `System.UnauthorizedAccessException` . K této chybě obvykle dochází, protože aplikace běží pod účtem s omezenými oprávněními. Účet má oprávnění k zápisu do složky stínové kopie, ale nemá oprávnění ke spouštění kódu.

Vzhledem k tomu, že k těmto chybám obvykle dochází při spuštění, obvykle se jedná o `ExceptionDuringConnect` chybu oznamující, že _se nepovedlo spustit odeslání._

Pokud chcete tyto chyby vyřešit, můžete ručně zadat složku stínové kopie pomocí `ShadowCopyFolder` Možnosti konfigurace. Například pomocí ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Nebo, pokud používáte appsettings.jsv s aplikací .NET Core:

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

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Použití hledání Application Insights k nalezení výjimek s snímky

Při vytvoření snímku je tato výjimka označena ID snímku. Toto ID snímku je zahrnuté jako vlastní vlastnost, když je v telemetrie výjimky hlášena Application Insights. Pomocí **vyhledávání** v Application Insights můžete najít veškerou telemetrii s `ai.snapshot.id` vlastní vlastností.

1. V Azure Portal přejděte k prostředku Application Insights.
2. Klikněte na **Vyhledat**.
3. `ai.snapshot.id`Do textového pole hledání zadejte a stiskněte klávesu ENTER.

![Hledání telemetrie s ID snímku na portálu](./media/snapshot-debugger/search-snapshot-portal.png)

Pokud toto hledání nevrátí žádné výsledky, nenahlásily se pro vaši aplikaci Application Insights žádné snímky ve vybraném časovém rozsahu.

Pokud chcete v protokolech odeslání vyhledat konkrétní ID snímku, zadejte toto ID do vyhledávacího pole. Pokud nemůžete najít telemetrii pro snímek, který víte, že jste nahráli, postupujte takto:

1. Ověřte, že se díváte na správný Application Insights prostředek tím, že ověříte klíč instrumentace.

2. Pomocí časového razítka z protokolu odeslání upravte filtr časového rozsahu hledání tak, aby pokryl tento časový rozsah.

Pokud stále nevidíte výjimku s tímto ID snímku, telemetrie výjimek nebyla nahlášena Application Insights. K této situaci může dojít, pokud dojde k chybě vaší aplikace po pořízení snímku, ale před tím, než nahlásila telemetrii výjimek. V takovém případě zkontrolujte protokoly App Service v části `Diagnose and solve problems` a zjistěte, jestli nedošlo k neočekávanému restartování nebo neošetřeným výjimkám.

## <a name="edit-network-proxy-or-firewall-rules"></a>Upravit síťová proxy nebo pravidla brány firewall

Pokud se vaše aplikace připojuje k Internetu prostřednictvím proxy serveru nebo brány firewall, možná budete muset upravit pravidla, aby aplikace mohla komunikovat se službou Snapshot Debugger. IP adresy, které používá Snapshot Debugger, jsou součástí značky služby Azure Monitor.
