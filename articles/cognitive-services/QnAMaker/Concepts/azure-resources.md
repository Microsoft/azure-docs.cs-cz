---
title: Prostředky Azure – QnA Maker
description: QnA Maker používá několik zdrojů Azure, z nichž každý má jiný účel. Porozumět tomu, jak se používají samostatně, vám umožní naplánovat a vybrat správnou cenovou úroveň nebo zjistit, kdy se má změnit cenová úroveň. Princip použití v kombinaci vám pomůže najít a opravit problémy, když k nim dojde.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 62f627fb9765f2a86a373f74c33437680c9305af
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777050"
---
# <a name="azure-resources-for-qna-maker"></a>Prostředky Azure pro QnA Maker

QnA Maker používá několik zdrojů Azure, z nichž každý má jiný účel. Porozumět tomu, jak se používají samostatně, vám umožní naplánovat a vybrat správnou cenovou úroveň nebo zjistit, kdy se má změnit cenová úroveň. Princip použití _v kombinaci_ vám pomůže najít a opravit problémy, když k nim dojde.

## <a name="resource-planning"></a>Plánování prostředků

Při prvním vývoji QnA Maker znalostní báze ve fázi prototypu je běžné mít jeden prostředek QnA Maker pro testování i pro produkční prostředí.

Když přejdete do vývojové fáze projektu, měli byste zvážit:

* kolik jazyků systém vašeho znalostní báze bude obsahovat
* Kolik oblastí potřebujete k dispozici ve znalostní bázi v/v
* kolik dokumentů v jednotlivých doménách bude váš systém obsahovat.

Naplánujte, aby jeden prostředek QnA Maker obsahoval všechny znalostní báze, které mají stejný jazyk, stejnou oblast a stejnou kombinaci domény předmětu.

## <a name="pricing-tier-considerations"></a>Doporučení cenové úrovně

Obvykle existují tři parametry, které je třeba vzít v úvahu:

* **Propustnost, kterou potřebujete ze služby**:
    * Podle svých potřeb vyberte vhodný [plán aplikace](https://azure.microsoft.com/pricing/details/app-service/plans/) pro vaši službu App Service. Aplikaci můžete [škálovat směrem nahoru](https://docs.microsoft.com/azure/app-service/manage-scale-up) nebo dolů.
    * Mělo by to mít vliv i na výběr skladové položky v Azure **kognitivní hledání** . Další podrobnosti najdete [tady](https://docs.microsoft.com/azure/search/search-sku-tier). Kromě toho možná budete muset nastavit [kapacitu](../../../search/search-capacity-planning.md) kognitivní hledání pomocí replik.

* **Velikost a počet znalostí znalostní báze**: vyberte příslušnou skladovou položku [Azure Search](https://azure.microsoft.com/pricing/details/search/) pro váš scénář. Obvykle se na základě počtu různých domén předmětu rozhodnete, že potřebujete požadovaný počet znalostí. Doména předmětu (pro jeden jazyk) by měla být v jedné znalostní bázi.

    Můžete publikovat N-1 znalostní báze v určité úrovni, kde N je maximální počet indexů povolených v této vrstvě. Také ověřte maximální velikost a počet dokumentů povolených na jednu úroveň.

    Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 báze znalostí (1 index na publikovanou znalostní bázi). Patnáctý index se používá pro všechny znalostní báze pro vytváření a testování.

* **Počet dokumentů jako zdrojů**: bezplatná SKU služby QnA maker Management omezuje počet dokumentů, které můžete spravovat přes portál, a rozhraní API na 3 (velikost 1 MB). Standardní SKU neomezuje počet dokumentů, které můžete spravovat. Další podrobnosti najdete [tady](https://aka.ms/qnamaker-pricing).

V následující tabulce jsou uvedeny některé pokyny vysoké úrovně.

|                            | Správa QnA Maker | App Service | Azure Cognitive Search | Omezení                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Experimentování**        | SKU zdarma             | Úroveň Free   | Úroveň Free    | Publikovat až 2 aktualizací KB velikost, 50 MB  |
| **Vývojové a testovací prostředí**   | Standardní SKU         | Shared      | Základní        | Publikování až o 14 aktualizací KB velikosti 2 GB    |
| **Produkční prostředí** | Standardní SKU         | Basic       | Standard     | Publikování až 49 aktualizací KB, velikost 25 GB |

## <a name="recommended-settings"></a>Doporučené nastavení

|Cílový QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 instance   | S1, 1 instance    |
| 50         | S3, 10 instancí       | S1, 12 instancí         |
| 80         | S3, 10 instancí      |  S3, 12 instancí  |
| 100         | P3V2, 10 instancí  | S3, 12 instancí, 3 oddíly   |
| 200 až 250         | P3V2, 20 instancí | S3, 12 instancí, 3 oddíly    |

## <a name="when-to-change-a-pricing-tier"></a>Kdy změnit cenovou úroveň

|Upgrade|Důvod|
|--|--|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) SKU správy QnA Maker|Chcete mít ve znalostní bázi více QnA párů nebo zdrojů dokumentů.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU a zkontroluje Kognitivní hledání vrstvu a [vytvoří kognitivní hledání repliky](../../../search/search-capacity-planning.md) .|Vaše znalostní báze potřebuje k obsluze více požadavků z klientské aplikace, jako je například robot pro chat.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Služba Azure Kognitivní hledání|Máte v plánu, abyste měli spoustu znalostní báze.|

Pomocí [aktualizace App Service v Azure Portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)získáte nejnovější aktualizace modulu runtime.

## <a name="resource-naming-considerations"></a>Požadavky na pojmenovávání prostředků

Název prostředku QnA Maker prostředku, například `qna-westus-f0-b` , se používá také k pojmenování dalších prostředků.

Okno Azure Portal vytvořit umožňuje vytvořit prostředek QnA Maker a vybrat cenové úrovně pro ostatní prostředky.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky Azure Portal pro vytváření prostředků QnA Maker](../media/concept-azure-resource/create-blade.png)

Po vytvoření prostředků mají stejný název, s výjimkou volitelného prostředku Application Insights, který postpends znaky na název.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s Azure Portalm seznamem prostředků](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Při vytváření prostředku QnA Maker vytvořte novou skupinu prostředků. To vám umožní zobrazit všechny prostředky přidružené k QnA Maker prostředku při hledání podle skupiny prostředků.

> [!TIP]
> Pomocí konvence vytváření názvů můžete označovat cenové úrovně v rámci názvu prostředku nebo skupiny prostředků. Když obdržíte chyby při vytváření nové znalostní báze nebo přidávání nových dokumentů, je běžným problémem omezení cenové úrovně Kognitivní hledání.

## <a name="resource-purposes"></a>Účely prostředků

Každý prostředek Azure vytvořený pomocí QnA Maker má určitý účel:

* Prostředek QnA Maker
* Prostředek Kognitivní hledání
* App Service
* Služba plánu aplikací
* Služba Application Insights


### <a name="cognitive-search-resource"></a>Prostředek Kognitivní hledání

Prostředek [kognitivní hledání](../../../search/index.yml) se používá pro:

* Uložit páry QnA
* Zadejte počáteční hodnocení (#1 hodnocení) párů QnA za běhu.

#### <a name="index-usage"></a>Využití indexu

Prostředek udržuje jeden index, který bude fungovat jako index testu, a zbývající indexy jsou v relaci k jedné publikované znalostní bázi.

Cena za prostředek se zablokuje 15 indexů, bude obsahovat 14 publikovaných znalostní báze a jeden index se používá k testování všech znalostí. Tento index testu je rozdělený ve znalostní bázi, takže dotaz používající interaktivní testovací podokno použije index testu, ale vrátí jenom výsledky z konkrétního oddílu přidruženého ke konkrétní znalostní bázi.

#### <a name="language-usage"></a>Použití jazyka

První znalostní báze vytvořená v prostředku QnA Maker slouží k určení _jedné_ sady jazyků pro prostředek kognitivní hledání a všech jeho indexů. Pro QnA Maker službu můžete mít jenom _jednu sadu jazyků_ .

### <a name="qna-maker-resource"></a>Prostředek QnA Maker

Prostředek QnA Maker poskytuje přístup k rozhraním API pro vytváření a publikování a také ke vrstvě pro zpracování v přirozeném jazyce (NLP) na základě druhé vrstvy hodnocení (přiřazení #2) párů QnA za běhu.

Druhé hodnocení používá inteligentní filtry, které mohou zahrnovat metadata a výzvy pro následné zpracování.

#### <a name="qna-maker-resource-configuration-settings"></a>Nastavení konfigurace prostředků QnA Maker

Když vytvoříte novou znalostní bázi na [portálu QnA maker](https://qnamaker.ai), nastavení **jazyka** bude jediným nastavením, které se použije na úrovni prostředků. Vyberte jazyk, když vytvoříte první znalostní bázi pro daný prostředek.

### <a name="app-service-and-app-service-plan"></a>Služba App Service a plán služby App Service

[Službu App Service](../../../app-service/index.yml) používá klientská aplikace pro přístup k publikovaným znalostní bázi prostřednictvím koncového bodu modulu runtime.

Aby se publikovala publikovaná znalostní báze, všechny publikované znalostní báze používají stejný koncový bod adresy URL, ale v rámci této trasy zadejte **ID znalostní báze** .

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) se používá ke shromažďování protokolů a telemetrie chatu. Projděte si nejčastější [dotazy Kusto](../how-to/get-analytics-knowledge-base.md) , kde najdete informace o vaší službě.

## <a name="share-services-with-qna-maker"></a>Sdílení služeb pomocí QnA Maker

QnA Maker vytvoří několik prostředků Azure. Pokud chcete snížit úroveň správy a výhod sdílení nákladů, použijte následující tabulku, která vám pomůže pochopit, co můžete a nemůžete sdílet:

|Služba|Sdílení|Důvod|
|--|--|--|
|Cognitive Services|×|Není možné podle návrhu|
|Plán služby App Service|✔|Pevné místo na disku přidělené pro plán App Service. Pokud jiné aplikace sdílející stejný plán App Service používají významné místo na disku, dojde k problémům s instancí App Service Qnamakerem.|
|App Service|×|Není možné podle návrhu|
|Application Insights|✔|Může být sdíleno|
|Služba Search|✔|1. `testkb` je rezervovaný název pro službu qnamakerem; nemůže ji použít jiný.<br>2. `synonym-map` pro službu qnamakerem je vyhrazená mapa synonym podle názvu.<br>3. počet publikovaných znalostní báze je omezený na úrovni služby vyhledávání. Pokud jsou dostupné bezplatné indexy, můžou je používat i jiné služby.|

### <a name="using-a-single-cognitive-search-service"></a>Použití jedné Kognitivní hledání služby

Pokud vytvoříte službu QnA a její závislosti (například vyhledávání) prostřednictvím portálu, vytvoří se vyhledávací služba pro vás a bude propojena s QnA Makerovou službou. Po vytvoření těchto prostředků můžete aktualizovat nastavení App Service tak, aby používalo dříve existující vyhledávací službu, a odebrat tu, kterou jste právě vytvořili.

Naučte [se, jak nakonfigurovat](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA maker pro použití jiného prostředku služby rozpoznávání, než který je vytvořený jako součást procesu vytváření prostředků QnA maker.

## <a name="management-service-region"></a>Oblast služby správy

Služba správy QnA Maker se používá jenom pro QnA Maker portál a pro počáteční zpracování dat. Tato služba je k dispozici pouze v **západní USA** oblasti. V této Západní USA službě se neukládají žádná zákaznická data.

## <a name="keys-in-qna-maker"></a>Klíče v QnA Maker

Vaše služba QnA Maker se zabývá dvěma druhy klíčů: **vytváření klíčů** a **klíčů koncových bodů dotazů** , které se používají s modulem runtime hostovaným ve službě App Service.

Pokud hledáte svůj **klíč předplatného**, [terminologie se změnila](#subscription-keys).

Tyto klíče použijte při provádění požadavků na službu prostřednictvím rozhraní API.

![Správa klíčů](../media/qnamaker-how-to-key-management/key-management.png)

|Název|Umístění|Účel|
|--|--|--|
|Vytváření klíče|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|Tyto klíče se používají pro přístup k [rozhraním API služby QnA maker Management](https://go.microsoft.com/fwlink/?linkid=2092179). Tato rozhraní API umožňují upravovat otázky a odpovědi ve znalostní bázi a publikovat znalostní bázi. Tyto klíče se vytvoří při vytvoření nové služby QnA Maker.<br><br>Tyto klíče najdete na **Cognitive Services** prostředku na stránce **klíče** .|
|Klíč koncového bodu dotazu|[Portál QnA Makeru](https://www.qnamaker.ai)|Tyto klíče se používají k dotazování publikovaného koncového bodu znalostní báze, aby se zobrazila odpověď pro otázku uživatele. Tento koncový bod dotazu obvykle používáte v robotovi chatu nebo v kódu klientské aplikace, který se připojuje ke službě QnA Maker. Tyto klíče se vytvoří při publikování QnA Maker znalostní báze Knowledge Base.<br><br>Tyto klíče najdete na stránce **nastavení služby** . Vyhledá tuto stránku v nabídce uživatele v pravém horním rohu stránky v rozevírací nabídce.|

### <a name="subscription-keys"></a>Klíče předplatného

Klíčovým bodem pro vytváření a dotazování podmínek jsou opravné výrazy. Předchozí termín byl **klíč předplatného**. Pokud se vám v dokumentaci k klíčům předplatného zobrazí další dokumentace, jsou ekvivalentní klíčům koncových bodů pro vytváření a dotazování (používá se v modulu runtime).

Musíte znát, k čemu má klíč přístup, správu znalostní báze nebo dotazování znalostní báze, abyste věděli, který klíč potřebujete najít.

## <a name="recommended-settings-for-network-isolation"></a>Doporučené nastavení pro izolaci sítě

* Chraňte prostředek služby Service od veřejného přístupu [konfigurací virtuální sítě](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal).
* Chránit App Service (QnA Runtime) z veřejného přístupu:
    * Povoluje provoz jenom z IP adres služeb rozpoznávání. Ty jsou už součástí značky služby "CognitiveServicesManagement". To se vyžaduje pro vytváření rozhraní API (vytvořit/aktualizovat KB) pro vyvolání služby App Service a aktualizaci služby Azure Search.
    * Ujistěte se, že taky povolíte další vstupní body, jako je robot Service, QnA Maker Portal (může to být váš Corpnet) atd. předpověď přístupu k rozhraní API GenerateAnswer.
    * Podívejte se na [Další informace o značkách služby.](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

## <a name="next-steps"></a>Další kroky

* Další informace o QnA Maker [znalostní bázi Knowledge Base](knowledge-base.md)
* Pochopení [životního cyklu znalostní báze](development-lifecycle-knowledge-base.md)
* Kontrola [omezení](../limits.md) služby a znalostí znalostní báze

