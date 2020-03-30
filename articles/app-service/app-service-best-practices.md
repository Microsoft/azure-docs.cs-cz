---
title: Osvědčené postupy
description: Seznamte se s doporučenými postupy a běžnými scénáři řešení potíží pro vaši aplikaci spuštěnou ve službě Azure App Service.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: ded812d5d7a0440466e7284b56c90965ea00406e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768482"
---
# <a name="best-practices-for-azure-app-service"></a>Osvědčené postupy pro Azure App Service
Tento článek shrnuje osvědčené postupy pro používání [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a><a name="colocation"></a>Colocation
Když se prostředky Azure, které tvoří řešení, jako je webová aplikace a databáze, nacházejí v různých oblastech, může to mít následující účinky:

* Zvýšená latence v komunikaci mezi prostředky
* Peněžní poplatky za odchozí přenos dat mezi oblastmi, jak je uvedeno na [stránce s cenami Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Společné umístění ve stejné oblasti je nejvhodnější pro prostředky Azure, které tvoří řešení, jako je webová aplikace a databázový nebo úložný účet používaný k ukládání obsahu nebo dat. Při vytváření prostředků se ujistěte, že jsou ve stejné oblasti Azure, pokud nemáte konkrétní obchodní nebo návrh důvod pro ně nesmí být. Aplikaci Služby App Service můžete přesunout do stejné oblasti jako databáze pomocí [funkce klonování služby App Service,](app-service-web-app-cloning.md) která je aktuálně dostupná pro aplikace Premium App Service Plan.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>Když aplikace spotřebovávají více paměti, než bylo očekáváno
Když zjistíte, že aplikace spotřebovává více paměti, než bylo očekáváno, jak je uvedeno prostřednictvím doporučení pro monitorování nebo službu, zvažte [funkci automatického ozdravování služby App Service](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Jednou z možností funkce automatického retušování je vlastní akce založené na prahové hodnotě paměti. Akce pokrývají spektrum od e-mailových oznámení až po šetření prostřednictvím výpisu paměti až po zmírnění na místě recyklací pracovního procesu. Automatické vyléčení lze konfigurovat prostřednictvím web.config a prostřednictvím přátelského uživatelského rozhraní, jak je popsáno v tomto blogu pro [rozšíření webu podpory služby aplikace](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>Když aplikace spotřebovávají více procesoru, než se očekávalo
Když zjistíte, že aplikace spotřebovává více procesoru, než se očekávalo, nebo dojde k opakovaným špičkám procesoru, jak je uvedeno prostřednictvím doporučení pro monitorování nebo služby, zvažte horizontální navýšení kapacity nebo horizontální navýšení kapacity plánu služby App Service. Pokud je vaše aplikace stavové, škálování je jedinou možností, zatímco pokud je vaše aplikace bezstavové, horizontální navýšení kapacity poskytuje větší flexibilitu a vyšší škálování potenciál. 

Další informace o "stavové" vs "bezstavové" aplikace můžete sledovat toto video: [Plánování škálovatelné end-to-end vícevrstvé aplikace na Azure App Service](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Další informace o možnostech škálování a automatického škálování služby App Service najdete [v tématu Škálování webové aplikace ve službě Azure App Service](manage-scale-up.md).  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>Při vyčerpání prostředků soketu
Častým důvodem vyčerpání odchozích připojení TCP je použití klientských knihoven, které nejsou implementovány pro opakované použití připojení TCP, nebo pokud se nepoužívá protokol vyšší úrovně, například HTTP - Keep-Alive. Projděte si dokumentaci ke každé knihovně, na kterou odkazují aplikace v plánu služby App Service, a ujistěte se, že jsou nakonfigurované nebo přístupné ve vašem kódu pro efektivní opakované použití odchozích připojení. Také postupujte podle pokynů pro dokumentaci knihovny pro správné vytvoření a uvolnění nebo vyčištění, aby se zabránilo úniku připojení. Zatímco tyto klientské knihovny vyšetřování probíhají, dopad může být zmírněna škálování na více instancí.

### <a name="nodejs-and-outgoing-http-requests"></a>Soubor Node.js a odchozí požadavky http
Při práci s Node.js a mnoho odchozích http požadavků je důležité, aby se zabývalo http - Keep-Alive. Můžete použít [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` balíček usnadnit ve vašem kódu.

Vždy zpracovat `http` odpověď, i když neprovedete nic v obslužné rutině. Pokud nezpracovat odpověď správně, aplikace se zasekne nakonec, protože žádné další sokety jsou k dispozici.

Například při práci `http` s `https` nebo balíček:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Pokud používáte službu App Service na Linuxu v počítači s více jádry, dalším osvědčeným postupem je použití PM2 ke spuštění více procesů Node.js ke spuštění aplikace. Můžete to udělat zadáním příkazu pro spuštění do kontejneru.

Chcete-li například spustit čtyři instance:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>Když se zálohování aplikace spustí, dojde k selhání
Dva nejčastější důvody, proč se zálohování aplikace nezdaří, jsou: neplatná nastavení úložiště a neplatná konfigurace databáze. K těmto chybám obvykle dochází, když dojde ke změnám prostředků úložiště nebo databáze nebo ke změnám přístupu k těmto prostředkům (například pověření aktualizovaná pro databázi vybranou v nastavení zálohování). Zálohy se obvykle spouštějí podle plánu a vyžadují přístup k úložišti (pro odchozí zálohované soubory) a databázím (pro kopírování a čtení obsahu, které mají být zahrnuty do zálohy). Výsledkem selhání přístupu k některému z těchto prostředků by bylo konzistentní selhání zálohování. 

Pokud dojde k selhání zálohování, zkontrolujte nejnovější výsledky, abyste pochopili, ke kterému typu selhání dochází. Pokud chcete, aby se nepodařilo získat přístup k úložišti, zkontrolujte a aktualizujte nastavení úložiště použitá v konfiguraci zálohování. V případě selhání přístupu k databázi zkontrolujte a aktualizujte řetězce připojení jako součást nastavení aplikace. pak pokračujte v aktualizaci konfigurace zálohy tak, aby správně zahrnovala požadované databáze. Další informace o zálohování aplikací najdete [v tématu Zálohování webové aplikace ve službě Azure App Service](manage-backup.md).

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>Když se do služby Azure App Service nasazují nové aplikace Node.js
Výchozí konfigurace služby Azure App Service pro aplikace Node.js je určena tak, aby co nejlépe vyhovovala potřebám většiny běžných aplikací. Pokud by konfigurace aplikace Node.js měla prospěch z přizpůsobeného ladění za účelem zvýšení výkonu nebo optimalizace využití prostředků pro prostředky procesoru/paměti/sítě, přečtěte si [článek Doporučené postupy a průvodce odstraňováním potíží pro aplikace uzlů ve službě Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Tento článek popisuje nastavení iisnode, které budete muset nakonfigurovat pro aplikaci Node.js, popisuje různé scénáře nebo problémy, kterým vaše aplikace může čelit, a ukazuje, jak tyto problémy řešit.


## <a name="next-steps"></a>Další kroky
Další informace o doporučených postupech najdete v centru [aplikace Diagnostika služby App Service](https://docs.microsoft.com/azure/app-service/overview-diagnostics) a vyhledejte užitečné postupy, které jsou specifické pro váš prostředek.

- Přejděte do webové aplikace na [webu Azure Portal](https://portal.azure.com).
- Klikněte na **Diagnostika a řešení problémů** v levém navigačním panelu, který otevře Diagnostika služby App Service.
- Zvolte dlaždice domovské stránky **doporučené postupy.**
- Chcete-li zobrazit aktuální stav aplikace s ohledem na tyto osvědčené postupy, klepněte na tlačítko **Doporučené postupy pro dostupnost & výkonu** nebo doporučené postupy pro optimální **konfiguraci.**

Pomocí tohoto odkazu můžete také přímo otevřít diagnostiku `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`služby App Service pro váš prostředek: .