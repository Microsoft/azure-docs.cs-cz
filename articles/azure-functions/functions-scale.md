---
title: Azure Functions hostování a škálování | Dokumentace Microsoftu
description: Zjistěte, jak si vybrat mezi plán Azure Functions Consumption a plán služby App Service.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure funkce, funkce, plán consumption, plán služby app service, zpracování událostí, webhooky, dynamické výpočty, architektura bez serveru
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 08/09/2018
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7168db80593426977e238fa2b8a5f09638cf0fdf
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542155"
---
# <a name="azure-functions-scale-and-hosting"></a>Hostování a škálování Azure Functions

Spuštění Azure Functions ve dvou různých režimech: plán Consumption a plán služby App Service. Plán Consumption automaticky přiděluje výpočetní výkon, pokud váš kód běží. Vaše aplikace je škálovat na více systémů v případě potřeby pro zpracování zátěže a kapacitu vertikálně snížit, když kód není spuštěný. Nemusíte platit za nečinných virtuálních počítačů nebo záložní kapacita předem. Tento článek se zaměřuje na plán Consumption [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) modelu aplikací. Podrobnosti o tom, jak funguje vyhrazený plán služby App Service najdete v tématu [podrobný přehled plánů služby Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

> [!NOTE]  
> [Hostování v Linuxu](functions-create-first-azure-function-azure-cli-linux.md) je momentálně dostupná jenom na plán služby App Service.

Pokud nejste obeznámeni s využitím Azure Functions, přečtěte si článek [přehled Azure Functions](functions-overview.md).

Když vytvoříte aplikaci function app, výběr plánu hostování pro funkce v aplikaci. V obou plánu instance *hostitele Azure Functions* provede funkce. Typ plánu ovládacích prvků:

* Jak hostovat instance při horizontálním škálování.
* Prostředky, které jsou k dispozici pro každého hostitele.

> [!IMPORTANT]
> Je nutné vybrat typ hostování plán při vytváření aplikace function app. Později ho nelze změnit.

V plánu služby App Service můžete škálovat mezi vrstvami k přidělování různých množství prostředků. Azure Functions v plánu Consumption automaticky zpracovává všechny přidělení prostředků. 

## <a name="consumption-plan"></a>Plán Consumption

Pokud používáte plán Consumption, instance hostitele Azure Functions dynamicky přidávají nebo odebírají na základě počtu příchozích událostí. Tento plán bez serveru se automaticky škáluje. proto vám budou účtovány za výpočetní prostředky, pouze když vaše funkce běží. V plánu Consumption vyprší časový limit po nastaveném časovém nastavení provádění funkce.

> [!NOTE]
> Výchozí hodnota časového limitu pro funkce v plánu Consumption je 5 minut. Je možné zvýšit hodnotu pro aplikaci Function App až do maximálního počtu 10 minut tak, že změníte vlastnost `functionTimeout` v [host.json](functions-host-json.md#functiontimeout) souboru projektu.

Fakturace vychází z počtu spuštění, čas spuštění a paměť použitá. Fakturace se agreguje přes všechny funkce v rámci aplikace function app. Další informace najdete v tématu [stránce s cenami za Azure Functions].

Plán Consumption je výchozí plán hostování a nabízí následující výhody:

* Platí, pouze když vaše funkce běží.
* Automatické horizontální navýšení kapacity, i během období vysoké zatížení.

## <a name="app-service-plan"></a>Plán služby App Service

V vyhrazený plán služby App Service spusťte vaše aplikace function App na vyhrazených virtuálních počítačích na Basic, Standard, Premium a izolované SKU, která je stejná jako ostatní aplikace služby App Service. Vyhrazené virtuální počítače se přidělují aplikaci function App, což znamená, že hostitel funkce může být [nepřetržitý provoz](#always-on). Plány služby App Service podporují Linux.

Vezměte v úvahu plán služby App Service v těchto případech:

* Máte stávající, nedostatečně využité virtuální počítače, které jsou již spuštěny jiné instance služby App Service.
* Vaše aplikace function App spouštět nepřetržitě téměř průběžně. Plán služby App Service v takovém případě může být cenově výhodnější.
* Budete potřebovat další možnosti procesoru nebo paměti, než je zadán v plánu Consumption.
* Váš kód je potřeba spustit delší než maximální doba spuštění povolena v plánu Consumption, což je až 10 minut.
* Vyžadujete funkce, které jsou dostupné jenom pro plán služby App Service, jako třeba podporu pro App Service Environment, virtuální sítě nebo VPN připojení a větší velikosti virtuálních počítačů.
* Chcete spustit aplikaci function app v Linuxu nebo chcete poskytnout vlastní image, ve kterém se spustí vaše funkce.

Virtuální počítač ze počet spuštění, čas spuštění a paměť použitá odděluje obě části. V důsledku toho nebude platit víc než náklady na instance virtuálních počítačů, kterou přidělíte. Podrobnosti o tom, jak funguje plán služby App Service najdete v tématu [podrobný přehled plánů služby Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

S plánem služby App Service můžete ručně škálovat přidáváním dalších instancí virtuálních počítačů, nebo můžete povolit automatické škálování. Další informace najdete v tématu [ruční nebo automatické škálování počtu instancí](../monitoring-and-diagnostics/monitoring-autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Také můžete škálovat výběrem jiný plán služby App Service. Další informace najdete v tématu [vertikální navýšení kapacity aplikace v Azure](../app-service/web-sites-scale.md). 

Při spuštění funkce jazyka JavaScript na plán služby App Service, měli byste zvolit plán, který má menší počet virtuálních procesorů. Další informace najdete v tématu [zvolte plány služby App Service jednojádrový](functions-reference-node.md#considerations-for-javascript-functions).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### <a name="always-on"></a>Stálé připojení

Pokud spustíte v plánu služby App Service, měli byste povolit **vždy na** nastavení tak, aby vaše aplikace function app běží správně. Plán služby App Service modul runtime služby functions začne nečinnosti, po několika minutách neaktivity, takže pouze triggerů HTTP bude "probuzení" vašich funkcí. Vždy v je dostupné jenom v plánu služby App Service. V plánu Consumption platformu automaticky aktivuje aplikace function App.

## <a name="what-is-my-hosting-plan"></a>Co je Můj plán hostování

K určení plánu hostování používají vaši aplikaci function app, naleznete v tématu **plán služby App Service / cenová úroveň** v **přehled** kartu pro odpovídající aplikaci function app [webu Azure portal](https://portal.azure.com). Pro plány služby App Service je také označena cenovou úroveň. 

![Zobrazit plán škálování na portálu](./media/functions-scale/function-app-overview-portal.png)

Rozhraní příkazového řádku Azure můžete použít také k určení plánu, následujícím způsobem:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Pokud je výstup tohoto příkazu `dynamic`, aplikace function app je v plánu Consumption. Všechny ostatní hodnoty znamenat úrovně plánu služby App Service.

I s povolenou funkci Always On, se řídí časový limit spuštění pro jednotlivé funkce `functionTimeout` nastavení [host.json](functions-host-json.md#functiontimeout) souboru projektu.

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště

V plánu Consumption a plán služby App Service aplikace function app vyžaduje obecný účet úložiště Azure, které podporuje Azure Blob, fronty, soubory a Table storage. Je to proto, že funkce spoléhá na Azure Storage pro operace, jako jsou Správa triggerů a protokolování provádění funkcí, ale některé účty úložiště nepodporují fronty a tabulky. Tyto účty, jako je například účty úložiště pouze objektů blob (včetně storage úrovně premium) a účty úložiště pro obecné účely s replikací zónově redundantní úložiště, jsou filtrované na více instancí ze stávajících **účtu úložiště** vybrané možnosti při vytváření aplikace funkcí.

<!-- JH: Does using a Premium Storage account improve perf? -->

Další informace o typech účtů úložiště najdete v tématu [seznámení se službami Azure Storage](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Jak funguje plán Consumption

V plánu Consumption měřítka řadiče automaticky škáluje tak, že přidáte další instance funkce hostitele, na základě počtu událostí, které její funkce se spouštějí na prostředky procesoru a paměti. Každá instance hostitele funkce je omezená na 1,5 GB paměti.  Instance hostitele je aplikace function app, to znamená všechny funkce v rámci prostředek funkce aplikace sdílení v rámci instance a škálování služby ve stejnou dobu. Aplikace Function App, které sdílejí stejného plánu Consumption se škálovat nezávisle.  

Pokud použijete plán hostování Consumption, soubory kódu funkce jsou uložené na sdílených složek Azure v účtu úložiště hlavní funkce. Když odstraníte hlavní účet úložiště z aplikace function app, soubory kódu funkce se odstraní a nejde obnovit.

> [!NOTE]
> Pokud používáte aktivační událost objektů blob v plánu Consumption, může být až 10 minut zpoždění při zpracování nové objekty BLOB. Toto zpoždění nastane, pokud aplikace function app náramků RFID nečinnosti. Po spuštění aplikace function app, objekty BLOB jsou zpracovány okamžitě. Pokud chcete vyhnout tomuto zpoždění dochází úplné spuštění, použijte plán služby App Service s **Always On** povolena, nebo použít trigger služby Event Grid. Další informace najdete v tématu [článku odkaz vazby aktivační událost objektů blob](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Škálování prostředí runtime

Služba Azure Functions využívá komponenty s názvem *měřítka řadiče* monitorovat frekvenci událostí a určit, zda má horizontální navýšení kapacity nebo horizontálně. Kontroler škálování pomocí heuristické metody pro každý typ aktivační události. Například pokud používáte aktivační událost Azure Queue storage, škáluje se na základě délky fronty a stáří nejstarší zprávy fronty.

Jednotka škálování je aplikace function app. Aplikace function app horizontální navýšení kapacity se další prostředky se přidělují ke spouštění více instancí hostitele Azure Functions. Naopak jako výpočetní snížení poptávky, měřítka řadiče odebere instance hostitele funkce. Počet instancí, které je nakonec vertikálně snížit kapacitu na nulu spuštěné žádné funkce v rámci aplikace function app.

![Škálování řadič událostech monitorování a vytváření instancí](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Porozumění chování škálování

Škálování se může lišit na celé řadě faktorů a škálování různě v závislosti na triggeru a vybraný jazyk. Několik aspektů škálování, díky kterému se však v systému existuje ještě dnes:

* Aplikace s jedinou funkci se pouze škálování až na 100 instancí. Jednu instanci může zpracovat více než jeden zprávy nebo žádost o najednou, proto není k dispozici nastavte limit počtu souběžných spuštění.
* Nové instance pouze se přidělí maximálně každých 10 sekund.

Jiné triggery mohou mít i jiné limity škálování, jakož i zdokumentovaných níže:

* [Centrum událostí](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Osvědčené postupy a vzory pro škálovatelných aplikací

Existuje mnoho aspektů aplikace function app, která ovlivňují, jak dobře provede se škálování, včetně konfigurace hostitele, modul runtime nároky na místo a efektivity prostředků.  Další informace najdete v tématu [škálovatelnost část článku aspekty výkonu](functions-best-practices.md#scalability-best-practices). Musíte mít také přehled o tom, jak se připojení chovat jako vaše funkce škálování aplikace. Další informace najdete v tématu [Správa připojení v Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Model fakturace

Fakturace pro plán Consumption je podrobně popsaný na [stránce s cenami za Azure Functions]. Využití se agregují na úrovni aplikace funkce a vrátí jenom při spuštění, který je proveden kód funkce. Tady jsou jednotky pro účely fakturace:

* **Spotřeba prostředků v gigabajtsekundách (GB-s)**. Vypočítat jako kombinace velikost paměti a dobu spuštění pro všechny funkce v rámci aplikace function app. 
* **Spuštění**. Počítají při každém spuštění funkce v reakci na aktivační procedura událostí.

[Stránce s cenami za Azure Functions]: https://azure.microsoft.com/pricing/details/functions
