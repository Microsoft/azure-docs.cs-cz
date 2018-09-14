---
title: Časté otázky – QnA Maker
titleSuffix: Azure Cognitive Services
description: Seznam nejčastějších dotazů týkajících se služba QnA Maker
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: nstulasi
ms.openlocfilehash: 0af93682c4a1be4de4d92e9c44e10586f740d8bf
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576676"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>Proč je můj adresy URL / soubory není extrahování páry otázka – odpověď?

Je možné, že nástroj QnA Maker nelze automaticky extrahovat otázky a odpovědi (QnA) obsah z platných adres URL Častých. V takových případech můžete vložit obsah otázek a odpovědí v souboru .txt a zobrazit, pokud nástroj může ingestovat. Alternativně můžete pomocí redakčních úprav přidat obsah do znalostní báze.

## <a name="how-large-a-knowledge-base-can-i-create"></a>Jak velkou znalostní bázi můžu vytvořit?

Velikost ve znalostní bázi závisí na skladovou Položku z Azure search, kterou zvolíte, při vytváření služby QnA Maker. Čtení [tady](./Tutorials/choosing-capacity-qnamaker-deployment.md) další podrobnosti.

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>Proč se mi nezobrazují v rozevíracího seznamu pro se nic při pokusu o vytvoření nové znalostní báze?

Zatím jste ještě nevytvořili žádné služby QnA Maker v Azure. Čtení [tady](./How-To/set-up-qnamaker-service-azure.md) jak na to.

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>Jak se dá sdílet znalostní báze s ostatními?

Sdílení funguje na úrovni služby QnA Maker, to znamená všechny znalostních bází ve službách se sdílet. Čtení [tady](./How-To/collaborate-knowledge-base.md) jak spolupracovat na znalostní bázi.

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Jak můžete změnit výchozí zprávu, pokud není nalezena žádná shoda dobré?

Výchozí zprávu je součástí nastavení ve službě App service.
- Přejděte prostředku služby vaší aplikace na webu Azure Portal

![QnA maker služby App Service](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klikněte na **nastavení** možnost

![nastavení služby App Service QnA maker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Změňte hodnotu **DefaultAnswer** nastavení
- Restartujte službu App service

![restartování služby App Service QnA maker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Proč není získávání extrahovat odkaz SharePoint?

Nástroj analyzuje pouze veřejné adresy URL a ověřených zdrojů v tuto chvíli nepodporuje. Alternativně můžete stáhnout soubor a pomocí možnosti nahrávání souborů extrakce otázek a odpovědí.


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Aktualizace provedené ve znalostní bázi při publikování neprojevily. Proč ne?

Každou operaci úprav v aktualizace tabulky, testu nebo nastavení, je potřeba uložit před publikováním. Nezapomeňte si kliknutím na Uložit a jejich trénování tlačítko po každé operaci úprav.

## <a name="when-should-i-refresh-my-endpoint-keys"></a>Kdy mám obnovit klíče Moje koncový bod?

Pokud máte podezření, že jsou ohrožené, měli byste aktualizovat vaše klíče koncového bodu.

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Podporuje znalostní báze podpory velké množství dat nebo multimédia?

Znalostní báze podporuje Markdown. Ale Automatická extrakce z adres URL má omezené možnosti převodu HTML na Markdown. Pokud chcete využít plnohodnotný Markdown, můžete upravit obsah přímo v tabulce nebo odeslat znalostní bázi s formátovaným obsahem.

Multimediálních souborů, jako jsou obrázky a videa, není v tuto chvíli nepodporuje.

## <a name="does-qna-maker-support-non-english-languages"></a>Podporuje QnA Maker jiné jazyky než angličtina?

Zobrazit další podrobnosti o [podporované jazyky](./Overview/languages-supported.md).

Pokud máte obsah z více jazyků, nezapomeňte vytvořit samostatné služby pro jednotlivé jazyky.

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Pokud chci používat QnA Maker, musím používat Bot Framework?

Ne, není potřeba rozhraní Bot Framework pomocí nástroje QnA Maker. Nástroj QnA Maker se ale nabízí jako jeden z několika šablon služby Azure Bot Service. Bot Service umožňuje rychlý vývoj inteligentních robotů prostřednictvím Microsoft Bot Framework a běží na serveru míň prostředí.

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>Jak lze vytvořit robota pomocí nástroje QnA Maker?

Postupujte podle pokynů v [to](./Tutorials/create-qna-bot.md) dokumentaci k vytvoření vašeho robota s robotem Azure.

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Jak se dá služba QnA Maker vložit na web?

Použijte následující postup vložit službu QnA Maker jako řízení webového chatu na vašem webu:

1. Vytvořte robota nejčastější dotazy týkající se podle následujících pokynů [tady](./Tutorials/create-qna-bot.md).
2. Povolení web chat podle postupu [zde](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)


