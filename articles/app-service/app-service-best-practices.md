---
title: Osvědčené postupy pro Azure App Service
description: Zjistěte, osvědčené postupy a řešení problémů pro službu Azure App Service.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 7c5eb6190d4a4cdfa47779d2c4d7aadac5a2fb80
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2018
ms.locfileid: "27868314"
---
# <a name="best-practices-for-azure-app-service"></a>Osvědčené postupy pro Azure App Service
Tento článek shrnuje doporučené postupy pro používání [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Společné umístění
Při sestavování řešení například webovou aplikaci a databázi prostředků Azure jsou umístěny v různých oblastech, může mít následující důsledky:

* Zvýší latence při komunikaci mezi prostředky
* Peněžní poplatky za odchozí data přenos mezi oblastmi uvedených na [Azure stránce s cenami](https://azure.microsoft.com/pricing/details/data-transfers).

Společné umístění ve stejné oblasti je nejvhodnější pro sestavování řešení například webovou aplikaci a účet databáze nebo úložiště používané pro udržení dat nebo obsahu prostředků Azure. Při vytváření prostředků, ujistěte se, že jsou ve stejné oblasti Azure, pokud máte konkrétní obchodní nebo návrh důvod pro ně nejsou. Aplikace služby App Service můžete přesunout do stejné oblasti jako vaše databáze pomocí [služby App Service funkce klonování](app-service-web-app-cloning.md) aktuálně dostupné pro plán aplikační služby Premium aplikace.   

## <a name="memoryresources"></a>Pokud aplikace vyžaduje více paměti, než se očekávalo
Pokud si všimnete aplikace využívá více paměti, než se očekávalo jako indikován monitorování nebo službu doporučení, zvažte [aplikace služby Automatické opravy funkce](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Jednu z možností pro funkci Automatické opravy trvá vlastní akce podle prahové hodnoty paměti. Akce span spektra z e-mailová oznámení k vyšetřování prostřednictvím výpis stavu paměti pro zmírnění dopadů na místě ve recykluje pracovní proces. Automatické opravy lze nakonfigurovat pomocí souboru web.config a prostřednictvím popisný uživatelské rozhraní dle instrukcí v tomto příspěvku na blogu pro [rozšíření lokality podporu služby aplikace](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Když aplikace spotřebovávat více procesorů, než se očekávalo
Když si všimnete, aplikace využívá více procesorů, než se očekávalo, nebo opakovaných špičky procesoru prostředí, které je uvedené prostřednictvím monitorování nebo službu doporučení, zvažte vertikálním navýšení kapacity nebo škálování plán služby App Service. Pokud vaše aplikace stavová, vertikálním navýšení kapacity je jedinou možností, zatímco, pokud je vaše aplikace bezstavové, škálování na více systémů poskytuje větší flexibilitu a vyšší potenciální škálování. 

Další informace o "bezstavové" aplikace "Stavová" vs můžete přehrát toto video: [plánování škálovatelné začátku do konce vícevrstvé aplikace na webové aplikace Microsoft Azure](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Další informace o možnostech škálování a automatické škálování služby App Service najdete v tématu [škálování webové aplikace v Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>Když vyčerpání prostředků soketů
Obvyklým důvodem vyčerpává odchozí připojení TCP je použití klientské knihovny, které nejsou implementované znovu použít připojení TCP, nebo když není použije vyšší úrovně protokolu, jako je například HTTP - Keep-Alive. Dokumentaci pro každou z knihoven odkazuje aplikace, které váš plán služby App Service k zajištění jejich nakonfigurován nebo získat přístup v kódu pro efektivní opakované použití odchozí připojení. Také podle pokynů uvedených knihovna dokumentace pro správné vytvoření a vydání nebo čištění, aby se zabránilo úniku připojení. Tato vyšetřování knihovny klienta jsou v průběhu, může být zmírnit dopad škálování na více instancí.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js a odchozí požadavky http
Při práci s Node.js a mnoho odchozí požadavky http, který se zabývá udržování připojení HTTP - je důležité. Můžete použít [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` balíčku, aby bylo snazší v kódu.

Vždy zpracování `http` odpověď, a to i v případě, že jste nedělat nic v obslužné rutině. Pokud nemáte zpracovat odpověď správně, nebudou aplikace, nakonec vázne, protože nejsou k dispozici žádné další sokety.

Například při práci s `http` nebo `https` balíčku:

```
var request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Pokud v App Service v systému Linux běží na počítači s více jader, jiné osvědčeným postupem je použít PM2 ke spuštění více procesů Node.js spuštění vaší aplikace. Můžete provést zadáním spuštění příkazu, který bude vaše kontejneru.

Chcete-li například spustit čtyři instancí:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Pokud vaše aplikace Zálohování spustí selhání
Dva nejčastější příčiny, proč se nezdaří zálohování aplikace: úložiště nejsou platné nastavení a konfiguraci databáze je neplatný. Tyto chyby obvykle dojít, když jsou změny zdrojů úložiště nebo databáze nebo změny jak získat přístup k tyto prostředky (například pověření aktualizované vybraná v nastavení zálohování databáze). Zálohování obvykle spouštět podle plánu a vyžadují přístup k úložišti (pro výstup vytvořeného zálohované soubory) a databází (pro kopírování a načítání obsahu, které mají být zahrnuty do zálohování). Výsledek neúspěšně snaží o přístup buď tyto prostředky by chyby konzistentní zálohování. 

Při stát selhání zálohování zkontrolujte nejnovější výsledky pochopit, jaký typ selhání se děje. Pro přístup k selhání úložiště zkontrolujte a aktualizujte nastavení úložiště používané v konfiguraci zálohování. Selhání přístupu k databázi zkontrolovat a aktualizovat vaše připojení řetězce jako součást nastavení aplikace; přejděte k aktualizaci konfigurace zálohování správně zahrnout požadované databází. Další informace o aplikaci Zálohování, naleznete v části [zálohování webové aplikace ve službě Azure App Service](web-sites-backup.md).

## <a name="nodejs"></a>Když se nasadí nové aplikace Node.js do Azure App Service
Výchozí konfigurace Azure App Service pro aplikace Node.js se má nejlépe vyhovovat potřebám nejčastěji používané aplikace. Pokud konfigurace pro aplikaci Node.js by těžit z přizpůsobené ladění zlepšit výkon nebo optimalizovat využití prostředků procesoru a paměti nebo síťových prostředků, najdete v článku [osvědčených postupů a Průvodci odstraňováním potíží uzlu aplikace v aplikaci Azure. Služba](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Tento článek popisuje nastavení modulu iisnode možná muset nakonfigurovat pro vaši aplikaci Node.js, popisuje různé scénáře nebo že vaše aplikace může být čelí a ukazuje, jak tyto problémy řeší problémy.

