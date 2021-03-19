---
title: 'Nejčastější dotazy: SRE a DevOps | Microsoft Docs'
titleSuffix: Azure
description: 'Nejčastější dotazy: Princip vztahů mezi SRE a DevOps'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 62872443c386c45d9821053dc901ad8e0055b74f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674273"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Nejčastější dotazy: Jaký je vztah mezi SRE a DevOps?

Existuje sada běžných otázek, které se týkají vztahů mezi spolehlivostí webu a DevOps, včetně toho, jak jsou stejné? Jaký je mezi nimi rozdíl? Máte v naší organizaci obojí? ". Tento článek se pokusí sdílet některé odpovědi, které nabídli komunity SRE a DevOps, které nám přiblíží porozumění tomuto vztahu.

## <a name="how-are-they-the-same"></a>Jak jsou stejné?

SRE a DevOps jsou postupy moderních operací, které byly vytvořeny a vyvinuty v reakci na výzvy, které zahrnují:

- rostoucí složitost našich produkčních prostředí a procesů vývoje
- zvyšující se podnikatelská závislost na nepřetržitém fungování těchto prostředí
- neschopnost škálovat pracovní síly lineárně s velikostí těchto prostředí
- nutnost rychlejšího přesunu, ale stále se zachovává provozní stabilita

U obou postupů operací je potřeba věnovat pozornost subjektům, které jsou zásadní pro jednání s těmito výzvami, jako jsou monitorování/pozorování, automatizace, dokumentace a nástroje pro vývoj softwaru pro spolupráci.

Mezi SRE a DevOps se výrazně překrývají nástroje a oblasti práce. V případě, že je _sešit spolehlivosti webu_ , "SRE se neshodují ve stejných případech jako DevOps, ale z mírně různých důvodů."

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>Tři různé způsoby, jak porovnat dvě provozní postupy

Podobnosti mezi SRE a DevOps jsou jasné. V takovém případě se v takovém případě nezajímavě stane, jak se tyto dva liší. Tady nabízíme tři způsoby, jak se svými vztahy představit jako v souvislosti s tím, že se do této otázky zavedou nějaké nuance. S těmito odpověďmi nesmíte souhlasit, ale každá z nich nabízí dobrý počáteční prostor pro diskuzi.

### <a name="class-sre-implements-interface-devops"></a>"Class SRE implementuje rozhraní DevOps"

_Sešit se spolehlivostí webu_ (uvedený v našem [seznamu Resource Book](../resources/books.md)) popisuje SRE a DevOps v první kapitole. Tato kapitola používá frázi "Class SRE implementuje rozhraní DevOps" jako svůj podnadpis. To je určeno pro návrh (použití fráze zaměřené na vývojáře), kterou by SRE mohlo být považováno za konkrétní implementaci DevOps filozofie. Jako kapitola odkazujeme "DevOps je relativně tichá na tom, jak spouštět operace na detailní úrovni", zatímco SRE je ve svých postupech výrazně důležitější. Jednu možnou odpověď na otázku, jak se tyto dva vztahy vztahují, by mohla být považována za jednu z mnoha možných implementací DevOps.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE je spolehlivost, jak DevOps je doručování

Toto porovnání je trochu muddied, protože existuje více definic pro SRE i DevOps, ale je stále potenciálně užitečné. Začíná otázkou "Pokud jste museli vyjednat jednotlivé provozní postupy v jednom nebo dvou slovech, která odrážejí podstatné obavy, co by to bylo?"

Pokud použijeme tuto definici SRE z centra pro [technickou spolehlivost webu](../index.yml):

> Site Reliability Engineering (SRE) je technická disciplína, která pomáhá organizaci udržitelně dosahovat odpovídající úrovně spolehlivosti jejích systémů, služeb a produktů.

v takovém případě by bylo snadné vyslovit slovo pro SRE "spolehlivost". Napravo od jejího názvu také nabízí skvělé důkazy pro tuto deklaraci identity.

Pokud používáme tuto definici DevOps z [centra zdrojů Azure DevOps](/azure/devops/learn/):

> DevOps je spojení lidí, procesů a produktů, které má koncovým uživatelům průběžně doručovat hodnotu.

obdobná reDevOps by mohla být "doručení".

Proto "SRE má spolehlivost, jak DevOps je doručována".

### <a name="direction-of-attention"></a>Směr pozornosti

Tato odpověď je uvedená v uvozovkách nebo mírně paraphrased od příspěvku od společnosti Limoncelli k _hledání knihy SRE_ , kterou [uvádí náš seznam knih prostředků](../resources/books.md). Poznamenejte si, že DevOps technici jsou převážně zaměřeny na kanál životního cyklu vývoje softwaru s příležitostnými odpovědnostmi za provozní operace, zatímco se SREs soustředit na produkční operace s příležitostnými zodpovědnostmi softwaru kanálu.

Ale důležitější je, že také nakreslí diagram, který začíná procesem vývoje softwaru na jedné straně, a provozní operace fungují na druhé straně. Tyto dva jsou propojeny obvyklým kanálem, který je sestaven tak, aby převzal kód od vývojářů, Shepherd ho prostřednictvím požadovaného počtu testů a fáze a pak tento kód přesune do produkčního prostředí.

Limoncelli poznámky, které DevOps technici spouštějí ve vývojovém prostředí, a automatizují kroky směrem k produkci. Po dokončení se vrátí k optimalizaci slabých míst.

SREs, na druhé straně, se zaměřte na produkční operace a Dosahujte hloubky do kanálu jako způsob, jak zlepšit konečný výsledek (v podstatě pracují v opačném směru).

Jedná se o rozdíl ve směru SRE a DevOps fokusu, který jim může usnadnit jejich rozlišení.

## <a name="coexistence-in-the-same-organization"></a>Koexistence ve stejné organizaci

Poslední otázkou, kterou chceme adresovat, je, že můžete mít ve stejné organizaci jak SRE, tak DevOps? "

Odpověď na tuto otázku je emphatic "Ano!".

Doufáme, že předchozí odpovědi nabízejí několik nápadů, jak se tyto dvě provozní postupy překrývají a v případě, že se nepřekrývají, jak se dají doplňovat. Organizace s navázáným DevOps postupem můžou experimentovat s postupy SRE v malém měřítku (například při pokusu o SLIs a slo) bez nutnosti potvrzování při vytváření SRE pozic nebo týmů. Toto je poměrně běžný vzor pro přijetí SRE.

## <a name="next-steps"></a>Další kroky

Zajímá vás další informace o sledování spolehlivosti a DevOps webů? Podívejte se na naše centrum pro [spolehlivost](../index.yml) a [centrum prostředků Azure DevOps](/azure/devops/learn/).