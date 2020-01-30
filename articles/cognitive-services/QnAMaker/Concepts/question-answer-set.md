---
title: Návrh znalostní báze Knowledge Base – QnA Maker
description: QnA Maker znalostní báze se skládá ze sady sad otázek a odpovědí (QnA) a volitelná Metadata přidružená ke každému páru QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844310"
---
# <a name="question-and-answer-set-concepts"></a>Koncepce sady otázek a odpovědí

Znalostní báze se skládá ze sad otázek a odpovědí (QnA).  Každá sada má jednu odpověď a sada obsahuje všechny informace spojené s touto _odpovědí_. Odpověď může volně napodobovat řádek databáze nebo instanci datové struktury.

## <a name="question-and-answer-sets"></a>Sady otázek a odpovědí

**Požadovaná** nastavení v sadě QnA (Question-and-Answer) jsou:

* **dotazový** text dotazu, který se používá k QnA maker strojového učení, pro zarovnání textu otázky uživatele s různou formulací, ale stejná odpověď
* odpověď **– odpověď** sady je odpověď, která se vrátí, když se dotaz uživatele shoduje s přidruženou otázkou.

Každá sada je reprezentovaná **identifikátorem**.

Mezi **volitelná** nastavení pro sadu patří:

* **Alternativní formy otázky** – to pomáhá QnA maker vrátit správnou odpověď na širší řadu otázek.
* **Metadata**: metadata jsou značky přidružené k páru QnA a jsou reprezentovány jako páry klíč-hodnota. Značky metadat slouží k filtrování párů QnA a omezení sady, u které se provádí párování dotazů.
* **Víceřádkové výzvy**, které se používají k pokračování vícenásobného zapínání konverzace

![Nástroj QnA Maker znalostních bází](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Znalostní báze pomocí redakčních úprav přidat

Pokud nemáte již existující obsah k naplnění znalostní báze, můžete přidat QnA sady v redakčním okně na portálu QnA Maker. Zjistěte, jak aktualizovat znalostní báze [tady](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Místní úprava znalostní báze

Po vytvoření znalostní báze se doporučuje provádět úpravy textu znalostní báze na [portálu QnA maker](https://qnamaker.ai)a nemusíte je exportovat a znovu importovat pomocí místních souborů. Nicméně může nastat situace, kdy budete muset znalostní bázi upravit místně.

Vyexportujte znalostní bázi ze stránky **Nastavení** a pak upravte znalostní bázi v aplikaci Microsoft Excel. Pokud se rozhodnete pro úpravu exportovaného souboru použít jinou aplikaci, může aplikace způsobit chyby syntaxe, protože není plně kompatibilní s požadavky na TSV. Soubory TSV v aplikaci Microsoft Excel obecně nezavádějí žádné chyby formátování.

Až budete s úpravami hotovi, znovu naimportujte soubor TSV ze stránky **Nastavení** . Tato akce zcela nahradí aktuální znalostní bázi importovanou znalostní bázi.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Životní cyklus znalostní báze v QnA Maker](./development-lifecycle-knowledge-base.md)