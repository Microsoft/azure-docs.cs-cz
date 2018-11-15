---
title: Úvod do Azure Internet of Things (IoT)
description: Seznámení se základy Azure IoT a služby IoT, včetně příkladů, které pomáhají s vysvětlením ilustrují použití IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: 2f690e6c930a1a119c9b7bf4b83ec04a564e47c9
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288743"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Co je Azure Internet of Things (IoT)?

Azure Internet of Things (IoT) je kolekce řízených cloudových služeb, které připojení, sledování a řízení miliard prostředků IoT. Jednodušší řečeno řešení IoT se skládá z jednoho nebo více zařízení IoT a jeden nebo více back endové služby spuštěné v cloudu, které komunikují. 

Tento článek popisuje základní informace o IoT, mluví o případy použití a stručnosti vysvětluje účel k dispozici osm samostatných služeb. Když porozumíte tomu, co je k dispozici, můžete zjistit, co chcete podívat na další úzce a pomáhá s návrhem vaší situaci.

## <a name="introduction"></a>Úvod

Hlavní části řešení IoT, jsou následující: zařízení, back endové služby a komunikace mezi nimi. 

### <a name="iot-devices"></a>Zařízení IoT

Zařízení se obvykle skládá z panelu okruhu s censors připojen, připojte se k Internetu. Mnoho zařízení komunikovat přes Wi-Fi čipu. Tady je několik příkladů zařízení IoT:

* přetížení senzorů čerpadla Vzdálená olej
* senzorů teploty a vlhkosti v jednotce nosníky
* akcelerometrů využívaných v hodnocení
* přítomnost senzory v místnosti

Jsou dvě zařízení, které jsou často používána pro vytváření prototypů základní MX čip TPM. IoT Devkit z Microsoftu a Raspberry PI zařízení. Devkit MX čip TPM mají snímače, které jsou součástí pro teploty, přetížení, vlhkosti, stejně jako volný setrvačník a akcelerometr, magnetometer a k Wi-Fi čipu TPM. Raspberry PI je IoT zařízení, ke kterému může připojit různé druhy senzory, takže můžete vybrat, co přesně potřebujete pro váš scénář. 

[Sad SDK zařízení IoT](../iot-hub/iot-hub-devguide-sdks.md) můžete sestavovat aplikace, které běží na vašich zařízeních, abyste mohli provádět úlohy, je nutné. Spolu se sadami SDK můžete odesílání telemetrických dat do služby IoT hub, přijímat zprávy a aktualizace ze služby IoT Hub a tak dále.

### <a name="communication"></a>Komunikace

Vaše zařízení může komunikovat s back endovým službám v obou směrech. Tady jsou některé příklady způsobů, jak zařízení může komunikovat s back-end řešení.

#### <a name="examples"></a>Příklady 

* Vaše zařízení může odesílat teploty z mobilní chladicí truck každých 5 minut do služby IoT Hub. 

* Back-end služby můžete požádat o zařízení k odesílání telemetrie častěji k diagnostice problému. 

* Vaše zařízení může odesílat výstrahy na základě hodnot číst z jeho senzory. Například pokud monitorování reaktor batch v chemických zařízení, můžete odeslat výstrahu, pokud teplota překročí určitou hodnotu.

* Vaše zařízení odeslat informace, na řídicí panel pro zobrazení lidská obsluha. Řídicí místnosti v výroby může například zobrazit teploty a tlaku každý kanál, jakož i přes tento kanál, operátorů a sledujte událost svazku. 

Tyto úkoly a další, je možné implementovat pomocí [sad SDK zařízení IoT](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Důležité informace o připojení

Největším problémem řešení IoT často je bezpečné a spolehlivé připojení zařízení. Je to proto, že zařízení IoT mají ve srovnání s klienty, například s prohlížeči a mobilními aplikacemi, jiné vlastnosti. Konkrétně zařízení IoT:

* Jsou často integrované systémy bez lidské obsluhy (na rozdíl od telefonu).

* Mohou být nasazená ve vzdálených umístěních, kam je fyzický přístup nákladný.

* Můžou být dostupná jenom prostřednictvím back-endu řešení. Neexistuje jiný způsob práce se zařízením.

* Můžou mít omezené prostředky pro napájení a zpracování.

* Můžou mít přerušované, pomalé nebo nákladné síťové připojení.

* Můžou potřebovat chráněné, vlastní nebo průmyslové protokoly aplikací.

### <a name="back-end-services"></a>Back endovým službám 

Tady jsou některé z funkcí, které nabízí služba back-end.

* Příjem škálované telemetrie ze zařízení a určení, jak zpracovávat a ukládat data.

* Analýza telemetrie s cílem poskytnout přehled v reálném čase nebo po jejich výskytu.

* Odesílání příkazů z cloudu do určitého zařízení. 

* Zřizování zařízení a řídit, které zařízení se můžou připojit k vaší infrastruktuře.

* Řízení stavu svých zařízení a sledovat jejich aktivity.

Například ve scénáři prediktivní údržby, back-end cloudu ukládá historická telemetrická. Toto řešení používá tato data k identifikaci potenciálně neobvyklého chování konkrétních čerpadel dřív, než způsobí skutečný problém. Pomocí analýzy dat může zjistit, že vhodnou prevencí je odeslat příkaz zpět do zařízení a provést nápravnou akci. Tento proces generuje automatizovanou smyčku zpětné vazby mezi zařízením a cloudem, která výrazně zvyšuje efektivitu řešení.

## <a name="an-iot-example"></a>Příklad IoT

Tady je příklad jednoho společnosti umožňuje ušetřit miliony dolarů IoT. 

Existuje obrovské zvířat ranch se stovkami tisíc krav. Je to důležitá záležitost udržovat přehled o daný počet krav a vědět, jak budete dělat a vyžaduje spoustu řízení kolem. Senzorů jsou připojené k každý jednoho krávy informace, jako jsou souřadnice GPS a teploty odesílá do back-end služby k zápisu do databáze.

Pak mají analytické služby, která vyhledá příchozích dat a analyzuje data pro každé krávy ke kontrole následující otázky:

* Běží krávy teplotu? Jak dlouho krávy běžel teplotu? Když po delší dobu než den, získat souřadnice GPS a najít krávy a v případě potřeby zacházet s antibiotik. 

* Je krávy na stejném místě pro více než jeden den? Pokud ano, získat souřadnice GPS a najít krávy přejít. Kleslo krávy mimo cliff? Střelba krávy Potřebuje krávy nápovědu? 

Implementace tohoto řešení IoT přinesla pro společnost zkontrolujte a zpracovávat krávy rychle a omezit množství času se musel věnovat řízení kolem kontrolu jejich zvířata, ukládá je spoustu peněz. Další příklady reálných zkušeností jak společnosti využívají IoT, naleznete v tématu [Microsoft technické případové studie pro IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>Služby IoT

Existuje několik služeb souvisejících s IoT v Azure a může být matoucí zjistit, které z nich chcete použít. Některé nástroje, například IoT Central a akcelerátory řešení IoT, poskytují šablony, které vám pomůžou vytvářet vlastní řešení a rychlé zahájení práce. Můžete také plně vyvíjet vlastní řešení s použitím jiných služeb, které jsou k dispozici – to všechno závisí na tom, kolik Nápověda chcete a jak velkou kontrolu. Tady je seznam služeb k dispozici, a také jak je můžete používat pro.

1. [**IoT Central**](../iot-central/overview-iot-central.md): to je řešení SaaS, která umožňuje připojení, monitorování a správa zařízení ioT. Pokud chcete začít, vyberte šablonu pro váš typ zařízení a vytvoříte a otestujete základní aplikace IoT Central, který bude používat operátory zařízení. Aplikace IoT Central vám také umožní sledovat zařízení a zřizovat nová zařízení. Tato služba je jednoduché řešení, která nevyžadují rozsáhlá přizpůsobení služeb. 

2. [**Akcelerátory řešení IoT**](/azure/iot-suite): jde o kolekci řešení PaaS můžete použít k urychlení vývoje řešení IoT. Začněte s zadané řešení IoT a plně přizpůsobit řešení vašim požadavkům. Potřebujete znalosti Javy nebo .NET k přizpůsobení back endu a znalosti JavaScriptu k přizpůsobení vizualizace. 

3. [**IoT Hub**](/azure/iot-hub/): Tato služba umožňuje připojení ze zařízení do služby IoT hub a sledování a řízení miliard zařízení IoT. To je obzvláště užitečné, pokud potřebujete obousměrnou komunikaci mezi zařízeními IoT a back-endu. Toto je základní služby pro IoT Central a akcelerátory řešení IoT. 

4. [**IoT Hub Device Provisioning Service**](/azure/iot-dps/): Toto je pomocná služba pro IoT Hub, která vám umožní bezpečně zřizovat zařízení do služby IoT hub. Pomocí této služby se kterou jednoduše zprovozníte miliony zařízení rychle, místo zřizování je jeden po druhém. 

5. [**IoT Edge**](/azure/iot-edge/): Tato služba postavená na službě IoT Hub. Slouží k analýze dat na zařízeních IoT, nikoli v cloudu. Díky přesunu částí úlohy do hraničních zařízení, třeba méně zpráv k odeslání do cloudu. 

6. [**Azure digitální dvojče**](/azure/azure-digital-twins/): Tato služba umožňuje vytvářet komplexní modely fyzických prostředí. Lze modelovat vztahy a interakce mezi lidmi, mezery a zařízení. Například můžete předpovídat údržby musí objekt pro vytváření, analýza v reálném čase energie požadavky k elektrické mřížky nebo optimalizovat využití volného místa pro office.

7. [**Time Series Insights**](/azure/time-series-insights): Tato služba umožňuje ukládat, vizualizaci a dotazování velkých objemů dat časových řad generované zařízeními IoT. Pomocí služby IoT Hub můžete použít tuto službu. 

8. [**Azure Maps**](/azure/azure-maps): Tato služba poskytuje zeměpisné údaje pro webové a mobilní aplikace. Zde je kompletní sadu rozhraní REST API, jakož i doprovázenou webovým ovládáním jazyka JavaScript, který slouží k vytváření flexibilních aplikací, které pracují na desktopové nebo mobilní aplikace pro zařízení Apple i Windows.

## <a name="next-steps"></a>Další postup

Některé samotných obchodních případů a architektury používané najdete v tématu [Microsoft Azure IoT technické případové studie](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Některé ukázkové projekty, které si můžete vyzkoušet s IoT DevKit, najdete v článku [IoT DevKit projektu katalogu](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Komplexnější vysvětlení různých služeb a jak se používají, najdete v článku [technologie a služby Azure IoT](iot-services-and-technologies.md).

Podrobné informace o architektuře IoT najdete v článku [Referenční architektura IoT v Microsoft Azure](https://aka.ms/iotrefarchitecture).
