---
title: Nejčastější dotazy ohledně Akustika – Cognitive Services
description: Tato stránka nabízí odpovědi na často kladené Akustika projektu, včetně otázky pokyny ke stažení a vytvoření procesu.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 7e27b5117f5f0ea98916ecfefcbb0dac9323e1d9
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181496"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

## <a name="what-is-project-acoustics"></a>Co je Akustika projektu?

Modul plug-in Akustika Unity projektu je Akustika systém, který vypočítá zvuk wave chování před runtime podobají statické osvětlení. Cloud nemá rutinní wave fyzika v době návrhu, tedy nízké náklady na modul runtime procesoru.  

## <a name="where-can-i-download-the-plugin"></a>Kde lze stáhnout modul plug-in?

Pokud vás zajímá vyhodnocení modulu plug-in Akustika, zaregistrujte [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) získat verzi Preview návrháře.

## <a name="is-azure-used-at-runtime"></a>Azure používá za běhu?

Ne, cloudovou integraci se používá pouze ve fázi precompute v době návrhu.
 
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

