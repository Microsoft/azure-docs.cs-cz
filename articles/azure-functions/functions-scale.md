---
title: Hostování a škálování Azure Functions
description: Naučte se vybírat mezi plánem Azure Functions spotřebu a plánem Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c5dd703851054b058d96440a3a994b9d10eecfa3
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372659"
---
# <a name="azure-functions-scale-and-hosting"></a>Hostování a škálování Azure Functions

Když vytvoříte aplikaci Function App v Azure, musíte zvolit plán hostování pro vaši aplikaci. K dispozici jsou tři základní plány hostování pro Azure Functions: [plán spotřeby](#consumption-plan), [Plán Premium](#premium-plan)a [vyhrazený plán (App Service)](#app-service-plan). Všechny plány hostování jsou obecně dostupné (GA) na virtuálních počítačích se systémy Linux a Windows.

Plán hostování, který zvolíte, bude určovat následující chování:

* Jak se škáluje aplikace Function App
* Prostředky dostupné pro jednotlivé instance aplikace Function App.
* Podpora pro pokročilé funkce, jako je například připojení k Azure Virtual Network.

Plány spotřeby i úrovně Premium automaticky přidávají výpočetní výkon, když je váš kód spuštěný. Vaše aplikace se škáluje v případě potřeby na zpracování zatížení a při změně kapacity v době, kdy se kód přestane spouštět. Pro plán spotřeby také nemusíte platit za nečinné virtuální počítače nebo rezervovat kapacitu předem.  

Plán Premium poskytuje další funkce, jako jsou výpočetní instance Premium, schopnost udržet instance na neomezenou dobu a připojení k virtuální síti.

App Service plán vám umožní využít výhod vyhrazené infrastruktury, kterou spravujete. Vaše aplikace Function App se škáluje na základě událostí, což znamená, že se nikdy neškáluje na nulu. (Vyžaduje, aby byla povolena možnost [vždy](#always-on) zapnuto.)

Podrobné porovnání různých plánů hostování (včetně hostování založeného na Kubernetes) naleznete v [části Porovnání plánů hostování](#hosting-plans-comparison).

## <a name="consumption-plan"></a>Plán Consumption

Pokud používáte plán spotřeby, instance Azure Functions hostitele se dynamicky přidávají a odstraňují na základě počtu příchozích událostí. Tento bezserverový plán se automaticky škáluje a výpočetní prostředky se vám účtují, jenom když vaše funkce běží. V plánu Spotřeba po nastavené době vyprší časový limit spuštění funkce.

Fakturace vychází z počtu spuštění, doby spuštění a použité paměti. Použití je agregované napříč všemi funkcemi v rámci aplikace Function App. Další informace najdete na stránce s [cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Plán spotřeby je výchozím plánem hostování a nabízí následující výhody:

* Plaťte jenom v případě, že jsou vaše funkce spuštěné.
* Horizontální navýšení kapacity i během období vysokého zatížení

Aplikace Function App ve stejné oblasti se dají přiřadit ke stejnému plánu spotřeby. Neexistuje žádný nevýhodou ani dopad na to, aby ve stejném plánu spotřeby běželo víc aplikací. Přiřazení více aplikací ke stejnému plánu spotřeby nemá žádný vliv na odolnost, škálovatelnost nebo spolehlivost každé aplikace.

Další informace o odhadu nákladů při spuštění v plánu spotřeby najdete v tématu [porozumění nákladům na plán spotřeby](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Plán Premium

Pokud používáte plán Premium, instance Azure Functions hostitele se přidají a odeberou na základě počtu příchozích událostí stejně jako u plánu spotřeby.  Plán Premium podporuje následující funkce:

* Trvalé zahřívání instancí, aby nedocházelo k žádnému studenému startu
* Připojení virtuální sítě
* Neomezené trvání spuštění (zaručené 60 minut)
* Velikosti instancí Premium (jedna jádro, dvě jádro a čtyři základní instance)
* Předvídatelné ceny
* Přidělování aplikací s vysokou hustotou pro plány s více aplikacemi Function App

Informace o tom, jak můžete vytvořit aplikaci Function App v plánu Premium, najdete v tématu [plán Azure Functions Premium](functions-premium-plan.md).

Fakturace plánu Premium vychází z počtu základních sekund a paměti přidělených napříč instancemi místo fakturace za spuštění a využité paměti.  S plánem Premium se neúčtují žádné poplatky za spuštění. Nejméně jedna instance musí být každému plánu vždy přidělena. Výsledkem je minimální měsíční cena za aktivní plán bez ohledu na to, jestli je funkce aktivní nebo nečinná. Mějte na paměti, že všechny aplikace Function App v plánu Premium sdílí přidělené instance.

Vezměte v úvahu plán Azure Functions Premium v následujících situacích:

* Vaše aplikace Function App běží nepřetržitě nebo téměř nepřetržitě.
* Máte vysoký počet malých spuštění a máte vysoké náklady na spuštění, ale v plánu spotřeby se účtují za méně GB.
* Budete potřebovat více možností procesoru nebo paměti, než jaké je k dispozici v plánu spotřeby.
* Váš kód musí běžet delší dobu, než je [Maximální doba spuštění](#timeout) v plánu spotřeby.
* Vyžadujete funkce, které jsou k dispozici pouze v plánu Premium, například připojení k virtuální síti. 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Vyhrazený plán (App Service)

Aplikace Function App můžete spustit taky na stejných vyhrazených virtuálních počítačích, jako jsou jiné App Service aplikace (Basic, Standard, Premium a izolované SKU).

Vezměte v úvahu App Service plán v následujících situacích:

* Máte existující a nevyužité virtuální počítače, které již spouštějí jiné instance App Service.
* Chcete zadat vlastní image, na které se mají spouštět vaše funkce.

Totéž platí pro aplikace Function App v plánu App Service, stejně jako u jiných prostředků App Service, jako jsou například webové aplikace. Podrobnosti o tom, jak plán App Service funguje, najdete v podrobném [přehledu Azure App Service plány](../app-service/overview-hosting-plans.md).

Pomocí App Serviceho plánu můžete ruční horizontální navýšení kapacity přidáním dalších instancí virtuálních počítačů. Můžete také povolit automatické škálování, i když automatické škálování bude pomalejší než Elastické škálování plánu Premium. Další informace najdete v tématu [Ruční nebo automatické škálování počtu instancí](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Horizontální navýšení kapacity můžete také škálovat tak, že vyberete jiný plán App Service. Další informace najdete v tématu [horizontální navýšení kapacity aplikace v Azure](../app-service/manage-scale-up.md). 

Při spouštění funkcí JavaScriptu v plánu App Service byste měli zvolit plán, který má méně vCPU. Další informace najdete v tématu [Výběr plánů App Service s jedním jádrem](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

Spuštění v [App Service Environment](../app-service/environment/intro.md) (pomocného programu) umožňuje plně izolovat vaše funkce a využívat větší počet instancí než plán App Service.

### <a name="always-on"></a><a name="always-on"></a> Vždy zapnuto

Pokud spustíte v plánu App Service, měli byste povolit nastavení **vždycky zapnuto** , aby aplikace Function App běžela správně. V App Serviceovém plánu se modul runtime Functions po několika minutách nečinnosti neukončí, takže se vaše funkce vystaví jenom triggery HTTP. Always On je k dispozici pouze v plánu App Service. V plánu spotřeby platforma automaticky aktivuje aplikace funkcí.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


I při zapnuté funkci Always On je časový limit spuštění pro jednotlivé funkce řízen `functionTimeout` nastavením v [host.js](functions-host-json.md#functiontimeout) v souboru projektu.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Určení plánu hostování existující aplikace

Chcete-li určit plán hostování používaný vaší aplikací Function App, přečtěte si téma **App Service plán** na kartě **Přehled** aplikace Function App v [Azure Portal](https://portal.azure.com). Pokud chcete zobrazit cenovou úroveň, vyberte název **plánu App Service**a v levém podokně vyberte **vlastnosti** .

![Zobrazit plán škálování na portálu](./media/functions-scale/function-app-overview-portal.png)

Pomocí rozhraní příkazového řádku Azure můžete také určit plán následujícím způsobem:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Když je výstup z tohoto příkazu `dynamic` , vaše aplikace Function App je v plánu spotřeby. Když je výstup z tohoto příkazu `ElasticPremium` , vaše aplikace Function App je v plánu Premium. Všechny ostatní hodnoty označují různé úrovně plánu App Service.

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

V jakémkoli plánu aplikace Function App vyžaduje účet obecné Azure Storage, který podporuje Azure Blob, Queue, soubory a Table Storage. Důvodem je to, že Azure Functions spoléhá na Azure Storage pro operace, jako je Správa triggerů a spouštění funkcí protokolování, ale některé účty úložiště nepodporují fronty a tabulky. Tyto účty, které zahrnují účty úložiště jen pro objekty BLOB (včetně služby Premium Storage) a účty úložiště pro obecné účely s replikací zóny redundantního úložiště, se při vytváření aplikace Function Filter odfiltrují ze stávajících výběrů **účtu úložiště** .

Stejný účet úložiště, který používá vaše aplikace Function App, můžete použít taky triggery a vazbami k uložení dat aplikace. U operací náročných na úložiště byste ale měli použít samostatný účet úložiště.  

Je možné, že více aplikací Function App sdílí stejný účet úložiště bez problémů. (Dobrým příkladem toho je, když vyvíjíte více aplikací v místním prostředí pomocí emulátoru Azure Storage, který funguje jako jeden účet úložiště.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Další informace o typech účtů úložiště najdete v tématu [představení služby Azure Storage Services](../storage/common/storage-introduction.md#core-storage-services).

### <a name="in-region-data-residency"></a>V oblasti data zasídlí

Pokud je potřeba, aby všechna zákaznická data zůstala v rámci jedné oblasti, musí být účet úložiště přidružený k aplikaci Function App [v oblasti redundance](../storage/common/storage-redundancy.md).  Pro Durable Functions [Azure Durable Functions](./durable/durable-functions-perf-and-scale.md#storage-account-selection) se taky musí použít účet redundantního redundantního úložiště v oblasti.

Jiná zákaznická data spravovaná platformou se budou ukládat jenom v rámci této oblasti, když se bude hostovat v interní Load Balancer App Service Environment (nebo interního nástroje pomocnému programu pro čtení).  Podrobnosti najdete v [redundanci v zóně pomocného mechanismu](../app-service/environment/zone-redundancy.md#in-region-data-residency).

## <a name="how-the-consumption-and-premium-plans-work"></a>Princip fungování plánů Consumption a Premium

V plánech spotřeby a Premium Azure Functions infrastruktura škálovat prostředky procesoru a paměti přidáním dalších instancí hostitele Functions na základě počtu událostí, na kterých se funkce aktivuje. Každá instance hostitele Functions v plánu spotřeby je omezená na 1,5 GB paměti a jeden procesor.  Instance hostitele je celá aplikace Function App, což znamená, že všechny funkce v rámci aplikace Function App sdílejí prostředky v rámci instance a škálují ve stejnou dobu. Aplikace Function App, které sdílejí stejný plán spotřeby, se škálují nezávisle.  V plánu Premium bude velikost vašeho plánu určovat dostupnou paměť a procesor pro všechny aplikace v tomto plánu na této instanci.  

Soubory s kódem funkce jsou uložené ve sdílených složkách služby soubory Azure na hlavním účtu úložiště funkce. Když odstraníte hlavní účet úložiště aplikace Function App, soubory s kódem funkce se odstraní a nelze je obnovit.

### <a name="runtime-scaling"></a>Škálování za běhu

Azure Functions používá komponentu s názvem *kontroler škálování* pro monitorování míry událostí a určení, zda se má horizontální navýšení nebo škálování škálovat. Kontroler škálování používá heuristiky pro každý typ triggeru. Pokud například používáte Trigger služby Azure Queue Storage, škáluje se podle délky fronty a stáří nejstarší zprávy fronty.

Jednotka škálování pro Azure Functions je aplikace Function App. Při horizontálním navýšení kapacity aplikace Function App se přidělí další prostředky pro spuštění více instancí Azure Functionsho hostitele. V opačném případě dojde k omezení požadavků na výpočetní výkon, protože řadič škálování odebere instance hostitele funkcí. V případě, že v aplikaci Function App nejsou spuštěny žádné funkce, počet instancí je nakonec *zvětšen* na hodnotu nula.

![Škálování událostí monitorování řadiče a vytváření instancí](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>Studený start

Až bude aplikace Function App po určitou dobu nečinná, může tato platforma škálovat počet instancí, na kterých vaše aplikace běží, na nulu. Další požadavek má přidanou latenci škálování od nuly po jeden. Tato latence se označuje jako _studená spuštění_. Počet závislostí, které musí být načteny aplikací Function App, může mít vliv na počáteční čas. Studená Start je více problémů při synchronních operacích, například triggerech HTTP, které musí vracet odpověď. Pokud mají tyto funkce vliv na studená spuštění, zvažte spuštění v plánu Premium nebo ve vyhrazeném plánu s povoleným funkcemi Always On.   

### <a name="understanding-scaling-behaviors"></a>Principy chování škálování

Škálování se může u různých faktorů lišit a škáluje se různě na základě zvoleného triggeru a jazyka. Existuje několik složitými rozhraními chování škálování, která je potřeba znát:

* Jedna aplikace Function App se škáluje maximálně na 200 instancí. Jedna instance může zpracovávat více než jednu zprávu nebo požádat současně, takže neexistuje nastavený limit počtu souběžných spuštění.  Můžete [zadat nižší maximum](#limit-scale-out) pro omezení škálování podle potřeby.
* Pro aktivační události HTTP se přidělují nové instance, a to nejvíce jednou za sekundu.
* U triggerů bez protokolu HTTP se přidělují nové instance, a to nejvíce každých 30 sekund. Škálování je rychlejší při provozu v [plánu Premium](#premium-plan).
* Pro aktivační události Service Bus použijte pro nejúčinnější škálování _Spravovat_ práva k prostředkům. U oprávnění k _naslouchání_ není škálování tak přesné, protože délka fronty se nedá použít k informování rozhodnutí o škálování. Další informace o nastavení práv v zásadách přístupu Service Bus najdete v tématu [zásady autorizace sdíleného přístupu](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* Aktivační události centra událostí najdete v [návodu k škálování](functions-bindings-event-hubs-trigger.md#scaling) v referenčním článku. 

### <a name="limit-scale-out"></a>Omezení horizontálního navýšení kapacity

Možná budete chtít omezit počet instancí, na které aplikace škáluje.  Toto je nejběžnější pro případy, kdy komponenta pro příjem dat, jako je databáze, má omezené propustnost.  Ve výchozím nastavení se funkce plánu spotřeby škálují až na až 200 instancí a funkce plánu Premium se škálují až na až 100 instancí.  Úpravou hodnoty můžete určit nižší maximum pro konkrétní aplikaci `functionAppScaleLimit` .  `functionAppScaleLimit`Hodnota může být nastavena na hodnotu 0 nebo null pro neomezenou nebo platnou hodnotu mezi 1 a maximum.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <resource_group> -n <function_app_name>/config/web --set properties.functionAppScaleLimit=<scale_limit>
```

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Osvědčené postupy a vzory pro škálovatelné aplikace

Existuje mnoho aspektů aplikace Function App, které budou mít vliv na to, jak se bude škálovat, včetně konfigurace hostitele, běhového prostředí a efektivity prostředků.  Další informace najdete v [části věnované škálovatelnosti v článku věnovaném důležitým](functions-best-practices.md#scalability-best-practices)informacím o výkonu. Měli byste taky vědět, jak se připojení chovají, jak se vaše aplikace Function škáluje. Další informace najdete v tématu [Správa připojení v Azure Functions](manage-connections.md).

Další informace o škálování v Pythonu a Node.js Azure Functions najdete v tématu [Příručka pro vývojáře v Pythonu – škálování a souběžnost](functions-reference-python.md#scaling-and-concurrency) a [Azure Functions Node.js příručka pro vývojáře – škálování a souběžnost](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Model fakturace

Fakturace pro různé plány je podrobně popsána na [stránce s cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Použití je agregované na úrovni aplikace funkcí a počítá se pouze v době, kdy je spuštěn kód funkce. Níže jsou uvedené jednotky pro fakturaci:

* **Spotřeba prostředků v GB-s (GB-s)**. Vypočítáno jako kombinace velikosti paměti a doby provádění pro všechny funkce v rámci aplikace Function App. 
* **Spuštění**. Počítá se pokaždé, když se funkce spustí v reakci na Trigger události.

Užitečné dotazy a informace o tom, jak pochopit vyúčtování spotřeby, najdete [na stránce s nejčastějšími dotazy k fakturaci](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>Porovnání plánů hostování

Následující tabulka porovnání uvádí všechny důležité aspekty, které vám pomůžou s rozhodnutím o Azure Functions výběru plánu hostování aplikací:

### <a name="plan-summary"></a>Souhrn plánu
| | |
| --- | --- |  
|**[Plán Consumption](#consumption-plan)**| Automatické škálování a Plaťte jenom za výpočetní prostředky, když jsou vaše funkce spuštěné. V plánu spotřeby se instance hostitele Functions dynamicky přidávají a odstraňují na základě počtu příchozích událostí.<br/> ✔ Výchozí plán hostování.<br/>Plaťte ✔ jenom v případě, že jsou vaše funkce spuštěné.<br/>✔ horizontálního navýšení kapacity, a to i během období vysokého zatížení.|  
|**[Plán Premium](#premium-plan)**|Při automatickém škálování na základě poptávky používejte předem zadržené pracovní procesy ke spouštění aplikací bez prodlevy po nečinnosti, spuštění na výkonnějších instancích a připojení k virtuální sítě. Zvažte plán Azure Functions Premium v následujících situacích, kromě všech funkcí plánu App Service: <br/>✔ Vaše aplikace Function App běží nepřetržitě nebo téměř nepřetržitě.<br/>✔ Máte vysoký počet malých spuštění a máte vysoké náklady na spuštění, ale v plánu spotřeby se účtují s malým počtem sekund.<br/>✔ Budete potřebovat více možností procesoru nebo paměti, než jaké poskytuje plán spotřeby.<br/>✔ Váš kód musí běžet delší dobu, než je maximální doba běhu povolená v plánu spotřeby.<br/>✔ Vyžadujete funkce, které jsou k dispozici pouze v plánu Premium, například připojení k virtuální síti.|  
|**[Vyhrazený plán](#app-service-plan)**<sup>1</sup>|Spusťte své funkce v rámci plánu App Service v pravidelných App Servicech tarifech. Vhodným způsobem pro dlouhotrvající operace, i když je potřeba více prediktivního škálování a nákladů. Vezměte v úvahu App Service plán v následujících situacích:<br/>✔ Máte existující, nevyužité virtuální počítače, které už používají jiné instance App Service.<br/>✔ Chcete zadat vlastní image, na které se mají spouštět vaše funkce.|  
|**[Pomocného mechanismu](#app-service-plan)**<sup>1</sup>|App Service Environment (pomocného mechanismu) je funkce App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění App Service aplikací ve velkém měřítku. Služby ASE jsou vhodné pro úlohy aplikací, které vyžadují: <br/>✔ Velmi vysokého měřítka.<br/>✔ Úplnou izolaci výpočtů a zabezpečení přístupu k síti.<br/>✔ Vysoké využití paměti.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes poskytuje plně izolované a vyhrazené prostředí běžící nad platformou Kubernetes.  Kubernetes je vhodný pro úlohy aplikací, které vyžadují: <br/>✔ Požadavky na vlastní hardware.<br/>✔ Izolaci a zabezpečení přístupu k síti.<br/>✔ Schopnost spouštět v hybridním nebo multi-cloudovém prostředí.<br/>✔ Běžet společně se stávajícími aplikacemi a službami Kubernetes.|  

<sup>1</sup> Pokud chcete určit omezení pro různé možnosti plánu App Service, přečtěte si [omezení App Service plánu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="operating-systemruntime"></a>Operační systém/modul runtime

| | Linux<sup>1</sup><br/>Pouze kód | Systém Windows<sup>2</sup><br/>Pouze kód | Linux<sup>1, 3</sup><br/>Kontejner Docker |
| --- | --- | --- | --- |
| **[Plán Consumption](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Bez podpory  |
| **[Plán Premium](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Vyhrazený plán](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[Pomocného mechanismu](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | Není k dispozici | Není k dispozici |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux je jediným podporovaným operačním systémem pro zásobník modulu runtime Pythonu.  
<sup>2</sup> . Systém Windows je jediným podporovaným operačním systémem pro zásobník modulu runtime prostředí PowerShell.   
<sup>3</sup> . Linux je jediným podporovaným operačním systémem pro kontejnery Docker.
<sup>4</sup> konkrétní omezení pro různé možnosti plánu App Service najdete v tématu [omezení plánu App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="scale"></a>Měřítko

| | Horizontální navýšení kapacity | Maximální počet instancí |
| --- | --- | --- |
| **[Plán Consumption](#consumption-plan)** | Řízený událost. Horizontální navýšení kapacity, a to i během období vysokého zatížení. Azure Functions infrastruktura škáluje prostředky procesoru a paměti přidáním dalších instancí hostitele Functions na základě počtu událostí, na kterých se spouští jeho funkce. | 200 |
| **[Plán Premium](#premium-plan)** | Řízený událost. Horizontální navýšení kapacity, a to i během období vysokého zatížení. Azure Functions infrastruktura škáluje prostředky procesoru a paměti přidáním dalších instancí hostitele Functions na základě počtu událostí, na kterých se spouští jeho funkce. |100|
| **[Vyhrazený plán](#app-service-plan)**<sup>1</sup> | Ruční nebo automatické škálování |10-20|
| **[Pomocného mechanismu](#app-service-plan)**<sup>1</sup> | Ruční nebo automatické škálování |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Automatické škálování založené na událostech pro clustery Kubernetes s využitím [keda](https://keda.sh) | Liší se &nbsp; podle &nbsp; clusteru.&nbsp;&nbsp;|

<sup>1</sup> Pokud chcete určit omezení pro různé možnosti plánu App Service, přečtěte si [omezení App Service plánu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="cold-start-behavior"></a>Chování studeného startu

|    |    | 
| -- | -- |
| **[&nbsp;Plán spotřeby](#consumption-plan)** | Aplikace se můžou po určitou dobu v nečinnosti škálovat na nulu, což znamená, že některé požadavky můžou při spuštění mít další latenci.  Plán spotřeby má několik optimalizací, které vám pomůžou snižovat čas na studený start, včetně přijímání předem zatepléch zástupných funkcí, které už mají spuštěné hostitele funkcí a jazyka. |
| **[Plán Premium](#premium-plan)** | Trvalé zahřívání instancí, aby nedocházelo k žádnému studenému startu. |
| **[Vyhrazený plán](#app-service-plan)**<sup>1</sup> | Při spuštění ve vyhrazeném plánu může hostitel služby Functions běžet průběžně, což znamená, že se ve skutečnosti nejedná o problém s studeným startem. |
| **[Pomocného mechanismu](#app-service-plan)**<sup>1</sup> | Při spuštění ve vyhrazeném plánu může hostitel služby Functions běžet průběžně, což znamená, že se ve skutečnosti nejedná o problém s studeným startem. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Závisí na konfiguraci KEDA. Aplikace je možné nakonfigurovat tak, aby vždy běžely a nikdy nezačaly běžet, nebo aby se nakonfigurovali na hodnotu nula, což vede ke studenému startu na nové události. 

<sup>1</sup> Pokud chcete určit omezení pro různé možnosti plánu App Service, přečtěte si [omezení App Service plánu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="service-limits"></a>Omezení služby

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>Síťové funkce

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>Fakturace

| | | 
| --- | --- |
| **[Plán Consumption](#consumption-plan)** | Platíte jenom za čas, kdy se vaše funkce spouštějí. Fakturace vychází z počtu spuštění, doby spuštění a použité paměti. |
| **[Plán Premium](#premium-plan)** | Plán Premium je založený na počtu základních sekund a paměti využitých v případě potřeby a předem zahřívání instancí. Alespoň jedna instance v každém plánu musí být zachována v zahřívání. Tento plán poskytuje předvídatelné ceny. |
| **[Vyhrazený plán](#app-service-plan)**<sup>1</sup> | Totéž platí pro aplikace Function App v plánu App Service, stejně jako u jiných prostředků App Service, jako jsou například webové aplikace.|
| **[Pomocného mechanismu](#app-service-plan)**<sup>1</sup> | pro pomocného objekt pro řízení se platí paušální měsíční sazba, která platí pro infrastrukturu a která se nemění podle velikosti pomocného mechanismu. Navíc platí, že vCPU plán bude mít cenu za App Service. Všechny aplikace hostované ve službě ASE jsou ve skladové položce s izolovanou cenou. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Platíte jenom náklady na váš cluster Kubernetes; žádné další fakturace pro funkce. Vaše aplikace Function App funguje v rámci clusteru jako úloha aplikace, stejně jako u běžné aplikace. |

<sup>1</sup> Pokud chcete určit omezení pro různé možnosti plánu App Service, přečtěte si [omezení App Service plánu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="next-steps"></a>Další kroky

+ [Rychlý Start: vytvoření projektu Azure Functions pomocí Visual Studio Code](functions-create-first-function-vs-code.md)
+ [Technologie nasazení v Azure Functions](functions-deployment-technologies.md) 
+ [Příručka pro vývojáře v Azure Functions](functions-reference.md)
