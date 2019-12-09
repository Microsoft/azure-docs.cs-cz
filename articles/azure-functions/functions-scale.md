---
title: Azure Functions škálování a hostování
description: Naučte se vybírat mezi plánem Azure Functions spotřebu a plánem Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6520f205d0a9c1a33d0cb4911a58a5e680bdadb7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929727"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions škálování a hostování

Když vytvoříte aplikaci Function App v Azure, musíte zvolit plán hostování pro vaši aplikaci. K dispozici jsou tři plány hostování pro Azure Functions: [plán spotřeby](#consumption-plan), [Plán Premium](#premium-plan)a [plán App Service](#app-service-plan).

Plán hostování, který zvolíte, bude určovat následující chování:

* Jak se škáluje aplikace Function App
* Prostředky dostupné pro jednotlivé instance aplikace Function App.
* Podpora pro pokročilé funkce, jako je třeba připojení k virtuální síti.

Plány spotřeby i úrovně Premium automaticky přidávají výpočetní výkon, když je váš kód spuštěný. Vaše aplikace se škáluje v případě potřeby na zpracování zatížení a při zastavení kódu se škáluje dolů. Pro plán spotřeby také nemusíte platit za nečinné virtuální počítače nebo rezervovat kapacitu předem.  

Plán Premium poskytuje další funkce, jako jsou výpočetní instance Premium, schopnost udržet instance na neomezenou dobu a připojení k virtuální síti.

App Service plán vám umožní využít výhod vyhrazené infrastruktury, kterou spravujete. Vaše aplikace Function App se škáluje na základě událostí, což znamená, že se nikdy neškáluje na nulu. (Vyžaduje, aby byla povolena možnost [vždy](#always-on) zapnuto.)

## <a name="hosting-plan-support"></a>Podpora plánu hostování

Podpora funkcí spadá do následujících dvou kategorií:

* _Všeobecně dostupná (GA)_ : plně podporované a schválené pro použití v produkčním prostředí.
* _Preview_: ještě není plně podporované a schválené pro použití v produkčním prostředí.

Následující tabulka uvádí aktuální úroveň podpory pro tři plány hostování při použití v systému Windows nebo Linux:

| | Plán Consumption | Plán Premium | Vyhrazený plán |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>Plán Consumption

Pokud používáte plán spotřeby, instance Azure Functions hostitele se dynamicky přidávají a odstraňují na základě počtu příchozích událostí. Tento bezserverový plán se automaticky škáluje a výpočetní prostředky se vám účtují, jenom když vaše funkce běží. V plánu Spotřeba po nastavené době vyprší časový limit spuštění funkce.

Fakturace vychází z počtu spuštění, doby spuštění a použité paměti. Fakturace se agreguje napříč všemi funkcemi v rámci aplikace funkce. Další informace najdete na stránce s [cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Plán spotřeby je výchozím plánem hostování a nabízí následující výhody:

* Plaťte jenom v případě, že jsou vaše funkce spuštěné.
* Horizontální navýšení kapacity i během období vysokého zatížení

Aplikace Function App ve stejné oblasti se dají přiřadit ke stejnému plánu spotřeby. Neexistuje žádný nevýhodou ani dopad na to, aby ve stejném plánu spotřeby běželo víc aplikací. Přiřazení více aplikací ke stejnému plánu spotřeby nemá žádný vliv na odolnost, škálovatelnost nebo spolehlivost každé aplikace.

Další informace o odhadu nákladů při spuštění v plánu spotřeby najdete v tématu [porozumění nákladům na plán spotřeby](functions-consumption-costs.md).

## <a name="premium-plan"></a>Plán Premium

Pokud používáte plán Premium, instance Azure Functions hostitele se přidají a odeberou na základě počtu příchozích událostí stejně jako u plánu spotřeby.  Plán Premium podporuje následující funkce:

* Trvalé zahřívání instancí, aby nedocházelo k žádnému studenému startu
* Připojení virtuální sítě
* Neomezené trvání spuštění
* Velikosti instancí Premium (jedna jádro, dvě jádro a čtyři základní instance)
* Předvídatelné ceny
* Přidělování aplikací s vysokou hustotou pro plány s více aplikacemi Function App

Informace o tom, jak můžete tyto možnosti nakonfigurovat, najdete v [dokumentu plánu Azure Functions Premium](functions-premium-plan.md).

Faktura za plán Premium vychází z počtu základních sekund a paměti využitých v případě potřeby a předem zaspotřebovaných instancí, a to místo fakturace za spuštění a využití paměti. Aspoň jedna instance musí být v každém plánu zadarmo. To znamená, že je k dispozici minimální měsíční cena za aktivní plán bez ohledu na počet spuštění. Mějte na paměti, že všechny aplikace Function App v plánu Premium sdílí předem zahříváníelné a aktivní instance.

Vezměte v úvahu plán Azure Functions Premium v následujících situacích:

* Vaše aplikace Function App běží nepřetržitě nebo téměř nepřetržitě.
* Máte vysoký počet malých spuštění a máte vysoké náklady na spuštění, ale v plánu spotřeby se účtují za méně GB.
* Budete potřebovat více možností procesoru nebo paměti, než jaké je k dispozici v plánu spotřeby.
* Váš kód musí běžet delší dobu, než je [Maximální doba spuštění](#timeout) v plánu spotřeby.
* Vyžadujete funkce, které jsou k dispozici pouze v plánu Premium, například připojení VNET/VPN.

Při spouštění funkcí JavaScriptu na plánu Premium byste měli zvolit instanci, která má méně vCPU. Další informace najdete v tématu [Výběr plánů Premium s jedním jádrem](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Vyhrazený plán (App Service)

Aplikace Function App můžete spustit taky na stejných vyhrazených virtuálních počítačích, jako jsou jiné App Service aplikace (Basic, Standard, Premium a izolované SKU).

Vezměte v úvahu App Service plán v následujících situacích:

* Máte existující a nevyužité virtuální počítače, které již spouštějí jiné instance App Service.
* Chcete zadat vlastní image, na které se mají spouštět vaše funkce.

Totéž platí pro aplikace Function App v plánu App Service, stejně jako u jiných prostředků App Service, jako jsou například webové aplikace. Podrobnosti o tom, jak plán App Service funguje, najdete v podrobném [přehledu Azure App Service plány](../app-service/overview-hosting-plans.md).

S plánem App Service můžete ručně škálovat přidáním dalších instancí virtuálních počítačů. Můžete také povolit automatické škálování. Další informace najdete v tématu [Ruční nebo automatické škálování počtu instancí](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Horizontální navýšení kapacity můžete také škálovat tak, že vyberete jiný plán App Service. Další informace najdete v tématu [horizontální navýšení kapacity aplikace v Azure](../app-service/manage-scale-up.md). 

Při spouštění funkcí JavaScriptu v plánu App Service byste měli zvolit plán, který má méně vCPU. Další informace najdete v tématu [Výběr plánů App Service s jedním jádrem](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a>Vždy zapnuto

Pokud spustíte v plánu App Service, měli byste povolit nastavení **vždycky zapnuto** , aby aplikace Function App běžela správně. V App Serviceovém plánu se modul runtime Functions po několika minutách nečinnosti neukončí, takže se vaše funkce vystaví jenom triggery HTTP. Always On je k dispozici pouze v plánu App Service. V plánu spotřeby platforma automaticky aktivuje aplikace funkcí.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


I když je funkce Always On zapnutá, časový limit spuštění pro jednotlivé funkce se řídí nastavením `functionTimeout` v souboru projektu [Host. JSON](functions-host-json.md#functiontimeout) .

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Určení plánu hostování existující aplikace

Chcete-li určit plán hostování používaný vaší aplikací Function App, přečtěte si část **App Service plán/cenová úroveň** na kartě **Přehled** pro aplikaci Function App v [Azure Portal](https://portal.azure.com). V případě plánů App Service je cenová úroveň také uvedena.

![Zobrazit plán škálování na portálu](./media/functions-scale/function-app-overview-portal.png)

Pomocí rozhraní příkazového řádku Azure můžete také určit plán následujícím způsobem:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Když je výstup z tohoto příkazu `dynamic`, vaše aplikace Function App je v plánu spotřeby. Když je výstup z tohoto příkazu `ElasticPremium`, vaše aplikace Function App je v plánu Premium. Všechny ostatní hodnoty označují různé úrovně plánu App Service.

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

V jakémkoli plánu aplikace Function App vyžaduje účet obecné Azure Storage, který podporuje Azure Blob, Queue, soubory a Table Storage. Důvodem je to, že funkce spoléhají na Azure Storage pro operace, jako je Správa triggerů a spouštění funkcí protokolování, ale některé účty úložiště nepodporují fronty a tabulky. Tyto účty, které zahrnují účty úložiště jen pro objekty BLOB (včetně služby Premium Storage) a účty úložiště pro obecné účely s replikací zóny redundantního úložiště, se při vytváření aplikace Function Filter odfiltrují ze stávajících výběrů **účtu úložiště** .

Stejný účet úložiště, který používá vaše aplikace Function App, můžete použít taky triggery a vazbami k uložení dat aplikace. U operací náročných na úložiště byste ale měli použít samostatný účet úložiště.   

<!-- JH: Does using a Premium Storage account improve perf? -->

Další informace o typech účtů úložiště najdete v tématu [představení služby Azure Storage Services](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Jak fungují plány spotřeby a Premium

V plánech spotřeby a Premium Azure Functions infrastruktura škálovat prostředky procesoru a paměti přidáním dalších instancí hostitele Functions na základě počtu událostí, na kterých se funkce aktivuje. Každá instance hostitele Functions v plánu spotřeby je omezená na 1,5 GB paměti a jeden procesor.  Instance hostitele je celá aplikace Function App, což znamená, že všechny funkce v rámci aplikace Function App sdílejí prostředky v rámci instance a škálují ve stejnou dobu. Aplikace Function App, které sdílejí stejný plán spotřeby, se škálují nezávisle.  V plánu Premium bude velikost vašeho plánu určovat dostupnou paměť a procesor pro všechny aplikace v tomto plánu na této instanci.  

Soubory s kódem funkce jsou uložené ve sdílených složkách služby soubory Azure na hlavním účtu úložiště funkce. Když odstraníte hlavní účet úložiště aplikace Function App, soubory s kódem funkce se odstraní a nelze je obnovit.

### <a name="runtime-scaling"></a>Škálování za běhu

Azure Functions používá komponentu s názvem *kontroler škálování* pro monitorování míry událostí a určení, zda se má horizontální navýšení nebo škálování škálovat. Kontroler škálování používá heuristiky pro každý typ triggeru. Pokud například používáte Trigger služby Azure Queue Storage, škáluje se podle délky fronty a stáří nejstarší zprávy fronty.

Jednotka škálování pro Azure Functions je aplikace Function App. Při horizontálním navýšení kapacity aplikace Function App se přidělí další prostředky pro spuštění více instancí Azure Functionsho hostitele. V opačném případě dojde k omezení požadavků na výpočetní výkon, protože řadič škálování odebere instance hostitele funkcí. V případě, že v aplikaci Function App nejsou spuštěny žádné funkce, počet instancí je nakonec *zvětšen* na hodnotu nula.

![Škálování událostí monitorování řadiče a vytváření instancí](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Principy chování škálování

Škálování se může u různých faktorů lišit a škáluje se různě na základě zvoleného triggeru a jazyka. Existuje několik složitými rozhraními chování škálování, která je potřeba znát:

* Jedna aplikace funkcí se může škálovat maximálně na 200 instancí. Jedna instance může zpracovávat více než jednu zprávu nebo požádat současně, takže neexistuje nastavený limit počtu souběžných spuštění.
* U triggerů HTTP se nové instance přiřazují jenom jednou za 1 sekundu.
* U triggerů bez protokolu HTTP se nové instance přiřazují jenom každých 30 sekund.

Různé aktivační události mohou mít také různá omezení škálování a jsou popsány níže:

* [Centrum událostí](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Osvědčené postupy a vzory pro škálovatelné aplikace

Existuje mnoho aspektů aplikace Function App, které budou mít vliv na to, jak se bude škálovat, včetně konfigurace hostitele, běhového prostředí a efektivity prostředků.  Další informace najdete v [části věnované škálovatelnosti v článku věnovaném důležitým](functions-best-practices.md#scalability-best-practices)informacím o výkonu. Měli byste taky vědět, jak se připojení chovají, jak se vaše aplikace Function škáluje. Další informace najdete v tématu [Správa připojení v Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Model fakturace

Fakturace pro různé plány je podrobně popsána na [stránce s cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Použití je agregované na úrovni aplikace funkcí a počítá se pouze v době, kdy je spuštěn kód funkce. Níže jsou uvedené jednotky pro fakturaci:

* **Spotřeba prostředků v GB-s (GB-s)** . Vypočítáno jako kombinace velikosti paměti a doby provádění pro všechny funkce v rámci aplikace Function App. 
* **Spuštění**. Počítá se pokaždé, když se funkce spustí v reakci na Trigger události.

Užitečné dotazy a informace o tom, jak pochopit vyúčtování spotřeby, najdete [na stránce s nejčastějšími dotazy k fakturaci](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Omezení služby

Následující tabulka uvádí omezení, která platí pro aplikace Function App při spuštění v různých plánech hostování:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
