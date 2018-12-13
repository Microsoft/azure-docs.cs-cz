---
title: Osvědčené postupy a řešení potíží pro Node.js – Azure App Service
description: Přečtěte si osvědčené postupy a řešení problémů s kroky pro aplikace v Node.js ve službě Azure App Service.
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.custom: seodec18
ms.openlocfilehash: 5a8760bc67125f857998f23ca33733a62a0d8fb5
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315719"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Osvědčené postupy a Průvodce odstraňováním potíží pro aplikace v Node.js v Azure App Service Windows

V tomto článku se naučíte osvědčené postupy a kroky pro řešení potíží [aplikací node](app-service-web-get-started-nodejs.md) běžící na Azure Web Apps (s [modulu iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Buďte opatrní při použití kroků pro řešení potíží na svoje produkční lokality. Doporučení je k řešení potíží s vaší aplikací o neprodukční nastavení například přípravného slotu a pokud je problém vyřešen, Prohodit vaše přípravný slot s produkční slot.
>

## <a name="iisnode-configuration"></a>Konfigurace modulu IISNODE

To [soubor schématu](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) zobrazuje všechna nastavení, můžete nakonfigurovat pro modul iisnode. Některá nastavení, které jsou užitečné pro vaši aplikaci:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Toto nastavení určuje počet procesů uzlu, které jsou spouštěny na aplikaci služby IIS. Výchozí hodnota je 1. Můžete spustit tolik node.exes jako počet virtuálních procesorů vašich virtuálních počítačů můžete změnit hodnotu na 0. Doporučená hodnota je 0 pro většinu aplikací, abyste mohli používat všechny virtuálních procesorů v počítači. Node.exe je jednovláknový tak spotřebovává jeden node.exe maximálně 1 virtuální procesor. Získat maximální výkon mimo vaši aplikaci uzlu, budete chtít použít všechny virtuální procesory.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Toto nastavení určuje cestu k node.exe. Tuto hodnotu tak, aby odkazoval na vaší verzi node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Toto nastavení určuje maximální počet souběžných požadavků odesílaných modulu iisnode pro každý node.exe. Ve službě Azure Web Apps výchozí hodnota je nekonečno. Když není hostované ve službě Azure Web Apps, výchozí hodnota je 1024. Můžete nakonfigurovat hodnotu v závislosti na tom, kolik požadavků, že vaše aplikace obdrží a jak rychle bude vaše aplikace zpracuje každý požadavek.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Toto nastavení určuje maximální počet pokusů o opakování modulu iisnode vytváření připojení v pojmenovaném kanálu k odesílání požadavků na node.exe. Toto nastavení v kombinaci s namedPipeConnectionRetryDelay Určuje celkový časový limit každého požadavku v rámci modulu iisnode. Výchozí hodnota je 200 ve službě Azure Web Apps. Celkový časový limit v sekundách = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Toto nastavení určuje dobu (v ms) modulu iisnode čekat mezi opakováními odešlete žádost pro node.exe přes pojmenovaný kanál. Výchozí hodnota je 250 ms.
Celkový časový limit v sekundách = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000

Ve výchozím nastavení, celkový časový limit v modulu iisnode ve službě Azure Web Apps je 200 \* 250 ms = 50 sekund.

### <a name="logdirectory"></a>logDirectory

Toto nastavení určuje adresář, kde modul iisnode zaznamená stdout/stderr. Výchozí hodnota je modul iisnode, který je relativní vzhledem k adresáři hlavního skriptu (adresáře, kde je k dispozici hlavní server.js)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Toto nastavení určuje, jaké verze modulu iisnode nástroj node-inspector používá při ladění aplikace v Ruby. V současné době modulu iisnode. Inspektoru 0.7.3.dll a modulu iisnode inspector.dll jsou jenom dvě platné hodnoty pro toto nastavení. Výchozí hodnota je modulu iisnode. Inspektoru 0.7.3.dll. Verze modulu iisnode. Inspektoru 0.7.3.dll používá node-inspector – 0.7.3 a používá webové sokety. Povolte webové sokety vaše Azure webových aplikací k používání této verze. Zobrazit <https://ranjithblogs.azurewebsites.net/?p=98> podrobné informace o tom, jak nakonfigurovat modul iisnode použít nové nástroje node-inspector.

### <a name="flushresponse"></a>flushResponse

Výchozí chování služby IIS je, že ho ukládá do vyrovnávací paměti dat odpovědi až na 4 MB před vyprazdňování nebo až do konce odpověď, a to podle toho, co nastane dřív. iisnode nabízí nastavení konfigurace pro toto chování přepsat: vyprázdnit fragment entity těla odpovědi poté, co modulu iisnode obdrží z node.exe, je nutné nastavit iisnode/@flushResponse atributu v souboru web.config na 'true':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Povolit spotřeby každý fragmentu odpovědi obsahu entity přidá nároky na výkon, která organizacím snižuje propustnosti systému % 5 (od v0.1.13). Nejlepší k určení rozsahu toto nastavení jenom na koncové body, které vyžadují odpovědi streamování (například pomocí `<location>` element v souboru web.config)

Kromě toho pro streamování aplikací, musíte taky nastavit responseBufferLimit vaše obslužná rutina modulu iisnode na hodnotu 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Středníkem oddělený seznam souborů, které jsou sledovány pro změny. Všechny změny do souboru způsobí, že aplikace recyklovat. Každý záznam se skládá z názvu nepovinných adresářů jako název požadovaný soubor, který jsou relativní vzhledem k adresáři, kde se nachází hlavní aplikace vstupní bod. Zástupné znaky jsou povoleny v pouze část názvu souboru. Výchozí hodnota je `*.js;web.config`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Výchozí hodnota je false. Pokud je povoleno, vaši aplikaci uzlu můžete připojit k pojmenovanému kanálu (proměnnou prostředí modulu IISNODE\_ovládací PRVEK\_kanálu) a odešle zprávu "recyklace". To způsobí, že w3wp recyklaci bez výpadku.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Výchozí hodnota je 0, což znamená, že tato funkce je zakázaná. Pokud nastavíte na hodnotu větší než 0, modulu iisnode bude stránka na všech jeho podřízených procesů každých "idlePageOutTimePeriod" v milisekundách. Zobrazit [dokumentaci](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) pochopit, co si znamená, že stránka. Toto nastavení je užitečné pro aplikace, které využívají vysoké množství paměti a chcete stránce limitu paměti na disk čas od času pro uvolnění paměti RAM.

> [!WARNING]
> Buďte opatrní při povolování následující nastavení konfigurace v aplikacích v produkčním prostředí. Doporučení nebudete je moci povolit na aktivní produkční aplikace.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Výchozí hodnota je false. Pokud je nastaveno na true, modulu iisnode přidá hlavičku HTTP odpovědi `iisnode-debug` do každé odpovědi protokolu HTTP odesílá `iisnode-debug` hodnota hlavičky je adresa URL. Je možné získat jednotlivé diagnostických informací prohlížení fragment adresy URL, ale vizualizace je k dispozici tak, že otevřete adresu URL v prohlížeči.

### <a name="loggingenabled"></a>loggingEnabled

Toto nastavení řídí protokolování stdout a stderr a modulu iisnode. Iisnode zaznamená stdout/stderr z uzlu procesů spustí a zapíše ho do adresáře určený v nastavení "logDirectory". Jakmile je tato možnost povolena, vaše aplikace zapisuje protokoly do systému souborů a podle toho, množství protokolování provádí aplikace, může mít vliv na výkon.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Výchozí hodnota je false. Při nastavenou na hodnotu true, modulu iisnode zobrazí stavový kód HTTP a kód chyby Win32 ve webovém prohlížeči. Kód win32 je užitečné při ladění určité typy potíží.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (není doporučeno zapínat na živý provozní web)

Toto nastavení řídí funkce ladění. Modul Iisnode je integrovaný nástroj node-inspector. Povolením tohoto nastavení můžete povolit ladění vaši aplikaci uzlu. Po povolení tohoto nastavení, vytvoří modul iisnode nástroj node-inspector soubory v adresáři "debuggerVirtualDir" na první žádost ladění vaší aplikace v Ruby. Nástroj node-inspector můžete načíst odesláním požadavku do http://yoursite/server.js/debug. Segment adresy URL ladění můžete řídit nastavení "debuggerPathSegment". Ve výchozím nastavení, debuggerPathSegment = "debug". Můžete nastavit `debuggerPathSegment` identifikátor GUID, například proto, že je obtížnější být zjištěny jinými uživateli.

Čtení [ladění aplikací node.js na Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) podrobné informace o ladění.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scénáře a řešení problémů a doporučení

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Moje aplikace v Ruby je provádění nadměrného volání odchozí

Mnoho aplikací byste měli provést odchozí připojení jako součást svých běžném provozu. Například když požadavek pochází, vaše aplikace node byste měli kontaktovat rozhraní REST API jinde a získáte nějaké informace pro zpracování žádosti. Chcete použít agenta zachovat naživu při volání http nebo https. Můžete použít modul agentkeepalive jako keep alive agenta při provádění těchto odchozích volání.

Modul agentkeepalive zajistí, že sockets jsou opakovaně použít na vaší webové aplikace Azure VM. Vytvoření nové soket na každý odchozí požadavek režie přidá do vaší aplikace. Aplikaci opětovné použití soketů pro odchozí požadavky zajistí, že vaše aplikace nepřekračuje maxSockets, který se přiděluje na jednotlivá virtuální počítač. Doporučení ve službě Azure Web Apps je nastavit hodnotu maxSockets agentKeepAlive se celkový počet (4 instance node.exe \* 40 maxSockets/instance) 160 sockets na virtuální počítač.

Příklad [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) konfigurace:

```nodejs
var keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Tento příklad předpokládá, že máte 4 node.exe běžící na virtuálním počítači. Pokud máte jiný počet node.exe běžící na virtuálním počítači, je třeba upravit maxSockets nastavení odpovídajícím způsobem.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Moje aplikace v Ruby spotřebovává příliš mnoho procesoru

Může se zobrazit doporučení od Azure Web Apps na portálu o vysoké využití procesoru. Monitorování můžete nastavit také sledovat u určitých [metriky](web-sites-monitor.md). Když zkontrolujete využití procesoru [řídicí panel portálu Azure](../application-insights/app-insights-web-monitor-performance.md), zkontrolujte maximální hodnoty pro procesor, Nenechte si ujít maximální hodnoty.
Pokud si myslíte, že vaše aplikace spotřebovává příliš mnoho CPU a nelze vysvětlit, proč, můžete provádět profilaci vaši aplikaci uzlu zjistit.

#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>Profilace aplikace node ve službě Azure Web Apps s V8 Profiler

Řekněme například, že budete mít aplikaci hello world, kterou chcete Profilovat následujícím způsobem:

```nodejs
var http = require('http');
function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Přejděte na web konzoly ladění https://yoursite.scm.azurewebsites.net/DebugConsole

Přejděte do adresáře site/wwwroot. Příkazový řádek uvidíte, jak je znázorněno v následujícím příkladu:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Spusťte příkaz `npm install v8-profiler`.

Tento příkaz nainstaluje profiler v8 pod uzlem\_moduly adresář a všechny jeho závislosti.
Nyní upravte vaše server.js k profilování aplikace.

```nodejs
var http = require('http');
var profiler = require('v8-profiler');
var fs = require('fs');

function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Předchozí kód profilů WriteConsoleLog funkci a pak zapíše výstup profilu do souboru "profile.cpuprofile" v rámci vaší lokality wwwroot. Odešle žádost do vaší aplikace. Naleznete v tématu "profile.cpuprofile" soubor vytvořený v rámci vaší lokality wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Stáhněte si tento soubor a otevřete jej pomocí nástroje F12 Chrome. Stisknutím klávesy F12 v Chrome, a pak zvolte **profily** kartu. Zvolte **zatížení** tlačítko. Vyberte soubor profile.cpuprofile, který jste stáhli. Klikněte na profil, který jste právě načetli.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Uvidíte, že 95 % doby spotřebovával WriteConsoleLog funkcí. Výstup také zobrazuje čísla řádků přesné a zdrojové soubory, které způsobily potíže.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Moje aplikace v Ruby spotřebovává příliš mnoho paměti

Pokud vaše aplikace spotřebovává příliš mnoho paměti, zobrazí na portálu o vysoké spotřeby paměti oznámení z Azure Web Apps. Můžete nastavit monitorování pro určité sledovat [metriky](web-sites-monitor.md). Při kontrole využití paměti na [řídicí panel portálu Azure](../application-insights/app-insights-web-monitor-performance.md), nezapomeňte se podívat maximální hodnoty paměti, Nenechte si ujít maximální hodnoty.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Detekce nevrácení paměti a halda Diff pro node.js

Můžete použít [uzel memwatch](https://github.com/lloyd/node-memwatch) ke snadnější identifikaci paměti nevrací.
Můžete nainstalovat `memwatch` stejně jako v8 profileru a úpravy kódu k zachycení a rozdílové haldy k identifikaci nevracení paměti v aplikaci.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Moje node.exe jsou získávání náhodně ukončen

Existuje několik důvodů, proč node.exe vypnutý náhodně:

1. Vaše aplikace vyvolává nezachycených výjimek – kontrola d:\\domácí\\LogFiles\\aplikace\\souboru protokolování errors.txt podrobnosti na výjimku. Tento soubor obsahuje trasování zásobníku, ladění a opravte vaší aplikace.
2. Vaše aplikace spotřebovává příliš mnoho paměti, což ovlivňuje jiné procesy od zahájení práce. Pokud celková velikost paměti virtuálního počítače je téměř 100 %, může vedoucí proces ukončen vaše node.exe. Správce procesu ukončuje některé procesy, které chcete, aby ostatní procesy mít šanci nějakou práci navíc. Chcete-li vyřešit tento problém, profilace aplikace nevracení paměti. Pokud vaše aplikace vyžaduje velké množství paměti, vertikálně navýšit kapacitu na větší virtuální počítač (což zvyšuje úroveň k dispozici k virtuálnímu počítači RAM).

### <a name="my-node-application-does-not-start"></a>Moje aplikace v Ruby nespustí.

Pokud vaše aplikace vrací 500 chyb při spuštění, může být z několika důvodů:

1. Node.exe se nenachází ve správném umístění. Zkontrolujte nastavení nodeProcessCommandLine.
2. Soubor hlavní skriptu se nenachází ve správném umístění. Zkontrolujte soubor web.config a ujistěte se, že název souboru hlavního skriptu v oddílu obslužných rutin odpovídá souboru hlavního skriptu.
3. Konfigurace souboru Web.config není správný – Zkontrolujte názvy a hodnoty nastavení.
4. Studený Start – aplikace trvá moc dlouho spustit. Pokud vaše aplikace trvá déle než (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000 sekund modulu iisnode vrátí chybu 500. Zvýšení hodnoty z těchto nastavení tak, aby odpovídaly spuštění vaší aplikace zabránit modulu iisnode z vypršení časového limitu a vrátí Chyba 500.

### <a name="my-node-application-crashed"></a>Moje aplikace v Ruby došlo k chybě

Vaše aplikace vyvolává nezachycených výjimek – kontrola `d:\\home\\LogFiles\\Application\\logging-errors.txt` souboru podrobnosti na výjimku. Tento soubor obsahuje trasování zásobníku pomáhají diagnostikovat a řešit vaší aplikace.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Moje aplikace v Ruby trvá příliš mnoho času spuštění (studený Start)

Obvyklou příčinou dlouhé doby spuštění aplikace je velký počet souborů v uzlu\_moduly. Aplikace se pokusí načíst většinu těchto souborů při spuštění. Ve výchozím nastavení protože jsou vaše soubory uložené ve sdílené síťové složky ve službě Azure Web Apps, načítání mnoho souborů může trvat dobu.
Některá řešení pro tento proces urychlit jsou:

1. Ujistěte se, že máte strukturu plochých závislost a nemá žádné duplicitní závislosti pomocí npm3 pro instalaci modulů.
2. Zkuste opožděné načtení uzlu\_moduly a nenačetla všechny moduly při spuštění aplikace. Opožděné načtení moduly volání require('module') měli vzít v úvahu modulu v rámci funkce před prvním spuštěním modulu kódu skutečně potřebujete.
3. Azure Web Apps nabízí funkci s názvem místní mezipaměti. Tato funkce zkopíruje obsah ze sdílené síťové složce na místní disk na virtuálním počítači. Protože soubory jsou místní, čas načtení uzlu\_moduly je mnohem rychlejší.

## <a name="iisnode-http-status-and-substatus"></a>Stav modulu IISNODE protokolu http a substatus

`cnodeconstants` [Zdrojový soubor](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) seznamy všechny kombinace iisnode možné stav/substatus může vrátit z důvodu chyby.

Povolit FREB pro aplikace, abyste viděli kód chyby win32 (Nezapomeňte povolit FREB pouze na webech li se o neprodukční z důvodů výkonu).

| Stav HTTP | Podřízený stav protokolu HTTP | Možný důvod? |
| --- | --- | --- |
| 500 |1000 |Došlo k problému některé odesílání požadavku do modulu IISNODE – zaškrtněte, pokud byl spuštěn node.exe. Node.exe může mít došlo k chybě při spuštění. Zkontrolujte konfiguraci souboru web.config pro chyby. |
| 500 |1001 |-Win32Error 0x2 – aplikace nereaguje na adresu URL. Pokud vaše aplikace express obsahuje správný trasy definované zkontrolujte pravidla pro přepis adres URL nebo kontrola. -Win32Error 0x6d – pojmenovaného kanálu je zaneprázdněn – Node.exe nepřijímá požadavky, protože kanálu je zaneprázdněný. Zkontrolujte vysoké využití procesoru. -Další chyby – zkontrolujte, pokud došlo k chybě node.exe. |
| 500 |1002 |Došlo k chybě Node.exe členové – projděte d:\\domácí\\LogFiles\\errors.txt protokolování pro trasování zásobníku. |
| 500 |1003 |Konfigurace kanálu problém – pojmenovaný kanál konfigurace je nesprávná. |
| 500 |1004-1018 |Při odesílání požadavku nebo odpovědi z node.exe zpracování došlo k nějaké chybě. Zkontrolujte, jestli došlo k chybě node.exe. Zkontrolujte d:\\domácí\\LogFiles\\errors.txt protokolování pro trasování zásobníku. |
| 503 |1000 |Není dostatek paměti k přidělení více pojmenovaných rour. Kontrola, proč vaše aplikace spotřebovává tolik paměti. Zkontrolujte hodnotu nastavení maxConcurrentRequestsPerProcess. Pokud není neomezená a máte velký počet požadavků, zvyšte tato hodnota k této chybě zabránit. |
| 503 |1001 |Žádost nebyla odeslána pro node.exe, protože aplikace se recykluje. Po recyklaci aplikaci, má požadavky by měl obvykle obsluhují. |
| 503 |1002 |Zkontrolujte kód chyby win32: skutečný z důvodu – žádost nebyla odeslána ke node.exe. |
| 503 |1003 |Pojmenovaný kanál je příliš zaneprázdněn – ověřte, zda node.exe nespotřeboval nadměrnému využití procesoru |

NODE.exe má nastavení nazývá `NODE_PENDING_PIPE_INSTANCES`. Ve výchozím nastavení když není nasazený ve službě Azure Web Apps, tato hodnota je 4. To znamená, že node.exe přijmout jenom čtyři požadavky po jednom v pojmenovaném kanálu. Ve službě Azure Web Apps tato hodnota nastavena na 5 000. Tato hodnota by měla být dostatečné pro většinu aplikací node běžící na Azure Web Apps. Z důvodu vysoké hodnoty pro byste neměli vidět 503.1003 ve službě Azure Web Apps `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Další zdroje informací

Další informace o aplikacích node.js ve službě Azure App Service na následujících odkazech.

* [Začínáme s webovými aplikacemi Node.js ve službě Azure App Service](app-service-web-get-started-nodejs.md)
* [Postup ladění webové aplikace Node.js ve službě Azure App Service](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Používání modulů Node.js s aplikacemi Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Středisko pro vývojáře Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Seznámení se supertajnou konzolou pro ladění modulu Kudu](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)