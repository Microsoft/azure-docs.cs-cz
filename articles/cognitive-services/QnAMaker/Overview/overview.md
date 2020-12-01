---
title: Co je služba QnA Maker?
description: QnA Maker je cloudová služba NLP, která v rámci vašich dat snadno vytvoří přirozenou konverzační vrstvu. Dá se použít k vyhledání nejvhodnější odpovědi pro všechny vstupy v přirozeném jazyce, od vlastní znalostní báze (KB) informací.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 05/26/2020
ms.custom: cog-serv-seo-aug-2020
keywords: QNA maker, konverzace s nízkým kódem roboty, konverzace s vícenásobným zahnutím
ms.openlocfilehash: 9655f64da455fb7a7124a97e6dc2d1d8437d05b7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353132"
---
# <a name="what-is-qna-maker"></a>Co je QnA Maker?

QnA Maker je cloudová služba pro zpracování přirozeného jazyka (NLP), která umožňuje vytvořit přirozenou konverzační vrstvu přes vaše data. Používá se k nalezení nejvhodnější odpovědi pro libovolný vstup z vaší vlastní znalostní báze (KB) informací.

QnA Maker se běžně používá k sestavování klientských aplikací v konverzaci, mezi které patří aplikace sociálních médií, chat roboty a desktopové aplikace s podporou řeči.

## <a name="when-to-use-qna-maker"></a>Kdy použít QnA Maker

* **Pokud** máte statické informace – použijte QnA maker, když máte statické informace ve znalostní bázi odpovědí. Tato znalostní báze je vlastní pro vaše potřeby, které jste sestavili s dokumenty, jako jsou [soubory PDF a adresy URL](../index.yml).
* **Pokud chcete zadat stejnou odpověď na žádost, dotaz nebo příkaz** – když jiný uživatel odešle stejnou otázku, vrátí se stejná odpověď.
* **Pokud chcete filtrovat statické informace založené na metadatech meta-informace** – Přidání značek [metadat](../how-to/metadata-generateanswer-usage.md) k poskytnutí dalších možností filtrování, které jsou relevantní pro uživatele klientské aplikace a informace. Mezi běžné informace o metadatech patří funkce [CHITEST-chat](../how-to/chit-chat-knowledge-base.md), typ obsahu nebo formát, účel obsahu a aktuálnost obsahu.
* **Pokud chcete spravovat konverzaci robota, která zahrnuje statické informace** – vaše znalostní báze přijímá text nebo příkaz v konverzaci uživatele a odpoví na něj. Pokud je odpověď součástí předem stanoveného toku konverzace, který je ve znalostní bázi v [kontextu vícenásobného](../how-to/multiturn-conversation.md)zastavení, může tento tok snadno poskytnout.

## <a name="what-is-a-knowledge-base"></a>Co je znalostní báze?

QnA Maker [importuje obsah](../index.yml) do dvojice otázek a odpovědí ve znalostní bázi. Proces importu extrahuje informace o vztahu mezi částmi strukturovaného a částečně strukturovaného obsahu, aby vyznamenal vztahy mezi páry otázka a odpověď. Tyto páry otázek a odpovědí můžete upravit nebo přidat nové páry.

Obsah dvojice otázek a odpovědí zahrnuje:
* Všechny alternativní formy otázky
* Značky metadat použité k filtrování voleb odpovědí během hledání
* Další výzvy pro pokračování v vylepšení hledání

![Ukázková otázka a odpověď s metadaty](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

Po publikování znalostní báze pošle klientská aplikace do vašeho koncového bodu otázku uživatele. Vaše služba QnA Maker zpracuje otázku a odpoví nejlepší odpovědí.

## <a name="create-a-chat-bot-programmatically"></a>Vytvoření robota v programu chat

Po publikování QnA Maker znalostní báze pošle klientská aplikace dotaz do koncového bodu znalostní báze a výsledky obdrží jako odpověď JSON. Společná klientská aplikace pro QnA Maker je robotem chatu.

![Požádat o otázku a získat odpověď od obsahu znalostní báze](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Krok|Akce|
|:--|:--|
|1|Klientská aplikace pošle _dotaz_ uživatele (text vlastními slovy), "návody programově aktualizovat moji znalostní bázi?" do koncového bodu znalostní báze.|
|2|QnA Maker využívá školenou znalostní bázi k poskytnutí správné odpovědi a dalších výzev, které lze použít k upřesnění hledání nejlepší odpovědi. QnA Maker vrátí odpověď ve formátu JSON.|
|3|Klientská aplikace používá odpověď JSON k rozhodování o tom, jak pokračovat v konverzaci. Tato rozhodnutí mohou zahrnovat zobrazení horní odpovědi a další možnosti pro upřesnění vyhledávání pro nejlepší odpověď. |
|||

## <a name="build-low-code-chat-bots"></a>Vytváření roboty chatu s nízkým kódem

Portál QnA Maker poskytuje kompletní prostředí pro vytváření znalostí znalostní báze. Do své znalostní báze můžete importovat dokumenty v jejich aktuálním formuláři. Tyto dokumenty (například časté otázky, ruční zadání produktu, tabulka nebo webová stránka) se převádějí na páry otázek a odpovědí. Každý pár se analyzuje pro následné výzvy a připojuje se k ostatním párů. Konečný formát _Markdownu_ podporuje bohatou prezentaci, včetně obrázků a odkazů.

Po úpravě znalostní báze publikujte znalostní bázi do funkčního [robota webové aplikace Azure](https://azure.microsoft.com/services/bot-service/) bez psaní kódu. Otestujte robota v [Azure Portal](https://portal.azure.com) nebo si ho stáhněte a pokračujte v vývoji.

## <a name="high-quality-responses-with-layered-ranking"></a>Vysoce kvalitní odezvy s vrstveným hodnocením

Systém QnA Maker je přístup k vrstveným seřazením. Data se ukládají do služby Azure Search, která také slouží jako první vrstva hodnocení. Výsledky z Azure Search se pak předávají QnA Maker pomocí NLP modelu rehodnocení, který vám umožní dosáhnout konečných výsledků a skóre spolehlivosti.

## <a name="multi-turn-conversations"></a>Vícenásobné konverzace

QnA Maker poskytuje vícenásobné výzvy a aktivní učení, které vám pomůžou vylepšit základní páry otázek a odpovědí.

Funkce **vícenásobného zapnutí** vám umožní propojit páry otázek a odpovědí. Toto připojení umožňuje klientské aplikaci poskytovat nejlepší odpověď a poskytuje další otázky pro upřesnění hledání konečné odpovědi.

Až znalostní báze obdrží dotazy od uživatelů v publikovaném koncovém bodu, QnA Maker použije **aktivní učení** na tyto otázky reálného světa a navrhne tak změny ve znalostní bázi, aby se zlepšila kvalita.

## <a name="development-lifecycle"></a>Životní cyklus vývoje

QnA Maker poskytuje vytváření, školení a publikování společně s oprávněními pro spolupráci k integraci do celého životního cyklu vývoje.

> [!div class="mx-imgBorder"]
> ![Koncepční obrázek vývojového cyklu](../media/qnamaker-overview-learnabout/development-cycle.png)


## <a name="complete-a-quickstart"></a>Dokončení rychlého startu

Nabízíme rychlé zprovoznění ve většině oblíbených programovacích jazyků, z nichž každá je navržena pro učení základních vzorů návrhu a spouštění kódu za méně než 10 minut. Seznam rychlých startů pro jednotlivé funkce najdete v následujícím seznamu.

* [Začínáme s klientskou knihovnou QnA Maker](../quickstarts/quickstart-sdk.md)
* [Začínáme s portálem QnA Maker](../quickstarts/create-publish-knowledge-base.md)
* [Začínáme s rozhraními REST API pro QnA Maker](../quickstarts/quickstart-rest-curl.md)


## <a name="next-steps"></a>Další kroky
QnA Maker poskytuje vše, co potřebujete k sestavování, správě a nasazení vlastní znalostní báze.

> [!div class="nextstepaction"]
> [Projděte si nejnovější změny.](../whats-new.md)