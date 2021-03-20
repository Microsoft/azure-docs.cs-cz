---
title: Návrh znalostní báze Knowledge Base – QnA Maker
description: QnA Maker znalostní báze se skládá ze sady párů otázek a odpovědí (QnA) a volitelných metadat přidružených ke každému páru QnA.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: ef5476ade205109f5dfede1b3bb2c3a4ae2e81a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94506001"
---
# <a name="question-and-answer-pair"></a>Pár otázek a odpovědí

Znalostní báze se skládá z párů otázek a odpovědí (QnA).  Každý pár má jednu odpověď a pár obsahuje všechny informace spojené s touto _odpovědí_. Odpověď může volně napodobovat řádek databáze nebo instanci datové struktury.

## <a name="question-and-answer-pairs"></a>Dvojice otázek a odpovědí

**Požadovaná** nastavení dvojice otázek a odpovědí (QnA) jsou:

* **dotazový** text dotazu, který se používá k QnA maker strojového učení, pro zarovnání textu otázky uživatele s různou formulací, ale stejná odpověď
* **odpověď** – odpověď páru je odpověď, která se vrátí, když se dotaz uživatele shoduje s přidruženou otázkou.

Každý pár je reprezentován **identifikátorem**.

Mezi **volitelná** nastavení pro pár patří:

* **Alternativní formy otázky** – to pomáhá QnA maker vrátit správnou odpověď na širší řadu otázek.
* **Metadata**: metadata jsou značky přidružené k páru QnA a jsou reprezentovány jako páry klíč-hodnota. Značky metadat slouží k filtrování párů QnA a omezení sady, u které se provádí párování dotazů.
* **Víceřádkové výzvy**, které se používají k pokračování vícenásobného zapínání konverzace

![QnA Maker základy znalostí](media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Redakční přidání do znalostní báze

Pokud nemáte již existující obsah k naplnění znalostní báze, můžete přidat páry QnA v redakčním portálu na portálu QnA Maker. Přečtěte si, jak [tady](How-To/edit-knowledge-base.md)aktualizovat znalostní bázi.

## <a name="editing-your-knowledge-base-locally"></a>Místní úprava znalostní báze

Po vytvoření znalostní báze se doporučuje provádět úpravy textu znalostní báze na [portálu QnA maker](https://qnamaker.ai)a nemusíte je exportovat a znovu importovat pomocí místních souborů. Nicméně může nastat situace, kdy budete muset znalostní bázi upravit místně.

Vyexportujte znalostní bázi ze stránky **Nastavení** a pak upravte znalostní bázi v aplikaci Microsoft Excel. Pokud se rozhodnete pro úpravu exportovaného souboru použít jinou aplikaci, může aplikace způsobit chyby syntaxe, protože není plně kompatibilní s požadavky na TSV. Soubory TSV v aplikaci Microsoft Excel obecně nezavádějí žádné chyby formátování.

Až budete s úpravami hotovi, znovu naimportujte soubor TSV ze stránky **Nastavení** . Tato akce zcela nahradí aktuální znalostní bázi importovanou znalostní bázi.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Životní cyklus znalostní báze v QnA Maker](Concepts/development-lifecycle-knowledge-base.md)
