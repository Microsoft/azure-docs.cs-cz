---
title: Azure Application Insights OpenCensus distribuované trasování místní předávání | Dokumentace Microsoftu
description: Dopředné OpenCensus distribuované trasování a časem ukončení jazyky, jako je Python a přejděte do služby Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: ddfcb90090d82d8fe947292737163a81c715b32d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972823"
---
# <a name="local-forwarder"></a>Místní předávání

Místní služba předávání je agenta, který shromažďuje Application Insights nebo [OpenCensus](https://opencensus.io/) telemetrická data z nejrůznějších sad SDK a směruje je do služby Application Insights. Je schopný běžet pod Windows a Linux. Je také možné spouštět v systému macOS, ale není oficiálně podporován v tuto chvíli.

## <a name="running-local-forwarder"></a>Spuštění místní služby předávání

Místní služba předávání je [projekt open source na Githubu](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Existuje řada různých způsobů, jak spustit místní server pro předávání na více platformách.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>Služba systému Windows

Po instalaci se jako služba Windows je nejjednodušší způsob spouštění místní server pro předávání v části Windows. Spustitelný soubor Windows služby je součástí vydání (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) která lze snadno dokument zaregistrovat u operačního systému.

> [!NOTE]
> Služba předávání místní vyžaduje minimálně rozhraní .NET Framework 4.7. Pokud nemáte rozhraní .NET Framework 4.7 služby bude instalace, ale nespustí. Pro přístup k nejnovější verzi rozhraní .NET Framework ** [najdete na stránce pro stažení rozhraní .NET Framework](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)**.

1. Stáhněte si LF. Soubor WindowsServiceHost.zip z [stránce verzí místní předávání](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) na Githubu.

    ![Snímek obrazovky stránky pro stažení verze místní server pro předávání](.\media\opencensus-local-forwarder\001-local-forwarder-windows-service-host-zip.png)

2. V tomto příkladu pro snadné ukázku jsme právě extrahuje soubor ZIP do cesty `C:\LF-WindowsServiceHost`.

    Registrovat službu a nakonfigurovat tak, aby při spuštění systému počítače spusťte následující příkaz z příkazového řádku jako správce:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Mělo by se zobrazit odpověď:
    
    `[SC] CreateService SUCCESS`
    
    Prozkoumat nové služby pomocí grafického uživatelského rozhraní služby typu ``services.msc``
        
     ![Snímek obrazovky služby předávání místní](.\media\opencensus-local-forwarder\002-services.png)

3. **Klikněte pravým tlačítkem na** nové místní server pro předávání a vyberte **Start**. Vaše služba nyní zadáte do spuštěného stavu.

4. Ve výchozím nastavení je služba vytvořena bez jakékoli akce obnovení. Je možné **klikněte pravým tlačítkem na** a vyberte **vlastnosti** > **obnovení** nakonfigurovat automatické odpovědi na chybu služby.

    Nebo pokud chcete nastavit možnosti automatického obnovení prostřednictvím kódu programu pro, pokud dojde k selhání, můžete použít:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. Ve stejném umístění jako váš ``Microsoft.LocalForwarder.WindowsServiceHost.exe`` soubor, který v tomto příkladu je ``C:\LF-WindowsServiceHost`` existuje soubor s názvem ``LocalForwarder.config``. Toto je soubor xml na základě, která vám umožní přizpůsobit konfiguraci vašeho localforwader a zadat Instrumentační klíč tohoto prostředku Application Insights má předávat data distribuované trasování. 

    Po dokončení úprav ``LocalForwarder.config`` soubor přidat Instrumentační klíč, nezapomeňte restartovat **místní služby předávání** umožňující provedené změny projevily.
    
6. Pokud chcete potvrdit, že požadovaná nastavení jsou na místě a, že místní služba předávání dat trasování jako očekávané kontrolu naslouchá ``LocalForwarder.log`` souboru. Měli byste vidět výsledky, podobně jako na obrázku níže v dolní části souboru:

    ![Snímek obrazovky LocalForwarder.log souboru](.\media\opencensus-local-forwarder\003-log-file.png)

#### <a name="console-application"></a>Konzolová aplikace

Pro určité případy použití, může být užitečné spustit místní server pro předávání jako konzolové aplikace. Verze obsahuje následující spustitelný soubor verze hostitele konzoly:
* binární soubor .NET Core závisí na architektuře */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Tento binární soubor spuštění vyžaduje .NET Core runtime, který má být nainstalována. Přečtěte si tento soubor ke stažení [stránky](https://www.microsoft.com/net/download/dotnet-core/2.1) podrobnosti.
```batchfile
E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```
* samostatná sada .NET Core, binární soubory pro x86 a x64 platformy. Ty nevyžadují .NET Core runtime pro spuštění. */ConsoleHost/Win-x86/Publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
```batchfile
E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
```

### <a name="linux"></a>Linux

Stejně jako u Windows, verze obsahuje následující spustitelný soubor verze hostitele konzoly:
* binární soubor .NET Core závisí na architektuře */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Tento binární soubor spuštění vyžaduje .NET Core runtime, který má být nainstalována. Přečtěte si tento soubor ke stažení [stránky](https://www.microsoft.com/net/download/dotnet-core/2.1) podrobnosti.

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* samostatná .NET Core je sada binární soubory pro linux-64. Ten nevyžaduje, aby modul runtime .NET Core pro spuštění. */ConsoleHost/Linux-x64/Publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Mnoho uživatelů Linuxu budou chtít spustit místní server pro předávání jako démon. V systémech Linux se dodává s širokou škálu řešení pro správu služeb, jako je Upstart, sysv nebo systemd. Všechno, co je konkrétní verzi, můžete ji spustit místní server pro předávání způsobem, který je pro váš scénář nejvhodnější.

Jako příklad vytvoříme pomocí systemd služby démona. Použijeme verze závisí na architektuře, ale stejné lze provést samostatná jeden také.

* Vytvořte následující soubor služby s názvem *localforwarder.service* a umístěte ho do */lib/systemd/system*.
Tento příklad předpokládá své uživatelské jméno je SAMPLE_USER a zkopírování binárních souborů závisí na architektuře místní server pro předávání (z */ConsoleHost/publikovat*) k */home/SAMPLE_USER/LOCALFORWARDER_DIR*.

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

* Spusťte následující příkaz, abyste instruovali systemd spustit místní server pro předávání v každém spuštění

```
systemctl enable localforwarder
```

* Spusťte následující příkaz, abyste instruovali systemd chcete okamžitě spustit místní server pro předávání

```
systemctl start localforwarder
```

* Monitorovat službu zkontrolováním **.log* soubory v adresáři /home/SAMPLE_USER/LOCALFORWARDER_DIR.

### <a name="mac"></a>Mac
Místní server pro předávání může fungovat v systému macOS, ale není aktuálně podporována oficiálně.

### <a name="self-hosting"></a>S vlastním hostováním
Místní server pro předávání se také distribuuje jako balíček NuGet pro rozhraní .NET Standard, díky tomu umožňuje hostovat vlastní aplikace .NET.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Konfigurace místní server pro předávání

* Když s některým z místní služby předávání vlastního hostitele (hostitele konzoly nebo hostitel služby Windows), zjistíte **LocalForwarder.config** umístěny vedle binárního souboru.
* Při hostování na vlastním místním předávání NuGet v kódu musí být zadaná konfigurace stejný formát (viz část o s vlastním hostováním). Konfigurace syntaxe, zkontrolujte, [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) v úložišti GitHub. 

> [!NOTE]
> Konfigurace může změnit z verzí, proto věnujte pozornost tomu, na kterou verzi používáte.

## <a name="monitoring-local-forwarder"></a>Monitorování místní server pro předávání

Trasování jsou zapsané do systému souborů vedle spustitelný soubor, na kterém běží server pro předávání místní (vyhledejte **.log* soubory). Můžete umístit soubor s názvem *NLog.config* vedle spustitelný soubor a zadejte vlastní konfiguraci místo výchozí hodnotu. Zobrazit [dokumentaci](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format) pro popis formátu.

Není-li žádný konfigurační soubor (což je výchozí hodnota), místní předávání použije výchozí konfigurace, který se nachází [tady](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config).

## <a name="next-steps"></a>Další postup

* [Otevřít sčítání](https://opencensus.io/)
