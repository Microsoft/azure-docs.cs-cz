---
title: Plánování aplikace – QnA Maker
description: Plánování aplikace QnA Maker vyžaduje, abyste porozuměli tomu, jak QnA Maker pracuje a spolupracuje s dalšími službami Azure, a s některými koncepty znalostní báze.
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875706"
---
# <a name="plan-your-qna-maker-app"></a>Plánování aplikace QnA Maker

Plánování aplikace QnA Maker vyžaduje, abyste porozuměli tomu, jak QnA Maker pracuje a spolupracuje s dalšími službami Azure, a s některými koncepty znalostní báze.

## <a name="azure-resources"></a>Prostředky Azure

Každý [prostředek Azure](azure-resources.md#resource-purposes) vytvořený pomocí QnA Maker má určitý účel. Vzhledem k tomu, že každý prostředek má svůj účel, omezení a [cenovou úroveň](azure-resources.md#pricing-tier-considerations), je důležité pochopit, co tyto prostředky slouží jako součást procesu plánování.

|Prostředek|Účel|
|--|--|
| Prostředek [QnA maker](azure-resources.md#qna-maker-resource)|Předpověď vytváření a dotazování|
| Prostředek [kognitivní hledání](azure-resources.md#cognitive-search-resource)|Úložiště dat a hledání|
| Prostředek [služby App Service prostředku a plánu aplikací](azure-resources.md#app-service-and-app-service-plan)|Koncový bod předpovědi dotazů|
| Prostředek [Application Insights](azure-resources.md#application-insights)|Telemetrie prediktivních dotazů|

### <a name="resource-planning"></a>Plánování prostředků

I když vytváříte předpověď vytváření a dotazování s využitím úrovně Free, `F0` každá z prostředků funguje a bude poskytovat prostředí předpovědi vytváření a dotazování. Když přejdete do produkčního, živého, scénáře, znovu vyhodnoťte výběr prostředků.

#### <a name="qna-maker-resource"></a>Prostředek QnA Maker

Jeden prostředek QnA Maker může hostovat víc než jednu znalostní bázi. Počet znalostí znalostní báze určuje množství podporovaných indexů cenové úrovně Kognitivní hledání. Přečtěte si další informace o [relacích indexů do znalostní](azure-resources.md#index-usage)báze.

#### <a name="knowledge-base-size-and-throughput"></a>Velikost a propustnost znalostní báze

Když plánujete sestavit skutečnou aplikaci, naplánujte své prostředky na velikost znalostní báze a požadované požadavky předpovědi dotazu.

Velikost znalostní báze řídí:
* [Kognitivní hledání](../../../search/search-limits-quotas-capacity.md) omezení cenové úrovně prostředků
* [Omezení QnA Maker](../limits.md)

Požadavek na prediktivní dotazování znalostní báze je řízen plánem webové aplikace a webovou aplikací. Pokud chcete naplánovat cenovou úroveň, přečtěte si [Doporučené nastavení](azure-resources.md#recommended-settings) .

### <a name="resource-sharing"></a>Sdílení prostředků

Pokud už máte některé z těchto prostředků používané, můžete zvážit sdílení prostředků. I když některé prostředky [lze sdílet](azure-resources.md#share-services-with-qna-maker), jedná se o pokročilý scénář.

Všechna znalostní báze vytvořená ve stejné QnA Maker prostředku sdílejí stejný koncový bod předpovědi dotazů **testu** .

### <a name="understanding-impact-of-resource-selection"></a>Porozumění dopadu výběru prostředků

Vhodný výběr prostředků znamená, že znalostní báze odpoví na předpovědi dotaz úspěšně.

Pokud vaše znalostní báze nefunguje správně, většinou se jedná o nesprávnou správu prostředků.

Nesprávný výběr prostředku vyžaduje šetření, aby bylo možné určit, který [prostředek se musí změnit](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Znalostní báze

Znalostní báze se přímo váže na svůj prostředek QnA Maker a obsahuje páry otázek a odpovědí (QnA) používané k zodpovězení požadavků na předpověď dotazů.

### <a name="language-considerations"></a>Jazykové požadavky

První znalostní báze vytvořená v prostředku QnA Maker nastaví jazyk pro daný prostředek. Pro prostředek QnA Maker můžete mít jenom jeden jazyk.

Strukturu QnA Maker prostředků podle jazyka nebo použijte [překladatele](../../translator/translator-info-overview.md) ke změně dotazu z jiného jazyka na jazyk znalostní báze před odesláním dotazu do koncového bodu předpovědi dotazu.

### <a name="ingesting-data-sources"></a>Ingestování zdrojů dat

Ingestované [zdroje dat](knowledge-base.md), které se používají k vytvoření znalostní báze, můžou být jedna z následujících:

* Veřejná adresa URL
* Privátní adresa URL SharePointu
* Soubor

Proces přijímání převede [podporované typy obsahu](content-types.md) na Markdownu. Všechny další úpravy *odpovědi* se provádí pomocí Markdownu. Po vytvoření znalostní báze můžete [QnA páry](question-answer-set.md) upravovat na portálu QnA maker s [bohatým vytvářením textu](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Požadavky na formát dat

Vzhledem k tomu, že je konečný formát dvojice QnA Markdownu, je důležité pochopit [podporu Markdownu](../reference-markdown-format.md) .

Aby se daly zobrazit v podokně test na portálu QnA Maker a také v každé klientské aplikaci, musí být připojené obrázky dostupné z veřejné adresy URL, protože QnA Maker neposkytuje ověřování obsahu včetně imagí.

### <a name="bot-personality"></a>Preference robota

Pomocí funkce [CHITEST-chat](../how-to/chit-chat-knowledge-base.md)přidejte do své znalostní báze osobnost bot. Tato osobnost se dodává s odpověďmi, které jsou k dispozici v určité konverzační formě, jako je například *Professional* a *Friend*. Tato funkce CHITEST-chat je poskytována jako konverzační sada, kterou máte k dispozici pro přidání, úpravu a odebrání.

Preference robota se doporučuje, pokud se robot připojí k vaší znalostní bázi. Pokud se připojujete k několika službám, jedním z nich je vaše znalostní báze, můžete se rozhodnout, že budete ve znalostní bázi používat funkci CHITEST-chat, ale měli byste si projít, jak se služba robot komunikuje s cílem zjistit, jestli se jedná o správný návrh architektury pro vaše použití.

### <a name="conversation-flow-with-a-knowledge-base"></a>Tok konverzace se znalostní bázi

Tok konverzace obvykle začíná oslovením uživatele, například `Hi` nebo `Hello` . Vaše znalostní báze může odpovědět na obecnou odpověď, jako `Hi, how can I help you` je například, a může také poskytnout výběr následných výzev, aby bylo možné pokračovat v konverzaci.

Měli byste navrhnout svůj postup konverzace s použitím smyčky, aby uživatel věděl, jak váš robot použil a neopustil robota v konverzaci. [Následné výzvy](../how-to/multiturn-conversation.md) poskytují propojení mezi páry QnA, což umožňuje tok konverzace.

### <a name="authoring-with-collaborators"></a>Vytváření spolupracujících spolupracovníků

Spolupracovníci můžou být jiní vývojáři, kteří sdílejí úplný vývojový zásobník aplikace znalostní báze Knowledge Base nebo můžou být omezené jenom na vytváření znalostní báze.

Vytváření znalostí ve znalostní bázi podporuje několik [oprávnění pro přístup na základě rolí](../how-to/collaborate-knowledge-base.md) , které použijete v Azure Portal k omezení rozsahu schopností spolupracovníka.

## <a name="integration-with-client-applications"></a>Integrace s klientskými aplikacemi

Integrace s [klientskými aplikacemi](integration-with-other-applications.md) znamená odeslání dotazu do koncového bodu předpovědi za běhu. Do vašeho koncového bodu webové aplikace QnA Maker se pošle dotaz do konkrétní znalostní báze se žádostí o sadu SDK nebo na základě REST.

Aby bylo možné požadavek klienta ověřit správně, musí klientská aplikace odeslat správné přihlašovací údaje a ID znalostní báze. Pokud používáte Azure Bot Service, nakonfigurujte nastavení jako součást konfigurace robota v Azure Portal.

### <a name="conversation-flow-in-a-client-application"></a>Tok konverzace v klientské aplikaci

Tok konverzací v [klientské aplikaci](integration-with-other-applications.md), jako je třeba robot Azure, může vyžadovat funkčnost před a po interakci se znalostní bázi.

Pokud vaše klientská aplikace podporuje tok konverzace, protože poskytuje alternativní prostředky pro zpracování následných výzev nebo funkce CHITEST-CHITEST, navrhněte je a ujistěte se, že je dotaz, který se používá v klientské aplikaci, správně zpracovává, a to buď jinou službou, nebo odeslaný do vaší znalostní báze.

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>Odesílání mezi QnA Maker a Language Understanding (LUIS)

Klientská aplikace může poskytovat několik funkcí, které jsou zodpovězené znalostní bázi, jenom na jednu z nich. Další funkce by stále musely pochopit text v konverzaci a extrahovat z něj význam.

Běžnou architekturou klientských aplikací je použití QnA Maker i [Language Understanding (Luis)](../../LUIS/what-is-luis.md) společně. LUIS poskytuje klasifikaci a extrakci textu pro jakýkoli dotaz, včetně dalších služeb, a QnA Maker poskytuje odpovědi z vaší znalostní báze.

Ve [sdílené architektuře](../choose-natural-language-processing-service.md)je odesílání mezi těmito dvěma službami provedeno pomocí nástroje [Dispatcher](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) z rozhraní bot.

### <a name="active-learning-from-a-client-application"></a>Aktivní učení z klientské aplikace

QnA Maker využívá _aktivní učení_ k vylepšení znalostní báze tím, že navrhuje alternativní otázky na odpověď. Klientská aplikace zodpovídá za součást tohoto [aktivního učení](active-learning-suggestions.md). Klientská aplikace, prostřednictvím výzev pro konverzaci, může zjistit, že odpověď vrácená ze znalostní báze nebyla odpověď, kterou uživatel hledal, a určit tak lepší odpověď. Klientská aplikace musí [tyto informace odeslat zpět do znalostní báze](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) , aby se zlepšila kvalita předpovědi.

### <a name="providing-a-default-answer"></a>Zadání výchozí odpovědi

Pokud vaše znalostní báze nenajde odpověď, vrátí _výchozí odpověď_. Tato odpověď se dá nakonfigurovat z portálu QnA Maker, na stránce **Nastavení** nebo v [rozhraních API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Tato výchozí odpověď se liší od výchozí odpovědi Azure bot. Výchozí odpověď na Azure bot je nakonfigurovaná v Azure Portal pro robota jako součást nastavení konfigurace a vrátí se, pokud není splněna prahová hodnota skóre.

## <a name="prediction"></a>Prediction (Předpověď)

Předpověď je odpověď z vaší znalostní báze a obsahuje další informace, než jenom odpověď. Chcete-li získat odpověď předpovědi dotazu, použijte [rozhraní GeneateAnswer API](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Kolísání skóre předpovědi

Skóre se může změnit v závislosti na několika faktorech:

* Počet odpovědí, které jste požadovali v reakci na [GenerateAnswer](query-knowledge-base.md) s `top` vlastností
* Řada dostupných alternativních otázek
* Filtrování metadat
* Odeslané dotazy `test` nebo `production` znalostní bázi

Existuje [hodnocení dvoufázové odpovědi](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
* Kognitivní hledání – první hodnocení – aby odpověď vracela z Kognitivní hledání, musí být _povolený počet odpovědí_ dostatečně vysoký, aby bylo možné zajistit, aby byly nejlepší odpovědi vráceny kognitivní hledání, aby se mohly předat do QnA maker.
* QnA Maker – druhé hodnocení – použijte featurization a Machine Learning a určete nejlepší odpověď.

### <a name="service-updates"></a>Aktualizace služeb

Aktualizace služby se automaticky spravují pomocí [nejnovějších aktualizací modulu runtime](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

### <a name="scaling-throughput-and-resiliency"></a>Škálování, propustnost a odolnost

Škálování, propustnost a odolnost proti chybám určují [prostředky Azure](../how-to/set-up-qnamaker-service-azure.md), jejich cenové úrovně a všechny okolní architektury, jako je [Traffic Manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Analýza pomocí Application Insights

Všechny dotazy do znalostní báze jsou uloženy v Application Insights. Využijte naše [Nejčastější dotazy](../how-to/get-analytics-knowledge-base.md) a pochopte metriky.

## <a name="development-lifecycle"></a>Životní cyklus vývoje

[Životní cyklus vývoje](development-lifecycle-knowledge-base.md) znalostní báze je průběžný: úpravy, testování a publikování znalostní báze.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Vývoj dvojic QnA Maker ve znalostní bázi Knowledge Base

Vaše [páry QnA](question-answer-set.md) by se měly navrhovat a vyvíjet na základě využití klientských aplikací.

Každý pár může obsahovat:
* Metadata jsou při dotazování vyfiltrovaná. To vám umožní označit páry QnA pomocí dalších informací o zdroji, obsahu, formátu a účelu vašich dat.
* Výzvy pro zpracování – Určete cestu přes znalostní bázi, aby uživatel dostal správnou odpověď.
* Alternativní otázky – alternativní otázky jsou důležité k tomu, aby vyhledávání odpovídalo vaší odpovědi z nejrůznějších forem otázek. [Aktivní návrhy výukových kurzů](active-learning-suggestions.md) se zapínají na alternativní otázky.

### <a name="devops-development"></a>Vývoj DevOps

Vývoj znalostní báze pro vložení do kanálu DevOps vyžaduje, aby znalostní báze při [dávkovém testování](../quickstarts/batch-testing.md)byly izolované.

Znalostní báze sdílí index Kognitivní hledání se všemi ostatními základy znalostní báze na prostředku QnA Maker. I když je znalostní báze izolována oddílem, sdílení indexu může v porovnání s publikovanou znalostní báze způsobit rozdíl ve skóre.

Pokud chcete mít _stejné skóre_ na `test` bázi znalostní báze a `production` , izolujte QnA maker prostředek do jedné znalostní báze. V této architektuře musí prostředek živě fungovat jenom na délku izolovaného testu dávky.

## <a name="next-step"></a>Další krok

* [Prostředky Azure](../how-to/set-up-qnamaker-service-azure.md)
* [Dvojice otázek a odpovědí](question-answer-set.md)