---
title: Osvědčené postupy pro Azure App Service
description: Přečtěte si osvědčené postupy a řešení potíží pro službu Azure App Service.
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
ms.openlocfilehash: ff79680c00a394790ba56db9ab4b237f06c677e5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240626"
---
# <a name="best-practices-for-azure-app-service"></a>Osvědčené postupy pro Azure App Service
Tento článek shrnuje osvědčené postupy při používání [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Společné umístění
Při sestavování řešení, třeba webovou aplikaci a databázi Azure prostředky jsou umístěné v různých oblastech, může mít tyto důsledky:

* Zvýší latence při komunikaci mezi prostředky
* Peněžní poplatky za odchozí data přenášet mezi různými oblastmi, jak je uvedeno na [stránku s cenami Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Společné umístění ve stejné oblasti je nejvhodnější pro sestavování řešení, třeba webové aplikace a účet databáze nebo úložiště používané pro udržení obsah nebo dat prostředků Azure. Při vytváření prostředků, ujistěte se, že jsou ve stejné oblasti Azure, pokud máte konkrétní obchodní nebo návrh je neměl by se důvod. Aplikace služby App Service můžete přesunout do stejné oblasti jako databáze s použitím [služby App Service funkce klonování](app-service-web-app-cloning.md) aktuálně k dispozici pro plán App Service Premium aplikace.   

## <a name="memoryresources"></a>Když aplikace spotřebovávat více paměti, než se čekalo
Pokud si všimnete aplikace využívá více paměti, než se očekávalo jako indikován při monitorování nebo služba doporučení, vezměte v úvahu [funkce App Service Samoopravení](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Jednu z možností pro funkci automatického opravení trvá vlastní akce podle prahové hodnoty paměti. Akce span spektrum od e-mailová oznámení na šetření přes výpis paměti pro zmírnění dopadů na místě pomocí recyklaci pracovního procesu. Automatické opravy lze konfigurovat pomocí souboru web.config a prostřednictvím popisný uživatelské rozhraní jak je popsáno v blogovém příspěvku pro [rozšíření webu podpory App Service](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Když aplikace spotřebovávat více procesorů, než se čekalo
Pokud si všimnete aplikace využívá více procesorů, než se očekávalo nebo prostředí pro opakované využití procesoru špičky indikován při monitorování nebo služba doporučení, zvažte vertikální navýšení kapacity nebo horizontální navýšení kapacity plánu služby App Service. Pokud vaše aplikace je stavový, vertikální navýšení kapacity je jedinou možností, a pokud je vaše aplikace bezstavové, škálování na více instancí přináší větší flexibilitu a vyšší riziko škálování. 

Další informace o vs "stavové" "" bezstavové můžete sledovat toto video: [plánování škálovatelné začátku do konce vícevrstvou aplikaci v Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Další informace o možnosti škálování a automatické škálování služby App Service najdete v tématu [škálování webové aplikace ve službě Azure App Service](web-sites-scale.md).  

## <a name="socketresources"></a>Když je vyčerpání prostředků soketu
Častým důvodem, proč spotřebovává odchozí připojení TCP je použití klientské knihovny, které nejsou implementovány pro opětovné použití připojení TCP, nebo když se nepoužívá protokol vyšší úrovně jako je například udržování připojení HTTP. Projděte si dokumentaci pro každou z knihoven odkazuje aplikace v plánu App Service jsou nakonfigurované nebo přístupné z kódu pro efektivní opakované použití odchozí připojení. Také pomocí pokynů knihovny dokumentace pro správné vytvoření a vydání nebo čištění, aby předešla úniku připojení. Během vyšetřování klientské knihovny jsou v průběhu, může být zmírnit dopad horizontální navýšení kapacity na několik instancí.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js a odchozí požadavky http
Při práci s využitím Node.js a mnoho odchozích požadavků http, je důležité se zabývá HTTP - Keep-Alive. Můžete použít [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` balíčku, aby bylo snazší ve vašem kódu.

Vždy zpracovat `http` odpověď, a to i když neprovedete žádnou akci v obslužné rutině. Pokud nechcete zpracovat odpověď správně, nebudou aplikace, nakonec zasekne, protože nejsou k dispozici žádné další sokety.

Například při práci s `http` nebo `https` balíčku:

```
var request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Pokud používáte ve službě App Service v Linuxu na počítači s více jádry, jiné osvědčeným postupem je použít PM2 ke spuštění více procesům Node.js ke spuštění vaší aplikace. Provedete to tak, že zadáte spouštěcí příkaz do kontejneru.

Chcete-li například spustit čtyři instancí:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Při zálohování vaší aplikace spustí služeb při selhání
Dva nejběžnější důvody, proč jsou selhání zálohování aplikace: neplatného nastavení úložiště a konfigurace databáze je neplatná. Tyto chyby se obvykle stát, pokud se změny prostředky úložiště nebo databáze nebo změny, jak získat přístup k těmto prostředkům (například přihlašovací údaje aktualizované vybrané v nastavení zálohování databáze). Zálohování obvykle spouštět podle plánu a vyžadují přístup k úložištím (k výstupu zálohované soubory) a databáze (pro kopírování a čtení obsahu, které mají být zahrnuty do zálohování). Výsledek služeb při selhání pro přístup k některé z těchto prostředků by selhání konzistentní zálohování. 

Když dochází k chybám zálohování, projděte si nejnovější výsledky, abyste pochopili, jaký typ selhání se děje. Pro selhání přístupu k úložišti zkontrolovat a aktualizovat nastavení úložiště používá v konfiguraci zálohování. Pro selhání přístupu k databázi zkontrolovat a aktualizovat vaše řetězce připojení jako součást nastavení aplikace; přejděte k aktualizovat konfiguraci zálohování tak, aby správně zahrnují požadované databáze. Další informace o zálohování aplikace najdete v tématu [zálohování webové aplikace ve službě Azure App Service](web-sites-backup.md).

## <a name="nodejs"></a>Když jsou nasazená nové aplikace Node.js do služby Azure App Service
Výchozí konfiguraci služby Azure App Service pro aplikace v Node.js se má nejlépe vyhovovat potřebám nejčastěji používané aplikace. Pokud se konfigurace pro aplikace v Node.js, budou těžit z přizpůsobené optimalizace pro zlepšení výkonu nebo optimalizovat využití prostředků pro prostředky procesoru/paměti/sítě naleznete v tématu [osvědčené postupy a Průvodce odstraňováním potíží pro aplikace v Node.js v aplikaci Azure Služba](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Tento článek popisuje nastavení modulu iisnode možná muset nakonfigurovat pro vaši aplikaci Node.js, popisuje různé scénáře nebo problémy, že vaše aplikace se potýkáte a ukazuje, jak řešení těchto problémů.

