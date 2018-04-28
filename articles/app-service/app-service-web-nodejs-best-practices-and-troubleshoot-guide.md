---
title: Osvědčené postupy a Průvodci odstraňováním potíží aplikace uzlu ve webových aplikacích Azure
description: Zjistěte, osvědčené postupy a řešení potíží pro uzlu aplikace v Azure Web Apps.
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
ms.openlocfilehash: 860874ed49056e6b4695c060b06bf061820c390e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Osvědčené postupy a Průvodci odstraňováním potíží aplikace uzlu ve webových aplikacích Azure

V tomto článku se dozvíte osvědčené postupy a řešení potíží pro [uzel aplikace](app-service-web-get-started-nodejs.md) systémem Azure Web Apps (s [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Buďte opatrní při používání při řešení potíží na vašem provozním serveru. Doporučuje se řešení problémů aplikace na instalační program mimo produkční například přípravný slot a pokud je problém vyřešen, Prohodit vaší přípravný slot s produkční slot.
>

## <a name="iisnode-configuration"></a>Konfigurace modulu IISNODE

To [soubor schématu](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) ukazuje všechna nastavení, můžete nakonfigurovat pro modulu iisnode. Některá nastavení, které jsou užitečné pro vaši aplikaci:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Toto nastavení určuje počet uzlu procesy, které jsou spouštěny na aplikaci služby IIS. Výchozí hodnota je 1. Můžete spustit tolik node.exes jako vaše počet virtuálních procesorů virtuálních počítačů můžete změnit hodnotu na 0. Doporučená hodnota je 0 pro většinu aplikací, abyste mohli používat všechny Vcpu na váš počítač. Node.exe je jedním podprocesem, jeden node.exe využívá maximálně 1 virtuální procesor. Chcete-li získat maximální výkon mimo aplikaci uzlu, chcete použít všechny Vcpu.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Toto nastavení určuje cestu ke node.exe. Můžete nastavit tuto hodnotu tak, aby odkazoval na vaší verzi node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Toto nastavení určuje maximální počet souběžných požadavků, které posílá každý node.exe modulu iisnode. V Azure Web Apps výchozí hodnota je nekonečno. Pokud nejsou hostované v Azure Web Apps, výchozí hodnota je 1024. Můžete nakonfigurovat hodnotu v závislosti na tom, kolik žádostí že aplikace přijímá a jak rychle vaše aplikace zpracovává každý požadavek.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Toto nastavení určuje maximální počet opakování modulu iisnode provedením připojení v pojmenovaném kanálu pro odesílání požadavků na node.exe. Toto nastavení v kombinaci s namedPipeConnectionRetryDelay Určuje celkový časový limit každého požadavku v rámci modulu iisnode. Výchozí hodnota je 200 ve webových aplikacích Azure. Celkový časový limit v sekundách = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Toto nastavení určuje dobu (v ms) modulu iisnode čeká mezi každou opakovat, pokud chcete odeslat požadavek node.exe přes pojmenovaný kanál. Výchozí hodnota je 250 ms.
Celkový časový limit v sekundách = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

Ve výchozím nastavení, celkový časový limit v modulu iisnode ve webových aplikacích Azure je 200 \* 250 ms = 50 sekund.

### <a name="logdirectory"></a>logDirectory

Toto nastavení určuje adresář, kam modulu iisnode protokoluje stdout/stderr. Výchozí hodnota je modulu iisnode, která je relativní vzhledem k adresáři hlavního skriptu (adresáře, kde je hlavní server.js existuje)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Toto nastavení určuje, jaká verze modulu iisnode nástroj node-inspector používá při ladění aplikace uzlu. V současné době modulu iisnode. inspector 0.7.3.dll a iisnode inspector.dll jsou pouze dvě platné hodnoty pro toto nastavení. Výchozí hodnota je modulu iisnode. inspector 0.7.3.dll. Verze modulu iisnode. inspector 0.7.3.dll používá uzlu inspector 0.7.3 a používá webové sokety. Povolte webové sokety na vaše Azure webapp používat tuto verzi. V tématu <http://ranjithblogs.azurewebsites.net/?p=98> další podrobnosti o tom, jak nakonfigurovat modulu iisnode používat nový nástroj node-inspector.

### <a name="flushresponse"></a>flushResponse

Výchozí chování služby IIS je, že ukládány data odpovědi až 4 MB před vyčištění, nebo až do konce odpověď, nastane dříve. iisnode nabízí konfigurační nastavení pro toto chování potlačit: Chcete-li vyprázdnit fragment textu entity odpovědi na co nejdříve po přijetí z node.exe modulu iisnode, je potřeba nastavit iisnode/@flushResponse atribut v souboru web.config na hodnotu true.:

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Povolit spotřeby každých fragmentu odpovědi obsahu entity zvýší nároky na výkon, sníží propustnost systému % ~ 5 (od v0.1.13). Nejlepší k určení rozsahu toto nastavení pouze pro koncové body, které vyžadují vysílání datového proudu odpovědi (například pomocí `<location>` element v souboru web.config)

Kromě toho pro streamování aplikací, musíte taky nastavit responseBufferLimit vaší obslužné rutiny modulu iisnode na hodnotu 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Středníkem oddělené seznam souborů, které jsou sledovaná změny. Všechny změny do souboru způsobí, že aplikace recyklace. Každá položka obsahuje název volitelné directory, jakož i název požadovaný soubor, které jsou relativní vzhledem k adresáři, kde se nachází vstupní bod hlavní aplikace. Zástupné znaky nejsou povoleny v pouze část názvu souboru. Výchozí hodnota je `*.js;web.config`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Výchozí hodnota je false. Pokud je povoleno, aplikace uzlu můžete připojit k pojmenovanému kanálu (proměnnou prostředí modulu IISNODE\_řízení\_kanálu) a odeslat zprávu "recyklaci". To způsobí, že w3wp řádně recyklace.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Výchozí hodnota je 0, což znamená, že tato funkce je vypnutá. Pokud nastavíte na hodnotu větší než 0, modulu iisnode bude stránka se všechny jeho podřízené procesy každých 'idlePageOutTimePeriod' v milisekundách. V tématu [dokumentace](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) zjistit, co stránky na prostředky. Toto nastavení je užitečné pro aplikace, které vysoké nároky na paměť a chcete na stránku limitu paměti na disk příležitostně pro uvolnění paměti RAM.

> [!WARNING]
> Buďte opatrní při povolování následující nastavení konfigurace na výrobní aplikace. Doporučuje se není je povolit v za provozu výrobní aplikace.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Výchozí hodnota je false. Pokud je nastaven na hodnotu true, modulu iisnode přidá hlavičku HTTP odpovědi `iisnode-debug` každé odpovědi HTTP odešle `iisnode-debug` hodnota hlavičky je adresa URL. Jednotlivé diagnostické informace můžete získat prohlížením fragment adresy URL, ale vizualizace je k dispozici po otevření této adresy URL v prohlížeči.

### <a name="loggingenabled"></a>loggingEnabled

Toto nastavení určuje protokolování stdout a stderr pomocí modulu iisnode. Iisnode zaznamená stdout/stderr z uzlu procesy se spustí a zapíše ho do adresáře zadali v nastavení 'logDirectory'. Jakmile je tato možnost povolena, vaše aplikace zapisuje protokoly do systému souborů a v závislosti na množství protokolování, provádí aplikace, může být ovlivnit výkon.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Výchozí hodnota je false. Když nastaven na hodnotu true, modulu iisnode zobrazí stavový kód HTTP a kód chyby Win32 prohlížeč. Kód win32 je užitečné při ladění určité typy potíží.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (nepovolujte na provozním serveru)

Toto nastavení řídí funkce ladění. Pomocí nástroje node-inspector je integrovaná modulu Iisnode. Povolením tohoto nastavení můžete povolit ladění aplikace uzlu. Po povolení tohoto nastavení, modulu iisnode vytvoří nástroj node-inspector soubory v adresáři 'debuggerVirtualDir' na první požadavek ladění na aplikaci uzlu. Nástroj node-inspector můžete načíst pomocí odesílání požadavku na http://yoursite/server.js/debug. Segment adresy URL ladění můžete ovládat nastavení 'debuggerPathSegment'. Ve výchozím nastavení debuggerPathSegment = "debug". Můžete nastavit `debuggerPathSegment` identifikátor GUID, například tak to je obtížnější být zjištěny jinými uživateli.

Čtení [ladění aplikací node.js v systému Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) Další informace o ladění.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scénáře a doporučení nebo řešení potíží

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Moje aplikace uzlu je volání nadměrné odchozí

Mnoho aplikací chtít provést v rámci jejich regulární operace odchozí připojení. Například když přijde žádost, aplikace uzlu vhodnější kontaktovat rozhraní REST API jinde a určité informace pro zpracování požadavku. Chcete by se použít zachování připojení agenta zachovat při volání protokolu http nebo https. Můžete použít modul agentkeepalive jako aktivní agenta zachovat při provádění těchto odchozí volání.

Modul agentkeepalive zajistí, že sockets jsou opakovaně na vaší webové aplikace Azure virtuálních počítačů. Vytvoření nové soketu na každý požadavek odchozí přidá režie k vaší aplikaci. Má vaše aplikace znovu použít sockets pro odchozí požadavky zajistí, zda aplikace nepřekročí maxSockets, které jsou přiděleny na virtuální počítač. Ve webových aplikacích Azure doporučujeme nastavit hodnotu maxSockets agentKeepAlive na celkem (4 instancí node.exe \* 40 maxSockets/instance) 160 sockets na virtuální počítač.

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
> Tento příklad předpokládá, že máte 4 node.exe systémem virtuálního počítače. Pokud máte jiný počet node.exe spuštěna na virtuálním počítači, musíte upravit maxSockets nastavení odpovídajícím způsobem.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Moje uzel aplikace využívala příliš mnoho procesoru

Z Azure Web Apps můžete obdržet doporučení na portálu o vysoké spotřeby procesoru. Můžete také nastavit tak, monitorování si chcete přehrát určité [metriky](web-sites-monitor.md). Při kontrole využití procesoru na [řídicího panelu portálu Azure](../application-insights/app-insights-web-monitor-performance.md), zkontrolujte maximální hodnoty pro procesor, nemusíte neproběhly hodnoty ve špičce.
Pokud se domníváte, že se vaše aplikace využívala příliš mnoho procesorů a důvod, proč nelze vysvětlují, je uzel aplikace a zjistěte, profilů.

#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>Profilace uzlu aplikace v Azure Web Apps s v8: profileru

Řekněme například, že máte aplikaci hello world, které chcete profil následujícím způsobem:

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

Přejděte na web ladění konzoly https://yoursite.scm.azurewebsites.net/DebugConsole

Přejděte do adresáře webu/wwwroot. Zobrazí příkazový řádek, jak je znázorněno v následujícím příkladu:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Spusťte příkaz `npm install v8-profiler`.

Tento příkaz nainstaluje profileru v8: v uzlu\_directory moduly a všechny jeho závislé součásti.
Nyní upravte vaše server.js do profilu aplikace.

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

Předchozí kód profily WriteConsoleLog funkce a pak zapíše profil výstup do souboru 'profile.cpuprofile' v rámci vaší lokality wwwroot. Odeslat požadavek do vaší aplikace. Zobrazí soubor 'profile.cpuprofile' vytvořil v rámci vaší lokality wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Tento soubor stáhnout a otevřete jej pomocí nástroje F12 Chrome. Stisknutím klávesy F12 na Chrome, a potom vyberte **profily** kartě. Vyberte **zatížení** tlačítko. Vyberte souboru profile.cpuprofile, který jste si stáhli. Klikněte na profil, který jste právě načetli.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Uvidíte, že 95 % času se spotřebovávají WriteConsoleLog funkce. Výstup také ukazuje linek čísla a zdrojové soubory, které způsobila problém.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Moje aplikace uzlu je spotřeba paměti převýší

Pokud vaše aplikace využívala příliš mnoho paměti, zobrazí na portálu o vysoké spotřeby paměti oznámení z Azure Web Apps. Můžete nastavit monitorování si chcete přehrát určité [metriky](web-sites-monitor.md). Při kontrole využití paměti na [řídicího panelu portálu Azure](../application-insights/app-insights-web-monitor-performance.md), nezapomeňte zkontrolovat maximální hodnoty paměti, nemáte neproběhly hodnoty ve špičce.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Zjištění paměti a rozdílové haldy pro node.js

Můžete použít [uzlu memwatch](https://github.com/lloyd/node-memwatch) usnadňující identifikaci paměti nevracení.
Můžete nainstalovat `memwatch` stejně jako v8: profileru a úpravy kódu k zachycení a rozdílové prostorovým k identifikaci nevracení paměti v aplikaci.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Moje node.exe jsou získávání ukončeny náhodně

Existuje několik důvodů, proč node.exe vypnutý náhodně:

1. Aplikace je vyvolání nezachycená výjimek – kontrola d:\\domácí\\LogFiles\\aplikace\\protokolování errors.txt souboru podrobnosti v došlo k výjimce. Tento soubor má trasování zásobníku pro ladění a odstranění vaší aplikace.
2. Vaše aplikace využívala příliš mnoho paměti, která ovlivňuje jiné procesy z Začínáme. Pokud celková velikost paměti virtuálního počítače je téměř 100 %, může vaše node.exe ukončeny správcem procesu. Proces manager ukončí některých procesů umožníte jiné procesy ještě nějakou práci. Chcete-li tento problém vyřešit, profil aplikace pro nevracení paměti. Pokud vaše aplikace vyžaduje velké množství paměti, škálování na větší virtuální počítač (takže se zvětší RAM, které jsou k dispozici pro virtuální počítač).

### <a name="my-node-application-does-not-start"></a>Moje aplikace uzlu nespustí.

Pokud vaše aplikace vrací chyby 500 po jeho spuštění, může být z několika důvodů:

1. Node.exe se nenachází ve správném umístění. Zkontrolujte nastavení nodeProcessCommandLine.
2. Soubor skriptu hlavní se nenachází ve správném umístění. Zkontrolujte soubor web.config a ujistěte se, že název souboru hlavní skriptu v oddílu obslužných rutin odpovídá souboru hlavní skriptu.
3. Konfigurace souboru Web.config není správný – zkontrolujte nastavení názvy nebo hodnoty.
4. Studený Start – aplikace trvá příliš dlouho spustit. Pokud vaše aplikace trvá déle, než (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1 000 sekund modulu iisnode vrátí chybu 500. Zvýšení hodnoty těchto nastavení tak, aby odpovídaly spuštění vaší aplikace aby modulu iisnode z vypršení časového limitu a vrácení za následek chybu 500.

### <a name="my-node-application-crashed"></a>Moje aplikace uzlu došlo k chybě

Aplikace je vyvolání nezachycená výjimek – kontrola `d:\\home\\LogFiles\\Application\\logging-errors.txt` souboru podrobnosti v došlo k výjimce. Tento soubor má trasování zásobníku pomohou diagnostikovat a opravit vaší aplikace.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Moje aplikace uzlu trvá příliš mnoho času spuštění (studený Start)

Obvyklou příčinou dlouhé doby spuštění aplikace je vysoký počet souborů v uzlu\_moduly. Aplikace se pokusí načíst většinu těchto souborů při spuštění. Ve výchozím nastavení protože jsou vaše soubory uložené ve sdílené síťové složce ve webových aplikacích Azure, načítání mnoho souborů může trvat dobu.
Některá řešení rychleji, aby tento proces se:

1. Ujistěte se, že máte struktura ploché závislostí a žádné duplicitní závislosti pomocí npm3 nainstalovat moduly.
2. Pokuste se opožděné načíst vaše uzlu\_moduly a zatížení všechny moduly při spuštění aplikace. Opožděné zatížení moduly volání require('module') třeba když potřebujete ve skutečnosti modul v rámci funkce před první spuštění modulu kódu.
3. Azure Web Apps nabízí funkci místní mezipaměti. Tato funkce zkopíruje obsah ze sdílené síťové složce na místní disk ve virtuálním počítači. Vzhledem k tomu, že soubory jsou místní, čas načítání uzlu\_moduly je mnohem rychlejší.

## <a name="iisnode-http-status-and-substatus"></a>Stav protokolu http modulu IISNODE a podřízeného stavu

`cnodeconstants` [Zdrojový soubor](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) seznamy všechny iisnode kombinace možné stav nebo substatus může vrátit z důvodu chyby.

Povolit FREB pro aplikace, abyste viděli kód chyby win32 (ujistěte se, povolíte FREB pouze na webech mimo produkční z důvodů výkonu).

| Stav HTTP | Podřízený stav protokolu HTTP | Možný důvod? |
| --- | --- | --- |
| 500 |1000 |Se některé problém odeslání požadavku na modulu IISNODE – zkontrolujte, zda bylo zahájeno node.exe. Node.exe může selhání při spouštění. Zkontrolujte konfiguraci web.config chyby. |
| 500 |1001 |-Win32Error 0x2 - aplikace nereaguje na adresu URL. Zkontrolujte pravidel přepisování adres URL nebo zkontrolujte, zda aplikace express má správný trasy definované. -Win32Error 0x6d – pojmenovaný kanál je zaneprázdněn – Node.exe nepřijímá požadavky, protože je zaneprázdněný. Zkontrolujte vysoké využití procesoru. -Další chyby – zkontrolujte, pokud node.exe došlo k chybě. |
| 500 |1002 |Došlo k chybě Node.exe – zkontrolujte d:\\domácí\\LogFiles\\errors.txt protokolování pro trasování zásobníku. |
| 500 |1003 |Konfigurace kanálu problém – pojmenovaný kanál konfigurace je nesprávná. |
| 500 |1004-1018 |Při odesílání požadavku nebo odpovědi do a z node.exe zpracování se nějaká chyba. Zkontrolujte, zda node.exe došlo k chybě. Zkontrolujte d:\\domácí\\LogFiles\\errors.txt protokolování pro trasování zásobníku. |
| 503 |1000 |Není dostatek paměti k přidělení více pojmenovaného kanálu připojení. Zkontrolujte, proč aplikace využívala mnoho paměti. Zkontrolujte hodnotu nastavení maxConcurrentRequestsPerProcess. Pokud není nekonečné a budete mít mnoho požadavků, zvýšit tuto hodnotu, aby se tato chyba. |
| 503 |1001 |Žádost nebyla odeslána do node.exe, protože je recyklace aplikace. Po aplikaci má recyklované, by měl obvykle zpracovat požadavky. |
| 503 |1002 |Kód chyby win32 zkontrolujte skutečné důvodu – žádost nebyla odeslána na node.exe. |
| 503 |1003 |Pojmenovaný kanál je příliš zaneprázdněn – ověřte, zda node.exe nespotřeboval nadměrnému využití procesoru |

NODE.exe má nastavení, nazvané `NODE_PENDING_PIPE_INSTANCES`. Ve výchozím nastavení když není nasazené v Azure Web Apps, tato hodnota je 4. Znamená, že node.exe přijmout jenom čtyři požadavků současně v pojmenovaném kanálu. Azure Web Apps tato hodnota nastavena do 5 000. Tato hodnota by měla být dostatečně vhodné pro většinu uzlu aplikací běžících na Azure Web Apps. Z důvodu vysoké hodnoty pro byste neměli vidět 503.1003 ve webových aplikacích Azure `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Další zdroje informací

Další informace o aplikací node.js v Azure App Service na následujících odkazech.

* [Začínáme s webovými aplikacemi Node.js ve službě Azure App Service](app-service-web-get-started-nodejs.md)
* [Postup ladění webové aplikace Node.js ve službě Azure App Service](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Používání modulů Node.js s aplikacemi Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Středisko pro vývojáře Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Seznámení se supertajnou konzolou pro ladění modulu Kudu](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)