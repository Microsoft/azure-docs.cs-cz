---
title: Pokyny pro návrh zveřejnění
titleSuffix: Azure Cognitive Services
description: Úvod do pokynů pro návrh zveřejnění a posouzení úrovně zveřejnění.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: fe38c6b7cfb1abbaf3f1079dd8bff66b51b98091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776381"
---
# <a name="disclosure-design-guidelines"></a>Zpřístupnění pokynů pro návrh
Naučte se budovat a udržovat důvěru se zákazníky tím, že je transparentní o syntetické povaze vašeho hlasového zážitku.

## <a name="what-is-disclosure"></a>Co je zveřejnění?

Zveřejnění je prostředkem, jak dát lidem vědět, že&#39;re interakci s nebo poslouchat hlas, který je synteticky generované.

## <a name="why-is-disclosure-necessary"></a>Proč je zveřejnění nezbytné?

Potřeba odhalit syntetický původ počítačem generovaného hlasu je relativně nová. V minulosti, počítač-generované hlasy byly samozřejmě, že-nikdo by nikdy splést je za skutečnou osobu. Každý den se však realismus syntetických hlasů zlepšuje a jsou stále více k nerozeznání od lidských hlasů.

## <a name="goals"></a>Cíle
Toto jsou zásady, které je třeba mít na paměti při navrhování syntetických hlasových zážitků:

**Posílit důvěru**
<br>Navrhněte s úmyslem neuspět turingův test bez degradace zkušeností. Ušetřte uživatelům skutečnost, že interagují se syntetickým hlasem a zároveň jim umožní bezproblémově se zapojit do zážitku.

**Přizpůsobení kontextu použití**
<br>Zjistěte, kdy, kde a jak budou uživatelé komunikovat se syntetickým hlasem, aby poskytli správný typ zveřejnění ve správný čas.

**Nastavte jasná očekávání**
<br>Umožněte uživatelům snadno zjistit a pochopit možnosti agenta. Nabídněte vám možnost dozvědět se více o syntetické hlasové technologii na vyžádání.

**Přijmout selhání**
<br>Použijte momenty selhání k posílení schopností agenta.

## <a name="how-to-use-this-guide"></a>Jak používat tohoto průvodce

Tato příručka vám pomůže určit, které vzory zveřejnění jsou nejvhodnější pro váš syntetický hlas ový zážitek. Pak nabízíme příklady toho, jak a kdy je používat. Každý z těchto vzorů je navržen tak, aby maximalizoval transparentnost s uživateli o syntetické řeči a zároveň zůstat věrný designu zaměřenému na člověka.

Vzhledem k obrovskému souboru pokynů k návrhu na hlasové zážitky se zde zaměřujeme konkrétně na:

1. [**Posouzení zveřejnění**](#disclosure-assessment): Proces určení typu zveřejnění doporučeného pro váš syntetický hlasový zážitek

2. [**Jak zveřejnit**](concepts-disclosure-patterns.md): Příklady zveřejňování vzorů, které mohou být použity pro váš syntetický hlas zkušenosti

3. [**Kdy zveřejnit**](concepts-disclosure-patterns.md#when-to-disclose): Optimální okamžiky pro zveřejnění po celou cestu uživatele

## <a name="disclosure-assessment"></a>Posouzení zveřejnění
Zvažte,&#39; uživatelé ohledně interakce a kontextu, ve kterém zažijí hlas. Pokud kontext jasně ukazuje, že &quot;mluví syntetický&quot; hlas, zveřejnění může být minimální, chvilkové, nebo dokonce zbytečné. Mezi hlavní typy kontextu, které ovlivňují zveřejnění, patří typ osoby, typ scénáře a úroveň expozice. To také pomáhá zvážit, kdo by mohl být poslech.

### <a name="understand-context"></a>Pochopit kontext

Tento list slouží k určení kontextu syntetickéhlasové zkušenosti. Použijete to v dalším kroku, kde určíte úroveň zveřejnění.

|                                    | Kontext použití                                                                                                                                                                                                                                                                                                                                                       | Potenciální rizika & výzvy                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Typ Persona**               | **Pokud platí některá z následujících položek, vaše osoba se vejde do kategorie "Lidská osobnost":**<br><br><ul><li> Persona ztělesňuje skutečného člověka, ať už je to fiktivní reprezentace nebo ne. (např. fotografie nebo počítačově generované vykreslování skutečné osoby)<br><br><li> Syntetický hlas je založen na hlasu široce rozpoznatelné skutečné osoby (např. celebrity, politické osobnosti) | Čím více lidských reprezentací dáte své osobě, tím větší je pravděpodobnost, že ji uživatel přidruží ke skutečné osobě, nebo způsobí, že se domnívají, že obsah je mluvený skutečnou osobou spíše než počítačem generovaný. </ul>                                                                                                                                                                      |
| **2. Typ scénáře**            | **Pokud platí některou z následujících možností, váš hlas se vejde do kategorie "Citlivé":**<br><br><ul><li> Získává nebo zobrazuje osobní údaje od uživatele <br><br> <li> Vysílá zprávy/informace citlivé na čas (např. nouzové upozornění)<br><br><li> Jeho cílem je pomáhat skutečným lidem komunikovat mezi sebou (např. číst osobní e-maily / texty)<br><br> <li> Poskytuje lékařskou/zdravotní pomoc </ul>            | Použití syntetického hlasu nemusí být vhodné nebo důvěryhodné pro lidi, kteří jej používají, pokud témata souvisejí s citlivými, osobními nebo naléhavými záležitostmi. Mohou také očekávat stejnou úroveň empatie a kontextového vědomí jako skutečný člověk. |
| **3. Úroveň expozice** |**Váš hlasový zážitek s největší pravděpodobností zapadá do kategorie "Vysoká", pokud:** <br><br><ul><li>Uživatel uslyší syntetický hlas nebo s ním bude komunikovat často nebo po dlouhou dobu </ul>                                                                                                                                                                             | Důležitost transparentnosti a budování důvěry s uživateli je ještě vyšší při navazování dlouhodobých vztahů.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Určit úroveň zveřejnění

Pomocí následujícího diagramu můžete zjistit, zda syntetické hlasové prostředí vyžaduje vysoké nebo nízké zveřejnění na základě kontextu použití.

  ![Diagram posouzení zveřejnění](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Referenční dokumenty

* [Zveřejnění pro hlasové talenty](https://aka.ms/disclosure-voice-talent)
* [Pokyny pro odpovědné zavádění syntetické hlasové technologie](concepts-guidelines-responsible-deployment-synthetic.md)
* [Gating – přehled](concepts-gating-overview.md)

## <a name="next-steps"></a>Další kroky

* [Zpřístupnění způsobů návrhu](concepts-disclosure-patterns.md)
