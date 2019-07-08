---
title: Projekt Akustika – nejčastější dotazy
titlesuffix: Azure Cognitive Services
description: Tato stránka nabízí odpovědi na často kladené Akustika projektu, včetně otázky pokyny ke stažení a vytvoření procesu.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 6e979db29f4a223b61580c48101c0d242fdbebbf
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616337"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Projekt Akustika – nejčastější dotazy

## <a name="what-is-project-acoustics"></a>Co je Project Acoustics?

Projekt Akustika sadu modulů plug-in je Akustika systém, který vypočítá zvuk wave chování před runtime podobají statické osvětlení. Cloud nemá rutinní wave fyzika výpočtů, tedy nízké náklady na modul runtime procesoru.  

## <a name="where-can-i-download-the-plugin"></a>Kde lze stáhnout modul plug-in?

Si můžete stáhnout [modulu plug-in Unity projektu Akustika](https://www.microsoft.com/download/details.aspx?id=57346) nebo [projektu Akustika Unreal modulu plug-in](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Podporuje projektu Akustika &lt;x&gt; platformy?

Podpora platformy projektu Akustika vyvíjí podle potřeb zákazníků. Kontaktujte nás prosím na [projektu Akustika fóra](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics) mohli dotázat na podporu pro další platformy.

## <a name="is-azure-used-at-runtime"></a>Azure používá za běhu?

Ne, cloudovou integraci se používá pouze během fáze precompute jako součást instalace scény.
 
## <a name="what-is-simulation-input"></a>Co je simulace vstup? 

Vstup simulace je 3D scény, virtuální prostředí nebo herní úroveň. Projekt Akustika provádí 3D odměrné wave simulace, které model fyzika zvuk přesně dodržovali, včetně smooth uzavření a rozptyl.
 
## <a name="what-is-the-runtime-cost"></a>Co je běhové náklady?

Akustika trvá přibližně na 0,01 % využití CPU jeden zdroj pro každý snímek. Využití paměti RAM závisí na velikosti scény a může být v rozsahu od 10 do 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Je potřeba zjednodušit úrovně geometrie? Počet trojúhelník ovládací prvek? Ujistěte se, mřížek vodotěsné?

Ne. Systém bude ingestovat podrobné úrovni geometrie přímo. Bude voxelized pro vnitřní zpracování.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Novinky ve vyhledávací tabulce modulu runtime

Zahrnutí souborů ACE je tabulka akustický parametrů mezi mnoha zdroj a umístění páry naslouchací proces, jakož i geometrie scény voxelized použitý pro parametr interpolace.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Dokáže zpracovat projektu Akustika přesunutí zdrojů?

Ano, projekt Akustika consults vyhledávací tabulky a aktualizuje DSP zvuku na jednotlivé takty, takže mohou zpracovat přesunutí zdrojů a naslouchacího procesu.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Dokáže zpracovat Akustika projektu dynamické geometrie? Zavření dveře? Stěn úžasné hned?

Ne. Akustický parametry jsou-li předpočítané na základě statické stavu her úroveň. Doporučujeme ponechat dveře geometrie mimo Akustika a potom se použijí na základě stavu zničitelné další uzavření a nastavení přesouvatelný herním objektům pomocí techniky.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Používá projektu Akustika akustický materiály?

Ano. Materiály se vybírají z fyzických materiálu názvů ve vaší úrovni řízení absorptivity.
 
## <a name="what-do-the-probes-represent"></a>Co představují "sond"?

Testy jsou novým možné player umístění. Každý test představuje samostatný wave simulace scény pocházející z umístění testu. Za běhu jsou interpolovány akustický parametry pro naslouchací proces umístění z nejbližší umístění testu.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Proč trávit tolik výpočty v cloudu? Co ji zakoupit mě?

Projekt Akustika poskytuje přesným a spolehlivým akustický parametry i pro mimořádně složité virtuální prostředí, s ohledem všechny aspekty architektury. Poskytuje smooth uzavření a překážky a dynamické dozvuku varianta bez dělat něco ručně kreslení svazky. Vše se zbývající světla na procesoru za běhu.

## <a name="what-exactly-happens-during-baking"></a>Co se stane přesně během "pečení"?

Která má označení vytvoření se skládá z akustický wave simulace cuboid simulace oblastí střed na každý test naslouchacího procesu.

## <a name="next-steps"></a>Další postup
* Zkuste [Unity projektu Akustika ukázkový obsah](unity-quickstart.md) nebo [Unreal ukázkový obsah](unreal-quickstart.md)

