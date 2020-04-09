---
title: Prostředky Azure – QnA Maker
description: QnA Maker používá několik zdrojů Azure, každý s jiným účelem. Pochopení toho, jak se používají jednotlivě, vám umožní naplánovat a vybrat správnou cenovou úroveň nebo vědět, kdy změnit cenovou úroveň. Pochopení toho, jak se používají v kombinaci umožňuje najít a opravit problémy, když k nim dojde.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 581029d2372f7a2ef704dcf02f266b66440aa246
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873901"
---
# <a name="azure-resources-for-qna-maker"></a>Prostředky Azure pro QnA Maker

QnA Maker používá několik zdrojů Azure, každý s jiným účelem. Pochopení toho, jak se používají jednotlivě, vám umožní naplánovat a vybrat správnou cenovou úroveň nebo vědět, kdy změnit cenovou úroveň. Pochopení toho, jak se používají _v kombinaci_ umožňuje najít a opravit problémy, když k nim dojde.

## <a name="resource-planning"></a>Plánování zdrojů

Při prvním vývoji znalostní báze QnA Maker ve fázi prototypu je běžné mít jeden prostředek QnA Maker pro testování i výrobu.

Když se přesunete do fáze vývoje projektu, měli byste zvážit:

* kolik jazyků bude systém znalostní báze držet
* kolik regionů potřebujete, aby vaše znalostní báze byla k dispozici
* kolik dokumentů v každé doméně bude systém obsahovat

Naplánujte, že jeden prostředek QnA Maker uchová všechny znalostní báze, které mají stejný jazyk, stejnou oblast a stejnou kombinaci domény předmětu.

## <a name="pricing-tier-considerations"></a>Aspekty cenové úrovně

Obvykle existují tři parametry, které je třeba zvážit:

* **Propustnost, kterou potřebujete od služby**:
    * Vyberte vhodný [plán aplikací](https://azure.microsoft.com/pricing/details/app-service/plans/) pro vaši službu App na základě vašich potřeb. Můžete [vertikálně navýšit](https://docs.microsoft.com/azure/app-service/manage-scale-up) nebo snížit kapacitu aplikace.
    * To by mělo také ovlivnit výběr skladové položky Azure **Cognitive Search,** další podrobnosti naleznete [zde](https://docs.microsoft.com/azure/search/search-sku-tier). Navíc budete muset upravit kognitivní vyhledávací [kapacitu](../../../search/search-capacity-planning.md) s replikami.

* **Velikost a počet znalostních bází**: Zvolte příslušnou [skladovou položku azure pro](https://azure.microsoft.com/pricing/details/search/) váš scénář. Obvykle rozhodujete o počtu znalostních bází, které potřebujete, na základě počtu různých oborů předmětů. Jakmile předmět domény (pro jeden jazyk) by měla být v jedné znalostní bázi.

    Znalostní báze N-1 můžete publikovat v určité vrstvě, kde N je maximální indexy povolené ve vrstvě. Zkontrolujte také maximální velikost a počet dokumentů povolených na úroveň.

    Například pokud vaše úroveň má 15 povolených indexů, můžete publikovat 14 znalostních bází (1 index na publikovanou znalostní bázi). Patnáctý index se používá pro všechny znalostní báze pro vytváření a testování.

* **Počet dokumentů jako zdrojů**: Volná skladová položka služby správy QnA Maker omezuje počet dokumentů, které můžete spravovat prostřednictvím portálu a api, na 3 (každá velikost 1 MB). Standardní skladová položka nemá žádné omezení počtu dokumentů, které můžete spravovat. Více informací [naleznete zde](https://aka.ms/qnamaker-pricing).

V následující tabulce jsou uvedeny některé pokyny vysoké úrovně.

|                        | QnA Maker Management | App Service | Azure Cognitive Search | Omezení                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentování        | Volná skladová položka             | Úroveň zdarma   | Úroveň zdarma    | Publikovat až 2 kbs, velikost 50 MB  |
| Vývoj/testování prostředí   | Standardní SKU         | Sdílená      | Základní        | Publikovat až 14 kb, velikost 2 GB    |
| Výrobní prostředí | Standardní SKU         | Základní       | Standard     | Publikovat až 49 kb, velikost 25 GB |

## <a name="recommended-settings"></a>Doporučená nastavení

|Cíl QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 Instance   | S1, 1 Instance    |
| 50         | S3, 10 instancí       | S1, 12 instancí         |
| 80         | S3, 10 instancí      |  S3, 12 instancí  |
| 100         | P3V2, 10 instancí  | S3, 12 instancí, 3 oddíly   |
| 200 až 250         | P3V2, 20 instancí | S3, 12 instancí, 3 oddíly    |

## <a name="when-to-change-a-pricing-tier"></a>Kdy změnit cenovou úroveň

|Upgrade|Důvod|
|--|--|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) Skladová položka správy qnA makeru|Chcete mít více párů QnA nebo zdrojů dokumentů ve znalostní bázi.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) SKU služby App Service a kontrola úrovně kognitivního vyhledávání a [vytváření replik kognitivního vyhledávání](../../../search/search-capacity-planning.md)|Vaše znalostní báze musí obsluhovat více požadavků z klientské aplikace, například chatovacího robota.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Služba azure kognitivního vyhledávání|Máte v plánu mít mnoho znalostí.|

Nejnovější aktualizace za běhu získáte [aktualizací služby App Service na webu Azure Portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Důležité informace o pojmenování prostředků

Název prostředku pro prostředek QnA Maker, například `qna-westus-f0-b`, se také používá k pojmenování ostatních prostředků.

Okno vytvoření portálu Azure umožňuje vytvořit prostředek QnA Maker a vybrat cenové úrovně pro ostatní prostředky.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky portálu Azure pro vytvoření prostředků QnA Makeru](../media/concept-azure-resource/create-blade.png)

Po vytvoření prostředků mají stejný název, s výjimkou volitelného prostředku Application Insights, který postpends znaky na název.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky se seznamem prostředků portálu Azure Portal](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Při vytváření prostředku QnA Maker vytvořte novou skupinu prostředků. To umožňuje zobrazit všechny prostředky přidružené k prostředku QnA Maker při vyhledávání podle skupiny prostředků.

> [!TIP]
> K označení cenových úrovní v rámci názvu prostředku nebo skupiny prostředků použijte konvenci pojmenování. Při vytváření nové znalostní báze nebo přidávání nových dokumentů se při vytváření nových znalostních bází nebo přidávání nových dokumentů jedná o běžný problém limit cenové úrovně cognitive search.

## <a name="resource-purposes"></a>Účely zdrojů

Každý prostředek Azure vytvořený pomocí QnA Makermá konkrétní účel:

* Zdroj QnA Maker
* Zdroj kognitivního vyhledávání
* App Service
* Služba Plán aplikace
* Služba Application Insights


### <a name="cognitive-search-resource"></a>Zdroj kognitivního vyhledávání

Prostředek [kognitivního vyhledávání](../../../search/index.yml) se používá k:

* Uložení párů QnA
* Poskytněte počáteční pořadí (ranker #1) párů QnA za běhu

#### <a name="index-usage"></a>Využití indexu

Prostředek udržuje jeden index fungovat jako index testu a zbývající indexy korelovat s jedním publikované znalostní báze každý.

Prostředek s cenou 15 indexů bude obsahovat 14 publikovaných znalostních bází a jeden index se používá k testování všech znalostních bází. Tento testovací index je rozdělen podle znalostní báze, takže dotaz používající interaktivní testovací podokno použije testovací index, ale vrátí pouze výsledky z konkrétního oddílu přidruženého k konkrétní znalostní bázi.

#### <a name="language-usage"></a>Používání jazyka

První znalostní báze vytvořená v prostředku QnA Maker se používá k určení _sady jednoho_ jazyka pro prostředek kognitivního vyhledávání a všechny jeho indexy. Pro službu QnA Maker můžete mít nastaven pouze _jeden jazyk._

### <a name="qna-maker-resource"></a>Zdroj QnA Maker

Prostředek QnA Maker poskytuje přístup k vytváření a publikování rozhraní API, stejně jako zpracování přirozeného jazyka (NLP) založené druhé pořadí vrstvy (ranker #2) párů QnA za běhu.

Druhé pořadí používá inteligentní filtry, které mohou obsahovat metadata a následné výzvy.

#### <a name="qna-maker-resource-configuration-settings"></a>Nastavení konfigurace prostředků programu QnA Maker

Když vytvoříte novou znalostní bázi na [portálu QnA Maker](https://qnamaker.ai), je nastavení **Jazyk** jediným nastavením, které je použito na úrovni prostředků. Jazyk vyberete při vytváření první znalostní báze pro zdroj.

### <a name="app-service-and-app-service-plan"></a>Plán služby App service a služby App service

[Služba App](../../../app-service/index.yml) používá vaše klientská aplikace pro přístup k publikovaným znalostním bázím prostřednictvím koncového bodu runtime.

Chcete-li zadat dotaz na publikovanou znalostní bázi, všechny publikované znalostní báze používají stejný koncový bod adresy URL, ale v rámci postupu zadejte **ID znalostní báze.**

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) se používá ke shromažďování protokolů chatu a telemetrie. Informace o vaší službě naleznete v [běžných dotazech kusto.](../how-to/get-analytics-knowledge-base.md)

## <a name="share-services-with-qna-maker"></a>Sdílení služeb s QnA Makerem

QnA Maker vytvoří několik prostředků Azure. Chcete-li snížit správu a těžit ze sdílení nákladů, použijte následující tabulku, abyste pochopili, co můžete a nemůžete sdílet:

|Služba|Sdílet|Důvod|
|--|--|--|
|Cognitive Services|×|Není možné záměrné|
|Plán služby App Service|✔|Pevné místo na disku přidělené pro plán služby App Service. Pokud jiné aplikace, které sdílejí stejný plán služby App Service, využívají značné místo na disku, instance služby QnAMaker App Service se setká s problémy.|
|App Service|×|Není možné záměrné|
|Application Insights|✔|Lze sdílet|
|Služba Search|✔|1. `testkb` je vyhrazený název pro službu QnAMaker; nemůže být použit ostatními.<br>2. Synonymní mapa `synonym-map` podle názvu je vyhrazena pro službu QnAMaker.<br>3. Počet publikovaných znalostních bází je omezen úrovní vyhledávacích služeb. Pokud jsou k dispozici volné indexy, mohou je používat jiné služby.|

### <a name="using-a-single-cognitive-search-service"></a>Použití jedné služby kognitivního vyhledávání

Pokud vytvoříte službu QnA a její závislosti (například vyhledávání) prostřednictvím portálu, vyhledávací služba se vytvoří pro vás a propojí se službou QnA Maker. Po vytvoření těchto prostředků můžete aktualizovat nastavení služby App Service tak, aby používalo dříve existující vyhledávací službu, a odebrat tu, kterou jste právě vytvořili.

[Zjistěte, jak nakonfigurovat](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker tak, aby používal jiný prostředek služby Cognitive Service, než který byl vytvořen jako součást procesu vytváření prostředků qnA makeru.

## <a name="management-service-region"></a>Oblast služeb správy

Služba správy QnA Maker se používá pouze pro portál QnA Maker a pro počáteční zpracování dat. Tato služba je k dispozici pouze v oblasti **Západní USA.** V této službě v USA v usa nejsou uložena žádná zákaznická data.

## <a name="keys-in-qna-maker"></a>Klíče v QnA Makeru

Vaše služba QnA Maker se zabývá dvěma druhy klíčů: **vytváření klíčů** a **koncových klíčů dotazu používaných** s runtime hostovaným ve službě App.

Pokud hledáte **klíč předplatného**, [terminologie se změnila](#subscription-keys).

Tyto klíče použijte při vytváření požadavků na službu prostřednictvím api.

![Správa klíčů](../media/qnamaker-how-to-key-management/key-management.png)

|Name (Název)|Umístění|Účel|
|--|--|--|
|Klíč pro vytváření|[Portál Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Tyto klíče se používají pro přístup k [řešení API služby správy služby QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Tato api umožňují upravovat otázky a odpovědi ve znalostní bázi a publikovat znalostní bázi. Tyto klíče jsou vytvořeny při vytváření nové služby QnA Maker.<br><br>Tyto klíče najdete v prostředku **služeb Cognitive Services** na stránce **Klíče.**|
|Klíč koncového bodu dotazu|[Portál QnA Makeru](https://www.qnamaker.ai)|Tyto klíče se používají k dotazování publikovaný koncový bod znalostní báze získat odpověď na otázku uživatele. Tento koncový bod dotazu se obvykle používá v chatovacím robotu nebo v kódu klientské aplikace, který se připojuje ke službě QnA Maker. Tyto klíče jsou vytvořeny při publikování znalostní báze QnA Maker.<br><br>Tyto klíče najdete na stránce **Nastavení služby.** Tuto stránku naleznete v nabídce uživatele v pravém horním části stránky v rozevírací nabídce.|

### <a name="subscription-keys"></a>Klíče předplatného

Termíny vytváření a koncový bod dotazu klíč jsou opravné termíny. Předchozí období bylo **klíčem předplatného**. Pokud se zobrazí další dokumentace odkazující na klíče předplatného, jsou ekvivalentní k vytváření a koncovýbod dotazu klíče (používá se v době runtime).

Musíte vědět, k čemu klíč přistupuje, správa znalostní báze nebo dotazování znalostní báze, abyste věděli, který klíč potřebujete najít.

## <a name="next-steps"></a>Další kroky

* Další informace o [znalostní bázi](knowledge-base.md) QnA Maker
* Principy [životního cyklu znalostní báze](development-lifecycle-knowledge-base.md)
* Kontrola limitů služeb a znalostní [báze](../limits.md)

