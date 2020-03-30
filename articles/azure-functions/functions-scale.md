---
title: Hostování a škálování Azure Functions
description: Přečtěte si, jak si vybrat mezi plánem Spotřeby funkcí Azure a plánem Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0a54d7490fb306bfbc8e1b111e7b7d64c09d2292
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276605"
---
# <a name="azure-functions-scale-and-hosting"></a>Hostování a škálování Azure Functions

Když v Azure vytvoříte funkční aplikaci, musíte pro svou aplikaci zvolit plán hostování. Pro funkce Azure jsou k dispozici tři plány [hostingu: plán spotřeby](#consumption-plan), [plán Premium](#premium-plan)a plán [Vyhrazené (App Service).](#app-service-plan)

Plán hostování, který zvolíte, určuje následující chování:

* Jak se mění velikost aplikace pro funkci.
* Prostředky, které jsou k dispozici pro každou instanci aplikace funkce.
* Podpora pokročilých funkcí, jako je například připojení virtuální sítě Azure.

Plány Spotřeba i Premium automaticky přidávají výpočetní výkon, když je váš kód spuštěný. Vaše aplikace se v případě potřeby zvětšuje a škáluje, když se přestane pracovat s kódem. Pro plán Spotřeba také nemusíte platit za nečinné virtuální uživatele nebo rezervovat kapacitu předem.  

Plán Premium poskytuje další funkce, jako jsou instance premium výpočetní, možnost udržovat instance teplé na neurčito a připojení virtuální sítě.

Plán služby App Service umožňuje využívat výhod vyhrazené infrastruktury, kterou spravujete. Aplikace funkce se neškáluje na základě událostí, což znamená, že se nikdy neškáluje na nulu. (Vyžaduje, aby bylo [povoleno vždy zapnuto.)](#always-on)

## <a name="hosting-plan-support"></a>Podpora hostingového plánu

Podpora funkcí spadá do následujících dvou kategorií:

* _Obecně dostupné (GA)_: plně podporované a schválené pro použití ve výrobě.
* _Náhled_: dosud plně podporována ani schválena pro produkční použití.

Následující tabulka označuje aktuální úroveň podpory pro tři plány hostování při spuštění v systému Windows nebo Linux:

| | Plán Consumption | Plán Premium | Vyhrazený plán |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>Plán Consumption

Když používáte plán Spotřeba, instance hostitele Azure Functions se dynamicky přidávají a odeberou na základě počtu příchozích událostí. Tento bezserverový plán se automaticky škáluje a výpočetní prostředky se vám účtují, jenom když vaše funkce běží. V plánu Spotřeba po nastavené době vyprší časový limit spuštění funkce.

Fakturace vychází z počtu spuštění, doby spuštění a použité paměti. Fakturace se agreguje napříč všemi funkcemi v rámci aplikace funkce. Další informace najdete na [stránce s cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Plán spotřeby je výchozí hostingový plán a nabízí následující výhody:

* Plaťte pouze v případě, že jsou spuštěny vaše funkce
* Horizontální navýšení kapacity automaticky, a to i během období vysokého zatížení

Funkční aplikace ve stejné oblasti lze přiřadit ke stejnému plánu spotřeby. Neexistuje žádná nevýhoda nebo dopad na to, že více aplikací běží ve stejném plánu spotřeby. Přiřazení více aplikací ke stejnému plánu spotřeby nemá žádný vliv na odolnost, škálovatelnost nebo spolehlivost jednotlivých aplikací.

Další informace o odhadu nákladů při spuštění v plánu spotřeby naleznete v [tématu Principy nákladů plánu spotřeby](functions-consumption-costs.md).

## <a name="premium-plan"></a><a name="premium-plan"></a>Plán Premium

Když používáte plán Premium, instance hostitele Azure Functions se přidávají a odeberou na základě počtu příchozích událostí, stejně jako plán Spotřeba.  Prémiový plán podporuje následující funkce:

* Neustále teplé instance, aby se zabránilo jakémukoli studenému startu
* Připojení virtuální sítě
* Neomezená doba provádění (zaručena 60 minut)
* Velikosti instancí Premium (jedno jádro, dvě jádra a čtyři základní instance)
* Předvídatelnější ceny
* Přidělení aplikací s vysokou hustotou pro plány s vícefunkčními aplikacemi

Informace o tom, jak můžete tyto možnosti nakonfigurovat, najdete v [dokumentu plánu Azure Functions Premium](functions-premium-plan.md).

Namísto fakturace na spuštění a spotřebované paměti je fakturace pro plán Premium založena na počtu základních sekund a paměti používané v potřebných a předem zahřátých instancích. Alespoň jedna instance musí být teplá po celou dobu na plán. To znamená, že je minimální měsíční náklady na aktivní plán, bez ohledu na počet spuštění. Mějte na paměti, že všechny funkční aplikace v plánu Premium sdílejí předem zahřáté a aktivní instance.

Zvažte plán Azure Functions Premium v následujících situacích:

* Vaše funkční aplikace běží nepřetržitě nebo téměř nepřetržitě.
* Máte vysoký počet malých exekucí a máte vysoké provedení účtu, ale nízké GB druhý účet v plánu spotřeba.
* Potřebujete více možností procesoru nebo paměti, než co je k dispozici v plánu spotřeby.
* Váš kód musí běžet déle, než [je maximální doba provádění povolená](#timeout) v plánu spotřeby.
* Vyžadujete funkce, které jsou k dispozici pouze v plánu Premium, jako je například připojení k virtuální síti.

Při spuštění funkcí JavaScriptu v plánu Premium byste měli zvolit instanci, která má méně virtuálních procesorů. Další informace najdete v tématu [Volba jednojádrových plánů Premium](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Vyhrazený plán (Služba aplikace)

Vaše funkční aplikace můžou taky běžet na stejných vyhrazených virtuálních počítačích jako ostatní aplikace služby App Service (Základní, Standardní, Premium a Izolované skum).

Zvažte plán služby App Service v následujících situacích:

* Máte existující, nevyužité virtuální chody, které už spouštějí jiné instance služby App Service.
* Chcete poskytnout vlastní obrázek, na kterém chcete spustit funkce.

Platíte stejné pro funkce aplikací v plánu služby App Service, jako byste pro jiné prostředky služby App Service, jako jsou webové aplikace. Podrobnosti o tom, jak plán služby App Service funguje, najdete v tématu [plány azure app service podrobný přehled](../app-service/overview-hosting-plans.md).

S plánem služby App Service můžete ručně horizontální navýšení kapacity přidáním dalších instancí virtuálních aplikací. Můžete také povolit automatické škálování. Další informace naleznete v tématu [Škálování počtu instancí ručně nebo automaticky](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Můžete také vertikálně navýšit kapacitu výběrem jiného plánu služby App Service. Další informace najdete [v tématu Vertika navýšit kapacitu aplikace v Azure](../app-service/manage-scale-up.md). 

Při spuštění funkcí JavaScriptu v plánu služby App Service byste měli zvolit plán, který má méně virtuálních procesorů. Další informace najdete [v tématu Volba jednojádrových plánů služby App Service](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a><a name="always-on"></a>Vždy zapnuto

Pokud spustíte plán služby App Service, měli byste povolit **vždy na** nastavení tak, aby vaše aplikace funkce běží správně. V plánu služby App Service se po několika minutách nečinnosti spustí zaběhá zaběhu funkce, takže pouze aktivační události HTTP "probudí" vaše funkce. Vždy zapnutý je k dispozici pouze v plánu služby App Service. V plánu Spotřeba platforma aktivuje aplikace funkcí automaticky.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


I s povolenou funkcí Always On je časový `functionTimeout` plán spuštění pro jednotlivé funkce řízen nastavením v souboru projektu [host.json.](functions-host-json.md#functiontimeout)

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Určení plánu hostování existující aplikace

Pokud chcete určit plán hostování, který používá vaše aplikace pro funkce, přečtěte si informace o **plánu služby App Service / cenové úrovni** na kartě **Přehled** aplikace funkce na [portálu Azure](https://portal.azure.com). U plánů služby App Service je také uvedena cenová úroveň.

![Zobrazit plán škálování na portálu](./media/functions-scale/function-app-overview-portal.png)

Můžete také použít Azure CLI k určení plánu, takto:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Pokud je `dynamic`výstup z tohoto příkazu , aplikace funkce je v plánu spotřeba. Pokud je `ElasticPremium`výstup z tohoto příkazu , vaše aplikace funkce je v plánu Premium. Všechny ostatní hodnoty označují různé úrovně plánu služby App Service.

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

V každém plánu vyžaduje aplikace funkce obecný účet Azure Storage, který podporuje azure blob, fronta, soubory a úložiště tabulek. Důvodem je, že funkce závisí na Azure Storage pro operace, jako je správa aktivačních událostí a protokolování spuštění funkce, ale některé účty úložiště nepodporují fronty a tabulky. Tyto účty, které zahrnují účty úložiště pouze pro objekty blob (včetně úložiště premium) a účty úložiště pro obecné účely s replikací zónově redundantního úložiště, jsou odfiltrovány z existujících výběrů **účtů úložiště** při vytváření aplikace funkcí.

Stejný účet úložiště, který používá vaše aplikace funkce, můžou taky používat aktivační události a vazby k ukládání dat aplikace. Pro operace náročné na úložiště byste však měli použít samostatný účet úložiště.  

Je jistě možné, aby více funkčních aplikací sdílelo stejný účet úložiště bez problémů. (Dobrým příkladem toho je, když vyvíjíte více aplikací v místním prostředí pomocí emulátoru úložiště Azure, který funguje jako jeden účet úložiště.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Další informace o typech účtů úložiště najdete [v tématu Představení služeb Azure Storage](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Jak fungují plány spotřeby a prémií

V plánech Spotřeba a Premium infrastruktura Azure Functions škáluje prostředky procesoru a paměti přidáním dalších instancí hostitele Functions na základě počtu událostí, na kterých se aktivují jeho funkce. Každá instance hostitele Functions v plánu Spotřeba je omezena na 1,5 GB paměti a jeden procesor.  Instance hostitele je celá aplikace funkce, což znamená, že všechny funkce v rámci prostředku sdílení aplikace funkce v rámci instance a škálování ve stejnou dobu. Aplikace funkcí, které sdílejí stejný plán spotřeby, se škálují nezávisle.  V plánu Premium velikost plánu určí dostupnou paměť a procesor pro všechny aplikace v tomto plánu v této instanci.  

Soubory kódu funkce se ukládají ve sdílených složkách Azure Files na hlavním účtu úložiště funkce. Když odstraníte hlavní účet úložiště aplikace funkce, soubory kódu funkce jsou odstraněny a nelze je obnovit.

### <a name="runtime-scaling"></a>Škálování za běhu

Azure Functions používá komponentu s názvem *řadič škálování* ke sledování rychlosti událostí a určení, zda horizontální navýšení kapacity nebo škálování v. Řadič váhy používá heuristiku pro každý typ aktivační události. Například když používáte aktivační událost úložiště fronty Azure, škáluje se na základě délky fronty a stáří nejstarší zprávy fronty.

Jednotka škálování pro funkce Azure je aplikace funkce. Když se aplikace funkce škálovat, další prostředky jsou přiděleny ke spuštění více instancí hostitele Azure Functions. Naopak jako výpočetní poptávka je snížena, řadič škálování odebere instance hostitele funkce. Počet instancí je nakonec *škálovat na nulu,* pokud žádné funkce jsou spuštěny v rámci aplikace funkce.

![Škálování událostí monitorování řadiče a vytváření instancí](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Principy chování škálování

Škálování se může lišit v řadě faktorů a škálování odlišně v závislosti na aktivační události a vybraném jazyce. Existuje několik složitostí chování škálování, které je třeba znát:

* Aplikace s jednou funkcí se škáluje pouze na maximálně 200 instancí. Jedna instance může zpracovat více než jednu zprávu nebo požadavek najednou, takže není nastaven limit na počet souběžných spuštění.
* Pro aktivační události HTTP jsou přiděleny nové instance maximálně jednou za sekundu.
* Pro aktivační události bez protokolu HTTP jsou přiděleny nové instance maximálně jednou za 30 sekund. Škálování je rychlejší při spuštění v [plánu Premium](#premium-plan).
* Pro aktivační události service bus použijte _spravovat_ práva na prostředky pro nejefektivnější škálování. S _listen_ práva škálování není tak přesné, protože délka fronty nelze použít k informování rozhodnutí škálování. Další informace o nastavení práv v zásadách přístupu služby Service Bus naleznete v [tématu Zásady autorizace sdíleného přístupu](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies).
* Aktivační události centra událostí najdete v [pokynech pro škálování](functions-bindings-event-hubs-trigger.md#scaling) v referenčním článku. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Doporučené postupy a vzory pro škálovatelné aplikace

Existuje mnoho aspektů aplikace funkce, které budou mít vliv na to, jak dobře bude škálovat, včetně konfigurace hostitele, stopy za běhu a účinnosti zdrojů.  Další informace naleznete v [části škálovatelnost článku aspekty výkonu](functions-best-practices.md#scalability-best-practices). Měli byste si také být vědomi toho, jak se připojení chovají jako měřítko aplikace funkce. Další informace najdete v tématu [Správa připojení v Azure Functions](manage-connections.md).

Další informace o škálování v Pythonu a Node.js najdete v [tématu Azure Functions Python developer guide – Škálování a souběžnost](functions-reference-python.md#scaling-and-concurrency) a [Azure Functions Node.js developer guide – škálování a souběžnost](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Model fakturace

Fakturace pro různé plány je podrobně popsána na [stránce s cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Využití je agregováno na úrovni aplikace funkce a počítá pouze čas, který je spuštěn kód funkce. Níže jsou jednotky pro fakturaci:

* **Spotřeba prostředků v gigabajtových sekundách (GB-s)**. Vypočítá se jako kombinace velikosti paměti a doby spuštění pro všechny funkce v rámci aplikace funkce. 
* **Popravy**. Počítá no pokaždé, když je funkce spuštěna v reakci na aktivační událost.

Užitečné dotazy a informace o tom, jak porozumět účtu za spotřebu, najdete [v nejčastějších dotazech k fakturaci](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Omezení služby

Následující tabulka označuje omezení, která platí pro funkční aplikace při spuštění v různých hostitelských plánech:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
