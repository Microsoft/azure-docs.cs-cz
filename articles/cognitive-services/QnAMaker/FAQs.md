---
title: Časté otázky – kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Nejčastější dotazy
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: a6bf32549715d0357771b3f3b0ff72f64788ec20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343264"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Proč je můj adresy URL / soubory není extrahujte dvojice odpovědí otázku?

Je možné, že QnA Maker nelze automaticky extrakce obsahu (QnA) otázku a odpověď z platné adresy URL – nejčastější dotazy. V takových případech můžete vložit obsah QnA v souboru .txt a v tématu, pokud nástroj dokáže přijímat. Alternativně můžete redakčně přidat obsah do znalostní báze.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Jak velkou znalostní bázi můžu vytvořit?

Velikost znalostní báze závisí na SKU z Azure search, který si zvolíte při vytváření služby QnA Maker. Čtení [sem](./Tutorials/choosing-capacity-qnamaker-deployment.md) další podrobnosti.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Proč nevidím v rozevíracím seznamu pro nic při pokusu vytvořit nové báze knowledge base?

Ještě jste nevytvořili žádné program QnA Maker služby v Azure. Čtení [sem](./How-To/set-up-qnamaker-service-azure.md) jak to provést.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Jak sdílet znalostní bázi knowledge base s jinými?

Sdílení funguje na úrovni služby QnA Maker, tj. všechny znalostních bází v služby budou sdílet. Čtení [sem](./How-To/collaborate-knowledge-base.md) jak spolupracovat na bázi knowledge base.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Jak můžete změnit výchozí zprávu, pokud není nalezena žádná shoda dobrý?

Výchozí zprávu je součástí nastavení v App service.
- Přejděte na služby prostředek vaší aplikace na portálu Azure

![qnamaker služby App Service](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klikněte na **nastavení** možnost

![nastavení qnamaker služby App Service](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Změňte hodnotu **DefaultAnswer** nastavení
- Restartovat App service

![restartování služby App Service qnamaker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Proč není získávání extrahovat Můj odkaz SharePoint?

Nástroj analyzuje pouze veřejné adresy URL a v tuto chvíli nepodporuje ověřené datové zdroje. Alternativně můžete soubor stáhnout a použít možnost nahrávání souborů k extrahování otázky a odpovědi.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Aktualizace provedené na můj znalostní báze se neprojeví publikování. Proč ne?

Každé operace upravit v tabulce aktualizace, testovací nebo nastavení, musí být před publikováním uložit. Nezapomeňte cvičení tlačítko po každé operace upravit a klikněte na tlačítko Uložit.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Pokud by měl aktualizovat mé klíče koncový bod?

Pokud máte podezření, že byl napaden, měli byste aktualizovat klíče pro koncový bod.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Podporuje bohaté data podporu znalostní báze knowledge base nebo multimédia?

Znalostní báze podporuje Markdown. Však automaticky extrahování z adresy URL omezené funkce převodu HTML Markdownu. Pokud chcete použít plně kvalifikované Markdownu, můžete upravit obsah přímo v tabulce nebo nahrát znalostní bázi s bohatou obsahem.

Multimédia, jako jsou bitové kopie a videa, není v současné době podporovaný.

## <a name="does-qna-maker-support-non-english-languages"></a>Podporuje QnA Maker jiných než anglických jazyků?

Zobrazte další podrobnosti [podporované jazyky](./Overview/languages-supported.md).

Pokud máte obsah z více jazyků, ujistěte se, že jste vytvoření samostatné služby pro jednotlivé jazyky.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Pokud chci používat QnA Maker, musím používat Bot Framework?

Ne, není potřeba používat rozhraní robota s QnA Maker. Ale QnA Maker je poskytován jako jednu z několika šablon ve službě Azure robota Service. Robota Service umožňuje rychlé inteligentního robota vývoj prostřednictvím Microsoft robota Framework a běží na serveru menší prostředí.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Jak můžete vytvořit robota pomocí QnA Maker?

Postupujte podle pokynů v [to](./Tutorials/create-qna-bot.md) dokumentace k vytvoření vašeho robota s Azure robota.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Jak se dá služba QnA Maker vložit na web?

Postupujte podle těchto kroků pro vložení službu QnA Maker jako ovládací prvek webového chatu ve vašem webu:

1. Vytvoření vaší robota – nejčastější dotazy podle těchto pokynů [zde](./Tutorials/create-qna-bot.md).
2. Pomocí následujících kroků povolte webového chatu [sem](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-webchat)


