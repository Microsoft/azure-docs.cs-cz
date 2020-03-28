---
title: Co je služba QnA Maker?
titleSuffix: Azure Cognitive Services
description: QnA Maker je cloudová služba NLP, která snadno vytváří přirozenou konverzační vrstvu nad vašimi daty. Lze jej použít k nalezení nejvhodnější odpovědi pro daný vstup v přirozeném jazyce z vlastní znalostní báze (KB) informací.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 2863a086343b0a5d3bc69ae319dbe93d557bcb4a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052928"
---
# <a name="what-is-the-qna-maker-service"></a>Co je služba QnA Maker?

[!INCLUDE [TLS 1.2 enforcement](../../../../includes/cognitive-services-tls-announcement.md)]

QnA Maker je cloudová služba NLP (Natural Language Processing), která snadno vytváří přirozenou konverzační vrstvu nad vašimi daty. Lze jej použít k nalezení nejvhodnější odpovědi pro daný vstup v přirozeném jazyce z vlastní znalostní báze (KB) informací.

Klientská aplikace pro QnA Maker je libovolná konverzační aplikace, která komunikuje s uživatelem v přirozeném jazyce a odpovídá na otázku. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb.

## <a name="when-to-use-qna-maker"></a>Kdy používat QnA Maker

* **Pokud máte statické informace** - Použijte QnA Maker, pokud máte statické informace ve znalostní bázi odpovědí. Tato znalostní báze je vlastní vašim potřebám, které jste vytvořili pomocí dokumentů, jako jsou [soubory PDF a adresy URL](../concepts/content-types.md).
* **Pokud chcete poskytnout stejnou odpověď na požadavek, otázku nebo příkaz** - když různí uživatelé odešlou stejnou otázku, je vrácena stejná odpověď.
* **Pokud chcete filtrovat statické informace na základě metadat** – přidejte [značky metadat,](../how-to/metadata-generateanswer-usage.md) které poskytují další možnosti filtrování relevantní pro uživatele klientské aplikace a informace. Běžné informace o metadatech zahrnují [chit-chat](../how-to/chit-chat-knowledge-base.md), typ obsahu nebo formát, účel obsahu a čerstvost obsahu.
* **Pokud chcete spravovat konverzaci robota, která obsahuje statické informace** – znalostní báze vezme konverzační text nebo příkaz uživatele a odpoví na něj. Pokud je odpověď součástí předem určeného toku konverzace, reprezentovaného ve znalostní bázi s [kontextem více otočení](../how-to/multiturn-conversation.md), může bot tento tok snadno poskytnout.

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Použití znalostní báze QnA Maker v chatovacím robotu

Po publikování znalostní báze QnA Maker, klientská aplikace odešle otázku do koncového bodu znalostní báze a obdrží výsledky jako odpověď JSON. Běžnou klientskou aplikací pro QnA Maker je chatovací robot.

![Položte botovi otázku a získejte odpověď z obsahu znalostní báze](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Krok|Akce|
|:--|:--|
|1|Klientská aplikace odešle _uživatelovu otázku_ (text vlastními slovy), "Jak programově aktualizovat znalostní bázi?" do koncového bodu znalostní báze.|
|2|QnA Maker používá trénované znalostní báze poskytnout správnou odpověď a všechny následné výzvy, které lze použít k upřesnění hledání nejlepší odpověď. QnA Maker vrátí odpověď ve formátu JSON.|
|3|Klientská aplikace používá odpověď JSON k rozhodování o tom, jak pokračovat v konverzaci. Tato rozhodnutí mohou zahrnovat zobrazení nejvyšší odpovědi a prezentaci více možností pro upřesnění hledání nejlepší odpovědi. |
|||

## <a name="what-is-a-knowledge-base"></a>Co je znalostní báze?

QnA Maker [importuje váš obsah](../concepts/knowledge-base.md) do znalostní báze sad otázek a odpovědí. Proces importu extrahuje informace o vztahu mezi částmi strukturovaného a částečně strukturovaného obsahu, což znamená vztahy mezi sadami otázek a odpovědí. Můžete upravit tyto sady otázek a odpovědí nebo přidat nové sady.

Obsah sady otázek a odpovědí zahrnuje:
* Všechny alternativní formy otázky
* Značky metadat používané k filtrování voleb odpovědí během hledání
* Následné výzvy k pokračování v upřesnění vyhledávání

![Příklad otázky a odpovědi s metadaty](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Po publikování znalostní báze klientská aplikace odešle otázku uživatele do koncového bodu. Vaše služba QnA Maker zpracuje otázku a odpoví s nejlepší odpovědí.

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Vytvoření, správa a publikování robotovi bez kódu

Portál QnA Maker poskytuje kompletní prostředí pro vytváření znalostní báze. Do znalostní báze můžete importovat dokumenty v jejich aktuální podobě. Tyto dokumenty (například nejčastější dotazy, produktová příručka, tabulka nebo webová stránka) jsou převedeny na sady otázek a odpovědí. Každá sada je analyzována pro následné výzvy a připojena k jiným množinům. Konečný formát _markdown_ podporuje bohatou prezentaci včetně obrázků a odkazů.

Po úpravě znalostní báze publikujte znalostní bázi do pracovního [robota Azure Web Appu,](https://azure.microsoft.com/services/bot-service/) aniž byste museli psát kód. Otestujte svého robota na [webu Azure Portal](https://portal.azure.com) nebo si stáhněte a pokračujte ve vývoji.

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Kvalita vyhledávání a hodnocení poskytuje nejlepší možnou odpověď

QnA Maker systém je vrstvený přístup hodnocení. Data se ukládají ve vyhledávání Azure, které slouží také jako první vrstva hodnocení. Nejlepší výsledky z azure vyhledávání jsou pak předány prostřednictvím QnA Maker nlp re-ranking model k vytvoření konečné výsledky a skóre spolehlivosti.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker vylepšuje proces konverzace

QnA Maker poskytuje vícenásobné výzvy a aktivní učení, které vám pomohou zlepšit základní sady otázek a odpovědí.

**Výzvy s více otočeními** vám dávají možnost připojit dvojice otázek a odpovědí. Toto připojení umožňuje klientské aplikaci poskytnout nejvyšší odpověď a poskytuje další otázky pro upřesnění hledání konečné odpovědi.

Poté, co znalostní báze obdrží otázky od uživatelů v publikovaném koncovém bodě, qnA maker použije **aktivní učení** na tyto skutečné otázky navrhnout změny vaší znalostní báze ke zlepšení kvality.

## <a name="development-lifecycle"></a>Životní cyklus vývoje

QnA Maker poskytuje vytváření, školení a publikování spolu s oprávněními ke spolupráci pro integraci do celého životního cyklu vývoje.

> [!div class="mx-imgBorder"]
> ![Koncepční obraz vývojového cyklu](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="how-do-i-start"></a>Jak mám začít?

**Krok 1**: Vytvoření prostředku QnA Maker na [webu Azure Portal](https://portal.azure.com).

**Krok 2**: Vytvoření znalostní báze na portálu [QnA Maker.](https://www.qnamaker.ai) Přidejte [soubory a adresy URL](../concepts/content-types.md) a vytvořte znalostní bázi.

**Krok 3**: Publikujte znalostní bázi a otestujte z vlastního koncového bodu pomocí [cURL nebo Postman](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

**Krok 4**: Z klientské aplikace programově zavolejte koncový bod znalostní báze. Klientská aplikace zpracuje odpověď JSON tak, aby uživateli zobrazila nejlepší odpověď.

## <a name="next-steps"></a>Další kroky
QnA Maker poskytuje vše, co potřebujete k vytváření, správě a nasazování vlastní znalostní báze.

> [!div class="nextstepaction"]
> [Projděte si nejnovější změny](../whats-new.md)
