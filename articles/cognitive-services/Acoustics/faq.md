---
title: Nejčastější dotazy k akustickým projektům
titlesuffix: Azure Cognitive Services
description: Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se akustického projektu, včetně pokynů ke stažení a zanesli procesu.
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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770199"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Nejčastější dotazy k akustickým projektům

## <a name="what-is-project-acoustics"></a>Co je Project Acoustics?

Projekt akustické sady modulů plug-in je akustický systém, který vypočítává chování zvukové vlny před modulem runtime podobají na statické osvětlení. Cloud provádí těžkou zvedání výpočetních prostředků Wave, takže náklady na procesor za běhu jsou nízké.  

## <a name="where-can-i-download-the-plugin"></a>Kde můžu stáhnout modul plug-in?

Můžete si stáhnout modul [Plug-in Unity pro projekt](https://www.microsoft.com/download/details.aspx?id=57346) nebo [akustický modul plug-in Unreal](https://www.microsoft.com/download/details.aspx?id=58090).

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Podporují se v projektech&gt; platformu &lt;x?

Podpora platforem v projektech se vyvíjí na základě zákaznických potřeb. Kontaktujte nás na [fóru problémové problémy s projektem](https://github.com/microsoft/ProjectAcoustics/issues) , kde se můžete dotazovat na podporu pro další platformy.

## <a name="is-azure-used-at-runtime"></a>Používá se Azure za běhu?

Ne, cloudová integrace se používá jenom během fáze předběžnáho zpracování jako součást nastavení scény.
 
## <a name="what-is-simulation-input"></a>Co je simulace vstupu? 

Vstup simulace je vaše 3D scéna, virtuální prostředí nebo úroveň hry. Akustické objemy projektů provádí prostorové simulace vlnovek, které modelují hladce zvuk, včetně hladkých překrytí a bodových.
 
## <a name="what-is-the-runtime-cost"></a>Jaké jsou náklady na modul runtime?

Akustické objemy přebírají přibližně 0,01% CPU na zdroj na jeden snímek. Využití paměti RAM závisí na velikosti scény a může být v rozsahu od 10 do 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Potřebuji zjednodušit geometrii úrovně? Chcete kontrolní počet trojúhelníků? Chcete mít vodotěsné sítě?

Ne. Systém zpracuje detailní geometrii úrovně přímo. Bude voxelized pro interní zpracování.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Co je v běhové vyhledávací tabulce?

Soubor ACE obsahuje tabulku akustických parametrů mezi mnoha páry umístění zdroje a naslouchacího procesu a také geometrií voxelized scény použitou pro interpolaci parametrů.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Můžou se prostředky z projektu manipulovat s přesunem zdrojů?

Ano, akustické projekty prochází vyhledávací tabulku a aktualizuje u každého zatržení zvukový procesor DSP, aby mohl zpracovat Přesun zdrojů a naslouchací proces.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Mohou promítnout akustické práce zpracovat dynamickou geometrii? Zavírá se dveře? Jste plnohodnotnou zdi?

Ne. Akustické parametry jsou předpočítány na základě statického stavu herní úrovně. Doporučujeme ponechat geometrii dvířek z akustického množství a pak použít další překrytí na základě stavu zničitelné a pohyblivých herních objektů pomocí zavedených technik.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Používají se v projektech akustické materiály?

Ano. Materiály se vybírají z fyzických názvů materiálu na úrovni, což řídí absorptivity.
 
## <a name="what-do-the-probes-represent"></a>Co představuje "sondy"?

Sondy představují vzorkování možných umístění hráčů. Každá sonda představuje samostatnou simulaci vlnovku scény, která pochází z umístění testu. V době běhu jsou akustické parametry pro umístění naslouchacího procesu interpolované od blízkých míst testu.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Proč strávíte spoustu výpočtů v cloudu? Co mi koupit?

Akustické a spolehlivé zvukové parametry jsou vhodné pro vysoce složitá virtuální prostředí, která se přiřadí ke každému aspektu architektury. Poskytuje plynulé překrytí a překážky a dynamické reverb variace bez ručního fungování vykreslování svazků. Vše a zbývající světlo na CPU během běhu.

## <a name="what-exactly-happens-during-baking"></a>Co se přesně děje během "pečení"?

Zanesli se skládá z akustických simulací vln cuboidch oblastí simulace, které jsou na každém testu naslouchacího procesu.

## <a name="is-my-source-content-secure"></a>Je můj zdrojový obsah zabezpečený?

Akustické projekty neodesílají geometrii zdrojové scény do cloudu. Místo toho simulace funguje na voxelization vaší scény, která je kombinována s daty umístění sondy a ukládána ve vlastním formátu.     

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte si [projekt akustického obsahu Unity](unity-quickstart.md) nebo [ukázkový obsah Unreal](unreal-quickstart.md)

