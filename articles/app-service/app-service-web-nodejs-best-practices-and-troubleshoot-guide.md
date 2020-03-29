---
title: Osvědčené postupy souboru Node.js a řešení potíží
description: Seznamte se s doporučenými postupy a postupy řešení potíží pro aplikace Node.js spuštěné ve službě Azure App Service.
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 682884d11b298a97e27056af3c10802dfd410e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430564"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Průvodce doporučenými postupy a řešením potíží pro aplikace uzlů ve službě Azure App Service Windows

V tomto článku se dozvíte osvědčené postupy a postupy řešení potíží pro [aplikace uzlů](app-service-web-get-started-nodejs.md) spuštěné ve službě Azure App Service (s [iisnode).](https://github.com/azure/iisnode)

> [!WARNING]
> Při použití kroků řešení potíží na produkčním webu postupujte opatrně. Doporučení je řešit potíže s aplikací v neprodukčním nastavení, například v pracovním slotu a když je problém vyřešen, vyměňte pracovní slot za produkční slot.
>

## <a name="iisnode-configuration"></a>Konfigurace iISNODE

Tento [soubor schématu](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) zobrazuje všechna nastavení, která můžete nakonfigurovat pro iisnode. Některá nastavení, která jsou užitečná pro vaši aplikaci:

### <a name="nodeprocesscountperapplication"></a>aplikace nodeProcessCountPerApplication

Toto nastavení řídí počet procesů uzlů, které jsou spuštěny v aplikaci služby IIS. Výchozí hodnota je 1. Můžete spustit tolik node.exes jako počet virtuálních procesorů virtuálního počítače změnou hodnoty na 0. Doporučená hodnota je 0 pro většinu aplikací, takže můžete použít všechny virtuální procesory v počítači. Node.exe je jednovláknový, takže jeden node.exe spotřebovává maximálně 1 virtuální procesor. Chcete-li získat maximální výkon z aplikace uzlu, chcete použít všechny virtuální procesory.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Toto nastavení řídí cestu k souboru node.exe. Tuto hodnotu můžete nastavit tak, aby ukazovala na verzi node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Toto nastavení řídí maximální počet souběžných požadavků odeslaných iisnodou do každého uzlu.exe. Ve službě Azure App Service je výchozí hodnota Infinite. Hodnotu můžete nakonfigurovat v závislosti na tom, kolik požadavků aplikace obdrží a jak rychle aplikace zpracuje každý požadavek.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Toto nastavení řídí maximální počet opakování iisnode, které mají být odeslány na pojmenovaném kanálu, aby byly odeslány požadavky na soubor node.exe. Toto nastavení v kombinaci s názvemPipeConnectionRetryDelay určuje celkový časový limit každého požadavku v rámci iisnode. Výchozí hodnota je 200 ve službě Azure App Service. Celkový časový limit v sekundách = \* (maxNamedPipeConnectionRetry namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>pojmenovánoPipeConnectionRetryDelay

Toto nastavení řídí dobu (v ms) iisnode čeká mezi jednotlivými pokusy odeslat požadavek node.exe přes pojmenovaný kanál. Výchozí hodnota je 250 ms.
Celkový časový limit v sekundách = \* (maxNamedPipeConnectionRetry namedPipeConnectionRetryDelay) / 1000

Ve výchozím nastavení je celkový časový limit v iisnode \* ve službě Azure App Service 200 250 ms = 50 sekund.

### <a name="logdirectory"></a>logDirectory

Toto nastavení řídí adresář, ve kterém protokoly iisnode stdout/stderr. Výchozí hodnota je iisnode, který je relativní k adresáři hlavního skriptu (adresář, kde je k dispozici hlavní server.js)

### <a name="debuggerextensiondll"></a>ladičExtensionDll

Toto nastavení určuje, jakou verzi iisnode node-inspector používá při ladění aplikace uzlu. V současné době jsou iisnode-inspector-0.7.3.dll a iisnode-inspector.dll jedinými dvěma platnými hodnotami pro toto nastavení. Výchozí hodnota je iisnode-inspector-0.7.3.dll. Verze iisnode-inspector-0.7.3.dll používá node-inspector-0.7.3 a používá webové sokety. Povolte webové sokety ve webové aplikaci Azure, abyste mohli používat tuto verzi. Další <https://ranjithblogs.azurewebsites.net/?p=98> podrobnosti o konfiguraci iisnode pro použití nového inspektoru uzlu naleznete v tématu.

### <a name="flushresponse"></a>flushResponse

Výchozí chování služby IIS je, že vyrovnávací paměti data odpovědi až 4 MB před vyprázdnění nebo až do konce odpovědi, podle toho, co nastane dříve. Iisnode nabízí nastavení konfigurace přepsat toto chování: chcete-li vyprázdnit fragment těla entity odpovědi, jakmile iisnode iisnode/@flushResponse obdrží z node.exe, je třeba nastavit atribut v web.config na 'true':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Povolit vyprázdnění každého fragmentu těla entity odezvy přidá režii výkonu, která snižuje propustnost systému o ~5 % (od v0.1.13). Nejlepší je obor toto nastavení pouze pro koncové body, `<location>` které vyžadují streamování odpovědí (například pomocí prvku v web.config)

Kromě toho pro streamování aplikací, musíte také nastavit responseBufferLimit obslužné rutiny iisnode na 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>sledovanéSoubory

Středník oddělený seznam souborů, které jsou sledovány pro změny. Jakákoli změna souboru způsobí, že aplikace recyklovat. Každá položka se skládá z volitelného názvu adresáře a požadovaného názvu souboru, který je relativní vzhledem k adresáři, ve kterém je umístěn hlavní vstupní bod aplikace. Zástupné znaky jsou povoleny pouze v části s názvem souboru. Výchozí hodnotou je `*.js;iisnode.yml`.

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Výchozí hodnota je False. Pokud je tato možnost povolena, aplikace uzlu se může\_připojit\_k pojmenovanému kanálu (proměnná prostředí IISNODE CONTROL PIPE) a odeslat zprávu "recyklovat". To způsobí, že w3wp recyklovat elegantně.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Výchozí hodnota je 0, což znamená, že tato funkce je zakázána. Pokud je nastavena na některé hodnoty větší než 0, iisnode bude stránku ze všech svých podřízených procesů každý 'idlePageOutTimePeriod' v milisekundách. Informace o tom, co znamená stránka, naleznete v [dokumentaci.](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) Toto nastavení je užitečné pro aplikace, které spotřebovávají velké množství paměti a chcete občas stránkovat paměť na disk, aby se uvolnila paměť RAM.

> [!WARNING]
> Při povolování následujících nastavení konfigurace v produkčních aplikacích postupujte opatrně. Doporučujeme je nepovolit je v živých produkčních aplikacích.
>

### <a name="debugheaderenabled"></a>funkce debugHeaderEnabled

Výchozí hodnota je False. Pokud je nastavena na hodnotu true, `iisnode-debug` iisnode přidá `iisnode-debug` hlavičku odpovědi HTTP ke každé odpovědi HTTP, kterou odešle, je hodnota záhlaví adresa URL. Jednotlivé diagnostické informace lze získat při pohledu na fragment adresy URL, ale vizualizace je k dispozici otevřením adresy URL v prohlížeči.

### <a name="loggingenabled"></a>protokolovatpovoleno

Toto nastavení řídí protokolování stdout a stderr podle iisnode. Iisnode zachycuje stdout/stderr z procesů uzlů, které spustí, a zapíše do adresáře určeného v nastavení logDirectory. Jakmile je tato možnost povolena, aplikace zapíše protokoly do systému souborů a v závislosti na množství protokolování provedeného aplikací může mít vliv na výkon.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Výchozí hodnota je False. Pokud je nastavena hodnota true, iisnode zobrazí stavový kód HTTP a win32 kód chyby ve vašem prohlížeči. Kód win32 je užitečné při ladění určité typy problémů.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (nepovolit na živém produkčním webu)

Toto nastavení řídí funkci ladění. Iisnode je integrován s node-inspektor. Povolením tohoto nastavení povolíte ladění aplikace uzlu. Po povolení tohoto nastavení iisnode vytvoří soubory diodinspektor v adresáři 'debuggerVirtualDir' na první požadavek ladění do aplikace uzlu. Inspektor uzlu můžete načíst odesláním požadavku `http://yoursite/server.js/debug`společnosti . Segment ladicí adresy URL můžete řídit pomocí nastavení debuggerPathSegment. Ve výchozím nastavení debuggerPathSegment='debug'. Můžete nastavit `debuggerPathSegment` identifikátor GUID, například tak, aby bylo obtížnější zjistit ostatními.

Další podrobnosti o ladění naleznete [v aplikacích Ladění node.js v systému Windows.](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html)

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scénáře a doporučení/řešení potíží

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Aplikace uzlu provádí nadměrné odchozí volání

Mnoho aplikací by chtělo vytvořit odchozí připojení jako součást jejich pravidelného provozu. Například když přijde požadavek, aplikace uzlu by chtěl kontaktovat rozhraní REST API jinde a získat nějaké informace pro zpracování požadavku. Při volání http nebo https byste chtěli použít agenta keep alive. Můžete použít modul agentkeepalive jako agenta keep alive při těchto odchozích voláních.

Modul agentkeepalive zajišťuje, že sokety jsou znovu použity na vašem virtuálním počítači Azure webapp. Vytvoření nového soketu pro každý odchozí požadavek přidá režii vaší aplikace. S vaší aplikace znovu použít sokety pro odchozí požadavky zajišťuje, že vaše aplikace nepřekročí maxSockets, které jsou přiděleny na virtuální počítač. Doporučení pro službu Azure App Service je nastavit hodnotu agentKeepAlive maxSockets na celkem (4 instance node.exe \* 40 maxSockets/instance) 160 soketů na virtuální počítač.

Příklad konfigurace [agentaKeepALive:](https://www.npmjs.com/package/agentkeepalive)

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Tento příklad předpokládá, že máte 4 node.exe spuštěna na vašem virtuálním počítači. Pokud máte jiný počet node.exe spuštěna na virtuálním počítači, je nutné upravit maxSockets nastavení odpovídajícím způsobem.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Moje aplikace uzlu spotřebovává příliš mnoho procesoru

Můžete obdržet doporučení od služby Azure App Service na portálu o vysoké spotřebě procesoru. Můžete také nastavit monitory pro sledování určitých [metrik](web-sites-monitor.md). Při kontrole využití procesoru na [řídicím panelu portálu Azure Portal](../azure-monitor/app/web-monitor-performance.md)zkontrolujte hodnoty MAX pro procesor, abyste nezmeškali hodnoty ve špičce.
Pokud se domníváte, že vaše aplikace spotřebovává příliš mnoho procesoru a nemůžete vysvětlit proč, můžete profilovat aplikaci uzlu zjistit.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Profilování aplikace uzlu ve službě Azure App Service pomocí Nástroje pro profilování V8

Řekněme například, že máte aplikaci Hello World, kterou chcete profilovat takto:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
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

Přejít na web konzoly ladění`https://yoursite.scm.azurewebsites.net/DebugConsole`

Přejděte do adresáře wwwroot. Zobrazí se příkazový řádek, jak je znázorněno v následujícím příkladu:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Spusťte příkaz `npm install v8-profiler`.

Tento příkaz nainstaluje v8-profiler\_pod adresář modulů uzlů a všechny jeho závislosti.
Nyní upravte server.js profilovat aplikaci.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
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

Předchozí kód profiluje funkci WriteConsoleLog a poté zapíše výstup profilu do souboru profile.cpuprofile pod webovou wwwroot. Odešlete žádost do vaší aplikace. Pod webovou webovou webovou společností se zobrazí soubor profile.cpuprofile.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Stáhněte si tento soubor a otevřete jej pomocí nástrojů Chrome F12. Stiskněte f12 v Chromu a pak **Load** zvolte kartu **Profily.** Vyberte soubor profile.cpuprofile, který jste stáhli. Klikněte na profil, který jste právě načetli.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Můžete vidět, že 95 % času bylo spotřebováno funkcí WriteConsoleLog. Výstup také zobrazuje přesná čísla řádků a zdrojové soubory, které problém způsobily.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Aplikace uzlu spotřebovává příliš mnoho paměti

Pokud vaše aplikace spotřebovává příliš mnoho paměti, zobrazí se oznámení z Azure App Service na portálu o vysoké spotřebě paměti. Můžete nastavit monitory sledovat určité [metriky](web-sites-monitor.md). Při kontrole využití paměti na [řídicím panelu portálu Azure Portal](../azure-monitor/app/web-monitor-performance.md)zkontrolujte hodnoty MAX pro paměť, abyste nezmeškali hodnoty ve špičce.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Detekce nevracení a rozdíl haldy pro soubor node.js

Můžete použít [node-memwatch,](https://github.com/lloyd/node-memwatch) které vám pomohou identifikovat nevracení paměti.
Můžete nainstalovat `memwatch` stejně jako v8 profiler a upravit kód pro zachycení a rozdíl hromady k identifikaci nevracení paměti v aplikaci.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Můj node.exe jsou zabiti náhodně

Existuje několik důvodů, proč je node.exe vypnut náhodně:

1. Vaše aplikace je vyvolání nezachycené\\výjimky – Kontrola d: home\\LogFiles\\Aplikace\\protokolování errors.txt soubor pro podrobnosti o výjimce vyvolána. Tento soubor má trasování zásobníku pomoci ladit a opravit aplikaci.
2. Aplikace spotřebovává příliš mnoho paměti, což ovlivňuje jiné procesy od začátku. Pokud se celková paměť virtuálního počítače blíží 100 %, může být program node.exe ukončen správcem procesů. Správce procesů zabije některé procesy, aby ostatní procesy dostaly šanci udělat nějakou práci. Chcete-li tento problém vyřešit, profil aplikace pro nevracení paměti. Pokud vaše aplikace vyžaduje velké množství paměti, škálovat až na větší virtuální počítač (což zvyšuje ram k dispozici pro virtuální počítač).

### <a name="my-node-application-does-not-start"></a>Aplikace uzlu se nespustí

Pokud vaše aplikace vrací 500 Chyby při spuštění, může být několik důvodů:

1. Node.exe není k dispozici ve správném umístění. Zkontrolujte nastavení nodeProcessCommandLine.
2. Hlavní soubor skriptu není k dispozici ve správném umístění. Zkontrolujte web.config a ujistěte se, že název hlavního souboru skriptu v části obslužné rutiny odpovídá hlavnímu souboru skriptu.
3. Konfigurace web.config není správná – zkontrolujte nastavení jména/hodnoty.
4. Studený start – spuštění aplikace trvá příliš dlouho. Pokud vaše aplikace trvá déle než (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 sekund, vrátí iisnode chybu 500. Zvyšte hodnoty těchto nastavení tak, aby odpovídaly času spuštění aplikace, abyste zabránili vypršení časového limitu iisnode a vrácení chyby 500.

### <a name="my-node-application-crashed"></a>Aplikace uzlu se zhroutila

Vaše aplikace je vyvolání nezachycené výjimky – Zkontrolujte `d:\\home\\LogFiles\\Application\\logging-errors.txt` soubor pro podrobnosti o výjimce vyvolána. Tento soubor má trasování zásobníku pomoci diagnostikovat a opravit aplikaci.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Spuštění aplikace uzlu trvá příliš dlouho (studený start)

Běžnou příčinou dlouhých časů spuštění aplikace je vysoký počet\_souborů v modulech uzlů. Aplikace se pokusí načíst většinu těchto souborů při spuštění. Ve výchozím nastavení vzhledem k tomu, že vaše soubory jsou uloženy ve sdílené síťové službě ve službě Azure App Service, načítání mnoha souborů může nějakou dobu trvat.
Některá řešení, aby se tento proces rychleji jsou:

1. Ujistěte se, že máte strukturu plochých závislostí a žádné duplicitní závislosti pomocí npm3 k instalaci modulů.
2. Pokuste se opožděně\_načíst moduly uzlů a nenačíst všechny moduly při spuštění aplikace. Chcete-li lazy zatížení moduly, volání vyžadovat ('modul') by měla být provedena, když skutečně potřebujete modul v rámci funkce před prvním spuštěním kódu modulu.
3. Služba Azure App Service nabízí funkci nazvanou místní mezipaměť. Tato funkce zkopíruje obsah ze sdílené síťové složky na místní disk na virtuálním počítači. Vzhledem k tomu, že soubory\_jsou místní, doba načítání modulů uzlů je mnohem rychlejší.

## <a name="iisnode-http-status-and-substatus"></a>Stav a dílčí stav protokolu IISNODE http

`cnodeconstants` [Zdrojový soubor](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) obsahuje seznam všech možných kombinací stavu/dílčího stavu, které se iisnode mohou vrátit z důvodu chyby.

Povolit FREB pro vaši aplikaci zobrazit win32 kód chyby (ujistěte se, že povolíte FREB pouze na neprodukčních lokalitách z důvodů výkonu).

| Stav protokolu Http | Stav protokolu Http | Možný důvod? |
| --- | --- | --- |
| 500 |1000 |Došlo k nějakému problému odeslání požadavku na IISNODE – Zkontrolujte, zda byl spuštěn soubor node.exe. Node.exe mohl havarovat při spuštění. Zkontrolujte konfiguraci web.config, zda nenajdete chyby. |
| 500 |1001 |- Win32Error 0x2 - Aplikace nereaguje na adresu URL. Zkontrolujte pravidla přepisu adresy URL nebo zkontrolujte, zda má expresní aplikace definovány správné trasy. - Win32Error 0x6d – pojmenovaný kanál je zaneprázdněn – Node.exe nepřijímá požadavky, protože kanál je zaneprázdněn. Zkontrolujte vysoké využití procesoru. - Další chyby – zkontrolujte, zda node.exe havaroval. |
| 500 |1002 |Node.exe havaroval – kontrola\\\\d:\\home LogFiles logging-errors.txt pro trasování zásobníku. |
| 500 |1003 |Problém konfigurace kanálu – konfigurace pojmenovaného kanálu je nesprávná. |
| 500 |1004-1018 |Při odesílání požadavku nebo zpracování odpovědi na soubor node.exe došlo k určité chybě. Zkontrolujte, zda soubor node.exe havaroval. kontrola\\d:\\home\\LogFiles logging-errors.txt pro trasování zásobníku. |
| 503 |1000 |Nedostatek paměti k přidělení více připojení pojmenovaného kanálu. Podívejte se, proč vaše aplikace spotřebovává tolik paměti. Zkontrolujte hodnotu nastavení maxConcurrentRequestsPerProcess. Pokud není nekonečný a máte mnoho požadavků, zvyšte tuto hodnotu, abyste této chybě zabránili. |
| 503 |1001 |Požadavek nelze odeslat do souboru node.exe, protože aplikace je recyklace. Po recyklaci žádosti by žádosti měly být podávány normálně. |
| 503 |1002 |Zkontrolujte kód chyby win32 ze skutečného důvodu – Požadavek nelze odeslat do souboru node.exe. |
| 503 |1003 |Pojmenovaný kanál je příliš zaneprázdněn – ověřte, zda program node.exe spotřebovává nadměrné zatížení procesoru |

NODE.exe má nastavení `NODE_PENDING_PIPE_INSTANCES`s názvem . Ve službě Azure App Service je tato hodnota nastavena na 5000. To znamená, že node.exe může přijmout 5000 požadavků najednou na pojmenovaném kanálu. Tato hodnota by měla být dostatečně vhodná pro většinu aplikací uzlů spuštěné ve službě Azure App Service. Ve službě Azure App Service byste neměli vidět 503.1003 z důvodu vysoké hodnoty`NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Další zdroje informací

Další informace o aplikacích node.js ve službě Azure App Service najdete na těchto odkazech.

* [Začínáme s webovými aplikacemi Node.js ve službě Azure App Service](app-service-web-get-started-nodejs.md)
* [Postup ladění webové aplikace Node.js ve službě Azure App Service](https://blogs.msdn.microsoft.com/azureossds/2018/08/03/debugging-node-js-apps-on-azure-app-services/)
* [Používání modulů Node.js s aplikacemi Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Středisko pro vývojáře Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Seznámení se supertajnou konzolou pro ladění modulu Kudu](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
