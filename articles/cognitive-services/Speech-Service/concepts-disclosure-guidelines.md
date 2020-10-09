---
title: Pokyny pro návrh zpřístupnění
titleSuffix: Azure Cognitive Services
description: Úvod do pokynů pro návrh zpřístupnění a posouzení úrovně zpřístupnění.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: fe38c6b7cfb1abbaf3f1079dd8bff66b51b98091
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74776381"
---
# <a name="disclosure-design-guidelines"></a>Zpřístupnění pokynů pro návrh
Naučte se vytvářet a udržovat vztahy důvěryhodnosti se zákazníky transparentním z hlediska syntetické povahy hlasového prostředí.

## <a name="what-is-disclosure"></a>Co je zpřístupnění?

Zveřejnění je způsob, jak uživatelům umožnit, aby&#39;znovu projednali nebo naslouchali hlasu, který se uměle vygeneroval.

## <a name="why-is-disclosure-necessary"></a>Proč je zveřejňování nezbytné?

Je potřeba zveřejnit syntetické zdroje hlasu generovaného počítačem je poměrně nové. V minulosti byly hlasy generované počítačem zjevně, ale nikdo by je v případě reálného uživatele nemusel omylem potřebovat. Každý den se ale zlepšuje realita syntetických hlasů a jejich počet je stále nerozlišovatelné od lidského hlasů.

## <a name="goals"></a>Cíle
Při navrhování syntetického hlasového prostředí je třeba mít na paměti tyto zásady:

**Posílit důvěryhodnost**
<br>Navrhněte s úmyslem selhání testu Turing bez snížení výkonu. Umožněte uživatelům ve skutečnosti, že pracují se syntetickým hlasem a zároveň jim umožňují bezproblémový provoz s prostředím.

**Přizpůsobit kontextu použití**
<br>Seznamte se s tím, kdy, kde a jak budou vaši uživatelé pracovat s syntetickým hlasem, aby poskytovaly správný typ zveřejnění ve správný čas.

**Nastavit jasné očekávání**
<br>Umožní uživatelům snadno zjistit a pochopit schopnosti agenta. Nabízí příležitosti k získání dalších informací o syntetické hlasové technologii na vyžádání.

**Zapojte chybu**
<br>Nepoužívejte moment selhání k posílení schopností agenta.

## <a name="how-to-use-this-guide"></a>Jak používat tohoto průvodce

Tato příručka vám pomůže určit, které modely zveřejňování jsou pro vaše syntetické hlasové prostředí nejvhodnější. Potom nabízíme příklady, jak a kdy je můžete používat. Každý z těchto vzorů je navržený tak, aby se maximalizovala průhlednost s uživateli o syntetickém rozpoznávání řeči při současném zachování na návrh na základě lidského středu.

S ohledem na obrovské tělo pokynů pro vytváření hlasových prostředí se tady zaměřujeme na tyto možnosti:

1. [**Posouzení zveřejnění**](#disclosure-assessment): proces, který určuje typ zveřejnění doporučený pro vaše syntetické hlasové prostředí.

2. [**Jak zveřejnit**](concepts-disclosure-patterns.md): příklady způsobů zveřejnění, které je možné použít pro vaše syntetické hlasové prostředí.

3. [**Kdy se má zveřejnit**](concepts-disclosure-patterns.md#when-to-disclose): optimální moment pro zveřejnění celé cesty uživatele

## <a name="disclosure-assessment"></a>Vyhodnocování zveřejnění
Berte v úvahu své uživatele&#39; očekávání týkající se interakce a kontextu, ve kterém se budou na hlasu zacházet. Pokud je v kontextu jasné, že je hovoří syntetický hlas &quot; , &quot; může být odhalení minimální, chvilku nebo dokonce zbytečné. Hlavní typy kontextu, které mají vliv na zveřejnění, zahrnují typ osoby, typ scénáře a úroveň ozáření. Pomáhá také zvážit, kdo může naslouchat.

### <a name="understand-context"></a>Pochopení kontextu

Pomocí tohoto listu můžete určit kontext syntetického hlasového prostředí. Tento postup použijete v dalším kroku, kde budete určovat úroveň zveřejnění.

|                                    | Kontext použití                                                                                                                                                                                                                                                                                                                                                       | Potenciální rizika & problémy                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. typ osoby**               | **Pokud platí kterákoli z následujících možností, vaše osoba se vejde do kategorie lidé, která je člověkem podobná:**<br><br><ul><li> "Osoba" předvede skutečnou člověka, ať už jde o fiktivní vyjádření. (například fotografie nebo vykreslování reálné osoby z počítače)<br><br><li> Syntetická hlas je založen na hlasu pro široce rozpoznatelnou skutečnou osobu (např. celebrit, politický obrázek). | Více lidí podobné reprezentace, které udělíte vašemu uživateli, je pravděpodobnější, že se k němu přiřadí skutečným osobám, nebo způsobí, že se obsah předá skutečnými osobami, nikoli vygenerovanými počítačem. </ul>                                                                                                                                                                      |
| **2. typ scénáře**            | **Pokud platí některá z následujících možností, vaše hlasové prostředí se zahodí do kategorie citlivého:**<br><br><ul><li> Získá nebo zobrazí osobní údaje uživatele. <br><br> <li> Všesměrové vysílání: informace o citlivých a informacích (například mimořádná výstraha)<br><br><li> Cílem je pomoci skutečným osobám navzájem komunikovat (například čtení osobních e-mailů/textů).<br><br> <li> Poskytuje pomoc na lékařské/zdravotní péči </ul>            | Použití syntetického hlasu nemusí být vhodné ani důvěryhodné pro lidi, kteří ho používají, když témata souvisejí s citlivými, osobními a naléhavými otázkami. Můžou také očekávat stejnou úroveň soucit a kontextové povědomí jako reálná lidská. |
| **3. úroveň ozáření** |**Vaše hlasové prostředí se pravděpodobně vejde do kategorie vysoká, pokud:** <br><br><ul><li>Uživatel bude slyšet nebo interagovat se syntetickým hlasem často nebo po dlouhou dobu. </ul>                                                                                                                                                                             | Důležitost průhlednosti a sestavování vztahů důvěryhodnosti s uživateli je ještě vyšší při navazování dlouhodobých vztahů.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Určit úroveň zveřejnění

Pomocí následujícího diagramu zjistíte, jestli vaše syntetické hlasové prostředí vyžaduje vysoké nebo nízké zveřejnění v závislosti na vašem kontextu použití.

  ![Diagram vyhodnocení zpřístupnění](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Referenční dokumenty

* [Zveřejnění pro hlasový talentů](https://aka.ms/disclosure-voice-talent)
* [Pokyny pro zodpovědnost nasazení syntetické hlasové technologie](concepts-guidelines-responsible-deployment-synthetic.md)
* [Přehled o uzavírání](concepts-gating-overview.md)

## <a name="next-steps"></a>Další kroky

* [Zpřístupnění způsobů návrhu](concepts-disclosure-patterns.md)
