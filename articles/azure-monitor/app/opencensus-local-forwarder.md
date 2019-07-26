---
title: Azure Application Insights OpenCensus distribuované trasování pro místní službu pro distribuci (Preview) | Dokumentace Microsoftu
description: Předejte OpenCensus distribuované trasování a rozsahy z jazyků, jako je Python, a přejít na Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: aa64755b636005f4ed8ea5c074ffaada51fb8dd9
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348148"
---
# <a name="local-forwarder-preview"></a>Místní server pro přeposílání (Preview)

Místní předávací server je agent, který shromažďuje Application Insights nebo [OpenCensus](https://opencensus.io/) telemetrie z nejrůznějších sad SDK a směruje je Application Insights. Dokáže běžet v systému Windows a Linux. Je možné, že ho budete moct spustit pod macOS, ale v tuto chvíli není oficiálně podporovaný.

## <a name="running-local-forwarder"></a>Spuštění místního serveru pro přeposílání

Místní server pro přeposílání je [otevřený zdrojový projekt na GitHubu](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). K dispozici je celá řada způsobů, jak spustit místní server pro přesměrování na různých platformách.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Služba systému Windows

Nejjednodušší způsob, jak spustit místní službu pro překládání v systému Windows, je její instalace jako služba systému Windows. Verze se dodává se spustitelným souborem služby systému Windows (*WindowsServiceHost/Microsoft. LocalForwarder. WindowsServiceHost. exe*), který lze snadno zaregistrovat s operačním systémem.

> [!NOTE]
> Místní služba pro přeposílání vyžaduje minimálně .NET Framework 4,7. Pokud nemáte .NET Framework 4,7, služba se nainstaluje, ale nespustí se. Pro přístup k nejnovější verzi rozhraní .NET Framework **[najdete na stránce pro stažení rozhraní .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Stáhněte si LF. Soubor WindowsServiceHost. zip ze [stránky pro vydání místního serveru](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) pro posílání na GitHubu.

    ![Snímek obrazovky se stránkou pro stažení místního serveru pro dopředné verze](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. V tomto příkladu je pro usnadnění ukázky pouze extrahování souboru zip do cesty `C:\LF-WindowsServiceHost`.

    Pokud chcete službu zaregistrovat a nakonfigurovat tak, aby se spouštěla při spuštění systému, spusťte na příkazovém řádku následující příkaz jako správce:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Měli byste obdržet odpověď na:
    
    `[SC] CreateService SUCCESS`
    
    Prohlédnutí nové služby prostřednictvím typu grafického uživatelského rozhraní služeb``services.msc``
        
     ![Snímek obrazovky místní služby pro přeposílání](./media/opencensus-local-forwarder/002-services.png)

3. Klikněte **pravým tlačítkem na** nový místní server pro směrování a vyberte **Spustit**. Vaše služba teď přejde do běžícího stavu.

4. Ve výchozím nastavení je služba vytvořena bez jakýchkoli akcí obnovení. Můžete **kliknout pravým tlačítkem** **a vybrat možnost** > **obnovit** nastavení a nakonfigurovat automatické odezvy na selhání služby.

    Pokud ale chcete nastavit automatické možnosti obnovení pro případ, že dojde k chybám, můžete použít:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. Ve stejném umístění ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` , ve kterém je ``C:\LF-WindowsServiceHost`` soubor, který v tomto příkladu je soubor s názvem ``LocalForwarder.config``. Jedná se o soubor založený na jazyce XML, který umožňuje upravit konfiguraci localforwader a zadat klíč instrumentace prostředku Application Insights, pro který chcete překládat data distribuovaného trasování. 

    Po úpravě ``LocalForwarder.config`` souboru pro přidání klíče instrumentace nezapomeňte restartovat **místní službu pro** dodávání změn, aby se provedené změny projevily.
    
6. Pokud chcete potvrdit, že jsou nastavená vaše požadovaná nastavení a že místní předávací server naslouchá datům trasování podle očekávání, zkontrolujte ``LocalForwarder.log`` soubor. V dolní části souboru by se měly zobrazit podobné výsledky jako na obrázku:

    ![Snímek obrazovky souboru LocalForwarder. log](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Konzolová aplikace

V některých případech použití může být užitečné spustit místní server pro přeposílání jako konzolovou aplikaci. Verze je dodávána s následujícími spustitelnými verzemi hostitele konzoly:
* binární */CONSOLEHOST/PUBLISH/Microsoft.LOCALFORWARDER.CONSOLEHOST.dll*.NET Core závislá na rozhraní. Spuštění tohoto binárního souboru vyžaduje, aby bylo možné nainstalovat modul runtime .NET Core. Podrobnosti najdete na této [stránce](https://www.microsoft.com/net/download/dotnet-core/2.1) ke stažení.
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* samostatná sada binárních souborů .NET Core pro platformy x86 a x64. Ty nevyžadují, aby se spouštěl modul runtime .NET Core. */ConsoleHost/Win-x86/Publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/Win-x64/Publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Stejně jako ve Windows se verze dodává s následujícími spustitelnými verzemi hostitele konzoly:
* binární */CONSOLEHOST/PUBLISH/Microsoft.LOCALFORWARDER.CONSOLEHOST.dll*.NET Core závislá na rozhraní. Spuštění tohoto binárního souboru vyžaduje, aby bylo možné nainstalovat modul runtime .NET Core. Podrobnosti najdete na této [stránce](https://www.microsoft.com/net/download/dotnet-core/2.1) ke stažení.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* samostatná sada binárních souborů .NET Core pro Linux-64. Tento postup nevyžaduje spuštění modulu .NET Core Runtime. */ConsoleHost/Linux-x64/Publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Mnoho uživatelů se systémem Linux bude chtít spustit místní server pro přesměrování jako démona. Systémy Linux jsou dodávány s nejrůznějšími řešeními pro správu služeb, jako je například spuštění, SYSV nebo systém. Bez ohledu na vaši konkrétní verzi ji můžete použít ke spuštění místního serveru pro směrování způsobem, který je nejvhodnější pro váš scénář.

Jako příklad vytvoříte službu démon pomocí systému. Použijeme verzi závislou na rozhraní, ale totéž můžete udělat i pro sebe, která obsahuje.

* Vytvořte následující soubor služby s názvem *localforwarder. Service* a umístěte ho do */lib/systemd/System*.
Tato ukázka předpokládá, že vaše uživatelské jméno je SAMPLE_USER a že jste zkopírovali binární soubory závislé na rozhraní pro směrování (z */ConsoleHost/Publish*) do */Home/SAMPLE_USER/LOCALFORWARDER_DIR*.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Spusťte následující příkaz, který systému dá pokyn, aby při každém spuštění spouštěl místní server pro zakládání.

```
systemctl enable localforwarder
```

* Spusťte následující příkaz, který systému dá pokyn k okamžitému spuštění místního serveru pro přeposílání.

```
systemctl start localforwarder
```

* Sledujte službu pomocí kontroly souborů * *. log* v adresáři/Home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Místní služba pro přeposílání může pracovat s macOS, ale aktuálně není oficiálně podporovaná.

### <a name="self-hosting"></a>Samoobslužné hostování
Místní server pro odesílání je také distribuován jako .NET Standard balíček NuGet, což umožňuje jeho hostování v rámci vlastní aplikace .NET.

```csharp
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Konfigurace místního serveru pro směrování

* Pokud provozujete jednoho z vlastních hostitelů pro místní přeposílání (hostitele konzoly nebo hostitele služby Windows), najdete soubor **LocalForwarder. config** , který se nachází vedle binárního souboru.
* Při samoobslužném hostování rozhraní NuGet pro místní předávací službu musí být v kódu uvedena konfigurace stejného formátu (viz oddíl na samoobslužném hostování). V části Syntaxe konfigurace se podívejte na soubor [LocalForwarder. config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) v úložišti GitHub. 

> [!NOTE]
> Konfigurace se může změnit z verze na verzi, takže věnujte pozornost verzi, kterou používáte.

## <a name="monitoring-local-forwarder"></a>Monitorování místního předávacího serveru

Trasování se zapisují do systému souborů vedle spustitelného souboru, který používá místní server pro překládání (hledání souborů * *. log* ). Můžete umístit soubor s názvem *nLOG. config* vedle spustitelného souboru a poskytnout tak vlastní konfiguraci místo výchozí. Popis formátu naleznete v [dokumentaci](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) .

Pokud není zadaný žádný konfigurační soubor (což je výchozí nastavení), místní server pro překládání použije výchozí konfiguraci, kterou najdete [tady](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Další postup

* [Otevřít sčítání](https://opencensus.io/)
