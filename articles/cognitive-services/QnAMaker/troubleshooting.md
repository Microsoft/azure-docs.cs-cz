---
title: Řešení potíží – QnA Maker
titleSuffix: Azure Cognitive Services
description: Seznam z nejčastějších dotazů týkajících se QnA Maker služby vám pomůže s tím, že službu povedete rychleji a s lepšími výsledky.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/22/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0b9a5e0797a105d7fe61c95b28a6c5f462e7255
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414747"
---
# <a name="troubleshooting-for-qna-maker"></a>Řešení potíží pro QnA Maker

Seznam z nejčastějších dotazů týkajících se QnA Maker služby vám pomůže s tím, že službu povedete rychleji a s lepšími výsledky.

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Použití robota pro Help na portálu QnA Maker

QnA Maker poskytuje na  portálu QnA maker robota pomocníka, která vám může pomáhat. Robot pro usnadnění je k dispozici na každé webové stránce. Robot používá QnA maker k poskytnutí odpovědí a poskytuje [ C# projekt kódu bot Framework](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot) pro robota, takže můžete rychle začít pracovat s vlastními roboty na základě odpovědí. 

![! [QnA Maker k dispozici je znak * * Help * * robot v rámci portálu QnA Maker, který vám může pomáhat.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Správa znalostní báze

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Odstranění části Moje QnA Maker, co mám dělat? 

Všechny operace odstranění jsou trvalé, včetně páry otázek a odpovědí, souborů, adresy URL, vlastní otázky a odpovědi, znalostních bází nebo prostředky Azure. Ujistěte se, že exportujete ze znalostní báze **nastavení** stránky před odstraněním libovolné části znalostní báze. 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Proč je můj adres(y) URL či souborům není extrahování páry otázka – odpověď?

Je možné, že nástroj QnA Maker nelze automaticky extrahovat otázky a odpovědi (QnA) obsah z platných adres URL Častých. V takových případech můžete vložit obsah otázek a odpovědí v souboru .txt a zobrazit, pokud nástroj může ingestovat. Alternativně můžete pomocí redakčních úprav přidat obsah do znalostní báze prostřednictvím [portál QnA Maker](https://qnamaker.ai).

### <a name="how-large-a-knowledge-base-can-i-create"></a>Jak velkou znalostní bázi můžu vytvořit?

Velikost ve znalostní bázi závisí na skladovou Položku z Azure search, kterou zvolíte, při vytváření služby QnA Maker. Čtení [tady](./Tutorials/choosing-capacity-qnamaker-deployment.md) další podrobnosti.

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Proč nevidím nic v rozevíracím seznamu při pokusu o vytvoření nové znalostní báze?

Zatím jste ještě nevytvořili žádné služby QnA Maker v Azure. Čtení [tady](./How-To/set-up-qnamaker-service-azure.md) se dozvíte, jak to provést.

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Jak sdílet znalostní báze s ostatními?

Sdílení funguje na úrovni služby QnA Maker, to znamená všechny znalostních bází ve službě bude sdílena. Čtení [tady](./How-To/collaborate-knowledge-base.md) jak spolupracovat na znalostní bázi.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Můžete sdílet s přispěvatele, která není ve stejném tenantovi AAD, chcete-li upravit znalostní bázi knowledge base? 

Sdílení je založená na řízení přístupu Azure na základě rolí (RBAC). Pokud můžete sdílet _jakékoli_ prostředků v Azure s jiným uživatelem, můžete také sdílet QnA Maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Pokud máte plán služby App Service s 5 znalostních bází QnA maker. Můžete přiřadit oprávnění pro čtení a zápis 5 různých uživatelů tak, aby každý z nich přístup pouze 1 znalostní báze QnA maker?

Můžete sdílet celou službu QnA maker, ne z individuálních znalostních bází.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Jak můžete změnit výchozí zprávu, pokud není nalezena žádná shoda dobré?

Výchozí zprávu je součástí nastavení ve službě App service.
- Přejít k prostředku služby vaší aplikace na webu Azure Portal

![QnA maker služby App Service](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klikněte na **nastavení** možnost

![nastavení služby App Service QnA maker](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Změňte hodnotu **DefaultAnswer** nastavení
- Restartujte službu App service

![restartování služby App Service QnA maker](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Proč není získávání extrahovat odkaz SharePoint?

Zobrazit [umístění zdroje dat](./Concepts/data-sources-supported.md#data-source-locations) Další informace.

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Aktualizace provedené ve znalostní bázi při publikování neprojevily. Proč ne?

Každou operaci úprav v aktualizace tabulky, testu nebo nastavení, je potřeba uložit před publikováním. Nezapomeňte kliknout na **uložit a jejich trénování** tlačítko po každé operaci úprav.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Podporuje znalostní báze podpory velké množství dat nebo multimédia?

Znalostní báze podporuje Markdown. Ale Automatická extrakce z adres URL má omezené možnosti převodu HTML na Markdown. Pokud chcete využít plnohodnotný Markdown, můžete upravit obsah přímo v tabulce nebo odeslat znalostní bázi s formátovaným obsahem.

Multimediálních souborů, jako jsou obrázky a videa, není v tuto chvíli nepodporuje.

### <a name="does-qna-maker-support-non-english-languages"></a>Podporuje QnA Maker jiné jazyky než angličtina?

Zobrazit další podrobnosti o [podporované jazyky](./Overview/languages-supported.md).

Pokud máte obsah z více jazyků, nezapomeňte vytvořit samostatné služby pro jednotlivé jazyky.

## <a name="manage-service"></a>Správa služby

### <a name="when-should-i-restart-my-app-service"></a>Pokud by měla restartovat Moje služby app service? 

Aktualizovat službu app service, pokud je ikona upozornění vedle hodnoty verze pro znalostní báze v **klíče koncového bodu** tabulky na **uživatelská nastavení** [stránky](https://www.qnamaker.ai/UserSettings).

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Kdy mám obnovit klíče Moje koncový bod?

Aktualizujte klíče koncového bodu, pokud máte podezření, že byl napaden.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>Můžete použít stejný prostředek Azure Search pro znalostní báze pomocí více jazyků?

Pokud chcete použít víc jazyků a více znalostních bází, má uživatel a vytvoří prostředek QnA Maker pro jednotlivé jazyky. Tím se vytvoří samostatná služba Azure Search na jednotlivé jazyky. Kombinování jiný jazyk znalostních bází ve službě Azure search jednoho způsobí snížení relevance výsledků.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Jak můžete změnit název prostředku Azure Search používá nástroj QnA Maker?

Název prostředku Azure Search je název prostředku QnA Maker se některé náhodné písmena přidán na konec. Díky tomu se obtížně rozlišovat mezi více hledat prostředky pro nástroj QnA Maker. Vytvoření samostatné služby Azure Search (pojmenování tak, jak byste chtěli) a připojení ke službě QnA. Postup je podobný postupu je potřeba udělat, abyste [upgrade Azure Search](How-To/upgrade-qnamaker-service.md#upgrade-azure-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Když QnA maker vrátí `Runtime core is not initialized,` , jak ho mám opravit?

Místo na disku pro službu App Service může být plné. Postup opravy místa na disku:

1. V [Azure Portal](https://portal.azure.com)vyberte službu App service vaší QnA maker a pak tuto službu zastavte.
1. Pořád ve službě App Service vyberte **vývojové nástroje**, pak **Rozšířené nástroje**a pak **Přejít**na. Otevře se nové okno prohlížeče.
1. Vyberte **ladit konzola**a pak **cmd** a otevřete nástroj příkazového řádku. 
1. Přejděte do _lokality/wwwroot/data/qnamakerem/_ Directory.
1. Odeberte všechny složky, jejichž název začíná `rd`na. 

    Neodstraňujte následující:

    * KbIdToRankerMappings. txt – soubor
    * Soubor EndpointSettings. JSON
    * EndpointKeys složka 

1. Spusťte službu App Service.
1. Přístup k vaší znalostní bázi vám umožní ověřit, jestli je teď funguje. 


## <a name="integrate-with-other-services-including-bots"></a>Integrace s dalšími službami, včetně robotů

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Pokud chci používat QnA Maker, musím používat Bot Framework?

Ne, nemusíte používat [rozhraní bot Framework](https://github.com/Microsoft/botbuilder-dotnet) s QnA maker. QnA Maker se ale nabízí jako jedna z několika šablon v [Azure bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Bot Service umožňuje rychlý vývoj inteligentních robotů prostřednictvím Microsoft Bot Framework a běží v prostředí bez serveru.

### <a name="how-can-i-create-a-bot-with-qna-maker"></a>Jak lze vytvořit robota pomocí nástroje QnA Maker?

Postupujte podle pokynů v [to](./Tutorials/create-qna-bot.md) dokumentaci k vytvoření vašeho robota pomocí služby Azure Bot Service.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Jak se dá služba QnA Maker vložit na web?

Použijte následující postup vložit službu QnA Maker jako řízení webového chatu na vašem webu:

1. Vytvořte robota nejčastější dotazy týkající se podle následujících pokynů [tady](./Tutorials/create-qna-bot.md).
2. Povolení web chat podle postupu [zde](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

## <a name="data-storage"></a>Úložiště dat

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Jaká data se ukládají a kde je uložen? 

Při vytváření služby QnA Maker vybrané oblasti Azure. Znalostních bází a soubory protokolu se ukládají v této oblasti. 
