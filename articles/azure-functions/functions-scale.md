---
title: Azure Functions hostování a škálování | Dokumentace Microsoftu
description: Zjistěte, jak si vybrat mezi plán Azure Functions Consumption a plán Premium.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure funkce, funkce, plán consumption, plán premium, zpracování událostí, webhooky, dynamické výpočty, architektura bez serveru
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3253cc7e379ae63880d533f14bc76e7af5a4425a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050552"
---
# <a name="azure-functions-scale-and-hosting"></a>Hostování a škálování Azure Functions

Když vytvoříte aplikaci function app v Azure, musíte zvolit plán hostování pro vaši aplikaci. Pro službu Azure Functions k dispozici jsou tři plány hostování: [Plán consumption](#consumption-plan), [plán Premium](#premium-plan), a [plán služby App Service](#app-service-plan).

Plán hostování, zvolíte určuje následující chování:

* Jak se škálovat vaši aplikaci function app.
* Prostředky dostupné pro každou instanci aplikace funkce.
* Podpora pro pokročilé funkce, jako je například připojení k virtuální síti.

Plány Consumption a Premium automaticky přidat výpočetní výkon, když váš kód běží. Vaše aplikace je škálovat na více systémů v případě potřeby pro zpracování zátěže a kapacitu vertikálně snížit, když kód přestane fungovat. Pro plán Consumption také nemusíte platit za nečinných virtuálních počítačů nebo záložní kapacita předem.  

Plán Premium poskytuje další funkce, jako například premium výpočetních instancí, schopnost uchovat teplý instance po neomezenou dobu a připojení k virtuální síti.

Plán služby App Service umožňuje využít výhod vyhrazená infrastruktura, která spravujete. Aplikace function app se neškáluje na základě událostí, což znamená, že se nikdy měřítka až k nule. (Vyžaduje, aby [vždy na](#always-on) je povolená.)

> [!NOTE]
> Můžete přepínat mezi plány Consumption a Premium změnou vlastnosti plánu prostředek aplikace funkce.

## <a name="hosting-plan-support"></a>Podpora plán hostování

Podpora funkce spadá do následujících dvou kategorií:

* _Všeobecně dostupná (GA)_ : plně podporované a schválena pro použití v produkčním prostředí.
* _Ve verzi Preview_: není dosud plně podporované a schválena pro použití v produkčním prostředí.

Následující tabulka označuje aktuální úroveň podpory pro tři plány hostování při běžící ve Windows nebo Linuxu:

| | Plán Consumption | Plán Premium | Vyhrazený plán |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | preview | GA |
| Linux | preview | neuvedeno | GA |

## <a name="consumption-plan"></a>Plán Consumption

Pokud používáte plán Consumption, instance hostitele Azure Functions dynamicky přidávají nebo odebírají na základě počtu příchozích událostí. Tento plán bez serveru se automaticky škáluje. proto vám budou účtovány za výpočetní prostředky, pouze když vaše funkce běží. V plánu Consumption vyprší časový limit po nastaveném časovém nastavení provádění funkce.

Fakturace vychází z počtu spuštění, čas spuštění a paměť použitá. Fakturace se agreguje přes všechny funkce v rámci aplikace function app. Další informace najdete v tématu [stránce s cenami za Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Plán Consumption je výchozí plán hostování a nabízí následující výhody:

* Platí, pouze když vaše funkce běží
* Automatické horizontální navýšení kapacity, i během období vysoké zatížení

Funkce ve stejné oblasti, je možné přiřadit do stejného plánu Consumption. Neexistuje žádné nevýhodou nebo dopad s tím, že více aplikací běžících v rámci stejného plánu Consumption. Přiřazení více aplikací do stejného plánu consumption nemá žádný vliv na pružnosti, škálovatelnosti nebo spolehlivost každou aplikaci.

## <a name="premium-plan"></a>Plán Premium (preview)

Pokud používáte plán Premium, instance hostitele Azure Functions přidávají nebo odebírají na základě počtu příchozích událostí stejně jako plánu Consumption.  Plán Premium podporuje následující funkce:

* Trvale vyzkoušeli instancí, aby všechny studený start
* Připojení k virtuální síti
* Doba trvání neomezený počet spuštění
* Velikosti instancí úrovně Premium (jedno jádro, dvě jádra a čtyři jádra instance)
* Více předvídatelné ceny
* Aplikace s vysokou hustotou přidělení pro plány s více aplikacemi – funkce

Informace o tom, jak můžete nastavit tyto možnosti najdete v [dokument plánu premium Azure Functions](functions-premium-plan.md).

Místo fakturace za spuštění a využitá paměť se fakturace za plán Premium podle počtu sekundy jader, doba spuštění a paměť použitá potřebné a rezervovaných instancí.  Nejméně jedna instance musí být teplé at celou dobu. To znamená, že je pevný měsíční poplatek za každý aktivního plánu, bez ohledu na počet spuštění.

Vezměte v úvahu prémiové služby Azure Functions v následujících situacích:

* Vaše aplikace function App spouštět nepřetržitě téměř průběžně.
* Budete potřebovat další možnosti procesoru nebo paměti, než poskytuje plánu Consumption.
* Váš kód je potřeba spustit déle, než [maximální doba spuštění povolené](#timeout) v plánu Consumption.
* Vyžadujete funkce, které jsou dostupné jenom na plán Premium, jako je například virtuální síť nebo VPN připojení.

> [!NOTE]
> Ve verzi preview plán premium aktuálně podporuje pouze Azure Functions na Windows.

Při spuštění funkce jazyka JavaScript na plán Premium, měli byste zvolit instanci, která má menší počet virtuálních procesorů. Další informace najdete v tématu [zvolte plány Premium jednojádrový](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Vyhrazené (plánu služby App Service)

Vaše aplikace function App můžete spustit také na stejné vyhrazených virtuálních počítačů jako ostatní aplikace služby App Service (Basic, Standard, Premium a izolované skladové položky).

Vezměte v úvahu plán služby App Service v těchto situacích:

* Máte stávající, nedostatečně využité virtuální počítače, které jsou již spuštěny jiné instance služby App Service.
* Chcete poskytnout vlastní image, ve kterém se spustí vaše funkce.

Platíte stejné aplikace function App v plán služby App Service stejně jako pro ostatní prostředky App Service, jako jsou webové aplikace. Podrobnosti o tom, jak funguje plán služby App Service najdete v tématu [podrobný přehled plánů služby Azure App Service](../app-service/overview-hosting-plans.md).

S plánem služby App Service můžete se ručně škálovat přidáváním dalších instancí virtuálních počítačů. Můžete také povolit automatické škálování. Další informace najdete v tématu [ruční nebo automatické škálování počtu instancí](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Také můžete škálovat výběrem jiný plán služby App Service. Další informace najdete v tématu [vertikální navýšení kapacity aplikace v Azure](../app-service/web-sites-scale.md). 

Při spuštění funkce jazyka JavaScript na plán služby App Service, měli byste zvolit plán, který má menší počet virtuálních procesorů. Další informace najdete v tématu [zvolte plány služby App Service jednojádrový](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

Pokud spustíte v plánu služby App Service, měli byste povolit **vždy na** nastavení tak, aby vaše aplikace function app běží správně. Plán služby App Service modul runtime služby functions začne nečinnosti, po několika minutách neaktivity, takže pouze triggerů HTTP bude "probuzení" vašich funkcí. Vždy v je dostupné jenom v plánu služby App Service. V plánu Consumption platformu automaticky aktivuje aplikace function App.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


I s povolenou funkci Always On, se řídí časový limit spuštění pro jednotlivé funkce `functionTimeout` nastavení [host.json](functions-host-json.md#functiontimeout) souboru projektu.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Určete plán hostování existující aplikace

K určení plánu hostování používají vaši aplikaci function app, naleznete v tématu **plán služby App Service / cenová úroveň** v **přehled** kartu pro odpovídající aplikaci function app [webu Azure portal](https://portal.azure.com). Pro plány služby App Service je také označena cenovou úroveň.

![Zobrazit plán škálování na portálu](./media/functions-scale/function-app-overview-portal.png)

Rozhraní příkazového řádku Azure můžete použít také k určení plánu, následujícím způsobem:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Pokud je výstup tohoto příkazu `dynamic`, aplikace function app je v plánu Consumption. Pokud je výstup tohoto příkazu `ElasticPremium`, aplikace function app je v plánu Premium. Všechny ostatní hodnoty určit různé úrovně plánu služby App Service.

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

Libovolný plán aplikace function app vyžaduje obecný účet úložiště Azure, které podporuje Azure Blob, fronty, soubory a Table storage. Je to proto, že funkce využívají služby Azure Storage pro operace, jako jsou Správa triggerů a protokolování provádění funkcí, ale některé účty úložiště nepodporují fronty a tabulky. Tyto účty, jako je například účty úložiště pouze objektů blob (včetně storage úrovně premium) a účty úložiště pro obecné účely s replikací zónově redundantní úložiště, jsou filtrované na více instancí ze stávajících **účtu úložiště** vybrané možnosti při vytváření aplikace funkcí.

<!-- JH: Does using a Premium Storage account improve perf? -->

Další informace o typech účtů úložiště najdete v tématu [seznámení se službami Azure Storage](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Jak fungují plány consumption a premium

Ve spotřebě a plánech sazeb premium se škáluje infrastruktury Azure Functions tak, že přidáte další instance funkce hostitele, na základě počtu událostí, které její funkce se spouštějí na prostředky procesoru a paměti. Každá instance hostitele funkce v plánu consumption je omezená na 1,5 GB paměti a jeden procesor.  Instance hostitele je celé aplikace function app, to znamená všechny funkce v rámci prostředek funkce aplikace sdílení v rámci instance a škálování služby ve stejnou dobu. Aplikace Function App, které sdílejí stejného plánu consumption se škálovat nezávisle.  V plánu premium velikost vašeho plánu určí dostatek paměti a procesoru u všech aplikací v tomto plánu na příslušné instanci.  

Soubory kódu funkce jsou uložené na sdílených složek Azure v účtu úložiště hlavní funkce. Když odstraníte hlavní účet úložiště z aplikace function app, soubory kódu funkce se odstraní a nejde obnovit.

> [!NOTE]
> Pokud používáte aktivační událost objektů blob v plánu Consumption, může být až 10 minut zpoždění při zpracování nové objekty BLOB. Toto zpoždění nastane, pokud aplikace function app náramků RFID nečinnosti. Po spuštění aplikace function app, objekty BLOB jsou zpracovány okamžitě. Pokud chcete vyhnout tomuto zpoždění dochází úplné spuštění, použijte plán Premium, nebo [trigger služby Event Grid](functions-bindings-event-grid.md). Další informace najdete v tématu [článku odkaz vazby aktivační událost objektů blob](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Škálování prostředí runtime

Služba Azure Functions využívá komponenty s názvem *měřítka řadiče* monitorovat frekvenci událostí a určit, zda má horizontální navýšení kapacity nebo horizontálně. Kontroler škálování pomocí heuristické metody pro každý typ aktivační události. Například pokud používáte aktivační událost Azure Queue storage, škáluje se na základě délky fronty a stáří nejstarší zprávy fronty.

Jednotka škálování pro Azure Functions se aplikace function app. Aplikace function app horizontální navýšení kapacity se další prostředky se přidělují ke spouštění více instancí hostitele Azure Functions. Naopak jako výpočetní snížení poptávky, měřítka řadiče odebere instance hostitele funkce. Počet instancí, které je nakonec vertikálně snížit kapacitu na nulu spuštěné žádné funkce v rámci aplikace function app.

![Škálování řadič událostech monitorování a vytváření instancí](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Porozumění chování škálování

Škálování se může lišit na celé řadě faktorů a škálování různě v závislosti na triggeru a vybraný jazyk. Existuje několik složitými rozhraními škálování chování je potřeba vědět:

* Jedna aplikace funkcí se může škálovat maximálně na 200 instancí. Jednu instanci může zpracovat více než jeden zprávy nebo žádost o najednou, proto není k dispozici nastavte limit počtu souběžných spuštění.
* Aktivace protokolu HTTP nové instance pouze se přidělí nejvýše jednou za každou 1 sekundu.
* Aktivace jiným protokolem než HTTP nové instance pouze se přidělí maximálně každých 30 sekund.

Jiné triggery mohou mít i jiné limity škálování, jakož i zdokumentovaných níže:

* [Centrum událostí](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Osvědčené postupy a vzory pro škálovatelných aplikací

Existuje mnoho aspektů aplikace function app, která ovlivňují, jak dobře provede se škálování, včetně konfigurace hostitele, modul runtime nároky na místo a efektivity prostředků.  Další informace najdete v tématu [škálovatelnost část článku aspekty výkonu](functions-best-practices.md#scalability-best-practices). Musíte mít také přehled o tom, jak se připojení chovat jako vaše funkce škálování aplikace. Další informace najdete v tématu [Správa připojení v Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Model fakturace

Fakturace pro různé plány je podrobně popsaný na [stránce s cenami za Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Využití se agregují na úrovni aplikace funkce a vrátí jenom při spuštění, který je proveden kód funkce. Tady jsou jednotky pro účely fakturace:

* **Spotřeba prostředků v gigabajtsekundách (GB-s)** . Vypočítat jako kombinace velikost paměti a dobu spuštění pro všechny funkce v rámci aplikace function app. 
* **Spuštění**. Počítají při každém spuštění funkce v reakci na aktivační procedura událostí.

Můžete najít užitečné dotazy a informace o tom, jak vysvětlení faktury za spotřebu [na nejčastější dotazy týkající se fakturace](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Omezení služby

Následující tabulka uvádí omezení, které se vztahují na aplikace function App, když běží v různých plány hostování:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
