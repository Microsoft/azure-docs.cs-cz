---
title: Znalostní báze Design – QnA Maker
description: Znalostní báze QnA Maker se skládá ze sady otázek a odpovědí (QnA) a volitelných metadat přidružených ke každé dvojici QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 1d24434d1343f4174cfbfeb3a30c36737b213168
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756785"
---
# <a name="question-and-answer-set-concepts"></a>Koncepty sad otázek a odpovědí

Znalostní báze se skládá ze sad otázek a odpovědí (QnA).  Každá sada má jednu odpověď a sada obsahuje všechny informace spojené s _tímto záznamem_. Odpověď může volně podobat řádku databáze nebo instanci datové struktury.

## <a name="question-and-answer-sets"></a>Sady otázek a odpovědí

**Požadovaná** nastavení v sadě otázek a odpovědí (QnA) jsou:

* **otázka** - text uživatelského dotazu, který se používá k strojovému učení QnA Maker, aby se vyrovnal textu otázky uživatele s různými formulacemi, ale stejnou odpovědí
* **odpověď** - odpověď sady je odpověď, která je vrácena, když je uživatelský dotaz spárován s přidruženou otázkou

Každá sada je reprezentována **ID**.

**Volitelná** nastavení sady zahrnují:

* **Alternativní formy otázky** - to pomáhá QnA Maker vrátit správnou odpověď pro širší škálu otázek formulací
* **Metadata**: Metadata jsou značky přidružené ke dvojici QnA a jsou reprezentovány jako dvojice klíč-hodnota. Značky metadat se používají k filtrování párů QnA a omezení sady, přes kterou se provádí párování dotazů.
* **Výzvy s více otočením**, které se používají k pokračování vícesměrové konverzace

![Znalostní báze QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Redakční přidání do znalostní báze

Pokud nemáte již existující obsah k naplnění znalostní báze, můžete přidat dvojice QnA redakčním způsobem na portálu QnA Maker. Zde se dozvíte, jak aktualizovat znalostní [bázi](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Úprava znalostní báze místně

Po vytvoření znalostní báze doporučujeme provádět úpravy textu znalostní báze na [portálu QnA Maker](https://qnamaker.ai), nikoli exportovat a znovu importovat prostřednictvím místních souborů. Mohou však napodobuje nutnost upravit znalostní bázi místně.

Exportujte znalostní bázi ze stránky **Nastavení** a upravte znalostní báze pomocí aplikace Microsoft Excel. Pokud se rozhodnete použít jinou aplikaci k úpravě exportovaného souboru, aplikace může zavést chyby syntaxe, protože není plně kompatibilní s TSV. Soubory TSV aplikace Microsoft Excel obvykle nezavádějí žádné chyby formátování.

Po dokončení úprav znovu importujte soubor TSV ze stránky **Nastavení.** Tím zcela nahradíte stávající znalostní bázi importovnou znalostní základnou.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Životní cyklus znalostní báze v QnA Makeru](./development-lifecycle-knowledge-base.md)