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
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 51d21dca1d8a5223e67cb7ea8489800989cff55c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026032"
---
# <a name="troubleshooting-for-qna-maker"></a>Řešení potíží pro QnA Maker

Seznam z nejčastějších dotazů týkajících se QnA Maker služby vám pomůže s tím, že službu povedete rychleji a s lepšími výsledky.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>Jak získat koncový bod služby Qnamakerem

Koncový bod služby Qnamakerem je užitečný pro účely ladění při kontaktování podpory Qnamakerem nebo UserVoice. Koncový bod je adresa URL v tomto formátu: https://your-resource-name.azurewebsites.net.
    
1. Přejít do služby Qnamakerem (skupina prostředků) v [Azure Portal](https://portal.azure.com)

    ![Qnamakerem skupiny prostředků Azure v Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Vyberte App Service přidružené k prostředku QnA Maker. Názvy jsou obvykle stejné.

     ![Vyberte Qnamakerem App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Adresa URL koncového bodu je k dispozici v části Přehled.

    ![Koncový bod Qnamakerem](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>Použití robota pro Help na portálu QnA Maker

QnA Maker poskytuje na portálu QnA Maker robota **pomocníka** , která vám může pomáhat. Robot pro usnadnění je k dispozici na každé webové stránce. Robot používá QnA maker k poskytnutí odpovědí a poskytuje [ C# projekt kódu bot Framework](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support) pro robota, takže můžete rychle začít pracovat s vlastními roboty na základě odpovědí. 

![! [QnA Maker k dispozici je znak * * Help * * robot v rámci portálu QnA Maker, který vám může pomáhat.] (./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>Správa znalostní báze

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>Omylem jsem odstranili součást mých QnA Maker, co mám dělat? 

Neodstraňujte žádnou ze služeb Azure vytvořených společně s QnA Makerm prostředkem, jako je například Search nebo webová aplikace. To je nezbytné, aby QnA Maker fungovalo, pokud ho odstraníte, QnA Maker přestane fungovat správně.

Všechna odstranění jsou trvalá, včetně párů dotazů a odpovědí, souborů, adres URL, vlastních otázek a odpovědí, znalostní báze nebo prostředků Azure. Před odstraněním jakékoli části znalostní báze se ujistěte, že jste vyexportovali svou znalostní bázi ze stránky **Nastavení** . 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>Proč moje adresy URL neextrahují páry otázek a odpovědí?

Je možné, že QnA Maker nemůže automaticky extrahovat obsah dotazů a odpovědí (QnA) z platných adres URL pro nejčastější dotazy. V takových případech můžete vložit obsah QnA do souboru. txt a zjistit, jestli ho může nástroj ingestovat. Alternativně můžete pomocí [portálu QnA maker](https://qnamaker.ai)přidat obsah do znalostní báze.

### <a name="how-large-a-knowledge-base-can-i-create"></a>Jak velká znalostní báze můžu vytvořit?

Velikost znalostní báze závisí na SKU Azure Search, které zvolíte při vytváření služby QnA Maker. Další podrobnosti najdete [tady](./Tutorials/choosing-capacity-qnamaker-deployment.md) .

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>Proč se při pokusu o vytvoření nové znalostní báze nezobrazí žádné informace v rozevíracím seznamu?

Ještě jste nevytvořili žádné služby QnA Maker v Azure. Další informace o tom, jak to provést, najdete [tady](./How-To/set-up-qnamaker-service-azure.md) .

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>Návody sdílet znalostní bázi s ostatními?

Sdílení funguje na úrovni služby QnA Maker, to znamená, že všechny databáze znalostí ve službě budou sdíleny. Přečtěte [si](./How-To/collaborate-knowledge-base.md) , jak spolupracovat na znalostní bázi.

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>Můžete znalostní bázi sdílet s přispěvatelem, který není ve stejném tenantovi AAD, abyste mohli upravit znalostní bázi? 

Sdílení je založené na řízení přístupu na základě role (RBAC) v Azure. Pokud můžete sdílet _libovolný_ prostředek v Azure s jiným uživatelem, můžete také sdílet QnA maker.

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>Pokud máte plán App Service s 5 Qnamakerem znalostní báze. Můžete přiřadit práva ke čtení a zápisu pro 5 různých uživatelů, aby každý z nich měl přístup jenom k 1 Qnamakerem znalostní bázi Knowledge Base?

Můžete sdílet celou službu Qnamakerem, ne jednotlivé základy znalostní báze.

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>Jak změním výchozí zprávu, když se nenajde žádná dobrá shoda?

Výchozí zpráva je součástí nastavení ve službě App Service.
- V Azure Portal přejdete do prostředku App Service.

![qnamakerem AppService](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klikněte na možnost **Nastavení** .

![nastavení qnamakerem AppService](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Změna hodnoty nastavení **DefaultAnswer**
- Restartujte službu App Service.

![qnamakerem AppService restartování](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>Proč můj odkaz na SharePoint nezíská extrakci?

Další informace najdete v tématu [umístění zdrojů dat](./Concepts/data-sources-supported.md#data-source-locations) .

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>Aktualizace, které jsem udělali ve znalostní bázi, se při publikování neprojeví. Proč?

Všechny operace úprav, ať už v tabulce, test nebo nastavení, je třeba před publikováním Uložit. Nezapomeňte kliknout na tlačítko **Uložit a naučit** se po každé operaci úprav.

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>Podporuje znalostní báze bohatá data a multimédia?

Znalostní báze podporuje Markdownu. Automatické extrakce z adres URL ale má omezené možnosti převodu z formátu HTML na Markdownu. Pokud chcete použít Full-podrobnějším Markdownu, můžete změnit obsah přímo v tabulce nebo odeslat znalostní bázi s bohatým obsahem.

Multimédia, jako jsou obrázky a videa, se v tuto chvíli nepodporují.

### <a name="does-qna-maker-support-non-english-languages"></a>Podporuje QnA Maker jiné než anglické jazyky?

Přečtěte si další podrobnosti o [podporovaných jazycích](./Overview/languages-supported.md).

Pokud máte obsah z více jazyků, nezapomeňte vytvořit samostatnou službu pro každý jazyk.

## <a name="manage-service"></a>Spravovat službu

### <a name="when-should-i-restart-my-app-service"></a>Kdy bych měl restartovat službu App Service? 

Aktualizujte službu App Service, pokud je ikona upozornění vedle hodnoty verze znalostní báze v tabulce **klíče koncového bodu** na [stránce](https://www.qnamaker.ai/UserSettings) **uživatelská nastavení** .

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>Odstranil (a) jsem stávající vyhledávací službu. Jak to mohu napravit?

Odstraníte-li index Azure Search, operace je finální a index nelze obnovit. 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>Odstranil (a) jsem index `testkb` ve vyhledávací službě. Jak to mohu napravit? 

Stará data se nedají obnovit. Vytvořte nový prostředek QnA Maker a znovu vytvořte znalostní bázi.

### <a name="when-should-i-refresh-my-endpoint-keys"></a>Kdy mám aktualizovat klíče koncového bodu?

Aktualizujte klíče koncového bodu, pokud máte podezření, že došlo k ohrožení zabezpečení.

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>Můžu použít stejný Azure Search prostředek pro znalostní báze s použitím více jazyků?

Chcete-li použít více jazyků a více znalostní báze, musí uživatel vytvořit prostředek QnA Maker pro každý jazyk. Tím se vytvoří samostatná služba Azure Search na jednotlivé jazyky. Kombinování různých jazykových znalostí v jedné službě Azure Search má za následek zhoršení důležitosti výsledků.

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>Jak můžu změnit název prostředku Azure Search používaného QnA Maker?

Název prostředku Azure Search je QnA Maker název prostředku s některými náhodnými písmeny připojenými na konci. Díky tomu je obtížné rozlišovat mezi více prostředky vyhledávání pro QnA Maker. Vytvořte samostatnou službu Azure Search (pojmenujte ji tak, jak byste chtěli) a připojte ji ke službě QnA. Postup je podobný postupům, které je třeba provést při [upgradu služby Azure Search](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-search-service).

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>Když QnA Maker vrátí `Runtime core is not initialized,`, jak ho mám opravit?

Místo na disku pro službu App Service může být plné. Postup opravy místa na disku:

1. V [Azure Portal](https://portal.azure.com)vyberte službu App service vaší QnA maker a pak tuto službu zastavte.
1. Pořád ve službě App Service vyberte **vývojové nástroje**, pak **Rozšířené nástroje**a pak **Přejít**na. Otevře se nové okno prohlížeče.
1. Vyberte **ladit konzola**a pak **cmd** a otevřete nástroj příkazového řádku. 
1. Přejděte do _lokality/wwwroot/data/qnamakerem/_ Directory.
1. Odeberte všechny složky, jejichž název začíná na `rd`. 

    **Neodstraňujte** následující:

    * KbIdToRankerMappings. txt – soubor
    * Soubor EndpointSettings. JSON
    * EndpointKeys složka 

1. Spusťte službu App Service.
1. Přístup k vaší znalostní bázi vám umožní ověřit, jestli je teď funguje. 


## <a name="integrate-with-other-services-including-bots"></a>Integrace s dalšími službami, včetně roboty

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Potřebuji použít rozhraní bot Framework, aby bylo možné použít QnA Maker?

Ne, nemusíte používat [rozhraní bot Framework](https://github.com/Microsoft/botbuilder-dotnet) s QnA maker. QnA Maker se ale nabízí jako jedna z několika šablon v [Azure bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Robot Service umožňuje rychlý vývoj v rámci inteligentních robotů přes Microsoft bot Framework a běží v prostředí bez serveru.

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>Jak můžu vytvořit nový robot s QnA Maker?

Podle pokynů v [této](./Tutorials/create-qna-bot.md) dokumentaci vytvořte robota s Azure bot Service.

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>Návody použít jinou znalostní bázi se stávající službou Azure bot Service?

Musíte mít následující informace o znalostní bázi Knowledge Base:

* ID znalostní báze
* Název vlastní subdomény publikovaného koncového bodu znalostní báze známý jako `host`, který se na stránce **Nastavení** našel po publikování.
* Klíč publikovaného koncového bodu znalostní báze – byl po publikování na stránce **Nastavení** nalezen. 

Tyto informace najdete v Azure Portal do App Service bot. V části **Nastavení-> konfigurace-> nastavení aplikace**změňte tyto hodnoty.  

Klíč koncového bodu znalostní báze je označený jako `QnAAuthkey` ve službě ABS. 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>Můžou dvě nebo víc klientských aplikací sdílet znalostní bázi? 

Ano, znalostní bázi se můžete dotazovat z libovolného počtu klientů. Pokud se odezva ze znalostní báze jeví jako pomalá nebo časový limit, zvažte možnost upgradovat úroveň služby pro službu App Service přidruženou ke znalostní bázi.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>Návody službu QnA Maker na svém webu vložit?

Pomocí těchto kroků vložte QnA Maker službu jako ovládací prvek webové konverzace na svém webu:

1. Pomocí pokynů [zde můžete](./Tutorials/create-qna-bot.md)vytvořit robotská okna s nejčastějšími dotazy.
2. Povolte webový chat podle kroků uvedených [tady](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat) .

## <a name="data-storage"></a>Úložiště dat

### <a name="what-data-is-stored-and-where-is-it-stored"></a>Jaká data jsou uložená a kde jsou uložená? 

Při vytváření služby QnA Maker jste vybrali oblast Azure. Vaše znalostní báze a soubory protokolu jsou uloženy v této oblasti. 
