---
title: Nejčastější dotazy týkající se akustiky projektu
titlesuffix: Azure Cognitive Services
description: Tato stránka obsahuje odpovědi na často kladené otázky týkající se aplikace Project Acoustics, včetně pokynů ke stažení a procesu pečení.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770199"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Nejčastější dotazy týkající se akustiky projektu

## <a name="what-is-project-acoustics"></a>Co je Project Acoustics?

Sada pluginů Project Acoustics je akustický systém, který vypočítává chování zvukových vln před runtime, podobu statického osvětlení. Cloud provádí zvedání těžkých nákladů na výpočty vlnové fyziky, takže náklady na procesor za běhu jsou nízké.  

## <a name="where-can-i-download-the-plugin"></a>Kde mohu stáhnout plugin?

Zde si můžete stáhnout [projekt Acoustics Unity plugin](https://www.microsoft.com/download/details.aspx?id=57346) nebo Projekt [Acoustics Unreal plugin](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Podporuje project &lt;acoustics&gt; platformu x?

Podpora platformy Project Acoustics se vyvíjí na základě potřeb zákazníků. Prosím, kontaktujte nás na [projektu Akustika problém fórum](https://github.com/microsoft/ProjectAcoustics/issues) informovat o podpoře pro další platformy.

## <a name="is-azure-used-at-runtime"></a>Používá se Azure za běhu?

Ne, integrace cloudu se používá pouze během fáze precompute jako součást nastavení scény.
 
## <a name="what-is-simulation-input"></a>Co je vstup simulace? 

Vstup simulace je vaše 3D scéna, virtuální prostředí nebo herní úroveň. Projekt Akustika provádí 3D simulace objemových vln, které pečlivě modelují fyziku zvuku, včetně hladkéokluze a rozptylu.
 
## <a name="what-is-the-runtime-cost"></a>Jaké jsou náklady za běhu?

Akustika trvá asi 0,01 % procesoru na zdroj na snímek. Využití paměti RAM závisí na velikosti scény a může se pohybovat od 10 do 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Musím zjednodušit geometrii úrovně? Počet kontrolních trojúhelníků? Aby ok vodotěsné?

Ne. Systém bude splet podrobnou geometrii úrovně přímo. Bude voxelized pro vnitřní zpracování.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Co je v tabulce vyhledávání za běhu?

Soubor ACE obsahuje tabulku akustických parametrů mezi mnoha páry umístění zdroje a posluchače, stejně jako geometrie voxelized scény používaná pro interpolaci parametrů.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Může aplikace Project Acoustics zpracovávat pohyblivé zdroje?

Ano, aplikace Project Acoustics nahlíží do vyhledávací tabulky a aktualizuje zvukový dsp na každém zaškrtávacím políčku, takže může zpracovávat pohyblivé zdroje a naslouchací proces.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Zvládne aplikace Project Acoustics dynamickou geometrii? Zavírání dveří? Zdi odfouknuty?

Ne. Akustické parametry jsou předem vypočítány na základě statického stavu herní úrovně. Navrhujeme ponechat geometrii dveří mimo akustiku a poté použít další okluzi založenou na stavu zničitelných a pohyblivých herních objektů pomocí zavedených technik.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Používá aplikace Project Acoustics akustické materiály?

Ano. Materiály jsou vybírány z názvů fyzikálních materiálů ve vaší úrovni, což vede k absorpční absorpční.
 
## <a name="what-do-the-probes-represent"></a>Co představují "sondy"?

Sondy jsou vzorkováním možných míst hráčů. Každá sonda představuje samostatnou vlnovou simulaci scény pocházející z místa sondy. Za běhu jsou akustické parametry pro umístění posluchače interpolovány z blízkých umístění sondy.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Proč utrácet tolik výpočetních prostředků v cloudu? Co mi to koupí?

Aplikace Project Acoustics poskytuje přesné a spolehlivé akustické parametry i pro ultrakomplexní virtuální prostředí s přihlédnutím ke každému architektonickému aspektu. Poskytuje hladkou okluzi a obstrukce a dynamické změny reverbu bez ruční práce výkresových objemů. To vše při zbývajícím světle procesoru za běhu.

## <a name="what-exactly-happens-during-baking"></a>Co přesně se stane během "pečení"?

Pečení se skládá z simulací akustických vln kvádrových simulačních oblastí soustředěných na každou posluchačskou sondu.

## <a name="is-my-source-content-secure"></a>Je zdrojový obsah zabezpečený?

Aplikace Project Acoustics nenahraje geometrii zdrojové scény do cloudu. Místo toho simulace pracuje na voxelizaci vaší scény, která je kombinována s daty o poloze sondy a uložena v proprietárním formátu.     

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte [ukázkový obsah Project Acoustics Unity](unity-quickstart.md) nebo [Neskutečný ukázkový obsah](unreal-quickstart.md)

