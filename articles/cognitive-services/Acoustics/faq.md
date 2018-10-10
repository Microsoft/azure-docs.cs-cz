---
title: Nejčastější dotazy ohledně Akustika projektu
titlesuffix: Azure Cognitive Services
description: Tato stránka nabízí odpovědi na často kladené Akustika projektu, včetně otázky pokyny ke stažení a vytvoření procesu.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: b8b5ea39c1cfb3a37a8e3009f59312728bcb0331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900389"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

## <a name="what-is-project-acoustics"></a>Co je Project Acoustics?

Modul plug-in Akustika Unity projektu je Akustika systém, který vypočítá zvuk wave chování před runtime podobají statické osvětlení. Cloud nemá rutinní wave fyzika výpočtů, tedy nízké náklady na modul runtime procesoru.  

## <a name="where-can-i-download-the-plugin"></a>Kde lze stáhnout modul plug-in?

Pokud si chcete vyzkoušet akustický modul plug-in, [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) se můžete zaregistrovat k verzi Designer Preview.

## <a name="is-azure-used-at-runtime"></a>Azure používá za běhu?

Ne, cloudovou integraci se používá pouze během fáze precompute jako součást instalace scény.
 
## <a name="what-is-simulation-input"></a>Co je simulace vstup? 

Vstup simulace je 3D scény, virtuální prostředí nebo herní úroveň. Projekt Akustika provádí 3D odměrné wave simulace, které model fyzika zvuk přesně dodržovali, včetně smooth uzavření a rozptyl.
 
## <a name="what-is-the-runtime-cost"></a>Co je běhové náklady?

Akustika trvá přibližně na 0,01 % využití CPU jeden zdroj pro každý snímek. Využití paměti RAM závisí na velikosti scény a může být v rozsahu od 10 do 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Je potřeba zjednodušit úrovně geometrie? Počet trojúhelník ovládací prvek? Ujistěte se, mřížek vodotěsné?

Ne. Systém bude ingestovat podrobné úrovni geometrie přímo. Bude voxelized pro vnitřní zpracování.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Novinky ve vyhledávací tabulce modulu runtime

ACE soubor představuje tabulku akustický parametrů mezi mnoha dvojice umístění zdroje a naslouchacího procesu.
 
## <a name="can-it-handle-moving-sources"></a>Dokáže zpracovat přesunutí zdrojů?

Ano, **Microsoft Acoustics** modulu plug-in spatializer Unity consults vyhledávací tabulky na jednotlivé takty zpracování zvuku s aktuální umístění zdroje a naslouchacího procesu. Spatializer DSP plynule aktualizuje akustický zpracování parametrů na jednotlivé takty.
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Dokáže zpracovat dynamické geometrie? Zavření dveře? Stěn úžasné hned?

Ne. Akustický parametry jsou-li předpočítané na základě statické stavu her úroveň. Můžeme navrhnout opuštění dveře geometrie mimo Akustika a pak použít další uzavření na základě stavu zničitelné a přesouvatelný herním objektům, které jsou zavedené technik.
 
## <a name="does-it-handle-materials"></a>Zpracování materiály?

Ano. Materiály se vybírají z fyzických materiálu názvů ve vaší úrovni řízení absorptivity.
 
## <a name="what-do-the-probes-represent"></a>Co představují "sond"?

Testy jsou novým možné player umístění. Každý test představuje samostatný wave simulace scény pocházející z umístění testu. Za běhu jsou interpolovány akustický parametry pro naslouchací proces umístění z nejbližší umístění testu.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Proč trávit tolik výpočty v cloudu? Co ji zakoupit mě?

Projekt Akustika poskytuje přesným a spolehlivým akustický parametry i pro mimořádně složité virtuální prostředí, s ohledem všechny aspekty architektury. Nabízí hladký uzavření/překážka bez ruční práce a dynamické dozvuku variace bez kreslení svazky. Vše se zbývající světla na procesoru za běhu.

## <a name="what-exactly-happens-during-baking"></a>Co se stane přesně během "pečení"?

Systém bere v úvahu potenciální umístění player generují sadu rovnoměrně rozmístěné "probe" Ukázkový pozic. Která má označení vytvoření pro úroveň se skládá z nezávislých úloh, které pro každý test: systém bere v úvahu cuboid "Simulace oblasti" na střed v testu a nemá podrobné wave simulace v dané oblasti až 25 cm rozlišením.

## <a name="next-steps"></a>Další kroky
* Prozkoumejte [ukázková Scéna](sample-walkthrough.md)

