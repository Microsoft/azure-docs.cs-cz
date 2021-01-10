---
title: Node.js osvědčené postupy a řešení potíží
description: Seznamte se s osvědčenými postupy a kroky pro řešení potíží pro Node.js aplikace běžící v Azure App Service.
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 9763835142e66bbbce51cd5c863dff87f261c270
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060156"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Osvědčené postupy a Průvodce odstraňováním potíží pro aplikace uzlů v Azure App Service Windows

V tomto článku se seznámíte s osvědčenými postupy a kroky při řešení potíží pro [aplikace Windows Node.js](quickstart-nodejs.md?pivots=platform-windows) spuštěné v Azure App Service (s [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Při použití kroků pro řešení potíží na provozním webu buďte opatrní. Doporučujeme, abyste aplikaci nastavili na neprodukční instalaci, například na přípravném slotu a po vyřešení problému, zahodíte si pracovní slot k produkčnímu slotu.
>

## <a name="iisnode-configuration"></a>Konfigurace IISNODE

Tento [soubor schématu](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) zobrazuje všechna nastavení, která můžete konfigurovat pro iisnode. Některá nastavení, která jsou užitečná pro vaši aplikaci:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Toto nastavení řídí počet procesů uzlů, které se spustí v rámci aplikace IIS. Výchozí hodnota je 1. Můžete spustit tolik node.exes jako vCPU počet virtuálních počítačů, a to tak, že změníte hodnotu na 0. Doporučená hodnota je 0 pro většinu aplikací, takže můžete na svém počítači použít všechny vCPU. Node.exe jediným vláknem, takže jeden node.exe spotřebovává maximálně 1 vCPU. Chcete-li získat maximální výkon u vaší aplikace uzlu, budete chtít použít všechny vCPU.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Toto nastavení řídí cestu k node.exe. Tuto hodnotu můžete nastavit tak, aby odkazovala na verzi node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Toto nastavení určuje maximální počet souběžných požadavků, které iisnode odesílá každému node.exe. V Azure App Service je výchozí hodnota nekonečno. Tuto hodnotu můžete nakonfigurovat v závislosti na počtu požadavků, které vaše aplikace přijme, a na tom, jak rychle vaše aplikace zpracovává jednotlivé požadavky.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Toto nastavení určuje maximální počet pokusů, kolikrát iisnode pokusy o připojení k pojmenovanému kanálu, aby odesílaly požadavky na node.exe. Toto nastavení v kombinaci s namedPipeConnectionRetryDelay určuje celkový časový limit každého požadavku v rámci iisnode. Výchozí hodnota je 200 na Azure App Service. Celkový časový limit v sekundách = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Toto nastavení určuje dobu (v MS), po kterou iisnode počká mezi jednotlivými pokusy o odeslání žádosti o node.exe přes pojmenovaný kanál. Výchozí hodnota je 250 ms.
Celkový časový limit v sekundách = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

Ve výchozím nastavení je celkový časový limit v iisnode v Azure App Service 200 \* 250 MS = 50 sekund.

### <a name="logdirectory"></a>logDirectory

Toto nastavení řídí adresář, ve kterém se iisnode protokoly stdout/stderr. Výchozí hodnota je iisnode, která je relativní vzhledem k adresáři hlavního skriptu (adresář, ve kterém je k dispozici hlavní server.js).

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Toto nastavení určuje, jakou verzi iisnodea Node-Inspector používá při ladění aplikace uzlu. V současné době jsou pro toto nastavení jediným ze dvou platných hodnot iisnode-inspector-0.7.3.dll a iisnode-inspector.dll. Výchozí hodnota je iisnode-inspector-0.7.3.dll. Verze iisnode-inspector-0.7.3.dll používá Node-Inspector-0.7.3 a používá webové sokety. Povolte webové sokety ve službě Azure WebApp, abyste mohli používat tuto verzi. <https://ranjithblogs.azurewebsites.net/?p=98>Další podrobnosti o tom, jak nakonfigurovat iisnode pro použití nového nástroje Node-Inspector, najdete v tématu.

### <a name="flushresponse"></a>flushResponse

Výchozím chováním služby IIS je, že před vyprázdněním ukládá data odpovědí až do velikosti 4 MB, nebo do konce odpovědi, podle toho, co nastane dřív. iisnode nabízí nastavení konfigurace pro přepsání tohoto chování: Chcete-li vyprázdnit fragment textu entity odpovědi, jakmile ho iisnode obdrží od node.exe, je nutné nastavit iisnode/@flushResponse atribut v web.config na hodnotu ' true ':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Povolit vyprázdnění každého fragmentu textu entity odpovědi přidá režijní náklady na výkon, které snižují propustnost systému pomocí ~ 5% (na 0.1.13). Nejvhodnější je určit nastavení oboru pouze pro koncové body, které vyžadují streamování odpovědí (například pomocí `<location>` elementu v web.config)

Kromě toho je třeba pro streamování aplikací nastavit také responseBufferLimit své obslužné rutiny iisnode na hodnotu 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Středníkem oddělený seznam souborů, které jsou sledovány pro změny. Při jakékoli změně souboru dojde k recyklování aplikace. Každá položka se skládá z volitelného názvu adresáře a názvu souboru, který je relativní vzhledem k adresáři, kde se nachází hlavní vstupní bod aplikace. Zástupné znaky jsou povoleny pouze v části název souboru. Výchozí hodnotou je `*.js;iisnode.yml`.

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Výchozí hodnota je False. Pokud je povoleno, vaše aplikace uzlů se může připojit k pojmenovanému kanálu (proměnná prostředí IISNODE \_ řídicí \_ kanál) a odeslat zprávu "recyklovat". Tím dojde k řádnému recyklování W3wp.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Výchozí hodnota je 0, což znamená, že tato funkce je zakázaná. Pokud je hodnota nastavena na hodnotu větší než 0, iisnode vyhledá všechny své podřízené procesy každou "idlePageOutTimePeriod" v milisekundách. V [dokumentaci](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) se dozvíte, co znamená stránka. Toto nastavení je užitečné pro aplikace, které spotřebovávají vysoké množství paměti a chtějí občas vystavit paměť na disk, abyste uvolnili paměť RAM.

> [!WARNING]
> Při povolování následujících nastavení konfigurace v produkčních aplikacích buďte opatrní. Doporučení je nepovolujte u živých produkčních aplikací.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Výchozí hodnota je False. Pokud je nastavená hodnota true, iisnode přidá hlavičku odpovědi HTTP `iisnode-debug` do každé odpovědi HTTP, kterou pošle `iisnode-debug` . hodnota hlavičky je adresa URL. Jednotlivé části diagnostických informací lze získat tak, že si prohlížíte fragment adresy URL. vizualizace je však k dispozici otevřením adresy URL v prohlížeči.

### <a name="loggingenabled"></a>loggingEnabled

Toto nastavení řídí protokolování stdout a stderr pomocí iisnode. Iisnode zachycuje stdout/stderr z procesů, které spustí, a zapisuje je do adresáře zadaného v nastavení ' logDirectory '. Jakmile je tato možnost povolena, zapisuje aplikace do systému souborů protokoly a v závislosti na množství protokolování provedeném aplikací může dojít k důsledkům výkonu.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Výchozí hodnota je False. Když se nastaví na true, iisnode zobrazí stavový kód HTTP a kód chyby Win32 v prohlížeči. Kód Win32 je užitečný při ladění určitých typů problémů.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (Nepovolit pro živý produkční Web)

Toto nastavení řídí funkci ladění. Iisnode je integrován s nástrojem Node-Inspector. Povolením tohoto nastavení povolíte ladění aplikace uzlu. Po povolení tohoto nastavení iisnode vytvoří v adresáři debuggerVirtualDir soubory pro kontrolu uzlů v prvním požadavku na ladění pro vaši aplikaci Node. Můžete načíst kontrolora uzlu odesláním žádosti do `http://yoursite/server.js/debug` . Segment adresy URL pro ladění můžete řídit pomocí nastavení "debuggerPathSegment". Ve výchozím nastavení je to debuggerPathSegment = ' ladit '. Můžete nastavit `debuggerPathSegment` na identifikátor GUID, například tak, aby bylo více obtížné zjistit jiné.

Další podrobnosti o ladění najdete [v tématu ladění aplikací node.js v systému Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) .

## <a name="scenarios-and-recommendationstroubleshooting"></a>Scénáře a doporučení/řešení potíží

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Moje aplikace Node provádí nadměrné odchozí hovory

Mnohé aplikace by chtěli v rámci své běžné operace vytvořit odchozí připojení. Například když je požadavek v, vaše aplikace Node by chtěla kontaktovat REST API jinde a získat nějaké informace pro zpracování žádosti. Při provádění volání http nebo https byste chtěli použít agenta Keep Alive. Při provádění těchto odchozích volání můžete použít modul agentkeepalive jako svého agenta Keep Alive.

Modul agentkeepalive zajišťuje, že se na VIRTUÁLNÍm počítači Azure WebApp znovu používají sokety. Vytvořením nového soketu u každé odchozí žádosti přidáte do své aplikace režii. Když vaše aplikace znovu používá soket pro odchozí požadavky, zajistí, že vaše aplikace nebude přesáhnout maxSockets, které jsou přiděleny na virtuální počítač. Doporučením na Azure App Service je nastavení hodnoty maxSockets agentKeepAlive na celkem (4 instance node.exe \* 32 maxSockets/instance) 128 soketů na virtuální počítač.

Příklad konfigurace [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) :

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 32,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> V tomto příkladu se předpokládá, že ve vašem VIRTUÁLNÍm počítači běží 4 node.exe. Pokud je na virtuálním počítači spuštěný jiný počet node.exe, musíte odpovídajícím způsobem upravit nastavení maxSockets.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>Moje aplikace Node spotřebovává příliš mnoho CPU.

Můžete obdržet doporučení od Azure App Service na portálu o vysoké spotřebě procesoru. Můžete také nastavit monitory, které sledují určité [metriky](web-sites-monitor.md). Když kontrolujete využití CPU na [řídicím panelu Azure Portal](../azure-monitor/platform/metrics-charts.md), podívejte se na maximum hodnot CPU, abyste nemuseli přijít na nejvyšší hodnoty.
Pokud se domníváte, že vaše aplikace spotřebovává příliš mnoho CPU a nemůžete vysvětlit, proč, můžete profilovat aplikaci uzlu, abyste zjistili, jestli je.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Profilování aplikace Node na Azure App Service s využitím V8-Profiler

Řekněme například, že máte aplikaci Hello World, kterou chcete profilovat následujícím způsobem:

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

Přejít na web konzoly ladění `https://yoursite.scm.azurewebsites.net/DebugConsole`

Přejdete do adresáře site/Wwwroot. Zobrazí se příkazový řádek, jak je znázorněno v následujícím příkladu:

![Snímek obrazovky zobrazující adresář site/wwwroot a příkazový řádek.](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Spusťte příkaz `npm install v8-profiler`.

Tento příkaz nainstaluje V8-Profiler do \_ adresáře modulů uzlů a všech jeho závislostí.
Nyní upravte server.js pro profilaci vaší aplikace.

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

Předchozí profil kódu WriteConsoleLog funkci a potom zapíše výstup profilu do souboru Profile. cpuprofile v rámci lokality Wwwroot. Odešlete žádost do aplikace. Zobrazí se soubor Profile. cpuprofile vytvořený v rámci lokality Wwwroot.

![Snímek obrazovky, který zobrazuje soubor Profile. cpuprofile.](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Stáhněte si tento soubor a otevřete ho pomocí nástrojů Chrome F12. Stiskněte klávesu F12 na Chrome a pak zvolte kartu **profily** . Klikněte na tlačítko **načíst** . Vyberte soubor Profile. cpuprofile, který jste stáhli. Klikněte na profil, který jste právě načetli.

![Snímek obrazovky zobrazující soubor Profile. cpuprofile, který jste načetli.](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Pomocí funkce WriteConsoleLog můžete zjistit, že 95% času bylo spotřebováno. Výstup také zobrazuje přesná čísla řádků a zdrojové soubory, které tento problém způsobily.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Aplikace můj uzel spotřebovává příliš mnoho paměti.

Pokud vaše aplikace spotřebovává příliš mnoho paměti, zobrazí se na portálu oznámení o vysoké spotřebě paměti Azure App Service na portálu. Můžete nastavit monitory, které sledují určité [metriky](web-sites-monitor.md). Když kontrolujete využití paměti na [řídicím panelu Azure Portal](../azure-monitor/platform/metrics-charts.md), zkontrolujte maximální hodnoty paměti, abyste nemuseli přijít na nejvyšší hodnoty.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Detekce nevracení a rozdíl haldy pro node.js

Můžete použít [Node-memwatch](https://github.com/lloyd/node-memwatch) , které vám pomůžou identifikovat nevracení paměti.
Můžete nainstalovat `memwatch` stejně jako V8-Profiler a upravit kód pro zachycení a rozdílové haldy k identifikaci nevracení paměti ve vaší aplikaci.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Moje node.exe se náhodně ukončily.

Existuje několik důvodů, proč se node.exe náhodně vypíná:

1. Vaše aplikace vyvolala nevyzachycené výjimky – ověřte, zda \\ \\ \\logging-errors.txt soubor aplikace Home LogFiles \\ pro podrobnosti o vyvolané výjimce. Tento soubor má trasování zásobníku, které vám umožní ladit a opravovat vaši aplikaci.
2. Vaše aplikace spotřebovává příliš mnoho paměti, což má vliv na další procesy od začátku. Pokud je celková paměť virtuálních počítačů blízko 100%, mohl by být node.exe ukončen správcem procesů. Správce procesů ukončuje některé procesy, aby bylo možné jiné procesy získat možnost udělat si nějakou práci. Chcete-li tento problém vyřešit, profilujte aplikaci pro nevracení paměti. Pokud vaše aplikace vyžaduje velké množství paměti, narůstá kapacitu na větší virtuální počítač (což zvyšuje velikost paměti RAM dostupné pro virtuální počítač).

### <a name="my-node-application-does-not-start"></a>Moje aplikace uzlu se nespustí

Pokud aplikace při spuštění vrací chyby 500, může to být několik důvodů:

1. Node.exe není k dispozici ve správném umístění. Ověřte nastavení nodeProcessCommandLine.
2. Hlavní soubor skriptu se nenachází ve správném umístění. Zaškrtněte web.config a ujistěte se, že název hlavního souboru skriptu v oddílu obslužné rutiny odpovídá hlavnímu souboru skriptu.
3. Konfigurace Web.config není správná – ověřte názvy a hodnoty nastavení.
4. Studená Start – spuštění vaší aplikace trvá příliš dlouho. Pokud vaše aplikace trvá déle než (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000 sekund, iisnode vrátí chybu 500. Zvyšte hodnoty těchto nastavení tak, aby odpovídaly času spuštění vaší aplikace, aby nedošlo k vypršení časového limitu iisnode a vrácení chyby 500.

### <a name="my-node-application-crashed"></a>V mé aplikaci uzlu došlo k chybě.

Vaše aplikace vyvolává nezachycené výjimky – kontrolní `d:\\home\\LogFiles\\Application\\logging-errors.txt` soubor pro podrobnosti o vyvolané výjimce. Tento soubor obsahuje trasování zásobníku, které vám může pomoct diagnostikovat a opravit vaši aplikaci.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Spuštění aplikace na mém uzlu trvá příliš dlouho (studený start)

Běžnou příčinou dlouhých časů spuštění aplikace je vysoký počet souborů v \_ modulech uzlů. Aplikace se pokusí načíst většinu těchto souborů při spuštění. Ve výchozím nastavení, protože soubory jsou uloženy ve sdílené síťové složce v Azure App Service, načítání mnoha souborů může trvat déle.
Některá řešení pro zajištění rychlejšího tohoto procesu:

1. Pokuste se o opožděné načtení \_ modulů uzlů a nenačte všechny moduly při spuštění aplikace. Do modulů opožděného načtení by volání vyžadovat (' Module ') mělo být provedeno, pokud skutečně potřebujete modul v rámci funkce před prvním spuštěním kódu modulu.
2. Azure App Service nabízí funkci s názvem místní mezipaměť. Tato funkce zkopíruje obsah ze sdílené síťové složky na místní disk ve VIRTUÁLNÍm počítači. Vzhledem k tomu, že jsou soubory místní, je doba načítání \_ modulů uzlů mnohem rychlejší.

## <a name="iisnode-http-status-and-substatus"></a>Stav IISNODE http a dílčí stav

`cnodeconstants` [Zdrojový soubor](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) obsahuje seznam všech možných kombinací stav/dílčí stav, které iisnode může vrátit z důvodu chyby.

Povolte FREB pro vaši aplikaci, aby se zobrazil kód chyby Win32 (je třeba povolit FREB pouze v neprodukčních lokalitách z důvodů výkonu).

| Stav http | Podřízený stav http | Možný důvod? |
| --- | --- | --- |
| 500 |1000 |Při odesílání požadavku do IISNODE došlo k nějakému problému – ověřte, jestli node.exe spuštěný. Při spuštění došlo k chybě Node.exe. Ověřte chyby v konfiguraci web.config. |
| 500 |1001 |-Win32Error 0x2-aplikace nereaguje na adresu URL. Ověřte pravidla pro přepis adres URL nebo ověřte, jestli má aplikace Express definované správné trasy. -Win32Error 0x6d – pojmenovaný kanál je zaneprázdněný – Node.exe nepřijímá požadavky, protože kanál je zaneprázdněný. Ověřte vysoké využití procesoru. – Jiné chyby – ověřte, zda node.exe selhat. |
| 500 |1002 |Node.exe došlo k chybě – \\ \\ \\ pro trasování zásobníku ověřtelogging-errors.txt domovské soubory. |
| 500 |1003 |Problém s konfigurací kanálu – konfigurace pojmenovaného kanálu je nesprávná. |
| 500 |1004-1018 |Při odesílání požadavku nebo zpracování odpovědi do nebo z node.exe došlo k chybě. Ověřte, zda node.exe došlo k chybě. \\ \\ \\ pro trasování zásobníku ověřtelogging-errors.txt domovské soubory. |
| 503 |1000 |Pro přidělení více pojmenovaných připojení kanálu není dostatek paměti. Podívejte se, proč vaše aplikace spotřebovává spoustu paměti. Ověřte hodnotu nastavení maxConcurrentRequestsPerProcess. Pokud není nekonečné a máte hodně požadavků, zvyšte tuto hodnotu, aby se zabránilo této chybě. |
| 503 |1001 |Požadavek nebylo možné odeslat do node.exe, protože aplikace se recykluje. Po recyklaci aplikace by měly být požadavky obsluhovány normálně. |
| 503 |1002 |Ověřte kód chyby Win32 ze skutečného důvodu – požadavek nebylo možné odeslat do node.exe. |
| 503 |1003 |Pojmenovaný kanál je moc zaneprázdněný – ověřte, jestli node.exe spotřebovává nadměrný procesor. |

NODE.exe má nastavení s názvem `NODE_PENDING_PIPE_INSTANCES` . V Azure App Service je tato hodnota nastavená na 5000. To znamená, že node.exe může přijmout 5000 požadavků v čase u pojmenovaného kanálu. Tato hodnota by měla být dostatečná pro většinu aplikací uzlů běžících na Azure App Service. Nemělo by se zobrazovat 503,1003 Azure App Service v důsledku vysoké hodnoty pro `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Další zdroje informací

Pomocí těchto odkazů se dozvíte více o node.js aplikacích v Azure App Service.

* [Začínáme s webovými aplikacemi Node.js ve službě Azure App Service](quickstart-nodejs.md)
* [Postup ladění webové aplikace Node.js ve službě Azure App Service](/archive/blogs/azureossds/debugging-node-js-apps-on-azure-app-services)
* [Používání modulů Node.js s aplikacemi Azure](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](/archive/blogs/silverlining/windows-azure-websites-node-js)
* [Středisko pro vývojáře Node.js](../nodejs-use-node-modules-azure-apps.md)
* [Seznámení se supertajnou konzolou pro ladění modulu Kudu](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
