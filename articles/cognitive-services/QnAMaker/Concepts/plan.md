---
title: Plánování aplikace – QnA Maker
description: Přečtěte si, jak naplánovat aplikaci QnA Maker. Seznamte se s tím, jak QnA Maker pracuje a komunikuje s dalšími službami Azure a s některými koncepty znalostní báze.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e20679c3999f7ece1f6d3ed47a241cfd9dab9236
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214741"
---
# <a name="plan-your-qna-maker-app"></a>Plánování aplikace QnA Maker

K naplánování QnA Maker aplikace je potřeba pochopit, jak QnA Maker funguje a komunikuje s ostatními službami Azure. Měli byste také mít ucelený přehled o konceptech znalostní báze.

## <a name="azure-resources"></a>Prostředky Azure

Každý [prostředek Azure](azure-resources.md#resource-purposes) vytvořený pomocí QnA Maker má určitý účel. Každý prostředek má svůj účel, omezení a [cenovou úroveň](azure-resources.md#pricing-tier-considerations). Je důležité pochopit funkci těchto prostředků, abyste mohli tyto znalosti použít k procesu plánování.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

| Prostředek | Účel |
|--|--|
| Prostředek [QnA maker](azure-resources.md#qna-maker-resource) | Předpověď vytváření a dotazování |
| Prostředek [kognitivní hledání](azure-resources.md#cognitive-search-resource) | Úložiště dat a hledání |
| Prostředek [služby App Service prostředku a plánu aplikací](azure-resources.md#app-service-and-app-service-plan) | Koncový bod předpovědi dotazů |
| Prostředek [Application Insights](azure-resources.md#application-insights) | Telemetrie prediktivních dotazů |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

| Prostředek | Účel |
|--|--|
| Prostředek [QnA maker](azure-resources.md#qna-maker-resource) | Vytváření, koncový bod a telemetrie prediktivních dotazů|
| Prostředek [kognitivní hledání](azure-resources.md#cognitive-search-resource) | Úložiště dat a hledání |

---
### <a name="resource-planning"></a>Plánování prostředků

Úroveň Free, v `F0` každém prostředku funguje a může poskytovat prostředí prediktivního vytváření a vyvíjení dotazů. Tato úroveň se dá využít k tomu, abyste se dozvěděli o vytváření a předpovědi dotazů. Když přejdete do provozu v produkčním nebo živém scénáři, znovu vyhodnoťte výběr prostředků.

#### <a name="qna-maker-resource"></a>Prostředek QnA Maker

Jeden prostředek QnA Maker může hostovat víc než jednu znalostní bázi. Počet znalostí znalostní báze určuje množství podporovaných indexů cenové úrovně Kognitivní hledání. Přečtěte si další informace o [relacích indexů do znalostní](azure-resources.md#index-usage)báze.

#### <a name="knowledge-base-size-and-throughput"></a>Velikost a propustnost znalostní báze

Při sestavování reálné aplikace Naplánujte dostatek prostředků na velikost znalostní báze a pro očekávané požadavky předpovědi dotazu.

Velikost znalostní báze řídí:
* [Kognitivní hledání](../../../search/search-limits-quotas-capacity.md) omezení cenové úrovně prostředků
* [Omezení QnA Maker](../limits.md)

Požadavek na prediktivní dotazování znalostní báze je řízen plánem webové aplikace a webovou aplikací. Pokud chcete naplánovat cenovou úroveň, přečtěte si [Doporučené nastavení](azure-resources.md#recommended-settings) .

### <a name="resource-sharing"></a>Sdílení prostředků

Pokud už máte některé z těchto prostředků používané, můžete zvážit sdílení prostředků. Podívejte se, které prostředky se [dají sdílet](azure-resources.md#share-services-with-qna-maker) s porozuměním, že sdílení prostředků je pokročilý scénář.

Všechna znalostní báze vytvořená ve stejné QnA Maker prostředku sdílejí stejný koncový bod předpovědi dotazů **testu** .

### <a name="understand-the-impact-of-resource-selection"></a>Pochopení dopadu výběru prostředků

Vhodný výběr prostředků znamená, že znalostní báze odpoví na předpovědi dotaz úspěšně.

Pokud vaše znalostní báze nefunguje správně, obvykle se jedná o problém nesprávné správy prostředků.

Nesprávný výběr prostředku vyžaduje šetření, aby bylo možné určit, který [prostředek se musí změnit](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Znalostní báze

Znalostní báze je přímo spojená s QnA Maker prostředkem. Obsahuje páry otázka a odpověď (QnA), které se používají k zodpovězení požadavků na předpověď dotazů.

### <a name="language-considerations"></a>Jazykové požadavky

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

První znalostní báze vytvořená v prostředku QnA Maker nastaví jazyk pro daný prostředek. Pro prostředek QnA Maker můžete mít jenom jeden jazyk.

Prostředky QnA Maker můžete strukturovat podle jazyka nebo můžete pomocí [překladače](../../translator/translator-info-overview.md) změnit dotaz z jiného jazyka na jazyk znalostní báze před odesláním dotazu do koncového bodu předpovědi dotazu.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

V rámci stejného QnA Maker prostředku teď můžete mít základy znalostní báze v různých jazycích. Když vytvoříte první znalostní bázi Knowledge Base, můžete zvolit, jestli chcete použít prostředek pro znalostní báze v jednom nebo několika jazycích.

![QnA Maker spravované (Preview) výběr vícejazykové znalostní báze](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

> [!NOTE]
> Pokud povolíte jazykové nastavení pro znalostní bázi Knowledge Base, nemůžete ve svém prostředku QnA Maker vytvořit tolik základních základů znalostní báze. [Další podrobnosti o omezeních nastavení jazyka](./azure-resources.md).

---

### <a name="ingest-data-sources"></a>Přijímání zdrojů dat

K vytvoření znalostní báze můžete použít jeden z následujících zpracovaných [zdrojů dat](../Concepts/data-sources-and-content.md) :

* Veřejná adresa URL
* Privátní adresa URL SharePointu
* Soubor

Proces přijímání převede [podporované typy obsahu](../reference-document-format-guidelines.md) na Markdownu. Všechny další úpravy *odpovědi* se provádí pomocí Markdownu. Po vytvoření znalostní báze můžete [QnA páry](question-answer-set.md) upravovat na portálu QnA maker s [bohatým vytvářením textu](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Požadavky na formát dat

Vzhledem k tomu, že je konečný formát dvojice QnA Markdownu, je důležité porozumět [podpoře Markdownu](../reference-markdown-format.md).

Propojené obrázky musí být k dispozici na veřejné adrese URL, aby se zobrazily v podokně test na portálu QnA Maker nebo v klientské aplikaci. QnA Maker neposkytuje ověřování obsahu, včetně obrázků.

### <a name="bot-personality"></a>Preference robota

Pomocí funkce [CHITEST-chat](../how-to/chit-chat-knowledge-base.md)přidejte do své znalostní báze osobnost bot. Tato osobnost se dodává s odpověďmi, které jsou k dispozici v určité konverzační formě, jako je například *Professional* a *Friend*. Tato funkce CHITEST-chat je poskytována jako konverzační sada, kterou máte k dispozici pro přidání, úpravu a odebrání.

Preference robota se doporučuje, pokud se robot připojí k vaší znalostní bázi. Můžete použít funkci CHITEST-chat ve znalostní bázi, a to i v případě, že se přihlásíte i k jiným službám, ale měli byste zkontrolovat, jak služba robot komunikuje s informacemi, jestli je to správný návrh architektury pro vaše použití.

### <a name="conversation-flow-with-a-knowledge-base"></a>Tok konverzace se znalostní bázi

Tok konverzace obvykle začíná oslovením uživatele, například `Hi` nebo `Hello` . Vaše znalostní báze může odpovědět na obecnou odpověď, například `Hi, how can I help you` , a může také poskytnout výběr následných výzev pro pokračování v konverzaci.

Měli byste navrhnout svůj postup konverzace s použitím smyčky, aby uživatel věděl, jak váš robot použil a neopustil robota v konverzaci. [Následné výzvy](../how-to/multiturn-conversation.md) poskytují propojení mezi páry QnA, což umožňuje tok konverzace.

### <a name="authoring-with-collaborators"></a>Vytváření spolupracujících spolupracovníků

Spolupracovníci můžou být jiní vývojáři, kteří sdílejí úplný vývojový zásobník aplikace znalostní báze Knowledge Base nebo můžou být omezené jenom na vytváření znalostní báze.

Vytváření znalostí ve znalostní bázi podporuje několik oprávnění pro přístup na základě rolí, které použijete v Azure Portal k omezení rozsahu schopností spolupracovníka.

## <a name="integration-with-client-applications"></a>Integrace s klientskými aplikacemi

Integrace s klientskými aplikacemi je zajištěna odesláním dotazu do koncového bodu modulu runtime předpovědi. Do vašeho koncového bodu webové aplikace QnA Maker se pošle dotaz do konkrétní znalostní báze se žádostí o sadu SDK nebo na základě REST.

Aby klientská aplikace správně ověřovala požadavky klienta, musí odeslat správné přihlašovací údaje a ID znalostní báze. Pokud používáte Azure Bot Service, nakonfigurujte tato nastavení jako součást konfigurace robota v Azure Portal.

### <a name="conversation-flow-in-a-client-application"></a>Tok konverzace v klientské aplikaci

Tok konverzací v klientské aplikaci, jako je třeba robot Azure, může vyžadovat funkčnost před a po interakci se znalostní bázi.

Podporuje vaše klientská aplikace tok konverzace, a to poskytnutím náhradních prostředků pro zpracování následných výzev nebo zahrnutí funkce CHITEST-CHITEST? V takovém případě si tyto návrhy Navrhněte a ujistěte se, že dotaz klientské aplikace je správně zpracován jinou službou nebo při odeslání do znalostní báze.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>Odeslání mezi QnA Maker a Language Understanding (LUIS)

Klientská aplikace může poskytovat několik funkcí, které jsou zodpovězené znalostní bázi, jenom na jednu z nich. Další funkce stále potřebují pochopit text v konverzaci a extrahovat z něj význam.

Běžnou architekturou klientských aplikací je použití QnA Maker i [Language Understanding (Luis)](../../LUIS/what-is-luis.md) společně. LUIS poskytuje klasifikaci a extrakci textu pro jakýkoli dotaz, včetně dalších služeb. QnA Maker poskytuje odpovědi z vaší znalostní báze.

V takovém scénáři [sdílené architektury](../choose-natural-language-processing-service.md) je odesílání mezi těmito dvěma službami provedeno nástrojem pro [odeslání](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) z bot Framework.

### <a name="active-learning-from-a-client-application"></a>Aktivní učení z klientské aplikace

QnA Maker využívá _aktivní učení_ k vylepšení znalostní báze tím, že navrhuje alternativní otázky na odpověď. Klientská aplikace zodpovídá za součást tohoto [aktivního učení](../How-To/use-active-learning.md). Prostřednictvím výzev pro konverzaci může klientská aplikace zjistit, že znalostní báze vrátil odpověď, která není pro uživatele užitečná, a může určit lepší odpověď. Klientská aplikace musí tyto informace odeslat zpět do znalostní báze, aby se zlepšila kvalita předpovědi.

### <a name="providing-a-default-answer"></a>Zadání výchozí odpovědi

Pokud vaše znalostní báze nenajde odpověď, vrátí _výchozí odpověď_. Tato odpověď se dá nakonfigurovat na stránce **Nastavení** na portálu QnA maker nebo v [rozhraních API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Tato výchozí odpověď se liší od výchozí odpovědi Azure bot. Výchozí odpověď pro Azure bot se konfiguruje v Azure Portal jako součást nastavení konfigurace. Vrátí se, pokud se prahová hodnota skóre nesplní.

## <a name="prediction"></a>Předpověď

Předpověď je odpověď z vaší znalostní báze a obsahuje více informací, než stačí odpověď. Chcete-li získat odpověď předpovědi dotazu, použijte [rozhraní GenerateAnswer API](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Kolísání skóre předpovědi

Skóre se může změnit v závislosti na několika faktorech:

* Počet odpovědí, které jste požadovali v reakci na [GenerateAnswer](query-knowledge-base.md) s `top` vlastností
* Řada dostupných alternativních otázek
* Filtrování metadat
* Odeslané dotazy `test` nebo `production` znalostní bázi

K dispozici jsou [dvě fáze hodnocení odpovědí](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
- Kognitivní hledání – první hodnocení Nastavte _povolený počet odpovědí_ , který je dostatečně vysoký, protože kognitivní hledání a pak předají do QnA maker hodnocení.
- Pořadí QnA Maker sekund. K určení nejlepší odpovědi použijte featurization a Machine Learning.

### <a name="service-updates"></a>Aktualizace služeb

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Použijte [nejnovější aktualizace modulu runtime](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates) pro automatické spravování aktualizací služby.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

V QnA Maker spravovaném (ve verzi Preview) je modul runtime spravovaný pomocí samotné služby QnA Maker. Aktualizace služby proto nelze použít.

---

### <a name="scaling-throughput-and-resiliency"></a>Škálování, propustnost a odolnost

Škálování, propustnost a odolnost proti chybám určují [prostředky Azure](../how-to/set-up-qnamaker-service-azure.md), jejich cenové úrovně a všechny okolní architektury, jako je [Traffic Manager](../how-to/configure-QnA-Maker-resources.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Analýza pomocí Application Insights

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Všechny dotazy do znalostní báze jsou uloženy v Application Insights. Využijte naše [Nejčastější dotazy](../how-to/get-analytics-knowledge-base.md) a pochopte metriky.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

Ve spravovaném nasazení se telemetrie nabízí prostřednictvím [služby Azure monitor](../../../azure-monitor/index.yml). Využijte naše [Nejčastější dotazy](../how-to/get-analytics-knowledge-base.md) a pochopte metriky.


---

## <a name="development-lifecycle"></a>Životní cyklus vývoje

[Životní cyklus vývoje](development-lifecycle-knowledge-base.md) znalostní báze je průběžný: úpravy, testování a publikování znalostní báze.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Vývoj dvojic QnA Maker ve znalostní bázi Knowledge Base

Vaše páry QnA by se měly navrhovat a vyvíjet na základě využití klientských aplikací.

Každý pár může obsahovat:
* Metadata jsou při dotazování, která umožňují označit páry QnA dalšími informacemi o zdroji, obsahu, formátu a účelu vašich dat, filtrovat.
* Následné výzvy – pomáhají určit cestu přes znalostní bázi, aby uživatel dostal správnou odpověď.
* Alternativní otázky – důležité: Pokud chcete, aby vyhledávání odpovídalo vaší odpovědi z různých forem otázky, je důležité. [Aktivní návrhy výukových kurzů](../How-To/use-active-learning.md) se zapínají na alternativní otázky.

### <a name="devops-development"></a>Vývoj DevOps

Vývoj znalostní báze pro vložení do kanálu DevOps vyžaduje, aby znalostní báze byla během dávkového testování izolovaná.

Znalostní báze sdílí index Kognitivní hledání se všemi ostatními základy znalostní báze na prostředku QnA Maker. I když je znalostní báze izolována oddílem, sdílení indexu může v porovnání s publikovanou znalostní báze způsobit rozdíl ve skóre.

Pokud chcete mít _stejné skóre_ na `test` `production` bázi znalostní báze a, izolujte QnA maker prostředek do jedné znalostní báze. V této architektuře musí prostředek fungovat jenom za provozu, dokud se jedná o izolovaný dávkový test.

## <a name="next-steps"></a>Další kroky

* [Prostředky Azure](../how-to/set-up-qnamaker-service-azure.md)
* [Dvojice otázek a odpovědí](question-answer-set.md)
