---
title: Řešení potíží s Azure Application Insights Snapshot Debugger
description: Tento článek představuje postup řešení potíží a informace, které vývojářům pomůžou povolit a používat Application Insights Snapshot Debugger.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a285f26a406caa88d91da5647b3b79cffc9b614f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217410"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Řešení potíží s povolením Application Insights Snapshot Debugger nebo zobrazením snímků
Pokud jste u své aplikace povolili Application Insights Snapshot Debugger, ale nevidíte snímky pro výjimky, můžete tyto pokyny použít k řešení potíží.

Může existovat mnoho různých důvodů, proč se snímky negenerují. Můžete začít spuštěním kontroly stavu snímku a identifikovat některé z možných běžných příčin.

## <a name="make-sure-youre-using-the-appropriate-snapshot-debugger-endpoint"></a>Ujistěte se, že používáte příslušný koncový bod Snapshot Debugger

V současné době jsou k [disAzure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) a [Azure Čína](https://docs.microsoft.com/azure/china/resources-developer-guide)jedinými oblastmi, které vyžadují úpravy koncových bodů.

Pro App Service a aplikace pomocí sady Application Insights SDK musíte aktualizovat připojovací řetězec pomocí podporovaných přepsání pro Snapshot Debugger, jak je definováno níže:

|Vlastnost připojovacího řetězce    | Cloud pro státní správu USA | Čína – Cloud |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Další informace o dalších přepsáních připojení najdete v tématu [Application Insights dokumentaci](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net#connection-string-with-explicit-endpoint-overrides).

V případě Function App je třeba aktualizovat `host.json` pomocí podporovaných přepsání níže:

|Vlastnost    | Cloud pro státní správu USA | Čína – Cloud |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Níže je uveden příklad `host.json` aktualizace s koncovým bodem cloudového agenta státní správy USA:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

## <a name="use-the-snapshot-health-check"></a>Použít kontrolu stavu snímku
Výsledkem některých běžných problémů je, že se nezobrazují snímky otevření ladicího programu. Použití zastaralých Snapshot Collector například; dosáhnete denního limitu nahrávání; nebo možná budete muset snímek jenom prodloužit. K odstraňování běžných problémů použijte kontrolu stavu snímku.

Existuje odkaz v podokně výjimky v rámci kompletního zobrazení trasování, které vás přesměruje na kontrolu stavu snímku.

![Zadat kontrolu stavu snímku](./media/snapshot-debugger/enter-snapshot-health-check.png)

Interaktivní rozhraní podobné chatu hledá běžné problémy a provede vás při jejich opravě.

![Kontroly stavu](./media/snapshot-debugger/healthcheck.png)

Pokud se tím problém nevyřeší, přečtěte si následující postup ručního řešení potíží.

## <a name="verify-the-instrumentation-key"></a>Ověření klíče instrumentace

Ujistěte se, že ve vaší publikované aplikaci používáte správný klíč instrumentace. Klíč instrumentace se obvykle čte z ApplicationInsights.config souboru. Ověřte, že hodnota je stejná jako klíč instrumentace pro prostředek Application Insights, který vidíte na portálu.

## <a name="check-tlsssl-client-settings-aspnet"></a><a id="SSL"></a>Ověřte nastavení klienta TLS/SSL (ASP.NET)

Pokud máte aplikaci ASP.NET, která je hostovaná v Azure App Service nebo ve službě IIS na virtuálním počítači, může se vaší aplikaci nepodaří připojit se ke službě Snapshot Debugger kvůli chybějícímu protokolu zabezpečení SSL.

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
Pokud používáte verzi Preview rozhraní .NET Core nebo pokud vaše aplikace odkazuje na Application Insights SDK přímo nebo nepřímo prostřednictvím závislého sestavení, postupujte podle pokynů pro [povolení Snapshot debugger pro ostatní prostředí](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json).

## <a name="check-the-diagnostic-services-site-extension-status-page"></a>Zkontroluje stavovou stránku rozšíření webu diagnostické služby.
Pokud byla Snapshot Debugger povolena prostřednictvím [podokna Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) na portálu, bylo povoleno rozšířením webu diagnostické služby.

> [!NOTE]
> Bezkódová instalace Application Insights Snapshot Debugger se řídí zásadami podpory .NET Core.
> Další informace o podporovaných modulech runtime najdete v tématu [zásady podpory .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Stavovou stránku tohoto rozšíření můžete zaškrtnout na následující adrese URL: `https://{site-name}.scm.azurewebsites.net/DiagnosticServices`

> [!NOTE]
> Doména odkazu na stavové stránce se bude lišit v závislosti na cloudu.
Tato doména bude stejná jako Kudu pro správu serveru pro App Service.

Tato stránka stavu zobrazuje stav instalace agentů profileru a Snapshot Collector. V případě, že došlo k neočekávané chybě, zobrazí se a ukáže, jak je opravit.

K získání základní adresy URL této stránky stavu můžete použít web pro správu Kudu pro App Service:
1. Otevřete aplikaci App Service v Azure Portal.
2. Vyberte **Rozšířené nástroje** nebo vyhledejte **Kudu**.
3. Vyberte **Přejít**.
4. Až budete na webu pro správu Kudu, přidejte v adrese URL **následující příkaz `/DiagnosticServices` a stiskněte klávesu ENTER**.
 Bude končit tímto způsobem: `https://<kudu-url>/DiagnosticServices`

Zobrazí se stránka stavu podobná následující: ![ stavová stránka diagnostické služby](./media/diagnostic-services-site-extension/status-page.png)

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgrade na nejnovější verzi balíčku NuGet
Na základě toho, jak byla povolena Snapshot Debugger, si přečtěte následující možnosti:

* Pokud byla Snapshot Debugger povolena prostřednictvím [podokna Application Insights na portálu](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), měla by již vaše aplikace používat nejnovější balíček NuGet.

* Pokud byla povolená Snapshot Debugger zahrnutím balíčku NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , použijte Správce balíčků NuGet sady Visual Studio a ujistěte se, že používáte nejnovější verzi Microsoft. ApplicationInsights. SnapshotCollector.

Nejnovější aktualizace a opravy chyb [najdete v poznámkách k verzi](./snapshot-collector-release-notes.md).

## <a name="check-the-uploader-logs"></a>Podívejte se na protokoly odeslání.

Po vytvoření snímku se na disku vytvoří soubor s minimálním výpisem (. dmp). Samostatný proces odeslání vytvoří soubor s minimálním výpisem a nahraje ho společně s případnými přidruženými soubory PDB pro Application Insights úložiště Snapshot Debugger. Po úspěšném nahrání s minimálním výpisem se z disku odstraní. Soubory protokolu procesu odeslání jsou uchovávány na disku. V prostředí App Service můžete tyto protokoly najít v `D:\Home\LogFiles` . K vyhledání těchto souborů protokolu použijte web pro správu Kudu pro App Service.

1. Otevřete aplikaci App Service v Azure Portal.
2. Vyberte **Rozšířené nástroje** nebo vyhledejte **Kudu**.
3. Vyberte **Přejít**.
4. V rozevíracím seznamu **Konzola ladění** vyberte **cmd**.
5. Vyberte **soubory protokolu**.

Měl by se zobrazit aspoň jeden soubor s názvem, který začíná `Uploader_` nebo `SnapshotUploader_` a `.log` příponou. Vyberte příslušnou ikonu ke stažení všech souborů protokolu nebo jejich otevření v prohlížeči.
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
S minimálním výpisem je přidružen ke snímku s ID `139e411a23934dc0b9ea08a626db16c5` . Toto ID můžete později použít k vyhledání přidruženého záznamu výjimky v Application Insights Analytics.

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
V Cloud Services může být výchozí dočasná složka příliš malá pro ukládání souborů s minimálním výpisem, což vede ke ztrátě snímků.

Požadované místo závisí na celkové pracovní sadě vaší aplikace a na počtu souběžných snímků.

Pracovní sada ASP.NET webové role 32 je obvykle mezi 200 MB a 500 MB. Povolte aspoň dva souběžné snímky.

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

Při vytvoření snímku je tato výjimka označena ID snímku. Toto ID snímku je zahrnuté jako vlastní vlastnost, pokud je výjimka hlášena Application Insights. Pomocí **vyhledávání** v Application Insights můžete najít všechny záznamy s `ai.snapshot.id` vlastní vlastností.

1. V Azure Portal přejděte k prostředku Application Insights.
2. Vyberte **Hledat**.
3. `ai.snapshot.id`Do textového pole hledání zadejte a stiskněte klávesu ENTER.

![Hledání telemetrie s ID snímku na portálu](./media/snapshot-debugger/search-snapshot-portal.png)

Pokud toto hledání nevrátí žádné výsledky, nenahlásily se žádné snímky Application Insights ve vybraném časovém rozsahu.

Pokud chcete v protokolech odeslání vyhledat konkrétní ID snímku, zadejte toto ID do vyhledávacího pole. Pokud nemůžete najít záznamy pro snímek, který víte, že jste nahráli, postupujte takto:

1. Ověřte, že se díváte na správný Application Insights prostředek tím, že ověříte klíč instrumentace.

2. Pomocí časového razítka z protokolu odeslání upravte filtr časového rozsahu hledání tak, aby pokryl tento časový rozsah.

Pokud stále nevidíte výjimku s tímto ID snímku, pak se záznam výjimky nenahlásil Application Insights. K této situaci může dojít, pokud dojde k chybě vaší aplikace po pořízení snímku, ale předtím, než nahlásila záznam výjimky. V takovém případě zkontrolujte protokoly App Service v části `Diagnose and solve problems` a zjistěte, jestli nedošlo k neočekávanému restartování nebo neošetřeným výjimkám.

## <a name="edit-network-proxy-or-firewall-rules"></a>Upravit síťová proxy nebo pravidla brány firewall

Pokud se vaše aplikace připojuje k Internetu prostřednictvím proxy serveru nebo brány firewall, možná budete muset aktualizovat pravidla pro komunikaci se službou Snapshot Debugger.

IP adresy, které používá Application Insights Snapshot Debugger, jsou součástí značky služby Azure Monitor. Další informace najdete v [dokumentaci k značkám služby](../../virtual-network/service-tags-overview.md).