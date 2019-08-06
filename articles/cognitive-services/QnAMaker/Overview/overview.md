---
title: Co je služba QnA Maker?
titleSuffix: Azure Cognitive Services
description: QnA Maker je cloudová služba NLP, která v rámci vašich dat snadno vytvoří přirozenou konverzační vrstvu. Dá se použít k vyhledání nejvhodnější odpovědi pro všechny vstupy v přirozeném jazyce, od vlastní znalostní báze (KB) informací.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 08/01/2019
ms.author: diberry
ms.openlocfilehash: f56798359cdc8739a363bed3bfddadd584617adf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815485"
---
# <a name="what-is-the-qna-maker-service"></a>Co je služba QnA Maker?

QnA Maker je cloudová služba pro zpracování přirozeného jazyka (NLP), která snadno vytvoří přirozenou konverzační vrstvu nad vašimi daty. Dá se použít k vyhledání nejvhodnější odpovědi pro všechny vstupy v přirozeném jazyce, od vlastní znalostní báze (KB) informací.

Klientská aplikace pro QnA Maker je jakákoli konverzační aplikace, která komunikuje s uživatelem v přirozeném jazyce a odpoví na otázku. Mezi příklady klientských aplikací patří aplikace sociálních médií, chatboti a desktopové aplikace s podporou hlasových služeb.

## <a name="when-to-use-qna-maker"></a>Kdy použít QnA Maker

* **Pokud** máte statické informace – použijte QnA maker, když máte statické informace ve znalostní bázi odpovědí. Tato znalostní báze je vlastní pro vaše potřeby, které jste sestavili s dokumenty, jako jsou [soubory PDF a adresy URL](../concepts/data-sources-supported.md).
* **Pokud chcete zadat stejnou odpověď na žádost, dotaz nebo příkaz** – když jiný uživatel odešle stejnou otázku, stejná odpověď se vrátí do obou. 
* **Pokud chcete filtrovat statické informace založené na metadatech meta-informace** – Přidání značek [metadat](../how-to/metadata-generateanswer-usage.md) k poskytnutí dalších možností filtrování, které jsou relevantní pro uživatele klientské aplikace a informace. Mezi běžné informace o metadatech patří funkce [CHITEST-chat](../how-to/chit-chat-knowledge-base.md), typ obsahu nebo formát, účel obsahu a aktuálnost obsahu.
* **Pokud chcete spravovat konverzaci robota, která zahrnuje statické informace** – vaše znalostní báze přebírá text nebo příkaz v konverzaci uživatele a odpoví na něj. Pokud je odpověď součástí předem stanoveného toku konverzace, který je ve znalostní bázi v [kontextu vícenásobného](../how-to/multiturn-conversation.md)zastavení, může tento tok snadno poskytnout.  

## <a name="use-qna-maker-knowledge-base-in-a-chat-bot"></a>Použití QnA Maker Knowledge Base v robotovi chatu

Po publikování QnA Maker znalostní báze pošle klientská aplikace dotaz do koncového bodu znalostní báze a výsledky obdrží jako odpověď JSON. Společná klientská aplikace pro QnA Maker je robotem chatu.

![Požádat o otázku a získat odpověď od obsahu znalostní báze](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Krok|Akce|
|:--|:--|
|1|Klientská aplikace pošle _dotaz_ uživatele (text vlastními slovy), "návody programově aktualizovat moji znalostní bázi?" do koncového bodu znalostní báze.|
|2|QnA Maker využívá školenou znalostní bázi k poskytnutí správné odpovědi a dalších výzev, které lze použít k upřesnění hledání nejlepší odpovědi. QnA Maker vrátí odpověď ve formátu JSON.|
|3|Klientská aplikace používá odpověď JSON k rozhodování o tom, jak pokračovat v konverzaci. Mezi tato rozhodnutí patří například zobrazení horní odpovědi nebo předvádění dalších možností pro upřesnění vyhledávání pro nejlepší odpověď. |
|||

## <a name="what-is-a-knowledge-base"></a>Co je znalostní báze? 

QnA Maker [importuje obsah](../concepts/data-sources-supported.md) do znalostní báze sad otázek a odpovědí. Proces importu extrahuje informace o vztahu mezi částmi strukturovaného a částečně strukturovaného obsahu, aby vyznamenal vztahy mezi otázkami a sadami odpovědí. Tyto otázky a sady odpovědí můžete upravit nebo přidat nové.  

Obsah sady otázek a odpovědí zahrnuje všechny alternativní otázky pro konkrétní odpověď, značky metadat použité k filtrování výběru odpovědí během hledání a následné výzvy pro pokračování v upřesnění hledání.

![Ukázková otázka a odpověď s metadaty](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Po publikování znalostní báze pošle klientská aplikace do vašeho koncového bodu otázku uživatele. Vaše služba QnA Maker zpracuje otázku a odpoví nejlepší odpovědí. 

## <a name="create-manage-and-publish-to-a-bot-without-code"></a>Vytváření, Správa a publikování na robotu bez kódu

Portál QnA Maker poskytuje kompletní prostředí pro vytváření znalostí znalostní báze. Do své znalostní báze můžete importovat dokumenty v jejich aktuálním formuláři. Tyto dokumenty (například časté otázky, ruční zadání produktu, tabulka nebo webová stránka) se převedou na sady otázek a odpovědí. Každá sada je analyzována pro následné výzvy a připojená k jiným sadám. Konečný formát Markdownu podporuje bohatou prezentaci, včetně obrázků a odkazů. 

Po úpravě znalostní báze publikujte znalostní bázi do funkčního [robota webové aplikace Azure](https://azure.microsoft.com/services/bot-service/) bez psaní kódu. Vyzkoušejte si robota v [Azure Portal](https://portal.azure.com) nebo si stáhněte a pokračujte v vývoji. 

## <a name="search-quality-and-ranking-provides-the-best-possible-answer"></a>Hledání kvality a hodnocení poskytuje nejlepší možnou odpověď.

Systém QnA Maker je přístup k vrstveným seřazením. Data se ukládají do služby Azure Search, která také slouží jako první vrstva hodnocení. Výsledky z Azure Search se pak předávají QnA Maker pomocí NLP modelu rehodnocení, který vám umožní dosáhnout konečných výsledků a skóre spolehlivosti.

## <a name="qna-maker-improves-the-conversation-process"></a>QnA Maker vylepšuje proces konverzace.

QnA Maker poskytuje vícenásobné výzvy a aktivní učení, které vám pomůžou vylepšit základní otázky a sady odpovědí. 

Funkce **vícenásobného zapnutí** vám umožní propojit páry otázek a odpovědí. Toto připojení umožňuje klientské aplikaci poskytovat nejlepší odpověď a poskytuje další otázky pro upřesnění hledání konečné odpovědi. 

Až znalostní báze obdrží dotazy od uživatelů v publikovaném koncovém bodu, QnA Maker použije **aktivní učení** na tyto otázky reálného světa a navrhne tak změny ve znalostní bázi, aby se zlepšila kvalita. 

## <a name="development-lifecycle"></a>Životní cyklus vývoje

QnA Maker poskytuje vytváření, školení a publikování společně s oprávněními pro spolupráci k integraci do celého životního cyklu vývoje. 

## <a name="how-do-i-start"></a>Jak začít?

**Krok 1**: Vytvoří prostředek QnA Maker v [Azure Portal](https://portal.azure.com). 

**Krok 2**: Vytvořte znalostní bázi na portálu [QnA maker](https://www.qnamaker.ai) . Přidejte [soubory a adresy URL](../concepts/data-sources-supported.md) pro vytvoření znalostní báze.  

**Krok 3**: Publikování znalostní báze a testování z vlastního koncového bodu pomocí [oblé](../quickstarts/get-answer-from-kb-using-curl.md) nebo [post](../quickstarts/get-answer-from-kb-using-postman.md). 

**Krok 4**: Z klientské aplikace programově zavolejte koncový bod znalostní báze a přečtěte si odpověď JSON. zobrazí se nejlepší odpověď pro uživatele.  

## <a name="news-and-updates"></a>Novinky a aktualizace

Přečtěte si, co je nového v QnA Maker.

* Červeně 2019
    * Vylepšený model hodnocení pro francouzštinu, italštinu, němčinu, španělštinu, portugalštinu
* Duben 2019
    * Podpora extrakce obsahu webu
    * Podpora dokumentů SharePointu
* Březen 2019
    * Aktivní učení 
    * Vylepšený model "NLP Rank" pro angličtinu, 

## <a name="next-steps"></a>Další postup
QnA Maker poskytuje vše, co potřebujete k sestavování, správě a nasazení vlastní znalostní báze. 

> [!div class="nextstepaction"]
> [Vytvoření služby QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
