---
title: Osvědčené postupy – Azure App Service
description: Naučte se osvědčené postupy a řešení potíží s Azure App Service.
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
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: b4c7524415865f7db5d4514c14f8e45030620330
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636679"
---
# <a name="best-practices-for-azure-app-service"></a>Osvědčené postupy pro Azure App Service
Tento článek shrnuje osvědčené postupy pro používání [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Společné umístění
Když jsou prostředky Azure, které vytváří řešení, jako je webová aplikace a databáze, umístěné v různých oblastech, může to mít následující důsledky:

* Vyšší latence při komunikaci mezi prostředky
* Peněžní poplatky za přenos odchozích dat mezi oblastmi, jak je uvedeno na [stránce s cenami Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Společné umístění ve stejné oblasti je nejvhodnější pro prostředky Azure, které tvoří řešení, jako je webová aplikace a databáze nebo účet úložiště, který slouží k ukládání obsahu nebo dat. Při vytváření prostředků se ujistěte, že jsou ve stejné oblasti Azure, pokud nemáte konkrétní obchodní nebo návrhový důvod pro tyto účely. Aplikaci App Service můžete přesunout do stejné oblasti, ve které je vaše databáze, pomocí [funkce klonování App Service](app-service-web-app-cloning.md) , která je aktuálně dostupná pro aplikace Premium App Service Plan.   

## <a name="memoryresources"></a>Když aplikace spotřebovávají více paměti, než se očekávalo
Pokud si všimnete, že aplikace spotřebovává více paměti, než se očekávalo, jak je uvedeno v doporučeních pro monitorování nebo služby, zapamatujte si [funkci App Service automatické](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)opravy. Jedna z možností funkce Automatické opravy přijímá vlastní akce založené na prahové hodnotě paměti. Akce vychází z e-mailových oznámení do šetření prostřednictvím výpisu paměti při zmírnění omezení na místě tím, že se pracovní proces recykluje. Automatické opravy se dají nakonfigurovat přes web. config a prostřednictvím popisného uživatelského rozhraní popsaného v tomto příspěvku blogu pro [rozšíření webu podpory App Service](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Když aplikace spotřebovávají více PROCESORů, než se očekávalo
Když si všimnete, že aplikace spotřebovává více PROCESORů, než se očekávala, nebo se pokusíte o opakované využití procesoru, jak je uvedeno v doporučení týkající se monitorování nebo služby, zvažte možnost škálovat škálování nebo škálovat App Service plánu. Pokud je vaše aplikace stavová, je jedinou možností horizontální navýšení kapacity, pokud je vaše aplikace Bezstavová, nabízí větší flexibilitu a vyšší škálovatelnost. 

Další informace o stavových a bezstavových aplikacích můžete sledovat v tomto videu: [Plánování škálovatelné komplexní aplikace na více úrovních v Azure App Service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Další informace o možnostech škálování App Service a automatického škálování najdete v tématu [škálování webové aplikace v Azure App Service](manage-scale-up.md).  

## <a name="socketresources"></a>Při vyčerpání prostředků soketu
Běžným důvodem pro vyčerpání odchozích připojení TCP je použití klientských knihoven, které nejsou implementované k opakovanému použití připojení TCP, nebo když se nepoužívá protokol vyšší úrovně, jako je třeba HTTP-Keep-Alive. Přečtěte si dokumentaci ke každé knihovně, na kterou odkazují aplikace v plánu App Service, abyste se ujistili, že jsou ve vašem kódu nakonfigurované nebo používané pro efektivní opakované použití odchozích připojení. Dále postupujte podle pokynů v dokumentaci ke knihovně pro správné vytvoření a vystavení nebo vyčištění, abyste zabránili nevracení připojení. I když tyto klientské knihovny probíhá šetření, dopad může být omezen škálováním na více instancí.

### <a name="nodejs-and-outgoing-http-requests"></a>Node. js a odchozí požadavky http
Při práci s Node. js a mnoho odchozích požadavků HTTP, které je potřeba řešit pomocí protokolu HTTP-Keep-Alive, je důležité. Pomocí balíčku [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` můžete usnadnit práci s vaším kódem.

Vždy zpracujte `http` odpověď, i když neuděláte nic v obslužné rutině. Pokud odpověď nezpracujete správně, vaše aplikace se zablokuje, protože už nejsou k dispozici žádné další sokety.

Například při práci s `http` balíčkem nebo: `https`

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Pokud používáte App Service v systému Linux na počítači s více jádry, další osvědčeným postupem je použití konfiguračního PM2 ke spuštění více procesů Node. js pro spuštění aplikace. Můžete to provést zadáním spouštěcího příkazu do kontejneru.

Pokud například chcete spustit čtyři instance:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Když se spustí zálohování vaší aplikace jako neúspěšné
Mezi nejčastější důvody, proč se zálohování aplikace nezdařily, patří neplatná nastavení úložiště a neplatná konfigurace databáze. K těmto selháním obvykle dochází, když dojde k změnám v úložišti nebo databázových prostředcích nebo při změnách, jak získat přístup k těmto prostředkům (například přihlašovací údaje aktualizované pro databázi vybranou v nastavení zálohování). Zálohy obvykle běží podle plánu a vyžadují přístup k úložišti (pro výstup zálohovaných souborů) a databází (pro kopírování a čtení obsahu, který se má zahrnout do zálohování). Výsledek selhání přístupu k některému z těchto prostředků by představoval konzistentní selhání zálohování. 

Pokud dojde k selhání zálohování, Projděte si nejnovější výsledky, abyste zjistili, jaký typ selhání se děje. V případě selhání přístupu k úložišti zkontrolujte a aktualizujte nastavení úložiště použité v konfiguraci zálohování. V případě selhání přístupu k databázi zkontrolujte a aktualizujte řetězce připojení jako součást nastavení aplikace. pak pokračujte v aktualizaci konfigurace zálohování tak, aby správně zahrnovala požadované databáze. Další informace o zálohování aplikací najdete v tématu [zálohování webové aplikace v Azure App Service](manage-backup.md).

## <a name="nodejs"></a>Když jsou nové aplikace Node. js nasazeny na Azure App Service
Azure App Service výchozí konfigurace pro aplikace Node. js má za cíl nejlépe vyhovovat potřebám většiny běžných aplikací. Pokud by konfigurace aplikace Node. js mohla těžit z přizpůsobeného ladění za účelem zlepšení výkonu nebo optimalizace využití prostředků procesoru, paměti nebo síťových prostředků, přečtěte si téma [osvědčené postupy a Průvodce odstraňováním potíží pro aplikace uzlů v Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Tento článek popisuje nastavení iisnode, která může být potřeba nakonfigurovat pro aplikaci Node. js, popisuje různé scénáře nebo problémy, na které vaše aplikace může být vystavena, a ukazuje, jak tyto problémy vyřešit.

