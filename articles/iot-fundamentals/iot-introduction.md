---
title: Úvod do Azure Internet věcí (IoT)
description: Seznámení s vysvětlením základních služeb Azure IoT a IoT, včetně příkladů, které vám pomůžou ilustrovat používání IoT.
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/11/2018
ms.author: robinsh
ms.openlocfilehash: ce82a2cc4cc936d2e0a7a8b82cbc0ed7e5c6eb52
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048655"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Co je Azure Internet věcí (IoT)?

Internet věcí (IoT) Azure je kolekce cloudových služeb spravovaných Microsoftem, která připojuje, monitoruje a řídí miliardy prostředků IoT. Jednodušeji řečeno se řešení IoT skládá z jednoho nebo více zařízení IoT a jedné nebo více back-endových služeb spuštěných v cloudu, které mezi sebou komunikují. 

Tento článek popisuje základy IoT, rozhovory o případech použití a stručně vysvětluje osm různých dostupných služeb. Když pochopíte, co je k dispozici, můžete zjistit, co chcete podrobněji najít, abyste mohli lépe navrhovat svůj scénář.

## <a name="introduction"></a>Úvod

Hlavní části řešení IoT jsou tyto: zařízení, back-endové služby a komunikace mezi nimi. 

### <a name="iot-devices"></a>Zařízení IoT

Zařízení se obecně skládají z panelu okruhů s připojenými senzory, které se připojují k Internetu. Mnoho zařízení komunikuje přes čip Wi-Fi. Tady je několik příkladů zařízení IoT:

* Senzory tlaku na vzdálené olejové čerpadlo
* Senzory teploty a vlhkosti v klimatizační jednotce
* akcelerometry v výtahu
* Senzory přítomnosti v místnosti

Dvě zařízení, která se často používají k vytváření prototypů, jsou základní MX IoT DevKit od Microsoftu a malinu PI. Čip MX DevKit má integrované senzory pro teplotu, tlak, vlhkost a také pro vybavený gyroskopem a akcelerometr, magnetometer a čip Wi-Fi. Malina PI je zařízení IoT, ke kterému můžete připojovat mnoho různých druhů senzorů, takže můžete vybrat přesně to, co pro svůj scénář potřebujete. 

Další informace o dostupných zařízeních IoT najdete v největším [katalogu zařízení certifikovaných pro IoT](https://catalog.azureiotsolutions.com/alldevices)v oboru.

Sady [SDK pro zařízení IoT](../iot-hub/iot-hub-devguide-sdks.md) umožňují vytvářet aplikace, které se spouštějí na vašich zařízeních, aby mohli provádět potřebné úlohy. Pomocí sad SDK můžete odeslat telemetrii do služby IoT Hub, přijímat zprávy a aktualizace z IoT Hub atd.

### <a name="communication"></a>Komunikace

Vaše zařízení může komunikovat s back-end službami v obou směrech. Tady je několik příkladů způsobů, jak může zařízení komunikovat s back-end řešením.

#### <a name="examples"></a>Příklady 

* Vaše zařízení může posílat teplotu od mobilního chladicího vozíku každých 5 minut až po IoT Hub. 

* Back-endové služba může požádat zařízení, aby odesílala telemetrii častěji, aby vám pomohla diagnostikovat problém. 

* Vaše zařízení může odesílat výstrahy na základě hodnot čtených z jeho senzorů. Pokud například sledujete účastníka v chemickém podniku, může být vhodné Odeslat výstrahu, když teplota překročí určitou hodnotu.

* Vaše zařízení může odesílat informace do řídicího panelu, aby je mohli zobrazit lidé z lidských operátorů. Například řídicí místnost v rámci rafinerie může ukazovat teplotu a tlak každého kanálu a také objem toku přes tento kanál, což umožňuje operátorům jejich sledování. 

Tyto úlohy a další lze implementovat pomocí [sad SDK pro zařízení IoT](../iot-hub/iot-hub-devguide-sdks.md).

#### <a name="connection-considerations"></a>Požadavky na připojení

Největším problémem řešení IoT často je bezpečné a spolehlivé připojení zařízení. Je to proto, že zařízení IoT mají ve srovnání s jinými klienty, jako jsou prohlížeče a mobilní aplikace, rozdílné charakteristiky. Konkrétně zařízení IoT:

* Jsou často integrované systémy bez lidské obsluhy (na rozdíl od telefonu).

* Mohou být nasazená ve vzdálených umístěních, kam je fyzický přístup nákladný.

* Můžou být dostupná jenom prostřednictvím back-endu řešení. Neexistuje jiný způsob práce se zařízením.

* Můžou mít omezené prostředky pro napájení a zpracování.

* Můžou mít přerušované, pomalé nebo nákladné síťové připojení.

* Můžou potřebovat chráněné, vlastní nebo průmyslové protokoly aplikací.

### <a name="back-end-services"></a>Back-endové služby 

Tady jsou některé z funkcí, které může poskytovat back-end služba.

* Příjem telemetrie se škálováním ze zařízení a určení způsobu zpracování a ukládání těchto dat.

* Analýza telemetrie za účelem poskytování přehledů v reálném čase nebo po faktu.

* Posílání příkazů z cloudu na konkrétní zařízení. 

* Zřizování zařízení a řízení zařízení, která se můžou připojovat k vaší infrastruktuře

* Řídit stav zařízení a monitorovat jejich aktivity.

Ve scénáři prediktivní údržby cloudový back-end například ukládá historická telemetrie. Toto řešení používá tato data k identifikaci potenciálně neobvyklého chování konkrétních čerpadel dřív, než způsobí skutečný problém. Pomocí analýzy dat může zjistit, že vhodnou prevencí je odeslat příkaz zpět do zařízení a provést nápravnou akci. Tento proces generuje automatizovanou smyčku zpětné vazby mezi zařízením a cloudem, která výrazně zvyšuje efektivitu řešení.

## <a name="an-iot-example"></a>Příklad IoT

Tady je příklad, jak jedna společnost používala IoT k uložení milionů dolarů. 

Existuje značný Ranch skotu se stovkami tisíc krav. Je to velký způsob, jak udržet přehled o těchto množství krav a o tom, jak se tyto krávy týkají, a vyžaduje hodně řízení. Připojili senzory ke každé jedné krávě, odesílají informace, jako jsou souřadnice GPS a teplota do back-endové služby, které mají být zapsány do databáze.

Pak mají analytickou službu, která kontroluje příchozí data a analyzuje data pro každou krávu a kontroluje například následující otázky:

* Je kráva v průběhu teploty? Jak dlouho má krav běžet teplotu? Pokud je delší než den, získejte souřadnice GPS, najděte krávu a v případě potřeby ho považovat za antibiotika. 

* Je kráva na stejném místě po dobu více než jednoho dne? Pokud ano, získáte souřadnice GPS a získáte krávy. Nedošlo k vzhlíželi krávy? Je v krávě poškozen? Je potřeba, aby tato kráva pomohla? 

Implementace tohoto řešení IoT umožňuje společnosti rychle kontrolovat a zpracovávat krávy a vyjímat dobu potřebnou k tomu, aby využívala kontrolu jejich zvířat, a jejich uložení do značné míry. Další příklady, jak společnosti využívají IoT, najdete v tématu [technické studie Microsoftu pro IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

## <a name="iot-services"></a>Služby IoT

V Azure je několik služeb souvisejících s IoT a může být matoucí zjistit, který z nich chcete použít. Některé, například IoT Central a akcelerátory řešení IoT, poskytují šablony, které vám pomohou vytvořit vlastní řešení a rychle začít. Pomocí dalších služeb, které jsou k dispozici, můžete také plně vyvíjet vlastní řešení – to vše závisí na tom, kolik potřebujete, a kolik ovládacích prvků. Tady je seznam dostupných služeb a také informace o tom, jak je můžete použít pro.

1. [**IoT Central**](../iot-central/core/overview-iot-central.md): Jedná se o aplikační platformu IoT, která zjednodušuje vytváření řešení IoT a pomáhá snižovat zatížení a náklady na operace správy IoT a vývoj. Začněte tím, že vyberete šablonu pro typ zařízení a vytvoříte a otestujete základní IoT Central aplikaci, kterou budou používat operátoři zařízení. Aplikace IoT Central vám také umožní monitorovat zařízení a zřizovat nová zařízení. Tato služba je určená pro přímočarý řešení, která nevyžadují přizpůsobení hloubkové služby.

2. [**Akcelerátory řešení IoT**](/azure/iot-suite): Jedná se o kolekci řešení PaaS, která můžete použít k urychlení vývoje řešení IoT. Začnete s dodaným řešením IoT a pak toto řešení plně upravíte podle svých požadavků. Dovednosti Java nebo .NET potřebujete k přizpůsobení prostředí back-endu a dovedností JavaScriptu pro přizpůsobení vizualizace. 

3. [**IoT Hub**](/azure/iot-hub/): Tato služba umožňuje připojení ze zařízení ke službě IoT Hub a monitorování a řízení miliard zařízení IoT. To je užitečné hlavně v případě, že potřebujete obousměrnou komunikaci mezi zařízeními IoT a vaším back-endu. Toto je základní služba pro IoT Central a akcelerátory řešení IoT. 

4. [**IoT Hub Device Provisioning Service**](/azure/iot-dps/): Toto je pomocná služba pro IoT Hub, kterou můžete použít ke bezpečnému zřízení zařízení ve službě IoT Hub. Pomocí této služby můžete snadno zřizovat miliony zařízení rychle, ale nemusíte je zřídit po jednom. 

5. [**IoT Edge**](/azure/iot-edge/): Tato služba se sestaví nad IoT Hub. Dá se použít k analýze dat na zařízeních IoT, nikoli v cloudu. Přesunutím částí úlohy do hraničního prostředí je třeba odeslat méně zpráv do cloudu. 

6. [**Digitální vlákna Azure**](../digital-twins/index.yml): Tato služba umožňuje vytvářet komplexní modely fyzického prostředí. Můžete modelovat vztahy a interakce mezi lidmi, mezerami a zařízeními. Můžete například předpovědět potřeby údržby pro továrnu, analyzovat požadavky na energii v reálném čase pro elektrickou mřížku nebo optimalizovat využití dostupného místa pro kancelář.

7. [**Time Series Insights**](/azure/time-series-insights): Tato služba umožňuje ukládání, vizualizaci a dotazování velkých objemů dat časových řad generovaných zařízeními IoT. Tuto službu můžete použít s IoT Hub. 

8. [**Azure Maps**](/azure/azure-maps): Tato služba poskytuje geografické informace pro webové a mobilní aplikace. Existuje plná sada rozhraní REST API a také webové ovládací prvky jazyka JavaScript, které lze použít k vytváření flexibilních aplikací pracujících na desktopových nebo mobilních aplikacích pro zařízení se systémem Apple i Windows.

## <a name="next-steps"></a>Další kroky

V případě některých skutečných obchodních případů a používané architektury si přečtěte [Microsoft Azure technické případové studie IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

U některých ukázkových projektů, které si můžete vyzkoušet s IoT DevKit, přečtěte si [katalog projektu IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

Komplexnější vysvětlení různých služeb a jejich použití najdete v tématu [služby a technologie Azure IoT](iot-services-and-technologies.md).

Podrobné informace o architektuře IoT najdete v článku [Referenční architektura IoT v Microsoft Azure](https://aka.ms/iotrefarchitecture).
