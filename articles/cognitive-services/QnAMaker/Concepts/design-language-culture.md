---
title: Návrh pro jazyk - QnA Maker
description: Prostředek QnA Maker a všechny znalostní báze uvnitř tohoto prostředku podporují jeden jazyk. Jeden jazyk je nezbytné poskytnout nejlepší výsledky odpovědi pro dotaz.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843417"
---
# <a name="design-knowledge-base-for-content-language"></a>Návrh znalostní báze pro jazyk obsahu

Prostředek QnA Maker a všechny znalostní báze uvnitř tohoto prostředku podporují jeden jazyk. Jeden jazyk je nezbytné poskytnout nejlepší výsledky odpovědi pro dotaz.

## <a name="single-language-per-resource"></a>Jeden jazyk na zdroj

Důležité informace o qnA makeru pro jazykovou podporu:

* Služba QnA Maker a všechny její znalostní báze podporují pouze jeden jazyk.
* Jazyk je explicitně nastaven při vytvoření první znalostní báze služby.
* Jazyk je určen ze souborů a adres URL přidaných při vytvoření znalostní báze.
* Jazyk nelze změnit pro žádné jiné znalostní báze ve službě
* Jazyk je používán službou Cognitive Search (ranker #1) a službou QnA Maker (ranker #2) ke generování nejlepší odpovědi na dotaz

## <a name="supporting-multiple-languages"></a>Podpora více jazyků

Pokud potřebujete podporovat systém znalostní báze, který obsahuje několik jazyků, můžete zvolit jednu z následujících metod:

* Službu [Překlad textu](../../translator/translator-info-overview.md) slouží k překladu otázky do jednoho jazyka před odesláním otázky do znalostní báze. To vám umožní zaměřit se na kvalitu jednoho jazyka a kvalitu alternativních otázek a odpovědí.
* Vytvořte prostředek QnA Maker a znalostní bázi uvnitř tohoto prostředku pro každý jazyk. To umožňuje spravovat samostatné alternativní otázky a odpovídat na text, který je více nuancí pro každý jazyk. To vám dává mnohem větší flexibilitu, ale vyžaduje mnohem vyšší náklady na údržbu, když se otázky nebo odpovědi změní ve všech jazycích.

Zkontrolujte [jazyky podporované](../overview/language-support.md) pro QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Podpora každého jazyka pomocí zdroje QnA Maker

* Vytvoření prostředku QnA Maker pro každý jazyk
* Přidávat pouze soubory a adresy URL pro tento jazyk
* K identifikaci jazyka použijte konvenci pojmenování prostředku. Příkladem jsou `qna-maker-fr` všechny znalostní báze pro francouzské dokumenty

## <a name="next-steps"></a>Další kroky

Seznamte se s [koncepty,](query-knowledge-base.md) jak získat odpověď na znalostní bázi Knowledge Base.