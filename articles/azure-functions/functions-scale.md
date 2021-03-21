---
title: Hostování a škálování Azure Functions
description: Naučte se vybírat mezi plánem Azure Functions spotřebu a plánem Premium.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936753"
---
# <a name="azure-functions-hosting-options"></a>Azure Functions možností hostování

Když vytvoříte aplikaci Function App v Azure, musíte zvolit plán hostování pro vaši aplikaci. K dispozici jsou tři základní plány hostování pro Azure Functions: [plán spotřeby](consumption-plan.md), [Plán Premium](functions-premium-plan.md)a [vyhrazený plán (App Service)](dedicated-plan.md). Všechny plány hostování jsou obecně dostupné (GA) na virtuálních počítačích se systémy Linux a Windows.

Plán hostování, který zvolíte, bude určovat následující chování:

* Jak se škáluje aplikace Function App
* Prostředky dostupné pro jednotlivé instance aplikace Function App.
* Podpora pokročilých funkcí, jako je například připojení k Azure Virtual Network.

Tento článek poskytuje podrobné porovnání různých plánů hostování spolu s hostováním založeném na Kubernetes.

## <a name="overview-of-plans"></a>Přehled plánů

Následuje souhrn výhod tří hlavních plánů hostování pro funkce:

| | |
| --- | --- |  
|**[Plán Consumption](consumption-plan.md)**| Automatické škálování a Plaťte jenom za výpočetní prostředky, když jsou vaše funkce spuštěné.<br/><br/>V plánu spotřeby se instance hostitele Functions dynamicky přidávají a odstraňují na základě počtu příchozích událostí.<br/><br/> ✔ Výchozí plán hostování.<br/>Plaťte ✔ jenom v případě, že jsou vaše funkce spuštěné.<br/>✔ Škáluje automaticky, a to i během období vysokého zatížení.|  
|**[Plán Premium](functions-premium-plan.md)**|Automatické škálování na základě poptávky pomocí předem zadržených pracovních procesů, které spouštějí aplikace bez prodlevy po nečinnosti, běží na výkonnějších instancích a připojuje se k virtuálním sítím. <br/><br/>Vezměte v úvahu plán Azure Functions Premium v následujících situacích: <br/><br/>✔ Vaše aplikace Function App běží nepřetržitě nebo téměř nepřetržitě.<br/>✔ Máte vysoký počet malých spuštění a vysoké vykonání vyúčtování, ale v plánu spotřeby je málo sekund.<br/>✔ Budete potřebovat více možností procesoru nebo paměti, než jaké poskytuje plán spotřeby.<br/>✔ Váš kód musí běžet delší dobu, než je maximální doba běhu povolená v plánu spotřeby.<br/>✔ Vyžadujete funkce, které nejsou dostupné v plánu spotřeby, jako je třeba připojení k virtuální síti.|  
|**[Plán Dedicated](dedicated-plan.md)** |Spusťte své funkce v rámci plánu App Service v pravidelných [App Servicech](https://azure.microsoft.com/pricing/details/app-service/windows/)tarifech.<br/><br/>Je nejvhodnější pro dlouhotrvající scénáře, kdy [Durable Functions](durable/durable-functions-overview.md) nelze použít. Vezměte v úvahu App Service plán v následujících situacích:<br/><br/>✔ Máte existující, nevyužité virtuální počítače, které už používají jiné instance App Service.<br/>✔ Chcete zadat vlastní image, na které se mají spouštět vaše funkce. <br/>✔ Prediktivní škálování a náklady jsou požadovány.|  

Srovnávací tabulky v tomto článku také obsahují následující možnosti hostování, které poskytují nejvyšší množství řízení a izolace, ve kterém se spouštějí aplikace Function App.  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service Environment (pomocného mechanismu) je funkce App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění App Service aplikací ve velkém měřítku.<br/><br/>Služby ASE jsou vhodné pro úlohy aplikací, které vyžadují: <br/><br/>✔ Velmi vysokého měřítka.<br/>✔ Úplnou izolaci výpočtů a zabezpečení přístupu k síti.<br/>✔ Vysoké využití paměti.|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes poskytuje plně izolované a vyhrazené prostředí běžící nad platformou Kubernetes.<br/><br/> Kubernetes je vhodný pro úlohy aplikací, které vyžadují: <br/>✔ Požadavky na vlastní hardware.<br/>✔ Izolaci a zabezpečení přístupu k síti.<br/>✔ Schopnost spouštět v hybridním nebo multi-cloudovém prostředí.<br/>✔ Běžet společně se stávajícími aplikacemi a službami Kubernetes.|  

Zbývající tabulky v tomto článku porovnávají plány různých funkcí a chování. Srovnání nákladů mezi plány dynamického hostování (spotřeba a Premium) najdete na stránce s [cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Ceny různých možností vyhrazeného plánu najdete na [stránce s cenami App Service](https://azure.microsoft.com/pricing/details/app-service/windows/). 

## <a name="operating-systemruntime"></a>Operační systém/modul runtime

Následující tabulka uvádí podporované operační systémy a podporu jazykového modulu runtime pro plány hostování.

| | Linux<sup>1</sup><br/>Pouze kód | Systém Windows<sup>2</sup><br/>Pouze kód | Linux<sup>1, 3</sup><br/>Kontejner Docker |
| --- | --- | --- | --- |
| **[Plán Consumption](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | Bez podpory  |
| **[Plán Premium](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[Plán Dedicated](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | Není k dispozici | Není k dispozici |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux je jediným podporovaným operačním systémem pro zásobník modulu runtime Pythonu. <br/>
<sup>2</sup> Windows je jediným podporovaným operačním systémem běhového zásobníku PowerShellu.<br/>
<sup>3</sup> Linux je jediným podporovaným operačním systémem kontejnerů Docker.<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>Měřítko

Následující tabulka porovnává chování různých plánů hostování.

| | Horizontální navýšení kapacity | Maximální počet instancí |
| --- | --- | --- |
| **[Plán Consumption](consumption-plan.md)** | [Řízený událost](event-driven-scaling.md). Horizontální navýšení kapacity, a to i během období vysokého zatížení. Azure Functions infrastruktura škáluje prostředky procesoru a paměti přidáním dalších instancí hostitele Functions na základě počtu událostí příchozích triggerů. | 200 |
| **[Plán Premium](functions-premium-plan.md)** | [Řízený událost](event-driven-scaling.md). Horizontální navýšení kapacity, a to i během období vysokého zatížení. Azure Functions infrastruktura škáluje prostředky procesoru a paměti přidáním dalších instancí hostitele Functions na základě počtu událostí, na kterých se spouští jeho funkce. |100|
| **[Vyhrazený plán](dedicated-plan.md)**<sup>1</sup> | Ruční nebo automatické škálování |10-20|
| **[Pomocného mechanismu](dedicated-plan.md)**<sup>1</sup> | Ruční nebo automatické škálování |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | Automatické škálování založené na událostech pro clustery Kubernetes s využitím [keda](https://keda.sh) | Liší se &nbsp; podle &nbsp; clusteru&nbsp;&nbsp;|

<sup>1</sup> Pokud chcete určit omezení pro různé možnosti plánu App Service, přečtěte si [omezení App Service plánu](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

## <a name="cold-start-behavior"></a>Chování studeného startu

|    |    | 
| -- | -- |
| **[&nbsp;Plán spotřeby](consumption-plan.md)** | Aplikace se můžou při nečinnosti škálovat na nulu, což znamená, že některé požadavky můžou při spuštění mít další latenci.  Plán spotřeby má několik optimalizací, které vám pomůžou snižovat čas na studený start, včetně přijímání předem zatepléch zástupných funkcí, které už mají spuštěné hostitele funkcí a jazyka. |
| **[Plán Premium](functions-premium-plan.md)** | Trvalé zahřívání instancí, aby nedocházelo k žádnému studenému startu. |
| **[Plán Dedicated](dedicated-plan.md)** | Při spuštění ve vyhrazeném plánu může hostitel služby Functions běžet průběžně, což znamená, že se ve skutečnosti nejedná o problém s studeným startem. |
| **[ASE](dedicated-plan.md)** | Při spuštění ve vyhrazeném plánu může hostitel služby Functions běžet průběžně, což znamená, že se ve skutečnosti nejedná o problém s studeným startem. |
| **[Kubernetes](functions-kubernetes-keda.md)**  | V závislosti na konfiguraci KEDA se dají aplikace nakonfigurovat tak, aby se zabránilo studenému startu. Pokud je konfigurace nastavena na hodnotu nula, je pro nové události k dispočátečnímu startu. 

## <a name="service-limits"></a>Omezení služby

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>Síťové funkce

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>Fakturace

| | | 
| --- | --- |
| **[Plán Consumption](consumption-plan.md)** | Platíte jenom za čas, kdy se vaše funkce spouštějí. Fakturace vychází z počtu spuštění, doby spuštění a použité paměti. |
| **[Plán Premium](functions-premium-plan.md)** | Plán Premium je založený na počtu základních sekund a paměti využitých v případě potřeby a předem zahřívání instancí. Alespoň jedna instance v každém plánu musí být zachována v zahřívání. Tento plán poskytuje nejpředvídatelné ceny. |
| **[Vyhrazený plán](dedicated-plan.md)* | Totéž platí pro aplikace Function App v plánu App Service, stejně jako u jiných prostředků App Service, jako jsou například webové aplikace.|
| **[App Service Environment (pomocného mechanismu)](dedicated-plan.md)** | Pro pomocného objekt pro řízení se platí paušální měsíční sazba, která platí pro infrastrukturu a která se nemění podle velikosti pomocného mechanismu. K dispozici je také cena za App Service plán vCPU. Všechny aplikace hostované ve službě ASE jsou ve skladové položce s izolovanou cenou. |
| **[Kubernetes](functions-kubernetes-keda.md)**| Platíte jenom náklady na váš cluster Kubernetes; žádné další fakturace pro funkce. Vaše aplikace Function App funguje v rámci clusteru jako úloha aplikace, stejně jako u běžné aplikace. |

## <a name="next-steps"></a>Další kroky

+ [Technologie nasazení v Azure Functions](functions-deployment-technologies.md) 
+ [Příručka pro vývojáře v Azure Functions](functions-reference.md)