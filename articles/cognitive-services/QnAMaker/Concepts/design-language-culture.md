---
title: Návrh pro jazykově QnA Maker
description: Prostředek QnA Maker a všechny znalostní báze uvnitř tohoto prostředku podporují jeden jazyk. Jeden jazyk je nutný k tomu, aby poskytoval nejlepší výsledky odpovědi pro dotaz.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e09e15f71b0574a5612e7f9bacd8aaa4739a441c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776999"
---
# <a name="design-knowledge-base-for-content-language"></a>Návrh znalostní báze pro jazyk obsahu

Prostředek QnA Maker a všechny znalostní báze uvnitř tohoto prostředku podporují jeden jazyk. Jeden jazyk je nutný k tomu, aby poskytoval nejlepší výsledky odpovědi pro dotaz.

## <a name="single-language-per-resource"></a>Jeden jazyk na prostředek

QnA Maker posouzení pro jazykovou podporu:

* Služba QnA Maker a všechny její základy znalostní báze podporují jenom jeden jazyk.
* Jazyk se explicitně nastaví, když se vytvoří první znalostní báze služby.
* Jazyk se určí ze souborů a adres URL přidaných při vytváření znalostní báze.
* Jazyk není možné změnit pro žádné jiné znalostní báze ve službě.
* Jazyk používá služba Kognitivní hledání (#1 Rank) a služba QnA Maker (#2 Rank) k vygenerování nejlepší odpovědi na dotaz.

## <a name="supporting-multiple-languages"></a>Podpora více jazyků

Pokud potřebujete podporovat systém znalostní báze, který obsahuje několik jazyků, můžete vybrat jednu z následujících metod:

* Před odesláním otázky do znalostní báze použijte [službu překladu textu](../../translator/translator-info-overview.md) k překladu otázky do jednoho jazyka. To vám umožní zaměřit se na kvalitu jednoho jazyka a kvality alternativních otázek a odpovědí.
* Vytvořte prostředek QnA Maker a v rámci tohoto prostředku znalostní bázi pro každý jazyk. Díky tomu můžete spravovat samostatné alternativní otázky a text odpovědi, který je pro jednotlivé jazyky odlišitější. Díky tomu získáte mnohem větší flexibilitu, ale při změně otázek a odpovědí napříč všemi jazyky potřebujete mnohem vyšší náklady na údržbu.

Přečtěte si [jazyky podporované](../overview/language-support.md) pro QnA maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Podpora jednotlivých jazyků pomocí QnA Makerho prostředku

* Vytvořit prostředek QnA Maker pro všechny jazyky
* Přidat jenom soubory a adresy URL pro daný jazyk
* K identifikaci jazyka použijte zásady vytváření názvů prostředků. Příklad je `qna-maker-fr` pro všechny znalostní báze pro francouzské dokumenty.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [konceptech](query-knowledge-base.md) dotazování znalostní báze na odpověď.